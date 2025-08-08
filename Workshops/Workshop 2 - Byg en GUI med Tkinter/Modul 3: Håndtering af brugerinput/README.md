# **03 – Input & validering**

*(Tkinter · Windows + VS Code · **små, trinvise snippets**)*

**Mål:** Du lærer at hente **brugerinput** (Entry, Text, Checkbutton, Radiobutton, Dropdown), vise **feedback** og lave **validering** (fx “Alder skal være et tal”).
**Forventet tid:** 60–90 min.
**Forudsætninger:** 01 er gennemført (opsætning), 02 er gennemført (widgets & layout).

---

## 🪜 Step 0 — Grundskabelon (kopiér én gang)

Lav en ny fil (fx `03a_form_skelet.py`) og start med:

```python
import tkinter as tk
from tkinter import ttk, messagebox

root = tk.Tk()
root.title("03 – Input & validering")
root.geometry("420x360")

# En form-container med grid
form = ttk.Frame(root, padding=12)
form.pack(fill="both", expand=True)

# Gør kolonne 1 fleksibel (felter kan strække sig)
form.columnconfigure(1, weight=1)

# --- tilføj flere widgets under denne linje ---

root.mainloop()
```

---

## 🧾 Step 1 — Entry-felter (fornavn, efternavn, alder)

**Hvad er Entry?**
Et ét-linjes tekstfelt. Du læser/skriver via en **StringVar** eller `.get()`.

```python
fn_var = tk.StringVar()
ln_var = tk.StringVar()
age_var = tk.StringVar()

ttk.Label(form, text="Fornavn:").grid(row=0, column=0, sticky="e", padx=6, pady=4)
ttk.Entry(form, textvariable=fn_var).grid(row=0, column=1, sticky="we", padx=6, pady=4)

ttk.Label(form, text="Efternavn:").grid(row=1, column=0, sticky="e", padx=6, pady=4)
ttk.Entry(form, textvariable=ln_var).grid(row=1, column=1, sticky="we", padx=6, pady=4)

ttk.Label(form, text="Alder:").grid(row=2, column=0, sticky="e", padx=6, pady=4)
ttk.Entry(form, textvariable=age_var).grid(row=2, column=1, sticky="we", padx=6, pady=4)
```

**Mini-øvelse:** Skriv i felterne og tjek at cursoren hopper rigtigt med **Tab**.

---

## 🎚️ Step 2 — Valgfelter (Checkbutton, Radiobutton, Dropdown)

**Hvad er de?**

* **Checkbutton**: sand/falsk.
* **Radiobutton**: vælg **én** af flere.
* **Dropdown**: rullemenu (brug `ttk.Combobox`).

```python
newsletter_var = tk.BooleanVar(value=True)
ttk.Checkbutton(form, text="Tilmeld nyhedsbrev", variable=newsletter_var)\
    .grid(row=3, column=1, sticky="w", padx=6, pady=(0,6))

gender_var = tk.StringVar(value="andet")
ttk.Label(form, text="Køn:").grid(row=4, column=0, sticky="e", padx=6, pady=4)
gender_frame = ttk.Frame(form)
gender_frame.grid(row=4, column=1, sticky="w")
for txt, val in [("Andet", "andet"), ("Kvinde", "k"), ("Mand", "m")]:
    ttk.Radiobutton(gender_frame, text=txt, value=val, variable=gender_var)\
        .pack(side="left", padx=(0,10))

from tkinter.ttk import Combobox
ttk.Label(form, text="Kategori:").grid(row=5, column=0, sticky="e", padx=6, pady=4)
category = Combobox(form, values=["Student", "Lærer", "Gæst"], state="readonly")
category.set("Student")
category.grid(row=5, column=1, sticky="we", padx=6, pady=4)
```

**Mini-øvelse:** Skift standardvalgene (fx newsletter = False, kategori = “Gæst”).

---

## 📝 Step 3 — Text-felt (flere linjer)

**Hvad er Text?**
Et **multilinje**-felt til længere input (fx besked/kommentar). Man læser med `.get("1.0", "end-1c")`.

```python
ttk.Label(form, text="Besked:").grid(row=6, column=0, sticky="ne", padx=6, pady=4)
msg = tk.Text(form, height=5, wrap="word")
msg.grid(row=6, column=1, sticky="nsew", padx=6, pady=4)
form.rowconfigure(6, weight=1)  # gør rækken med Text fleksibel
```

**Mini-øvelse:** Skriv flere linjer og træk vinduet større. Teksten skal følge med.

---

## 📣 Step 4 — Statuslinje + “Gem”/“Nulstil”

Statuslinjen giver **feedback** (succes/fejl). Knapper binder UI til funktioner.

```python
status_var = tk.StringVar(value="Udfyld felterne og tryk Gem")
status = ttk.Label(root, textvariable=status_var, anchor="w", padding=(12,6))
status.pack(fill="x")

btns = ttk.Frame(root, padding=12)
btns.pack(fill="x")
save_btn = ttk.Button(btns, text="Gem")
reset_btn = ttk.Button(btns, text="Nulstil")
save_btn.pack(side="right")
reset_btn.pack(side="right", padx=(0,8))
```

**Mini-øvelse:** Skift standardteksten i statuslinjen.

---

## 🚦 Step 5 — Basal validering ved Gem

Nu tjekker vi **tomme felter** og at **alder er et heltal ≥ 0**. Vi viser **nære fejl** (rød tekst under feltet) og fokus på første fejl.

```python
# Små fejl-labels (tomt indtil der er fejl)
fn_err = ttk.Label(form, text="", foreground="red")
ln_err = ttk.Label(form, text="", foreground="red")
age_err = ttk.Label(form, text="", foreground="red")
fn_err.grid(row=0, column=1, sticky="w", padx=6, pady=(0,4))
ln_err.grid(row=1, column=1, sticky="w", padx=6, pady=(0,4))
age_err.grid(row=2, column=1, sticky="w", padx=6, pady=(0,4))

def clear_errors():
    for lbl in (fn_err, ln_err, age_err):
        lbl.config(text="")

def validate_form() -> bool:
    clear_errors()
    ok = True
    first_bad = None

    if not fn_var.get().strip():
        fn_err.config(text="Fornavn må ikke være tomt")
        ok, first_bad = False, first_bad or 0
    if not ln_var.get().strip():
        ln_err.config(text="Efternavn må ikke være tomt")
        ok, first_bad = False, first_bad or 1
    try:
        val = int(age_var.get())
        if val < 0:
            raise ValueError
    except Exception:
        age_err.config(text="Alder skal være et heltal ≥ 0")
        ok, first_bad = False, first_bad or 2

    # Sæt fokus på første fejlende felt
    if first_bad == 0: form.grid_slaves(row=0, column=1)[0].focus_set()
    if first_bad == 1: form.grid_slaves(row=1, column=1)[0].focus_set()
    if first_bad == 2: form.grid_slaves(row=2, column=1)[0].focus_set()

    return ok

def on_save():
    if not validate_form():
        status_var.set("Ret fejl (rød tekst) og prøv igen")
        return
    # Læs værdier
    data = {
        "fornavn": fn_var.get().strip(),
        "efternavn": ln_var.get().strip(),
        "alder": int(age_var.get()),
        "køn": gender_var.get(),
        "nyhedsbrev": bool(newsletter_var.get()),
        "kategori": category.get(),
        "besked": msg.get("1.0", "end-1c").strip(),
    }
    status_var.set("Gemt ✔")
    messagebox.showinfo("Gemt", f"Tak, {data['fornavn']}! Dine data er gemt.")

def on_reset():
    for v in (fn_var, ln_var, age_var):
        v.set("")
    newsletter_var.set(False)
    gender_var.set("andet")
    category.set("Student")
    msg.delete("1.0", "end")
    clear_errors()
    status_var.set("Formular nulstillet")

save_btn.config(command=on_save)
reset_btn.config(command=on_reset)

# Enter = Gem
root.bind("<Return>", lambda e: on_save())
```

**Mini-øvelse:** Tilføj et ekstra felt (fx “Email”) og valider, at det indeholder `@`.

---

## ⚡ Step 6 — Live-validering (mens der skrives)

Brug **`trace_add`** på dine `StringVar`’s til at validere i takt med input.

```python
def live_check_name(*_):
    fn_err.config(text="" if fn_var.get().strip() else "Fornavn må ikke være tomt")
def live_check_age(*_):
    t = age_var.get().strip()
    ok = t.isdigit() and int(t) >= 0
    age_err.config(text="" if ok else "Alder skal være tal ≥ 0")

fn_var.trace_add("write", live_check_name)
age_var.trace_add("write", live_check_age)
```

**Mini-øvelse:** Deaktiver **Gem**-knappen, når der er fejl (fx hvis en fejl-label ikke er tom).

---

## 🧠 Ordliste (hurtig recap)

* **Entry**: ét-linjes inputfelt (`StringVar` / `.get()`).
* **Text**: multilinje input (`get("1.0", "end-1c")`).
* **Checkbutton / Radiobutton**: check/valg (brug `BooleanVar`/`StringVar`).
* **Combobox**: dropdown (værdien læses med `.get()`).
* **messagebox**: små pop-up dialoger (info, advarsel).
* **Validering**: tjek af input; vis **nær-fejl** under feltet og fokus på første fejl.

---

## 🚧 Kendte faldgruber

* Bland ikke **pack** og **grid** i **samme** parent.
* Giv **feedback** tæt på feltet (ikke kun i en popup).
* Husk at **Text**-feltet kræver `"1.0"` → `"end-1c"` for at læse tekst.
* Brug `.strip()` når du tjekker tomme felter.
* Pas på at knappen ikke kalder logik med **ugyldigt** input.

---

## ✅ Definition of Done (Modul 03)

* “Gem” tjekker tomme felter og at *Alder* er heltal ≥ 0.
* Fejl vises i **rød tekst** under de relevante felter, og fokus hopper til første fejl.
* Statuslinjen viser *succes/fejl*.
* (Bonus) Live-validering for mindst ét felt.

---

## ⏱️ Estimeret tid

**60–90 min.** afhængigt af hvor meget live-validering du bygger.

---

## ➡️ Næste: **04 – Avanceret layout & frames**

Vi grupperer UI’et pænt med **Frames**, tilføjer **faner (Notebook)** og gør layoutet robust ved resize — plus lidt dynamik, hvor UI skifter baseret på brugerens valg.
