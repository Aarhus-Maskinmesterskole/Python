# **09 – Real PLC (valgfrit)**

*(rigtig S7-PLC · trin-for-trin · **2 små filer** + tjekliste)*

**Mål:** Forbind til en **rigtig Siemens S7-PLC** (fx S7-1200/1500) og lav en **sikker read-only** test. Når det virker, kan du lave en **kontrolleret write** til et *aftalt* DB-område.
**Forudsætninger:** 02–08 gennemført. Du har filen `08-error-handling/reliable_client.py` (S7Reliable).
**Tid:** 30–60 min. + TIA-opsætning.

---

## 🗂️ Mappe & filer

Opret mappen **`09-real-plc-optional`** med:

* `README.md` *(denne guide – valgfrit at oprette)*
* `read_plc.py` → **read-only** sanity check mod rigtig PLC
* `write_plc_safe.py` → **kontrolleret write** til et aftalt DB-område

> Kopiér **`reliable_client.py`** fra modul 08 ind i **denne** mappe, eller justér `PYTHONPATH`, så import virker.

---

## 🛡️ Step 0 — Sikkerhed (læs før du skriver)

* **Skriv aldrig** til uafklarede adresser på udstyr i drift.
* Aftal et **test-DB** (egen blok, ingen logik afhænger af den).
* Start **read-only**. Skriv kun, når underviser/tekniker har godkendt det.
* Log altid ændringer og kunne rulle tilbage.

---

## 🔧 Step 1 — TIA Portal: forbered PLC’en (S7-1200/1500)

1. **Protection & Security** (CPU-egenskaber)

   * Sæt *”Permit access with PUT/GET communication from remote partner”* = **Tilladt**.
   * Acceptér genstart hvis krævet.

2. **Opret en Data Block til Python**

   * Ny **Global DB**: fx `DB100_Snap7Demo` → **Slå “Optimized block access” fra** (krav for absolutte adresser).
   * Indsæt felter med *eksakte adresser* (eksempel nedenfor).
   * **Download** til PLC (og sørg for PLC i *RUN*).

3. **IP-adresse**

   * Notér PLC’ens IP (fx `192.168.0.10`).
   * **Rack/Slot**: typisk **0/1** for S7-1200/1500 (S7-300 ofte **0/2**).

### 🌐 Eksempel på DB-layout (i **DB100**)

**Telemetri** *(read-only fra Python – PLC kan skrive disse)*

* `DBD0`   (0..3)  → `counter` (DINT)
* `DBW4`   (4..5)  → `temperature` (INT)
* `DBD6`   (6..9)  → `pressure` (REAL)
* `DBX10.0`        → `alarm` (BOOL)
* `DBB11` (11)     → `mode_ro` (BYTE)

**Kommandoer** *(Python må skrive her – PLC må ikke overskrive)*

* `DBW20` (20..21) → `setpoint_int` (INT)
* `DBD22` (22..25) → `setpoint_real` (REAL)
* `DBX26.0`        → `cmd_start` (BOOL)
* `DBX26.1`        → `cmd_stop` (BOOL)
* `DBB27` (27)     → `mode` (BYTE)

> Brug **samme adresser** i koden herunder. Hvis du ændrer dem i TIA, så ændr også i Python.

---

## 🖧 Step 2 — PC & net

* Sæt PC på **samme subnet** (fx 192.168.0.x/24).
* **Ping** PLC: `ping 192.168.0.10`.
* Windows Firewall må ikke blokere udgående på **TCP 102** (standard S7).

---

## 👀 Step 3 — Første **read-only** mod rigtig PLC

**`09-real-plc-optional/read_plc.py`**

```python
import snap7
import snap7.util as s7

IP   = "192.168.0.10"  # <- skift til din PLC
RACK = 0
SLOT = 1               # S7-1200/1500 typisk 1
PORT = 102             # rigtig PLC = 102
DB   = 100

def main():
    c = snap7.client.Client()
    try:
        c.connect(IP, RACK, SLOT, PORT)

        buf = c.db_read(DB, 0, 12)  # telemetri-felter (DBD0..DBB11)
        counter     = s7.get_dint(buf, 0)
        temperature = s7.get_int(buf, 4)
        pressure    = s7.get_real(buf, 6)
        alarm       = s7.get_bool(buf, 10, 0)
        mode_ro     = s7.get_byte(buf, 11)

        print(f"OK: counter={counter}, temp={temperature}°C, "
              f"press={pressure:.2f}, alarm={alarm}, mode_ro={mode_ro}")

    finally:
        try: c.disconnect()
        except: pass
        try: c.destroy()
        except: pass

if __name__ == "__main__":
    main()
```

**Kør:**

```powershell
py 09-real-plc-optional\read_plc.py
```

**Forventet:** En pæn linje med værdier. Hvis du får *”invalid transport size”* → se **Fejlsøgning**.

---

## ✍️ Step 4 — Kontrolleret **write** til aftalt område

Brug din **robuste** klient fra modul 08 (S7Reliable). Kopiér `reliable_client.py` hertil.

**`09-real-plc-optional/write_plc_safe.py`**

```python
import snap7.util as s7
from reliable_client import S7Reliable
import time

IP, RACK, SLOT, PORT = "192.168.0.10", 0, 1, 102
DB = 100

def write_setpoint_int(cli: S7Reliable, val: int):
    b = bytearray(2); s7.set_int(b, 0, val)
    cli.write_db(DB, 20, b)  # DBW20

def write_setpoint_real(cli: S7Reliable, val: float):
    b = bytearray(4); s7.set_real(b, 0, val)
    cli.write_db(DB, 22, b)  # DBD22

def write_cmd(cli: S7Reliable, start=None, stop=None):
    # read–modify–write af byte 26 (bit 0/1)
    if start is not None:
        cli.read_modify_write_byte_bit(DB, 26, 0, bool(start))
    if stop is not None:
        cli.read_modify_write_byte_bit(DB, 26, 1, bool(stop))

def write_mode(cli: S7Reliable, val: int):
    b = bytearray(1); s7.set_byte(b, 0, val & 0xFF)
    cli.write_db(DB, 27, b)  # DBB27

def main():
    cli = S7Reliable(IP, RACK, SLOT, PORT, max_retries=10, base_backoff=0.5)
    print("Skriver sikre testværdier (aftalt DB-område)…")

    write_setpoint_int(cli, 123)
    write_setpoint_real(cli, 3.14)
    write_cmd(cli, start=True, stop=False)
    write_mode(cli, 2)

    time.sleep(1.0)

    write_cmd(cli, start=False, stop=True)
    write_setpoint_int(cli, 200)
    write_mode(cli, 5)

    print("Færdig.")

if __name__ == "__main__":
    main()
```

**Kør:**

```powershell
py 09-real-plc-optional\write_plc_safe.py
```

**Forventet:** PLC’en ser ændringerne i `DB100` på offsets 20–27 (fx via *Watch table* i TIA).

---

## ✅ Definition of Done (DoD)

* [ ] **read\_plc.py** udlæser meningsfulde værdier (ingen exceptions).
* [ ] **write\_plc\_safe.py** ændrer **kun** de aftalte felter i test-DB’en.
* [ ] Du kan forklare **rack/slot**, **port 102**, **PUT/GET**, og hvorfor **optimized block access** skal være **slået fra** for den DB, du adresserer.

---

## 🧪 Mini-øvelser

1. **Dataclass på rigtig PLC**: Genbrug `Telemetry`/`Command` fra modul 06 til DB100.
2. **Read-back**: Efter write – læs DB’en og verificér, at værdierne er præcis dem, du skrev.
3. **Alarm-logik (read-only)**: Vis en advarsel i konsol, hvis `alarm=True`.

---

## 🆘 Fejlsøgning (hurtig)

* **`invalid transport size` / mærkelige værdier**
  → DB’en er sandsynligvis **optimized**. Slå *optimized block access* **fra** for den DB du læser.
* **Kan ikke forbinde**
  → Tjek IP, **ping**, samme subnet, **rack/slot** (0/1 for 1200/1500), **port 102**, firewall.
* **`Address out of range`**
  → DB-nummer/offset/length matcher ikke det, der findes i PLC’en.
* **Værdier “hopper”**
  → PLC-logik overskriver samme område. Brug en **ren test-DB**, som logikken ikke rører.
* **S7-300/400**
  → Ofte **rack=0, slot=2** (CPU-slot). Tjek HW-konfig for at være sikker.

---

## 🎉 Klar!

Du har nu en **ende-til-ende** kæde fra Python → rigtig S7-PLC. Brug mønstrene fra modul 06–08 (dataklasser + robust klient) til dine egne projekter — og husk altid **aftalt DB** og **read-only først**.
