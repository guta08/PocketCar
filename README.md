# PocketCar

Aplicație mobilă publică pentru administrarea unuia sau mai multor autovehicule: valabilități, mentenanță, notificări și seif privat pentru documente.

## Decizii confirmate
- Cont obligatoriu; Android și iOS.
- Mai multe vehicule per cont din MVP.
- Publicare planificată în Google Play și Apple App Store.
- Push și e-mail, configurabile independent.
- Backup E2EE opțional; documentele rămân locale în utilizarea normală.
- Niciun document, imagine sau OCR nu se transmite în clar către backend.

## Documentație — sursa de adevăr
- [Product Requirements](docs/PRODUCT_REQUIREMENTS.md)
- [Architecture & Security](docs/ARCHITECTURE_SECURITY.md)
- [Deployment Architecture — Windows + TrueNAS](docs/DEPLOYMENT_ARCHITECTURE.md)
- [Romania Data Sources](docs/ROMANIA_DATA_SOURCES.md)
- [Roadmap](docs/ROADMAP.md)
- [Final Qoder Implementation Specification](docs/QODER_IMPLEMENTATION_SPEC.md)
- [Execution Playbook — instrucțiuni complete Faza 0–7](docs/EXECUTION_PLAYBOOK.md)
- [Qoder Commands — comenzi scurte](docs/QODER_COMMANDS.md)
- [Implementation Status — progresul proiectului](docs/IMPLEMENTATION_STATUS.md)

## Design PocketCar

- [Design Specification](docs/DESIGN_SPECIFICATION.md) — direcția vizuală aprobată și cerințele de implementare.
- [Concept Board Light/Dark (SVG)](assets/design/pocketcar-concept-board.svg) — planșă vizuală scalabilă pentru referință.

## Cum se lucrează

1. Repository-ul se clonează pe PC-ul Windows; Qoder folosește workspace-ul local **This computer**.
2. PC-ul este mediul principal de dezvoltare și testare.
3. GitHub este sursa oficială de cod și documentație.
4. TrueNAS găzduiește serviciile backend PocketCar; nu este workspace-ul principal de dezvoltare.
5. Qoder urmează [Deployment Architecture](docs/DEPLOYMENT_ARCHITECTURE.md) și comenzile din [QODER_COMMANDS.md](docs/QODER_COMMANDS.md).
6. Pe TrueNAS se publică numai versiuni validate și aprobate explicit. Datele persistente sunt separate de cod și nu se suprascriu la deploy.

Qoder execută fiecare fază integral, testează, actualizează documentația și se oprește la final. Nu începe faza următoare și nu face deploy în producție fără aprobarea utilizatorului.
