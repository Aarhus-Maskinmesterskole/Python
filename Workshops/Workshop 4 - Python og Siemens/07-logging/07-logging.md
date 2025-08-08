# **07 – Logging (CSV + valgfri live-plot)**

*(ingen hardware · trin-for-trin · **1–2 små filer**)*

**Mål:** Log **telemetri** fra DB til **CSV** hvert sekund. (Valgfrit) lav en **live-graf** under logning.
**Forudsætninger:** 02–06 gennemført. Du kan genbruge serveren fra **06**: `06-dataclass-mapping/server_combined.py`.

---

## 🗂️ Mappe & filer

Opret mappen **`07-logging`**:

* `logger_csv.py` → logger til `log.csv` (obligatorisk)
* `live_plot.py` → simpel live-graf (valgfri, kræver `matplotlib`)

---

## 🧪 Step 1 — Start fake PLC (genbrug fra 06)

I én terminal:

```powershell
py 06-dataclass-mapping\server_combined.py
```

Lad den køre. Den opdaterer telemetri i DB1 og printer WRITE-ændringer.

---

## 📥 Step 2 — Log til CSV (obligatorisk)

**`07-logging/logger_csv.py`**

```python
import time, csv, snap7
import snap7.util as s7
from datetime import datetime

IP, RACK, SLOT, PORT = "127.0.0.1", 0, 1, 1102
DB = 1

# Felter vi logger (matcher serverens Telemetry fra modul 06)
def read_telemetry(c: snap7.client.Client):
    buf = c.db_read(DB, 0, 12)  # DBD0..DBB11
    return {
        "ts": datetime.now().isoformat(timespec="seconds"),
        "counter":     s7.get_dint(buf, 0),
        "temperature": s7.get_int(buf, 4),
        "pressure":    s7.get_real(buf, 6),
        "alarm":       s7.get_bool(buf, 10, 0),
        "mode_ro":     s7.get_byte(buf, 11),
    }

def main():
    c = snap7.client.Client()
    c.connect(IP, RACK, SLOT, PORT)
    print("Forbundet. Logger til log.csv (Ctrl+C for at stoppe).")

    with open("log.csv", "w", newline="", encoding="utf-8") as f:
        fieldnames = ["ts","counter","temperature","pressure","alarm","mode_ro"]
        w = csv.DictWriter(f, fieldnames=fieldnames)
        w.writeheader()

        try:
            while True:
                row = read_telemetry(c)
                w.writerow(row)
                f.flush()           # skriv til disk med det samme
                print("LOG:", row)
                time.sleep(1)
        except KeyboardInterrupt:
            print("\nStopper logning...")
    c.disconnect(); c.destroy()

if __name__ == "__main__":
    main()
```

**Kør i en NY terminal:**

```powershell
py 07-logging\logger_csv.py
```

**Forventet:**

* I konsollen: linjer med `LOG: {...}` hvert sekund
* I mappen: en **`log.csv`** med kolonnerne `ts,counter,temperature,pressure,alarm,mode_ro`

---

## 📊 Step 3 — Live-graf (valgfri)

Installer først pakken:

```powershell
py -m pip install matplotlib
```

**`07-logging/live_plot.py`** *(meget enkel linjegraf af `counter`)*

```python
import time, collections, snap7, snap7.util as s7
import matplotlib.pyplot as plt

IP, RACK, SLOT, PORT = "127.0.0.1", 0, 1, 1102
DB = 1
WINDOW = 60  # seneste N punkter

def read_counter(c):
    buf = c.db_read(DB, 0, 4)    # kun DBD0
    return s7.get_dint(buf, 0)

def main():
    c = snap7.client.Client(); c.connect(IP, RACK, SLOT, PORT)
    xs = collections.deque(maxlen=WINDOW)
    ys = collections.deque(maxlen=WINDOW)

    plt.ion()
    fig, ax = plt.subplots()
    line, = ax.plot([], [])
    ax.set_xlabel("samples"); ax.set_ylabel("counter"); ax.set_title("Live counter")

    t = 0
    try:
        while plt.fignum_exists(fig.number):
            y = read_counter(c)
            xs.append(t); ys.append(y)
            line.set_data(range(len(ys)), list(ys))
            ax.relim(); ax.autoscale_view()
            plt.pause(0.01)
            t += 1
            time.sleep(1)
    except KeyboardInterrupt:
        pass
    finally:
        c.disconnect(); c.destroy()
        plt.ioff(); plt.show()

if __name__ == "__main__":
    main()
```

**Kør:**

```powershell
py 07-logging\live_plot.py
```

Du ser en enkel graf, der opdaterer hvert sekund.

---

## ✅ Definition of Done (DoD)

* [ ] `logger_csv.py` kører og skriver nye linjer til **`log.csv`** hvert sekund
* [ ] Kolonnerne matcher: `ts,counter,temperature,pressure,alarm,mode_ro`
* [ ] (Valgfrit) `live_plot.py` viser en opdaterende graf uden fejl

---

## 🧪 Mini-øvelser

1. **Sample-interval:** Skift `time.sleep(1)` til 0.5 s og se effekten.
2. **Filrotation:** Lav en ny logfil pr. time/dag (`log_YYYYMMDD.csv`).
3. **Alarm-markering:** Skriv kun en linje, når `alarm` ændrer til/fra `True`.
4. **Flere signaler:** Tilføj `setpoint_int` osv. (kræver læsning fra deres offsets).

---

## 🆘 Fejlsøgning

* **`PermissionError: log.csv i brug`** → Luk filen i Excel før du logger igen.
* **Graf fryser** → Brug `plt.ion()` og `plt.pause(...)` som i eksemplet, eller luk figuren for at afslutte.
* **`ModuleNotFoundError: matplotlib`** → Installer pakken i **.venv** (se kommando ovenfor).
* **Ingen data** → Kører serveren? Samme **PORT (1102)**? Læser du de rigtige længder/offsets?

---

## ➡️ Næste modul: **08 – Error handling**

Vi gør forbindelsen **robust**: timeouts, automatisk **reconnect**, og venlige fejlbeskeder (uden at blokere GUI’er eller logloops).
