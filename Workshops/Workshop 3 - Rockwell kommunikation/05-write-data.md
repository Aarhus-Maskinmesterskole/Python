# 📤 Skrivning af tags til PLC med Python

I dette modul lærer du, hvordan du skriver data til en Rockwell PLC ved hjælp af `pylogix`. Dette gør det muligt at fjernstyre din PLC, sende sætpunkter eller aktivere handlinger.

### 📂 Eksempel: Skriv en værdi til et REAL-tag
```python
from pylogix import PLC

with PLC() as comm:
    comm.IPAddress = '192.168.1.10'
    write_result = comm.Write('SetPoint', 22.5)
    print(f"Skrivning lykkedes: {write_result.Status}")
```

> `SetPoint` skal være defineret som REAL i controller scope.

### 📂 Eksempel: Skriv til BOOL-tag
```python
with PLC() as comm:
    comm.IPAddress = '192.168.1.10'
    res_on = comm.Write('SwitchState', True)
    time.sleep(2)
    res_off = comm.Write('SwitchState', False)
    print(f"ON status: {res_on.Status}, OFF status: {res_off.Status}")
```

### 🚫 Begrænsninger
- Du kan ikke skrive til tags i **Program Scope**, kun **Controller Scope**.
- PLC'en skal være i RUN- eller REMOTE RUN-tilstand.
- Vær forsigtig med at skrive til output-tags, der styrer fysisk udstyr!

### ✅ Statushåndtering
`pylogix.Write()` returnerer et objekt med:
- `Status`: Tekstbaseret status ("Success", "Tag Not Found" etc.)
- `Value`: Den skrevne værdi (ikke altid returneret)

Brug dette til debugging:
```python
res = comm.Write('SetPoint', 15.0)
if res.Status != 'Success':
    print(f"Fejl ved skrivning: {res.Status}")
```

---

Nu hvor du kan skrive og læse data, er du klar til at visualisere dem i realtid i `06-realtime-plot.md`!

