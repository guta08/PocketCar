# PocketCar — Product Requirements

Versiune 1.0 · 12 noiembrie 2026 · Decizii confirmate

## 1. Viziune și decizii ferme
PocketCar este o aplicație mobilă publică pentru administrarea unuia sau mai multor autovehicule. Reduce riscul de a uita expirări, revizii sau documente și oferă acces securizat la documentele auto/personale.

- Cont obligatoriu.
- Platforme Android și iOS.
- Mai multe vehicule per cont încă din MVP.
- Destinație: Google Play și Apple App Store.
- Push și e-mail: ambele canale suportate, configurabile independent.
- Backup E2EE, activat explicit de utilizator.
- Documentele și conținutul lor rămân locale în utilizarea normală; backup-ul opțional conține numai date criptate pe dispozitiv.

## 2. Cont și onboarding
- Cont obligatoriu înainte de utilizarea funcțiilor principale.
- Înregistrare cu e-mail, verificarea adresei, autentificare, logout, revocare sesiuni și ștergere cont.
- Recuperarea contului nu trebuie să permită automat decriptarea seifului.
- După autentificare, utilizatorul poate crea și gestiona mai multe vehicule.
- Explică separat ce date sunt locale, ce metadate ajung la server și cum funcționează backup-ul.

## 3. Garaj și adăugare vehicul
- Adăugare prin VIN introdus manual sau scanat.
- Validare VIN; suport controlat pentru vehicule mai vechi.
- Decodare printr-un furnizor configurabil.
- Date posibile: marcă, model, an model, caroserie, combustibil, cilindree, putere, transmisie, numai dacă sursa le furnizează.
- Ecran de confirmare/editare înainte de salvare.
- Număr de înmatriculare, data primei înmatriculări, kilometraj și seria CIV manual sau din scanarea talonului.
- Nu deduce proprietarul și nu accesa date personale nepublice.
- Fiecare vehicul are date, documente, mentenanță și alerte separate.

## 4. Valabilități
Tipuri: RCA, CASCO, rovinietă, ITP.

Pentru fiecare: dată început/expirare, emitent și număr opțional, notițe, document asociat, sursă, ultima verificare și status (neverificat, verificat, expirat, necunoscut, neconcludent).
- Datele manuale pot fi introduse/modificate de utilizator.
- OCR și rezultatele externe sunt propuneri până la confirmare.
- Nu suprascrie o valoare confirmată fără prezentarea diferenței și accept.
- Păstrează istoricul local.
- CASCO se introduce din poliță/OCR sau integrare autorizată; nu presupune registru public centralizat.
- „Nu am găsit rezultat” nu înseamnă automat „nu există poliță”.

## 5. Mentenanță
Operațiuni: ulei și filtre, lichid frână, antigel, bujii, distribuție, frâne, anvelope, baterie, revizie generală și operațiuni personalizate.
Fiecare regulă suportă interval km, interval luni, ultima dată/km, praguri de avertizare, cost, service, notițe și factură scanată.
- Declanșator „oricare dintre”: ex. 10.000 km SAU 12 luni.
- Dacă kilometrajul nu este actualizat, scadența în km este estimativă/necunoscută.
- Înregistrarea unei operațiuni generează următorul termen.
- Istoricul nu se șterge la schimbarea intervalului viitor.

## 6. Seif privat
Categorii auto (RCA, CASCO, ITP, rovinietă, talon, CIV, facturi, devize), personale (permis, CI, pașaport) și personalizate.
- Stocare în sandbox privat; fără upload în clar.
- Criptare autentificată înainte de scriere și înainte de backup.
- Chei locale protejate de Keychain/Secure Enclave pe iOS și Android Keystore pe Android.
- Backup E2EE opțional; serverul stochează numai ciphertext.
- Acces Face ID/Touch ID/biometrie Android și PIN fallback.
- Blocare automată la background și timeout.
- Fără conținut sensibil în notificări, loguri sau analytics.
- Exclude fișierele/cheile din backup OS necriptat.
- Ștergerea elimină fișierul criptat, thumbnail-uri și temporare; politica pentru backup este explicată.
- Export numai la acțiunea explicită, după reautentificare.
Aplicația nu accesează sau stochează șabloane biometrice.

## 7. Scanare și OCR
- Scanare document, nu simplă fotografie: detectare margini, captură auto/manuală, corecție perspectivă, crop, rotire, filtre și export JPEG/PDF.
- Procesare și OCR on-device.
- OCR poate propune tip document, VIN, număr, emitent și expirare.
- Confirmare obligatorie; marchează valorile incerte.
- Șterge fotografia brută după procesare dacă utilizatorul nu cere păstrarea.

## 8. Notificări
Push pe telefon și e-mail sunt ambele obligatorii ca funcționalități, configurabile separat per utilizator și tip de eveniment.
- Praguri inițiale: 30, 14, 7, 3, 1 zi și ziua expirării; configurabile.
- Canalele sunt independente; eroarea unuia nu anulează celălalt.
- Deduplicare, retry și anulare/actualizare la schimbarea termenului.
- Payload generic, fără VIN, număr sau conținut document.
- Backend necesar pentru push remote și e-mail când aplicația este închisă.
- „Trimis de provider” nu înseamnă „citit”.

## 9. Backup și sincronizare
- Backup E2EE opțional și transparent.
- Criptare pe dispozitiv înainte de transmitere; serverul nu deține cheia de decriptare.
- Definește format versionat, recovery secret, rotație, revocare, retenție și ștergere.
- Pierderea cheii/recovery secret poate face backup-ul imposibil de recuperat; avertizare și test de recovery.
- Sincronizarea metadatelor este separată de backup-ul seifului.
- Conflictele multi-device nu se rezolvă prin suprascriere silențioasă.
- Fără copii necriptate în iCloud/Google sau servicii terțe.

## 10. Dashboard
- Selector și rezumat global/per vehicul.
- Următoarea expirare, status documente, următoarea operațiune, kilometraj și ultima actualizare.
- Acțiuni rapide: document, operațiune, kilometraj.
- Prioritate: expirate, următoarele 7 zile, 30 zile, mentenanță.
- Datele lipsă apar ca „neverificat/date incomplete”, nu „totul este în regulă”.

## 11. Non-funcționale
Offline pentru date locale; TLS; minimizare date backend; fără upload în clar al documentelor/OCR; localizare; toleranță la indisponibilitatea registrelor; export/ștergere; revocare dispozitive; accesibilitate; performanță; conformitate GDPR și cerințe store.

## 12. Acceptanță MVP
- [ ] Cont, verificare e-mail, autentificare, logout, revocare și ștergere.
- [ ] Mai multe vehicule, cu date izolate.
- [ ] VIN decode și confirmare/editare.
- [ ] RCA/CASCO/rovinietă/ITP per vehicul.
- [ ] Proveniență și timestamp verificare.
- [ ] Mentenanță pe timp/km și istoric.
- [ ] Scanner cu crop/perspectivă și OCR local.
- [ ] Vault criptat local, biometric și PIN.
- [ ] Backup E2EE cu restore testat.
- [ ] Push și e-mail testate independent pe ambele platforme.
- [ ] Alerte deduplicate, fără date sensibile.
- [ ] Funcționare offline, export și ștergere date.
- [ ] Testare fizică Android/iOS și pregătire publicare.

## 13. În afara MVP
Plăți/subscription, OBD-II, telematică, profil familie cu roluri avansate, import automat tranzacții, istoric daune și marketplace service.
