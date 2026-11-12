# PocketCar — Architecture & Security

Versiune 1.0 · 12 noiembrie 2026

## 1. Principii
Arhitectură local-first cu backend minimal. Contul este obligatoriu, dar nu presupune stocarea documentelor în clar. Datele și documentele sunt disponibile local/offline. Backend-ul gestionează identitatea, metadatele minime de notificare, sincronizarea selectivă și ciphertext-ul backup-ului E2EE.

**Regulă critică:** documentele, imaginile brute, OCR-ul și cheile de decriptare nu ajung niciodată în clar la backend.

## 2. Componente
- Mobile App iOS/Android: UI, onboarding, garaj multi-vehicul, reguli, scanner, API client.
- Local Database și Encrypted Document Vault.
- OS Secure Storage: Keychain/Secure Enclave și Android Keystore.
- Identity API: cont, verificare e-mail, sesiuni, revocare.
- Sync API: sincronizare selectivă și conflict handling.
- Backup API/Object Storage: exclusiv ciphertext E2EE.
- Notification API/Worker: evenimente, preferințe, deduplicare, retry.
- Email Provider și Push Provider (APNs/FCM).
- Vehicle Data Adapters pentru VIN și verificări oficiale asistate.

## 3. Clasificarea datelor
Local/E2EE: documente, scanări, thumbnail-uri, OCR integral, metadate sensibile, chei și recovery secret. Backup-ul poate încărca numai ciphertext.
Backend minim: e-mail cont, identificator cont/dispozitiv, token push, platformă, preferințe notificări, evenimente cu tip generic și termen, metadate tehnice backup și ciphertext.
Nu trimite VIN complet, număr complet, documente, imagini sau OCR în clar către backend-ul de notificări.

## 4. Autentificare cont
- Cont obligatoriu, e-mail verificat.
- Parole gestionate standard și hash adaptiv server-side (Argon2id sau echivalent), niciodată în clar.
- Token-uri scurte, refresh token rotativ în storage securizat.
- Rate limiting, protecție brute-force, verificare e-mail și resetare sigură.
- Logout, revocare sesiuni/dispozitive și ștergere cont.
- Resetarea parolei contului nu decriptează vault-ul.
- Identitatea contului este separată de identitatea criptografică a seifului.

## 5. Criptare locală și backup E2EE
- Criptare autentificată modernă (AES-256-GCM sau ChaCha20-Poly1305) prin biblioteci verificate.
- Chei aleatorii și envelope encryption; cheile locale protejate de Keychain/Secure Enclave și Android Keystore.
- Criptare pe dispozitiv înainte de backup; serverul primește ciphertext.
- Cheia E2EE nu este transmisă în clar.
- Recovery controlat de utilizator (recovery key sau passphrase cu KDF puternic și parametri versionați); parola contului nu este secretul unic.
- Serverul poate stoca un key envelope criptat, niciodată cheia utilizabilă în clar.
- Avertizare clară: pierderea recovery secret poate face backup-ul nerecuperabil.
- Teste backup/restore pe dispozitiv nou, cheie greșită, ciphertext corupt, upgrade, revocare și ștergere.
- Nu crea algoritmi proprii; revizuire de securitate înainte de release.

## 6. Seif și biometrie
- iOS LocalAuthentication; Android BiometricPrompt cu biometric strong și/sau credential dispozitiv.
- PIN fallback cu KDF robust, salt unic și anti-brute-force.
- Timeout imediat/1/5 minute; blocare la background.
- Reautentificare pentru export și ștergere în masă.
- Fără previzualizări sensibile pe ecran blocat.
OS-ul validează biometria; aplicația nu primește imaginea feței, amprenta sau șablonul biometric.

## 7. Push și e-mail
- Push iOS APNs; Android FCM; e-mail prin provider tranzacțional.
- Canale independente, configurabile separat.
- Worker cu programare, deduplicare idempotentă, retry/backoff și anulare la schimbarea datelor.
- Token push stocat securizat și revocat când este posibil.
- Payload generic: „Ai o valabilitate care se apropie de expirare. Deschide PocketCar.”
- Fără VIN, număr, emitent sau detalii document.
- Status provider != livrare garantată/citire.
- Notificările locale sunt fallback; e-mail și push remote necesită backend.

## 8. Model de date conceptual
- Account: id, email, email_verified_at, created_at, status.
- Device: id, account_id, platform, push_token, last_seen_at, revoked_at.
- Vehicle: id, account_id, VIN/registration local sau criptat, make, model, year, fuel, engine, mileage, source_metadata.
- Validity: id, vehicle_id, type, start_date, expiry_date, issuer/reference local, status, source, checked_at, confirmed_at.
- MaintenanceRule: id, vehicle_id, item_type, interval_months, interval_km, warning_thresholds.
- MaintenanceEvent: id, vehicle_id, item_type, performed_at, odometer_km, cost, provider, notes.
- DocumentMetadata: id, category, encrypted_file_id, mime_type, created_at, linked_vehicle_id; local/E2EE.
- ReminderPreference: event_type, channel, lead_times, enabled.
- NotificationEvent: id, account_id, event_type, due_at, notify_at, channel, status, idempotency_key.
- BackupManifest: id, account_id, ciphertext_version, ciphertext_size, created_at, device_id.

Minimizează câmpurile sincronizate. VIN, număr, CIV, documente și OCR rămân locale sau E2EE dacă sunt incluse explicit în backup.

## 9. Sincronizare multi-device
- Nu sincroniza date sensibile în clar.
- Versionare/revision și updated_at pentru modificări.
- Conflictele se prezintă utilizatorului; fără suprascriere silențioasă.
- Tombstones pentru ștergeri și retenție documentată.
- Restaurarea vault-ului E2EE este separată de sync-ul metadatelor.

## 10. Scanner și OCR
Procesare on-device. Android: ML Kit Document Scanner sau alternativă verificată. iOS: VisionKit/scanner nativ. OCR local, confirmare obligatorie și testare pentru umbre, reflexii, colțuri îndoite și documente față-verso.

## 11. Threat model
Telefon pierdut: criptare, biometrie, timeout, revocare. Root/jailbreak: reducerea suprafeței de atac, fără promisiuni absolute. Backup compromis: ciphertext E2EE. Backend compromis: fără documente/chei în clar. Token push compromis: payload generic. Loguri: redactare VIN/e-mail/număr/OCR/token. OCR greșit: confirmare. Sursă oficială indisponibilă: neverificat, nu presupunere. Recovery secret pierdut: avertizare că restore poate fi imposibil.

## 12. Checklist securitate
- [ ] Fișierele nu apar în galerie publică și sunt criptate pe disc.
- [ ] Object storage conține exclusiv ciphertext.
- [ ] Backup OS nu include fișiere/chei în clar.
- [ ] Restore E2EE funcționează numai cu secretul corect.
- [ ] Biometrie revocată și PIN fallback testate.
- [ ] PIN-ul nu este stocat în clar și are anti-brute-force.
- [ ] Notificări fără date sensibile.
- [ ] Backend-ul nu primește imagini/PDF/OCR.
- [ ] Ștergerea elimină cache, thumbnail și aplică politica backup.
- [ ] Logout, revocare dispozitiv și ștergere cont testate.
- [ ] Review OWASP MASVS și testare API înainte de lansare.
