# PocketCar — Roadmap

Versiune 1.1 · 12 noiembrie 2026

## Decizii ferme
| Domeniu | Decizie |
|---|---|
| Cont | Obligatoriu |
| Platforme | Android și iOS |
| Vehicule | Multiple din MVP |
| Destinație | Aplicație publică |
| Notificări | Push și e-mail, independente |
| Backup | E2EE, opțional |
| Documente | Locale, criptate, fără upload în clar |
| VIN/OCR | Confirmare utilizator |

## Hosting și livrare

- Dezvoltare/testare: PC Windows cu Qoder în workspace local.
- Versionare: GitHub.
- Hosting backend: TrueNAS în calea stabilită de proprietar, /mnt/Pool1/Stocare/PocketCar, după auditul de capacitate și securitate.
- Actualizare TrueNAS: numai versiuni validate, versionate și aprobate explicit; datele persistente sunt separate de cod.
- Detalii și criterii: [DEPLOYMENT_ARCHITECTURE.md](DEPLOYMENT_ARCHITECTURE.md).

## Plan până la prima versiune funcțională
Execuția completă, cu task-uri și criterii de acceptanță, este în [EXECUTION_PLAYBOOK.md](EXECUTION_PLAYBOOK.md).

| Fază | Livrabil |
|---|---|
| 0 | Audit repository, stack, arhitectură, model date, threat model |
| 1 | Fundație cross-platform, infrastructură UI de bază, backend și conturi |
| 1.5 | Implementare UI/UX completă conform designului aprobat | 1 |
| 2 | Garaj multi-vehicul, VIN și dashboard funcțional |
| 3 | RCA/CASCO/rovinietă/ITP și mentenanță |
| 4 | Seif criptat, scanner și OCR local |
| 5 | Push și e-mail reale |
| 6 | Backup E2EE și sync multi-device |
| 7 | Integrare, QA, release candidate internă — prima versiune funcțională |

Verificările oficiale AIDA/RAR/CNAIR rămân asistate dacă nu există API autorizat; nu blochează MVP-ul.

## Reguli de execuție
- Qoder execută integral faza solicitată și nu cere aprobări intermediare pentru decizii tehnice uzuale.
- La final testează, repară, actualizează IMPLEMENTATION_STATUS.md, face commit și prezintă raport.
- Faza 1.5 este obligatorie după Faza 1 și înainte de Faza 2. Qoder se oprește după fiecare fază și așteaptă comanda „Începe Faza N”.
- Nu începe automat următoarea fază.
- Nu compromite securitatea pentru a marca o fază ca terminată.
- Nu publica în magazine fără aprobare explicită.

Vezi [QODER_COMMANDS.md](QODER_COMMANDS.md) pentru comenzile de copiat.
