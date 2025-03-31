# ⚠️ Fejlhåndtering og troubleshooting

I dette afsluttende modul ser vi på, hvordan du opdager, håndterer og forebygger kommunikationsfejl mellem din Rockwell PLC og Python. 

Kommunikation over Ethernet/IP kan fejle af mange grunde – netværk, tag-navne, tilstand på PLC eller kodeproblemer.

---

### 🚫 Typiske fejl og deres årsager

| Problem | Mulig årsag | Løsning |
|--------|----------------|----------|
| `result.Value = None` | Forkert tag-navn | Tjek stavning og om tagget er i Controller Scope |
| `result.Status != 'Success'` | PLC svarer ikke | Er PLC'en i RUN? IP korrekt? Netværksforbindelse? |
| `ImportError: No module named pylogix` | Bibliotek ikke installeret | `pip install pylogix` |
| Timeout | Netværksproblem eller langsom PLC | Brug `comm.Timeout = 5` (sekunder) |

---

### 🔐 Brug af `Status` til fejlkontrol
Eksempel på robust aflæsning:
```python
res = comm.Read('TempSensor')
if res.Status != 'Success':
    print(f"Fejl: {res.Status}")
else:
    print(f"Temperatur: {res.Value}")
```

---

### 🚫 Undgå kendte faldgruber
- Undgå Program Scope-tags
- Tjek at PLC er sat til **Remote Run** og ikke kun **Program**
- Slå firewall fra midlertidigt ved test (Windows Defender blokeringer)
- Brug statisk IP på din PC og PLC hvis muligt

---

### 📈 Avanceret: Log dine fejl
Gem fejlhændelser til en logfil:
```python
import logging
logging.basicConfig(filename='plc_errors.log', level=logging.WARNING)

res = comm.Read('TempSensor')
if res.Status != 'Success':
    logging.warning(f"Fejl ved tag 'TempSensor': {res.Status}")
```

---

### ✅ Tjekliste
- [ ] Kan du pinge PLC'en fra din PC?
- [ ] Er IP korrekt sat i Python?
- [ ] Er tag-navnet stavet korrekt og i Controller Scope?
- [ ] Er PLC i RUN og ikke FAULT?
- [ ] Har du testet forbindelsen i Studio 5000 eller RSLinx?

---

Tillykke! Du er nu i stand til at:
- Kommunikere med Rockwell PLC fra Python
- Læse og skrive data
- Visualisere og fejlsøge kommunikation

Du har nu et solidt fundament for at integrere Python med industrielle automatiseringssystemer – klar til prototyper, dashboards og avanceret analyse. 🎉
