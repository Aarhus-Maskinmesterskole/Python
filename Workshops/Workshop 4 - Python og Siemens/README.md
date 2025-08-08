# 🚀 Workshop: Kommunikation mellem Python og Siemens S7 (Snap7)

## 📌 Introduktion

Denne workshop lærer dig at tale med en **Siemens S7-PLC** fra **Python** via `python-snap7`. Du starter helt **uden hardware** ved at bruge en indbygget **simuleret PLC** (server), og bevæger dig derfra til at **læse/skrive data** i Data Blocks (DB), mappe bytes til rigtige typer og (valgfrit) koble til en **rigtig PLC**.

---

## 🎯 Hvad du lærer i denne workshop

✔️ Installation og brug af **python-snap7** på Windows  
✔️ Køre en **lokal “fake” PLC** (snap7 server) og øve sikkert uden udstyr  
✔️ **Læsning** (db\_read) og **skrivning** (db\_write) af DB-data  
✔️ Mapping af **bytes → INT/DINT/REAL/BOOL** (og tilbage) + dataclass-model  
✔️ **Logning** af måleværdier til CSV (valgfrit: simpel visualisering)  
✔️ **Fejlhåndtering** (timeouts, reconnect) og tjekliste til **rigtig PLC**  

---

## 📌 Indhold

| Modul                          | Emne                                                          |
| ------------------------------ | ------------------------------------------------------------- |
| 📄 **01-introduction.md**      | 🎬 Introduktion til S7, Snap7 og dataområder (DB/M/I/Q)       |
| 📄 **02-installation.md**      | 🖥️ Installation af Python + VS Code + python-snap7           |
| 📄 **03-fake-plc.md**          | 🧪 Start en lokal snap7-server og læs første værdi            |
| 📄 **04-read-data.md**         | 📥 Læs DB-bytes og pak til INT/DINT/REAL/BOOL                 |
| 📄 **05-write-data.md**        | 📤 Skriv værdier tilbage til DB på sikre offsets              |
| 📄 **06-dataclass-mapping.md** | 🧩 Dataclass/strukturering af DB-felter (renere kode)         |
| 📄 **07-logging.md**           | 🗃️ Log til CSV (valgfrit: enkel live-visning)                |
| 📄 **08-error-handling.md**    | ⚠️ Timeouts, reconnect, robusthed og debugging                |
| 📄 **09-real-plc-optional.md** | 🔌 Valgfrit: Forbind til rigtig PLC (TIA: PUT/GET, rack/slot) |

> Hvert modul er **kort og afsluttet**: 1 README + 1 lille øvefil, så du får hurtig succes trin for trin.

---

## 🔧 Krav før du starter

### 💻 Software (ingen PLC påkrævet)

✅ **Windows 10/11**
✅ **Python 3.12+**
✅ **Visual Studio Code** (Extensions: *Python*, *Pylance*)
✅ **python-snap7**
✅ *(Valgfrit, først til sidst)* Siemens **TIA Portal** og adgang til en test-PLC

---

## 📥 Installation af nødvendige Python-pakker

```bash
pip install python-snap7
```

*(Valgfrit senere i kurset:)*

```bash
pip install matplotlib
```

---

✅ **Nu er du klar til at starte på 01–introduction og derefter 02–installation.**
Vi tager det **roligere end roligt**, med små moduler og tydelige milepæle 💪
