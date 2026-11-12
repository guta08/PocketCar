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
- [Romania Data Sources](docs/ROMANIA_DATA_SOURCES.md)
- [Roadmap](docs/ROADMAP.md)
- [Final Qoder Implementation Specification](docs/QODER_IMPLEMENTATION_SPEC.md)
- [Execution Playbook — instrucțiuni complete Faza 0–7](docs/EXECUTION_PLAYBOOK.md)
- [Qoder Commands — comenzi scurte](docs/QODER_COMMANDS.md)
- [Implementation Status — progresul proiectului](docs/IMPLEMENTATION_STATUS.md)

## Cum se lucrează
1. Deschide Qoder în repository-ul de cod PocketCar.
2. Dă comanda din [QODER_COMMANDS.md](docs/QODER_COMMANDS.md) pentru Faza 0.
3. După raport, folosește aceeași comandă schimbând numărul fazei.
4. Qoder citește progresul din IMPLEMENTATION_STATUS.md; nu trebuie să copiezi rapoarte între agenți.

Qoder execută fiecare fază integral, testează, actualizează documentația și se oprește la final. Nu începe faza următoare fără comanda utilizatorului.
