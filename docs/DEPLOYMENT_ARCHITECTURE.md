# PocketCar — Deployment Architecture: Windows Development + TrueNAS Hosting

Versiune 1.0 · 12 noiembrie 2026

## 1. Decizia arhitecturală

Modelul oficial de lucru pentru PocketCar este:

- **PC Windows = mediu principal de dezvoltare și testare.** Qoder modifică proiectul dintr-un checkout Git local. Build-urile, testele și verificările se execută pe PC.
- **GitHub = sursa oficială de cod și documentație.** Orice schimbare validată este versionată și publicată în repository.
- **TrueNAS = mediu de găzduire pentru serviciile server-side PocketCar.** Versiunea activă se actualizează numai printr-un deployment controlat al unei versiuni validate.
- **Datele persistente sunt separate de cod și de versiunile aplicației.** Un deploy sau rollback nu trebuie să suprascrie baza de date, documentele ori backupurile.
- **Qoder nu editează direct versiunea activă de pe TrueNAS și nu face deploy automat în producție.**

Această decizie înlocuiește orice interpretare conform căreia Qoder ar trebui să lucreze permanent direct în folderul TrueNAS.

## 2. Ce înseamnă „PocketCar găzduit pe TrueNAS”

Aplicația mobilă Android/iOS este instalată pe telefoanele utilizatorilor prin distribuția convenită (testare internă, apoi magazine). TrueNAS găzduiește componentele backend necesare aplicației:

- API/backend PocketCar;
- PostgreSQL;
- worker-e pentru notificări și sarcini programate;
- endpoint-uri pentru autentificare, sincronizare și backup E2EE;
- object/file storage numai pentru conținut criptat end-to-end, dacă arhitectura finală îl cere;
- reverse proxy și terminare TLS;
- monitorizare, loguri redactate și mecanisme de backup.

Nu se presupune că TrueNAS găzduiește direct aplicația mobilă sau că utilizatorii accesează interfața de dezvoltare.

## 3. Fluxul de dezvoltare și livrare

### Flux normal

1. Qoder lucrează în repository-ul local de pe PC Windows.
2. Qoder rulează formatter, lint, teste unitare, teste de integrare și build-urile aplicabile.
3. Qoder verifică criteriile de acceptanță și securitate ale fazei.
4. Modificările validate sunt comise în Git și împinse în GitHub.
5. Se identifică un commit/versiune exactă, nu se deployează cod neversionat.
6. Se construiește un artefact/container versionat și reproductibil.
7. Se publică mai întâi într-un mediu de staging/test pe TrueNAS, dacă acesta este disponibil și separat.
8. Se execută health checks, smoke tests, verificări de migrare și teste de integrare.
9. Deploy-ul în instanța activă se face numai după validarea rezultatului și aprobarea explicită a proprietarului proiectului.
10. Se păstrează versiunea precedentă și procedura de rollback.

### Reguli obligatorii

- Nu sincroniza automat folderul de lucru Qoder peste directorul activ.
- Nu folosi tag-ul latest ca singur identificator al versiunii în producție; fixează tag și digest de imagine.
- Nu rula deploy la fiecare salvare sau commit.
- Nu modifica manual fișiere în containerul activ.
- Nu șterge volume/datasets persistente la upgrade.
- Nu executa migrații destructive fără backup verificat și strategie de rollback.
- Nicio comandă de deploy, restart sau migrare în producție fără aprobare explicită.
- Qoder poate pregăti scripturi și pipeline-uri de deploy, dar nu primește automat acces la secretele sau serviciile de producție.

## 4. Organizarea TrueNAS

Calea de bază solicitată de proprietar:

/mnt/Pool1/Stocare/PocketCar

Calea trebuie verificată în TrueNAS; numele pool-ului și dataset-urilor sunt case-sensitive. Nu presupune că o cale introdusă de utilizator este deja montată sau disponibilă în container.

Structură logică recomandată:

~~~text
/mnt/Pool1/Stocare/PocketCar/
├── app/
│   ├── deployment/       # compose/configuration versionate, fără secrete
│   ├── releases/         # manifest și identificatori de release
│   └── staging/          # numai dacă există mediu separat
├── data/
│   ├── postgres/         # date persistente PostgreSQL
│   ├── object-storage/   # numai ciphertext E2EE
│   └── uploads-tmp/      # temporare, curățate automat
├── backups/
│   ├── database/
│   ├── configuration/
│   └── manifests/
└── logs/
~~~

Recomandarea pentru implementarea reală este să se creeze **datasets ZFS separate** pentru datele bazei de date, object storage și backupuri, dacă structura și permisiunile TrueNAS permit. Structura de mai sus este o convenție logică, nu o instrucțiune de a crea toate directoarele direct în root-ul unui singur dataset.

### Permisiuni și mounts

- Creează un utilizator/grup dedicat serviciilor PocketCar, cu UID/GID documentate.
- Acordă acces numai la directoarele necesare fiecărui container.
- Nu rula containerele ca root dacă imaginea permite utilizator non-root.
- Nu monta întregul pool în container; montează numai dataset-urile necesare.
- Nu expune dataset-urile prin SMB/NFS către utilizatorii aplicației mobile.
- Configurațiile fără secrete pot fi versionate; parolele, cheile, token-urile și certificatele private se păstrează în secrets/environment securizat, în afara Git.
- Verifică ACL-urile TrueNAS și comportamentul UID/GID înainte de primul deployment.

## 5. Runtime și containere

Qoder trebuie să inspecteze versiunea TrueNAS instalată și mecanismul Apps/Docker disponibil înainte de a alege implementarea. Preferă Custom App / Docker Compose sau mecanismul suportat oficial de versiunea instalată.

Componentele potențiale (decizia finală aparține Fazei 0, după auditul stack-ului):

| Serviciu | Cerință |
|---|---|
| pocketcar-api | Backend API stateless, container versionat |
| pocketcar-worker | Job-uri programate și notificări |
| postgres | Bază de date persistentă, backup și restore testate |
| reverse-proxy | HTTPS, rutare, limitare și security headers |
| object storage | Numai dacă este necesar; stochează exclusiv ciphertext E2EE |
| monitoring | Health checks, resurse, disponibilitate și loguri redactate |

Nu instala pachete de dezvoltare direct în sistemul de bază TrueNAS și nu activa Developer Mode doar pentru a transforma NAS-ul într-o stație de dezvoltare. Nu modifica manual sistemul de operare TrueNAS pentru a instala runtime-uri. Folosește containere și mecanismele de administrare suportate.

## 6. Rețea, acces și expunere publică

PocketCar este destinat unei aplicații publice. Prin urmare, backend-ul trebuie să fie accesibil în siguranță de pe internet, nu doar din LAN sau Tailscale.

Înainte de producție, verifică și documentează:
- dacă conexiunea ISP permite conexiuni inbound și dacă există CGNAT;
- adresă IP publică/statică sau mecanism DDNS adecvat;
- domeniu DNS și certificate TLS valide, cu reînnoire automată;
- porturile strict necesare expuse către reverse proxy;
- firewall și izolarea serviciilor interne;
- faptul că PostgreSQL, Docker API, TrueNAS UI, SSH și porturile interne nu sunt expuse public;
- protecție rate-limit, autentificare, actualizări și monitorizare;
- acces administrativ prin LAN/Tailscale/VPN, nu prin endpoint-uri publice de administrare.

Nu presupune că un port forward este suficient sau sigur. Dacă ISP/CGNAT, disponibilitatea conexiunii ori riscul de securitate fac hosting-ul public nepotrivit, documentează alternativele (tunel securizat/reverse tunnel sau VPS pentru API), păstrând TrueNAS pentru date/backup numai dacă este justificat și securizat. Nu activa un tunel sau serviciu terț fără aprobare.

## 7. Capacitate și disponibilitate

TrueNAS-ul proprietarului are resurse limitate (Dell Inspiron i5-7200U, 8 GB RAM) și găzduiește deja alte servicii. Tratează hosting-ul PocketCar ca pe o decizie ce necesită măsurători, nu ca pe o capacitate garantată.

Înainte de go-live:
- măsoară RAM/CPU/disk I/O și spațiul disponibil în condiții normale și de vârf;
- stabilește limite CPU/RAM pentru fiecare container;
- verifică impactul asupra serviciilor existente;
- definește praguri de alertare pentru RAM, disk, pool health și temperatură;
- testează restart/recovery după reboot și căderea containerelor;
- verifică UPS, alimentarea și conectivitatea internet;
- stabilește RPO/RTO realiste și documentează că un server găzduit acasă poate avea întreruperi.

Dacă testele indică resurse insuficiente sau disponibilitate neadecvată pentru o aplicație publică, recomandă mutarea API-ului și a bazei de date pe un VPS/serviciu gestionat. Nu reduce securitatea sau integritatea datelor pentru a forța hosting-ul pe hardware insuficient. Proprietarul decide schimbarea destinației.

## 8. Date, confidențialitate și backup

Se aplică integral ARCHITECTURE_SECURITY.md:
- documentele, imaginile, OCR-ul integral și cheile nu ajung în clar pe backend;
- backup-ul E2EE este criptat pe dispozitiv înainte de upload;
- backend/object storage poate vedea doar ciphertext și metadate strict necesare;
- datele bazei de date sunt persistente și independente de lifecycle-ul containerului;
- notificările nu includ VIN, număr de înmatriculare sau detalii de document.

### Politica de backup

- Backup PostgreSQL consistent, programat și cu retenție documentată.
- Backup pentru configurațiile necesare restaurării, fără secrete în clar.
- Backup pentru ciphertext-ul E2EE și manifestele necesare, dacă acestea sunt stocate pe TrueNAS.
- Copie suplimentară în afara aceluiași pool și, pentru protecție la pierderea NAS-ului/locației, copie off-site criptată.
- Nu considera snapshot-ul ZFS singur drept backup complet.
- Monitorizează rezultatul backupurilor și spațiul disponibil.
- Execută periodic teste de restaurare într-un mediu izolat și documentează rezultatele.
- Definește retenția și ștergerea datelor conform cerințelor legale și politicii produsului.
- Backupurile nu trebuie să fie accesibile utilizatorilor aplicației sau containerelor fără motiv.

## 9. Migrații, upgrade și rollback

Fiecare release trebuie să aibă:
- tag/versiune semantică și commit SHA;
- imagine container fixată prin tag și digest;
- release notes și lista migrărilor;
- compatibilitate declarată cu versiunea bazei de date;
- backup pre-deploy și rezultat verificat;
- health check și smoke test;
- instrucțiuni de rollback.

Folosește migrații backward-compatible (expand/contract) când este posibil:
1. adaugă schema compatibilă cu versiunea curentă;
2. deploy cod compatibil cu schema veche și nouă;
3. migrează datele controlat;
4. elimină structuri vechi numai într-un release ulterior, după validare.

Rollback-ul aplicației nu inversează automat migrațiile de date. Pentru migrații incompatibile, rollback-ul poate necesita restaurare DB din backup; explică impactul și cere aprobare înainte.

## 10. Medii

Ținta conceptuală:
- **Development:** PC Windows, date sintetice, servicii locale/containerizate.
- **Staging:** instanță separată pe TrueNAS dacă resursele permit; dataset, DB, secrete și domeniu separate. Nu folosi date personale reale.
- **Production:** instanța activă PocketCar pe TrueNAS, numai după checklist-ul de go-live și aprobarea proprietarului.

Dacă TrueNAS nu poate susține staging și production izolate, nu simula izolarea doar prin nume de containere. Folosește medii locale pe PC pentru testare și păstrează pe NAS doar instanța activă. Nu rula testele distructive pe datele de producție.

## 11. Checklist obligatoriu înainte de primul deployment

- [ ] Faza 0 a verificat TrueNAS version, Apps/Docker runtime și capacitatea.
- [ ] Calea /mnt/Pool1/Stocare/PocketCar a fost verificată pe host.
- [ ] Dataset-urile și permisiunile sunt documentate.
- [ ] Niciun secret nu este în Git sau în imagine.
- [ ] Persistența DB este separată de container.
- [ ] Backup și restore DB au fost testate.
- [ ] TLS, DNS, firewall și acces public au fost verificate.
- [ ] Porturile administrative și DB nu sunt publice.
- [ ] Resource limits și health checks sunt configurate.
- [ ] Logurile sunt redactate.
- [ ] Testele de securitate și autorizare au trecut.
- [ ] Există versiune anterioară și procedură rollback.
- [ ] Proprietarul a aprobat explicit deploy-ul și expunerea publică.

## 12. Instrucțiuni obligatorii pentru Qoder

Qoder trebuie să trateze acest document ca decizie de produs/infrastructură și să îl citească înainte de orice implementare backend/deploy.

### În Faza 0
1. Auditează repo și stack-ul existent.
2. Documentează compatibilitatea reală cu TrueNAS instalat; nu presupune acces SSH, Docker Compose sau căi montate.
3. Evaluează dacă hardware-ul și conectivitatea susțin backend public.
4. Propune servicii/containerizare, resource limits, volume, datasets, permisiuni, DNS/TLS, backup/restore, monitoring și rollback.
5. Definește separarea development/staging/production.
6. Listează exact acțiunile manuale pe care proprietarul trebuie să le execute în TrueNAS.
7. Nu modifica TrueNAS și nu porni deploy în această fază.

### În fazele 1–7
- Dezvoltă și testează pe PC; nu folosi TrueNAS ca workspace de dezvoltare principal.
- Livrează backend-ul containerizabil și portabil, fără dependențe de căi locale hardcodate.
- Folosește environment variables/secrets pentru configurare.
- Adaugă health/readiness checks, graceful shutdown, loguri structurate și redactate, migrații și backup hooks unde sunt relevante.
- Menține datele persistente separate de cod.
- Actualizează documentația de deploy când se schimbă arhitectura.
- Nu face deploy în production, nu expune porturi și nu schimbă configurația TrueNAS fără instrucțiuni și aprobare explicită.
- Dacă accesul la TrueNAS nu este disponibil, continuă dezvoltarea cu mediu local și furnizează instrucțiuni exacte pentru deployment ulterior.
- Nu inventa că un deploy sau test pe TrueNAS a fost efectuat dacă nu ai dovezi.

### Comanda standard de deploy (numai după aprobare)
Qoder va pregăti un plan de deployment care specifică:
1. commit SHA și release tag;
2. imagini și digest-uri;
3. servicii care se schimbă;
4. migrații și impact;
5. backup necesar și verificarea lui;
6. comenzi exacte;
7. health checks și teste;
8. procedura de rollback;
9. riscuri și întrerupere estimată.

Așteaptă aprobarea înainte de execuția pe instanța activă.

## 13. Limitări și decizii deschise

Următoarele nu sunt presupuneri și trebuie verificate în Faza 0:
- versiunea și configurația exactă TrueNAS, Apps și runtime Docker disponibile;
- spațiul liber și starea pool-ului;
- permisiuni și ACL pentru dataset;
- IP public/CGNAT, port forwarding și DDNS;
- domeniul și providerul DNS;
- TLS/reverse proxy;
- UPS și disponibilitatea conexiunii;
- capacitatea reală pentru numărul de utilizatori estimat;
- dacă staging și production pot fi izolate pe același NAS.

Proprietarul a stabilit locația dorită: Pool1/Stocare/PocketCar. Qoder nu trebuie să ceară din nou această informație; trebuie să verifice calea și să raporteze orice diferență tehnică.
