# 🔧 Opsætning af Rockwell PLC i Studio 5000

Dette modul guider dig gennem opsætningen af en simpel tag-struktur i Studio 5000, så du kan etablere forbindelse til PLC'en fra Python ved hjælp af `pylogix`.

### 🔹 Forudsætninger
- Du har adgang til Studio 5000 Logix Designer
- Du har en CompactLogix, ControlLogix eller bruger Echo
- Du kender IP-adressen på din PLC (eksempel: `192.168.1.10`)

### 📂 Oprettelse af nyt projekt
1. Åbn **Studio 5000 Logix Designer**
2. Klik **New Project** og vælg den rette controller-type
3. Navngiv projektet, fx `PylogixTest`
4. Indtast controllerens IP og vælg rack/slot (typisk slot 0)
5. Klik *Finish*

### 🖊️ Opret tags i Controller Scope
1. I **Controller Organizer**: Gå til *Controller Tags*
2. Klik **Edit Tags** og tilføj følgende:

| Name        | Data Type  | Description                |
|-------------|------------|----------------------------|
| TempSensor  | REAL       | Simuleret temperaturværdi |
| SetPoint    | REAL       | Målværdi sat af Python     |
| SwitchState | BOOL       | Boolsk ON/OFF input        |

> 📅 Tip: Du kan simulere ændringer i Echo ved at ændre tag-værdier manuelt i tag-listen.

### 📶 Netværksopsætning
1. I projektet: Gå til *Ethernet Port Configuration* under Controller Properties
2. Angiv IP-adresse der matcher din netværksplan (fx `192.168.1.10`)
3. Download projektet til PLC eller Emulate controller
4. Brug *RSLinx Classic* eller *Ethernet Devices* til at teste forbindelsen fra PC til PLC

### 🚫 Undgå fejlkilder
- Firewall på PC kan blokere trafik til PLC
- Du skal være på samme subnet (f.eks. 192.168.1.x)
- Brug et direkte kabel eller managed switch, hvis muligt

### 📁 Gem og klar
- Gem og luk projektet
- Notér IP-adressen og tag-navnene du har oprettet – vi skal bruge dem i Python!

---

I næste modul installerer vi `pylogix` og tester forbindelsen fra Python → PLC.

