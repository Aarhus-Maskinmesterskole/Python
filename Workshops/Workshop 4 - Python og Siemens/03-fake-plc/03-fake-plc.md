# **03 – Fake PLC (snap7-server)**

*(ingen hardware · trin-for-trin · **2 små filer**)*

**Mål:** Start en **lokal, simuleret PLC** (snap7 server) og læs din **første værdi** fra DB1 i Python — helt uden udstyr.
**Forventet tid:** 15–25 min.
**Forudsætninger:** Modul **02 – Installation** gennemført (Python + VS Code + `python-snap7` virker).

---

## 🗂️ Mappe & filer

Opret mappen `03-fake-plc` i dit projekt og læg **to** filer i den:

* `server.py` → kører en fake PLC, der tæller i DB1
* `client_read.py` → læser værdien fra DB1

---

## 🧪 Step 1 — Start fake PLC (server)

**`03-fake-plc/server.py`**

```python
from time import sleep
from snap7.server import Server
from snap7.types import Areas
import snap7.util as s7
import ctypes

# Konfiguration
DB_NUM  = 1
DB_SIZE = 32
PORT    = 1102   # 1102 kræver typisk IKKE admin (modsat 102)

# Opret server + DB-område
srv = Server()
db  = (ctypes.c_uint8 * DB_SIZE)()
srv.register_area(Areas.DB, DB_NUM, db, DB_SIZE)

# Start server
srv.start(tcpport=PORT)
print(f"Fake PLC kører på 127.0.0.1:{PORT}  (DB{DB_NUM} = {DB_SIZE} bytes). Ctrl+C for at stoppe.")

# Skriv en tæller til DBD0 hvert sekund (4 bytes DINT @ offset 0)
i = 0
while True:
    s7.set_dint(db, 0, i)
    i += 1
    sleep(1)
```

**Kør i VS Code-terminalen:**

```powershell
py 03-fake-plc\server.py
```

Lad serveren køre i dette terminalvindue.

---

## 📥 Step 2 — Læs første værdi (klient)

**`03-fake-plc/client_read.py`**

```python
import time, snap7
from snap7.util import get_dint

IP, RACK, SLOT, PORT = "127.0.0.1", 0, 1, 1102

c = snap7.client.Client()
c.connect(IP, RACK, SLOT, PORT)

for _ in range(5):
    data = c.db_read(1, 0, 4)          # DB1, fra byte 0, 4 bytes
    print("DINT @ DB1.DBD0 =", get_dint(data, 0))
    time.sleep(1)

c.disconnect()
c.destroy()
```

**Kør i en NY terminal:**

```powershell
py 03-fake-plc\client_read.py
```

**Forventet output:**
En tæller, der stiger (0, 1, 2, 3, 4 …) — præcis den værdi, serveren skriver i DB1.

---

## 🧠 Hvad skete der?

* **Serveren** registrerer et **DB-område** (DB1) på **32 bytes** og **opdaterer** de første 4 bytes hvert sekund med en DINT.
* **Klienten** kobler på `127.0.0.1:RACK0/SLOT1` via **port 1102**, læser **4 bytes** fra DB1 offset **0** og fortolker dem som **DINT**.

> **Tip:** 1200/1500-PLC’er bruger typisk **rack=0, slot=1**. Vi bruger samme konvention her — det er bare parametre til Snap7-klienten.

---

## ✅ Definition of Done (DoD)

* Du kan starte **server.py** uden fejl (den bliver stående og tæller).
* **client\_read.py** udskriver en stigende værdi fra **DB1.DBD0** fem gange.
* Du kan forklare med dine egne ord: **DB-nummer**, **offset**, **længde** (bytes) og **datatype** (DINT).

---

## 🆘 Fejlsøgning (hurtige fixes)

* **`ModuleNotFoundError: snap7`** → Sørg for at **.venv** er aktiv (se modul 02) og kør `py -m pip install python-snap7`.
* **`Connection refused` eller timeout** → Er **server.py** startet? Bruger **begge** filer **samme port** (1102)?
* **Port 102 kræver admin** → Vi bruger 1102 netop for at undgå det.
* **Firewall** → Tillad Python i Windows Firewall, hvis den spørger.
* **Intet tal / mærkelige værdier** → Læser du **4 bytes** fra **offset 0** og bruger `get_dint(..., 0)`?

---

## 🎯 Næste skridt

Videre til **04 – Read data**: Læs flere **typer** (INT/DINT/REAL/BOOL) fra forskellige offsets og forstå præcis, **hvordan bytes bliver til rigtige værdier**.
