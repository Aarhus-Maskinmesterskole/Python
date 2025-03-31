# 📊 Real-time visualisering af PLC-data

I dette modul bygger vi et real-time plot i Python, som viser data direkte fra PLC’en. Dette er nyttigt til overvågning, fejlsøgning og visuel analyse.

### 📂 Krav
- `pylogix`, `matplotlib`, `numpy` installeret
- Et PLC-tag som fx `TempSensor`, der opdateres kontinuerligt

### 🌐 Simpelt real-time plot
```python
from pylogix import PLC
import matplotlib.pyplot as plt
import time

x = []
y = []
start = time.time()

with PLC() as comm:
    comm.IPAddress = '192.168.1.10'

    plt.ion()  # Interaktiv tilstand
    fig, ax = plt.subplots()

    for i in range(50):
        val = comm.Read('TempSensor').Value
        t = time.time() - start
        x.append(t)
        y.append(val)

        ax.clear()
        ax.plot(x, y)
        ax.set_title("Real-time Temperatur")
        ax.set_xlabel("Tid (s)")
        ax.set_ylabel("°C")
        plt.pause(0.5)
```

> Brug `plt.ion()` og `plt.pause()` for at opdatere grafen i realtid.

### 🔹 Tilpasninger og forbedringer
- Brug `deque` for at begrænse datapunkter (fx seneste 100)
- Tilføj flere plots (fx `SetPoint` og `TempSensor` på samme graf)
- Gem data til CSV undervejs

### 🚫 Almindelige fejl
- Intet vises: Mangler `plt.ion()` eller `plt.pause()`
- Graf flimrer: Brug `ax.plot()` i stedet for `plt.plot()` direkte
- CPU-forbrug for højt: Sænk opdateringsfrekvens eller brug `blit`

### 📅 Ekstra opgave
Udvid dit script, så det også viser sætpunktet `SetPoint` som en rød stiplet linje i grafen:
```python
ax.plot(x, [22.5]*len(x), 'r--', label='SetPoint')
ax.legend()
```

---

Næste og sidste modul: `07-error-handling.md`, hvor vi gennemgår fejlhåndtering og diagnosticering.
