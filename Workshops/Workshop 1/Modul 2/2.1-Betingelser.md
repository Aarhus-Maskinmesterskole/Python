
# 📌 **Modul 2: Betingelser i Python**  

I dette modul lærer du at bruge betingede udsagn (`if`, `elif`, `else`) til at styre programflowet i Python. Med betingelser kan din kode **træffe beslutninger**, afhængigt af input eller data.

> **Mål for dette modul:**  
> ✅ Forstå `if`, `elif`, og `else`  
> ✅ Lære at bruge sammenligningsoperatorer  
> ✅ Arbejde med logiske operatorer (`and`, `or`, `not`)  
> ✅ Skrive programmer, der træffer beslutninger baseret på brugerinput  

---

## 🏗 **1. If-sætninger: Beslutningstagning i Python**  

En `if`-sætning gør det muligt at udføre kode **kun hvis en bestemt betingelse er opfyldt**.

```python
alder = 18

if alder >= 18:
    print("Du er myndig!")
```

👉 Hvis `alder` er **større end eller lig med 18**, udskrives `"Du er myndig!"`.  

> **OBS:** Python bruger **indrykning (indentation)** til at definere kodeblokke. Alt kode indenfor `if`-blokken skal være **indrykket med 4 mellemrum eller en tab**.

---

## 🔄 **2. If-Else: Hvad hvis betingelsen ikke er opfyldt?**  

Du kan bruge `else` til at definere en alternativ handling, hvis betingelsen **ikke** er sand.

```python
alder = 16

if alder >= 18:
    print("Du er myndig!")
else:
    print("Du er ikke myndig endnu.")
```

💡 Output: `"Du er ikke myndig endnu."`  

---

## 🔀 **3. If-Elif-Else: Flere betingelser**  

Hvis du vil have flere muligheder, kan du bruge `elif` (else if). Python evaluerer dem **i rækkefølge** og stopper, når en betingelse er opfyldt.

```python
karakter = 10

if karakter == 12:
    print("Perfekt!")
elif karakter >= 7:
    print("Godt gået!")
elif karakter >= 2:
    print("Bestået.")
else:
    print("Desværre ikke bestået.")
```

🔹 Hvis `karakter` er **10**, vil den første `if` ikke gælde, men `elif karakter >= 7` vil blive kørt.  

---

## 🔢 **4. Sammenligningsoperatorer**

Python bruger disse **operatorer** til at sammenligne værdier:

| Operator | Beskrivelse | Eksempel (`a = 5`, `b = 10`) | Resultat |
|----------|------------|----------------|---------|
| `==`     | Lighed     | `a == b`       | `False` |
| `!=`     | Ulighed    | `a != b`       | `True`  |
| `>`      | Større end | `b > a`        | `True`  |
| `<`      | Mindre end | `a < b`        | `True`  |
| `>=`     | Større el. lig | `a >= 5`  | `True`  |
| `<=`     | Mindre el. lig | `b <= 10` | `True`  |

Eksempel med en **if-sætning**:

```python
x = 15
if x > 10:
    print("x er større end 10.")
```

---

## 🛠 **5. Logiske operatorer (`and`, `or`, `not`)**  

Logiske operatorer bruges til at **kombinere flere betingelser** i én if-sætning.

| Operator | Beskrivelse | Eksempel |
|----------|------------|----------|
| `and`    | Sand **hvis begge** betingelser er sande | `x > 5 and x < 20` |
| `or`     | Sand **hvis mindst én** betingelse er sand | `x < 5 or x > 10` |
| `not`    | Gør en betingelse **modsat** | `not(x > 5)` |

### Eksempler

**Brug af `and`** (begge skal være sande):  
```python
alder = 20
indtægt = 5000

if alder >= 18 and indtægt >= 4000:
    print("Du kan få et kreditkort.")
else:
    print("Du opfylder ikke kravene.")
```

**Brug af `or`** (mindst én skal være sand):  
```python
vejret = "sol"
dag = "søndag"

if vejret == "sol" or dag == "søndag":
    print("Lad os tage på stranden!")
```

**Brug af `not`** (modsat betingelse):  
```python
er_regnvejr = False

if not er_regnvejr:
    print("Vi kan gå en tur!")
```

---

## 🎯 **6. Øvelser**

### 1️⃣ **Grundlæggende if-else**
- Bed brugeren om at indtaste en temperatur (brug `input()`).
- Hvis temperaturen er under 0, udskriv `"Det er frostvejr!"`.
- Hvis den er 0-15, udskriv `"Det er koldt!"`.
- Ellers udskriv `"Det er varmt!"`.

### 2️⃣ **Ligestillingskontrol**
- Opret to variabler `x` og `y` med tilfældige værdier.
- Lav en if-sætning, der tjekker:
  - Om `x` og `y` er ens.
  - Om `x` er større end `y`.
  - Om `y` er større end `x`.

### 3️⃣ **Vælg en aktivitet**
- Bed brugeren om at indtaste, hvilken dag det er (`mandag`, `fredag`, `søndag` osv.).
- Brug `if`, `elif` og `else` til at foreslå en aktivitet afhængigt af dagen.

### 🔥 **Bonusudfordring**
Lav et **loginsystem**, der beder brugeren om et brugernavn og en adgangskode.

---

## 🚀 **7. Opsummering**
✔️ `if` tjekker en betingelse  
✔️ `else` køres, hvis `if` ikke er sand  
✔️ `elif` tilføjer flere betingelser  
✔️ **Sammenligningsoperatorer** (`==`, `<`, `>`, `!=`, osv.)  
✔️ **Logiske operatorer** (`and`, `or`, `not`)  

**📌 Videre til næste modul: [Loops](2.2-Loops.md)**  
Her lærer du, hvordan du gentager kode med **for- og while-løkker**! 🌀  

**God fornøjelse med betingelser i Python!** 🎉🐍
