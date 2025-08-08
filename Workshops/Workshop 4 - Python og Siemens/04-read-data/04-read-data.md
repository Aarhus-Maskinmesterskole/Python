# **04 – Read data (DB → typer)**

*(ingen hardware · trin-for-trin · **2 små filer**)*

**Mål:** Læs **rå bytes** fra en Data Block (DB) og fortolk dem som **BOOL/INT/DINT/REAL/BYTE** med `python-snap7`.
**Forventet tid:** 20–30 min.
**Forudsætninger:** 02 (installation) og 03 (fake PLC) gennemført.

---

## 🗂️ Mappe & filer

Opret mappen **`04-read-data`** i dit projekt med disse to filer:

* `fake_server_types.py` → en *fake PLC*, der opdaterer flere felter i DB1
* `read_types.py` → en lille klient, der læser og **pakker bytes ud** til rigtige typer

---

## 🧭 DB-layout (vi bruger i eksemplet)

| Offset        | Type | Symbolsk felt | Note                            |
| ------------- | ---- | ------------- | ------------------------------- |
| `DBD0` (0..3) | DINT | `counter`     | tæller +1 hvert sekund          |
| `DBW4` (4..5) | INT  | `temperature` | fast 25                         |
| `DBD6` (6..9) | REAL | `pressure`    | fast 1.23                       |
| `DBX10.0`     | BOOL | `alarm`       | skifter sand/falsk hvert sekund |
| `DBB11` (11)  | BYTE | `mode`        | fast 3                          |

> **Husk:** DBD = 4 bytes, DBW = 2 bytes, DBB = 1 byte, DBXbit = 1 *bit*.

---

## 🧪 Step 1 — Start fake PLC med felter

**`04-read-data/fake_server_types.py`**

```python
from time import sleep
from snap7.server import Server
from snap7.types import Areas
import snap7.util as s7
import ctypes

DB_NUM, DB_SIZE, PORT = 1, 64, 1102  # 1102 undgår admin-krav
srv = Server()
db = (ctypes.c_uint8 * DB_SIZE)()
srv.register_area(Areas.DB, DB_NUM, db, DB_SIZE)

srv.start(tcpport=PORT)
print(f"Fake PLC (typer) kører på 127.0.0.1:{PORT} – Ctrl+C for stop.")

i = 0
while True:
    s7.set_dint(db, 0, i)            # DBD0: counter
    s7.set_int(db, 4, 25)            # DBW4: temperature
    s7.set_real(db, 6, 1.23)         # DBD6: pressure
    s7.set_bool(db, 10, 0, (i % 2 == 0))  # DBX10.0: alarm toggler
    s7.set_byte(db, 11, 3)           # DBB11: mode
    i += 1
    sleep(1)
```

**Kør i en terminal (lad køre):**

```powershell
py 04-read-data\fake_server_types.py
```

---

## 📥 Step 2 — Læs og fortolk felterne

**`04-read-data/read_types.py`**

```python
import time, snap7
import snap7.util as s7

IP, RACK, SLOT, PORT = "127.0.0.1", 0, 1, 1102
DB = 1

c = snap7.client.Client()
c.connect(IP, RACK, SLOT, PORT)

def read_all():
    buf = c.db_read(DB, 0, 16)  # læs 16 bytes fra DB1
    counter = s7.get_dint(buf, 0)       # DBD0
    temperature = s7.get_int(buf, 4)    # DBW4
    pressure = s7.get_real(buf, 6)      # DBD6
    alarm = s7.get_bool(buf, 10, 0)     # DBX10.0
    mode = s7.get_byte(buf, 11)         # DBB11
    print(f"counter={counter}  temp={temperature}°C  "
          f"press={pressure:.2f}bar  alarm={alarm}  mode={mode}")

for _ in range(5):
    read_all()
    time.sleep(1)

c.disconnect()
c.destroy()
```

**Kør i en NY terminal:**

```powershell
py 04-read-data\read_types.py
```

**Forventet output:**
En linje pr. sekund, fx:

```
counter=7  temp=25°C  press=1.23bar  alarm=True  mode=3
```

---

## 🧠 Hvad lærte du her?

* Du kan læse **en blok rå bytes** og **pakke** dem til typer med `snap7.util` (`get_dint`, `get_int`, `get_real`, `get_bool`, `get_byte`).
* **Offset** + **type** + **længde** bestemmer, hvad du faktisk får ud.
* En **fake PLC** kan give dig realistiske øvelser uden hardware.

---

## ✅ Definition of Done (DoD)

* [ ] Fake serveren kører og opdaterer felter i DB1.
* [ ] Klienten udskriver korrekte værdier (counter stiger, alarm skifter).
* [ ] Du kan forklare forskel på **DBD/DBW/DBB/DBX** og hvorfor vi læser lidt “ekstra” bytes (her 16) for at dække alle felter.

---

## 🛠️ Mini-øvelser

1. **Tilføj felt:** Læg en ekstra `REAL` på `DBD12` i serveren og læs den i klienten.
2. **Anden bit:** Skift fra `DBX10.0` til `DBX10.3` og læs den rigtige bit i klienten.
3. **Hex-syn:** Print hele `buf` som hex (`buf.hex()`) for at se rå bytes.

---

## 🆘 Fejlsøgning

* **Intet output / fejl ved connect:** Er serveren startet? Samme **PORT (1102)** i begge filer?
* **Mærkelige værdier:** Tjek **offsets** og at du læser nok **længde** (`db_read(DB, start, length)`).
* **ImportError `snap7`**: Aktivér `.venv` og `pip install python-snap7` (se modul 02).

---

## ➡️ Næste modul: **05 – Write data**

Nu skriver vi **sikre testværdier** tilbage til DB’en (uden at træde andre felter over tæerne) og ser dem ændre sig live 🎯

