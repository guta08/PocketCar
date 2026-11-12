# PocketCar

PocketCar este o aplicație mobilă pentru administrarea unei mașini: valabilități, mentenanță, notificări și seif privat pentru documente.

## Obiectiv
Adăugare rapidă prin VIN, confirmarea datelor, urmărirea RCA/CASCO/rovinietă/ITP, mentenanță după timp și kilometri, scanare documente și alerte push/e-mail.

## Principii
1. Documentele sunt stocate exclusiv local și criptat.
2. VIN-ul precompletează date tehnice, dar nu garantează accesul la toate valabilitățile.
3. Datele din VIN/OCR/surse externe necesită confirmarea utilizatorului.
4. Backend-ul pentru notificări nu primește documente, imagini sau text OCR.
5. Biometria este gestionată de sistemul de operare; aplicația nu accesează amprente sau date Face ID.

## Documentație
- [Product Requirements](docs/PRODUCT_REQUIREMENTS.md)
- [Architecture & Security](docs/ARCHITECTURE_SECURITY.md)
- [Romania Data Sources](docs/ROMANIA_DATA_SOURCES.md)
- [Roadmap & Decisions](docs/ROADMAP.md)

Documentație inițială — noiembrie 2026. Implementarea se va realiza separat.