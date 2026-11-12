# PocketCar — Romania Vehicle Data Sources

Versiune 0.1 · Verificare: 12 noiembrie 2026

## 1. Principiul de integrare
VIN-ul poate decoda unele caracteristici și poate fi folosit la anumite verificări. Nu este o cheie universală pentru toate valabilitățile.

Distinge între:
- API oficial documentat;
- formular public oficial;
- parteneriat privat;
- date furnizate de utilizator.

Nu implementa scraping agresiv, ocolire CAPTCHA sau API-uri private neautorizate.

## 2. Decodare VIN
Sursă de test: NHTSA vPIC — https://vpic.nhtsa.dot.gov/api/

- API public de decodare VIN, bazat pe date transmise de producători.
- Are limitare automată a traficului.
- Completitudinea câmpurilor variază după producător și piață.
- Este un catalog NHTSA din SUA; pentru vehicule europene poate returna câmpuri incomplete.
- Nu oferă prin decodare istoricul service, kilometrajul real, proprietari, RCA, CASCO, ITP sau rovinietă.

Cerințe:
- Izolează furnizorul într-un adaptor configurabil.
- Nu presupune că un singur decoder acoperă toate mărcile europene.
- Păstrează sursa și momentul decodării pentru fiecare câmp.
- Afișează necunoscutele ca necunoscute.
- Permite confirmare și editare.
- Evaluează furnizori comerciali pentru acoperire UE, cost, licență și retenție.

## 3. RCA — AIDA / BAAR
URL: https://www.aida.info.ro/ro/politerca/cautare

- AIDA permite verificarea poliței RCA după VIN sau număr de înmatriculare și data verificării.
- Datele reflectă informațiile introduse de asigurători.
- Pentru neconcordanțe, BAAR recomandă contactarea asigurătorului.
- Pagina publică observată este un formular web; nu a fost identificată documentație API publică.

MVP:
1. Deschide sursa oficială și explică pașii.
2. Utilizatorul caută după VIN sau număr.
3. Utilizatorul introduce/confirmă rezultatul și expirarea din poliță.
4. Salvează sursa și momentul verificării.

Automatizare numai prin API/parteneriat autorizat.

## 4. ITP — Registrul Auto Român
URL: https://prog.rarom.ro/rarpol/rarpol.asp?job=completare

- RAR permite verificarea ITP după seria CIV sau VIN.
- Pentru VIN mai scurt de 17 caractere, RAR indică folosirea seriei CIV.
- RAR precizează că baza este actualizată periodic.
- Formularul include cod de verificare; nu a fost identificată documentație API publică.

MVP:
- Cere VIN și, opțional, seria CIV.
- Deschide sursa oficială.
- Utilizatorul confirmă statusul și data expirării.
- Nu deduce durata ITP doar din anul mașinii.
- Dacă data nu este disponibilă, marchează „verificat — expirare neconfirmată”.

## 5. Rovinietă — CNAIR
Portal: https://portal.etoll.ro/
Ghid: https://portal.etoll.ro/web/guest/ghid-rovinieta

- Portalul TollRO include funcții de verificare a tichetelor de rută și informații despre perioade de valabilitate.
- Ghidul oficial precizează că informațiile sunt orientative, bazate pe datele disponibile la interogare, și afișează momentul ultimei actualizări.
- Aplicația eTarife CNAIR documentează verificarea rovinietei; unele operațiuni cer număr de înmatriculare, VIN și categoria vehiculului.
- Nu presupune existența unui API public pentru integrare.

MVP:
- Colectează numărul, VIN și categoria când sunt cerute.
- Oferă acces la sursa oficială.
- Permite introducerea datei din confirmarea de achiziție.
- Salvează sursa și data verificării.
- „Nu am găsit rezultat” nu înseamnă automat „nu există rovinietă”.

## 6. CASCO
Nu a fost identificată o bază publică națională pentru consultarea centralizată a valabilității CASCO după VIN. Este o poliță facultativă emisă de asigurători diferiți.

MVP:
- Introducere manuală sau scanare poliță.
- OCR local pentru asigurător, număr poliță, început și expirare.
- Confirmare de către utilizator.
- Integrare directă doar dacă asigurătorul oferă API și acces contractual.

## 7. De ce doar VIN nu este suficient
VIN-ul:
- identifică vehiculul și poate furniza specificații;
- poate fi criteriu de căutare RCA în AIDA;
- poate fi criteriu de căutare ITP în RAR;
- poate fi folosit împreună cu alte date la verificări de rovinietă.

VIN-ul nu conține datele de expirare, nu oferă acces la polița CASCO, nu garantează API, și nu înlocuiește numărul, seria CIV, categoria vehiculului, CAPTCHA sau consimțămintele cerute de portaluri.

## 8. Proveniența datelor
Pentru fiecare verificare salvează:
- tip și nume sursă;
- timestamp verificare;
- rezultat: valid, invalid, not_found, unavailable, inconclusive;
- date început/expirare, dacă sunt furnizate;
- confirmarea utilizatorului;
- referință minimă la rezultat, fără date personale inutile.

Stări UI:
- Confirmat de utilizator;
- Verificat la sursa oficială la data X;
- Extras din document — necesită confirmare;
- Neverificat;
- Sursa indisponibilă;
- Rezultat neconcludent.

## 9. Surse
1. NHTSA vPIC — https://vpic.nhtsa.dot.gov/api/
2. AIDA / BAAR — https://www.aida.info.ro/ro/politerca/cautare
3. RAR ITP — https://prog.rarom.ro/rarpol/rarpol.asp?job=completare
4. CNAIR TollRO — https://portal.etoll.ro/
5. CNAIR eTarife — https://play.google.com/store/apps/details?id=ro.cnair.eTarife

Interfețele se pot schimba. Înainte de implementare, verifică din nou termenii oficiali și existența unei documentații API actuale.