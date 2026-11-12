# PocketCar — Roadmap & Decisions

Versiune 0.1 · 12 noiembrie 2026

## 1. Strategie
Construiește în pași verticali utilizabili. Nu începe cu automatizarea tuturor registrelor: riscul principal este accesul/API-ul, nu interfața.

## 2. Faze

### Faza 0 — Validare și fundație
- Confirmare platforme/framework.
- Decizie cont și backup.
- Model date local.
- Threat model și criptare.
- Verificare licențe/termeni pentru scanner, OCR și decoder VIN.
- Navigație și design system.
Rezultat: specificație tehnică și backlog aprobate.

### Faza 1 — Garaj și valabilități
- Adăugare VIN și decodare prin adaptor.
- Confirmare/editare date vehicul.
- Număr, CIV și kilometraj.
- CRUD RCA, CASCO, rovinietă, ITP.
- Dashboard expirări și proveniență.
Rezultat: produs util fără integrarea automată a registrelor.

### Faza 2 — Service
- Catalog operațiuni.
- Reguli după timp și km.
- Istoric, kilometraj, costuri și service.
- Alerte locale.
Rezultat: evidență mentenanță.

### Faza 3 — Seif securizat
- Scanner on-device și OCR local.
- Vault criptat.
- Biometrie și PIN fallback.
- Asociere documente cu vehicul/valabilitate/service.
- Teste securitate și backup.
Rezultat: documente private și locale.

### Faza 4 — Notificări cloud
- Backend minimal.
- E-mail și token push.
- Worker, programare și deduplicare.
- APNs/FCM și provider e-mail.
- Preferințe și teste.
Rezultat: push și e-mail independente de rularea aplicației.

### Faza 5 — Verificări oficiale asistate
- Fluxuri ghidate AIDA, RAR și CNAIR.
- Confirmare manuală rezultat.
- Istoric sursă și moment.
- Evaluare API/parteneriate oficiale.
Rezultat: verificări mai rapide fără scraping neautorizat.

### Faza 6 — Extensii
Cont și sincronizare metadate, backup E2EE opt-in, profil familie, costuri totale, alimentări, anvelope, istoric daune, widget-uri, OBD-II.

## 3. Riscuri
| Risc | Impact | Măsură |
|---|---|---|
| Lipsă API public pentru registre | Automatizare completă imposibilă | Flux asistat/manual |
| Decoder VIN incomplet în UE | Date incomplete | Multi-provider, confirmare |
| Pierdere telefon | Pierdere documente | Backup criptat opt-in, avertizare |
| Backend expune date | Risc de confidențialitate | Payload generic și minimizare |
| Push întârziat | Alertă ratată | E-mail ca al doilea canal |
| OCR greșit | Termen incorect | Confirmare obligatorie |
| Backup necriptat | Încălcarea local-only | Excludere sau E2EE |
| Schimbare formular oficial | Flux asistat afectat | Fallback manual și link |

## 4. Decizii recomandate
1. Documente locale și criptate, fără upload.
2. Biometrie prin API OS și PIN fallback.
3. VIN decoder configurabil; NHTSA doar ca sursă inițială de test, nu garanție pentru Europa.
4. Valabilități manual/OCR confirmat + verificare oficială asistată.
5. Backend minimal pentru push/e-mail, fără documente.
6. Mentenanță cu declanșator „oricare dintre km sau timp”.
7. Scanner și OCR on-device.
8. Modelul suportă mai multe mașini chiar dacă MVP începe cu una.

## 5. Întrebări deschise
- Cont obligatoriu sau opțional?
- Dacă telefonul se pierde, accepți pierderea documentelor sau vrei backup E2EE?
- PIN separat de cel al telefonului sau credential OS?
- Alerte pentru toate evenimentele sau configurabile per categorie?
- Aplicație personală sau publicare în magazine?
- Mai multe mașini din prima versiune?

## 6. Instrucțiuni pentru agentul de implementare
- Citește toate documentele din docs înainte de implementare.
- Nu presupune API public doar pentru că există formular web.
- Nu implementa scraping/CAPTCHA bypass.
- Nu trimite documente, imagini sau OCR în backend.
- Nu considera VIN/OCR confirmat fără utilizator.
- Verifică permisiuni, colectare date, licență, cost și procesare locală pentru orice SDK.
- Implementează pe faze și cere validare la finalul fiecărei faze.
- Orice abatere de la local-only necesită aprobare explicită.