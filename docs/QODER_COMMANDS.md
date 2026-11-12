# PocketCar — Comenzi scurte pentru Qoder

Folosește aceste comenzi după ce Qoder a citit documentația completă.

## Prima pornire
```text
Citește README.md și toate documentele din docs/, în special docs/DEPLOYMENT_ARCHITECTURE.md. Urmează docs/EXECUTION_PLAYBOOK.md. Începe Faza 0 și execută integral instrucțiunile. Nu implementa faza următoare. La final actualizează docs/IMPLEMENTATION_STATUS.md, rulează verificările și prezintă raportul standard.
```

## Pornirea unei faze ulterioare
Înlocuiește N cu numărul fazei:
```text
Începe Faza N din docs/EXECUTION_PLAYBOOK.md. Citește statusul curent din docs/IMPLEMENTATION_STATUS.md și verifică dependențele. Execută integral faza fără opriri intermediare pentru aprobări tehnice uzuale. Rulează și repară testele, verifică toate criteriile de acceptanță, actualizează documentația și IMPLEMENTATION_STATUS.md, creează commit și prezintă raportul standard. Oprește-te la final; nu începe faza următoare. Nu executa deployment pe TrueNAS și nu modifica infrastructura activă fără aprobarea explicită a proprietarului.
```

## Dacă Qoder s-a întrerupt în timpul unei faze
```text
Reia Faza N. Înainte de modificări, inspectează git status, ultimele commit-uri și docs/IMPLEMENTATION_STATUS.md. Identifică exact ce este deja implementat și ce testează. Continuă fără să duplici sau să suprascrii munca validă. Termină faza, rulează verificările, actualizează statusul și raportează.
```

## Dacă vrei audit independent al unei faze
```text
Auditează Faza N față de docs/EXECUTION_PLAYBOOK.md și docs/PRODUCT_REQUIREMENTS.md. Nu modifica codul înainte de raport. Verifică implementarea reală, testele, securitatea, datele persistate și traficul de rețea. Listează fiecare criteriu PASS/FAIL și dovezile. Nu declara PASS pe baza existenței unui ecran sau a unui mock.
```

## Ordinea recomandată
- Faza 0 — audit și plan tehnic
- Faza 1 — fundație, interfață și cont
- Faza 2 — garaj multi-vehicul și VIN
- Faza 3 — valabilități și mentenanță
- Faza 4 — seif, scanner și OCR
- Faza 5 — push și e-mail
- Faza 6 — backup E2EE și sync
- Faza 7 — integrare, QA și prima versiune funcțională

**Tu nu trebuie să copiezi rapoartele între agenți.** Qoder citește progresul din repository. În conversația cu Qoder este suficientă comanda pentru faza următoare.


## Implementarea designului PocketCar

Citește integral `docs/DESIGN_SPECIFICATION.md` și inspectează `assets/design/pocketcar-concept-board.svg`. Acestea sunt referințele aprobate pentru identitatea vizuală. Implementează componente native reutilizabile, design tokens, Light/Dark cu urmărirea temei sistemului, layout responsive, bottom navigation și localizare EN/RO. Nu folosi planșa SVG ca interfață în aplicație și nu reproduce dimensiunile ei fixe. Construiește toate ecranele și stările descrise în specificație. Nu considera designul complet după implementarea exclusivă a dashboard-ului; livrează capturi pentru revizuire.

## Deployment pe TrueNAS — numai după aprobare explicită

Qoder nu execută deployment în producție ca parte implicită a unei faze. După ce versiunea a trecut testele pe PC și utilizatorul aprobă explicit publicarea, folosește:

~~~text
Pregătește deployment-ul versiunii validate PocketCar pe TrueNAS conform docs/DEPLOYMENT_ARCHITECTURE.md. Verifică mai întâi commit SHA, release tag, imagini/digest-uri, configurația, volumele persistente, migrațiile, backupul și procedura de rollback. Prezintă planul și comenzile exacte, serviciile afectate, riscurile și testele post-deploy. Nu executa încă nicio comandă pe TrueNAS și nu modifica instanța activă; așteaptă aprobarea mea explicită pentru execuție.
~~~

După analizarea planului, utilizatorul poate aproba separat execuția. Qoder trebuie să execute numai pașii aprobați și să raporteze dovezile verificării. Nu include secrete în conversație sau în repository.
