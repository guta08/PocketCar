# PocketCar — Specificație finală de implementare pentru Qoder

Versiune 1.0 · 12 noiembrie 2026
Statut: aprobată pentru începerea implementării MVP
Repo documentație: https://github.com/guta08/PocketCar

## 1. Rolul agentului și regula de lucru

Ești agentul tehnic care va analiza repository-ul de cod și va implementa PocketCar. Documentația din acest repository este contractul de produs și securitate. Nu presupune cerințe care nu sunt scrise aici.

Înainte de orice modificare:
1. Citește README.md și toate documentele din docs/.
2. Inspectează repository-ul de cod existent, identifică framework-ul, structura, starea build-ului și testele.
3. Prezintă un audit inițial: ce există, ce lipsește, riscuri, propunere tehnică și fazele de lucru.
4. Nu începe implementarea până când utilizatorul aprobă planul tehnic și eventualele alegeri care nu sunt deja fixate.

Lucrează incremental. La finalul fiecărei faze oprește-te, rulează testele, prezintă fișierele schimbate, rezultatele, problemele rămase și cere aprobare explicită pentru continuare. Nu implementa fazele următoare în avans.

## 2. Decizii de produs care nu se negociază

- Cont obligatoriu.
- Android și iOS.
- Mai multe vehicule per cont încă din MVP.
- Aplicație destinată publicării în Google Play și Apple App Store.
- Push și e-mail sunt ambele canale suportate și configurabile independent.
- Backup E2EE opțional, activat explicit de utilizator.
- Documentele rămân locale în utilizarea normală. Niciun document, imagine sau OCR nu poate fi transmis în clar la backend.
- Datele din VIN, OCR sau surse externe sunt propuneri și necesită confirmarea utilizatorului.
- Interfețele și documentația MVP sunt în limba română; arhitectura trebuie să permită localizare ulterioară.
- Nu introduce abonamente, reclame, analytics sau trackere fără aprobare.

## 3. Produsul MVP

### 3.1 Cont și securitate cont
- Înregistrare cu e-mail, verificare e-mail, login, logout și sesiuni securizate.
- Resetarea parolei contului nu deblochează seiful și nu recuperează cheia E2EE.
- Revocare dispozitive/sesiuni și ștergere cont.
- Ecrane clare pentru consimțământ, notificări, backup și prelucrarea datelor.
- Nu salva parole în clar. Folosește mecanisme standard, hashing adaptiv server-side și rate limiting.

### 3.2 Garaj multi-vehicul
- Creare, editare, arhivare și ștergere vehicul.
- Adăugare prin VIN tastat sau scanat.
- Decodare prin adaptor configurabil; niciun provider nu trebuie legat direct de UI.
- Afișează câmpurile returnate de sursă și permite confirmare/editare.
- Număr înmatriculare, CIV, kilometraj și date lipsă pot fi introduse manual.
- Dashboard global și dashboard pentru vehiculul selectat.
- Izolare strictă: nu amesteca niciodată datele, documentele sau alertele între vehicule.

### 3.3 Valabilități
RCA, CASCO, rovinietă și ITP, fiecare asociată unui vehicul. Include date început/expirare, emitent, referință opțională, notițe, document atașat, proveniență, timestamp verificare și status.
Permite introducere manuală. OCR și verificările oficiale doar propun date. Utilizatorul confirmă. Nu trata „not found” ca dovadă de lipsă a poliței.

### 3.4 Mentenanță
Catalog inițial: ulei, filtre, lichide, bujii, distribuție, frâne, anvelope, baterie, revizie și operațiuni custom.
Regulile suportă interval în luni și/sau km, cu declanșatorul primul termen atins. Păstrează istoricul, kilometrajul, costul, furnizorul și notițele. Dacă kilometrajul nu este actualizat, marchează calculul ca estimativ.

### 3.5 Seif documente
- Categorii auto, personale și custom.
- Scanare reală de document: detectare margini, crop, perspectivă, rotire, îmbunătățire și PDF/JPEG.
- OCR local, opțional; confirmare obligatorie a câmpurilor.
- Fișierele sunt criptate înainte de scriere și păstrate în sandbox privat.
- Biometrie OS și PIN fallback; auto-lock și reautentificare pentru export.
- Niciun upload de fișier, imagine, OCR sau thumbnail în clar.
- Șterge temporarele și thumbnail-urile la ștergerea documentului.

### 3.6 Notificări
- Push: APNs pentru iOS și FCM pentru Android.
- E-mail prin provider tranzacțional.
- Ambele canale disponibile, independente și configurabile.
- Praguri inițiale 30/14/7/3/1 zile și în ziua expirării; configurabile.
- Backend worker cu deduplicare, retry/backoff, idempotency și anularea evenimentelor învechite.
- Payload generic, fără date auto sau documente.
- Notificările locale sunt fallback, nu substitut pentru e-mail.

### 3.7 Backup E2EE
- Dezactivat până la activarea explicită și parcurgerea explicației de recovery.
- Criptează pe dispozitiv înainte de upload.
- Backend/object storage stochează numai ciphertext și metadate tehnice minime.
- Cheia de decriptare nu ajunge la server în clar.
- Recovery key/passphrase separat de parola contului; KDF robust și parametri versionați.
- Implementare backup/restore, revocare dispozitiv și ștergere.
- Testează restaurarea pe dispozitiv nou și scenariile de pierdere/corupere a cheii.
- Dacă proiectarea sigură E2EE nu poate fi finalizată, nu livra un backup slab sau în clar; oprește-te și raportează blocajul.

## 4. Arhitectură și cerințe tehnice

Alege framework-ul după auditul repository-ului și cerințele native. Prioritizează o bază de cod comună, dar folosește API-uri native pentru biometrie, secure storage, scanner și push când sunt necesare. Justifică alegerea înainte de implementare.

Separă cel puțin:
- UI/presentation;
- domeniu și reguli de business;
- persistence locală;
- vault/crypto/key management;
- API client și autentificare;
- notificări;
- adaptoare VIN și surse oficiale;
- scanner/OCR.

Folosește scheme versionate pentru baza de date locală și backup. Include migrații. Folosește TLS, validare server-side, rate limiting și control acces per account. Nu pune secrete în repository, aplicație sau loguri.

Criptografie: folosește biblioteci verificate și primitive moderne de criptare autentificată, nu implementa algoritmi proprii. Cheile locale trebuie protejate prin iOS Keychain/Secure Enclave și Android Keystore. Solicită revizuire de securitate înainte de release.

## 5. Integrarea registrelor din România

Citește docs/ROMANIA_DATA_SOURCES.md. VIN-ul nu este o cheie universală pentru toate datele.
- NHTSA vPIC este sursă de decodare orientativă, posibil incompletă pentru UE.
- AIDA/BAAR, RAR și CNAIR au formulare/fluxuri oficiale; nu presupune API public documentat.
- CASCO se gestionează din poliță sau integrare contractuală.
- MVP: verificare ghidată, utilizatorul introduce și confirmă rezultatul.
- Interzis: CAPTCHA bypass, scraping agresiv, API privat neautorizat sau automatizare care încalcă termenii.
- Salvează sursa și timestamp-ul; păstrează stări „neverificat”, „indisponibil” și „neconcludent”.

## 6. Faze de implementare și livrabile

### Faza 0 — Audit și plan tehnic
Livrabile: raport repository, stack propus, arhitectură, model de date, threat model, plan de testare, riscuri și estimare relativă. Oprește-te pentru aprobare.

### Faza 1 — Fundație, cont și garaj
Livrabile: schelet aplicație cross-platform, navigație, design system, autentificare, verificare e-mail, sesiuni, CRUD multi-vehicul, persistență locală și teste. Oprește-te pentru aprobare.

### Faza 2 — Valabilități și mentenanță
Livrabile: CRUD expirări, proveniență, dashboard, reguli service timp/km, istoric, kilometraj și teste automate de calcul. Oprește-te pentru aprobare.

### Faza 3 — Vault și scanner
Livrabile: scanner, OCR local, criptare fișiere, biometrie/PIN, lock timeout, export/ștergere și teste care demonstrează că datele nu pleacă în clar. Oprește-te pentru aprobare.

### Faza 4 — Backend și notificări
Livrabile: API, device registry, APNs/FCM, provider e-mail, preferințe, worker, deduplicare, retry, anulare și teste pe dispozitive fizice iOS/Android. Oprește-te pentru aprobare.

### Faza 5 — Backup E2EE
Livrabile: format versionat, criptare locală, upload ciphertext, recovery, restore, revocare și teste de securitate. Oprește-te pentru aprobare.

### Faza 6 — Surse oficiale asistate
Livrabile: linkuri/fluxuri ghidate, confirmare manuală, provenance și fallback. Oprește-te pentru aprobare.

### Faza 7 — Pregătire lansare
Livrabile: privacy policy, terms, GDPR flows, account deletion, store disclosures, accesibilitate, crash/performance, testare securitate, beta și checklist publicare. Nu publica fără aprobare explicită.

## 7. Testare obligatorie
- Unit tests pentru date, scadențe, intervale și fus orar.
- Integration tests pentru cont, sync, notificări și backup.
- Teste multi-vehicul și autorizare per cont.
- Teste offline/online și conflict multi-device.
- Teste scanner/OCR cu documente reale de test, fără date personale reale în fixtures.
- Verifică stocarea criptată și traficul de rețea: zero documente/OCR în clar către backend.
- Teste PIN brute-force, biometrie revocată, logout, revocare dispozitiv și ștergere cont.
- Teste push/e-mail independente pe iOS și Android.
- Teste backup/restore, cheie greșită și ciphertext corupt.
- Nu declara o fază completă fără rezultate de test concrete.

## 8. Formatul raportului de fază
1. Obiectiv și scope.
2. Fișiere/module create sau modificate.
3. Decizii tehnice și justificare.
4. Teste executate și rezultate exacte.
5. Cerințe îndeplinite/neîndeplinite.
6. Riscuri, probleme și datorie tehnică.
7. Capturi/evidențe utile.
8. Confirmare explicită că nu ai început faza următoare.

## 9. Prima instrucțiune executabilă

Începe exclusiv cu Faza 0. Citește documentația, inspectează repository-ul aplicației și returnează raportul de audit și planul tehnic. Nu modifica încă fișierele aplicației și nu începe Faza 1 până când utilizatorul aprobă raportul.
