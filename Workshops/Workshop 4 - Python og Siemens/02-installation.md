# **02 – Installation (Windows + VS Code)**

*(ingen forudsætninger · trin-for-trin · 1 lille testfil)*

## 🎯 Mål

Når du er færdig med dette modul, kan du:

* køre **Python 3.12+** på Windows
* arbejde i **VS Code** med korrekt **.venv**
* installere **python-snap7**
* **verificere** at Snap7 virker med en lille testfil

> **Output:** En kørende fil `02-install/check_snap7.py`, der printer Snap7-versionen ✅

---

## 🧰 Det skal du bruge

* **Windows 10/11**
* Internet (til at hente Python, VS Code og pakker)

---

## 🪜 Trin-for-trin

### 1) Installér Python

* Åbn **Microsoft Store** → søg **“Python 3.12”** → *Installér*
  *(Alternativt: hent fra* python.org → Downloads → Windows\*)
* Luk/åbn **Windows Terminal** (eller PowerShell) og test:

  ```powershell
  py --version
  ```

  Du bør se `Python 3.12.x`.

### 2) Installér VS Code + udvidelser

* Hent **Visual Studio Code** fra code.visualstudio.com
* Åbn VS Code → **Extensions** (Ctrl+Shift+X) → installer:

  * **Python** (Microsoft)
  * **Pylance** (Microsoft)

### 3) Opret projektmappe og åbn den

* Lav en mappe, fx: `C:\Users\<dig>\Documents\snap7-workshop`
* I VS Code: **File → Open Folder…** → vælg mappen

### 4) Opret og aktivér virtuelt miljø (.venv)

Åbn **Terminal** i VS Code (Ctrl+\`):

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
```

> **Hvis scripts er blokeret første gang:**
>
> ```powershell
> Set-ExecutionPolicy -Scope CurrentUser RemoteSigned -Force
> ```
>
> Luk/åbn terminalen igen og aktivér:
>
> ```powershell
> .\.venv\Scripts\Activate.ps1
> ```

### 5) Installér python-snap7

```powershell
py -m pip install --upgrade pip
py -m pip install python-snap7
```

### 6) Vælg den rigtige fortolker i VS Code

* Klik på Python-versionen nederst til venstre i statuslinjen
* Vælg fortolkeren fra **.venv** (fx `.\.venv\Scripts\python.exe`)

### 7) Lav testfilen og kør den

Opret mappen `02-install\` og filen **`check_snap7.py`**:

```python
# 02-install/check_snap7.py
import snap7
print("python-snap7 OK – version:", getattr(snap7, "__version__", "ukendt"))
```

Kør i terminalen (stadig i projektroden):

```powershell
py 02-install\check_snap7.py
```

**Forventet output:** `python-snap7 OK – version: ...` ✅

---

## 🧪 Definition of Done (DoD)

* [ ] `py --version` viser **Python 3.12+**
* [ ] VS Code er installeret med **Python** + **Pylance**
* [ ] **.venv** er oprettet **og aktiv** i terminalen
* [ ] Du har valgt **.venv**-fortolkeren i VS Code
* [ ] `check_snap7.py` kører og printer Snap7-versionen

---

## 🆘 Fejlsøgning (hurtige fixes)

**`py` ikke fundet**

* Genåbn terminalen eller genstart Windows.
* Installer Python fra Microsoft Store eller python.org.

**Kan ikke aktivere `.venv`**

* Kør `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned -Force`, luk/åbn terminal og prøv igen.

**`ModuleNotFoundError: No module named 'snap7'`**

* Sørg for at **.venv er aktiv**, og kør: `py -m pip install python-snap7`.

**“cannot load snap7/snap7.dll”**

* Luk/åbn VS Code-terminalen og prøv igen.
* Tjek at du bruger **64-bit Python** (standard fra Store/python.org).
* Kør: `py -m pip install --force-reinstall python-snap7`.

**VS Code bruger forkert Python**

* Klik på versionen i statuslinjen → vælg **.venv**-fortolkeren.

---

## ⏭️ Næste modul: **03 – Fake PLC (snap7-server)**

I næste modul starter du en **lokal, simuleret PLC** (server) og læser din **første værdi** fra DB1 — helt uden hardware.
*(Vi bruger port **1102** i eksemplerne for at undgå admin-krav på port 102.)*
