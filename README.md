# PocketCar

PocketCar este o aplicație mobilă publică pentru administrarea unuia sau mai multor autovehicule: valabilități, mentenanță, notificări și seif privat pentru documente.

## Decizii confirmate
- Cont obligatoriu.
- Android și iOS.
- Mai multe vehicule per cont încă din MVP.
- Publicare în Google Play și Apple App Store.
- Push și e-mail, ambele suportate și configurabile independent.
- Backup criptat end-to-end, activat explicit de utilizator.
- Documentele rămân locale în utilizarea normală; backup-ul opțional conține numai ciphertext.

## Principii obligatorii
1. Local-first: datele și documentele sunt disponibile local/offline.
2. Documentele nu sunt încărcate niciodată în clar în backend.
3. Backup-ul este criptat pe dispozitiv înainte de upload; serverul nu deține cheia.
4. Contul obligatoriu susține identitatea, notificările și backup-ul, dar nu deblochează automat seiful.
5. VIN/OCR/surse externe sunt date propuse până la confirmarea utilizatorului.
6. Biometria este gestionată de sistemul de operare.
7. Backend-ul primește numai metadatele minime necesare alertelor.

## Documentație
- [Product Requirements](docs/PRODUCT_REQUIREMENTS.md)
- [Architecture & Security](docs/ARCHITECTURE_SECURITY.md)
- [Romania Data Sources](docs/ROMANIA_DATA_SOURCES.md)
- [Roadmap & Decisions](docs/ROADMAP.md)
- [Final Qoder Implementation Specification](docs/QODER_IMPLEMENTATION_SPEC.md)

Documentația este sursa de adevăr pentru implementarea realizată de Qoder.
