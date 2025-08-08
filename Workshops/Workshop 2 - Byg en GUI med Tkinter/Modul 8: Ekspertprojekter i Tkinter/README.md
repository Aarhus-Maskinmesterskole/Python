# **08 – Ekspertprojekter**

*(Tkinter · Windows + VS Code · **vælg ét spor** – step-by-step med små snippets)*

**Vælg ét projekt og byg det færdigt:**
A) **To-Do app** (JSON-persistens) · B) **Lommeregner** (tastaturgenveje) · C) **CRUD + SQLite** (formular + tabel).
**Forventet tid:** 2–5 timer afhængigt af ambitionsniveau.
**Forudsætninger:** 01–07 gennemført.

> **Filnavne (forslag):** `08a_todo.py`, `08b_calc.py`, `08c_crud_sqlite.py`
> Kør i VS Code med `.venv` aktiv. Snippets her er små og kan sættes direkte ind.

---

## 🅰️ Spor A — **To-Do app (JSON)**

Lav en lille opgaveliste med filter (Alle | Aktive | Færdige) og gem/indlæs til/fra **JSON**.

### 🎯 Mål og funktioner

* Tilføj opgaver (titel)
* Markér som færdig/ufærdig
* Slet opgave
* Filter: Alle | Aktive | Færdige
* Gem ved luk, indlæs ved start (`todo.json`)

### 🪜 Milestones

**M0 – Skelet + layout**

```python
import tkinter as tk
from tkinter import ttk
root = tk.Tk(); root.title("To-Do"); root.geometry("420x520")

top = ttk.Frame(root, padding=8); top.pack(fill="x")
mid = ttk.Frame(root, padding=(8,0)); mid.pack(fill="both", expand=True)
bot = ttk.Frame(root, padding=8); bot.pack(fill="x")

new_var = tk.StringVar()
ttk.Entry(top, textvariable=new_var).pack(side="left", fill="x", expand=True)
ttk.Button(top, text="Tilføj").pack(side="left", padx=(8,0))

listbox = tk.Listbox(mid, selectmode="browse"); listbox.pack(fill="both", expand=True)
status = ttk.Label(bot, text="0 opgaver", anchor="w"); status.pack(fill="x")
```

**M1 – Tilføj/Slet/Toggle**

* Tilføj: `items = []` (liste af dicts `{"title": "...", "done": False}`)
* Vis i `Listbox` med “✔/□ ” foran titlen

```python
items = []

def refresh():
    listbox.delete(0, "end")
    for it in items:
        box = "✔ " if it["done"] else "□ "
        listbox.insert("end", box + it["title"])
    status.config(text=f"{len(items)} opgaver")

def add_item():
    t = new_var.get().strip()
    if not t: return
    items.append({"title": t, "done": False})
    new_var.set(""); refresh()

def toggle_selected():
    sel = listbox.curselection()
    if not sel: return
    idx = sel[0]
    items[idx]["done"] = not items[idx]["done"]
    refresh()

def delete_selected():
    sel = listbox.curselection()
    if not sel: return
    items.pop(sel[0]); refresh()

root.bind("<Return>", lambda e: add_item())
```

**M2 – Filter (Alle/Aktive/Færdige)**

* Tilføj `filter_var = tk.StringVar(value="Alle")` + `ttk.Radiobuttons`
* Lad `refresh()` tage hensyn til filteret (vis subset).

**M3 – Persistens (JSON)**

```python
import json, os
PATH = "todo.json"

def load_state():
    if os.path.exists(PATH):
        with open(PATH, "r", encoding="utf-8") as f:
            return json.load(f)
    return []

def save_state():
    with open(PATH, "w", encoding="utf-8") as f:
        json.dump(items, f, ensure_ascii=False, indent=2)

items = load_state(); refresh()
root.protocol("WM_DELETE_WINDOW", lambda: (save_state(), root.destroy()))
```

**✅ DoD (To-Do)**

* Tilføj/markér/slet virker
* Filter virker
* Data bevares mellem kørsel (load ved start, save ved luk)

**⭐ Bonus**

* Redigér titel (dobbeltklik åbner lille `Toplevel`)
* Sortér: færdige nederst
* Batch: “Markér alle som færdige”

---

## 🅱️ Spor B — **Lommeregner**

Byg en enkel, stabil miniregner med både **knapper** og **tastatur**.

### 🎯 Mål og funktioner

* Knapper: 0–9, ., +, −, ×, ÷, =, C (ryd)
* Tastatur: 0–9, ., +, -, \*, /, Enter(=), Esc(C)
* Stabil evaluering (undgå `eval` direkte på rå input!)

### 🪜 Milestones

**M0 – Display + knapgitter**

```python
import tkinter as tk
from tkinter import ttk

root = tk.Tk(); root.title("Calc"); root.resizable(False, False)
disp_var = tk.StringVar(value="0")

disp = ttk.Entry(root, textvariable=disp_var, justify="right", width=18, font=("Segoe UI", 16))
disp.grid(row=0, column=0, columnspan=4, padx=8, pady=8, sticky="we")
for c in range(4): root.columnconfigure(c, weight=1)

layout = [
    ["7","8","9","/"],
    ["4","5","6","*"],
    ["1","2","3","-"],
    ["0",".","C","+"],
]
```

**M1 – Knaphandlere**

```python
def put(ch):
    cur = disp_var.get()
    disp_var.set("0" if cur=="0" and ch!="." else (cur + ch))

def clear():
    disp_var.set("0")

def calc():
    expr = disp_var.get().replace("×","*").replace("÷","/")
    try:
        # Sikker eval: begræns tegn og brug eval på kontrolleret udtryk
        if not all(c in "0123456789.+-*/() " for c in expr):
            raise ValueError("Ugyldigt tegn")
        val = eval(expr, {"__builtins__": {}}, {})
        disp_var.set(str(val))
    except Exception:
        disp_var.set("Fejl")

for r, row in enumerate(layout, start=1):
    for c, ch in enumerate(row):
        cmd = clear if ch=="C" else (calc if ch=="=" else (lambda ch=ch: put(ch)))
        ttk.Button(root, text=ch, command=cmd, width=4).grid(row=r, column=c, padx=4, pady=4, sticky="nsew")

ttk.Button(root, text="=", command=calc).grid(row=5, column=0, columnspan=4, sticky="nsew", padx=8, pady=(0,8))
```

**M2 – Tastaturgenveje**

```python
def key(e):
    k = e.keysym
    if k in "0123456789":
        put(k)
    elif k in ("plus","+"): put("+")
    elif k in ("minus","-"): put("-")
    elif k in ("asterisk","*"): put("*")
    elif k in ("slash","/"): put("/")
    elif k in ("period","comma","."): put(".")
    elif k in ("Return","KP_Enter"): calc()
    elif k == "Escape": clear()

root.bind("<Key>", key)
```

**✅ DoD (Calc)**

* Knapper **og** tastatur virker
* Undgår crashe på underlige input (viser “Fejl”)
* Ryd (C) nulstiller til “0”

**⭐ Bonus**

* Plus/minus-toggle (±)
* Procent (%)
* Historikpanel (sidste 10 udtryk)

---

## 🅾️ Spor C — **CRUD + SQLite**

En lille registreringsapp: **formular** + **tabel** (Treeview) med **gem/hent** i SQLite.

### 🎯 Mål og funktioner

* Opret/Læs/Opdater/Slet poster
* Vis i **Treeview**, redigér i formularfelter
* Gem/Hent i **SQLite** (`sqlite3` stdlib)

### 🪜 Milestones

**M0 – UI-skelet**

```python
import tkinter as tk
from tkinter import ttk, messagebox
import sqlite3, os

root = tk.Tk(); root.title("CRUD + SQLite"); root.geometry("720x480")

# Tabel
cols = ("id","navn","alder")
tree = ttk.Treeview(root, columns=cols, show="headings", selectmode="browse")
for c in cols: tree.heading(c, text=c.upper()); tree.column(c, width=140, anchor="center")
tree.pack(fill="both", expand=True, padx=12, pady=(12,0))

# Formular
form = ttk.Frame(root, padding=12); form.pack(fill="x")
id_var, name_var, age_var = tk.StringVar(), tk.StringVar(), tk.StringVar()
ttk.Label(form, text="ID").grid(row=0, column=0); ttk.Entry(form, textvariable=id_var, width=10).grid(row=0, column=1)
ttk.Label(form, text="Navn").grid(row=0, column=2); ttk.Entry(form, textvariable=name_var, width=20).grid(row=0, column=3)
ttk.Label(form, text="Alder").grid(row=0, column=4); ttk.Entry(form, textvariable=age_var, width=6).grid(row=0, column=5)

bar = ttk.Frame(root, padding=(12,0,12,12)); bar.pack(fill="x")
```

**M1 – DB-init + model‐helpers**

```python
DB = "app.db"
def db_init():
    with sqlite3.connect(DB) as con:
        con.execute("""CREATE TABLE IF NOT EXISTS personer(
            id TEXT PRIMARY KEY, navn TEXT NOT NULL, alder INTEGER)""")

def db_all():
    with sqlite3.connect(DB) as con:
        return con.execute("SELECT id, navn, alder FROM personer ORDER BY navn").fetchall()

def db_upsert(pid, navn, alder):
    with sqlite3.connect(DB) as con:
        con.execute("INSERT INTO personer(id,navn,alder) VALUES(?,?,?) "
                    "ON CONFLICT(id) DO UPDATE SET navn=excluded.navn, alder=excluded.alder",
                    (pid, navn, alder))
def db_delete(pid):
    with sqlite3.connect(DB) as con:
        con.execute("DELETE FROM personer WHERE id=?", (pid,))
```

**M2 – View-sync (indlæs/udfyld)**

```python
def refresh():
    tree.delete(*tree.get_children())
    for r in db_all():
        tree.insert("", "end", values=(r[0], r[1], r[2]))

def on_select(_=None):
    sel = tree.selection()
    if not sel: return
    v = tree.item(sel[0], "values")
    id_var.set(v[0]); name_var.set(v[1]); age_var.set(v[2])
tree.bind("<<TreeviewSelect>>", on_select)

db_init(); refresh()
```

**M3 – Opret/Opdatér/Slet knapper**

```python
def save():
    pid = id_var.get().strip(); navn = name_var.get().strip()
    try:
        alder = int(age_var.get().strip() or 0)
    except ValueError:
        messagebox.showwarning("Alder", "Alder skal være et heltal"); return
    if not pid or not navn:
        messagebox.showwarning("Validering", "ID og Navn er påkrævet"); return
    db_upsert(pid, navn, alder); refresh()

def delete():
    pid = id_var.get().strip()
    if not pid: return
    db_delete(pid); refresh()

ttk.Button(bar, text="Gem", command=save).pack(side="right")
ttk.Button(bar, text="Slet", command=delete).pack(side="right", padx=(0,8))
```

**✅ DoD (CRUD)**

* Vise alle rækker i Treeview
* Opret/Opdater/Slet fungerer og afspejles i tabellen
* Validering for tomme felter og alder som tal

**⭐ Bonus**

* Søg-felt der filtrerer Treeview
* “Ny” rydder formularen
* “Eksport JSON/CSV” (genbrug fra modul 07)

---

## 🧠 Fælles tips (gælder alle spor)

* **Tilstand (state):** Hold én sand model (liste/dict eller DB); UI er et spejl → altid opdatér *begge* ved ændringer.
* **Feedback:** statuslinje i bunden: “Gemt ✔”, “Fejl: alder ikke tal”.
* **Genveje:** bind Enter til primær handling, Esc til “Luk/Annuller”.
* **Layout:** sørg for at felter/listbox/treeview **strækker sig** ved resize (`column/rowconfigure` + `sticky`).
* **Robusthed:** `try/except` omkring fil-/DB-IO; vis venlig fejl.

---

## ✅ Overordnet Definition of Done (Modul 08)

* Ét af sporene A/B/C er **færdigt** efter kravene.
* Ingen tracebacks ved almindelig brug; fejl håndteres pænt.
* UI’et skalerer fornuftigt; primære flows kan betjenes med tastatur.

---

## ⏭️ Hvad så nu?

Vil du have **pakning til .exe** (fx med `pyinstaller`) eller en **lille style-overhaling** (ttk.Style temaer, primær knap osv.) som bonus-modul **09**? Siger du bare til, så laver jeg næste skridt.
