# **09 – Pakning til .exe (Windows)**

*(Tkinter · VS Code · **step-by-step med små snippets**)*

**Mål:** Byg en **selvstændig .exe** af din Tkinter-app, så den kan køre på Windows uden Python installeret.
**Forventet tid:** 45–90 min.
**Forudsætninger:** Du har en fungerende app fra modul 05–08 (fx `main.py`), i et aktivt `.venv`.

> Vi bruger **PyInstaller** (ingen ekstra værktøjer). Tkinter understøttes direkte.

---

## 🪜 Step 0 — Klargør projektet

**Struktur (eksempel):**

```
projekt/
├─ main.py            # din app's entrypoint (if __name__ == "__main__")
├─ assets/            # ikoner, billeder, startdata
│  └─ app.ico
└─ venv/.venv         # dit virtuelle miljø (ikke med i exe)
```

**Husk i `main.py`:**

* En **entydig startfunktion** og `if __name__ == "__main__": main()`
* **Ingen absolutte stier** – vi løser data-stier i Step 3.

---

## 🧰 Step 1 — Installer PyInstaller (i .venv)

I VS Code terminalen (PowerShell) med `.venv` aktiv:

```powershell
py -m pip install --upgrade pip
py -m pip install pyinstaller
```

---

## ⚙️ Step 2 — Første build (onefile & windowed)

* **`--onefile`** pakker alt i én .exe (langsommere opstart, nem deling)
* **`--noconsole`** (eller `--windowed`) skjuler konsolvindue til GUI-apps
* **`--icon`** giver et ikon til .exe (valgfrit)

```powershell
py -m PyInstaller `
  --onefile `
  --noconsole `
  --icon assets\app.ico `
  --name MinApp `
  main.py
```

**Output:**

* `dist\MinApp.exe`  ✅
* `build\` og en `.spec`-fil (PyInstallers opskrift)

> **Tip:** Drop `--onefile` og brug **`--onedir`** hvis du vil have **hurtigere opstart** (exe + mappe).

---

## 📦 Step 3 — Medtag datafiler (ikoner, skabeloner, db)

Når din kode skal bruge filer (fx `assets\logo.png`, `assets\app.db`), skal de **med** i buildet **og** koden skal kunne finde dem ved runtime.

### 3a) Build-flag

På **Windows** bruges `;` som separator i `--add-data`:

```powershell
py -m PyInstaller --onefile --noconsole --name MinApp `
  --add-data "assets\app.ico;assets" `
  --add-data "assets\startdata.json;assets" `
  main.py
```

### 3b) Find filerne ved runtime

I din kode, brug en lille helper til at finde ressource-stien — virker både i udvikling og i .exe:

```python
# utils_paths.py
import os, sys

def resource_path(relative_path: str) -> str:
    """Returnerer absolut sti til en ressource i udvikling ELLER i PyInstaller .exe."""
    base = getattr(sys, "_MEIPASS", os.path.abspath("."))
    return os.path.join(base, relative_path)
```

Brug den fx:

```python
from utils_paths import resource_path
ICON_PATH = resource_path("assets/app.ico")
```

---

## 🧪 Step 4 — Test & fejlsøgning

* Kør `dist\MinApp.exe` **fra Stifinder** (ikke kun fra terminal)
* Test på en **anden Windows-konto** (eller en ren VM)
* Hvis noget mangler:

  * Byg med `--clean` for en frisk build
  * Tjek at alle datafiler er tilføjet med `--add-data`
  * Brug midlertidigt `--console` for at se fejl i en sort boks

```powershell
py -m PyInstaller --onefile --clean --noconsole main.py
```

---

## 🧾 Step 5 — Brug `.spec` for flere tilpasninger (valgfrit)

Når du har bygget én gang, ligger der en **`MinApp.spec`**. Du kan redigere den for at håndtere mange filer mere overskueligt.

**Eksempel: tilføj data i spec:**

```python
# i MinApp.spec
datas = [
    ('assets/app.ico', 'assets'),
    ('assets/startdata.json', 'assets'),
]
a = Analysis(['main.py'], ... , datas=datas, ...)
```

Byg derefter **fra .spec**:

```powershell
py -m PyInstaller MinApp.spec
```

**Skjulte imports:** Hvis noget importeres dynamisk, kan PyInstaller misse det. Tilføj i spec:

```python
hiddenimports = ['modulX', 'modulY']
```

---

## 🖼️ Step 6 — Ikoner & vinduesikon

* **.exe-ikon:** `--icon assets\app.ico` i build-kommandoen
* **Vinduesikon i Tkinter:**

```python
root = tk.Tk()
root.iconbitmap(ICON_PATH)  # brug resource_path fra Step 3
```

---

## 🗃️ Step 7 — SQLite & start-DB (hvis relevant)

* Medtag en **start-database** (fx `assets\app.db`) via `--add-data`
* Når du skriver til DB: skriv til en **brugersti** (fx `%APPDATA%\MinApp\app.db`), ikke ned i exe-mappen.

**Eksempel (flyt til brugerprofil første gang):**

```python
import os
from utils_paths import resource_path
from pathlib import Path
import shutil

def ensure_user_db():
    target = Path(os.getenv("APPDATA")) / "MinApp" / "app.db"
    target.parent.mkdir(parents=True, exist_ok=True)
    if not target.exists():
        shutil.copy(resource_path("assets/app.db"), target)
    return str(target)
```

---

## 🧹 Step 8 — Ryd op & scripts

Lav en lille build-script så de studerende slipper for at huske flags.

**`build_exe.ps1` (i projektroden):**

```powershell
# Kør fra VS Code terminal (PowerShell) med .venv aktiv
$Name = "MinApp"
py -m PyInstaller --clean --noconsole --onefile `
  --name $Name `
  --icon assets\app.ico `
  --add-data "assets\app.ico;assets" `
  --add-data "assets\startdata.json;assets" `
  main.py

Write-Host "Build færdig: dist\$Name.exe"
```

---

## 🛡️ Kendte bump (og fixes)

* **Antivirus/SmartScreen brokker sig:** Underskriv exe (code signing) i produktion; til undervisning: kør fra en “tillidsmappe”.
* **“Missing DLL”/VCRUNTIME:** Opdatér Windows eller installer VC++ redistributable (sjældent nødvendigt).
* **Langsom first-run i `--onefile`:** Overvej `--onedir` for hurtigere start.
* **Stier virker i udvikling men ikke i exe:** Brug **`resource_path(...)`** (Step 3) – ikke `__file__`/relative stier.
* **DPI/skalerings-issues:** Sæt en større standardfont (fx `root.option_add("*Font", "Segoe UI 10/11")`).

---

## ✅ Definition of Done (Modul 09)

* Du kan bygge en **.exe** der starter uden Python installeret
* Ikon og eventuelle datafiler **følger** med og findes ved runtime
* Appen kører på en **anden Windows-konto**/maskine uden fejl
