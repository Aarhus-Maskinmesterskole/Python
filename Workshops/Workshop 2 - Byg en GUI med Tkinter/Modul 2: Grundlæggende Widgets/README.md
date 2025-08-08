# **02-widgets-og-layout** *(step-by-step, Windows + VS Code)*

**Mål:** Lær de mest brugte widgets i Tkinter (Label, Button, Entry, Text, Check/Radios/Dropdown), og hvordan du placerer dem med `pack` og `grid`.
**Forventet tid:** 60–90 min.
**Forudsætninger:** 01 er gennemført, og du har et tomt projekt i VS Code med `.venv`.

---

## Step 0 — Grundskabelon (startfil)

Kopier dette **én gang** i toppen af din fil. Alle næste snippets kan sættes ind **efter** kommentaren, hvor det giver mening.

```python
import tkinter as tk
from tkinter import ttk

root = tk.Tk()
root.title("Tkinter – 02")
root.geometry("360x220")  # bredde x højde

# --- tilføj dine widgets under denne linje ---

root.mainloop()
```

---

## Step 1 — Label + Button (første interaktion)

**Hvad er det?**

* **Label** viser tekst.
* **Button** udfører en funktion via `command=`.
* **StringVar** er en lille “databeholder” som binder data til widgets.

**Gør sådan:**

```python
status_var = tk.StringVar(value="Tryk på knappen...")

title_lbl = ttk.Label(root, text="Velkommen!", font=("Segoe UI", 14))
status_lbl = ttk.Label(root, textvariable=status_var)
btn = ttk.Button(root, text="Sig hej", command=lambda: status_var.set("Hej 👋"))

title_lbl.pack(pady=8)
status_lbl.pack(pady=8)
btn.pack()
```

**Mini-øvelse:** Skift knapteksten og den tekst, der vises i `status_var`.

---

## Step 2 — Entry (brugerinput) + Enter-genvej

**Hvad er det?**

* **Entry** er ét linjes tekstfelt for input.
* Du læser/skriver via `StringVar` eller `entry.get()`.
* **Events**: du kan binde Enter-tasten til en funktion.

**Gør sådan (læg til i samme fil eller i ny fil):**

```python
name_var = tk.StringVar()

row = ttk.Frame(root)
row.pack(pady=6)
ttk.Label(row, text="Dit navn:").pack(side="left", padx=(0,6))
ttk.Entry(row, textvariable=name_var, width=20).pack(side="left")

def say_hi():
    name = name_var.get().strip() or "verdensborger"
    status_var.set(f"Hej, {name}!")

ttk.Button(root, text="Sig hej", command=say_hi).pack()

root.bind("<Return>", lambda e: say_hi())
```

**Mini-øvelse:** Sørg for at Enter i feltet også virker (hint: fokus ligger allerede i Entry).

---

## Step 3 — `pack` vs. `grid` (layout)

**Hvad er det?**

* **pack**: enkel stabling (top/bund/venstre/højre).
* **grid**: rækker/kolonner (mest fleksibel i formularer).
* **VIGTIGT:** bland ikke `pack` og `grid` i **samme parent**.

**grid-eksempel (ny fil er okay):**

```python
# grid i root
root.columnconfigure((0,1), weight=1)  # gør kolonner fleksible

ttk.Label(root, text="Fornavn:").grid(row=0, column=0, sticky="e", padx=8, pady=6)
fn_var = tk.StringVar()
ttk.Entry(root, textvariable=fn_var).grid(row=0, column=1, sticky="we", padx=8)

ttk.Label(root, text="Efternavn:").grid(row=1, column=0, sticky="e", padx=8, pady=6)
ln_var = tk.StringVar()
ttk.Entry(root, textvariable=ln_var).grid(row=1, column=1, sticky="we", padx=8)

msg_var = tk.StringVar(value="Udfyld felter og tryk Gem")
ttk.Label(root, textvariable=msg_var).grid(row=2, column=0, columnspan=2, pady=(8,0))

def save():
    msg_var.set(f"Gemte: {fn_var.get()} {ln_var.get()}")

ttk.Button(root, text="Gem", command=save).grid(row=3, column=0, columnspan=2, pady=8)
```

**Mini-øvelse:** Få felterne til at **strække sig** vandret (du har gjort det med `sticky="we"` og `columnconfigure`).

---

## Step 4 — Valgfelter (Checkbutton, Radiobutton, Dropdown)

**Hvad er det?**

* **Checkbutton**: sand/falsk (flere kan være slået til samtidig).
* **Radiobutton**: vælg **én** mulighed i en gruppe.
* **OptionMenu/ttk.Combobox**: dropdown-valg.

**Snippets (vælg én eller flere):**

```python
# Checkbutton
newsletter_var = tk.BooleanVar(value=True)
ttk.Checkbutton(root, text="Tilmeld nyhedsbrev", variable=newsletter_var).pack(anchor="w", padx=8)

# Radiobuttons (gruppe via samme variable)
gender_var = tk.StringVar(value="andet")
for txt, val in [("Andet", "andet"), ("Kvinde", "k"), ("Mand", "m")]:
    ttk.Radiobutton(root, text=txt, value=val, variable=gender_var).pack(anchor="w", padx=8)

# Dropdown (OptionMenu)
color_var = tk.StringVar(value="Blå")
ttk.Label(root, text="Farve:").pack(anchor="w", padx=8)
ttk.OptionMenu(root, color_var, color_var.get(), "Blå", "Rød", "Grøn").pack(anchor="w", padx=8)
```

**Mini-øvelse:** Vis i `status_var`, hvad der er valgt, når du trykker en “OK”-knap.

---

## Step 5 — Text + Scrollbar (notatfelt)

**Hvad er det?**

* **Text** er et multilinje-område til længere tekst.
* Scrollbar kobles på via `yscrollcommand`/`command`.

```python
text = tk.Text(root, height=6, wrap="word")
scroll = ttk.Scrollbar(root, orient="vertical", command=text.yview)
text.configure(yscrollcommand=scroll.set)

text.grid(row=0, column=0, sticky="nsew", padx=(8,0), pady=8)
scroll.grid(row=0, column=1, sticky="ns", pady=8)
root.rowconfigure(0, weight=1)   # gør rækken fleksibel
root.columnconfigure(0, weight=1)
```

**Mini-øvelse:** Skriv antal tegn i en status-label, når brugeren skriver (hint: `<KeyRelease>` event).

---

## Step 6 — Frame (gruppering)

**Hvad er det?**

* **Frame** er en container til at samle widgets og styre layout i sektioner.

```python
form = ttk.Frame(root, padding=8)
form.pack(fill="x")

ttk.Label(form, text="Email:").grid(row=0, column=0, sticky="e", padx=6, pady=4)
email_var = tk.StringVar()
ttk.Entry(form, textvariable=email_var).grid(row=0, column=1, sticky="we", padx=6, pady=4)
form.columnconfigure(1, weight=1)
```

**Mini-øvelse:** Lav en **form-frame** og en **status-frame** (bundlinje).

---

## Step 7 — Canvas (tegneflade)

**Hvad er det?**

* **Canvas** er en flade, hvor du kan **tegne** (linjer, rektangler, tekst, billeder).
* Bruges til simple grafikker, baggrunde, diagrammer, custom widgets.

```python
canvas = tk.Canvas(root, width=200, height=120, background="#f5f5f5", highlightthickness=0)
canvas.pack(pady=8)
canvas.create_rectangle(20, 20, 180, 100, fill="#cfe8ff", outline="#2b6cb0")
canvas.create_text(100, 60, text="Canvas", font=("Segoe UI", 12))
```

**Mini-øvelse:** Tegn en cirkel og skriv tekst i midten.

---

## Ordliste (hurtigt opslagsværk)

* **Label:** viser tekst/billede.
* **Button:** udfører en funktion via `command=`.
* **Entry:** ét linjes inputfelt (brug `StringVar`/`.get()`).
* **Text:** multilinje tekstområde (kan have scrollbar).
* **Checkbutton / Radiobutton:** afkrydsning / eksklusivt valg.
* **OptionMenu / Combobox:** dropdown-valg.
* **Frame:** container til at gruppere widgets og styre layout.
* **Canvas:** tegneflade til figurer/tekst/billeder.
* **Menu:** menulinje (Fil/Hjælp) – kommer i et senere modul.
* **Toplevel:** ekstra vindue ud over hovedvinduet.
* **ttk:** “tema-widgets” (pænere, mere konsistente).
* **StringVar/IntVar/BooleanVar:** binder data mellem din kode og widgets.
* **pack / grid / place:** layoutsystemer (brug `grid` til formularer).
* **event / event-loop:** hændelser (klik/tastning) og det loop, der holder GUI’en “i live”.

---

## Kendte faldgruber (og hvordan du undgår dem)

* **Mix af layout:** bland aldrig `pack` og `grid` i **samme parent**.
* **Ikke-skalerende felter:** husk `columnconfigure`/`rowconfigure` og `sticky="we"`/`"nsew"`.
* **Stille UI:** vis **feedback** i en label i bunden (succes/fejl).
* **Langvarigt arbejde:** blokér ikke GUI (ingen `sleep()` i main-tråd).

---

## Definition of Done (for 02)

* Du har **kørt** mindst ét mini-eksempel (Label+Button+Entry).
* Du har **valgt** et layout (pack eller grid) og fået felter til at **skalere**.
* Du kan forklare forskellen på Entry vs. Text, og hvornår du bruger Canvas.
* Du har skitseret en **mini-app** (widgets + layout + events) klar til næste modul.
