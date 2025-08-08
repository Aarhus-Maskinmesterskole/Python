# **04 – Avanceret layout & frames**

*(Tkinter · Windows + VS Code · **små, trinvise snippets**)*

**Mål:** Lær at bygge **pænt, robust layout** med *frames*, **indlejrede grids**, **faneblade (ttk.Notebook)** og **dynamiske sektioner** (vis/skjul).
**Forventet tid:** 60–90 min.
**Forudsætninger:** 01 (opsætning), 02 (widgets & layout), 03 (input & validering).

---

## 🪜 Step 0 — Grundskabelon (kopiér én gang)

```python
import tkinter as tk
from tkinter import ttk

root = tk.Tk()
root.title("04 – Avanceret layout & frames")
root.geometry("520x360")

# --- tilføj dine widgets under denne linje ---

root.mainloop()
```

---

## 🧱 Step 1 — Hvad er en Frame?

**Frame = beholder**. Du lægger widgets i en frame for at:

* gruppere relaterede felter,
* styre layout (fx `grid`) uafhængigt af andre sektioner,
* undgå at blande `pack` og `grid` i samme parent.

**Eksempel: én form-frame + én status-frame**

```python
form = ttk.Frame(root, padding=12)
form.pack(fill="both", expand=True)  # her må vi gerne bruge pack

status = ttk.Frame(root)
status.pack(fill="x")

ttk.Label(status, text="Klar.", anchor="w").pack(side="left", padx=8, pady=6)
```

> **Regel:** Du må gerne bruge **pack i root** og **grid INDE i en frame**.
> Du må **ikke** mixe pack & grid i *samme* parent.

---

## 🧩 Step 2 — Indlejret grid (robust formular)

Nu bruger vi `grid` **kun inde i** `form`. Sæt kolonne 1 til at strække sig.

```python
form.columnconfigure(1, weight=1)

ttk.Label(form, text="Fornavn:").grid(row=0, column=0, sticky="e", padx=8, pady=6)
fn = ttk.Entry(form); fn.grid(row=0, column=1, sticky="we", padx=8, pady=6)

ttk.Label(form, text="Efternavn:").grid(row=1, column=0, sticky="e", padx=8, pady=6)
ln = ttk.Entry(form); ln.grid(row=1, column=1, sticky="we", padx=8, pady=6)

ttk.Label(form, text="Besked:").grid(row=2, column=0, sticky="ne", padx=8, pady=6)
txt = tk.Text(form, height=6, wrap="word")
txt.grid(row=2, column=1, sticky="nsew", padx=8, pady=6)
form.rowconfigure(2, weight=1)  # Text-rækken kan vokse
```

**Mini-øvelse:** Tilføj en knaprække i bunden af `form` (row=3) med `Gem` og `Luk`.
**Tip:** læg dem i en *indre* `ttk.Frame(form)` og brug `pack(side="right")` inde i den – det er en ny parent, så det er ok.

---

## 🗂️ Step 3 — Sektioner i sektioner (frames i frames)

Grupper relaterede felter i en **sub-frame** for bedre læsbarhed.

```python
contact = ttk.LabelFrame(form, text="Kontakt", padding=8)
contact.grid(row=4, column=0, columnspan=2, sticky="we", padx=8, pady=(0,8))
contact.columnconfigure(1, weight=1)

ttk.Label(contact, text="Email:").grid(row=0, column=0, sticky="e", padx=6, pady=4)
email = ttk.Entry(contact)
email.grid(row=0, column=1, sticky="we", padx=6, pady=4)

ttk.Label(contact, text="Telefon:").grid(row=1, column=0, sticky="e", padx=6, pady=4)
phone = ttk.Entry(contact)
phone.grid(row=1, column=1, sticky="we", padx=6, pady=4)
```

**Mini-øvelse:** Læg en tynd separator mellem formular og kontaktsektion: `ttk.Separator(form).grid(..., sticky="we")`.

---

## 🗃️ Step 4 — Faner med `ttk.Notebook`

**Notebook** giver faneblade. Hver fane er typisk **en frame**.

```python
nb = ttk.Notebook(root)
nb.pack(fill="both", expand=True)

tab_form = ttk.Frame(nb, padding=12)
tab_log = ttk.Frame(nb, padding=12)
nb.add(tab_form, text="Formular")
nb.add(tab_log, text="Log")

# Læg nu dit tidligere 'form'-indhold ind i 'tab_form' i stedet:
tab_form.columnconfigure(1, weight=1)
ttk.Label(tab_form, text="Fornavn:").grid(row=0, column=0, sticky="e")
# ... resten af din formular ...

# Log-fanen: en Text + scrollbar
log = tk.Text(tab_log, height=10, wrap="word")
scroll = ttk.Scrollbar(tab_log, orient="vertical", command=log.yview)
log.configure(yscrollcommand=scroll.set)
log.grid(row=0, column=0, sticky="nsew")
scroll.grid(row=0, column=1, sticky="ns")
tab_log.columnconfigure(0, weight=1)
tab_log.rowconfigure(0, weight=1)
```

**Mini-øvelse:** Når du “Gemmer” i formularfanen, tilføj en linje i **Log**-fanen (`log.insert("end", "Gemte ...\n")`).

---

## 🎛️ Step 5 — Dynamiske sektioner (vis/skjul)

Skjul “Avanceret” indstillinger indtil brugeren beder om dem.
Brug `grid_remove()` for at huske placeringen, og `grid()` for at vise igen.

```python
advanced = ttk.LabelFrame(tab_form, text="Avanceret", padding=8)
advanced.grid(row=10, column=0, columnspan=2, sticky="we", padx=8, pady=8)
advanced.columnconfigure(1, weight=1)

ttk.Label(advanced, text="API-nøgle:").grid(row=0, column=0, sticky="e", padx=6, pady=4)
api = ttk.Entry(advanced, show="*"); api.grid(row=0, column=1, sticky="we", padx=6, pady=4)

show_adv = tk.BooleanVar(value=False)
toggle = ttk.Checkbutton(tab_form, text="Vis avanceret", variable=show_adv)
toggle.grid(row=9, column=0, sticky="w", padx=8, pady=(8,0))

def update_advanced(*_):
    if show_adv.get():
        advanced.grid()          # vis igen
    else:
        advanced.grid_remove()   # skjul
show_adv.trace_add("write", update_advanced)
update_advanced()
```

**Mini-øvelse:** Gem tilstanden “Vis avanceret” i en `BooleanVar` og afspejl den i en status-label.

---

## ➿ Step 6 — Fleksibel resize (sticky + weight)

**Husk:** rækker/kolonner der skal vokse ved resize, skal have `rowconfigure/columnconfigure(weight=1)` **og** widgets i dem skal have passende `sticky` (fx `"nsew"`).

Tjekliste for skalerbart layout:

* [ ] de rækker/kolonner der skal vokse har **weight=1**
* [ ] widgets i de celler har **sticky="we"** (vandret) eller **"nsew"** (begge)
* [ ] store tekstområder og lister ligger i en **frame** med `rowconfigure/columnconfigure`

---

## ✨ Step 7 — Små layout-tricks

* **Uens kantafstande:** `padx=(venstre,højre)` / `pady=(top,bund)`
* **Ens linjer:** brug samme `pady` på labels/entries for lige rækker
* **Sektionstitler:** `LabelFrame` gør UI mere læsbart end *kun* labels
* **Statuslinje:** læg en lav `Label` i bunden, `anchor="w"` for venstrejustering

---

## 🧠 Ordliste

* **Frame:** container/gruppe med eget layout
* **LabelFrame:** Frame med overskrift
* **Notebook:** faneblade
* **grid\_remove():** skjul men husk placering (modsat `grid_forget()`, der glemmer)
* **weight:** hvor “meget” række/kolonne må vokse
* **sticky:** hvilken side/corner cellen klistres til (`n/e/s/w`, kombineres)

---

## 🚧 Kendte faldgruber

* **Mix af layout:** bland ikke `pack` og `grid` i **samme** parent.
* **Ikke-skalerende felter:** glemte `row/columnconfigure` og `sticky`.
* **Notebook uden udfyldning:** glemte `fill="both", expand=True`.
* **Skjulte sektioner:** brug `grid_remove()` i stedet for `place_forget()` i grid-baseret layout.

---

## ✅ Definition of Done (Modul 04)

* Du har **form-sektioner** i frames med pænt, konsekvent grid.
* Mindst **én fane** (Notebook) indeholder formular, en anden indeholder log/tekst.
* En **Avanceret**-sektion kan vises/skjules uden at layoutet hopper skævt.
* Dit vindue **skalerer** pænt: tekstområder/kolonner strækker sig som forventet.

---

## ⏱️ Estimeret tid

**60–90 min.** afhængigt af hvor meget du vil finpudse notebook og dynamik.

---

## ➡️ Næste: **05 – Menuer & dialoger**

Vi tilføjer **menulinje** (Fil/Hjælp), **dialoger** (åbn/gem, beskeder) og **flere vinduer** med `Toplevel`—det der får din app til at føles “rigtig”.
