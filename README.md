# Python – Selvstudie (grundlæggende) 

## Introduktion

Dette forløb er dit **rolige, selvstyrende startpunkt** til Python. Fokus er 100% på **ren, grundlæggende Python** – ingen Git, ingen GitHub, ingen hardware (ingen ESP32/PLC). Materialet er inspireret af måden *Teknologi & Projektudvikling (2. sem.)* arbejder på, men fungerer som **fundamentet** før man kobler software på sensorer/PLC senere.

---

## Velkommen

Målet er, at du hurtigt kan komme i gang i **VS Code på Windows**, skrive små, læsbare programmer og forstå, hvad din kode gør. Du arbejder i korte “missions”, der hver kan klares på 45–90 min.

---

## Forventninger

* Brug **1–2 timer pr. mission** (nogle kortere, nogle længere).
* Læs missionens korte README, **løs opgaver i `tasks.py`**, og kør lokale tests (hvis missionen har tests).
* Skriv 3–5 linjers **refleksion**: *Hvad var svært? Hvad lærte jeg?*
* Du må gerne google fejlbeskeder—men forstå **hvorfor** løsningen virker.

---

## Forudsætninger

* **Windows 10/11**
* **Python 3.12+** (Installeres via Microsoft Store eller python.org)
* **Visual Studio Code** + **Python**-udvidelsen (Microsoft) og **Pylance**
* Ingen Git, ingen online-konti – **alt foregår lokalt**

---

## Struktur

Forløbet er delt i 8–10 korte **missions**. Hver mission har:

* Et mini-oplæg (hvad og hvorfor)
* Opgaver i `tasks.py`
* Evt. tests, du kan køre lokalt (for at tjekke dig selv)

**Oversigt (eksempler):**

1. **Intro** – variabler, `print`, simple funktioner
2. **Flow** – `if/elif/else`, `for`/`while`, FizzBuzz
3. **Samlinger** – `list`, `dict`, `set`, `tuple`, slicing
4. **Strenge** – rensning, splitting, palindromer
5. **Funktioner** – parametre, defaults, genbrug
6. **Filer & JSON** – `pathlib`, læs/skriv tekst, `json`
7. **Fejl & exceptions** – `try/except`, robusthed
8. **OOP/dataclasses** – simple værdityper og metoder
9. **Iteratorer/generatorer** *(valgfri)*
10. **Lille CLI** *(valgfri)*

> **Bemærk:** Denne grundpakke bruger primært **standardbiblioteket**. Hvis I senere vil bygge bro til dataanalyse (fx Pandas/NumPy/Matplotlib) eller hardware, kan vi lægge *tilvalgs-missions* ovenpå.

---

## Formål

* Give et **stærkt Python-fundament**: tænkning i små funktioner, klar kontrolflow, og praktisk filhåndtering.
* Skabe **gode vaner**: navngivning, enkle moduler, fejlhåndtering og basal test.
* Understøtte en **glat overgang** til projekter i Teknologi & Projektudvikling (fx kravspec, test og senere integration) – men **uden** at kræve hardware nu.

---

## Kompetencer (du opbygger)

* **Programmeringslogik**: beslutninger, løkker, funktioner
* **Datahåndtering**: arbejde med strenge, lister, ordbøger og filer/JSON
* **Fejlhåndtering**: forstå fejl og håndter dem uden crash
* **Struktur & læsbarhed**: gøre kode enkel at forstå og vedligeholde
* **(Valgfrit)**: bygge en lille **CLI** med `argparse`

---

## Hvor skal jeg starte? (trin-for-trin i VS Code på Windows)

### 1) Installér værktøjer

* **Python 3.12+**: Microsoft Store → “Python 3.12” → Installer
* **VS Code**: installer fra code.visualstudio.com
* Åbn VS Code → **Extensions** (Ctrl+Shift+X) → installer **Python** (Microsoft) og **Pylance**

### 2) Åbn din kursusmappe i VS Code

**File → Open Folder…** → vælg mappen, som kommer til at indeholde `missions\`.

### 3) Opret og aktivér virtuelt miljø (i VS Codes terminal)

Åbn **Terminal** (Ctrl+\`) og kør:

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
```

> Første gang kan PowerShell bede om tilladelse til scripts. Kør da:
>
> ```powershell
> Set-ExecutionPolicy -Scope CurrentUser RemoteSigned -Force
> ```
>
> Luk/åbn terminalen igen og aktivér venv:
>
> ```powershell
> .\.venv\Scripts\Activate.ps1
> ```

### 4) (Valgfrit) Installér testværktøj

```powershell
py -m pip install -U pip
py -m pip install pytest
```

### 5) Start i **`missions\01_intro`**

* Læs `missions\01_intro\README.md`
* Åbn `missions\01_intro\tasks.py` og løs opgaverne
* **Hvis missionen har tests**, kør:

  ```powershell
  py -m pytest -q missions\01_intro
  ```

  Ret koden til alle tests er **grønne**.
* Fortsæt derefter til **`missions\02_flow`** med samme rytme.

---

*Når du har gennemført 01–04, har du et solidt Python-grundlag. 05–10 tilføjer filer, fejlhåndtering og – hvis du vil – en lille CLI. Senere kan der lægges spor til dataanalyse eller hardwareintegration, men det er **ikke** en del af dette grundforløb.*
