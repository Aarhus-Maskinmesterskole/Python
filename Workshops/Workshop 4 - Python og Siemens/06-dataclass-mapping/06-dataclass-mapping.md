# **06 – Dataclass mapping (DB ↔ Python-objekter)**

*(ingen hardware · trin-for-trin · **2 små filer**)*

**Mål:** Gør dine DB-bytes til **pæne Python-objekter** (dataclasses) – og skriv tilbage igen uden at ødelægge nabo-data.
**Forventet tid:** 20–30 min.
**Forudsætninger:** 02 (installation), 03 (fake PLC), 04 (read), 05 (write).

---

## 🗂️ Mappe & filer

Opret mappen **`06-dataclass-mapping`** med **to** filer:

* `server_combined.py` → fake PLC der både **producerer telemetri** og **monitorerer skrive-område**
* `map_client.py` → klient med **dataclasses** + parsing/skrivning

---

## 📐 DB-layout i dette modul

**Telemetri (serveren opdaterer disse):**

| Offset         | Type | Felt                         |
| -------------- | ---- | ---------------------------- |
| `DBD0`  (0..3) | DINT | `counter`                    |
| `DBW4`  (4..5) | INT  | `temperature`                |
| `DBD6`  (6..9) | REAL | `pressure`                   |
| `DBX10.0`      | BOOL | `alarm`                      |
| `DBB11` (11)   | BYTE | `mode_ro` *(read-only demo)* |

**Kommandoer (du skriver her):**

| Offset           | Type | Felt            |
| ---------------- | ---- | --------------- |
| `DBW20` (20..21) | INT  | `setpoint_int`  |
| `DBD22` (22..25) | REAL | `setpoint_real` |
| `DBX26.0`        | BOOL | `cmd_start`     |
| `DBX26.1`        | BOOL | `cmd_stop`      |
| `DBB27` (27)     | BYTE | `mode`          |

> Serveren **rører ikke** offset 20–27 – den **printer kun** ændringer dér, så du tydeligt ser dine writes.

---

## 🧪 Step 1 — Start den kombinerede fake PLC

**`06-dataclass-mapping/server_combined.py`**

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
print(f"Fake PLC (combined) på 127.0.0.1:{PORT} – Ctrl+C for stop.")

i = 0
last = {"setpoint_int": None, "setpoint_real": None, "cmd_start": None, "cmd_stop": None, "mode": None}

while True:
    # --- Telemetri (opdateres løbende) ---
    s7.set_dint(db, 0, i)              # DBD0
    s7.set_int(db, 4, 25)              # DBW4
    s7.set_real(db, 6, 1.23)           # DBD6
    s7.set_bool(db, 10, 0, (i % 2 == 0))  # DBX10.0
    s7.set_byte(db, 11, 7)             # DBB11 (read-only demo)
    i += 1

    # --- Monitorér skriveområdet (20..27) og udskriv ændringer ---
    cur = {
        "setpoint_int": s7.get_int(db, 20),
        "setpoint_real": s7.get_real(db, 22),
        "cmd_start": s7.get_bool(db, 26, 0),
        "cmd_stop": s7.get_bool(db, 26, 1),
        "mode": s7.get_byte(db, 27),
    }
    if cur != last:
        print("WRITE:", cur)
        last = cur
    sleep(0.5)
```

**Kør i en terminal (lad den køre):**

```powershell
py 06-dataclass-mapping\server_combined.py
```

---

## 🧱 Step 2 — Dataclasses + læs/skriv (klient)

**`06-dataclass-mapping/map_client.py`**

```python
from dataclasses import dataclass
import time, snap7
import snap7.util as s7

IP, RACK, SLOT, PORT = "127.0.0.1", 0, 1, 1102
DB = 1

# --- Datamodeller -------------------------------------------------------------

@dataclass
class Telemetry:
    counter: int        # DBD0
    temperature: int    # DBW4
    pressure: float     # DBD6
    alarm: bool         # DBX10.0
    mode_ro: int        # DBB11 (kun fra serveren)

@dataclass
class Command:
    setpoint_int: int   # DBW20
    setpoint_real: float# DBD22
    cmd_start: bool     # DBX26.0
    cmd_stop: bool      # DBX26.1
    mode: int           # DBB27

# --- Mapping helpers ----------------------------------------------------------

def parse_telemetry(buf: bytes) -> Telemetry:
    return Telemetry(
        counter     = s7.get_dint(buf, 0),
        temperature = s7.get_int(buf, 4),
        pressure    = s7.get_real(buf, 6),
        alarm       = s7.get_bool(buf, 10, 0),
        mode_ro     = s7.get_byte(buf, 11),
    )

def apply_command(client: snap7.client.Client, cmd: Command) -> None:
    # INT @ DBW20
    b = bytearray(2); s7.set_int(b, 0, cmd.setpoint_int); client.db_write(DB, 20, b)
    # REAL @ DBD22
    b = bytearray(4); s7.set_real(b, 0, cmd.setpoint_real); client.db_write(DB, 22, b)
    # Bits @ DBX26.0/26.1 (read–modify–write én BYTE)
    byte26 = bytearray(client.db_read(DB, 26, 1))
    s7.set_bool(byte26, 0, 0, cmd.cmd_start)
    s7.set_bool(byte26, 0, 1, cmd.cmd_stop)
    client.db_write(DB, 26, byte26)
    # BYTE @ DBB27
    b = bytearray(1); s7.set_byte(b, 0, cmd.mode & 0xFF); client.db_write(DB, 27, b)

# --- Demo-flow ----------------------------------------------------------------

c = snap7.client.Client(); c.connect(IP, RACK, SLOT, PORT)

# Læs telemetri som objekt
buf = c.db_read(DB, 0, 12)  # dækker vores Telemetry-felter
t0 = parse_telemetry(buf)
print("TEL:", t0)

# Skriv en kommando-pakke
cmd = Command(setpoint_int=123, setpoint_real=3.14, cmd_start=True, cmd_stop=False, mode=2)
apply_command(c, cmd)
time.sleep(1.0)

# Opdater kommandoer igen
cmd = Command(setpoint_int=200, setpoint_real=4.56, cmd_start=False, cmd_stop=True, mode=5)
apply_command(c, cmd)

# Læs telemetri igen
time.sleep(0.5)
t1 = parse_telemetry(c.db_read(DB, 0, 12))
print("TEL:", t1)

c.disconnect(); c.destroy()
print("Færdig.")
```

**Kør i en NY terminal:**

```powershell
py 06-dataclass-mapping\map_client.py
```

**Forventet:**

* Konsollen viser `TEL: Telemetry(...)` før/efter.
* Server-vinduet udskriver `WRITE: {...}` når du skriver (INT/REAL/bits/BYTE).

---

## 🧠 Hvad vandt vi med dataclasses?

* **Læsbarhed:** Felter har navne og typer → lettere at forstå & validere.
* **Vedligehold:** Ændrer DB-layout? Opdatér ét sted i mapping-funktionen.
* **Sikkerhed:** `apply_command` bruger **read–modify–write** for bits, så du ikke overskriver andre bit i samme byte.

---

## ✅ Definition of Done (DoD)

* [ ] Du kan printe **Telemetry** som et Python-objekt.
* [ ] Du kan skrive en **Command** og se ændringer på serveren.
* [ ] Du kan forklare hvorfor bits kræver **read–modify–write**.

---

## 🧪 Mini-øvelser

1. **Udvid Telemetry** med et ekstra felt (`DBD12` som REAL) – tilføj i server + mapping.
2. **Validering:** Indfør grænser for `setpoint_int` (0–500). Afvis ellers.
3. **Round-trip:** Læs Telemetry → beslut en Command (fx stop hvis `alarm=True`) → skriv Command.

---

## 🆘 Fejlsøgning

* **Ingen WRITE-logs i serveren:** Kører `server_combined.py`? Samme **PORT (1102)**?
* **Værdier “hopper”:** Tjek **offsets** og læs nok **bytes** (`db_read(DB, start, length)`).
* **Bits opfører sig underligt:** Husk at **læse byte 26** først, ændre **bit 0/1**, og skrive **samme byte** tilbage.

---

## ➡️ Næste modul: **07 – Logging**

Vi logger Telemetry til **CSV** hvert sekund og (valgfrit) laver en enkel live-plot. Klar til “rigtig dataopsamling” 📈
