# 08 — UX Architecture & User Flows

## 1. Scopul documentului

Acest document definește experiența utilizatorului pentru întreg produsul.

Este unul dintre cele mai importante documente ale proiectului.

Produsul poate avea:

* Nutrition Engine;
* Nutrient Gap Engine;
* Recommendation Engine;
* Portion Solver;
* Household Solver;
* Weekly Planner;
* Budget Engine;
* Pantry Engine;
* Learning Engine;
* AI;
* Vision;

dar utilizatorul nu trebuie să fie obligat să înțeleagă niciunul dintre aceste sisteme.

Obiectivul UX este:

> **să transformăm o infrastructură extrem de complexă într-o experiență care pare simplă, naturală și evidentă.**

---

# 2. UX North Star

Utilizatorul trebuie să poată deschide aplicația și să înțeleagă în câteva secunde:

1. **Unde sunt astăzi?**
2. **Ce trebuie să fac acum?**
3. **Ce urmează?**

Restul este secondary information.

---

# 3. Fundamental UX Principle

> **Complexity belongs to the system, not to the user.**

Dacă produsul știe:

```text
remaining protein
remaining fiber
vitamin C gap
budget remaining
pantry availability
expiry
work schedule
training schedule
meal prep
household compatibility
```

utilizatorul nu trebuie să combine manual aceste informații.

Produsul trebuie să spună:

> **„Îți recomand asta.”**

---

# 4. Decision UX, Not Dashboard UX

Nu construim produsul în jurul:

```text
charts
rings
numbers
tables
metrics
```

Construim produsul în jurul:

```text
decisions
actions
next steps
```

Datele există pentru a susține decizia.

---

# 5. Three-Level Information Hierarchy

Fiecare feature trebuie să respecte trei niveluri.

## Level 1 — Action

Ce trebuie să fac?

Exemplu:

> **Chicken Fajita Bowl**

`[ Mănânc asta ]`

---

## Level 2 — Summary

Informația necesară pentru decizie.

```text
610 kcal
48g proteină
18 min
```

---

## Level 3 — Details

Disponibil doar dacă utilizatorul îl cere.

```text
full macros
micronutrients
ingredient quantities
why this
nutrition confidence
cost breakdown
```

---

# 6. Progressive Disclosure

Nu punem toate opțiunile pe primul ecran.

Principiul:

```text
simple first
↓
details on demand
↓
advanced controls when needed
```

---

# 7. Example

Greșit:

```text
Chicken Bowl

Calories: 612
Protein: 48.3
Carbs: 62.1
Fat: 17.8
Fiber: 10.4
Calcium: 328mg
Iron: ...
Magnesium: ...
Potassium: ...
Vitamin C...
Recipe score...
Pantry match...
```

Corect:

```text
Chicken Bowl

610 kcal · 48g proteină
18 min

[ Mănânc asta ]

Detalii
```

---

# 8. UX Must Hide Internal Vocabulary

Userul nu trebuie să vadă:

```text
Nutrient Gap Engine
DailyNutritionState
Constraint Solver
NutritionVector
Household Compatibility Score
```

UI spune:

```text
Mai ai pentru azi
Se potrivește bine
Porția ta
Pentru amândoi
```

---

# 9. One Primary Action Per Screen

Orice ecran trebuie să aibă un CTA dominant.

Exemple:

### Today

> `Ce mănânc acum?`

### Plan

> `Planifică săptămâna`

### Groceries

> `Începe cumpărăturile`

### Cook

> `Începe gătitul`

---

# 10. Secondary Actions

Maximum 1–2 acțiuni vizibile lângă primary.

Restul:

```text
•••
```

sau contextual.

---

# 11. Do Not Force Configuration

Regula:

> **Never ask the user to configure something before it becomes useful.**

Nu cerem în onboarding:

* program complet pe 7 zile;
* magazine;
* echipamente;
* buget;
* meal prep;
* micronutrient preferences;
* lunchbox;
* varietate;
* AI settings.

Le cerem **just in time**.

---

# 12. Progressive Onboarding

Onboarding-ul nu este:

> un formular de 25 de pași înainte să vezi aplicația.

Este un proces distribuit în utilizarea produsului.

---

# 13. First-Session Onboarding

Colectăm doar informația necesară primei valori.

Pentru Iteration 0:

```text
1. Cine folosește aplicația?
2. Ce targeturi ai?
3. Ce plan alimentar urmărim?
4. Today
```

Atât.

---

# 14. Skip Is A Feature

Orice configurare non-criticală trebuie să permită:

> **Mai târziu**

Produsul trebuie să aibă defaults rezonabile.

---

# 15. Just-In-Time Setup

Când user folosește pentru prima dată:

### Weekly Planner

întrebăm:

> De câte ori vrei să gătești?

### Groceries

întrebăm:

> Ai un buget săptămânal?

### Office Lunch

întrebăm:

> Ai frigider / microunde la birou?

### Pantry

întrebăm:

> Vrei să folosim mai întâi ce ai deja?

---

# 16. No Global Setup Wizard

Nu trebuie să existe:

> Configurează tot sistemul.

Sistemul crește odată cu utilizatorul.

---

# 17. Main User Mental Model

Utilizatorul nu gândește:

> Nutrition → Planner → Recipe → Portion Solver.

Gândește:

```text
ASTĂZI
Ce mănânc?

SĂPTĂMÂNA
Ce vom mânca?

GĂTIT
Ce trebuie să pregătesc?

CUMPĂRĂTURI
Ce trebuie să cumpăr?

EU
Cum mă descurc?
```

UX-ul trebuie să urmeze acest model.

---

# 18. Final Mobile Navigation

Arhitectura finală recomandată:

```text
┌─────────┬─────────┬─────────┬──────────────┬─────────┐
│  Azi    │  Plan   │    +    │ Cumpărături │   Eu    │
└─────────┴─────────┴─────────┴──────────────┴─────────┘
```

---

# 19. Why These Five

## Azi

Acțiunea zilnică.

## Plan

Viitorul.

## +

Introducere rapidă în sistem.

## Cumpărături

Tot ce ține de food logistics:

* listă;
* pantry;
* cost.

## Eu

Tot ce ține de:

* profil;
* progres;
* preferințe;
* household;
* setări.

---

# 20. What Is NOT Main Navigation

Nu trebuie să avem tab separat pentru:

```text
Recipes
Budget
Pantry
Cook
AI Coach
Micronutrients
Learning
```

Acestea sunt accesibile contextual.

---

# 21. Cook Is Contextual

User nu se gândește:

> „Vreau să deschid modulul Cook.”

Se gândește:

> „Trebuie să gătesc masa asta.”

Din:

```text
Today
Plan
Meal Prep
```

apasă:

> `Gătește`

și intră în Cook Mode.

---

# 22. Recipes Are Contextual

Recipes apar când:

* alegi o masă;
* înlocuiești o masă;
* explorezi;
* planifici.

O bibliotecă completă poate exista în:

```text
Plan → Rețete
```

sau `Eu / Saved`.

Nu ocupă permanent main navigation.

---

# 23. Budget Belongs With Shopping

Userul gândește:

> „Cât cheltuim pe mâncare?”

nu:

> „Vreau să deschid financial subsystem.”

De aceea:

```text
Cumpărături
├── Listă
├── În casă
└── Buget
```

---

# 24. Pantry UX Naming

În UI nu este obligatoriu să folosim termenul tehnic:

> Pantry.

În română poate fi:

> **În casă**

cu filtre:

```text
Frigider
Congelator
Cămară
```

Mai natural.

---

# 25. AI Coach Is Global

AI Coach nu trebuie să fie un tab separat.

Mai târziu poate exista în header:

```text
[ ✦ Întreabă ]
```

sau ca bottom sheet global.

---

# 26. Why

AI-ul este:

> un shortcut către produs.

Nu produsul însuși.

---

# 27. Universal Add Action

Butonul central:

```text
+
```

este important pentru extensibilitate.

---

# 28. Iteration 0 +

La început:

```text
Adaugă

🍽 Masă
⚖️ Greutate
```

---

# 29. Later +

Devine:

```text
Adaugă

📷 Scanează masă
▦ Scanează produs
🧾 Scanează bon
⚖️ Scanează cântar
🧊 Scanează frigider

✎ Introdu manual
```

---

# 30. Important

Nu schimbăm modelul mental al userului.

De la început:

> **+ = introdu ceva în sistem.**

Ulterior devine mai inteligent.

---

# 31. Navigation Evolution

Nu afișăm tab-uri incomplete.

---

## Iteration 0

```text
Azi        +        Eu
```

---

## Iteration 3

Adăugăm Plan:

```text
Azi     Plan      +      Eu
```

---

## Iteration 5

Adăugăm Cumpărături:

```text
Azi     Plan      +      Cumpărături      Eu
```

Aceasta devine structura finală.

---

# 32. New Features Should Feel Like Upgrades

Când apare Plan:

un card discret în Today:

> **Nou — Îți putem planifica întreaga săptămână.**

`[ Încearcă ]`

După dismiss:

nu mai apare.

---

# 33. Never Surprise Existing Users

Un feature nou nu trebuie să schimbe automat:

* targeturi;
* plan;
* preferences;
* navigation behavior;

fără context.

---

# 34. TODAY — Most Important Screen

Today este ecranul principal al aplicației.

Trebuie să rezolve:

> **Ce trebuie să fac acum?**

---

# 35. Today Information Hierarchy

Ordine recomandată:

```text
1. Current context
2. Next action
3. Daily nutrition summary
4. Today's meals
5. Secondary insights
```

---

# 36. Today Header

```text
Azi · Joi, 20 august

[ Alberto ⌄ ]                     [ ✦ ]
```

Member selector este vizibil, dar discret.

---

# 37. Household Switching

Tap pe:

```text
Alberto ⌄
```

deschide:

```text
Alberto
Partner
Household
```

---

# 38. Preserve Context

Dacă utilizatorul schimbă member:

rămâne pe același ecran și aceeași zi.

Nu navigăm într-un alt „profil”.

---

# 39. Today Hero

Cea mai importantă zonă.

Trebuie să fie dinamică.

---

# 40. State — Planned Next Meal

```text
PRÂNZ · 13:00

Chicken Fajita Bowl

610 kcal · 48g proteină

[ Am mâncat ]
[ Vezi masa ]
```

---

# 41. State — No Meal Planned

```text
PRÂNZ

Ce mănânci?

Îți pot alege ceva potrivit pentru
restul zilei.

[ Recomandă-mi ]
```

---

# 42. State — Time To Cook

```text
CINA · 19:30

Turkey Pasta
Pentru amândoi

[ Începe gătitul ]
```

---

# 43. State — Meal Already Logged

Hero se mută automat la următorul pas.

Userul nu trebuie să scroll-eze printre lucruri terminate.

---

# 44. Daily Nutrition Summary

Compact.

Exemplu:

```text
1.340 / 2.150 kcal

Proteină
126 / 170g

[ Detalii nutriție ]
```

---

# 45. Do Not Show Four Giant Rings

Nu vrem:

```text
Calories ring
Protein ring
Carbs ring
Fat ring
Fiber ring
```

ocupând jumătate din ecran.

---

# 46. Macro Summary

Poate fi:

```text
P 126/170   C 132/210   G 42/65
```

într-un singur card compact.

---

# 47. Most Important Macro Highlight

Dacă proteina este relevantă pentru profil:

poate avea vizibilitate mai mare.

Dar UI-ul nu trebuie să presupună că toate profilurile au aceeași prioritate.

---

# 48. Micronutrients On Today

Nu afișăm 20 de bare.

Mai bine:

```text
Nutriție

Pe drumul bun.

De completat:
Fibre · Potasiu · Vitamina C
```

---

# 49. Data Incomplete

```text
Datele pentru unii micronutrienți
sunt parțiale.
```

`[ Detalii ]`

---

# 50. Today's Meal Timeline

Sub hero:

```text
✓ Mic dejun
  Greek Yogurt Bowl

✓ Prânz
  Chicken Bowl

○ Gustare
  Protein Shake

○ Cină
  Turkey Pasta
```

---

# 51. Meal Card Density

În listă:

nu afișăm toate ingredientele.

Doar:

```text
meal
status
short nutrition
```

---

# 52. Meal Card Actions

Tap pe card:

> Meal Detail.

Swipe/overflow pentru:

```text
Replace
Move
Skip
Lock
```

Nu afișăm cinci butoane permanent.

---

# 53. Common Path Must Be One Tap

Pentru o masă exact planificată:

```text
[ Am mâncat ]
```

trebuie să fie suficient.

---

# 54. Planned Meal Logging Flow

```text
Today
↓
Am mâncat
↓
FoodLog saved
↓
Nutrition recalculated
↓
Hero advances
```

Fără modal suplimentar.

---

# 55. Undo

După:

> Am mâncat

snackbar:

```text
Masa a fost înregistrată.

[ Anulează ]
```

---

# 56. Why Undo Is Better Than Confirmation

Nu cerem:

> „Ești sigur că ai mâncat?”

la fiecare masă.

Confirmările repetate distrug UX-ul.

---

# 57. Adjust Before Logging

Lângă primary action:

```text
Ai mâncat altă cantitate?
```

poate fi text link:

> `Ajustează`

---

# 58. Portion Adjustment Sheet

Tap:

```text
Ajustează
```

deschide bottom sheet.

---

# 59. Simple Portion Adjustment

```text
Cât ai mâncat?

50%   75%   100%   125%

sau

[ Introdu cantitatea ]
```

---

# 60. Precise Mode

Pentru user avansat:

```text
Pui           160g
Orez          190g
Legume        210g
```

---

# 61. Don't Force Precision

Utilizatorul poate spune:

```text
~¾ din porție
```

iar sistemul păstrează estimarea.

---

# 62. Meal Detail Screen

Trebuie să răspundă la:

> **Ce este masa asta și ce trebuie să fac cu ea?**

---

# 63. Meal Detail Header

```text
Chicken Fajita Bowl

610 kcal · 48g proteină
18 min
```

---

# 64. Personalized Portion

```text
Porția ta

180g pui
210g orez
200g legume
8g sos
```

---

# 65. Shared Meal

```text
Pentru voi doi

Alberto
610 kcal

Partner
470 kcal
```

---

# 66. Combined Cooking

CTA:

```text
[ Gătește pentru amândoi ]
```

---

# 67. Details Sections

Collapsed:

```text
Porții
Ingrediente
Rețetă
Nutriție
De ce recomandarea asta?
Cost
```

---

# 68. Do Not Present Advanced Details First

Userul vine pentru masă, nu pentru audit engine.

---

# 69. Generate Next Meal Flow

Acesta trebuie să fie unul dintre cele mai bune flows din produs.

---

# 70. Flow

```text
Today
↓
Recomandă-mi
↓
Loading
↓
One Best Match
```

---

# 71. Recommendation Result

```text
CEA MAI BUNĂ POTRIVIRE

Turkey Burrito Bowl

620 kcal · 51g proteină
15 min

Completează bine proteina și fibrele
rămase azi.

[ Aleg masa asta ]

Altă variantă
```

---

# 72. Do Not Show 12 Recommendations

Default:

```text
1 best
+
2 alternatives
```

maximum.

---

# 73. Another Option

Tap:

```text
Altă variantă
```

nu deschide o configurare nouă.

Sistemul păstrează automat:

* target;
* gaps;
* time;
* household;
* budget;
* pantry;
* locks.

---

# 74. Why Another?

Opțional bottom sheet:

```text
De ce vrei alta?

○ Nu am chef azi
○ Prea mult de gătit
○ Prea scumpă
○ Nu am ingredientele
○ Am mâncat recent
○ Nu-mi place
```

---

# 75. Do Not Ask Every Time

Reason feedback este optional.

Poate apărea după mai multe regenerate-uri.

---

# 76. Learning Without Friction

Primul tap pe:

> Alta

regenerează instant.

Nu blochează flow-ul cu un formular.

---

# 77. Craving Flow

User:

> Vreau paste diseară.

În UI poate face și fără AI:

```text
Dinner
↓
Setează preferință
↓
Paste
```

---

# 78. Meal Lock UI

Card:

```text
CINĂ

🔒 Paste
```

Indicator mic și clar.

---

# 79. Lock Meaning

Tap pe lock:

```text
Ce vrei să păstrăm?

● Preparatul exact
○ Doar tipul: paste
○ Doar ingredientul
```

Avansat doar dacă necesar.

---

# 80. Default Lock

Dacă user apasă:

> Lock this meal

default:

> recipe exact.

---

# 81. Replanning UX

Nu afișăm:

> Constraint solver recalculating.

Afișăm:

```text
Am adaptat restul zilei.
```

---

# 82. If Changes Are Significant

```text
Am ajustat:

• Gustarea
• Porția de la prânz

Cina rămâne neschimbată.
```

---

# 83. PLAN — Mobile Philosophy

Nu folosim un calendar desktop cu:

```text
7 coloane × 4 mese
```

pe telefon.

Este greu de citit.

---

# 84. Mobile Week Navigation

Top:

```text
L   M   M   J   V   S   D
18  19  20  21  22  23  24
```

Tap pe zi.

Sub:

mesele zilei.

---

# 85. Weekly Overview

Poate exista:

```text
[ Vezi săptămâna ]
```

ca overview compact.

Dar day view este primary pe mobil.

---

# 86. Plan Screen Header

```text
Săptămâna 24–30 august

3 sesiuni de gătit
~470 lei
```

---

# 87. Generate Week CTA

Dacă nu există plan:

```text
Să planificăm săptămâna.

[ Generează planul ]
```

---

# 88. Weekly Plan Setup

Nu formular de 20 de câmpuri.

Bottom sheet / wizard scurt.

---

# 89. Step 1 — Special Events

Sistemul deja știe schedule-ul.

Afișează:

```text
Am găsit:

Luni      sală
Marți     birou
Miercuri  sală
Vineri    cină în oraș
```

`[ Corectează ]`

---

# 90. Step 2 — Cooking

```text
De câte ori vrei să gătești?

2   3   4   Oricând
```

---

# 91. Step 3 — Strategy

```text
Cum vrei să fie săptămâna?

● Echilibrată
○ Mai ieftină
○ Mai puțin gătit
○ Mai variată
```

---

# 92. Budget

Dacă Budget este activ:

```text
Buget
500 lei
```

prefilled.

Nu cerem din nou.

---

# 93. Generate

```text
[ Generează săptămâna ]
```

---

# 94. Weekly Generation Result

Înainte de lista completă:

```text
Săptămâna ta

21 mese
14 mese împreună
3 sesiuni de gătit
5 pachete pentru birou
~472 lei

[ Folosește planul ]
```

---

# 95. Then Show Plan

User poate inspecta zilele.

Nu forțăm review-ul fiecărei mese.

---

# 96. Plan Is Draft Until Accepted

Generarea produce:

```text
Draft Plan
```

User:

```text
[ Folosește planul ]
```

îl activează.

---

# 97. Plan Editing

Tap pe meal:

```text
Chicken Bowl

[ Schimbă ]
[ Mută ]
[ Repetă ]
[ Blochează ]
```

---

# 98. Replace Flow

Tap:

> Schimbă

→ recommendation sheet.

Nu naviga prin 5 ecrane.

---

# 99. Direct Manipulation

Unde este natural:

* drag meal to another day;
* tap duplicate;
* swipe actions.

Dar fiecare gest trebuie să aibă și alternativă accesibilă prin buton.

---

# 100. Replan Remaining Week

Menu:

```text
•••

Refă restul săptămânii
Schimbă strategia
Modifică programul
```

---

# 101. Replan Preview

Înainte de apply:

```text
Vor fi schimbate 4 mese.

Cost:
486 → 448 lei

Cooking:
4 → 3 sesiuni

[ Aplică ]
```

---

# 102. Stability Is Visible

Userul trebuie să aibă sentimentul:

> aplicația nu-mi distruge planul când modific ceva.

---

# 103. OFFICE / LUNCHBOX UX

Nu creăm un modul separat.

Este un atribut al meal context.

---

# 104. Meal Badge

```text
🥡 Pachet
```

pe lunch card.

---

# 105. First Office Setup

Prima dată:

```text
Pentru prânzul la birou:

Ai frigider?
✓

Ai microunde?
✓
```

Save as default.

---

# 106. Future Weeks

Nu mai cerem.

Planner-ul știe.

---

# 107. MEAL PREP UX

Meal Prep apare după generarea planului.

---

# 108. Plan Insight

```text
Poți găti săptămâna asta în 3 sesiuni.

Duminică
Miercuri
Vineri

[ Vezi planul de gătit ]
```

---

# 109. Meal Prep Screen

Nu este listă de rețete.

Este listă de sesiuni.

```text
DUMINICĂ

~1h 10 min

Chicken Fajita × 6
Turkey Pasta × 4

[ Începe meal prep ]
```

---

# 110. COOK MODE

Cook trebuie să se simtă ca un mod temporar, focusat.

---

# 111. Cook Header

```text
Chicken Fajita Bowl

6 porții
~35 min
```

---

# 112. First Screen

```text
Pregătește:

620g pui
410g orez
450g ardei
160g sos

[ Începem ]
```

---

# 113. Cooking Steps

Un singur pas dominant:

```text
2 / 6

Taie ardeii și ceapa.

~5 min

[ Gata ]
```

---

# 114. Timers

Dacă pasul are timp:

```text
[ Pornește 12 min ]
```

Timer contextual.

---

# 115. Avoid Tiny Text While Cooking

Cook Mode trebuie să aibă:

* text mare;
* butoane mari;
* contrast bun;
* minimum clutter.

---

# 116. Final Portioning

După gătit:

```text
Împarte în:

Alberto
520g

Partner
370g

Alberto · luni prânz
490g

Partner · luni prânz
350g
```

---

# 117. Containers

```text
🥡 1
Partner
Luni · Prânz

350g
```

---

# 118. Labels

Opțional:

```text
[ Generează etichete ]
```

nu primary feature.

---

# 119. Batch Weight UX

Pentru paste/curry:

aplicația poate spune:

```text
Cântărește vasul după gătire.

Greutate totală:
[ 1.820 g ]
```

apoi calculează porțiile.

---

# 120. This Must Be Optional

Dacă user nu dorește precizie:

```text
[ Împarte aproximativ ]
```

---

# 121. CUMPĂRĂTURI

Screen structure:

```text
Listă | În casă | Buget
```

---

# 122. Default Tab

`Listă`

pentru că aceasta este acțiunea cea mai frecventă.

---

# 123. Shopping List Header

```text
Săptămâna 24–30 august

~472 lei
din 500 lei
```

---

# 124. Shopping Categories

```text
Legume
Carne & pește
Lactate
Cereale
Altele
```

---

# 125. Shopping Item

```text
☐ Greek Yogurt

Ai nevoie: 1.430g
Cumpără: 4 × 400g

~31,96 lei
```

---

# 126. Do Not Show Calculation By Default

Primary:

```text
4 × 400g
```

Secondary small:

> necesar 1.430g

---

# 127. Shopping Mode

CTA:

```text
[ Începe cumpărăturile ]
```

intră într-un UI mai simplu:

```text
☐ Pui
☐ Iaurt
☐ Ouă
☐ Orez
```

---

# 128. Checked Items Collapse

După bifare:

mutate automat jos:

```text
Cumpărate 6
```

---

# 129. Store Mode

Ulterior poate grupa:

```text
Lidl
Kaufland
```

dar nu este necesar implicit.

---

# 130. Unavailable Item

Long press / overflow:

```text
Nu este disponibil
Înlocuiește
Nu cumpăr
```

---

# 131. Substitute UX

```text
Nu găsești Pilos Greek Yogurt?

Alternative:
1. Olympus 2%
2. Zuzu Greek Style

[ Aleg ]
```

---

# 132. Receipt Completion

După shopping:

```text
Ai terminat?

[ Scanează bonul ]
[ Finalizează fără bon ]
```

---

# 133. IN CASĂ

Trebuie să fie simplu.

Nu cerem userului inventar militar.

---

# 134. Pantry Modes

Exemplu:

```text
Ouă
7 buc.

Orez
În stoc

Lapte
Puțin
```

Toate sunt valide.

---

# 135. Quick Pantry Actions

Tap item:

```text
Cantitate
Expiră
Mută
Am terminat
```

---

# 136. Expiring Section

Top:

```text
De folosit curând

Pui        mâine
Iaurt      2 zile
```

---

# 137. Recommendation CTA

```text
[ Ce putem găti cu astea? ]
```

---

# 138. BUDGET UX

Nu facem aplicație de accounting.

---

# 139. Basic Budget View

```text
August

1.420 / 2.000 lei

Săptămâna asta:
386 lei

Estimare până la final:
1.890 lei
```

---

# 140. Primary Budget Setup

Un singur input:

```text
Buget lunar pentru mâncare

[ 2.000 lei ]
```

---

# 141. Advanced Categories

Doar dacă user dorește:

```text
Alimente
Restaurant
Suplimente
Gustări
```

---

# 142. Cost Inside Meal

Nu trebuie să deschid Budget.

Meal detail poate arăta:

```text
~16,90 lei pentru amândoi
```

---

# 143. SCAN / UNIVERSAL ADD

Camera trebuie să reducă input-ul, nu să creeze încă un workflow complex.

---

# 144. Scan Entry

Tap `+`:

în versiunile cu vision:

```text
Ce vrei să adaugi?

📷 Masă
▦ Produs
🧾 Bon
⚖️ Greutate
🧊 Frigider

✎ Manual
```

---

# 145. Later Smart Camera

După ce confidence este bun:

putem avea:

> `Deschide camera`

și detectare automată.

Dar explicit modes sunt mai bune la început.

---

# 146. Scan Flow Principle

Toate scan flows folosesc:

```text
Capture
↓
Detect
↓
Review only uncertainty
↓
Confirm
↓
Save
```

---

# 147. Do Not Force Full Review

Dacă receipt are 18 item-uri și 16 au confidence mare:

evidențiem doar 2.

---

# 148. Receipt Review

```text
Am găsit 18 produse.

16 par corecte.

Verifică 2:
```

Apoi item-urile nesigure.

---

# 149. Meal Photo

După poză:

Dacă match cu planul:

```text
Pare Chicken Fajita Bowl-ul planificat.

[ Am mâncat masa planificată ]

Ajustează
```

Acesta trebuie să fie default.

---

# 150. Unplanned Meal Photo

```text
Am găsit:

Pui
Orez
Broccoli
Sos posibil

~620 kcal

[ Salvează ]
[ Ajustează ]
```

---

# 151. Uncertainty Language

Folosim:

```text
~
aprox.
pare să fie
```

Nu procente de confidence în UI normal.

---

# 152. Low Confidence

```text
Nu sunt sigur ce este sosul.

[ Fără sos ]
[ Sos de iaurt ]
[ Altceva ]
```

---

# 153. Scale Scan

```text
94,7 kg

[ Confirmă ]
[ Modifică ]
```

Extrem de simplu.

---

# 154. Fridge Scan

După scan:

```text
Am găsit 8 produse.

+ Ouă
+ Iaurt
+ Ardei
+ Pui
...
```

Checkbox per item.

---

# 155. Fridge Reconciliation

Nu afișăm intern:

> inventory reconciliation.

Spunem:

```text
Vrei să actualizez ce ai în casă?
```

---

# 156. AI COACH UX

AI Coach trebuie să fie opțional și contextual.

---

# 157. Global Entry

Header:

```text
✦
```

Tap deschide bottom sheet/full screen.

---

# 158. Empty AI State

Nu:

> „Cum te pot ajuta?”

generic.

Mai util:

```text
Poți să-mi spui:

„Ce mănânc acum?”
„Vreau paste diseară.”
„Refă săptămâna mai ieftin.”
„Avem pui care expiră mâine.”
```

---

# 159. AI Responses Must Contain Actions

Exemplu:

User:

> Vreau burger diseară.

AI:

```text
Sigur. L-am rezervat pentru cină și am
adaptat restul zilei.

[ Vezi modificările ]
```

---

# 160. Avoid Chat-Only UX

Dacă AI recomandă o masă:

răspunsul trebuie să includă Meal Card real.

Nu doar text.

---

# 161. Rich AI Response

```text
Aș merge pe:

[ Chicken Bowl card ]

[ Aleg masa ]
[ Altă variantă ]
```

---

# 162. AI Can Navigate

User:

> Arată-mi lista de cumpărături.

AI:

> `Deschide Cumpărături`

Nu reproduce lista într-un chat gigantic dacă avem ecran dedicat.

---

# 163. AI Is A Router Too

Un AI bun știe când să:

```text
answer
act
navigate
```

---

# 164. ME / PROFILE

Tab-ul:

```text
Eu
```

trebuie să fie liniștit.

Nu dashboard.

---

# 165. Me Screen

```text
Alberto

Progres
Obiectiv & targeturi
Preferințe
Program
Household
Setări
```

---

# 166. Progress Is First

User intră adesea aici pentru:

> Cum mă descurc?

---

# 167. PROGRESS UX

Nu supraîncărcăm cu analytics.

---

# 168. Progress Header

```text
Greutate

94,7 kg

Trend 7 zile
↓ 0,4 kg
```

---

# 169. Weight Chart

Simplu.

Nu 8 linii.

---

# 170. Below

```text
Nutriție
Consistență
Buget
```

apar pe măsură ce modulele există.

---

# 171. Progressive Progress

V0:

```text
Weight
```

V2:

```text
Weight
Nutrition consistency
```

V5:

```text
+ spending
```

V9:

```text
+ preference/behavior insights
```

---

# 172. No Empty Sections

Dacă Spending nu există încă:

nu afișăm card gol.

---

# 173. Target Setup UX

User nu trebuie să completeze formule.

---

# 174. Target Mode

```text
Cum vrei să setăm targeturile?

● Le introduc eu / de la antrenor
○ Calculează pentru mine
```

---

# 175. Manual Target

```text
Calorii
Proteină
Carbohidrați
Grăsimi
```

---

# 176. Auto Target

Flow simplu:

```text
Obiectiv
↓
Date de bază
↓
Activitate
↓
Suggested target
↓
Accept
```

---

# 177. Show Recommendation, Not Formula

```text
Țintă sugerată

2.150 kcal

Proteină
170g
```

`[ Folosește ]`

---

# 178. Formula Details

Sub:

```text
Cum am calculat?
```

pentru user curios.

---

# 179. Preferences UX

Nu listă de 2.000 alimente.

---

# 180. Preference Onboarding

Poate fi Tinder-like / quick chips:

```text
Ouă          ❤️
Pui          ❤️
Somon        👍
Ciuperci     🚫
Avocado      👎
```

---

# 181. Search For Specific Food

Plus:

```text
Caută un aliment
```

---

# 182. Meal Preferences Separate

Sub:

```text
Preparatele tale
```

cu recipes folosite.

---

# 183. Learned Preferences UI

Nu trebuie să afișăm algoritmul.

Poate exista:

```text
Am observat că alegi des:

Omletă
Paste cu pui
Cartofi copți
```

---

# 184. Correct Learning

```text
[ Corect ]
```

deschide:

```text
Mai des
La fel
Mai rar
Nu recomanda
```

---

# 185. Variety UX

Un singur slider:

```text
Cât de mult vrei să variem?

Familiar ─────●──── Nou
```

---

# 186. Do Not Expose Weights

Nu:

```text
Novelty Weight: 0.17
Cuisine Diversity: 0.23
```

---

# 187. Household UX

Configurația household trebuie să fie simplă.

---

# 188. Household Screen

```text
Casa noastră

Alberto
Partner

[ + Adaugă persoană ]

Mese împreună
Buget
Gătit
Echipamente
```

---

# 189. Shared Meal Defaults

UI:

```text
De obicei mâncăm împreună:

Mic dejun       Separat
Prânz           Separat
Cină             Împreună
Weekend         Împreună
```

---

# 190. This Is Default Only

În Plan user poate modifica orice zi.

---

# 191. Household UX Must Avoid Double Work

Dacă o setare este comună:

nu o configurăm pe fiecare persoană.

Exemple:

```text
equipment
groceries
budget
pantry
```

sunt household.

---

# 192. Personal Settings Stay Personal

```text
weight
nutrition targets
food preferences
schedule
```

sunt member.

---

# 193. Hiding Complexity Through Defaults

Fiecare system advanced trebuie să aibă un default bun.

---

# 194. Examples

### Recipe scoring

user nu configurează ponderile.

Default:

> Balanced.

### Portion solver

user nu configurează tolerance.

### Nutrition targets

defaults generated.

### Budget

optional.

### Meal prep

default normal cooking.

### Variety

middle.

---

# 195. Advanced Settings Exist, But Deep

Un optimizer poate modifica mai mult.

Dar:

```text
Eu → Setări → Planificare avansată
```

nu primary UX.

---

# 196. No "Simple Mode / Expert Mode" Required

Preferabil:

> aceeași aplicație.

Advanced controls apar progresiv.

Nu două produse separate.

---

# 197. Search UX

Global search poate fi accesibil contextual.

---

# 198. Food Search

Când loghezi:

```text
Caută aliment sau produs
```

rezultate:

```text
Recente
Favorite
Produse
Alimente
```

---

# 199. Recents First

Userii repetă alimente.

Nu face search de fiecare dată.

---

# 200. Saved Meals

Quick log:

```text
Mic dejun standard
Omleta mea
Shake
```

---

# 201. Speed Is UX

Common flows trebuie să fie foarte scurte.

---

# 202. UX Action Budget

Ținta pentru acțiunile comune:

### Log planned meal

1 tap.

### Log weight manually

2–3 actions.

### Generate next meal

1 tap + choose.

### Replace meal

2 actions.

### Open cooking

1 tap.

### Check shopping item

1 tap.

---

# 203. Avoid Multi-Screen Wizard For Small Tasks

Nu:

```text
Replace
↓
Select meal category
↓
Select nutrition objective
↓
Select prep time
↓
Select ingredients
↓
Generate
```

Contextul este deja cunoscut.

---

# 204. Context Must Eliminate Questions

Dacă user apasă:

> Replace Lunch

sistemul știe:

```text
meal type
member
date
nutrition gaps
budget
location
```

Nu întreabă din nou.

---

# 205. Forms

Reguli:

* unul sau câteva fields per section;
* sensible defaults;
* numeric keypad pentru numeric;
* instant validation;
* autosave unde e sigur.

---

# 206. Avoid Save Buttons Everywhere

Preferințe:

tap → saved.

Checkbox:

tap → saved.

Slider:

release → saved.

---

# 207. Save Button Where Transaction Matters

Exemple:

```text
Create profile
Apply weekly plan
Confirm receipt
Change nutrition targets
```

---

# 208. Bottom Sheets

Pe mobil, folosite pentru:

* quick edit;
* meal actions;
* portion;
* preference;
* filters.

---

# 209. Full Screen

Pentru:

* Today;
* Plan;
* Cook Mode;
* AI Coach;
* complex receipt review.

---

# 210. Avoid Modal Stacking

Nu deschidem:

```text
modal
→ modal
→ popup
→ confirmation
```

---

# 211. Back Must Never Lose Work

Dacă user începe weekly plan setup și revine:

starea rămâne.

---

# 212. Empty States

Empty state trebuie să ofere următoarea acțiune.

---

# 213. Bad Empty State

```text
No meals.
```

---

# 214. Good Empty State

```text
Nu ai încă un prânz pentru azi.

[ Recomandă-mi ceva ]
```

---

# 215. Pantry Empty

```text
Nu știm încă ce ai în casă.

[ Adaugă primul aliment ]
```

Mai târziu:

```text
[ Scanează frigiderul ]
```

---

# 216. Error States

Error trebuie să răspundă:

1. Ce s-a întâmplat?
2. Ce poate face userul?

---

# 217. Recommendation Failure

Nu:

```text
SolverError 482
```

Ci:

```text
Nu găsesc o masă care să respecte toate
condițiile.

Putem:
• crește timpul de gătit la 30 min
• folosi un ingredient care nu e în casă

[ Vezi opțiunile ]
```

---

# 218. Scan Failure

```text
Nu am putut citi clar cântarul.

[ Fă altă poză ]
[ Introdu manual ]
```

---

# 219. Network Failure

Core state trebuie să rămână vizibil dacă este cached.

---

# 220. Offline

Ideal:

* Today readable;
* Plan readable;
* Groceries usable;
* Cook usable.

---

# 221. AI Requires Internet

Dacă AI nu este disponibil:

```text
Recomandările AI nu sunt disponibile momentan.

Poți continua cu:
[ Mesele tale ]
```

---

# 222. Loading States

Nu spinner gol.

---

# 223. Generate Meal

Skeleton meal card:

```text
Aleg cea mai potrivită variantă...
```

---

# 224. Generate Week

Poate arăta progres semantic:

```text
✓ Programul
✓ Mesele împreună
• Optimizăm gătitul
• Calculăm cumpărăturile
```

Doar dacă reflectă realmente pipeline-ul.

---

# 225. Optimistic UI

Pentru acțiuni simple:

```text
check shopping item
favorite
mark meal eaten
```

UI răspunde instant.

---

# 226. Destructive Actions

Doar acestea necesită confirmare puternică:

```text
Delete household
Delete history
Remove member
Reset preferences
```

---

# 227. Everyday Corrections Use Undo

Mai bun UX.

---

# 228. Color Semantics

Nu folosim 20 de culori pentru nutrients.

---

# 229. Recommended Semantic System

```text
Primary accent
Neutral
Success
Warning
Danger
```

---

# 230. Danger Reserved For Actual Danger

Nu colorăm roșu:

> ai depășit cu 100 kcal.

Roșu trebuie rezervat pentru:

* allergy conflict;
* destructive action;
* serious error.

---

# 231. Nutrition Above Target

Poate folosi:

```text
neutral / amber
```

cu limbaj calm.

---

# 232. No Shame UI

Nu:

```text
❌ FAILED
```

---

# 233. Instead

```text
Astăzi ai ajuns puțin peste targetul estimat.
Vom adapta recomandările rămase.
```

---

# 234. Typography

Prioritate:

* high legibility;
* larger numbers where meaningful;
* short labels;
* clear hierarchy.

---

# 235. Touch Targets

Minimum comfortable tap areas.

Checkbox-ul de shopping trebuie să poată fi bifat mergând prin magazin fără precizie fină.

---

# 236. Accessibility

Trebuie să existe din start:

* contrast suficient;
* screen reader labels;
* color-independent states;
* scalable text;
* keyboard support desktop;
* reduced motion support;
* semantic controls.

---

# 237. Charts

Orice chart trebuie să aibă și text.

Nu depindem de:

> „vezi linia verde”.

---

# 238. Motion

Motion trebuie să explice schimbarea.

Exemplu:

Meal logged:

card-ul se reduce discret și next meal intră.

Nu motion decorativ excesiv.

---

# 239. Haptics

În PWA/native future:

pot fi utile pentru:

* shopping check;
* timer;
* successful scan.

Nu esențiale.

---

# 240. Notification UX

Notification trebuie să fie:

> action-oriented.

---

# 241. Good Notifications

```text
Puiul expiră mâine.
[ Folosește-l la cină ]
```

```text
Meal prep pentru mâine nu este încă pregătit.
[ Vezi ]
```

---

# 242. Bad Notifications

```text
Your potassium is only 72%.
```

---

# 243. Notification Frequency

Default conservator.

User poate extinde ulterior.

---

# 244. New Feature Introduction

Orice nou iteration trebuie să aibă:

```text
discover
understand
first value
```

fără onboarding global.

---

# 245. Example — Pantry Release

După update:

Today poate arăta o singură dată:

```text
Nou

Spune-ne ce ai în casă și vom încerca
să folosim acele ingrediente mai întâi.

[ Adaugă ce ai ]
```

---

# 246. If User Ignores It

Produsul continuă să funcționeze ca înainte.

Foarte important.

---

# 247. Feature Additions Must Be Optional Enhancements

Iteration 6 Pantry nu trebuie să facă Iteration 5 inutil dacă user nu configurează Pantry.

---

# 248. Same Principle Everywhere

AI Vision apare?

Manual entry rămâne.

Auto Targets apar?

Manual targets rămân.

Weekly Planner apare?

Daily recommendations rămân.

Learning apare?

Explicit preferences rămân.

---

# 249. Iteration 0 UX

Main navigation:

```text
Azi    +    Eu
```

---

# 250. Today V0

```text
Azi

1.200 / 2.100 kcal

P 105/170
C 120/200
G 38/65

Masa 1 ✓
Masa 2 ○
Masa 3 ○
Masa 4 ○
```

---

# 251. +

```text
Adaugă masă
Adaugă greutate
```

---

# 252. Eu

```text
Greutate
Targeturi
Plan alimentar
Household
```

---

# 253. Complete Product

Nu pare:

> early alpha missing stuff.

Pare:

> un tracker minimalist de plan alimentar.

---

# 254. Iteration 1 UX Upgrade

Today meal cards primesc:

```text
Porția ta
```

Shared meals:

```text
Pentru amândoi
```

Cook Together apare contextual.

---

# 255. Iteration 2 UX Upgrade

Hero-ul Today devine inteligent:

```text
Ce mănânci acum?
[ Recomandă-mi ]
```

și:

```text
De completat azi:
Proteină · Fibre
```

---

# 256. Iteration 3 UX Upgrade

`Plan` apare în navigation.

Restul UX-ului nu se schimbă radical.

---

# 257. Iteration 4 UX Upgrade

Meal cards pot avea:

```text
Pregătit
```

și CTA:

```text
Începe gătitul
```

Meal Prep apare în Plan.

---

# 258. Iteration 5 UX Upgrade

`Cumpărături` apare în navigation.

---

# 259. Iteration 6 UX Upgrade

În Cumpărături apare:

```text
În casă
```

Today recommendations primesc subtil:

```text
Folosește ce ai deja
```

---

# 260. Iteration 7 UX Upgrade

`+` devine mai puternic.

Nu apare un nou tab.

Aceasta este extensibilitate bună.

---

# 261. Iteration 8 UX Upgrade

În header apare:

```text
✦
```

AI Coach.

Nu apare un al șaselea bottom tab.

---

# 262. Iteration 9 UX Upgrade

User nu vede:

> Learning Engine.

Vede doar:

> recomandările devin mai bune.

În `Eu → Preferințe` apar eventual insights.

---

# 263. Iteration 10 UX Upgrade

Progress poate spune:

```text
Trendul tău a fost stabil în ultimele 3 săptămâni.

[ Revizuiește targetul ]
```

Nu schimbă nimic singur.

---

# 264. UX Extensibility Rule

Feature-urile noi trebuie să intre într-unul dintre cele cinci mental models:

```text
Azi
Plan
Adaugă
Cumpărături
Eu
```

sau să fie contextual.

---

# 265. New Feature Test

Înainte să adăugăm un feature, întrebăm:

> **Unde s-ar aștepta natural utilizatorul să găsească asta?**

Dacă răspunsul este:

> „Avem nevoie de un nou tab.”

trebuie să verificăm foarte atent.

---

# 266. UX Should Not Mirror Backend Modules

Backend:

```text
Nutrition
Learning
Budget
Pantry
Vision
```

nu înseamnă:

```text
5 pages
```

---

# 267. UX Flow — Morning

Ideal:

```text
Open app
↓
Today
↓
Next meal visible
↓
Eat / Generate
↓
Done
```

---

# 268. UX Flow — Lunch After Breakfast

```text
Open app
↓
Today
↓
Breakfast already logged
↓
Lunch is hero
↓
Recommendation reflects breakfast
↓
Choose
```

User nu știe că `NutrientGap` s-a recalculat.

---

# 269. UX Flow — Partner Office Lunch

```text
Plan
↓
Tuesday
↓
Partner lunch
🥡
↓
Meal already lunchbox-compatible
```

Nicio configurație zilnică.

---

# 270. UX Flow — Sunday Planning

```text
Plan
↓
Generate week
↓
3 quick choices
↓
Preview
↓
Accept
↓
Shopping list ready
↓
Meal prep ready
```

---

# 271. UX Flow — Cooking

```text
Today / Plan
↓
Cook
↓
Total ingredients
↓
Steps
↓
Portion
↓
Containers
↓
Done
```

---

# 272. UX Flow — Shopping

```text
Cumpărături
↓
Start shopping
↓
Check items
↓
Receipt scan
↓
Review uncertainty
↓
Done
```

---

# 273. UX Flow — Unplanned Meal

```text
+
↓
Meal
↓
Photo / manual
↓
Review
↓
Save
↓
Today adapts automatically
```

---

# 274. UX Flow — Craving

```text
Today
↓
Dinner
↓
I want something else
↓
Pasta
↓
Lock
↓
Remaining day adapts
```

sau prin AI:

```text
✦
↓
"Vreau paste diseară"
↓
Done
```

---

# 275. UX Flow — Expiring Food

System insight:

```text
Puiul expiră mâine.

[ Folosește la cină ]
```

tap:

```text
recommendation
↓
accept
```

---

# 276. UX Flow — Budget Conflict

```text
Plan exceeds budget by ~45 lei.

[ Fă-l mai ieftin ]
```

Tap:

```text
preview 3 changed meals
↓
Apply
```

---

# 277. UX Flow — No Feasible Solution

```text
Nu găsesc o variantă care să respecte
totul.

Ce putem relaxa?

○ 20 → 30 min gătit
○ +15 lei
○ Cumpărăm un ingredient nou
```

User selectează un tradeoff.

---

# 278. UX Flow — Household Conflict

User A loves rice.

User B avoids rice.

System poate prezenta:

```text
Aceeași bază, garnitură diferită.

Alberto
Rice

Partner
Potatoes
```

Nu:

> „Constraint conflict detected.”

---

# 279. UX Flow — Weight

```text
+
↓
Greutate
↓
94,7
↓
Save
```

Later:

```text
+
↓
Cântar
↓
Photo
↓
94,7
↓
Confirm
```

Same mental model.

---

# 280. UX Copy Principles

Textul trebuie să fie:

* scurt;
* concret;
* natural;
* fără jargon;
* fără moralizare.

---

# 281. Good

> „Îți recomand asta.”

---

# 282. Bad

> „Pe baza profilului tău nutrițional și a deficitului de micronutrienți algoritmic…”

---

# 283. Good

> „Mai ai nevoie de proteină și fibre azi.”

---

# 284. Bad

> „Current nutrient gap severity: HIGH.”

---

# 285. Good

> „Nu știm exact cantitatea din fotografie.”

---

# 286. Bad

> „Vision confidence = 0.54.”

---

# 287. Explain Technical Details Only On Demand

Pentru utilizatori avansați:

```text
Detalii tehnice
```

poate exista eventual.

Nu default.

---

# 288. Emotion & Tone

Produsul trebuie să se simtă:

```text
helpful
competent
calm
non-judgmental
```

Nu:

```text
disciplinarian
fitness bro
medical dashboard
```

---

# 289. No Gamification That Creates Food Anxiety

Evita:

* streak punishment;
* red failure screens;
* shame;
* huge deficit/surplus warnings.

---

# 290. Positive Reinforcement

Poate fi:

```text
Ai completat toate mesele de azi.
```

sau:

```text
Planul pentru mâine este pregătit.
```

Practic, nu infantil.

---

# 291. UX Metrics

UX trebuie măsurat.

---

# 292. Activation

Cât de repede ajunge user la prima valoare?

Exemplu:

```text
first meal logged
```

---

# 293. Daily Friction

Măsurăm:

```text
actions per meal logged
```

---

# 294. Recommendation Quality

```text
acceptance rate
regeneration rate
```

---

# 295. Weekly Planning Quality

```text
plan accepted
meals manually replaced
week replans
```

---

# 296. Shopping UX

```text
shopping completion
item correction
receipt correction
```

---

# 297. Vision UX

```text
scan completion
correction rate
abandonment rate
```

---

# 298. Cook UX

```text
cooking session completed
containers created
meal prep usage
```

---

# 299. UX Success Is Not Screen Time

Ideal:

userul poate petrece **mai puțin timp** în aplicație pentru că produsul funcționează.

---

# 300. Core UX Metric

Conceptual:

> **How many decisions did the product remove?**

---

# 301. Usability Testing

Fiecare major flow trebuie testat fără explicație verbală.

---

# 302. Test

Dăm userului telefonul și spunem:

> „Ai mâncat micul dejun. Înregistrează-l.”

Dacă întreabă:

> „Unde?”

UX-ul trebuie revizuit.

---

# 303. Test

> „Vrei paste vineri seara. Pune-le în plan.”

---

# 304. Test

> „Vrei să gătești doar de trei ori săptămâna viitoare.”

---

# 305. Test

> „Ești în Lidl. Vezi ce trebuie cumpărat.”

---

# 306. Test

> „Ai făcut poza bonului. Adaugă cumpărăturile.”

---

# 307. Test

> „Puiul expiră mâine. Găsește ceva de făcut cu el.”

---

# 308. No Developer Guidance

În test:

nu explicăm userului structura internă.

---

# 309. Critical UX Anti-Patterns

Trebuie evitate explicit.

### Anti-pattern 1

Dashboard cu 20 de metrici.

### Anti-pattern 2

Onboarding de 30 de întrebări.

### Anti-pattern 3

Calendar desktop înghesuit pe telefon.

### Anti-pattern 4

10 tab-uri.

### Anti-pattern 5

AI chat ca singura interfață.

### Anti-pattern 6

Confirmare după fiecare acțiune.

### Anti-pattern 7

Necesitatea gramajului exact pentru orice.

### Anti-pattern 8

Features viitoare afișate disabled.

### Anti-pattern 9

Separate user și household applications.

### Anti-pattern 10

Settings pentru fiecare weight din algoritm.

### Anti-pattern 11

Recomandări sub forma unor liste uriașe.

### Anti-pattern 12

Micronutrient dashboard anxiogen.

### Anti-pattern 13

Red = ai depășit calorii.

### Anti-pattern 14

AI care modifică ceva fără feedback.

### Anti-pattern 15

User obligat să reintroducă informație deja cunoscută.

---

# 310. UX Invariant — One Source Of Context

Dacă aplicația știe:

> Partner este la birou marți,

nu întreabă din nou:

> „Masa este pentru birou?”

---

# 311. UX Invariant — Preserve Input

Orice user edit trebuie păstrat dacă navighează înapoi accidental.

---

# 312. UX Invariant — Common Path Is Fast

Cel mai frecvent flow trebuie să fie cel mai scurt.

---

# 313. UX Invariant — Advanced Control Is Available

Simplu nu înseamnă limitat.

Userul avansat trebuie să poată:

* ajusta gramaje;
* vedea micronutrients;
* modifica targeturi;
* lock-ui ingrediente;
* inspecta cost;
* ajusta plan.

Dar prin secondary layers.

---

# 314. UX Invariant — User Can Always Override

Orice recomandare:

```text
Replace
Adjust
Skip
```

---

# 315. UX Invariant — User Understands State

Trebuie să fie clar:

```text
planned
prepared
eaten
skipped
```

fără a deschide detalii.

---

# 316. UX Invariant — Confidence Is Honest

Estimated data este marcată.

---

# 317. UX Invariant — No Dead Ends

Orice failure oferă:

> următoarea acțiune posibilă.

---

# 318. UX Invariant — Iterations Never Break The Mental Model

Feature nou:

adaugă capabilitate.

Nu reinventează navigația și flow-urile deja învățate.

---

# 319. Final Product Experience

În forma matură, o zi poate arăta astfel:

### Dimineața

User deschide aplicația.

```text
Azi

Mic dejun
Greek Yogurt Bowl

[ Am mâncat ]
```

Un tap.

---

### Prânz

Deschide aplicația.

```text
Ai nevoie mai ales de proteină și fibre.

Aș merge pe:

Chicken Fajita Bowl

[ Aleg masa ]
```

Două taps.

---

### După-amiază

User spune:

> „Vreau paste diseară.”

Aplicația:

> „Sigur. Am păstrat pastele pentru cină și am adaptat gustarea.”

---

### Seara

```text
Cina pentru amândoi este gata de gătit.

[ Începe gătitul ]
```

Aplicația spune:

```text
Total:
305g pui
360g orez
400g legume
```

și apoi cum se împart.

---

### Final

User nu a calculat:

* calorii;
* proteină;
* gramaje;
* micronutrienți;
* household portions;
* ingredient overlap;

dar toate au fost luate în calcul.

---

# 320. Weekly Product Experience

Duminică:

```text
Planificăm săptămâna?
```

User alege:

```text
3 sesiuni de gătit
Balanced
500 lei
```

Apasă:

> `Generează`

Primește:

```text
7 zile
3 sesiuni de gătit
5 lunchbox-uri
~472 lei
```

Apasă:

> `Folosește planul`

Lista de cumpărături apare automat.

---

# 321. Ultimate UX North Star

Produsul este reușit atunci când poate avea în spate:

```text
hundreds of foods
dozens of nutrients
multiple household members
different targets
budgets
prices
pantry
schedules
recipes
meal prep
vision
AI
learning
optimization
```

dar utilizatorul continuă să simtă că folosește o aplicație simplă.

---

# 322. Final UX Principle

> **The user should experience decisions, not systems.**

Nu trebuie să vadă cât de sofisticată este aplicația.

Trebuie doar să observe că:

> **„Știe ce am nevoie, îmi dă o variantă bună și îmi face viața mai simplă.”**

Aceasta este experiența pe care toate deciziile de produs și design trebuie să o protejeze.
