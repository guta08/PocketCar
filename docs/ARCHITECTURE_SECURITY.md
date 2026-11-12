# PocketCar — Architecture & Security

Versiune 0.1 · 12 noiembrie 2026

## 1. Arhitectură recomandată
Model local-first cu backend minimal pentru notificări și integrări. Datele vehiculului, mentenanța și documentele se păstrează local. Backend-ul este folosit pentru e-mail, push remote, programare/deduplicare alerte și eventual proxy pentru furnizori externi.

### Date care nu părăsesc dispozitivul
- PDF/JPEG ale documentelor;
- imagini brute, scanări, thumbnail-uri;
- conținut OCR integral;
- chei de criptare locale;
- date biometrice (aplicația nu le poate accesa).

### Date minime care pot ajunge la backend, cu acord
- e-mail;
- token push și platformă;
- identificator aleator al instalării/contului;
- tip eveniment, termen și status alertă;
- preferințe canal și lead time.

Backend-ul nu are nevoie de VIN complet, număr complet de înmatriculare sau fișiere pentru a trimite alerte generice.

## 2. Componente
- Mobile App: UI, date locale, scanner, OCR local, reguli service, client API.
- Local Database: vehicule, valabilități, service, setări.
- Encrypted Document Vault: fișiere criptate și index local.
- OS Secure Storage: protejarea cheilor.
- Notification API: înregistrare dispozitiv, preferințe și evenimente.
- Notification Worker: programare, deduplicare, retry.
- Email Provider: e-mail tranzacțional.
- Push Provider: APNs iOS și FCM Android/iOS.
- Vehicle Data Adapters: decodare VIN și verificări oficiale.

Serviciile de notificare nu primesc acces la Document Vault.

## 3. Criptare locală
- Documente în sandbox privat, nu galerie publică.
- Criptare autentificată înainte de scriere (de exemplu AES-GCM sau echivalent modern).
- Chei de date aleatorii, protejate de o cheie master.
- Cheia master protejată prin Keychain/Secure Enclave pe iOS și Android Keystore pe Android.
- Nu hardcoda chei și nu deriva cheia direct din VIN, PIN sau biometrie.
- Folosește biblioteci verificate, nu algoritmi proprii.
- Șterge cache, thumbnail-uri și fișiere temporare.
- Verifică backup-ul OS: excludere din backup necriptat sau backup criptat end-to-end opt-in.

## 4. Autentificare seif
- iOS: LocalAuthentication (Face ID/Touch ID sau passcode, în funcție de politică).
- Android: BiometricPrompt cu biometric strong și/sau credential dispozitiv.
- PIN local ca fallback, cu KDF robust, salt unic și protecție anti-brute-force.
- Timeout configurabil: imediat, 1 minut, 5 minute.
- Blocare la background și reautentificare pentru export/ștergere în masă.
- Ecranul blocat nu afișează nume sau previzualizări sensibile.

Biometria este validată de OS. Aplicația nu primește imaginea feței, amprenta sau șablonul biometric.

## 5. Push și e-mail
Push remote necesită un provider server; Apple livrează prin APNs, Android prin FCM. E-mailul necesită backend și un furnizor de e-mail. Notificările locale pot funcționa fără server, dar nu pot trimite e-mail.

Flux:
1. Aplicația cere permisiunea pentru notificări.
2. Primește token push de la OS.
3. Înregistrează token-ul și preferințele în backend prin TLS.
4. Înregistrează evenimentele permise.
5. Worker-ul calculează momentul, verifică preferințele și deduplică.
6. Trimite push și e-mail independent.
7. Salvează statusul tehnic și reîncearcă erorile temporare.

Payload generic recomandat: „Ai o valabilitate care se apropie de expirare. Deschide PocketCar.” Fără VIN, număr de înmatriculare sau detalii din document.

Câmpuri conceptuale pentru eveniment:
- event_id, installation_id, event_type;
- due_at, notify_at;
- channel, status;
- idempotency_key.

Fiabilitate:
- retry cu backoff;
- deduplicare idempotentă;
- actualizare/anulare evenimente când se schimbă data;
- gestionarea token-urilor invalide;
- fus orar explicit și testarea schimbării orei;
- separarea statusului provider de confirmarea citirii.

## 6. Scanner și OCR
- Procesare on-device.
- Android: Google ML Kit Document Scanner poate oferi detectare, captură, crop, rotire, filtre și curățare.
- iOS: scanner nativ on-device (de exemplu VisionKit) sau bibliotecă matură.
- OCR local; nu transmite documentul sau textul OCR către API extern.
- Confirmare obligatorie a câmpurilor extrase.
- Testare pentru umbre, reflexii, colțuri îndoite și documente față-verso.

## 7. Cont și backup — variante
A. Fără cont și fără sincronizare: maximă confidențialitate, dar pierderea telefonului poate însemna pierderea datelor.
B. Cont pentru notificări, fără documente: backend-ul păstrează e-mail și metadate de alerte, documentele rămân locale.
C. Backup criptat end-to-end opt-in, ulterior: fișierele sunt criptate pe telefon înainte de upload; serverul nu deține cheia. Recuperarea cheii trebuie proiectată atent.

Recomandare MVP: varianta B sau A; explică explicit consecințele pierderii dispozitivului.

## 8. Model minim de date
- Vehicle: id, vin, registration_number, make, model, model_year, fuel, engine, mileage, source_metadata.
- Validity: id, vehicle_id, type, start_date, expiry_date, issuer, reference_number, status, source, checked_at, confirmed_at.
- MaintenanceRule: id, vehicle_id, item_type, interval_months, interval_km, warning_thresholds.
- MaintenanceEvent: id, vehicle_id, item_type, performed_at, odometer_km, cost, provider, notes.
- DocumentMetadata: id, category, encrypted_file_id, mime_type, created_at, linked_vehicle_id. Local only.
- ReminderPreference: event_type, channel, lead_times, enabled.
- NotificationEvent: id, event_type, due_at, notify_at, channel, status, idempotency_key.

Fișierele efective și indexul lor sensibil rămân locale.

## 9. Threat model
- Telefon pierdut: criptare, biometric, timeout.
- Root/jailbreak: reducerea suprafeței de atac; nu promite protecție absolută.
- Backup necriptat: excludere sau E2EE opt-in.
- Loguri/telemetrie: redactare VIN, e-mail, număr și OCR.
- Backend compromis: backend-ul nu deține documente sau chei.
- Token push compromis: payload generic, revocare și autentificare API.
- OCR greșit: confirmare obligatorie.
- Sursă oficială indisponibilă: status neverificat, nu presupunere.

## 10. Teste de securitate înainte de lansare
- Documentele nu apar în galerie publică.
- Fișierele persistate sunt criptate.
- Backup-ul iCloud/Google nu include documente în clar.
- Biometria revocată și fallback-ul funcționează.
- PIN-ul nu este stocat în clar și are protecție anti-brute-force.
- Notificările nu expun date pe lock screen.
- Backend-ul nu primește imagini, PDF-uri sau OCR.
- Ștergerea elimină fișier, cache și thumbnail.