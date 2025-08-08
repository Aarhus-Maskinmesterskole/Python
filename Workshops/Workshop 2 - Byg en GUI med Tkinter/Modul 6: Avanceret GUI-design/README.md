# **06 – Avanceret GUI-design (ttk, Style, Canvas & Scrollbar)**

*(Tkinter · Windows + VS Code · **små, trinvise snippets**)*

**Mål:** Få din app til at se **ren og professionel** ud med `ttk`-widgets og `Style()`, og lær to vigtige mønstre: **Canvas-tegning** og **scrollbare områder** (scrollbar + indhold der kan rulle).
**Forventet tid:** 60–90 min.
**Forudsætninger:** 01–05 gennemført (opsætning, widgets, input, layout, menuer/dialoger).

---

## 🪜 Step 0 — Grundskabelon (kopiér én gang)

```python
import tkinter as tk
from tkinter import ttk

root = tk.Tk()
root.title("06 – Avanceret GUI-design")
root.geometry("640x420")

# --- begynd her ---

root.mainloop()
```

---

## 🎨 Step 1 — Skift tema & Style-objekt

**Hvad er det?**
`ttk.Style()` styrer tema (fx *clam*, *default*, *vista*) og udseende på widgets.

```python
style = ttk.Style()

# Se tilgængelige temaer:
# print(style.theme_names())

style.theme_use("clam")  # prøv også "vista" eller "default"

# Global font/størrelse (simpelt trick via option):
root.option_add("*Font", "Segoe UI 10")
```

**Mini-øvelse:** Skift mellem temaer og bemærk forskellen på knapper/felter.

---

## 🧯 Step 2 — Justér widgets med Style.configure/map

**Hvad er det?**
`Style.configure` sætter **grundstil** for en widget-type/“style”.
`Style.map` sætter **tilstands-afhængig** stil (hover/pressed/disabled).

```python
# Lav en "Primary.TButton"-stil (egen stilvariant)
style.configure(
    "Primary.TButton",
    padding=(12, 6),     # indre polstring (h, v)
    font=("Segoe UI", 10, "bold")
)
style.map(
    "Primary.TButton",
    foreground=[("disabled", "#888"), ("active", "#000")],
    background=[("active", "#e6f0ff")]
)

# Brug stilen:
btnbar = ttk.Frame(root, padding=12)
btnbar.pack(fill="x")
ttk.Button(btnbar, text="OK", style="Primary.TButton").pack(side="right")
ttk.Button(btnbar, text="Annuller").pack(side="right", padx=(0,8))
```

**Mini-øvelse:** Lav en “Danger.TButton” med rød tekst og tydelig hover.

---

## 📐 Step 3 — Konsistente afstande & grid-rytme

**Idé:** ens `padx/pady` og faste kolonnebredder gør UI roligt.

```python
frm = ttk.Frame(root, padding=12)
frm.pack(fill="both", expand=True)
for col in (0, 1):
    frm.columnconfigure(col, weight=1)

ttk.Label(frm, text="Felt A:").grid(row=0, column=0, sticky="e", padx=8, pady=6)
ttk.Entry(frm).grid(row=0, column=1, sticky="we", padx=8, pady=6)

ttk.Label(frm, text="Felt B:").grid(row=1, column=0, sticky="e", padx=8, pady=6)
ttk.Entry(frm).grid(row=1, column=1, sticky="we", padx=8, pady=6)

ttk.Separator(frm).grid(row=2, column=0, columnspan=2, sticky="we", pady=(8,6))
```

**Mini-øvelse:** Brug `LabelFrame` til at gruppere felter og behold samme afstande.

---

## 🖼️ Step 4 — Canvas: tegn figurer & tekst

**Hvad er det?**
`Canvas` er en **tegneflade** til rektangler, ovaler, linjer, billeder og tekst.

```python
canvas = tk.Canvas(frm, height=160, background="#f7f7f7", highlightthickness=0)
canvas.grid(row=3, column=0, columnspan=2, sticky="nsew", padx=8, pady=8)
frm.rowconfigure(3, weight=1)  # gør canvas-rækken fleksibel

# Tegn lidt
canvas.create_rectangle(20, 20, 220, 120, fill="#cfe8ff", outline="#2b6cb0")
canvas.create_oval(260, 20, 380, 120, fill="#ffe0cc", outline="#cc7a29", width=2)
canvas.create_text(120, 70, text="Canvas 😎", font=("Segoe UI", 12, "bold"))
```

**Mini-øvelse:** Tegn en lille statusindikator: grøn/gul/rød cirkel + label.

---

## 📜 Step 5 — Scrollbar + scrollbart område (mønster)

**Problem:** Mange elementer → du vil kunne **scrolle**.

**Løsning (scrollable frame-mønster):** Canvas + indlejret Frame + Scrollbar.

```python
container = ttk.Frame(root)
container.pack(fill="both", expand=True, padx=12, pady=(0,12))

canvas = tk.Canvas(container, highlightthickness=0)
scroll = ttk.Scrollbar(container, orient="vertical", command=canvas.yview)
canvas.configure(yscrollcommand=scroll.set)

scroll.pack(side="right", fill="y")
canvas.pack(side="left", fill="both", expand=True)

# Indre frame (ruller sammen med canvas)
inner = ttk.Frame(canvas, padding=8)
inner_id = canvas.create_window((0, 0), window=inner, anchor="nw")

# Fyld indhold (demo: mange rækker)
for i in range(1, 51):
    ttk.Label(inner, text=f"Række {i}").grid(row=i, column=0, sticky="w", pady=2)
    ttk.Entry(inner, width=30).grid(row=i, column=1, sticky="we", padx=8)
    inner.columnconfigure(1, weight=1)

# Hold scrollregion opdateret
def _on_configure(_=None):
    canvas.configure(scrollregion=canvas.bbox("all"))
    # gør indholdets bredde = canvas-bredde
    canvas.itemconfigure(inner_id, width=canvas.winfo_width())

inner.bind("<Configure>", _on_configure)
canvas.bind("<Configure>", _on_configure)

# Mousewheel (Windows)
def _on_mousewheel(e):
    canvas.yview_scroll(int(-1*(e.delta/120)), "units")
canvas.bind_all("<MouseWheel>", _on_mousewheel)
```

**Mini-øvelse:** Tilføj en fast **header-bar** ovenover (uden for `container`), så kun indholdet ruller.

---

## 📋 Step 6 — “List viewer” (scrollbar + rækker + valg)

En simpel, rullelig liste med klik-valg (uden at bruge Treeview).

```python
list_wrap = ttk.Frame(root, padding=(12,0,12,12))
list_wrap.pack(fill="both", expand=True)

list_canvas = tk.Canvas(list_wrap, highlightthickness=0)
list_scroll = ttk.Scrollbar(list_wrap, orient="vertical", command=list_canvas.yview)
list_canvas.configure(yscrollcommand=list_scroll.set)
list_canvas.pack(side="left", fill="both", expand=True)
list_scroll.pack(side="right", fill="y")

rows = ttk.Frame(list_canvas)
rows_id = list_canvas.create_window((0,0), window=rows, anchor="nw")

selected = tk.IntVar(value=-1)

def select_row(idx):
    selected.set(idx)
    for i, child in enumerate(rows.winfo_children()):
        # hver "række" er en Frame → farv baggrund via style map (simpelt hack: label med style)
        bg = "#e6f0ff" if i == idx else ""
        for w in child.winfo_children():
            if isinstance(w, ttk.Label):
                w.configure(background=bg)

# Lav 200 rækker
for i in range(200):
    row = ttk.Frame(rows, padding=(6,3))
    row.grid(row=i, column=0, sticky="we")
    rows.columnconfigure(0, weight=1)
    lbl = ttk.Label(row, text=f"Element #{i:03d}", anchor="w")
    lbl.pack(side="left", fill="x", expand=True)
    btn = ttk.Button(row, text="Vælg", command=lambda i=i: select_row(i))
    btn.pack(side="right")

def _cfg(_=None):
    list_canvas.configure(scrollregion=list_canvas.bbox("all"))
    list_canvas.itemconfigure(rows_id, width=list_canvas.winfo_width())
rows.bind("<Configure>", _cfg)
list_canvas.bind("<Configure>", _cfg)
```

**Mini-øvelse:** Vis detaljer for “valgt række” i et højre panel (ny Frame ved siden af).

> **Note:** Til “tabeller” med kolonner er `ttk.Treeview` et godt valg (kommer fint i senere modul, eller sig til så tilføjer jeg et Step 7b med Treeview-intro).

---

## 🧠 Ordliste

* **ttk.Style()**: styrer tema og udseende på ttk-widgets.
* **configure / map**: grundstil vs. tilstands-afhængig stil (hover/pressed).
* **Canvas**: tegneflade (figurer, tekst, billeder).
* **Scrollable frame**: Canvas + indlejret Frame + Scrollbar + `scrollregion`.
* **MouseWheel**: bind til `<MouseWheel>` på Windows for rulning.

---

## 🚧 Kendte faldgruber

* **Glemsom scrollregion**: husk at opdatere `scrollregion` når indholdet ændrer sig.
* **Bredde mismatch**: sæt `itemconfigure(inner_id, width=canvas.winfo_width())` på `<Configure>`.
* **Stil vs. baggrund**: ttk-widgets arver temaets farver; at farve baggrund kræver enten style-varianten eller en Label som “bagplade”.
* **DPI/skalering**: hvis ting ser små ud, justér font via `root.option_add("*Font", "...")`.

---

## ✅ Definition of Done (Modul 06)

* Du har valgt et **tema** og lavet mindst én **egen style** (fx `Primary.TButton`).
* Du har et **Canvas** med mindst to figurer og en tekst.
* Du har et **scrollbart område** (Canvas + Frame + Scrollbar) der fungerer og skalerer.
* Du har en simpel **list viewer** hvor du kan markere/udpege en række.

---

## ⏱️ Estimeret tid

**60–90 min.** alt efter hvor meget styling og scroll-logik du vil bygge.

---

## ➡️ Næste: **07 – Dataintegration (CSV/JSON/SQLite)**

Vi kobler din GUI til **data**: importer/eksporter **CSV/JSON**, og gem/hent fra **SQLite** – stadig kun med standardbiblioteket. Ønsker du også en **Treeview-variant** her i 06, siger du til, så smider jeg et lille ekstra step på.
