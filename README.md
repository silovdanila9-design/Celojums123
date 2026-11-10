# Ceļojumu aģentūras sistēma

**Projekta darbs 2 (PD2) — Programmas koda rakstīšana (Kodēšana), Java valoda**
**Autori:** Danils Silovs, Ričards Čitalkins (8. grupa)
**Tēma:** Nr. 14 — Ceļojumu aģentūras sistēma (ceļojumu katalogs, klientu reģistrācija, rezervāciju veikšana, maksājumu uzskaite, rezervāciju vēsture)

---

## 1. Tehnoloģijas

| Tehnoloģija | Pielietojums |
|---|---|
| Java 17 | programmēšanas valoda |
| NetBeans IDE + **GUI Builder** | izstrāde un galvenā GUI klase `gui.GalvenaisLogs` (viens JFrame logs + visi pārējie logi kā JDialog apakšlogi vienā formā) |
| Apache Derby (embedded) + JDBC | relāciju datu bāze ar FK saistībām |
| JUnit 4 | automātiskie testi (`test/` mape) |
| Git / GitHub | versiju kontrole |

## 2. Projekta struktūra (bez liekām pakotnēm)

```
src/
├── celojumu_agenturass_sistema_silovs_citalkins/   → palaišanas klase (main)
├── gui/       → GalvenaisLogs.java + GalvenaisLogs.form (VIENA GUI Builder klase:
│                 galvenais JFrame logs + 9 JDialog apakšlogi vienā formā)
├── model/     → Lietotajs, Celojums, Rezervacija, Maksajums
├── dao/       → LietotajsDAO, CelojumsDAO, RezervacijaDAO, MaksajumsDAO (JDBC)
├── db/        → DatabaseManager (shēma ar PK/FK + sākuma dati)
├── util/      → PasswordUtil (SHA-256), Validacija, Sesija
└── lidmasina.jpg → fona attēls (lidmašīna) galvenajam logam; ielādē no
                  classpath — ja fails nav atrasts, logs paliek vienkrāsains
test/
├── util/      → ValidacijaTest, PasswordUtilTest
└── dao/       → DatubazesIntegracijasTest (GUI→DAO→JDBC→Derby)
docs/          → ģenerētā Javadoc dokumentācija (atveriet docs/index.html)
lib/           → derby.jar, junit.jar, hamcrest-core-1.3.jar
```

## 3. Instalēšana un palaišana

1. **Atvēršana:** NetBeans → `File → Open Project` → izvēlaties šo mapi → `Open`.
   Nekādas papildu bibliotēkas konfigurēt nav jādara — `derby.jar`, `junit.jar`
   atrodas projekta `lib/` mapē un ir piesaistīti `nbproject/project.properties`.
2. **Palaišana:** peles labā poga uz projekta → `Run` (vai F6).
   Datu bāze izveidojas AUTOMĀTISKI pirmajā palaišanas reizē mapē
   `C:\Users\<lietotājs>\CelojumuAgenturaDB\agenturas_db` (Linux/Mac: `~/CelojumuAgenturaDB/agenturas_db`).
3. **Atkļūdošana:** `Debug Project` (Ctrl+F5) — iespējams likt breakpointus.
4. **Testi:** peles labā poga uz projekta → `Test Files` (Alt+F6), vai uz
   atsevišķa testa faila → `Run File`. Sagaidāmais rezultāts: **20 testi, visi OK**.
5. **Javadoc:** `Run → Generate Javadoc` NetBeans vidē (gatava versija jau ir `docs/` mapē).
6. **Distribūcija:** `Clean and Build` izveido `dist/*.jar`; to var palaist arī citā
   datorā ar komandu `java -jar dist/Celojumu_Agenturass_Sistema_Silovs_Citalkins.jar`
   (blakus jāatrodas `lib/derby.jar` un jānorāda classpath, vai izmanto NetBeans projektu).

## 4. Iekļautie konti

| Loma | Lietotājvārds | Parole | Tiesības |
|---|---|---|---|
| Administrators | `admin` | `admin123` | pārvalda katalogu (pievieno/labot/dzēst ceļojumus), redz VISAS rezervācijas un maksājumus, dzēš maksājumus un klientus |
| Klients | (reģistrējieties paši caur "Jauna klienta reģistrācija") | — | apskata katalogu, meklē, rezervē, apmaksā un pārvalda TIKAI SAVAS rezervācijas |

Pirmajā palaišanā datu bāzē automātiski tiek iesēti 18 ceļojumi un administrators.

## 5. Demo scenārijs (prezentācijai)

1. Palaidiet programmu → atveras **galvenais logs** (nosaukums, izvēlne, pogas).
2. Reģistrējiet jaunu klientu ("Jauna klienta reģistrācija").
3. Pieslēdzieties ("Pieteikšanās sistēmā" → klienta dati) → poga "Nav pieslēgties" nomainās uz "Pieslēdzies: ...".
4. "Ceļojumu katalogs" → meklēšana pēc atslēgvārda (piem., "Santorīni").
5. "Rezervācijas" → izvēlieties ceļojumu, ievadiet personu skaitu → "Rezervēt" → ieraksts parādās tabulā.
6. "Maksājumu uzskaite" → ievadiet rezervācijas ID un daļēju summu → pēc pilnīgas apmaksas statuss automātiski mainās uz APMAKSATA.
7. "Rezervāciju vēsture" → filtrēt pēc statusa; kopsavilkuma rinda parāda kopsummu un apmaksāto.
8. Izrakstieties (izvēlne Darbības → Izrakstīties), pieslēdzieties kā `admin/admin123` → pieejams "Klientu saraksts", kataloga pārvaldība un visu rezervāciju pārvaldība.
9. Aizveriet programmu ar krustziņu → apstiprinājuma dialogs → dati saglabājas datu bāzē (pārbaudiet, atkārtoti palaižot).

## 6. Kas uzlabots salīdzinājumā ar iepriekšējo versiju

1. **Visi logi vienā GUI Builder klasē** — `GalvenaisLogs` (JFrame) + 9 JDialog apakšlogi vienā `.form` failā; pieteikšanās/reģistrācija vairs nav JOptionPane pagaidu paneļi, bet pilnvērtīgi dialogi.
2. **Datubāzes shēma pārrakstīta ar PK/FK** — pievienotas tabulas MAKSAJUMS un loma LIETOTAJS tabulā, ārējās atslēgas (REZERVACIJA→LIETOTAJS, REZERVACIJA→CELOJUMS, MAKSAJUMS→REZERVACIJA), CHECK ierobežojumi un indeksi; iepriekš MaksajumsDAO un AdministratorsDAO izsauca neeksistējošas tabulas.
3. **Rezervācija saistīta ar klienta ID** (ārējā atslēga), nevis ar lietotājvārdu kā tekstu; klients redz tikai savus datus.
4. **Pilns CRUD visam:** ceļojumi (pievienot/labot/dzēst + meklēšana), rezervācijas (izveide/atcelšana/dzēšana + statusi), maksājumi (reģistrēšana ar automātisku APMAKSATA statusu, dzēšana ar statusa atgriešanu), klienti (saraksts/meklēšana/dzēšana).
5. **Autentifikācija un autorizācija:** lomas ADMIN/KLIENTS, poga "Nav pieslēgties" atjaunojas pēc pieslēgšanās, klientam admin pogas ir atslēgtas.
6. **Paroles tiek kodētas SHA-256** (iepriekš PasswordUtil atgrieza paroli bez kodēšanas).
7. **Validācija:** e-pasta formāts, paroles garums un sakritība, cena > 0, personas 1–10, datumi dd.mm.gggg, vietu skaita kontrole.
8. **PDF prasības logiem:** katrs logs ar virsrakstu un centrēts uz ekrāna, vienoti fonti/krāsas, vienlaikus redzams tikai viens logs, pēc aizvēršanas atgriežas galvenais logs un lauki tiek notīrīti, pēc katra DB nolasījuma/izmaiņu showMessageDialog (INFORMATION_MESSAGE/ERROR_MESSAGE), iziešana ar showConfirmDialog.
9. **JUnit4 testu kopa 20 testiem** (validācija, parole, integrācija ar izolētu pagaidu Derby bāzi) — iepriekš viens tests pat izpildījās ar kļūdu.
10. **Javadoc komentāri visām klasēm un metodēm** (@param, @return, @throws) + ģenerēta dokumentācija `docs/` mapē; galvenās klases `main()` vairs nav tukšs.
11. **Atjaunināts dizains:** visas pogas tumši rozā (darbībām gaišāks, aizvēršanai tumšāks tonis, ar uzbrērumu peles kursoram), gaiši rozains dialogu fons, rozā tabulu galvenes un izvēles rindas, tumša izvēļne ar baltu tekstu, bet galvenajā logā — fona fotogrāfija ar lidmašīnu (`src/lidmasina.jpg`, automātiski mērogojas loga izmēram).

## 7. Sīkāka informācija par datu bāzi

- Ja datu bāze ir bojāta vai vēlaties sākt no jauna — izdzēsiet mapi `~/CelojumuAgenturaDB` un palaidiet programmu vēlreiz.
- Testi izmanto **atsevišķu pagaidu datu bāzi** (sistēmas rekvizīts `celojumu.db.home`), tāpēc testēšana nesabojā reālos datus.
- Shēma: `LIETOTAJS (PK id)`, `CELOJUMS (PK id)`, `REZERVACIJA (PK id, FK lietotajs_id, FK celojums_id)`, `MAKSAJUMS (PK id, FK rezervacija_id)`.
