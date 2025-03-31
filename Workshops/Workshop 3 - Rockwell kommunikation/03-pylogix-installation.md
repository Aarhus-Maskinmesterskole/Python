# 💻 Installation af pylogix og opsætning i Python

I dette modul installerer vi `pylogix`-biblioteket og laver en hurtig testforbindelse til din Rockwell PLC.

### 🔧 Installation af pylogix
Først skal du have installeret `pylogix`, `matplotlib` og `numpy` i dit Python-miljø. Du kan bruge pip:

```bash
pip install pylogix matplotlib numpy
```

> 🚫 Hvis du bruger en virtuel miljø (fx `venv`), så aktivér det før installation.

Bekræft at det virker:
```bash
pip show pylogix
```

### 📂 Opret testprojekt i Python
Opret en ny Python-fil, fx `test_connection.py`, og indsæt følgende kode:

```python
from pylogix import PLC

comm = PLC()
comm.IPAddress = '192.168.1.10'  # Udskift med din PLCs IP
result = comm.Read('TempSensor')
comm.Close()

print(f"Værdi fra TempSensor: {result.Value}")
```

Hvis alt virker, vil du se værdien af `TempSensor` printet i terminalen.

### 🚫 Almindelige fejl og løsninger
| Fejl | Løsning |
|------|---------|
| `None` i `result.Value` | Tag-navnet er forkert eller ikke i controller scope |
| `Connection Error` | Tjek IP-adresse, netværk, firewall |
| `ImportError` | Sørg for at `pylogix` er korrekt installeret |

### ✅ Kontrolspørgsmål
- Har du noteret den korrekte IP-adresse på PLC'en?
- Er `TempSensor` oprettet som tag i controlleren?
- Kan du pinge PLC'en fra terminalen?

---

Når du har testet forbindelsen, er du klar til at læse og skrive data – det tager vi fat på i næste modul: `04-read-data.md`.

