# **02 – Grundlæggende widgets & layout**

*(Tkinter · Windows + VS Code — ingen kode i dette dokument)*

## Overblik

I Modul 02 går vi fra begreber til praksis — stadig **uden at vise kode her**. Du lærer, hvad de mest brugte **widgets** gør (Label, Button, Entry, Text m.fl.), hvordan de placeres med **pack / grid / place**, og hvordan **command** og simple **events** binder UI sammen med logik.

**Målet** er, at du efter modulet kan planlægge og beskrive en lille GUI med korrekt layout og brugerflow — klar til at implementere i næste modul.

---

## Forudsætninger

* Modul **01 – Introduktion & opsætning** er gennemført (Python 3.12+, VS Code, `.venv`, Tkinter verificeret).
* Basal Python: variabler, funktioner, `if/elif/else`.

---

## Læringsmål (efter dette modul kan du…)

* identificere og forklare formålet med **Label**, **Button**, **Entry**, **Text**, **Checkbutton**, **Radiobutton**, **OptionMenu** (ttk-varianter hvor det giver mening),
* vælge et passende **layout-system** (pack, grid eller place) til en given skærm,
* beskrive **command** (på knapper/menupunkter) og simple **events** (fx Enter-tast),
* skitsere et vindue med felter, knapper og feedback-områder, inkl. hvordan det **skalerer** ved resize,
* planlægge **validering** af brugerinput (hvad er lovligt/ulovligt, og hvordan vises fejl tydeligt).

---

## Indhold

1. **Widgets – byggeklodser**

   * **Label** (tekst/feedback), **Button** (handling), **Entry** (enkelt linje), **Text** (flere linjer)
   * **Checkbutton/Radiobutton** (valg), **OptionMenu/Combobox** (dropdown)
   * **StringVar/IntVar/BooleanVar** til at binde data mellem UI og “model”
   * **ttk**-widgets for et mere konsistent look

2. **Layout – placering og skalering**

   * **pack**: simpelt, lodret/vandret stabling
   * **grid**: rækker/kolonner, fleksibel skalering via `row/columnconfigure`
   * **place**: absolut placering (sjældent det rigtige valg)
   * Grundregel: **Bland ikke pack og grid i samme container**

3. **Adfærd – at gøre UI levende**

   * **command**: Button → funktion
   * **events**: fx Enter-tast i et Entry-felt, ændring i en variabel → opdater label
   * **feedback**: tydelig succes/fejl, statusfelt i bunden (label)

4. **UX-rammer for små værktøjer**

   * Én skærm → ét primært formål
   * Tydelige labels og felter, fornuftig **Tab-rækkefølge**
   * Validering tæt på feltet, **ikke** som pop-up, når det kan undgås
   * Skalerbarhed: felter der må vokse, vokser; tekster brydes pænt

---

## Øvelser (uden kode – planlægning & specifikation)

> Brug disse som **design-briefs**. I næste modul implementeres de.

### Øvelse A — “Sig hej”-vindue (starter)

**Formål:** Grundlæggende widgets + feedback.
**Krav (beskriv i tekst/skitse):**

* Et felt til navn (Entry), en knap (“Sig hej”), en label der viser svaret.
* Når knappen trykkes, opdateres label med “Hej, <navn>!”.
* Layout med **grid**: 2 kolonner (label + felt/knap), skalere pænt vandret.
* Standardtekst i statusfelt: “Skriv dit navn og tryk Enter.”
* **Event:** Enter-tast i feltet gør det samme som knappen.

### Øvelse B — Mini-formular med validering (basis)

**Formål:** Flere widgets + simpel validering.
**Krav:**

* Felter: Fornavn (Entry), Efternavn (Entry), Alder (Entry).
* Valg: **Radiobuttons** for køn (fx “Andet/M/K”) eller en **OptionMenu**.
* Knapper: **Gem**, **Nulstil**.
* Validering: “Alder” skal være tal; fornavn/efternavn må ikke være tomme.
* Fejl vises som **rød, lille tekst** under det felt der fejler.
* Statusfelt i bundlinjen (grønt ved succes, rødt ved fejl).
* Layout med **grid**; felter skal kunne strække sig vandret.

### Øvelse C — Notatblok (udvidet)

**Formål:** Text-widget, menuer og filflow planlægges.
**Krav:**

* Stort **Text**-område, statuslinje der viser antal tegn.
* Menulinje (plan): **Fil** → Ny, Åbn…, Gem som…; **Hjælp** → Om…
* Når tekst ændres, opdateres tælleren i statusfeltet.
* Åbn/Gem (plan): brug windows fil-dialog; vis filnavn i vinduestitel.
* Tænk over **tastaturgenveje**: Ctrl+N/O/S og Ctrl+Q (luk).

---

## Arbejdsgang i VS Code (konceptuelt)

* Opret **én fil pr. øvelse** i en modulmappe (fx `02_widgets_layout/`).
* Brug `.venv`-fortolkeren (vælg i statuslinjen).
* Kør programmet via VS Code (ingen kommandolinje nødvendig for modulet her).
* Hold strukturen enkel: én **root** (`Tk`), evt. *Frames* for sektioner.

---

## Tjekliste før videre

* [ ] Jeg kan forklare, hvad de nævnte widgets bruges til.
* [ ] Jeg kan vælge mellem **pack** og **grid** og begrunde valget.
* [ ] Jeg kan pege på, hvor og hvordan UI skal give **feedback** ved succes/fejl.
* [ ] Jeg har skitseret layout (rækker/kolonner eller sektioner) for mindst én øvelse.
* [ ] Jeg ved, hvilke felter der skal valideres og hvordan fejl skal vises.

---

## Definition of Done (Modul 02)

* Du har en **skriftlig plan** for mindst én af øvelserne (A/B/C), inkl. widgets, layout, events og feedback.
* Du kan med dine egne ord beskrive **command** og en enkel **event** (fx Enter).
* Du har besluttet **grid/pack** pr. container og dokumenteret, hvilke elementer der skal **skalere**.

---

## Kendte faldgruber

* **Pack vs. grid**: bland dem ikke i **samme** parent.
* **Mangel på feedback**: brug statuslinje eller nær-felt-fejl — ikke kun popups.
* **Ikke-skalerende UI**: husk `column/rowconfigure` for de kolonner/rækker der skal vokse.
* **Uklare labels**: vær konkret og kort.

---

## Estimeret tid

* **60–90 min.** inkl. skitser og planlægning.

---

## Videre til **03 – Input & validering**

Her omsætter du planerne til praksis: Entry/Text, Check/Radios/OptionMenu, status-feedback og første rigtige validering — stadig enkelt og overskueligt.
*Sig til, hvis du vil have `03` i samme stil (README uden kode) eller med starter-filer.*

