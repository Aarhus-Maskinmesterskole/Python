# **05 – Menuer, dialoger & flere vinduer**

*(Tkinter · Windows + VS Code · **små, trinvise snippets**)*

**Mål:** Tilføj en **menulinje** (Fil/Hjælp), brug **dialoger** (beskeder + åbn/gem) og lav **ekstra vinduer** med `Toplevel`.
**Forventet tid:** 60–90 min.
**Forudsætninger:** 01 (opsætning), 02 (widgets & layout), 03–04 (form, frames & layout).

---

## 🪜 Step 0 — Grundskabelon (kopiér én gang)

```python
import tkinter as tk
from tkinter import ttk, messagebox, filedialog

root = tk.Tk()
root.title("05 – Menuer & dialoger")
root.geometry("640x400")

# En central Text til demo af Åbn/Gem (kan genbruges fra modul 04)
text = tk.Text(root, wrap="word")
text.pack(fill="both", expand=True)

current_file = None  # husker aktiv filsti

# --- tilføj menu og funktioner her ---

root.mainloop()
```

---

## 🧾 Step 1 — Menulinje (Fil/Hjælp)

**Hvad er det?**
`tk.Menu` er containeren for menulinjen. Hver **submenu** er et `Menu`-objekt, du *cascader* på.

```python
menubar = tk.Menu(root)
root.config(menu=menubar)  # vis menubaren i vinduet

file_menu = tk.Menu(menubar, tearoff=False)
help_menu = tk.Menu(menubar, tearoff=False)

menubar.add_cascade(label="Fil", menu=file_menu)
menubar.add_cascade(label="Hjælp", menu=help_menu)

# Placeholder-kommandoer (vi laver funktionerne i næste step)
file_menu.add_command(label="Ny", accelerator="Ctrl+N")
file_menu.add_command(label="Åbn…", accelerator="Ctrl+O")
file_menu.add_command(label="Gem", accelerator="Ctrl+S")
file_menu.add_command(label="Gem som…", accelerator="Ctrl+Shift+S")
file_menu.add_separator()
file_menu.add_command(label="Afslut", accelerator="Ctrl+Q")

help_menu.add_command(label="Om…")
```

> **Tip:** `accelerator` viser genvejen i menuen, men **binder ikke** tasten. Det gør vi i Step 6.

---

## 💬 Step 2 — Besked-dialoger (messagebox)

**Hvad er det?**
Små popups til info/fejl/spørgsmål.

```python
def show_about():
    messagebox.showinfo("Om", "Tkinter demo – Menuer & dialoger")

def confirm_exit():
    if messagebox.askokcancel("Afslut", "Vil du afslutte?"):
        root.destroy()
```

Knyt dem på menupunkter:

```python
help_menu.entryconfig("Om…", command=show_about)
file_menu.entryconfig("Afslut", command=confirm_exit)
```

**Andre varianter:**
`showwarning`, `showerror`, `askyesno`, `askyesnocancel` (returnerer True/False/None).

---

## 📂 Step 3 — Åbn & Gem (filedialog)

**Hvad er det?**
Windows’ fil-dialoger til hent/skriv.

```python
def new_file(event=None):
    global current_file
    current_file = None
    text.delete("1.0", "end")
    root.title("05 – Menuer & dialoger")

def open_file(event=None):
    global current_file
    path = filedialog.askopenfilename(
        title="Åbn fil",
        filetypes=[("Tekstfiler", "*.txt"), ("Alle filer", "*.*")]
    )
    if not path:
        return
    with open(path, "r", encoding="utf-8") as f:
        content = f.read()
    text.delete("1.0", "end")
    text.insert("1.0", content)
    current_file = path
    root.title(f"05 – {path}")

def save_file(event=None):
    global current_file
    if not current_file:
        return save_file_as()
    with open(current_file, "w", encoding="utf-8") as f:
        f.write(text.get("1.0", "end-1c"))

def save_file_as(event=None):
    global current_file
    path = filedialog.asksaveasfilename(
        title="Gem som",
        defaultextension=".txt",
        filetypes=[("Tekstfiler", "*.txt"), ("Alle filer", "*.*")]
    )
    if not path:
        return
    current_file = path
    save_file()
    root.title(f"05 – {path}")
```

Tilslut menupunkter:

```python
file_menu.entryconfig("Ny", command=new_file)
file_menu.entryconfig("Åbn…", command=open_file)
file_menu.entryconfig("Gem", command=save_file)
file_menu.entryconfig("Gem som…", command=save_file_as)
```

---

## 🪟 Step 4 — “Om…” som **Toplevel** (ekstra vindue)

**Hvad er det?**
`tk.Toplevel` skaber et **nyt vindue**. Brug det til “Om”-dialoger, indstillinger m.m.

```python
about_win = None  # singleton-reference

def show_about_window():
    global about_win
    if about_win and about_win.winfo_exists():
        about_win.lift()
        about_win.focus_force()
        return

    about_win = tk.Toplevel(root)
    about_win.title("Om")
    about_win.resizable(False, False)
    about_win.transient(root)  # hold over hovedvindue
    about_win.grab_set()       # modal (blokér bagved)
    ttk.Label(about_win, text="Tkinter demo\nMenuer, dialoger & vinduer", padding=12)\
        .pack()
    ttk.Button(about_win, text="Luk", command=about_win.destroy)\
        .pack(pady=(0,12))

    def on_close():
        about_win.destroy()
    about_win.protocol("WM_DELETE_WINDOW", on_close)

# Skift help-menu til at bruge vindue i stedet for messagebox:
help_menu.entryconfig("Om…", command=show_about_window)
```

---

## 🧳 Step 5 — Flere vinduer (arbejdsvindue)

**Eksempel:** Et ekstra “Søg & erstat”-vindue, der arbejder mod din `Text`.

```python
search_win = None

def open_search():
    global search_win
    if search_win and search_win.winfo_exists():
        search_win.lift(); search_win.focus_force(); return

    search_win = tk.Toplevel(root)
    search_win.title("Søg & erstat")
    search_win.resizable(False, False)
    frm = ttk.Frame(search_win, padding=10); frm.pack(fill="both", expand=True)

    find_var = tk.StringVar(); repl_var = tk.StringVar()
    ttk.Label(frm, text="Find:").grid(row=0, column=0, sticky="e", padx=6, pady=4)
    ttk.Entry(frm, textvariable=find_var, width=24).grid(row=0, column=1, sticky="w")
    ttk.Label(frm, text="Erstat med:").grid(row=1, column=0, sticky="e", padx=6, pady=4)
    ttk.Entry(frm, textvariable=repl_var, width=24).grid(row=1, column=1, sticky="w")

    def do_replace():
        target = find_var.get()
        repl = repl_var.get()
        if not target:
            messagebox.showwarning("Søg", "Angiv en tekst at finde.")
            return
        content = text.get("1.0", "end-1c")
        text.delete("1.0", "end")
        text.insert("1.0", content.replace(target, repl))

    ttk.Button(frm, text="Erstat alle", command=do_replace).grid(
        row=2, column=0, columnspan=2, pady=(8,0))

file_menu.add_separator()
file_menu.add_command(label="Søg & erstat…", command=open_search, accelerator="Ctrl+F")
```

---

## ⌨️ Step 6 — Tastaturgenveje (Ctrl+N/O/S/F/Q)

**Husk:** `accelerator` i menuen **viser** genvejen; her **binder** vi den.

```python
root.bind("<Control-n>", new_file)
root.bind("<Control-o>", open_file)
root.bind("<Control-s>", save_file)
root.bind("<Control-S>", save_file_as)  # Shift+Ctrl+S
root.bind("<Control-f>", lambda e: open_search())
root.bind("<Control-q>", lambda e: confirm_exit())
```

> På macOS ville det være `Command`, men vi holder os til Windows i dette kursus.

---

## 🧼 Step 7 — “Ugemte ændringer?” (bonus)

**Idé:** Track om `Text` er “beskidt”, og spørg før luk.

```python
dirty = tk.BooleanVar(value=False)

def mark_dirty(_=None):
    dirty.set(True)
text.bind("<<Modified>>", lambda e: (text.edit_modified(0), mark_dirty()))

def confirm_maybe_save():
    if not dirty.get():
        return True
    res = messagebox.askyesnocancel("Gem", "Vil du gemme ændringer?")
    if res is None:   # Cancel
        return False
    if res is True:   # Yes
        save_file()
    return True

def on_close():
    if confirm_maybe_save():
        root.destroy()

root.protocol("WM_DELETE_WINDOW", on_close)
```

---

## 🧠 Ordliste

* **Menu / add\_cascade:** menulinje og tilføjelse af undermenuer.
* **messagebox:** små besked-/spørge-dialoger.
* **filedialog:** åbne/gemme filer (returnerer sti eller tom streng).
* **Toplevel:** ekstra vindue (kan gøres *modal* med `grab_set()` og *over* med `transient()`).
* **accelerator vs. bind:** tekst i menuen vs. faktisk tastaturbinding.
* **WM\_DELETE\_WINDOW:** vinduets lukkeknap—kan fanges til “Gem før luk”.

---

## 🚧 Kendte faldgruber

* **Glemte binds:** accelerator vises, men genvejen virker ikke uden `root.bind(...)`.
* **Manglende encoding:** brug `encoding="utf-8"` ved fil-IO.
* **Mange Toplevels:** brug “singleton”-mønster (tjek `winfo_exists()` og `lift()`).
* **Blokér ikke GUI:** undgå lange operationer i event-handler (ingen `sleep()`).

---

## ✅ Definition of Done (Modul 05)

* Menulinje med **Fil/Hjælp** virker.
* **Ny/Åbn/Gem/Gem som** fungerer, og vinduestitlen opdateres med filnavn.
* **“Om…”** vises som separat vindue (`Toplevel`) eller messagebox.
* Mindst ét **ekstra vindue** (fx “Søg & erstat”) kan åbnes og bruges.
* Genveje **Ctrl+N/O/S/Shift+Ctrl+S/F/Q** virker.

---

## ⏱️ Estimeret tid

**60–90 min.** afhængigt af, hvor meget “bonus” (dirty-check, søg/erstat) du tilføjer.

---

## ➡️ Næste: **06 – Avanceret GUI-design**

Vi finpudser temaer og stil med **ttk.Style()**, arbejder med **Canvas/Scrollbar** og bygger lister, der kan rulle pænt — så din app føles poleret.

