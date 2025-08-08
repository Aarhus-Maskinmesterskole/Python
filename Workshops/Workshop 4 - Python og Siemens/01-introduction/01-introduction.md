# **01 – Introduktion til Siemens S7 & Snap7**

*(ingen forudsætninger · ingen kode i dette modul)*

## 👋 Hvad er det her?

Velkommen! Dette er første skridt i en begynder‐venlig workshop, hvor du lærer at lade **Python** tale med **Siemens S7** via **Snap7**. Vi starter med overblik og begreber — **ingen hardware** og **ingen kode** endnu. Næste modul guider dig igennem installation.

---

## 🎯 Formål (hvad du får ud af modul 01)

* Forstå **hvad en PLC er**, og hvad **Siemens S7** betyder i praksis.
* Vide hvad **Snap7 / python-snap7** gør: læse/skriv data over Ethernet.
* Få et **mentalt kort** over S7’s vigtigste dataområder (DB, M, I, Q).
* Lære ord som **DB-adresse, offset, bit/byte, rack/slot, PUT/GET, timeout**.
* Være klar til at installere i **02 – installation** (og køre en “fake PLC” i 03).

---

## 🧠 Hurtig PLC-intro

* **PLC** (Programmable Logic Controller): en robust industricomputer, der læser **inputs**, styrer **outputs**, og kører et program i en **cyklus**.
* **Siemens S7**: PLC-familie (fx S7-1200/1500).
* **Ethernet (S7 protokol)**: netværksvejen, som Snap7 bruger til at **læse/skriv** data i PLC’en.

---

## 🗂️ S7 dataområder (kort og klart)

* **DB** (*Data Block*): dine strukturerede data/variabler. Her læser/skriv’er vi oftest.
* **M** (*Merker*): interne “hjælpebits/bytes” i PLC’en.
* **I** (*Inputs*): fysiske indgange (sensorer).
* **Q** (*Outputs*): fysiske udgange (aktuatorer).

> I workshoppen fokuserer vi primært på **DB** (sikrest og mest almindeligt).

---

## 🧩 Adressering – hvordan vi peger på data

* **DB-nummer**: fx *DB1*, *DB12*
* **Offset**: hvor i blokken? (byteposition)
* **Type & længde**:

  * **BOOL** → 1 bit (fx *DBX12.0*)
  * **BYTE** → 8 bit (1 byte)
  * **WORD/INT** → 16 bit (2 bytes)
  * **DWORD/DINT/REAL** → 32 bit (4 bytes)
* **Endianness**: S7 bruger **big-endian** for flertalsbytes. (*Bare rolig:* `python-snap7` helperfunktioner håndterer det for dig.)

**Eksempel på adresseidé (uden kode):**
“**DB1, offset 4, DINT**” betyder: læs 4 bytes fra DB1 startende ved byte 4 og fortolk som 32-bit heltal.

---

## 🔌 Forbindelse – parametre du vil genkende

* **IP**: PLC’ens IP-adresse (fx `192.168.0.10`).
* **Rack/Slot**: placeringen af CPU’en på nogle S7-modeller.

  * Typisk **S7-1200/1500** → `rack=0, slot=1`
  * Ofte **S7-300** → `rack=0, slot=2`
* **PUT/GET**: PLC-indstilling der skal være **tilladt**, hvis eksterne klienter må læse/skriv.

> I starten bruger vi en **lokal, simuleret PLC** (snap7-server) — ingen IP eller TIA-opsætning nødvendig.

---

## 🛤️ Hvordan workshoppen forløber (små succeser)

* **02 – installation**: du installerer Python, VS Code og `python-snap7`.
* **03 – fake PLC**: du starter en **simuleret PLC** lokalt og læser en tæller.
* **04 – read data**: du læser bytes fra en DB og fortolker dem som INT/DINT/REAL/BOOL.
* **05 – write data**: du skriver sikre testværdier tilbage til DB.
* **06 – dataclass**: du mapper rå bytes til *pæne Python-objekter*.
* **07 – logging**: du logger målinger til CSV.
* **08 – error handling**: timeouts, reconnect, robusthed.
* **09 – (valgfrit) rigtig PLC**: tjekliste og første forsigtige læsning.

Hvert modul = **ét lille README + én mini-øvefil** → hurtig “grøn” oplevelse.

---

## 🧭 Begrebs-ordliste (til lommen)

* **Snap7 / python-snap7**: bibliotek/py-binding til S7-protokollen.
* **DB**: Data Block (vores hovedfokus).
* **Offset**: byteposition i DB’en (hvor data starter).
* **BOOL / INT / DINT / REAL**: almindelige PLC-datatyper.
* **Rack/Slot**: hardwareplacering af CPU’en (bruges ved forbindelse).
* **PUT/GET**: PLC-tilladelse for eksterne læs/skriv.
* **Timeout**: hvor længe klienten venter på svar, før den giver op.

---

## ✅ Tjekliste (klar til modul 02)

* Jeg ved hvad **PLC, S7 og Snap7** grundlæggende betyder.
* Jeg kan forklare forskellen på **DB/M/I/Q** med én sætning.
* Jeg forstår idéen bag **DB-nummer, offset og datatype**.
* Jeg ved, at vi starter **uden hardware** (fake PLC), og at **rigtig PLC** først kommer til sidst.
* Jeg er klar til at installere værktøjerne i **02 – installation**.

---

## ⏱️ Tid & næste skridt

* **Tid:** 10–15 min.
* **Videre:** Åbn **`02-installation.md`** og følg guiden.
  Når du er færdig med 02, kan du i **03** starte din første “fake PLC” og læse din **første værdi** 🚀

---

*Spørgsmål undervejs? Notér dem — vi samler op, inden vi går videre til installation.*
