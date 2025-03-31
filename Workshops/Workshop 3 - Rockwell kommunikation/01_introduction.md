# 🎥 Introduktion til Rockwell PLC-kommunikation

I denne workshop introduceres du til, hvordan du opretter forbindelse mellem en Rockwell PLC (CompactLogix eller ControlLogix) og Python ved hjælp af biblioteket `pylogix`. Dette er en vigtig færdighed, hvis du vil bygge bro mellem industrielle styringer og databehandling i Python.

### Hvorfor kommunikere med en PLC fra Python?
Moderne automationssystemer kræver øget fleksibilitet og evnen til at udtrække, analysere og reagere på data i realtid. Ved at forbinde Python til en PLC får du adgang til:

- 🔹 Realtidsdataanalyse (temperatur, tryk, flow osv.)
- 🔹 Dynamisk styring eller overvågning fra et eksternt system
- 🔹 Integration med databaser, dashboards eller AI-modeller

### Hvad er pylogix?
`pylogix` er et Python-bibliotek, der bruger Ethernet/IP til at kommunikere med Rockwell PLC'er. Det er let at bruge og kræver minimal opsætning.

Fordele:
- 💡 Enkel syntaks og hurtig adgang til tag-data
- 💡 Understøtter baåde læsning og skrivning
- 💡 Ideel til prototyper, dashboardintegration og dataopsamling

### Hvordan fungerer Ethernet/IP?
Rockwell PLC'er kommunikerer via Ethernet/IP (Industrial Protocol) - en standard protokol til industriel kommunikation.

Ethernet/IP bygger på TCP/IP og anvender et object-orienteret model, hvor hver enhed (f.eks. en PLC) tilbyder adgang til "tags" eller dataværdier.

Pylogix gør det muligt at læse og skrive disse tags direkte fra Python-kode.

### Forudsætninger for workshoppen
Inden du fortsætter, skal du sikre dig følgende:

- En fungerende Rockwell PLC (eller en LogixEmulate-session)
- Kendskab til Studio 5000 og oprettelse af tags
- Python 3.x installeret på din PC
- Et netværk, hvor din PC kan pinge PLC'en (f.eks. 192.168.1.10)

### Næste skridt
I næste modul (“02-plc-setup.md”) skal vi opsætte en simpel tag-struktur i Studio 5000, som vi senere kan tilgå via Python.

---

🚀 Klar til at få Python og PLC til at tale sammen? Lad os komme i gang!

