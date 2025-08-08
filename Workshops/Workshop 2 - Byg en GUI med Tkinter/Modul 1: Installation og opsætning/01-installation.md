# Installation (Windows + VS Code)

*Gælder Workshop 2 · Modul 1 — Tkinter GUI. Ingen Git/GitHub.*

## Hvad du ender med

Efter denne guide har du:

* Python **3.12+** installeret
* **VS Code** installeret med **Python**- og **Pylance**-udvidelser
* Et **projektfolder** klar til kurset
* Et **virtuelt miljø** (`.venv`) aktivt i VS Code
* Verificeret at **Tkinter** virker på din maskine

---

## Forudsætninger

* **Windows 10/11**
* Internetadgang (til at hente Python og VS Code)
* Rettigheder til at installere programmer

---

## 1) Installér Python

**Anbefalet (nemmest):**

1. Åbn **Microsoft Store** → søg efter **“Python 3.12”** → Installer.
2. Luk og genåbn **Windows Terminal** (eller PowerShell), så PATH opdateres.

**Alternativ (hvis Store er blokeret):**

* Gå til **python.org → Downloads → Windows** og hent **seneste 64-bit installer**.
* Vælg *Install Now* (standardvalg inkluderer **Tcl/Tk**, som Tkinter bruger).

**Tjek installationen:**

* Åbn **Windows Terminal → PowerShell** og skriv:

  * `py --version`  *(eller)*  `python --version`
    Du bør se noget á la `Python 3.12.x`.

---

## 2) Installér Visual Studio Code

* Hent fra **code.visualstudio.com** og installer.
* Start **VS Code** → Gå til **Extensions** (Ctrl+Shift+X) → installer:

  * **Python** (Microsoft)
  * **Pylance** (Microsoft)

---

## 3) Opret projektmappen

* Opret en mappe, fx: `C:\Users\<dig>\Documents\tkinter-workshop`
* I VS Code: **File → Open Folder…** → vælg mappen.

> **Tip:** Brug en lokal mappe (ikke netværksdrev eller synk-mapper med restriktioner), så undgår du tilladelsesproblemer.

---

## 4) Opret og aktivér virtuelt miljø i VS Code

Åbn **Terminal** i VS Code (Ctrl+\`):

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
```

**Hvis scripts er blokeret første gang:**
Kør (én gang):

```powershell
Set-ExecutionPolicy -Scope CurrentUser RemoteSigned -Force
```

Luk/åbn terminalen igen og aktivér:

```powershell
.\.venv\Scripts\Activate.ps1
```

**Vælg korrekt fortolker i VS Code:**

* Klik på Python-versionen nederst i VS Code-statuslinjen
* Vælg den, der peger på **`.venv`** (fx “.venv\Scripts\python.exe”)

---

## 5) Verificér at Tkinter virker

I VS Codes terminal (med venv aktiv), kør en kort verifikation:

```powershell
py - << 'PY'
import tkinter as tk
print("Tkinter OK – Tk version:", tk.TkVersion)
PY
```

Du bør se fx: `Tkinter OK – Tk version: 8.6…`
**Hvis du får fejl om `tkinter`:**

* Geninstaller Python fra **python.org** med standardindstillinger (Tcl/Tk følger med).

---

## 6) (Valgfrit) Installér testværktøj til senere moduler

I terminalen:

```powershell
py -m pip install -U pip
py -m pip install pytest
```

---

## 7) Fejlsøgning (hurtige fixes)

* **`py` eller `python` ikke fundet** → Genåbn terminalen eller genstart PC. Tjek at Python er installeret.
* **Kan ikke aktivere `.venv`** → Kør `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned -Force`, luk/åbn terminal og prøv igen.
* **Forkert fortolker i VS Code** → Vælg `.venv` via statuslinjen (nederst) eller `Ctrl+Shift+P` → “Python: Select Interpreter”.
* **Tkinter-fejl** → Brug python.org-installeren (64-bit), standard “Install Now”.

---

## 8) Klar til næste skridt

Når ovenstående virker, er du klar til **Modul 2**, hvor du åbner dit første vindue og prøver de første widgets og layoutprincipper.
