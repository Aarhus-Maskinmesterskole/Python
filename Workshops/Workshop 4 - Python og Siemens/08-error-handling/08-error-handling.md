# **08 – Error handling & robust forbindelse**

*(ingen hardware · trin-for-trin · **2 små filer**)*

**Mål:** Gør din Snap7-klient **robust**: automatisk **reconnect**, **retry** med backoff, pæn **fejlhåndtering** og **graceful shutdown**.
**Forudsætninger:** 02–07 gennemført. Du kan genbruge serveren fra **06**: `06-dataclass-mapping/server_combined.py`.

---

## 🗂️ Mappe & filer

Opret mappen **`08-error-handling`**:

* `reliable_client.py` → en lille hjælperklasse med **auto-reconnect** og **retry**
* `test_robust_logger.py` → demo der læser `DB1.DBD0` hvert sekund og **overlever** afbrydelser

---

## 🧪 Step 1 — Kør fake PLC (som før)

I én terminal:

```powershell
py 06-dataclass-mapping\server_combined.py
```

Lad den køre. (Du må gerne **stoppe og starte** den om lidt, for at teste robusthed.)

---

## 🧱 Step 2 — Robust klient med reconnect + retry

**`08-error-handling/reliable_client.py`**

```python
import time
from typing import Optional, Callable, Any, Tuple
import snap7
from snap7.common import Snap7Exception

class S7Reliable:
    """
    Lille Snap7-klient med auto-reconnect og retry.
    Brug read_db()/write_db(), så klarer den selv forbindelses-issues.
    """
    def __init__(
        self,
        ip: str,
        rack: int = 0,
        slot: int = 1,
        port: int = 1102,              # vi bruger 1102 i workshoppen
        max_retries: int = 5,
        base_backoff: float = 0.5,     # sekunder (eksponentiel backoff)
    ) -> None:
        self.ip, self.rack, self.slot, self.port = ip, rack, slot, port
        self.max_retries, self.base_backoff = max_retries, base_backoff
        self._c: Optional[snap7.client.Client] = None
        self._connected = False

    # ---------- lav-niveau ----------
    def connect(self) -> None:
        if self._c is None:
            self._c = snap7.client.Client()
        if not self._connected:
            self._c.connect(self.ip, self.rack, self.slot, self.port)
            self._connected = True

    def disconnect(self) -> None:
        try:
            if self._c:
                self._c.disconnect()
        except Exception:
            pass
        finally:
            self._connected = False

    def destroy(self) -> None:
        try:
            if self._c:
                self._c.destroy()
        except Exception:
            pass
        finally:
            self._c = None
            self._connected = False

    def _with_retry(self, fn: Callable[..., Any], *args, **kwargs) -> Any:
        """Kør fn med auto-reconnect + eksponentiel backoff."""
        last_err: Optional[Exception] = None
        backoff = self.base_backoff
        for attempt in range(1, self.max_retries + 1):
            try:
                if not self._connected:
                    self.connect()
                return fn(*args, **kwargs)
            except (Snap7Exception, OSError) as e:
                last_err = e
                # forbindelsen er nok død → luk og prøv igen efter backoff
                self.disconnect()
                print(f"[S7Reliable] Fejl ({attempt}/{self.max_retries}): {e}. "
                      f"Prøver igen om {backoff:.1f}s …")
                time.sleep(backoff)
                backoff = min(backoff * 2, 8.0)  # cap backoff
        # gav op
        raise last_err  # type: ignore

    # ---------- high-level API ----------
    def read_db(self, db: int, start: int, size: int) -> bytes:
        if not self._c:
            self._c = snap7.client.Client()
        return self._with_retry(self._c.db_read, db, start, size)

    def write_db(self, db: int, start: int, data: bytes) -> None:
        if not self._c:
            self._c = snap7.client.Client()
        return self._with_retry(self._c.db_write, db, start, data)

    def read_modify_write_byte_bit(self, db: int, byte_start: int, bit_index: int, value: bool) -> None:
        """Læs 1 byte, ændr én bit, skriv byte tilbage (sikkert for nabo-bits)."""
        if not (0 <= bit_index <= 7):
            raise ValueError("bit_index skal være 0..7")
        b = bytearray(self.read_db(db, byte_start, 1))
        # Snap7s set_bool forventer (buf, byte_index, bit_index, value)
        import snap7.util as s7
        s7.set_bool(b, 0, bit_index, value)
        self.write_db(db, byte_start, b)
```

---

## 📥 Step 3 — Brug den robuste klient (log med overlevelse)

**`08-error-handling/test_robust_logger.py`**

```python
import time
import snap7.util as s7
from reliable_client import S7Reliable

IP, RACK, SLOT, PORT = "127.0.0.1", 0, 1, 1102
DB = 1

def main():
    cli = S7Reliable(IP, RACK, SLOT, PORT, max_retries=9999, base_backoff=0.5)
    print("Starter robust log. Stop/Start din fake PLC for at teste reconnect.")
    try:
        while True:
            try:
                # Læs kun DBD0 (counter)
                buf = cli.read_db(DB, 0, 4)
                val = s7.get_dint(buf, 0)
                print("counter:", val)
            except Exception as e:
                # _with_retry udmatter først alle forsøg; vi viser blot fejl og fortsætter
                print("[LOG] Stadig ingen forbindelse:", e)
                time.sleep(1.0)
                continue
            time.sleep(1.0)
    except KeyboardInterrupt:
        print("\nStopper…")
    finally:
        cli.disconnect()
        cli.destroy()

if __name__ == "__main__":
    main()
```

**Kør i en NY terminal:**

```powershell
py 08-error-handling\test_robust_logger.py
```

**Test robusthed:**

1. Mens loggeren kører, **stop** serveren (Ctrl+C i server-vinduet).
   → Du ser gentagne reconnect-forsøg med stigende **backoff**.
2. **Start** serveren igen (`py 06-dataclass-mapping\server_combined.py`).
   → Loggeren finder selv forbindelsen igen og fortsætter.

---

## 🧠 Mønstre du kan genbruge

* **Auto-reconnect**: Pak alle `db_read`/`db_write` i en **retry-wrapper** med backoff.
* **Read-modify-write for bits**: Læs **1 byte**, ændr **én bit**, skriv byte tilbage.
* **Graceful shutdown**: Fang `KeyboardInterrupt`, luk forbindelsen pænt (`disconnect()`, `destroy()`).
* **Overvågning**: En statuslinje i GUI eller en periodisk “heartbeat”-læsning (fx 1 byte) kan indikere, om du er forbundet.

---

## ✅ Definition of Done (DoD)

* [ ] Loggeren udskriver `counter` når serveren kører.
* [ ] Når serveren **stoppes**, ser du pæne retry-beskeder (ingen crash).
* [ ] Når serveren **startes igen**, fortsætter loggeren automatisk.
* [ ] Du kan forklare, hvad **backoff** og **retry** gør for stabiliteten.

---

## 🧪 Mini-øvelser

1. **Skriv med robusthed**: Tilføj en periodisk **write** (fx hver 10. s) via `write_db` og se, at den også overlever afbrydelser.
2. **Maks retries**: Sæt `max_retries=5` og vis en venlig fejl, hvis det ikke lykkes (fx pop-up i GUI).
3. **Backoff-tuning**: Sammenlign `base_backoff=0.2` vs. `1.0` på et ustabilt net.

---

## 🆘 Fejlsøgning

* **Øjeblikkeligt crash ved server-stop** → Sørg for at *alle* PLC-kald går via `S7Reliable` (ikke direkte `client.db_read`).
* **Aldrig reconnect** → Tjek at IP/port stemmer, og at serveren virkelig kører på samme port.
* **Høj CPU** → Backoff for lav? Hæv `base_backoff`, så du ikke spammer netværket.

---

## ➡️ Næste modul: **09 – Real PLC (valgfrit)**

Tjekliste for **rigtig S7-PLC** (PUT/GET, IP, rack/slot), første **read-only** læsning og forsigtig write-test — med din **robuste klient** i ryggen.
