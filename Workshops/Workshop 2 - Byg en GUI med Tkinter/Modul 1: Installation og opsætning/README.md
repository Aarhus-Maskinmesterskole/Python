# **01 – Introduktion & opsætning** (Tkinter · Windows + VS Code)

> **Formål:** Dette modul gør dig klar til at arbejde med grafiske brugerflader i Python med **Tkinter**. Vi afklarer begreber, forventninger og arbejdsgang i **Windows + VS Code**. **Ingen kode** i dette modul — selve installationstrin ligger i `installation.md`.

---

## Velkommen

Modul 01 er din bløde indflyvning til GUI-udvikling i Python. Du får overblik over, hvad Tkinter er, hvordan et GUI-program “lever” (event-loop), og hvordan vi strukturerer resten af workshoppen. Du afslutter modulet med en klar tjekliste, så du ved, at alt er klar til Modul 02.

---

## Hvad er Tkinter — kort fortalt

* **Tkinter** er Pythons standardbibliotek til GUI (følger med Python-installationen).
* Styrker: hurtigt at komme i gang, ingen ekstra pakker, godt til undervisning & små værktøjer.
* Fokus i workshoppen: forstå **widgets** (knapper, labels, felter), **layout** (pack, grid, place) og **events** (hændelser) — så du kan bygge små, robuste apps.

---

## Formål med Modul 01

* Give dig et **mentalt kort** over GUI-begreber (widgets, layout, events, event-loop).
* Afklare **arbejdsgang i VS Code** på Windows (uden at gå i detaljer med kommandoer her).
* Sikre at du ved præcis, hvad der skal være på plads, **før** du skriver den første linje kode i Modul 02.

---

## Læringsmål (efter dette modul kan du…)

* beskrive, hvad **Tkinter** er, og hvornår det er et godt valg,
* forklare forskellen på **widgets** og **layout-systemer** (pack, grid, place) på et overordnet niveau,
* forklare, hvad **event-loopet** gør i en GUI,
* redegøre for den anbefalede **arbejdsgang i VS Code** (vælge fortolker, arbejde i projektmappe/venv),
* krydse tjeklisten af, så du er klar til at kode i Modul 02.

---

## Målgruppe & forudsætninger

* **Målgruppe:** Studerende/undervisningsdeltagere uden tidligere GUI-erfaring.
* **Forudsætninger:**

  * Windows 10/11
  * Python 3.12+ installeret
  * Visual Studio Code med udvidelserne **Python** og **Pylance**
  * Basal Python (variabler, funktioner, simpel kontrolflow)
* **Ikke nødvendigt:** Git, GitHub, ekstern hardware, eller eksterne Python-pakker.

> **Praktisk:** Alle konkrete installations- og kontroltrin findes i `installation.md`.

---

## Indhold i Modul 01

1. **Kort intro til Tkinter**: hvad, hvorfor og typiske anvendelser.
2. **GUI-grundbegreber** *(konceptuelt)*

   * **Widgets**: byggeklodserne (labels, knapper, tekstfelter).
   * **Layout**: principper bag pack, grid og place — hvornår hvad giver mening.
   * **Events & event-loop**: hvorfor en GUI “lever”, og hvordan app’en reagerer på brugeren.
3. **Arbejdsgang i VS Code** *(konceptuelt)*

   * Projektmappe pr. modul/forløb.
   * Vælg den **rigtige Python-fortolker** (din `.venv`).
   * Kørsel og terminal i VS Code — uden kommandoer her, se `installation.md`.
4. **Kvalitetskriterier inden du koder**

   * Fortolker valgt i VS Code (peger på `.venv`).
   * Tkinter er tilgængelig (verificeres efter `installation.md`).
   * Du kan forklare widgets/layout/event-loop med dine egne ord.

---

## Mini-heuristik for gode GUI’er (uden kode)

* **Klar tekst:** sig hvad felter og knapper gør.
* **Konsistens:** samme afstande, samme skrifter, samme tone.
* **Fokus & flow:** Tab-rækkefølge giver mening; Enter/Escape-adfærd overvejes.
* **Feedback:** viser tydeligt “succes/fejl/busy”.
* **En skærm — ét formål:** undgå at proppes for meget ind i ét vindue.
* **Tilgængelighed light:** kontrast, fontstørrelse og synligt fokusfelt.

---

## Kendte faldgruber (godt at vide fra start)

* **Bland ikke** `pack` og `grid` i **samme** container (samme parent-frame).
* **Blokér ikke** event-loopet med lange beregninger (GUI “fryser”).
* **Skærmskalering/DPI** kan påvirke layout — test i forskellige vinduesstørrelser.
* `Toplevel` (sekundært vindue) **≠** `Tk` (hovedvinduet).

---

## Tjekliste før du går videre

* [ ] Python 3.12+ og VS Code installeret
* [ ] Udvidelserne **Python** og **Pylance** er aktive i VS Code
* [ ] Projektmappe oprettet og åbnet i VS Code
* [ ] `.venv` oprettet og valgt som fortolker i VS Code
* [ ] Tkinter verificeret (se `installation.md`)
* [ ] Jeg kan forklare: *widget*, *layout (pack/grid/place)*, *event*, *event-loop*

---

## Definition of Done (Modul 01)

* Du har læst og forstået modulbeskrivelsen og begreberne.
* Dit Windows/VS Code-miljø er klar (verificeret via `installation.md`).
* Du er klar til at oprette den første Tkinter-fil i **02 – Grundlæggende widgets & layout**.

---

## Tid & leverancer

* **Estimeret tid:** 45–60 min.
* **Leverance:** Afkrydset tjekliste og kort refleksion (3–5 linjer) om, hvad du forventer at få ud af Tkinter, og hvilke begreber du vil holde ekstra øje med i næste modul.

---

## Videre til **02 – Grundlæggende widgets & layout**

I næste modul åbner du dit første vindue, placerer dine første widgets og arbejder med begivenheder og layout i praksis — trin for trin.
