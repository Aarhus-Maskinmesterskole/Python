# 📅 Læsning af tags fra PLC med Python

I dette modul lærer du, hvordan du læser tag-data fra din Rockwell PLC med `pylogix` og gemmer eller viser disse data i Python.

### 📓 Mål
- Forstå pylogix' `Read()`-funktion
- Hent realtidsdata fra et eller flere tags
- Formatér og udskriv data i Python

### 📂 Simpel aflæsning
```python
from pylogix import PLC

with PLC() as comm:
    comm.IPAddress = '192.168.1.10'  # Din PLC IP
    result = comm.Read('TempSensor')
    print(f"Temperatur: {result.Value}")
```

> `with PLC() as comm:` sikrer, at forbindelsen lukkes korrekt bagefter.

### 🤑 Læsning af flere tags
```python
with PLC() as comm:
    comm.IPAddress = '192.168.1.10'
    tags = ['TempSensor', 'SetPoint', 'SwitchState']
    for tag in tags:
        res = comm.Read(tag)
        print(f"{tag}: {res.Value}")
```

### 🔎 Aflæsning i løkke (simuleret realtid)
```python
import time

with PLC() as comm:
    comm.IPAddress = '192.168.1.10'
    for i in range(10):
        res = comm.Read('TempSensor')
        print(f"{i}: {res.Value}")
        time.sleep(1)
```

### 🚫 Undgå fejlkilder
- Brug nøjagtige tag-navne som defineret i Studio 5000
- PLC'en skal være i RUN-tilstand (eller Echo skal køre)
- Tjek at tags er i **Controller Scope** og ikke i Program Scope

### 🌟 Bonus: Gem data i liste
```python
data = []
with PLC() as comm:
    comm.IPAddress = '192.168.1.10'
    for _ in range(20):
        val = comm.Read('TempSensor').Value
        data.append(val)
        time.sleep(0.5)
print(data)
```

---

Næste trin: Nu hvor du kan læse data, skal du lære at skrive til PLC'en med `Write()` i `05-write-data.md`.

