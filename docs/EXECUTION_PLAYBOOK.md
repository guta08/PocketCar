# PocketCar — Execution Playbook pentru Qoder

Versiune 1.0 · 12 noiembrie 2026
Scop: instrucțiuni complete de execuție pentru toate fazele până la prima versiune funcțională end-to-end.

## 0. Cum se folosește acest playbook

Acest document, împreună cu PRODUCT_REQUIREMENTS.md, ARCHITECTURE_SECURITY.md, ROMANIA_DATA_SOURCES.md și QODER_IMPLEMENTATION_SPEC.md, este contractul de execuție.

**Regula de operare:** utilizatorul îi spune lui Qoder doar „Începe Faza N”. Qoder execută integral acea fază fără să ceară confirmări intermediare pentru activități deja specificate. La final:
1. rulează toate testele aplicabile;
2. verifică criteriile de acceptanță;
3. repară problemele introduse de fază;
4. actualizează documentația și jurnalul de progres;
5. produce raportul de fază;
6. se oprește și așteaptă comanda pentru faza următoare.

Nu întreba utilizatorul să aleagă framework-uri, biblioteci sau detalii tehnice care pot fi decise prin analiză competentă. Alege soluția matură, compatibilă cu cerințele, justific-o în raport și documenteaz-o. Cere intervenția utilizatorului numai pentru: credențiale/secrete care nu există, conturi sau servicii externe ce necesită autorizare, costuri/contracte, decizii de produs care nu sunt documentate, riscuri ireversibile ori imposibilități reale. Dacă lipsește o cheie/API, construiește un adaptor cu configurare prin environment variables și mock/local mode, documentează exact ce lipsește și continuă restul fazei.

## 1. Decizii fixe

- Produs public, Android și iOS.
- Cont obligatoriu, autentificare cu e-mail verificat.
- Mai multe vehicule per cont din MVP.
- Limba MVP: română; arhitectură localizabilă.
- Push și e-mail ambele suportate, preferințe independente.
- Date locale/offline pentru funcțiile principale.
- Seif local criptat; documentele și OCR nu sunt încărcate în clar.
- Backup E2EE opțional, cu recovery secret separat de parola contului.
- VIN/OCR/surse oficiale furnizează sugestii, utilizatorul confirmă.
- Nu se implementează scraping neautorizat, CAPTCHA bypass sau API-uri private.
- Nu se adaugă reclame, abonamente, analytics sau trackere fără aprobare.
- Nu se publică în magazine fără aprobare explicită.

## 2. Reguli tehnice transversale

### Calitatea implementării
- Inspectează înainte de modificare; păstrează ce este bun și evită rescrieri inutile.
- Folosește arhitectură modulară, tipuri stricte unde stack-ul permite, separare UI/domain/data.
- Nu lăsa ecrane mock sau butoane decorative în fluxurile declarate funcționale.
- Nu marca funcționalități ca terminate dacă folosesc doar date hardcodate, mock sau TODO.
- Mock-urile sunt permise doar în teste și demo mode clar etichetat.
- Adaugă teste unitare, integrare și UI relevante în aceeași fază.
- Păstrează build-ul funcțional după fiecare grup logic de modificări.
- Nu introduce secrete în cod, Git, fixtures, capturi sau loguri.
- Gestionează erorile, loading, empty states, offline și retry.
- UI coerent, accesibil, responsive, cu stări vizibile și mesaje în română.

### Securitate și confidențialitate
- Nu loga parole, token-uri, VIN complet, numere de înmatriculare, OCR sau conținut document.
- Nu transmite fișiere, imagini, thumbnails sau OCR în clar către backend.
- Folosește storage securizat al sistemului de operare pentru secrete.
- Criptografia trebuie să folosească biblioteci mature; nu implementa primitive proprii.
- Nu pretinde că un test static demonstrează securitatea; testează stocarea și traficul.
- Toate endpoint-urile verifică autentificarea și autorizarea pe account_id.
- Protejează API-urile de brute-force, replay, rate abuse și acces cross-account.
- Notificările au conținut generic.

### Autonomie
- Nu aștepta feedback după fiecare fișier sau subtask.
- Dacă există mai multe soluții acceptabile, alege una și consemnează motivul.
- Dacă o integrare externă nu este disponibilă, implementează interfața, fallback și teste; nu inventa rezultate.
- Nu bloca întreaga fază din cauza unei integrări opționale indisponibile.
- Dacă apare o problemă care compromite securitatea sau face imposibilă cerința de bază, oprește doar acea componentă, izoleaz-o și explică blocajul; continuă activitățile independente.

## 3. Definiția primei versiuni funcționale

Prima versiune funcțională PocketCar înseamnă un MVP end-to-end care permite unui utilizator:
1. să creeze cont, să confirme e-mailul și să se autentifice;
2. să adauge și să administreze mai multe vehicule;
3. să urmărească RCA, CASCO, rovinietă și ITP;
4. să înregistreze mentenanța după timp și kilometri;
5. să scaneze și să păstreze documente în seiful local criptat;
6. să primească notificări push și e-mail;
7. să activeze backup E2EE și să restaureze pe un dispozitiv nou;
8. să utilizeze datele locale offline;
9. să exporte/șteargă datele și să șteargă contul;
10. să ruleze pe dispozitive reale Android și iOS.

Verificările oficiale românești pot rămâne fluxuri asistate dacă nu există API oficial autorizat. Nu condiționa MVP de integrarea automată a registrelor.

## 4. Faze și dependențe

| Fază | Rezultat | Depinde de |
|---|---|---|
| 0 | Audit tehnic și plan de execuție | — |
| 1 | Fundație, design system, backend dev și conturi | 0 |
| 2 | Garaj multi-vehicul și VIN | 1 |
| 3 | Valabilități și mentenanță | 2 |
| 4 | Seif, scanner și OCR local | 1, 2 |
| 5 | Push și e-mail reale | 1, 3 |
| 6 | Backup E2EE și sincronizare minimă | 1, 4 |
| 7 | Integrare, QA și prima versiune funcțională | 1–6 |

Fazele 3 și 4 pot fi dezvoltate secvențial de același agent; nu sări peste dependențe. Dacă repository-ul existent are deja componente valide, auditează-le și reutilizează-le.

## 5. Faza 0 — Audit și plan tehnic

### Obiectiv
Înțelege repository-ul și pregătește planul executabil. Nu modifica încă funcționalitățile aplicației.

### Activități
1. Inspectează toate fișierele, branch-ul, commit-urile, framework-ul, configurațiile, build-ul și testele.
2. Rulează build/test existente și consemnează starea inițială.
3. Identifică ce există deja și ce lipsește.
4. Evaluează framework-ul cross-platform și justifică alegerea pe criterii: mentenanță, acces la API-uri native, securitate, push, scanner, biometric, ecosistem și cost.
5. Definește arhitectura mobile/backend/database/object storage.
6. Definește schema de date, tenant isolation și modelul multi-device.
7. Proiectează fluxurile de autentificare, seif, chei, recovery și backup E2EE.
8. Definește integrarea APNs, FCM și provider e-mail prin adaptoare.
9. Definește CI, medii dev/staging/prod, secrets și strategie de testare.
10. Identifică serviciile externe care necesită conturi/chei și oferă alternative/mock mode.
11. Actualizează documentele tehnice cu deciziile rezultate.

### Acceptanță
- raport inventar repository și baseline build/test;
- stack și arhitectură justificate;
- diagrame/descriere module și fluxuri de date;
- model de date și autorizare multi-tenant;
- threat model și plan crypto/recovery;
- plan CI/testare și lista de secrete externe;
- listă de riscuri și decizii asumate.
Nu cere aprobarea planului; faza 1 va urma planul ales, în limitele deciziilor ferme.

## 6. Faza 1 — Fundație, interfață și cont

### Obiectiv
Aplicație cross-platform pornibilă, design system și backend de identitate funcțional.

### Activități mobile
- Configurează proiectul, environment dev, lint/format, test runner și build Android/iOS.
- Creează navigația de bază: onboarding, autentificare, garaj (placeholder funcțional), setări și profil.
- Construiește design system: culori, tipografie, spacing, butoane, inputuri, carduri, dialoguri, empty/loading/error states.
- Interfață română, responsive, accesibilă.
- Creează client API tipizat, gestionare sesiune, retry controlat și erori.
- Persistă local setările non-sensibile și cache-ul necesar.

### Activități backend
- Configurează backend și database cu migrații.
- Înregistrare, verificare e-mail, login, refresh token, logout, revocare sesiuni.
- Hash parole cu Argon2id sau echivalent adecvat.
- Rate limiting, validare input, protecție brute-force și resetare sigură.
- Endpoint account profile și account deletion (cu cleanup).
- Teste de autorizare și izolare cont.

### Acceptanță
- Build debug pe Android și iOS.
- Flux real de înregistrare/verificare/login/logout.
- Sesiunea persistă în siguranță și poate fi revocată.
- Contul A nu poate accesa datele contului B.
- Ecrane cu stări loading/error/offline.
- CI rulează lint, teste și build.
- Nu există secrete în repository.

## 7. Faza 2 — Garaj multi-vehicul și VIN

### Obiectiv
Utilizatorul poate administra mai multe mașini, cu date izolate.

### Activități
- Model Vehicle și migrații: id, account_id, VIN local/criptat, registration local, make, model, year, fuel, engine, mileage, created/updated, source metadata.
- CRUD vehicul: adaugă, editează, arhivează, șterge.
- VIN introdus manual și scanat cu camera (dacă scannerul generic este disponibil).
- Validare VIN cu mesaje clare pentru format invalid/vehicul vechi.
- Creează adaptor decodor VIN provider-independent; NHTSA vPIC doar ca sursă posibil incompletă pentru Europa.
- Nu expune date neconfirmate: ecran de revizuire câmp cu câmp și salvare confirmată.
- Câmpuri manuale pentru număr, CIV, prima înmatriculare și kilometraj.
- Selector vehicul, dashboard global și dashboard per vehicul.
- Izolare completă între vehicule la toate query-urile și documentele asociate.
- Offline CRUD cu persistență locală și sincronizare conform arhitecturii stabilite.

### Acceptanță
- Minimum două vehicule într-un cont.
- CRUD complet și validare.
- Teste pentru schimbarea vehiculului activ și izolarea datelor.
- Decodare VIN prin adaptor sau fallback manual funcțional.
- Câmpurile returnate de provider sunt afișate cu sursă și pot fi corectate.
- Niciun VIN nu este prezent în loguri sau analytics.

## 8. Faza 3 — Valabilități și mentenanță

### Obiectiv
Funcționalitate reală de urmărire a expirărilor și operațiunilor service.

### Valabilități
- CRUD RCA, CASCO, rovinietă, ITP per vehicul.
- Date început/expirare, emitent, referință, notițe, document link.
- Proveniență: manual/OCR/verificare oficială/API; checked_at și confirmed_at.
- Status neverificat/verificat/expirat/necunoscut/neconcludent.
- Istoric modificări și protecție la suprascrierea datelor confirmate.
- Calcul zile rămase în timezone local; date limită și schimbare fus orar testate.
- Dashboard pentru expirate, 7 zile, 30 zile.

### Mentenanță
- Catalog operațiuni inițiale din PRODUCT_REQUIREMENTS.
- Reguli interval luni și/sau kilometri, trigger OR (primul prag atins).
- Înregistrare operațiune: dată, kilometraj, cost, service, notițe, atașament.
- Calcul următoarei scadențe și istoric.
- Kilometraj învechit => avertizare că scadența km este estimativă.
- Operațiuni custom și interval editabil fără ștergerea istoricului.

### Acceptanță
- CRUD și istoric pentru toate cele patru tipuri de valabilități.
- Teste de calendar, leap year, timezone, date expirate și limite.
- Teste intervale km/luni și declanșator primul prag.
- Datele sunt separate per vehicul și disponibile offline.
- Stări incomplete/neverificate nu sunt afișate ca valide.

## 9. Faza 4 — Seif, scanner și OCR local

### Obiectiv
Documente scanate, criptate și accesibile numai după autentificarea seifului.

### Activități
- Implementează scanner nativ/matur: iOS VisionKit sau echivalent; Android ML Kit Document Scanner sau echivalent compatibil.
- Detectare margini, auto capture, crop, perspectivă, rotire, filtre, multipage și export PDF/JPEG.
- OCR on-device; niciun serviciu OCR cloud.
- Ecran de confirmare pentru tip, VIN, emitent, număr, dată și expirare; marchează încrederea OCR.
- Creează vault în sandbox privat, criptare autentificată și envelope encryption.
- Chei locale protejate cu Keychain/Secure Enclave și Android Keystore.
- Integrează biometrie OS și PIN fallback cu protecție anti-brute-force.
- Auto-lock la background și timeout; reautentificare la export.
- Documente asociabile vehiculului, valabilității și operațiunii service.
- Thumbnail-urile și temporarele trebuie criptate sau gestionate fără leak.
- Exclude vault-ul și cheile din backup OS necriptat.
- Ștergere document: fișier, cache, thumbnail și temporare.
- Export explicit după reautentificare.

### Acceptanță
- Scanare multi-page pe ambele platforme.
- OCR local și confirmare manuală.
- Fișierele persistate sunt criptate; aplicația nu le expune în galerie publică.
- Biometrie și PIN fallback testate pe dispozitive fizice.
- Auto-lock și export protejat.
- Verificare trafic: zero fișiere/OCR în clar trimise către server.
- Teste ștergere și cleanup.

## 10. Faza 5 — Push și e-mail

### Obiectiv
Notificări remote reale, programate de backend și configurabile independent.

### Activități
- Configurează provider e-mail tranzacțional prin adaptor; template-uri română, verificare adresă și unsubscribe/preferences.
- Configurează FCM Android și APNs iOS.
- Device registry securizat, înregistrare/rotire/revocare token.
- UI preferințe: push și e-mail separat, tip eveniment, lead times.
- Backend worker pentru notificări: 30/14/7/3/1 zile și ziua expirării, configurabil.
- Deduplicare cu idempotency key, retry/backoff, dead-letter/error status și anulare la editare/ștergere.
- Push/email independent; eșecul unui canal nu anulează celălalt.
- Payload generic fără VIN, număr, emitent sau conținut document.
- Local notifications fallback.
- Instrumente de test pentru trimitere staging fără expunere date reale.

### Acceptanță
- Push testat pe Android și iOS fizice.
- E-mail real testat cu provider staging/production aprobat.
- Preferințele fiecărui canal funcționează separat.
- Modificarea sau ștergerea expirării actualizează notificările.
- Deduplicare și retry testate.
- Lock-screen payload nu expune informații personale.
- Erorile providerului sunt vizibile în loguri redactate și dashboard tehnic.

## 11. Faza 6 — Backup E2EE și sync multi-device

### Obiectiv
Backup criptat end-to-end restaurabil, fără ca serverul să poată citi conținutul.

### Activități
- Definit format backup versionat, manifest, encryption envelope și migrare.
- Criptează pe dispozitiv datele incluse și documentele înainte de upload.
- Recovery key sau passphrase separat de parola contului; KDF puternic și parametri versionați.
- UX activare backup, afișare și confirmare recovery secret, avertizare pierdere.
- Backend/object storage stochează ciphertext și metadate strict necesare.
- Restore pe dispozitiv nou cu autentificare cont + recovery secret.
- Revocare dispozitiv, rotație/rekey când necesar, ștergere backup și retenție.
- Sync metadate separat de vault; conflicte explicite și tombstones.
- Nu permite serverului să decripteze backup-ul.
- Dacă se sincronizează date sensibile, acestea sunt E2EE; nu lărgi setul de date fără justificare.

### Acceptanță
- Backup creat și încărcat; verificare că obiectul server-side este ciphertext.
- Restore complet pe instalare/dispozitiv nou.
- Secret greșit, ciphertext corupt și versiune incompatibilă tratate sigur.
- Teste de revocare și ștergere.
- Parola contului singură nu poate decripta backup-ul.
- Backup OS nu expune fișierele sau cheile.
- Recovery flow testat de la cap la cap.

## 12. Faza 7 — Integrare, QA și prima versiune funcțională

### Obiectiv
Livrare MVP coerent, stabil, utilizabil de la onboarding până la backup/restore.

### Activități
1. Integrează toate fluxurile: cont → garaj → valabilități/service → documente → notificări → backup.
2. Testează scenariul utilizator nou și utilizator cu mai multe vehicule.
3. Testează offline, reconectare, conflict multi-device și schimbarea dispozitivului.
4. Testează permisiuni refuzate, token push invalid, provider e-mail indisponibil și API VIN indisponibil.
5. Testează ștergere vehicul, document, cont și revocare device.
6. Verifică accesibilitate, ecrane mici/mari, orientare și localizare date.
7. Audit privacy: network inspection, logs, crash reports, analytics SDK și backup OS.
8. Audit autorizare backend și cross-account access.
9. Rulează unit, integration, UI, static analysis, dependency/license scan și build release.
10. Testează pe cel puțin un dispozitiv fizic Android și unul iOS.
11. Scrie ghid de instalare, configurare environment, deployment backend, backup/restore și troubleshooting.
12. Pregătește release candidate internă; nu publica în store.

### Acceptanță finală — MVP funcțional
- Toate criteriile din PRODUCT_REQUIREMENTS.md sunt îndeplinite sau excepțiile sunt explicit documentate.
- Android și iOS build release candidate.
- Cont și mai multe vehicule funcționale.
- Valabilități și mentenanță funcționale offline.
- Seif criptat, scanare și OCR local funcționale.
- Push și e-mail funcționale independent.
- Backup E2EE restore testat pe dispozitiv separat.
- Zero documente/OCR în clar în traficul către backend.
- Ștergere cont/date și revocare device funcționale.
- Nicio problemă blocker/critical deschisă.
- Raport de testare și known issues publicate.

## 13. Raportul obligatoriu la finalul fiecărei faze

Folosește exact structura:
1. Faza și obiectivul.
2. Rezumat executiv: completă/parțială/blocată.
3. Module și fișiere create/modificate.
4. Decizii tehnice și justificări.
5. Teste executate: comandă, rezultat, număr teste.
6. Matrice de acceptanță: pass/fail/not applicable.
7. Verificări securitate/privacy.
8. Probleme rămase, riscuri și datorie tehnică.
9. Configurări/credențiale externe care lipsesc și instrucțiuni pentru obținere.
10. Commit SHA și branch.
11. Confirmare că nu ai început faza următoare.
12. Comanda exactă recomandată pentru următoarea fază.

## 14. Actualizarea documentației și progresului

La finalul fiecărei faze:
- actualizează docs/IMPLEMENTATION_STATUS.md cu status, commit, teste, decizii, blockers și următoarea fază;
- actualizează documentele de arhitectură dacă implementarea a ales detalii concrete;
- păstrează istoricul deciziilor și nu rescrie cerințe de produs fără motiv;
- adaugă instrucțiuni de configurare/deploy când apar servicii noi;
- commit separat, cu mesaj descriptiv, pentru fază;
- nu modifica statusul la „complet” dacă testele nu au fost rulate.

## 15. Regula de oprire

După raportul complet, oprește-te. Nu porni automat următoarea fază și nu cere utilizatorului să reanalizeze planul. Așteaptă doar comanda scurtă „Începe Faza N”.
