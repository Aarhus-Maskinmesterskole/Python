# **07 – Dataintegration (CSV / JSON / SQLite)**

*(Tkinter · Windows + VS Code · **små, trinvise snippets**)*

**Mål:** Lær at **læse/skriv data** fra filer (**CSV, JSON**) og at gemme/hente i en **SQLite**-database – og vis det i din GUI.
**Forventet tid:** 90–120 min.
**Forudsætninger:** 01–06 gennemført (opsætning, widgets/layout, input/validering, frames, menuer/dialoger, style/scroll).

> **Filer (forslag):** `07a_csv_json.py`, `07b_treeview.py`, `07c_sqlite_basics.py`, `07d_crud_app.py`

---

## 🪜 Step 0 — Grundskabelon + menu

Vi bruger en **Text/Treeview**-centreret app og en simpel menulinje til import/eksport.

```python
import tkinter as tk
from tkinter import ttk, filedialog, messagebox
import csv, json, sqlite3, os

root = tk.Tk()
root.title("07 – Dataintegration")
root.geometry("760x480")

menubar = tk.Menu(root); root.config(menu=menubar)
file_menu = tk.Menu(menubar, tearoff=False)
data_menu = tk.Menu(menubar, tearoff=False)
menubar.add_cascade(label="Fil", menu=file_menu)
menubar.add_cascade(label="Data", menu=data_menu)

status_var = tk.StringVar(value="Klar.")
status = ttk.Label(root, textvariable=status_var, anchor="w", padding=(12,6))
status.pack(side="bottom", fill="x")

# --- indhold kommer i de næste steps ---

root.mainloop()
```

---

## 📄 Step 1 — CSV: importer & eksporter

**CSV** er “kommasepareret” (kan også være semikolon). Vi viser data i **Treeview** (tabellignende widget).

**Treeview-skelet:**

```python
cols = ("id", "navn", "alder")
tree = ttk.Treeview(root, columns=cols, show="headings", selectmode="browse")
for c in cols:
    tree.heading(c, text=c.capitalize())
    tree.column(c, anchor="center", width=120)
tree.pack(fill="both", expand=True, padx=12, pady=12)

data = []  # liste af dicts: {"id": "...", "navn": "...", "alder": "..."}
```

**Importer CSV → udfyld `data` + Treeview:**

```python
def load_csv():
    path = filedialog.askopenfilename(
        title="Vælg CSV",
        filetypes=[("CSV-filer", "*.csv"), ("Alle filer", "*.*")]
    )
    if not path: return
    try:
        with open(path, "r", encoding="utf-8", newline="") as f:
            reader = csv.DictReader(f)
            rows = [ {k.strip(): v.strip() for k,v in row.items()} for row in reader ]
    except Exception as e:
        messagebox.showerror("CSV", f"Kunne ikke læse filen:\n{e}")
        return

    # Normalisér kolonner (sørg for id/navn/alder findes)
    global data
    data = []
    for r in rows:
        item = {
            "id": r.get("id", ""),
            "navn": r.get("navn", ""),
            "alder": r.get("alder", ""),
        }
        data.append(item)

    tree.delete(*tree.get_children())
    for item in data:
        tree.insert("", "end", values=(item["id"], item["navn"], item["alder"]))

    status_var.set(f"Indlæst {len(data)} rækker fra CSV")

file_menu.add_command(label="Importer CSV…", command=load_csv)
```

**Eksporter nuværende `data` til CSV:**

```python
def export_csv():
    if not data:
        messagebox.showwarning("CSV", "Der er ingen data at eksportere.")
        return
    path = filedialog.asksaveasfilename(
        title="Gem som CSV",
        defaultextension=".csv",
        filetypes=[("CSV-filer", "*.csv")]
    )
    if not path: return
    try:
        with open(path, "w", encoding="utf-8", newline="") as f:
            writer = csv.DictWriter(f, fieldnames=list(data[0].keys()))
            writer.writeheader()
            writer.writerows(data)
    except Exception as e:
        messagebox.showerror("CSV", f"Kunne ikke gemme filen:\n{e}")
        return
    status_var.set(f"Gemt {len(data)} rækker til CSV")

file_menu.add_command(label="Eksporter CSV…", command=export_csv)
```

**Mini-øvelse:** Tilføj en tæller i statusfeltet: “Viser N rækker”.

---

## 🧾 Step 2 — JSON: gem/indlæs

**JSON** passer godt til struktureret data (liste af dicts).

```python
def export_json():
    if not data:
        messagebox.showwarning("JSON", "Ingen data at gemme.")
        return
    path = filedialog.asksaveasfilename(
        title="Gem som JSON",
        defaultextension=".json",
        filetypes=[("JSON-filer", "*.json")]
    )
    if not path: return
    try:
        with open(path, "w", encoding="utf-8") as f:
            json.dump(data, f, ensure_ascii=False, indent=2)
    except Exception as e:
        messagebox.showerror("JSON", f"Kunne ikke gemme:\n{e}")
        return
    status_var.set(f"Gemt {len(data)} rækker til JSON")

def load_json():
    path = filedialog.askopenfilename(
        title="Åbn JSON",
        filetypes=[("JSON-filer", "*.json"), ("Alle filer", "*.*")]
    )
    if not path: return
    try:
        with open(path, "r", encoding="utf-8") as f:
            rows = json.load(f)
        assert isinstance(rows, list)
    except Exception as e:
        messagebox.showerror("JSON", f"Kunne ikke læse:\n{e}")
        return

    # opdater data + tree
    global data
    data = rows
    tree.delete(*tree.get_children())
    for item in data:
        tree.insert("", "end", values=(item.get("id",""), item.get("navn",""), item.get("alder","")))
    status_var.set(f"Indlæst {len(data)} rækker fra JSON")

file_menu.add_command(label="Åbn JSON…", command=load_json)
file_menu.add_command(label="Gem som JSON…", command=export_json)
```

**Mini-øvelse:** Tilføj simpel validering: alder skal være tal, ellers vis advarsel.

---

## 📊 Step 3 — Treeview: tilføj/ret/slet rækker

Vi laver små dialoger (enkle messagebox-agtige inputs kan laves med `Toplevel` – her holder vi det simpelt ved at redigere valgt række i felter under tabellen).

```python
edit = ttk.Frame(root, padding=(12,0,12,12))
edit.pack(fill="x")
id_var = tk.StringVar(); name_var = tk.StringVar(); age_var = tk.StringVar()

ttk.Label(edit, text="ID").grid(row=0, column=0, padx=6)
ttk.Entry(edit, textvariable=id_var, width=10).grid(row=0, column=1)
ttk.Label(edit, text="Navn").grid(row=0, column=2, padx=6)
ttk.Entry(edit, textvariable=name_var, width=20).grid(row=0, column=3)
ttk.Label(edit, text="Alder").grid(row=0, column=4, padx=6)
ttk.Entry(edit, textvariable=age_var, width=6).grid(row=0, column=5)

def refresh_tree():
    tree.delete(*tree.get_children())
    for item in data:
        tree.insert("", "end", values=(item["id"], item["navn"], item["alder"]))

def on_select(_=None):
    sel = tree.selection()
    if not sel: return
    vals = tree.item(sel[0], "values")
    id_var.set(vals[0]); name_var.set(vals[1]); age_var.set(vals[2])

tree.bind("<<TreeviewSelect>>", on_select)

def add_row():
    item = {"id": id_var.get().strip(), "navn": name_var.get().strip(), "alder": age_var.get().strip()}
    if not item["id"] or not item["navn"]: 
        messagebox.showwarning("Tilføj", "ID og Navn må ikke være tomme.")
        return
    data.append(item); refresh_tree(); status_var.set("Tilføjet række")

def update_row():
    sel = tree.selection()
    if not sel:
        messagebox.showinfo("Opdater", "Vælg en række først."); return
    idx = tree.index(sel[0])
    data[idx] = {"id": id_var.get().strip(), "navn": name_var.get().strip(), "alder": age_var.get().strip()}
    refresh_tree(); status_var.set("Opdateret række")

def delete_row():
    sel = tree.selection()
    if not sel:
        messagebox.showinfo("Slet", "Vælg en række først."); return
    idx = tree.index(sel[0])
    del data[idx]; refresh_tree(); status_var.set("Slettet række")

ttk.Button(edit, text="Tilføj", command=add_row).grid(row=0, column=6, padx=8)
ttk.Button(edit, text="Opdater", command=update_row).grid(row=0, column=7, padx=8)
ttk.Button(edit, text="Slet", command=delete_row).grid(row=0, column=8, padx=8)
```

**Mini-øvelse:** Sortér kolonner ved klik på kolonneheader (hint: `tree.heading(col, command=...)`).

---

## 🗄️ Step 4 — SQLite: opret DB & gem/hent

**SQLite** er en filbaseret database (ingen server). Vi bruger standardbibliotekets `sqlite3`.

```python
DB_PATH = "app.db"

def db_init():
    with sqlite3.connect(DB_PATH) as con:
        cur = con.cursor()
        cur.execute("""
            CREATE TABLE IF NOT EXISTS personer (
                id TEXT PRIMARY KEY,
                navn TEXT NOT NULL,
                alder INTEGER
            )
        """)
        con.commit()

def db_save_all():
    if not data:
        messagebox.showwarning("DB", "Ingen data at gemme."); return
    with sqlite3.connect(DB_PATH) as con:
        cur = con.cursor()
        cur.execute("DELETE FROM personer")
        cur.executemany(
            "INSERT INTO personer (id, navn, alder) VALUES (?, ?, ?)",
            [(d["id"], d["navn"], int(d["alder"] or 0)) for d in data]
        )
        con.commit()
    status_var.set(f"Gemt {len(data)} rækker til DB ({os.path.abspath(DB_PATH)})")

def db_load_all():
    with sqlite3.connect(DB_PATH) as con:
        cur = con.cursor()
        rows = cur.execute("SELECT id, navn, alder FROM personer").fetchall()
    global data
    data = [{"id": r[0], "navn": r[1], "alder": str(r[2] if r[2] is not None else "")} for r in rows]
    refresh_tree()
    status_var.set(f"Indlæst {len(data)} rækker fra DB")

db_init()
data_menu.add_command(label="Gem alle til DB", command=db_save_all)
data_menu.add_command(label="Hent alle fra DB", command=db_load_all)
```

**Mini-øvelse:** Tilføj “Find efter ID” (SELECT … WHERE id = ?), og fyld felterne hvis fundet.

---

## 🧠 Ordliste

* **CSV**: tekstformat med separator (komma/semicolon) – godt til udveksling.
* **JSON**: struktureret tekstformat til lister/dicts – godt til tilstand/konfiguration.
* **SQLite**: filbaseret database (`sqlite3`) – godt til små apps.
* **Treeview**: tabel-lignende visning med kolonner/rows.
* **CRUD**: Create, Read, Update, Delete (typiske dataoperationer).

---

## 🚧 Kendte faldgruber

* **Encoding**: brug `encoding="utf-8"` + `newline=""` for CSV på Windows.
* **Typer**: `alder` som tekst/tal – konvertér, når du gemmer i DB (`int(...)`).
* **Primærnøgle**: samme `id` to gange → `UNIQUE`-fejl – håndtér med try/except eller opdater i stedet.
* **State i GUI**: opdater både `data` (model) **og** `Treeview` (view), ellers bliver de ude af sync.
* **Bloker ikke GUI**: store filer/DB-arbejde bør senere flyttes til en tråd — her holder vi det småt.

---

## ✅ Definition of Done (Modul 07)

* Du kan **importere** fra CSV **og** JSON og vise data i **Treeview**.
* Du kan **eksportere** til CSV eller JSON.
* Du kan **tilføje/opdatere/slette** rækker via GUI og se ændringer i tabellen.
* Du kan **gemme** alt til **SQLite** og **indlæse** igen.

---

## ⏱️ Estimeret tid

**90–120 min.** alt efter hvor meget CRUD/validering du bygger.

---

## ➡️ Næste: **08 – Ekspertprojekter (To-Do, Lommeregner, CRUD + SQLite)**

Vælg ét projekt og byg det færdigt. Hvis du vil, kan jeg lave **08** med komplette kravspecs + milepæle – eller vi kan folde **Treeview-sortering/filtrering** ud som et ekstra mini-modul.

