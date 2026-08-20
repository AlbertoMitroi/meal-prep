# 07 — Iterative MVP & Product Roadmap

## 1. Scopul documentului

Acest document definește modul în care construim produsul progresiv.

Obiectivul nu este:

> să implementăm cât mai repede toate feature-urile definite în Product Vision.

Obiectivul este:

> **să avem după fiecare iterație un produs complet utilizabil, iar fiecare versiune următoare să se simtă ca un upgrade natural al produsului existent.**

---

# 2. Core Roadmap Principle

Principiul fundamental al roadmap-ului este:

> **Every iteration must be independently useful.**

Nu construim:

```text
Database
↓
Nutrition Engine
↓
AI Layer
↓
Planner
↓
UI
↓
abia după 3 luni avem ceva utilizabil
```

Construim vertical:

```text
Small useful problem
      ↓
Data
      ↓
Backend logic
      ↓
API
      ↓
Frontend
      ↓
Tests
      ↓
Deploy
      ↓
USE IT
```

Apoi repetăm.

---

# 3. Vertical Slices, Not Horizontal Layers

Greșit:

### Sprint 1

Database.

### Sprint 2

Backend.

### Sprint 3

AI.

### Sprint 4

Frontend.

Utilizatorul nu are nimic folosibil până la final.

---

Corect:

### Iteration 0

```text
Track coach meal plan
```

include:

* DB;
* backend;
* frontend;
* calculations;
* deploy.

Este utilizabil.

---

### Iteration 1

```text
Personalized portions
```

include tot ce trebuie pentru feature.

Este din nou utilizabil.

---

### Iteration 2

```text
What should I eat next?
```

include întreg flow-ul.

Și tot așa.

---

# 4. No Incomplete Product Surface

O regulă foarte importantă:

> **Nu afișăm feature-uri până când nu sunt complete.**

Dacă Pantry nu există:

nu afișăm:

```text
Pantry
Coming Soon
```

Pur și simplu Pantry nu există în UI.

Când este gata:

apare ca upgrade.

---

# 5. Every Version Has Its Own Product Promise

Fiecare versiune trebuie să poată fi descrisă într-o propoziție.

Exemplu:

### V0

> Îți urmăresc simplu planul alimentar.

### V1

> Îți personalizez porțiile.

### V2

> Îți spun ce să mănânci în continuare.

### V3

> Îți planific săptămâna.

### V4

> Îți organizez și gătitul.

### V5

> Îți spun ce să cumperi și cât costă.

### V6

> Știu ce ai deja în casă.

### V7

> Poți introduce aproape orice prin cameră.

### V8

> Poți controla tot sistemul prin conversație.

### V9

> Sistemul învață ce îți place.

### V10

> Sistemul se adaptează după rezultatele tale.

Fiecare propoziție reprezintă un produs valid.

---

# 6. Architectural Principle

Chiar dacă implementăm puțin la început:

> **arhitectura trebuie să permită întregul Product Vision.**

Dar există o diferență importantă între:

```text
designing for extension
```

și:

```text
implementing everything in advance
```

Vom face primul.

Nu al doilea.

---

# 7. Do Not Prebuild Future Complexity

Nu construim din prima:

* 60 de tabele goale;
* event bus complex;
* microservices;
* vector database;
* agent framework;
* distributed queue;
* Kubernetes;
* recommendation infrastructure complet;
* full AI orchestration.

Construim aceste lucruri atunci când prima funcție reală are nevoie de ele.

---

# 8. Preserve Domain Boundaries From Day One

Chiar în prima versiune păstrăm conceptele fundamentale:

```text
Household
Member

Food
Recipe

NutritionTarget
FoodLog
WeightLog
```

Nu le combinăm într-un tabel:

```text
users_and_food_and_everything
```

doar pentru că MVP-ul este mic.

---

# 9. Product Evolution Model

Roadmap-ul este:

```text
TRACK
  ↓
PERSONALIZE
  ↓
RECOMMEND
  ↓
PLAN
  ↓
PREP
  ↓
SHOP
  ↓
UNDERSTAND KITCHEN
  ↓
SEE
  ↓
TALK
  ↓
LEARN
  ↓
ADAPT
```

Această ordine este importantă.

Fiecare nivel folosește datele nivelului precedent.

---

# 10. ITERATION 0 — Coach Plan Tracker

## Product Promise

> **Îmi urmăresc simplu planul alimentar și știu unde sunt cu alimentația în fiecare zi.**

Acesta este primul produs real.

Nu AI.

Nu weekly optimization.

Nu shopping.

Doar ceva pe care îl putem folosi imediat.

---

# 11. Why Start Here

Avem deja o problemă reală:

un plan primit de la antrenor cu:

* Masa 1;
* Masa 2;
* Masa 3;
* Masa 4;
* mai multe variante.

În loc să construim infrastructură abstractă:

introducem efectiv acel plan în aplicație.

---

# 12. Iteration 0 — User Flow

Onboarding minimal:

```text
Create household
      ↓
Add member
      ↓
Set daily target
      ↓
Select coach plan
      ↓
Today
```

---

# 13. Support Household Immediately

Chiar în V0:

```text
Household
├── Member A
└── Member B
```

trebuie să fie posibil.

Dar fără automatic household optimization încă.

---

# 14. Targets In V0

Targeturile sunt introduse manual.

Exemplu:

```text
Alberto

Calories
2100

Protein
170g

Carbs
200g

Fat
65g
```

Nu construim încă automatic TDEE engine.

---

# 15. Why Manual Targets First

Pentru că putem testa:

* tracking-ul;
* recipe system;
* nutrition calculations;
* Today experience;

fără să construim simultan Target Engine.

Dacă antrenorul oferă target:

îl folosim.

---

# 16. Coach Recipes

Introducem doar rețetele / mesele relevante.

Exemplu:

```text
Greek Yogurt Breakfast

200g Greek Yogurt
50g Oats
50g Berries
```

---

# 17. Today V0

UI:

```text
TODAY

1,340 / 2,100 kcal

Protein
118 / 170g

Carbs
126 / 200g

Fat
42 / 65g


Masa 1
Greek Yogurt Bowl
✓

Masa 2
Chicken + Rice
✓

Masa 3
Protein Shake
○

Masa 4
Chicken + Vegetables
○
```

---

# 18. Actions V0

User poate:

```text
Select meal variation
Mark as eaten
Adjust quantity
Undo
Add simple food manually
```

---

# 19. Manual Weight Tracking

Din V0:

```text
94.7 kg
[ Save ]
```

și:

```text
Weight History
```

cu trend simplu.

Nu avem încă scale photo.

---

# 20. V0 Nutrition Scope

Urmărim doar:

```text
Calories
Protein
Carbohydrates
Fat
```

eventual:

```text
Fiber
```

dacă datele sunt disponibile.

---

# 21. Important UX Rule

Nu afișăm:

```text
Micronutrients
Coming soon
```

Pur și simplu nu există încă acea secțiune.

Produsul se prezintă drept:

> plan & macro tracker.

Și este complet pentru această promisiune.

---

# 22. V0 Required Modules

```text
Household
Member
NutritionTarget
Food
Recipe
Today
FoodLog
WeightLog
```

Atât.

---

# 23. V0 Does NOT Need

```text
AI
Pantry
Budget
Shopping
Weekly Planner
Learning
Receipt
Barcode
Meal Vision
```

---

# 24. V0 Definition Of Done

Iteration 0 este finalizată doar când putem efectiv:

1. deschide aplicația de pe telefon;
2. selecta membrul;
3. vedea mesele planului;
4. loga o masă;
5. vedea macro-urile actualizate;
6. modifica porția;
7. introduce greutatea;
8. reveni mâine și avea o nouă zi;
9. păstra istoricul.

Dacă acestea funcționează:

avem produs.

---

# 25. ITERATION 1 — Personalized Nutrition

## Product Promise

> **Aplicația știe cât ar trebui să mănânce fiecare.**

---

# 26. Add Automatic Profile

Introducem:

```text
age
sex
height
weight
activity
goal
```

---

# 27. Nutrition Target Engine V1

Acum implementăm:

```text
REE
PAL
estimated maintenance
goal energy
macro targets
```

conform Nutrition Spec.

---

# 28. Manual Override Remains

Foarte important:

```text
AUTO
CUSTOM
COACH / PROFESSIONAL
```

rămân disponibile.

Nu eliminăm V0.

Îl extindem.

---

# 29. Personalized Portioning

Începem cu:

> aceeași rețetă, porții diferite.

Exemplu:

```text
Chicken Rice Bowl

Alberto
Chicken 180g
Rice 220g

Partner
Chicken 125g
Rice 145g
```

---

# 30. Portion Solver V1

Nu trebuie să folosim încă advanced optimization.

Putem începe cu:

```text
recipe scaling
+
protein floor
+
energy range
```

și min/max ingredient.

---

# 31. Cook Together V1

UI:

```text
TOTAL TO COOK

Chicken
305g

Rice
365g

Vegetables
400g
```

---

# 32. Explicit Preferences V1

Adăugăm:

### Food Preferences

```text
❤️ Love
👍 Like
😐 Neutral
👎 Avoid
🚫 Never
```

### Meal Preferences

separat.

---

# 33. Iteration 1 Is Complete When

Un cuplu poate:

1. avea două profiluri;
2. avea două targeturi;
3. selecta aceeași masă;
4. primi porții diferite;
5. găti o singură dată;
6. loga individual rezultatul.

Acum produsul este:

> **personalized household meal tracker.**

---

# 34. ITERATION 2 — Daily Nutrition Copilot

## Product Promise

> **Nu trebuie să decid singur ce să mănânc în continuare.**

Aici apare primul mare diferențiator.

---

# 35. Recipe Library

Nu generăm încă rețete nelimitate.

Construim o bibliotecă validată.

De exemplu:

```text
30–50 recipes
```

suficient de bune.

---

# 36. Recipe Categories

```text
Breakfast
Lunch
Dinner
Snack
```

cu:

* ingredients;
* nutrition;
* scalable quantities;
* preparation time.

---

# 37. Daily Nutrition State

Implementăm complet:

```text
target
consumed
planned
remaining
```

---

# 38. Nutrient Gap V1

Inițial:

```text
Calories
Protein
Carbs
Fat
Fiber
```

---

# 39. Generate Next Meal

Buton:

```text
✨ What should I eat?
```

System:

```text
Daily State
+
Preferences
+
Recipe Library
        ↓
Recommendation
```

---

# 40. Recommendation Engine V1

Scoring simplu:

```text
Nutrition Fit
Preference Fit
Variety
Prep Time
```

---

# 41. Regenerate

User:

```text
Another option
```

Primește o altă masă care respectă contextul.

---

# 42. Lock Future Meal

User:

> Vreau paste diseară.

UI permite:

```text
Dinner
🔒 Pasta
```

Daily State rezervă nutriția aproximativă.

---

# 43. Recalculate Around Locked Meal

Breakfast/Lunch recommendations țin cont de:

```text
future reserved nutrition
```

---

# 44. Micronutrients — Initial Upgrade

Odată ce food data este suficient de bună:

adăugăm gradual:

```text
selected core micronutrients
```

dar numai pentru date cu coverage/confidence suficient.

---

# 45. No Fake Completeness

Dacă micronutrient coverage este slab:

nu afișăm valori înșelătoare.

Nutrition Spec rămâne autoritatea.

---

# 46. Iteration 2 Is Complete When

User poate:

1. loga breakfast;
2. apăsa `What should I eat?`;
3. primi lunch potrivit;
4. loga lunch;
5. primi altă recomandare;
6. lock-ui dinner;
7. restul zilei se adaptează.

Acum avem:

> **Daily Nutrition Copilot.**

---

# 47. ITERATION 3 — Weekly Planner

## Product Promise

> **Nu mai trebuie să mă gândesc zilnic ce mănânc.**

---

# 48. Weekly Calendar

```text
MON TUE WED THU FRI SAT SUN
```

cu:

```text
Breakfast
Lunch
Dinner
Snack
```

---

# 49. Weekly Constraints

User poate configura:

```text
office days
workout days
external meals
locked meals
shared meals
```

---

# 50. Office Lunch

Acum introducem:

```text
lunchboxRequired
microwaveAvailable
fridgeAvailable
```

---

# 51. Generate Week V1

Planner-ul folosește:

```text
Nutrition
Preferences
Schedule
Variety
```

Nu optimizează încă cumpărături complexe.

---

# 52. Weekly Planner V1 Algorithm

Poate fi relativ simplu:

```text
Lock fixed meals
↓
Fill shared meals
↓
Fill individual meals
↓
Validate each day
↓
Improve variety
```

Nu avem nevoie încă de global solver sofisticat.

---

# 53. Manual Replanning

User poate:

```text
replace
move
lock
copy
repeat
```

---

# 54. Iteration 3 Complete When

Putem apăsa:

```text
Generate next week
```

și obține un plan realist pentru household.

Acum produsul este:

> **Weekly Nutrition Planner.**

---

# 55. ITERATION 4 — Meal Prep & Cooking

## Product Promise

> **Aplicația nu doar îmi spune ce mănânc. Îmi spune când și cât trebuie să gătesc.**

---

# 56. Cooking Frequency

User selectează:

```text
Every day
5 times
3 times
2 times
```

---

# 57. Meal Prep Engine V1

Planner-ul caută:

```text
repeatable meals
batch-friendly meals
leftover opportunities
```

---

# 58. Cooking Sessions

Exemplu:

```text
SUNDAY

Chicken Fajita × 6
Turkey Pasta × 4

WEDNESDAY

Salmon Potatoes × 4
```

---

# 59. Cook View

Include:

```text
total ingredients
steps
individual portions
batch quantity
```

---

# 60. Containers

Introducem:

```text
Container #1
Partner
Monday Lunch

Container #2
Alberto
Monday Lunch
```

---

# 61. Leftovers V1

Aplicația știe:

```text
2 servings available
```

și le poate pune în plan.

---

# 62. Iteration 4 Complete When

Household-ul poate spune:

> **Vrem să gătim doar de trei ori săptămâna asta.**

și planul funcționează.

Acum produsul devine:

> **Meal Prep Planner.**

---

# 63. ITERATION 5 — Groceries & Budget

## Product Promise

> **Știu exact ce trebuie să cumpăr și aproximativ cât mă costă.**

---

# 64. Ingredient Aggregation

Meal Plan:

```text
Chicken
Rice
Yogurt
Eggs
...
```

devine:

```text
TOTAL WEEKLY DEMAND
```

---

# 65. Shopping List

```text
☐ Chicken 1.8kg
☐ Rice 1kg
☐ Yogurt 1.6kg
☐ Eggs 12
```

---

# 66. Products

Introducem:

```text
Food
→ preferred commercial Product
```

Exemplu:

```text
Greek Yogurt
→ Pilos Greek Yogurt 2%
```

---

# 67. Package Rounding

```text
Need:
1.43kg

Product:
400g

Buy:
4 packages
```

---

# 68. Manual Prices First

Nu avem nevoie de supermarket API.

User poate salva:

```text
Pilos Greek Yogurt
7.99 lei
```

---

# 69. Price Observation

Datele pot veni din:

```text
manual
previous purchases
```

---

# 70. Budget

User setează:

```text
Weekly food budget:
500 lei
```

---

# 71. Weekly Cost

Planner-ul afișează:

```text
Projected:
472 lei
```

---

# 72. Meal Cost

Exemplu:

```text
Chicken Rice Bowl

Alberto:
9.20 lei

Partner:
6.90 lei

Household:
16.10 lei
```

---

# 73. Budget-Aware Planning V1

Dacă planul depășește:

```text
500 lei
```

Recommendation Engine poate prefera alternative mai ieftine.

---

# 74. Iteration 5 Complete When

Putem:

1. genera săptămâna;
2. vedea cumpărăturile;
3. vedea cantitățile;
4. vedea pachetele necesare;
5. vedea costul aproximativ;
6. rămâne în buget.

Acum avem:

> **Nutrition + Grocery Planner.**

---

# 75. ITERATION 6 — Smart Pantry

## Product Promise

> **Aplicația știe ce avem deja și încearcă să folosim asta înainte să cumpărăm altceva.**

---

# 76. Pantry V1

User poate adăuga manual:

```text
Chicken 600g
Rice 800g
Eggs 7
```

---

# 77. Shopping Subtraction

```text
Need:
1000g rice

Pantry:
350g

Buy:
650g
```

---

# 78. Expiry

User poate seta:

```text
Chicken
expires tomorrow
```

---

# 79. Pantry-First Recommendation

Recommendation score primește:

```text
PantryFit
ExpiryFit
```

---

# 80. Waste Reduction

Planner-ul încearcă să folosească:

```text
food expiring soon
```

înainte să adauge produse noi.

---

# 81. What Can I Cook?

Buton:

```text
What can we make with what we have?
```

---

# 82. Iteration 6 Complete When

Pantry-ul afectează:

* next meal;
* weekly plan;
* groceries;
* waste.

Acum avem:

> **Smart Kitchen Planner.**

---

# 83. ITERATION 7 — Smart Capture

Aceasta NU trebuie implementată ca un mega-release.

Se împarte în sub-iterații independente.

---

# 84. ITERATION 7.1 — Barcode & Label

## Promise

> **Adaug un produs în câteva secunde.**

---

# 85. Barcode Flow

```text
Camera
↓
Barcode
↓
Product lookup
↓
Nutrition
↓
Confirm
```

---

# 86. Label Photo Fallback

Produs necunoscut:

```text
Take nutrition label photo
↓
Extract values
↓
Confirm
↓
Custom Product
```

Această sub-iterație este completă singură.

---

# 87. ITERATION 7.2 — Scale Photo

## Promise

> **Îmi înregistrez greutatea făcând o fotografie.**

```text
Photo
↓
94.7kg
↓
Confirm
↓
Weight Log
```

Foarte mic feature.

Foarte utilizabil.

---

# 88. ITERATION 7.3 — Receipt Scan

## Promise

> **După cumpărături, fotografiez bonul și aplicația înțelege ce am cumpărat și cât am plătit.**

---

# 89. Receipt Output

Actualizează:

```text
Budget
Prices
Shopping List
Pantry
```

după confirmare.

---

# 90. ITERATION 7.4 — Meal Photo

## Promise

> **Pot loga rapid o masă din fotografie.**

Prioritate:

```text
planned meal matching
```

apoi:

```text
food estimation
```

---

# 91. ITERATION 7.5 — Fridge Scan

## Promise

> **Pot actualiza rapid ce am în frigider.**

```text
Photo
↓
Detected foods
↓
Confirm
↓
Pantry reconciliation
```

---

# 92. Why Split Vision Into Five Iterations

Pentru că fiecare pipeline este:

* diferit;
* testabil;
* util separat;
* are propriul confidence model.

Nu vrem:

> „AI Camera”

care face 5 lucruri pe jumătate.

---

# 93. ITERATION 8 — AI Coach

## Product Promise

> **Pot controla aplicația pur și simplu vorbind cu ea.**

---

# 94. AI Coach Does Not Replace UI

UI rămâne complet.

AI devine shortcut.

---

# 95. Initial Commands

```text
Ce mănânc acum?

Vreau paste diseară.

Mută cina pe vineri.

Ea are nevoie de pachet mâine.

Nu vreau pui azi.

Folosește somonul.

Fă săptămâna mai ieftină.
```

---

# 96. AI Orchestration V1

AI:

```text
language
↓
structured intent
↓
existing engines
```

Nu construim alt recommendation system.

---

# 97. Why AI Coach Comes Late

Pentru că acum are ce orchestra.

Dacă îl construim în Iteration 0:

AI-ul ar fi doar un chatbot.

Acum poate controla:

* Nutrition;
* Planner;
* Pantry;
* Budget;
* Cooking;
* Groceries.

---

# 98. ITERATION 9 — Learning System

## Product Promise

> **Cu cât folosesc aplicația, cu atât recomandările devin mai bune pentru mine.**

---

# 99. Learning Events

Începem să folosim istoricul deja acumulat:

```text
accepted
regenerated
rejected
favorited
repeated
```

---

# 100. Learned Food Affinity

Separat de:

```text
explicit preference
```

---

# 101. Learned Meal Affinity

Sistemul poate descoperi:

```text
You love omelettes
You frequently reject tuna salads
```

---

# 102. Frequency Learning

Foarte important:

User poate:

```text
love eggs
```

dar să nu vrea:

```text
eggs every morning
```

Learning Engine învață repetition tolerance.

---

# 103. Variety Personalization

Slider-ul devine mai inteligent.

---

# 104. Household Learning

Sistemul descoperă:

```text
meals that both members accept
```

și le favorizează pentru shared cooking.

---

# 105. Learning Correction

User poate vedea:

```text
We think you like:
...
```

și corecta.

---

# 106. ITERATION 10 — Adaptive Nutrition

## Product Promise

> **Planul începe să se adapteze și după rezultatele reale ale corpului meu.**

---

# 107. Weight Trend Engine

Acum avem suficient istoric.

Calculăm:

```text
smoothed weight trend
```

---

# 108. Adherence

Avem și:

```text
food logging history
planned vs actual
```

---

# 109. Target Review

System poate observa:

```text
Weight trend flat
+
tracking confidence high
+
adherence high
```

și propune:

> review calorie target.

---

# 110. No Automatic Silent Change

User confirmă.

---

# 111. Long-Term Nutrition Patterns

Analizăm:

```text
7-day
14-day
30-day
```

pentru:

* micronutrients;
* diversity;
* protein;
* fiber.

---

# 112. ITERATION 11 — Advanced Optimization

## Product Promise

> **Aplicația optimizează întreg household-ul simultan: nutriție, timp, bani și waste.**

Aceasta este versiunea matură a Product Vision.

---

# 113. Advanced Weekly Solver

Acum merită să introducem optimization tooling specializat.

Variabile:

```text
recipes
meal slots
portions
cooking sessions
packages
```

Constraints:

```text
nutrition
budget
schedule
household
meal prep
availability
```

Objectives:

```text
preference
cost
nutrition
variety
waste
cooking time
```

---

# 114. Why Not Advanced Solver From Day One

Pentru că înainte trebuie să învățăm:

* ce constraints contează real;
* cum folosim produsul;
* ce date avem;
* ce probleme apar.

Un solver excelent pentru un model greșit este inutil.

---

# 115. Roadmap Overview

```text
ITERATION 0
Coach Plan Tracker
        ↓
ITERATION 1
Personalized Portions
        ↓
ITERATION 2
Daily Nutrition Copilot
        ↓
ITERATION 3
Weekly Planner
        ↓
ITERATION 4
Meal Prep & Cook
        ↓
ITERATION 5
Groceries & Budget
        ↓
ITERATION 6
Smart Pantry
        ↓
ITERATION 7
Smart Capture
        ↓
ITERATION 8
AI Coach
        ↓
ITERATION 9
Learning System
        ↓
ITERATION 10
Adaptive Nutrition
        ↓
ITERATION 11
Advanced Optimization
```

---

# 116. Product Capability Ladder

| Iteration | Produsul știe                          |
| --------- | -------------------------------------- |
| 0         | Ce ai mâncat                           |
| 1         | Cât ar trebui să mănânci               |
| 2         | Ce ar trebui să mănânci în continuare  |
| 3         | Ce ar trebui să mănânci săptămâna asta |
| 4         | Când și cât trebuie să gătești         |
| 5         | Ce trebuie să cumperi și cât costă     |
| 6         | Ce ai deja în casă                     |
| 7         | Ce vede camera                         |
| 8         | Ce îi spui natural                     |
| 9         | Ce îți place cu adevărat               |
| 10        | Cum răspunde corpul tău                |
| 11        | Cum optimizăm tot sistemul simultan    |

---

# 117. Every Iteration Extends Existing Data

Nu construim V0 și apoi îl aruncăm.

Exemplu:

```text
V0 FoodLog
```

este folosit ulterior de:

```text
Daily State
Learning
Weight Adaptation
Analytics
```

---

# 118. V0 Recipes Become V2 Recipe Library

Nu rescriem.

Adăugăm:

* metadata;
* scalable ingredients;
* tags;
* capabilities.

---

# 119. V0 Household Remains Household

Nu începem cu:

```text
userId on everything
```

și apoi trebuie să migrăm toată aplicația la household.

Household există din ziua 1.

---

# 120. V0 Manual Targets Become One Target Mode

Mai târziu:

```text
SYSTEM_ESTIMATED
USER_CONFIGURED
PROFESSIONAL_CONFIGURED
```

Manual-ul V0 devine:

```text
USER_CONFIGURED
```

Nu este legacy hack.

---

# 121. Evolution Without Rewrites

Aceasta trebuie să fie regula:

> **Every iteration adds capability, not architectural debt that must be replaced immediately afterward.**

---

# 122. Recommended Technical Architecture

## Architecture Style

> **Modular Monolith**

Nu microservices.

---

# 123. Why Modular Monolith

Produsul este încă:

* dezvoltat de o echipă foarte mică;
* cu domain-ul încă în evoluție;
* cu multe tranzacții cross-module;
* fără scale requirements extreme.

Microservices ar adăuga:

* deployment complexity;
* network boundaries;
* distributed transactions;
* observability complexity;

fără valoare imediată.

---

# 124. Logical Architecture

```text
┌─────────────────────────────┐
│         Next.js App         │
│                             │
│ React / Mobile-first PWA    │
└──────────────┬──────────────┘
               │
               │ HTTPS / API
               ▼
┌─────────────────────────────┐
│         FastAPI API         │
│                             │
│ Modular Python Backend      │
│                             │
│ Nutrition                   │
│ Meals                       │
│ Planning                    │
│ Pantry                      │
│ Shopping                    │
│ Learning                    │
│ AI                          │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│         PostgreSQL          │
└─────────────────────────────┘
```

---

# 125. Frontend Recommendation

```text
Next.js
React
TypeScript
App Router
Tailwind
shadcn/ui
```

Mobile-first.

PWA din start.

Next.js continuă să ofere App Router ca router-ul modern al framework-ului React și este o alegere potrivită pentru o aplicație web interactivă și responsive.

---

# 126. Why PWA First

Pentru produsul inițial:

nu avem nevoie imediat de:

```text
Swift
Kotlin
React Native
```

Avem nevoie de:

* UI bun pe telefon;
* instalare pe Home Screen;
* cameră;
* rapid iteration.

Dacă mai târziu avem nevoie de native integrations mai profunde:

putem reconsidera.

---

# 127. Backend Recommendation

```text
Python
FastAPI
Pydantic
SQLAlchemy
Alembic
PostgreSQL
```

---

# 128. Why Python

Produsul nostru va avea foarte mult:

```text
optimization
constraint solving
numerical logic
data processing
AI integrations
vision
ML
```

Python are un ecosistem excelent exact în aceste zone.

---

# 129. Why FastAPI

FastAPI se potrivește foarte bine deoarece modelele Python tipate pot fi folosite simultan pentru:

* request definitions;
* validation;
* serialization;
* OpenAPI documentation.

FastAPI generează OpenAPI din aceleași declarații tipate folosite de backend.

Acest lucru este foarte valoros pentru un frontend TypeScript separat.

---

# 130. API Contract Strategy

Backend:

```text
Pydantic schemas
↓
OpenAPI
↓
Generated TypeScript client
↓
Next.js
```

Astfel evităm:

```text
Python type
+
manual duplicated TypeScript type
```

care inevitabil ajung să difere.

---

# 131. Database

Recomand:

> **PostgreSQL**

din prima zi.

Nu SQLite ca principal production database.

---

# 132. Why PostgreSQL

Domain-ul nostru este puternic relațional:

```text
Household
Members
Recipes
Meals
FoodLogs
Pantry
Shopping
Prices
Preferences
```

și are nevoie de:

* transactions;
* constraints;
* joins;
* JSON where appropriate.

---

# 133. ORM

```text
SQLAlchemy 2
```

este o alegere bună pentru layer-ul persistence.

Documentația actuală SQLAlchemy oferă ORM, relații, transactions și suport asyncio.

---

# 134. Database Migrations

```text
Alembic
```

Migrations trebuie să fie:

```text
forward
versioned
reviewable
```

din prima iterație.

---

# 135. Managed Infrastructure

Pentru început putem folosi:

```text
Managed PostgreSQL
```

și eventual același provider pentru:

* auth;
* file storage.

Un exemplu practic poate fi Supabase.

Important:

> domain logic-ul nu trebuie cuplat de API-ul specific al provider-ului.

---

# 136. Auth

Auth trebuie delegat unei soluții mature.

Backend primește:

```text
validated identity
```

și aplică authorization pe:

```text
HouseholdMembership
```

---

# 137. File Storage

Nu avem nevoie în V0.

Când apare Iteration 7:

introducem:

```text
S3-compatible object storage
```

pentru:

* receipt photos;
* meal photos;
* fridge photos;
* scale photos.

---

# 138. Background Jobs

Nu introducem queue în V0.

---

# 139. When We Need Jobs

La:

```text
receipt processing
fridge vision
heavy weekly optimization
```

putem introduce worker.

Architecture:

```text
FastAPI
   ↓
Job Queue
   ↓
Python Worker
```

---

# 140. Do Not Start With Distributed Infrastructure

Inițial:

```text
single API service
single database
```

este suficient.

---

# 141. Optimization Stack

Nu introducem solver avansat în V0.

---

# 142. Portion Solver V1

Plain Python.

Eventual:

```text
small numerical search
```

este suficient.

---

# 143. Advanced Solvers Later

Când ajungem la:

```text
Weekly Multi-Objective Planning
```

Python ne permite folosirea unor instrumente specializate.

Google OR-Tools oferă prin Python suport pentru probleme de:

* linear optimization;
* mixed-integer optimization;
* constraint programming;
* scheduling;
* assignment.

Acestea sunt foarte apropiate conceptual de problema noastră de weekly planning.

---

# 144. Possible Solver Evolution

```text
Iteration 1
Simple Python math

Iteration 2
Weighted scoring

Iteration 3
Greedy + repair planner

Iteration 4–6
Heuristics

Iteration 11
OR-Tools / optimization solver
```

---

# 145. This Is Deliberate

Nu optimizăm prematur.

Întâi înțelegem problema.

Apoi alegem solver-ul.

---

# 146. Recommended Repository Structure

```text
/
├── apps/
│   │
│   ├── web/
│   │   └── Next.js
│   │
│   └── api/
│       └── FastAPI
│
├── docs/
│   └── product/
│
├── infra/
│
└── README.md
```

---

# 147. Backend Structure

Preferăm:

> **module-first**

nu un folder gigantic:

```text
models/
services/
repositories/
controllers/
```

pentru tot produsul.

---

# 148. Example

```text
apps/api/app/

├── core/
│   ├── config/
│   ├── database/
│   ├── auth/
│   └── errors/
│
└── modules/
    │
    ├── household/
    ├── nutrition/
    ├── catalog/
    ├── recipes/
    ├── meals/
    ├── planning/
    ├── progress/
    ├── pantry/
    ├── shopping/
    ├── budget/
    ├── learning/
    └── ai/
```

---

# 149. Module Internals

La început putem păstra simplu:

```text
nutrition/

├── models.py
├── schemas.py
├── repository.py
├── service.py
├── engine.py
├── router.py
└── tests/
```

Nu avem nevoie de 15 abstraction layers.

---

# 150. When Module Grows

Putem ulterior împărți:

```text
nutrition/
├── domain/
├── application/
├── infrastructure/
└── api/
```

doar dacă complexitatea justifică.

---

# 151. Frontend Structure

Tot:

> feature-first.

```text
apps/web/src/

├── features/
│   ├── today/
│   ├── household/
│   ├── recipes/
│   ├── progress/
│   ├── planner/
│   ├── cook/
│   ├── groceries/
│   ├── pantry/
│   └── ai-coach/
│
├── components/
│   └── ui/
│
└── lib/
```

---

# 152. Don't Build Screens For Future Modules

În V0:

```text
features/
├── today
├── household
├── recipes
└── progress
```

Atât.

---

# 153. API Design

REST este suficient.

Exemple:

```text
GET /today
POST /food-logs

GET /recipes
GET /members

POST /weight-logs
```

Mai târziu:

```text
POST /recommendations/meal

POST /plans/generate

POST /ai/receipt-scan
```

---

# 154. No GraphQL Requirement

Domain-ul nu justifică GraphQL din prima zi.

OpenAPI + REST este mai simplu.

---

# 155. Domain Logic Lives In Python

Frontend nu calculează:

```text
nutrition targets
portion solving
nutrient gaps
recipe scoring
budget planning
```

---

# 156. Frontend May Calculate Presentation Only

Exemplu:

```text
progress percentage
```

pentru UI poate fi local.

Dar canonical result vine din backend.

---

# 157. Nutrition Engine Is Pure Python

Ideal:

```text
nutrition/calculations/
```

fără:

* HTTP;
* DB;
* FastAPI.

---

# 158. Example

```text
calculate_recipe_nutrition()
calculate_daily_state()
calculate_nutrient_gap()
```

pot fi testate independent.

---

# 159. Solver Is Also Pure

```text
portion_solver.solve(...)
```

primește structured input.

Returnează structured output.

Nu știe ce este FastAPI.

---

# 160. Recommendation Engine Same Principle

```text
recommendation.rank(...)
```

este domain logic.

---

# 161. Benefits

Putem ulterior:

* rula simulations;
* benchmark-ui;
* unit-test-ui;
* refactor-ui;
* pune worker;

fără să rescriem logică.

---

# 162. Database Access

Repository layer simplu:

```text
repository
↓
SQLAlchemy
↓
Postgres
```

---

# 163. Business Transaction

Service layer coordonează:

```text
load state
↓
run engine
↓
persist
```

---

# 164. AI Is Another Adapter

La Iteration 8:

```text
AI Orchestrator
```

nu accesează direct Postgres.

Folosește application services.

---

# 165. Development Principle

Nu construim:

```text
AI-specific parallel architecture
```

AI folosește aceleași commands ca UI-ul.

---

# 166. Example

UI:

```text
POST /meals/{id}/lock
```

AI intent:

```text
LOCK_MEAL
```

ambele ajung la:

```text
LockMealService
```

---

# 167. Testing Pyramid

Din V0:

### Unit tests

Pentru engines.

### Integration tests

Pentru DB + API.

### E2E

Pentru flows critice.

---

# 168. Critical V0 E2E

```text
Create member
↓
Open Today
↓
Log breakfast
↓
Nutrition updates
↓
Refresh
↓
Data persists
```

---

# 169. Critical V1 E2E

```text
Two members
↓
Same recipe
↓
Different portions
↓
Both log meal
```

---

# 170. Critical V2 E2E

```text
Log breakfast
↓
Generate lunch
↓
Accept
↓
Nutrition state changes
```

---

# 171. Feature Flags

Features aflate în dezvoltare pot exista în codebase.

Dar production UI le ascunde complet.

---

# 172. No Half-Shipped Features

Feature flag:

```text
false
```

până când:

* backend;
* frontend;
* validation;
* tests;

sunt complete.

---

# 173. Database Migration Rule

Fiecare iteration:

```text
additive migration
```

pe cât posibil.

Nu facem:

```text
drop everything and recreate
```

între versiuni.

---

# 174. Seed Data

V0 trebuie să aibă:

```text
coach meal plan seed
```

pentru development și utilizare reală.

---

# 175. Recipe Data Grows Organically

V0:

```text
~10 meals
```

V2:

```text
30–50
```

V3+:

```text
100+
```

Nu trebuie să construim 5.000 de rețete înainte de produs.

---

# 176. Internal Dogfooding

Primele două persoane care folosesc produsul suntem:

```text
Household real
```

Nu test accounts imaginare.

---

# 177. Product Development Loop

La fiecare iteration:

```text
BUILD
↓
USE FOR REAL
↓
OBSERVE FRICTION
↓
ADJUST DOMAIN
↓
NEXT ITERATION
```

---

# 178. No Roadmap Blindness

Dacă în V1 observăm că:

> userii nu vor să cântărească separat componentele,

poate schimbăm prioritatea:

```text
Batch Weight Portioning
```

mai devreme.

Documentele 00–06 sunt fundație.

Nu închisoare.

---

# 179. Roadmap Priority Formula

Feature-ul următor trebuie ales după:

```text
User Value
×
Frequency
×
Learning Value
÷
Complexity
```

---

# 180. Learning Value Is Important

Un feature poate avea valoare suplimentară dacă ne învață ceva fundamental despre produs.

Exemplu:

```text
Generate Next Meal
```

ne învață:

* ce recomandări sunt acceptate;
* cât de bine funcționează scoring-ul;
* ce preferințe contează.

Foarte valoros.

---

# 181. What We Should NOT Build Early

### Native mobile apps

PWA first.

### Full supermarket integration

Manual/product API first.

### Complex AI agents

Structured calls first.

### Automatic fridge inventory

Manual Pantry first.

### AI meal calories

Planned logging first.

### Complex ML recommendation model

Weighted rules first.

### Microservices

Modular monolith first.

### Advanced solver

Simple algorithms first.

---

# 182. Why This Matters

Multe dintre feature-urile spectaculoase:

```text
fridge AI
meal photo AI
receipt AI
```

sunt convenience features.

Diferențiatorul adevărat este mai devreme:

```text
Nutrition State
↓
Nutrient Gap
↓
Next Meal
↓
Personalized Portion
```

Acesta trebuie validat primul.

---

# 183. First Major Product Milestone

După Iteration 2 avem deja ceva foarte interesant:

```text
Personalized household
+
tracking
+
dynamic recommendations
```

Acesta poate fi primul:

> **real product milestone.**

---

# 184. Second Major Product Milestone

După Iteration 5:

```text
Nutrition
+
Weekly Planning
+
Meal Prep
+
Groceries
+
Budget
```

Avem deja:

> **Household Nutrition OS**

fără niciun AI fancy.

---

# 185. Third Major Product Milestone

După Iteration 9:

```text
Vision
+
AI Coach
+
Learning
```

produsul devine:

> **AI-native Nutrition OS**

---

# 186. MVP Definition

Important:

Nu aș numi:

```text
Iterations 0–5
```

toate „MVP”.

---

## MVP 0

Iteration 0:

> usable internal product.

---

## Core MVP

Iterations 0–2:

> demonstrează diferențiatorul.

---

## Household MVP

Iterations 0–5:

> demonstrează întreaga promisiune operatională.

---

# 187. Recommended Immediate Build Target

Prima țintă concretă trebuie să fie:

> **Iteration 0 — Coach Plan Tracker**

și nimic mai mult.

---

# 188. Iteration 0 Product Surface

Navigation:

```text
Today
Progress
Profile
```

Atât.

Recipe selection este contextual în Today.

---

# 189. Today V0

Trebuie să fie foarte bun.

Nu „admin dashboard”.

Mobile-first.

---

# 190. Progress V0

Doar:

```text
Weight
History
Basic trend
```

---

# 191. Profile V0

```text
Member
Manual nutrition targets
Household switcher
```

---

# 192. No Planner Tab Yet

Planul coach-ului este implicit.

Nu avem nevoie de calendar.

---

# 193. No Recipes Tab Yet

Rețetele sunt selectate din meal slot.

Biblioteca separată apare când devine utilă.

---

# 194. First Database Scope

Conceptual doar:

```text
Account
Household
Member

NutritionTargetProfile
NutrientTarget

Food
FoodNutrientValue

Recipe
RecipeIngredient

FoodLog
FoodLogItem

WeightLog
```

plus strict ce necesită implementarea.

---

# 195. First Backend Modules

```text
household
nutrition
catalog
recipes
meals
progress
```

---

# 196. First Frontend Features

```text
today
profile
progress
```

---

# 197. First Engine

Doar:

```text
Nutrition Calculation Engine
```

Nu Recommendation Engine.

Nu Weekly Planner.

Nu AI.

---

# 198. First Useful Equation

Practic V0 trebuie doar să știe:

```text
meal nutrition
+
daily totals
+
remaining macros
```

și să le știe foarte bine.

---

# 199. First Production Goal

Să putem folosi aplicația:

> **7 zile consecutive**

fără să avem nevoie de Notes / Excel / WhatsApp pentru tracking-ul planului.

Acesta este primul success criterion.

---

# 200. Second Production Goal

Iteration 1:

> amândoi putem mânca aceeași rețetă cu porții calculate separat.

---

# 201. Third Production Goal

Iteration 2:

> putem întreba aplicația ce mâncăm în continuare și recomandarea chiar este utilă.

---

# 202. Roadmap Rule — Never Build For Demo Only

Orice feature trebuie să intre în:

```text
daily real workflow
```

Nu construim feature-uri doar pentru:

> „wow demo”.

---

# 203. Roadmap Rule — Data Compounds

Preferăm features care acumulează date utile.

Exemplu:

```text
FoodLog
WeightLog
MealPreference
PriceObservation
```

devin mai valoroase în timp.

---

# 204. Roadmap Rule — Intelligence Comes After Data

Ordinea:

```text
TRACK
↓
DATA
↓
RULES
↓
RECOMMEND
↓
LEARN
↓
ADAPT
```

Nu:

```text
AI
↓
hope it knows everything
```

---

# 205. Roadmap Rule — Keep Escape Hatches

În fiecare iteration trebuie să existe:

```text
manual override
```

Exemplu:

Auto target?

→ manual override.

AI product scan?

→ manual product.

Meal recommendation?

→ choose something else.

Pantry prediction?

→ edit quantity.

---

# 206. Roadmap Rule — Never Depend On Perfect AI

Core workflow trebuie să funcționeze dacă:

```text
AI unavailable
```

---

# 207. Technical Evolution

```text
V0
Next + FastAPI + Postgres

V2
+ Recommendation Engine

V3
+ Planning Engine

V5
+ Pricing/Budget

V7
+ Object Storage
+ AI Vision
+ Worker if needed

V8
+ AI Orchestration

V9
+ Learning Engine

V11
+ Optimization Solver
```

---

# 208. Architecture North Star

Nu alegem tehnologie pentru:

> toate lucrurile pe care poate le vom face peste doi ani.

Alegem o arhitectură care:

1. rezolvă versiunea curentă simplu;
2. păstrează limitele domain-ului;
3. permite feature-ului următor fără rewrite;
4. permite extragerea unui modul ulterior dacă devine necesar.

---

# 209. Stack Decision

Recomandarea actuală:

```text
FRONTEND
Next.js
React
TypeScript
Tailwind
shadcn/ui
PWA

BACKEND
Python
FastAPI
Pydantic
SQLAlchemy
Alembic

DATABASE
PostgreSQL

OPTIMIZATION
Python initially
OR-Tools later where justified

AI
Provider abstraction
only when needed

FILES
S3-compatible storage
when vision arrives

ARCHITECTURE
Modular Monolith
```

---

# 210. Final Roadmap Principle

Nu construim un produs incomplet până la V1.

Construim:

```text
small complete product
        ↓
better complete product
        ↓
smarter complete product
        ↓
more automated complete product
        ↓
adaptive complete product
```

---

# 211. Ultimate Development Rule

După orice merge în production trebuie să putem spune:

> **„Dacă am opri developmentul aici, aplicația încă are un motiv clar să existe și o pot folosi zilnic.”**

Dacă răspunsul este nu:

iterația este prea mare sau este tăiată în locul greșit.

---

# 212. Immediate Next Development Target

Nu construim încă:

* AI Coach;
* weekly solver;
* pantry;
* receipt recognition;
* barcode scanner;
* full micronutrient engine.

Construim:

## Coach Plan Tracker

cu:

```text
Household
+
Members
+
Manual Targets
+
Coach Recipes
+
Today
+
Meal Logging
+
Macro Tracking
+
Manual Weight Tracking
```

Îl folosim real.

Abia apoi:

> **Personalized Portions.**

Acesta este primul pas corect către întregul Product Vision.
