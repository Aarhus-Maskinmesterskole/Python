# **01 – Introduktion & opsætning**

*(Tkinter · Windows + VS Code · **ingen kode** i dette modul)*

## 👋 Velkommen

Modul **01** er din rolige indflyvning til **GUI i Python med Tkinter**. Vi går **trin for trin**, så alle kan være med — og vi rører **ikke** ved kode endnu. Målet er, at du efter modulet trygt kan fortsætte til næste modul, hvor vi bygger dit første vindue.

---

## 🎯 Hvad går modul 01 ud på?

* Forstå **hvad Tkinter er**, og hvornår det er et godt valg.
* Få styr på **arbejdsgang i VS Code på Windows** (uden kommandolinjemagi).
* Lære de **grundbegreber**, vi bruger resten af workshoppen: *widget, layout, event, event-loop*.
* Tjekke at **dit miljø** er klar (detaljer i `installation.md`).

> **Kort sagt:** Du får sprog, overblik og et klart *to-do* til din opsætning — *før* vi begynder at kode.

---

## 🗺️ Overblik over vejen frem

* **01 – Introduktion & opsætning** (denne) → begreber + klar miljøtjek
* **02 – Widgets & layout** → første vindue, labels/knapper/entry, pack vs. grid
* **03 – Input & validering** → brugerinput, feedback & simple regler
* … og videre til menuer, dialoger, data m.m.

---

## 🧠 Nøglebegreber (uden kode)

* **GUI**: *Graphical User Interface* — vinduer, knapper, felter m.m.
* **Tkinter**: Pythons **standard** GUI-bibliotek (følger med Python).
* **Widget**: en byggesten i UI (fx **Label**, **Button**, **Entry**, **Text**, **Frame**).
* **Layout**: hvordan widgets placeres:

  * **pack** ➜ enkel stabling (op/ned/venstre/højre)
  * **grid** ➜ rækker/kolonner (*mest fleksibel til formularer*)
  * **place** ➜ faste koordinater (*brug sjældent*)
* **Event**: en hændelse (klik, tast, ændring).
* **Event-loop**: “hjertet” der holder vinduet i live og reagerer på events.
* *(Teaser til næste modul)* **Entry** = ét-linjes tekstfelt · **Text** = flersidet tekst · **Canvas** = tegneflade · **ttk** = pænere, tema-baserede widgets.

---

## 🧰 Krav og forudsætninger

* **Windows 10/11**
* **Python 3.12+**
* **Visual Studio Code** + udvidelserne **Python** og **Pylance**
* Let kendskab til Python (variabler, funktioner, `if`/`else`)
* **Ingen** Git/GitHub, ingen hardware — *kun Python på din PC*

---

## 🪜 Step-by-step (uden kode)

> **Alle praktiske detaljer (screenshots/kommandoer) ligger i `installation.md`.**
> Her får du kun *hvad* du skal gøre og *i hvilken rækkefølge*.

**Step 1 — Installer værktøjer** ✅

* Python 3.12+ (Microsoft Store eller python.org)
* VS Code + udvidelserne *Python* & *Pylance*

**Step 2 — Opret din kursusmappe** 🗂️

* Fx `C:\Users\<dig>\Documents\tkinter-workshop`
* Åbn mappen i VS Code (**File → Open Folder…**)

**Step 3 — Forbered projektet i VS Code** 🧪

* Opret et **virtuelt miljø** (`.venv`)
* Vælg **.venv** som Python-fortolker i statuslinjen nederst i VS Code

**Step 4 — Tjek at Tkinter er klar** 🩺

* Følg mini-testen i `installation.md` (hurtigt check for Tk/Tcl)

**Step 5 — Klar til næste modul** 🟢

* Du ved, hvad *widget/layout/event/event-loop* betyder
* Dit miljø er klar → videre til **02 – Widgets & layout**

---

## ✅ Tjekliste (før du går videre)

* [ ] Python 3.12+ og VS Code er installeret
* [ ] Udvidelserne **Python** og **Pylance** er aktive i VS Code
* [ ] Projektmappen er oprettet og åbnet i VS Code
* [ ] **.venv** er oprettet **og valgt** som fortolker i VS Code
* [ ] Tkinter-tjekket i `installation.md` kører igennem
* [ ] Jeg kan med egne ord forklare: *widget*, *layout (pack/grid/place)*, *event*, *event-loop*

---

## 🧭 Kvalitetskriterier (DoD for 01)

* Miljøet er verificeret (*alt grønt* i din egen tjekliste).
* Begreberne ovenfor giver mening for dig.
* Du føler dig tryg ved at åbne en **tom Python-fil** i næste modul og begynde at placere widgets.

---

## ✍️ Refleksion (3–5 linjer)

* Hvornår giver en **GUI** mere mening end en kommandolinje?
* Hvorfor ville du vælge **grid** frem for **pack** i en formular?
* Hvilke to begreber vil du holde ekstra øje med i **02**?

---

## 🆘 Hvis noget driller

* Kig i **`installation.md` → Fejlsøgning** (PowerShell-politik, fortolker-valg, Tkinter-check).
* Stadig problemer? Notér præcis fejltekst og hvor i processen den opstår — så er det hurtigt at hjælpe dig videre.

---

## 🚀 Næste skridt: **02 – Widgets & layout**

Her bygger vi dit første vindue **med små, tydelige snippets**: *Label*, *Button*, *Entry*, *Text*, *pack vs. grid*, events og feedback. Klar? 💪
