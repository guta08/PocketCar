# PocketCar — Roadmap & Decisions

Versiune 1.0 · 12 noiembrie 2026 · Decizii confirmate

## 1. Decizii ferme
| Domeniu | Decizie |
|---|---|
| Cont | Obligatoriu |
| Platforme | Android și iOS |
| Vehicule | Mai multe vehicule per cont din MVP |
| Destinație | Aplicație publică: Google Play și Apple App Store |
| Notificări | Push și e-mail, suportate și configurabile independent |
| Backup | E2EE, activat explicit de utilizator |
| Documente | Locale în utilizarea normală; niciodată upload în clar |
| VIN/OCR | Propunerile necesită confirmare |

## 2. Strategie
Construiește vertical, cu funcționalitate reală la sfârșitul fiecărei faze. Securitatea, protecția datelor și testarea cross-platform sunt criterii de acceptanță, nu activități de final. Nu automatiza registrele înainte de validarea accesului legal și tehnic.

## 3. Faze

### Faza 0 — Fundație
- Framework cross-platform, arhitectură modulară, CI.
- Cont obligatoriu, e-mail verificat, sesiuni, revocare și ștergere.
- Model multi-vehicul și izolare per cont.
- Threat model, key management și proiectare E2EE.
- Backend dev/staging/prod și observabilitate fără date sensibile.
**Gate:** arhitectură, model date și fluxuri de securitate aprobate.

### Faza 1 — Cont și garaj
- Înregistrare/login/logout și device registry.
- CRUD vehicule multiple.
- VIN decode prin adaptor, confirmare/editare.
- Selector vehicul și dashboard global/per vehicul.
**Gate:** vehicule izolate corect și date disponibile offline.

### Faza 2 — Valabilități și service
- RCA, CASCO, rovinietă, ITP per vehicul.
- Proveniență, status, istoric verificări.
- Catalog mentenanță, intervale luni/km, istoric, costuri și kilometraj.
- Alerte locale.
**Gate:** calcule și alerte validate automat.

### Faza 3 — Seif și scanare
- Scanner on-device și OCR local.
- Vault criptat, Keychain/Keystore, biometrie și PIN.
- Asociere documente cu vehicul/valabilitate/service.
- Export controlat, ștergere și teste.
**Gate:** niciun document/OCR nu părăsește dispozitivul în clar.

### Faza 4 — Backend notificări și sync
- Identity API, device registry, APNs/FCM și provider e-mail.
- Worker, programare, deduplicare, retry și anulare.
- Preferințe push/e-mail independente.
- Sync selectiv și conflict handling.
**Gate:** notificări pe dispozitive fizice iOS și Android.

### Faza 5 — Backup E2EE
- Format versionat și criptare pe dispozitiv.
- Upload exclusiv ciphertext.
- Recovery secret, revocare dispozitive și restore.
- Teste cheie greșită, corupere, upgrade și migrare.
**Gate:** serverul nu poate decripta; restore validat pe dispozitiv nou.

### Faza 6 — Verificări oficiale asistate
- Fluxuri ghidate AIDA, RAR și CNAIR.
- Confirmare manuală și istoric sursă.
- Evaluare API/parteneriate oficiale.
**Gate:** fără scraping neautorizat sau CAPTCHA bypass.

### Faza 7 — Lansare publică
- GDPR/privacy policy, terms, account deletion și data safety disclosures.
- App Store privacy labels și Google Play Data Safety.
- Teste accesibilitate, performanță, securitate și crash.
- Beta, feedback, remediere și release.
**Gate:** checklist store complet, fără probleme critice; backup și notificări validate.

## 4. Riscuri
| Risc | Măsură |
|---|---|
| Lipsă API public pentru registre | Flux asistat/manual |
| Decoder VIN incomplet în UE | Adaptor multi-provider și confirmare |
| Recovery secret pierdut | Recovery test și avertizare clară |
| Backend compromis | Minimizare, E2EE, payload generic |
| Push întârziat | E-mail ca al doilea canal |
| OCR greșit | Confirmare obligatorie |
| Conflict multi-device | Versionare și rezolvare explicită |
| Cerințe store/GDPR | Privacy-by-design și review pre-release |

## 5. Instrucțiuni ferme pentru Qoder
- Citește README și toate fișierele din docs înainte de implementare.
- docs/QODER_IMPLEMENTATION_SPEC.md este contractul MVP și sursa de adevăr.
- Nu schimba deciziile ferme fără aprobare.
- Nu implementa scraping/CAPTCHA bypass sau API-uri private neautorizate.
- Nu trimite documente, imagini sau OCR în clar către backend.
- Nu stoca cheia E2EE pe server și nu folosi parola contului ca unic secret de decriptare.
- Implementează pe faze; oprește-te la fiecare gate și prezintă raport, teste și riscuri.
- Nu începe faza următoare fără validare explicită.
- Nu adăuga analytics, reclame sau trackere fără evaluare de confidențialitate și aprobare.
- Orice abatere de securitate necesită aprobare explicită și actualizarea documentației.
