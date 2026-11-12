# PocketCar — Specificație finală de implementare pentru Qoder

Versiune 1.1 · 12 noiembrie 2026
Repository documentație: https://github.com/guta08/PocketCar

## 1. Ierarhia documentelor

În caz de suprapunere, respectă în această ordine:
1. Deciziile ferme și cerințele de produs din PRODUCT_REQUIREMENTS.md.
2. Cerințele de securitate din ARCHITECTURE_SECURITY.md.
3. Instrucțiunile detaliate de execuție și acceptanță din EXECUTION_PLAYBOOK.md.
4. Acest document ca rezumat al contractului MVP.
5. ROADMAP.md și IMPLEMENTATION_STATUS.md pentru ordine și progres.

Citește README.md și toate fișierele din docs/ înainte de prima fază. Nu ignora documentele pentru că ai primit o comandă scurtă.

## 2. Mod de lucru — fără ping-pong

Utilizatorul va transmite doar comanda „Începe Faza N”. La primirea ei:
- verifică IMPLEMENTATION_STATUS.md, git status, branch-ul și commit-urile;
- verifică dependențele și criteriile fazei din EXECUTION_PLAYBOOK.md;
- execută integral faza fără aprobări intermediare pentru alegeri tehnice uzuale;
- ia decizii tehnice rezonabile, documentate, compatibile cu cerințele;
- nu cere utilizatorului să aleagă framework, biblioteci sau detalii care pot fi decise prin analiză;
- dacă lipsesc credențiale externe, implementează adaptorul și modul de test/local, continuă activitățile independente și raportează exact ce configurare este necesară;
- rulează testele, repară problemele introduse și verifică acceptanța;
- actualizează IMPLEMENTATION_STATUS.md și documentele afectate;
- creează commit și prezintă raportul standard;
- se oprește la final și așteaptă comanda fazei următoare.

Solicită intervenția utilizatorului numai pentru secrete/conturi externe care necesită autorizare, costuri/contracte, decizii de produs noi, acțiuni ireversibile ori un blocaj real de securitate. Nu opri întreaga fază pentru un serviciu opțional indisponibil.

## 3. Decizii de produs ferme

- Cont obligatoriu, e-mail verificat, sesiuni securizate.
- Android și iOS.
- Multiple vehicule per cont din MVP.
- Aplicație destinată publicării publice.
- Push și e-mail ambele suportate și configurabile independent.
- Backup E2EE opțional, activat explicit de utilizator.
- Documentele rămân locale în utilizarea normală; niciun fișier, imagine, thumbnail sau OCR în clar către backend.
- VIN/OCR/surse externe sunt sugestii până la confirmare.
- Interfață MVP în română, arhitectură pregătită pentru localizare.
- Fără abonamente, reclame, analytics sau trackere fără aprobare.
- Fără publicare în magazine fără aprobare explicită.

## 4. Domeniul MVP

1. Cont: înregistrare, verificare e-mail, login/logout, resetare parolă, revocare sesiuni și ștergere cont.
2. Garaj: CRUD pentru mai multe vehicule, VIN decode prin adaptor, confirmare/editare date.
3. Valabilități: RCA, CASCO, rovinietă, ITP per vehicul, status și proveniență.
4. Mentenanță: operațiuni, intervale luni/km cu primul prag atins, istoric și costuri.
5. Seif: scanare documente, OCR local, criptare locală, biometrie și PIN fallback.
6. Notificări: push APNs/FCM și e-mail, configurabile independent, deduplicare și retry.
7. Backup: E2EE, recovery secret separat de parola contului, restore pe dispozitiv nou.
8. Offline: acces la datele locale și sincronizare controlată.
9. Privacy: export, ștergere, revocare dispozitive și zero date sensibile în loguri/payload-uri.

Detaliile complete și criteriile de acceptanță se află în EXECUTION_PLAYBOOK.md și PRODUCT_REQUIREMENTS.md.

## 5. Arhitectură și securitate

Alege stack-ul după auditul repository-ului și justifică-l. Prioritizează cross-platform, dar folosește API-uri native mature pentru biometrie, secure storage, scanner și push. Separă UI, domeniu, persistence, vault/crypto, API, notificări și adaptoare externe.

Folosește:
- TLS, autentificare și autorizare server-side per account;
- migrații și scheme versionate;
- stocare securizată OS pentru secrete;
- criptare autentificată prin biblioteci verificate, fără criptografie proprie;
- E2EE real pentru backup, criptat înainte de upload;
- payload-uri de notificare generice;
- loguri redactate și minimizarea datelor backend.

Nu trimite documente/OCR în clar, nu pune secrete în Git și nu folosi parola contului drept unic secret E2EE.

## 6. Surse auto din România

Citește ROMANIA_DATA_SOURCES.md. Nu presupune că VIN-ul oferă automat RCA, CASCO, ITP sau rovinietă. MVP folosește fluxuri oficiale asistate și confirmare manuală dacă nu există API public autorizat. Interzis CAPTCHA bypass, scraping agresiv sau API privat neautorizat.

## 7. Faze obligatorii

Urmează exact scope-ul, dependențele și criteriile din EXECUTION_PLAYBOOK.md:
- Faza 0 — audit și plan tehnic.
- Faza 1 — fundație, interfață și cont.
- Faza 2 — garaj multi-vehicul și VIN.
- Faza 3 — valabilități și mentenanță.
- Faza 4 — seif, scanner și OCR local.
- Faza 5 — push și e-mail.
- Faza 6 — backup E2EE și sincronizare.
- Faza 7 — integrare, QA și prima versiune funcțională.

Nu începe automat faza următoare. Nu declara completă o fază fără teste concrete.

## 8. Raportare și status

La finalul fiecărei faze, actualizează docs/IMPLEMENTATION_STATUS.md, documentele tehnice afectate și commit-ul. Raportul trebuie să includă: status, module/fișiere, decizii, teste exacte, matrice acceptanță, verificări securitate, riscuri/blockers, credențiale necesare, commit SHA și comanda următoare.

După raport, oprește-te. Utilizatorul nu trebuie să copieze rapoarte sau să coordoneze subtask-uri; Qoder citește progresul direct din repository.
