# **05 – Write data (DB ← sikre skrivninger)**

*(ingen hardware · trin-for-trin · **2 små filer**)*

**Mål:** Skriv **enkeltfelter** (INT/REAL/BOOL/BYTE) til en DB uden at ødelægge nabo-data. Du lærer “**read–modify–write**” for bits og præcise, korte writes for ord/dword/byte.
**Forventet tid:** 20–30 min.
**Forudsætninger:** 02 (installation) og 03 (fake PLC) + 04 (read data).

---

## 🗂️ Mappe & filer

Opret mappen **`05-write-data`** i dit projekt med disse to filer:

* `server_monitor.py` → fake PLC, der **ikke** overskriver dit skrive-område, men **printer ændringer**
* `write_values.py` → klient der skriver INT, REAL, BOOL (bit), BYTE til sikre offsets

> Du kan også vælge at **genbruge** `04-read-data/fake_server_types.py`. Her bruger vi en server, der **overvåger** et separat skrive-område, så du tydeligt ser dine writes.

---

## 📐 DB-layout i denne øvelse

| Offset           | Type | Felt            | Anvendelse                      |
| ---------------- | ---- | --------------- | ------------------------------- |
| `DBD0` (0..3)    | DINT | `counter`       | (server tæller, read-only demo) |
| `DBW20` (20..21) | INT  | `setpoint_int`  | **DU skriver**                  |
| `DBD22` (22..25) | REAL | `setpoint_real` | **DU skriver**                  |
| `DBX26.0`        | BOOL | `cmd_start`     | **DU sætter bit**               |
| `DBX26.1`        | BOOL | `cmd_stop`      | **DU sætter bit**               |
| `DBB27` (27)     | BYTE | `mode`          | **DU skriver**                  |

> Serveren rører **ikke** ved offset 20–27; den **udskriver** bare ændringer.

---

## 🧪 Step 1 — Start fake PLC med **monitorering**

**`05-write-data/server_monitor.py`**

```python
from time import sleep
from snap7.server import Server
from snap7.types import Areas
import snap7.util as s7
import ctypes

DB_NUM, DB_SIZE, PORT = 1, 64, 1102
srv = Server()
db = (ctypes.c_uint8 * DB_SIZE)()
srv.register_area(Areas.DB, DB_NUM, db, DB_SIZE)
srv.start(tcpport=PORT)
print(f"Fake PLC (monitor) på 127.0.0.1:{PORT} – Ctrl+C for stop.")

# Demo: vi opdaterer kun en counter (læsefelt), IKKE skriveområdet
i = 0
last = {
    "setpoint_int": None,
    "setpoint_real": None,
    "cmd_start": None,
    "cmd_stop": None,
    "mode": None,
}
while True:
    # DBD0 tæller bare op (read-only demo)
    s7.set_dint(db, 0, i)
    i += 1

    # Læs skriveområdet og udskriv ændringer
    cur = {
        "setpoint_int": s7.get_int(db, 20),
        "setpoint_real": s7.get_real(db, 22),
        "cmd_start": s7.get_bool(db, 26, 0),
        "cmd_stop": s7.get_bool(db, 26, 1),
        "mode": s7.get_byte(db, 27),
    }
    if cur != last:
        print("SKRIVE-OMRÅDE:", cur)
        last = cur
    sleep(0.5)
```

**Kør i en terminal (lad køre):**

```powershell
py 05-write-data\server_monitor.py
```

---

## ✍️ Step 2 — Skriv værdier (INT, REAL, BOOL-bit, BYTE)

**`05-write-data/write_values.py`**

```python
import time, snap7
import snap7.util as s7

IP, RACK, SLOT, PORT = "127.0.0.1", 0, 1, 1102
DB = 1

c = snap7.client.Client()
c.connect(IP, RACK, SLOT, PORT)

# --- INT (DBW20): kan skrives som 2 bytes direkte ---
def write_setpoint_int(val: int):
    buf = bytearray(2)
    s7.set_int(buf, 0, val)
    c.db_write(DB, 20, buf)

# --- REAL (DBD22): 4 bytes direkte ---
def write_setpoint_real(val: float):
    buf = bytearray(4)
    s7.set_real(buf, 0, val)
    c.db_write(DB, 22, buf)

# --- BOOLs i samme byte (DBX26.0 og DBX26.1) ---
# Læs-modificér-skriv 1 BYTE for at undgå at overskrive andre bits
def write_cmd_bits(start: bool = None, stop: bool = None):
    buf = bytearray(c.db_read(DB, 26, 1))  # læs eksisterende byte
    if start is not None:
        s7.set_bool(buf, 0, 0, start)  # bit 0
    if stop is not None:
        s7.set_bool(buf, 0, 1, stop)   # bit 1
    c.db_write(DB, 26, buf)

# --- BYTE (DBB27): 1 byte direkte ---
def write_mode(val: int):
    buf = bytearray(1)
    s7.set_byte(buf, 0, val & 0xFF)
    c.db_write(DB, 27, buf)

# Demo-sekvens
print("Skriver INT=123, REAL=3.14, START=True, STOP=False, MODE=2")
write_setpoint_int(123)
write_setpoint_real(3.14)
write_cmd_bits(start=True, stop=False)
write_mode(2)

time.sleep(1.0)

print("Skifter START=False, STOP=True, MODE=5, og ramp INT 200..204")
write_cmd_bits(start=False, stop=True)
write_mode(5)
for v in range(200, 205):
    write_setpoint_int(v)
    time.sleep(0.3)

c.disconnect()
c.destroy()
print("Færdig.")
```

**Kør i en NY terminal:**

```powershell
py 05-write-data\write_values.py
```

**Forventet:** I server-vinduet ser du linjer som:

```
SKRIVE-OMRÅDE: {'setpoint_int': 123, 'setpoint_real': 3.140000104904175, 'cmd_start': True, 'cmd_stop': False, 'mode': 2}
SKRIVE-OMRÅDE: {'setpoint_int': 200, 'setpoint_real': 3.140000104904175, 'cmd_start': False, 'cmd_stop': True, 'mode': 5}
...
```

*(REAL afrundes binært; det er normalt.)*

---

## 🧠 Hvorfor “read–modify–write” for bits?

Bits deler en **byte**. Hvis du skrev en “ny byte” uden at læse først, kunne du komme til at **nulstille/overskrive** de andre bits.
Mønstret er: **læs** byte → **ændr kun de bits** du vil → **skriv byte tilbage**.

---

## ✅ Definition of Done (DoD)

* [ ] Serveren kører og **printer ændringer**, når du skriver.
* [ ] Du har skrevet **INT, REAL, to BOOL-bits og en BYTE** til de angivne offsets.
* [ ] Du kan forklare forskellen på **direkte writes** (INT/REAL/BYTE) og **read–modify–write** (bit i samme byte).

---

## 🧪 Mini-øvelser

1. **Fælles write:** Læs 8 bytes fra offset 20, sæt **alle felter** i bufferen og skriv dem i **ét db\_write**.
2. **Toggler:** Lav en lille løkke der **toggler** `cmd_start` hvert sekund (True/False/True/…).
3. **Grænser:** Hvad sker der hvis du skriver `mode = 300`? (tip: `& 0xFF` i eksemplet begrænser til 0–255).

---

## 🆘 Fejlsøgning

* **Intet ændrer sig i server-vinduet** → Kører serveren? Samme **PORT (1102)**? Skriver du til de **rigtige offsets**?
* **Skrivning “forsvinder”** → Du skriver måske til et område som serveren overskriver i sin løkke — brug offsets 20–27 som her.
* **Bit opfører sig mærkeligt** → Tjek at du **læser byte 26 først**, ændrer bit 0/1, og skriver byte 26 tilbage.

---

## ➡️ Næste modul: **06 – Dataclass mapping**

Du lærer at mappe dine DB-bytes til et **pænt Python-objekt** (dataclass) – og tilbage igen – så koden bliver mere læsbar og robust.

