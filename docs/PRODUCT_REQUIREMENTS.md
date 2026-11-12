# PocketCar — Product Requirements

Versiune 0.1 · 12 noiembrie 2026 · Draft · Platforme: iOS și Android

## 1. Viziune
PocketCar este un asistent personal pentru administrarea unuia sau mai multor autovehicule. Reduce riscul de a uita expirări, revizii sau documente și oferă acces rapid la documentele necesare.

## 2. Garaj și adăugare vehicul
- Adăugare prin VIN introdus manual sau scanat cu camera.
- Validare VIN; suport controlat pentru vehicule mai vechi.
- Decodare VIN printr-un furnizor configurabil.
- Date posibile: marcă, model, an model, caroserie, combustibil, cilindree, putere, transmisie, doar dacă sursa le furnizează.
- Ecran de confirmare/editare înainte de salvare.
- Număr de înmatriculare, data primei înmatriculări, kilometraj și seria CIV se completează manual sau prin scanarea talonului.
- Nu se deduce proprietarul și nu se accesează date personale nepublice.

## 3. Valabilități
Tipuri inițiale: RCA, CASCO, rovinietă, ITP.

Pentru fiecare înregistrare:
- dată început și dată expirare;
- emitent/asigurător și număr document, opțional;
- notițe și document scanat asociat;
- sursă: manual, OCR, verificare oficială sau API;
- data ultimei verificări;
- stare: neverificat, verificat, expirat, necunoscut sau neconcludent.

Reguli:
- Utilizatorul poate introduce/modifica manual orice dată.
- OCR și datele externe sunt doar propuneri până la confirmare.
- O verificare nu suprascrie o valoare confirmată fără prezentarea diferenței și acceptul utilizatorului.
- Istoricul modificărilor se păstrează local.
- CASCO se introduce din poliță/OCR sau prin integrare autorizată cu asigurătorul; nu presupunem registru public centralizat.

## 4. Mentenanță / service
Operațiuni predefinite:
- ulei motor și filtre (ulei, aer, habitaclu, combustibil);
- lichid frână, antigel, bujii;
- distribuție/curea/lanț, frâne, anvelope, baterie;
- revizie generală și operațiuni personalizate.

Fiecare regulă poate avea:
- interval în kilometri;
- interval în luni;
- dată și kilometraj la ultima operațiune;
- praguri de avertizare;
- cost, service, notițe și factură scanată.

Reguli de calcul:
- Suportă declanșatorul „oricare dintre”: de exemplu 10.000 km SAU 12 luni.
- Dacă kilometrajul nu este actualizat, nu se pretinde că scadența bazată pe km este precisă.
- La înregistrarea operațiunii se generează următorul termen.
- Istoricul nu se șterge când se schimbă intervalul viitor.

## 5. Seif privat de documente
Categorii:
- auto: RCA, CASCO, ITP, rovinietă, talon, CIV, facturi, devize;
- personale: permis, carte de identitate, pașaport;
- altele: categorii definite de utilizator.

Cerințe obligatorii:
- Fișierele rămân în sandbox-ul privat al aplicației; fără upload în cloud/backend.
- Criptare înainte de scrierea pe disc.
- Cheia protejată prin Keychain/Secure Enclave pe iOS și Android Keystore pe Android.
- Acces prin Face ID/Touch ID/biometrie Android și/sau PIN al aplicației.
- Blocare automată la background și după timeout.
- Fără previzualizări sau nume sensibile în notificări, loguri sau analytics.
- Excludere din backup necriptat; politica de backup trebuie explicată clar.
- Ștergerea elimină fișierul criptat, thumbnail-urile și fișierele temporare.
- Export doar la acțiunea explicită a utilizatorului.

Aplicația nu accesează și nu stochează amprente sau șabloane Face ID. Sistemul de operare confirmă autentificarea.

## 6. Scanare documente
- Flux de scanare, nu simplă fotografie.
- Detectare margini și captură automată/manuală.
- Corecție perspectivă, crop, rotire, îndreptare, contrast și filtre.
- Export JPEG și/sau PDF.
- Procesare on-device.
- Revizuire și ajustare înainte de salvare.
- OCR opțional local pentru tip document, VIN, număr, emitent și expirare.
- Câmpurile OCR necesită confirmare; se evidențiază valorile incerte.
- Fotografia brută se șterge după procesare dacă utilizatorul nu a cerut păstrarea ei.

## 7. Notificări
Canale: push pe telefon și e-mail, configurabile independent.

Lead times inițiale propuse: 30, 14, 7, 3 și 1 zi înainte, plus în ziua expirării. Utilizatorul poate configura pragurile și tipurile de evenimente.

Cerințe:
- Push și e-mail sunt independente; eșecul unuia nu anulează celălalt.
- Deduplicare și retry pentru erori temporare.
- Fără VIN complet, număr complet sau conținut document în notificare.
- Backend necesar pentru e-mail și push remote când aplicația este închisă.
- Notificările locale pot fi fallback, dar nu înlocuiesc backend-ul pentru e-mail.
- Statusul „trimis de provider” nu trebuie confundat cu „citit de utilizator”.

## 8. Dashboard
- Vehicul selectat și rezumat.
- Următoarea expirare.
- Documente cu status vizibil.
- Următoarea operațiune service.
- Kilometraj și data ultimei actualizări.
- Acțiuni rapide: adaugă document, operațiune, actualizează kilometraj.

Ordine alerte: expirate, următoarele 7 zile, următoarele 30 zile, apoi mentenanță estimată. Dacă lipsesc date, afișează „date incomplete” sau „neverificat”, nu „totul este în regulă”.

## 9. Cerințe non-funcționale
- Funcționare offline pentru datele locale.
- Criptare pentru documente și date sensibile locale.
- TLS pentru comunicații.
- Minimizarea datelor trimise către backend.
- Fără upload documente, imagini brute sau text OCR.
- Localizare pentru dată, fus orar, unități și limbă.
- Toleranță la indisponibilitatea surselor oficiale.
- Export și ștergere date.
- Gestionarea schimbării dispozitivului fără compromiterea principiului local-first.

## 10. Criterii de acceptanță MVP
- [ ] Adăugare vehicul prin VIN și editarea datelor decodate.
- [ ] Gestionare RCA, CASCO, rovinietă și ITP.
- [ ] Afișare sursă și momentul verificării.
- [ ] Reguli service după timp și/sau km și istoric operațiuni.
- [ ] Scanare document cu crop și corecție perspectivă.
- [ ] Documente criptate local, fără transmitere la backend.
- [ ] Seif biometric și fallback PIN.
- [ ] Notificări push și e-mail testate.
- [ ] Alerte deduplicate și fără date sensibile.
- [ ] Utilizare offline pentru datele locale.

## 11. Extensii viitoare
Mai multe mașini/profil familie, cost total de deținere, alimentări și consum, anvelope și DOT, istoric daune, export PDF service, widget, import calendar, OBD-II și profil service pe motorizare.

## 12. Decizii deschise
- Cont obligatoriu sau opțional?
- Backend și furnizor e-mail?
- Backup criptat end-to-end opt-in sau fără backup?
- Framework mobil?
- Doar România sau și alte piețe?
- O mașină în MVP sau mai multe din prima versiune?