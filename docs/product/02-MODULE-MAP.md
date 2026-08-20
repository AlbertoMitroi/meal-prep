# 02 — Module Map

## 1. Scopul documentului

Acest document definește structura funcțională a produsului.

Obiectivul este să răspundă la întrebările:

* Ce module există?
* Ce responsabilitate are fiecare?
* Ce nu trebuie să facă fiecare modul?
* Ce informații intră în modul?
* Ce informații ies din modul?
* Ce module depind unele de altele?
* Ce funcționalități sunt user-facing?
* Ce funcționalități sunt internal engines?
* Care este fluxul principal al datelor prin produs?

Documentul nu definește încă:

* schema exactă a bazei de date;
* API endpoints;
* componente UI;
* framework-uri;
* implementarea algoritmilor;
* formulele exacte de nutriție.

Acestea vor fi definite ulterior.

---

# 2. Product Architecture Overview

Produsul trebuie privit ca trei straturi principale.

```text
┌─────────────────────────────────────────────┐
│              EXPERIENCE LAYER               │
│                                             │
│ Today · Planner · Cook · Groceries · Scan   │
│ Pantry · Recipes · Progress · AI Coach      │
└──────────────────────┬──────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────┐
│             INTELLIGENCE LAYER              │
│                                             │
│ Nutrition Engine                            │
│ Nutrient Gap Engine                         │
│ Recommendation Engine                       │
│ Portion Solver                              │
│ Weekly Planner                              │
│ Grocery Optimizer                           │
│ Budget Engine                               │
│ Learning Engine                             │
│ Vision / AI Interpretation                  │
└──────────────────────┬──────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────┐
│               DOMAIN LAYER                  │
│                                             │
│ Household · Members · Foods · Products      │
│ Recipes · Meals · Pantry · Prices           │
│ Plans · Logs · Preferences · Progress       │
└─────────────────────────────────────────────┘
```

Principiul de bază:

> UI-ul nu trebuie să conțină logica nutrițională critică.

Modulele user-facing cer informații și afișează rezultate.

Motoarele interne calculează și optimizează.

---

# 3. Module Categories

Modulele sunt împărțite în patru categorii:

## A. Core Experience Modules

Modulele folosite zilnic de utilizator.

1. Today
2. Planner
3. Cook
4. Groceries
5. Scan

## B. Supporting Experience Modules

Module importante, dar accesate mai puțin frecvent.

6. Pantry
7. Recipes
8. Budget
9. Progress

## C. Personalization & Administration

10. Household
11. Profile & Goals
12. Preferences
13. Schedule & Lifestyle
14. Settings

## D. Intelligence Modules

15. Nutrition Engine
16. Nutrient Gap Engine
17. Recommendation Engine
18. Portion Solver
19. Weekly Planning Engine
20. Meal Prep Engine
21. Grocery Optimizer
22. Budget Engine
23. Learning Engine
24. AI Coach
25. Vision & Scan Intelligence

---

# 4. Primary Navigation Philosophy

Navigația principală trebuie să rămână simplă.

Nu vrem 12 tab-uri.

Pentru mobile, structura recomandată este:

```text
Today
Plan
Scan
Groceries
More
```

Sau:

```text
Today
Plan
Scan
Cook
Groceries
```

În funcție de cât de central devine Cook în utilizarea reală.

Restul modulelor sunt accesibile contextual sau prin More/Profile.

---

# 5. MODULE — Today

## Rol

Today reprezintă centrul experienței zilnice.

Trebuie să răspundă în primul rând la:

> „Unde sunt astăzi și ce ar trebui să fac în continuare?”

---

## Today trebuie să afișeze

Pentru membrul selectat:

* calorii consumate;
* calorii planificate;
* calorii rămase;
* proteină;
* carbohidrați;
* grăsimi;
* fibre;
* micronutrient status;
* mesele zilei;
* workout context;
* weight log dacă este relevant.

---

## Exemplu

```text
TODAY

1,340 / 2,150 kcal

Protein
126 / 170g

Fiber
17 / 30g

Breakfast             ✓
Lunch                 ✓
Snack                 ○
Dinner                ○

[ What should I eat? ]
```

---

## Household mode

Today poate avea:

```text
Alberto | Partner | Household
```

Household view nu combină targeturile într-un număr fără sens.

Afișează mai degrabă:

* ce mese sunt comune;
* cine a mâncat;
* ce urmează;
* ce trebuie gătit.

---

## Acțiuni principale

* log planned meal;
* generate next meal;
* regenerate;
* replace;
* skip;
* adjust portion;
* scan meal;
* add food manually;
* lock desired meal;
* open Cook;
* open nutrient details.

---

## Inputs

* Daily Nutrition State;
* Meal Plan;
* Food Logs;
* Nutrition Targets;
* Schedule;
* Workout;
* Preferences;
* Pantry;
* Budget context.

## Outputs

* meal confirmation;
* food log;
* request recommendation;
* meal replacement;
* updated nutrition state.

---

## Today NU trebuie să facă

* calcule nutriționale independent;
* generare haotică de rețete;
* pantry management complet;
* weekly planning.

---

# 6. MODULE — Planner

## Rol

Planner gestionează viitorul.

Trebuie să răspundă la:

> „Ce mâncăm în următoarele zile?”

---

## Scope

* day planning;
* weekly planning;
* meal schedule;
* household participation;
* locked meals;
* office meals;
* restaurant meals;
* training days;
* recurring meals;
* plan regeneration.

---

## Weekly view

```text
        MON   TUE   WED   THU   FRI   SAT   SUN

Breakfast
Lunch
Dinner
Snack
```

---

## Meal states

Fiecare meal slot poate fi:

```text
Empty
Suggested
Planned
Locked
Prepared
Consumed
Skipped
Replaced
External
```

---

## User actions

* Generate Week;
* Generate Day;
* regenerate specific meal;
* regenerate day;
* regenerate remaining week;
* lock;
* unlock;
* move meal;
* copy meal;
* repeat meal;
* mark eating out;
* change participants;
* change meal location;
* add constraint.

---

## Planner constraints

User poate introduce:

```text
Tuesday lunch
Partner → Office

Wednesday dinner
Restaurant

Friday dinner
Pizza

Sunday
Meal prep

Cook only 3 times
```

---

## Inputs

* household;
* profiles;
* nutrition targets;
* schedule;
* preferences;
* pantry;
* budget;
* cooking strategy;
* meal history;
* recipes.

## Outputs

* Meal Plan;
* Cooking Sessions;
* ingredient demand;
* shopping requirements;
* estimated weekly cost.

---

# 7. MODULE — Cook

## Rol

Transformă planul într-o activitate executabilă în bucătărie.

Planner spune:

> ce mâncăm.

Cook spune:

> cum pregătim efectiv.

---

# 8. Cook View

Exemplu:

```text
CHICKEN FAJITA BOWL

For household

Total to cook:
Chicken       620g
Rice          410g
Peppers       450g
Yogurt sauce  160g

Portioning

Alberto
Chicken 180g
Rice    130g

Partner
Chicken 125g
Rice     85g
```

---

## Cook trebuie să includă

* total ingredient quantities;
* preparation instructions;
* serving allocation;
* cooking timer links;
* substitutions;
* batch size;
* storage instructions;
* fridge/freezer information;
* leftovers.

---

# 9. Cook — Meal Prep Mode

Exemplu:

```text
SUNDAY MEAL PREP

1. Chicken Fajita Bowl × 6
2. Greek Yogurt Breakfast × 4
3. Turkey Pasta × 4

Estimated time:
1h 25m
```

---

## Container allocation

```text
Container 1
Alberto
Monday Lunch

Container 2
Partner
Monday Lunch

Container 3
Partner
Tuesday Lunch
```

---

## Inputs

* Meal Plan;
* Portion Solver;
* recipes;
* participants;
* batch configuration.

## Outputs

* prepared meal state;
* leftovers;
* pantry consumption;
* container assignments.

---

# 10. MODULE — Groceries

## Rol

Transformă planul alimentar într-o listă de cumpărături executabilă.

Trebuie să răspundă:

> „Ce trebuie să cumpăr?”

---

## Shopping List Engine Output

```text
PROTEIN

☐ Chicken breast
  2 × 1kg

☐ Greek yogurt
  4 × 400g

CARBS

☐ Rice
  1 × 1kg
```

---

## Grocery module capabilities

* aggregate ingredients;
* subtract pantry;
* package rounding;
* product preference matching;
* categories;
* shopping checklist;
* store grouping;
* price estimate;
* actual price;
* receipt reconciliation.

---

## States

```text
Needed
In cart
Purchased
Unavailable
Substituted
Skipped
```

---

# 11. Grocery Product Resolution

Ingredient:

```text
Greek Yogurt
```

Preferred product:

```text
Pilos Greek Yogurt 2%
400g
7.99 lei
```

Grocery list poate transforma:

```text
Need: 1,430g
```

în:

```text
Buy:
4 × 400g
Estimated: 31.96 lei
```

---

## Inputs

* Meal Plan;
* ingredient quantities;
* Pantry;
* preferred products;
* package sizes;
* price history.

## Outputs

* shopping list;
* predicted spending;
* purchased products;
* pantry additions.

---

# 12. MODULE — Scan

## Rol

Scan este punctul universal de intrare pentru informații din lumea reală.

Trebuie să reducă manual input-ul.

---

## Supported scan types

```text
Meal
Barcode
Fridge
Scale
Receipt
Food / Ingredient
```

---

## Scan Home

Camera poate încerca să detecteze automat contextul.

Alternativ user selectează:

```text
Scan meal
Scan fridge
Scan barcode
Scan scale
Scan receipt
```

---

# 13. Scan — Meal

Fotografie masă.

AI detectează:

* foods;
* probable recipe;
* estimated quantities.

Dacă există planned meal:

> încearcă mai întâi match cu planul.

---

# 14. Scan — Barcode

Detectează EAN/UPC.

Rezolvă:

```text
Barcode
    ↓
Product database
    ↓
Nutrition information
```

User confirmă.

---

# 15. Scan — Fridge

Computer vision:

```text
Detected:

Eggs
Greek yogurt
Chicken
Tomatoes
Bell peppers
```

User confirmă.

Doar după confirmare se modifică Pantry.

---

# 16. Scan — Scale

Detectează valoarea.

```text
Detected:
94.7 kg

[ Confirm ]
[ Edit ]
```

---

# 17. Scan — Receipt

Extrage:

* merchant;
* date;
* items;
* quantity;
* prices;
* total.

După confirmare poate alimenta:

* Pantry;
* Budget;
* Price History.

---

## Scan principle

> Detection first, confirmation before permanent mutation.

---

# 18. MODULE — Pantry

## Rol

Pantry reprezintă modelul digital al alimentelor disponibile în household.

---

## Storage zones

```text
Fridge
Freezer
Pantry / Cupboard
Other
```

---

## Pantry Item

Conceptual:

```text
Chicken breast

Quantity:
600g

Location:
Fridge

Expires:
Tomorrow

Source:
Lidl

Status:
Open
```

---

## Quantity modes

Pentru precizie:

```text
620g
```

Dar aplicația trebuie să permită și:

```text
In stock
Low
Out
```

pentru UX simplificat.

---

## Actions

* add manually;
* scan;
* consume;
* mark out;
* edit quantity;
* expiry;
* transfer storage;
* favorite;
* discard.

---

## Pantry Intelligence

Trebuie să poată semnala:

```text
Use soon
Chicken — tomorrow
Yogurt — 2 days
```

și să influențeze recommendation engine-ul.

---

# 19. MODULE — Recipes

## Rol

Recipes reprezintă biblioteca controlată de preparate pe care sistemul le poate folosi pentru planificare.

Nu este doar o galerie de inspirație.

Este unul dintre principalele input-uri pentru optimizare.

---

## Recipe metadata

Fiecare rețetă poate conține:

* ingredients;
* preparation steps;
* nutritional data;
* cuisine;
* meal type;
* prep time;
* cook time;
* difficulty;
* equipment;
* meal prep score;
* lunchbox friendliness;
* microwave friendliness;
* freezer suitability;
* shelf life;
* tags;
* substitution groups.

---

# 20. Scalable Recipe Model

Rețeta nu trebuie să fie doar:

```text
Chicken 150g
Rice 100g
```

Trebuie să poată avea componente ajustabile.

```text
Protein:
Chicken
100–250g

Carb:
Rice
50–150g dry

Vegetable:
200–400g

Fat:
Olive oil
5–15g
```

Asta permite Portion Solver-ului să personalizeze masa.

---

# 21. Recipe Actions

* favorite;
* rate;
* dislike;
* never recommend;
* add custom recipe;
* clone;
* modify;
* add to plan;
* cook now;
* generate variation.

---

# 22. MODULE — Budget

## Rol

Budget conectează alimentația cu costul real.

---

## Levels

```text
Meal
Day
Week
Month
Household
```

---

## Budget categories

* groceries;
* eating out;
* supplements;
* snacks;
* other.

---

# 23. Budget Dashboard

```text
AUGUST

Food budget
2,350 lei

Spent
1,420 lei

Projected
2,180 lei

Remaining
930 lei
```

---

## Meal Cost

```text
Chicken Rice Bowl

Alberto      9.80 lei
Partner      7.10 lei

Household   16.90 lei
```

---

## Budget module provides

* estimated cost;
* actual cost;
* remaining budget;
* forecast;
* cost per meal;
* cost per day;
* cost per protein target;
* cost trend.

---

# 24. MODULE — Progress

## Rol

Progress răspunde:

> „Funcționează sistemul pentru mine?”

---

## Core metrics

* weight;
* weight trend;
* target progression;
* nutrition adherence;
* protein consistency;
* fiber consistency;
* micronutrient coverage;
* meal plan adherence;
* cooking consistency;
* spending trend.

---

# 25. Weight Progress

Trebuie preferat trendul.

```text
Daily
94.8
95.2
94.6
94.5

7-day average
94.7
```

---

## Optional measurements

Ulterior:

* waist;
* body fat estimate;
* progress photos;
* other measurements.

---

# 26. MODULE — Household

## Rol

Household gestionează relația dintre persoanele care împart alimentația.

---

## Household settings

* household name;
* members;
* invitations;
* shared meals;
* grocery preferences;
* budget;
* pantry;
* cooking equipment;
* stores;
* cooking strategy.

---

# 27. Member Participation

Fiecare meal slot trebuie să poată avea:

```text
Participants

Alberto       ✓
Partner       ✓
Child         ✕
Guest         ✓
```

---

# 28. Household Defaults

Exemplu:

```text
Breakfast
Usually separate

Lunch
Weekdays separate
Weekend shared

Dinner
Usually shared
```

Acestea sunt defaults, nu reguli rigide.

---

# 29. MODULE — Profile & Goals

## Rol

Stochează contextul fiziologic și obiectivele individuale.

---

## Includes

* height;
* weight;
* age;
* goal;
* calorie target;
* macro target;
* micronutrient target;
* activity;
* target weight;
* units.

---

# 30. Target modes

```text
Auto
Custom
Professional-defined
```

Aplicația trebuie să poată diferenția între:

> suggested target

și

> configured target.

---

# 31. MODULE — Preferences

## Rol

Centralizează tot ce sistemul știe despre gusturile utilizatorului.

---

## Preference domains

```text
Food Preferences
Meal Preferences
Cuisine Preferences
Texture Preferences
Cooking Preferences
Variety
Price Sensitivity
Novelty Preference
```

---

# 32. Explicit vs Learned

Trebuie diferențiate:

```text
Explicit:
User says ❤️ Eggs

Learned:
User accepts egg breakfasts frequently
```

Learning Engine poate produce un confidence score.

---

# 33. MODULE — Schedule & Lifestyle

## Rol

Modelează constrângerile vieții reale.

---

## Includes

* office days;
* remote days;
* workout days;
* workout times;
* meal locations;
* time available;
* lunchbox requirements;
* fridge availability;
* microwave availability;
* recurring external meals.

---

# 34. Example

```text
MONDAY

08:00 Breakfast — Home

12:30 Lunch — Office
Lunchbox required

18:30 Gym

20:00 Dinner — Home
```

Acest context este folosit direct de recommendation engine.

---

# 35. MODULE — AI Coach

## Rol

AI Coach reprezintă interfața conversațională către întregul sistem.

Nu trebuie să fie un chatbot separat care nu știe ce se întâmplă în aplicație.

---

# 36. Example requests

```text
"Ce mănânc acum?"
```

```text
"Vreau paste diseară."
```

```text
"Avem 500g pui care expiră."
```

```text
"Mâine ea merge la birou și eu merg la sală."
```

```text
"Nu vreau să gătesc azi."
```

```text
"Fă săptămâna viitoare mai ieftină."
```

---

# 37. AI Coach Responsibilities

AI-ul interpretează limbajul natural și îl transformă în structured intents.

Exemplu:

```text
"Vreau paste diseară"
```

↓

```text
action:
lock_meal_preference

meal:
dinner

food_constraint:
pasta
```

Apoi motoarele deterministe rezolvă restul.

---

# 38. AI Coach Can

* interpret user intent;
* query context;
* call recommendation engine;
* modify plan;
* explain results;
* suggest alternatives;
* interpret messy input.

---

# 39. AI Coach Cannot Be Source of Truth For

* kcal;
* exact macros;
* micronutrients;
* allergies;
* exact product nutrition;
* precise gram calculations.

---

# 40. INTERNAL ENGINE — Nutrition Engine

## Rol

Menține adevărul nutrițional al sistemului.

---

## Responsibilities

* food nutrient calculations;
* recipe nutrition;
* portion nutrition;
* daily totals;
* macro totals;
* micronutrient totals;
* target comparison.

---

# 41. Nutrition State

Pentru fiecare member + date:

```text
Target
Consumed
Planned
Remaining
```

pentru fiecare nutrient relevant.

---

# 42. INTERNAL ENGINE — Nutrient Gap Engine

## Rol

Determină:

> „Ce îi lipsește utilizatorului?”

---

## Exemplu

```text
Remaining:

Calories       1,120
Protein           83g
Fiber              18g

Vitamin C          71%
Calcium            48%
Magnesium          44%
```

Engine-ul transformă aceste date într-un gap profile utilizabil de ranking.

---

# 43. INTERNAL ENGINE — Recommendation Engine

## Rol

Selectează cele mai potrivite preparate în context.

---

## Possible scoring

```text
Nutrition fit
Micronutrient fit
Preference fit
Pantry match
Budget fit
Meal prep fit
Schedule fit
Ingredient reuse
Variety
Expiry usage
```

---

## Output

Nu doar:

```text
Chicken Rice Bowl
```

ci:

```text
Recipe ID
Score
Reason codes
Required adjustments
```

---

# 44. INTERNAL ENGINE — Portion Solver

## Rol

Transformă o rețetă într-o porție potrivită unei persoane.

---

## Input

```text
Recipe
Meal calorie target
Nutrient gap
Member profile
Recipe constraints
```

## Output

```text
Chicken 180g
Rice 90g
Broccoli 200g
Oil 8g
```

---

# 45. Household Portion Solver

Pentru shared meal:

```text
Alberto
portion A

Partner
portion B
```

apoi:

```text
Combined cooking quantities
```

---

# 46. INTERNAL ENGINE — Weekly Planning Engine

## Rol

Optimizează mai multe mese simultan.

Nu poate trata fiecare zi independent deoarece trebuie să considere:

* budget;
* leftovers;
* ingredient reuse;
* cooking frequency;
* variety;
* meal prep;
* office schedule.

---

# 47. Weekly Planning Output

```text
Meal Plan
Cooking Sessions
Recipe Allocations
Portion Allocations
Expected Nutrition
Ingredient Demand
Projected Cost
```

---

# 48. INTERNAL ENGINE — Meal Prep Engine

## Rol

Transformă planul săptămânal în sesiuni eficiente de gătit.

---

## Inputs

* planned meals;
* cooking frequency;
* recipe compatibility;
* storage lifetime;
* household portions.

---

## Output

```text
Sunday:
Cook A × 6
Cook B × 4

Wednesday:
Cook C × 6
```

---

# 49. INTERNAL ENGINE — Grocery Optimizer

## Rol

Transformă ingredient demand în cumpărături optimizate.

---

## Responsibilities

* aggregate;
* pantry subtraction;
* package rounding;
* preferred product mapping;
* substitute selection;
* ingredient reuse analysis;
* waste minimization.

---

# 50. INTERNAL ENGINE — Budget Engine

## Rol

Calculează impactul financiar și introduce bugetul ca constraint.

---

## Sources

* actual receipt prices;
* product history;
* estimated prices;
* package cost.

---

## Output

```text
Meal cost
Daily cost
Weekly projected cost
Budget remaining
```

---

# 51. INTERNAL ENGINE — Learning Engine

## Rol

Transformă comportamentul utilizatorului în personalizare.

---

# 52. Learning Events

Exemple:

```text
meal_accepted
meal_rejected
meal_regenerated
meal_favorited
meal_skipped
recipe_rated
food_liked
food_avoided
meal_repeated
```

---

# 53. Learning Outputs

Pentru fiecare:

```text
Food affinity
Meal affinity
Cuisine affinity
Novelty preference
Ingredient affinity
```

cu:

```text
score
confidence
source
```

---

# 54. INTERNAL ENGINE — Vision Intelligence

## Rol

Transformă imaginile în informație structurată.

---

## Vision domains

```text
Meal
Fridge
Scale
Receipt
Product
```

---

## Important

Vision produce:

```text
candidate detections
confidence
```

Nu modifică direct adevărul sistemului fără confirmare atunci când există incertitudine semnificativă.

---

# 55. Cross-Module Flow — Eat Planned Meal

```text
Today
  ↓
User taps "I ate this"
  ↓
Food Log
  ↓
Nutrition Engine
  ↓
Daily Nutrition State
  ↓
Nutrient Gap Engine
  ↓
Recommendation context updated
```

---

# 56. Cross-Module Flow — Generate Next Meal

```text
Today
  ↓
Recommendation request
  ↓
Daily Nutrition State
  +
Preferences
  +
Pantry
  +
Budget
  +
Schedule
  +
Meal history
  ↓
Recommendation Engine
  ↓
Portion Solver
  ↓
Meal suggestion
  ↓
Today
```

---

# 57. Cross-Module Flow — Generate Week

```text
Planner
   ↓
User constraints
   ↓
Weekly Planning Engine
   │
   ├── Nutrition Engine
   ├── Recommendation Engine
   ├── Portion Solver
   ├── Meal Prep Engine
   ├── Budget Engine
   └── Pantry
   ↓
Weekly Plan
   │
   ├── Planner
   ├── Cook
   └── Grocery Optimizer
          ↓
       Groceries
```

---

# 58. Cross-Module Flow — Scan Receipt

```text
Scan
 ↓
Vision Intelligence
 ↓
Receipt candidates
 ↓
User confirmation
 ↓
┌──────────────┬───────────────┬────────────────┐
▼              ▼               ▼
Pantry       Budget        Price History
```

---

# 59. Cross-Module Flow — Scan Fridge

```text
Scan
 ↓
Vision
 ↓
Detected Foods
 ↓
Confirmation
 ↓
Pantry
 ↓
Recommendation Engine receives new context
```

---

# 60. Cross-Module Flow — Weight Photo

```text
Scan
 ↓
Scale detection
 ↓
Confirmation
 ↓
Weight Log
 ↓
Progress
 ↓
Future nutrition adjustment logic
```

---

# 61. Cross-Module Flow — User Wants Pizza

```text
AI Coach / Planner / Today
 ↓
"I want pizza tonight"
 ↓
Structured constraint
 ↓
Dinner locked
 ↓
Nutrition Engine
 ↓
Remaining day recalculated
 ↓
Recommendation Engine
 ↓
Earlier/later meals adapted
```

---

# 62. Shared Core Concepts

Mai multe module trebuie să folosească aceleași concepte și nu să le redefinească.

## Meal

O instanță concretă planificată sau consumată.

## Recipe

Template reutilizabil.

## Food

Ingredient generic.

## Product

Produs comercial.

## Portion

Cantitatea unui recipe/meal pentru un member.

## Cooking Batch

Cantitate gătită simultan.

## Container

Porție fizică alocată.

## Pantry Item

Produs disponibil.

## Meal Plan

Plan pe o perioadă.

## Food Log

Ce a fost consumat efectiv.

---

# 63. Generic Food vs Product

Foarte important.

```text
FOOD
Greek Yogurt
```

este concept nutrițional generic.

```text
PRODUCT
Pilos Greek Yogurt 2%
400g
```

este produs comercial.

Recipe poate cere:

```text
Greek Yogurt
```

iar Product Resolver poate folosi:

```text
Pilos Greek Yogurt
```

în funcție de preferințele household-ului.

---

# 64. Recipe vs Meal

```text
Recipe
Chicken Rice Bowl
```

este template.

```text
Meal
Chicken Rice Bowl
Monday Lunch
Alberto + Partner
```

este instanță.

---

# 65. Plan vs Log

Trebuie separate.

```text
PLANNED
180g chicken

ACTUAL
160g chicken
```

Asta permite:

* adherence;
* recalcularea zilei;
* diferența plan vs actual.

---

# 66. Module Ownership Rules

Pentru a evita arhitectura haotică:

### Today owns

daily experience.

### Planner owns

future meal schedule.

### Cook owns

execution of cooking.

### Pantry owns

available food.

### Groceries owns

food acquisition.

### Budget owns

financial state.

### Recipes owns

recipe library.

### Progress owns

historical outcomes.

### Household owns

shared context.

### Preferences owns

declared preference state.

### Learning Engine owns

inferred preference state.

### Nutrition Engine owns

nutrition truth.

---

# 67. Avoid Duplicate Responsibilities

Exemplu greșit:

```text
Planner calculates calories.
Today calculates calories.
Recipes calculates calories.
```

Corect:

```text
Nutrition Engine calculates calories.

Planner consumes result.
Today displays result.
Recipes displays result.
```

---

# 68. Module Dependency Map

```text
Household
   ├── Profiles
   ├── Preferences
   ├── Schedule
   └── Budget
          │
          ▼

Recipes ──────────────┐
Foods ────────────────┤
Products ─────────────┤
Pantry ───────────────┤
                      ▼
              Intelligence Layer
                      │
            ┌─────────┼──────────┐
            ▼         ▼          ▼
          Today     Planner    Groceries
                      │
                      ▼
                     Cook
```

---

# 69. Context Aggregation Layer

Pentru recomandări este util conceptual să existe un serviciu care construiește:

```text
RecommendationContext
```

Acesta agregă fără a modifica datele.

---

## RecommendationContext

```text
Member
Household
Nutrition State
Meal History
Meal Plan
Preferences
Learned Preferences
Pantry
Budget
Schedule
Workout
Meal Location
Cooking Constraints
Recipe History
```

Apoi Recommendation Engine nu trebuie să interogheze haotic 15 module.

---

# 70. WeeklyPlanningContext

Similar:

```text
Household Members
Nutrition Profiles
Week Schedule
Locked Meals
Cooking Frequency
Budget
Pantry
Preferences
Meal History
Recipe Library
```

---

# 71. Core User-Facing Actions

Indiferent de ecran, aplicația trebuie să aibă un vocabular consistent.

```text
Generate
Regenerate
Replace
Lock
Unlock
Eat / Log
Skip
Move
Cook
Scan
Favorite
Rate
Adjust
```

Același concept nu trebuie denumit diferit în module diferite.

---

# 72. Global Search

Ulterior, poate exista search universal pentru:

* foods;
* products;
* recipes;
* meals.

Nu este modul principal.

---

# 73. Notifications Layer

Cross-cutting module.

Poate genera:

```text
Lunchbox ready for tomorrow?
```

```text
Chicken expires tomorrow.
```

```text
Meal prep planned tonight.
```

```text
You're missing ingredients for tomorrow.
```

Nu trebuie să devină spammy.

---

# 74. Notification Sources

* Pantry;
* Planner;
* Meal Prep;
* Progress;
* Groceries.

---

# 75. Favorites

Favorites trebuie să fie cross-module.

Poți avea:

```text
Favorite Food
Favorite Product
Favorite Recipe
Favorite Meal Pattern
```

Dar fiecare tip trebuie diferențiat în domain model.

---

# 76. History

Trebuie să existe istoric pentru:

* meals;
* products;
* weights;
* prices;
* grocery trips;
* plans;
* recipes;
* preference events.

Istoricul alimentează Learning Engine.

---

# 77. Modes vs Modules

Unele concepte nu trebuie transformate în module separate.

Exemplu:

```text
Save Money
Nutrition First
Minimal Cooking
Variety
```

Sunt **planning strategies**, nu pagini.

Ele modifică ponderile motoarelor.

---

# 78. Planning Strategy

Conceptual:

```text
Balanced
Save Money
Minimal Cooking
Nutrition Max
High Variety
Custom
```

---

# 79. Balanced Strategy

Priorități aproximativ echilibrate:

* nutrition;
* preferences;
* cost;
* time;
* variety.

---

# 80. Save Money Strategy

Crește:

* cost weight;
* pantry usage;
* ingredient reuse;
* batch cooking.

---

# 81. Minimal Cooking Strategy

Crește:

* meal prep score;
* leftovers;
* batch reuse;
* simple recipes.

---

# 82. Nutrition Max Strategy

Crește:

* nutrient gap score;
* food diversity;
* micronutrient fit.

---

# 83. High Variety Strategy

Crește:

* novelty;
* cuisine diversity;
* recipe diversity.

Reduce repetarea.

---

# 84. Custom Strategy

Ulterior user poate controla:

```text
Nutrition        40%
Budget           20%
Time             20%
Variety          10%
Preference       10%
```

Nu este necesar pentru MVP.

---

# 85. Module State Communication

Ideal, modulele nu se actualizează unele pe altele prin logică ad-hoc.

Evenimente conceptuale:

```text
meal.logged
meal.changed
meal.skipped
pantry.updated
receipt.confirmed
weight.logged
plan.generated
shopping.purchased
recipe.rated
```

Alte module reacționează.

---

# 86. Example Event

```text
meal.logged
```

produce:

```text
NutritionState updated
NutrientGap recalculated
Pantry optionally decreased
Learning event stored
Progress adherence updated
```

---

# 87. System of Record

Trebuie definit ulterior tehnic, dar conceptual:

### Nutrition truth

Nutrition Engine + canonical nutrient data.

### Food availability truth

Pantry.

### Planned food truth

Meal Plan.

### Consumed food truth

Food Logs.

### Financial truth

Budget Transactions / Receipts.

### User preference truth

Explicit Preferences + Learning signals.

---

# 88. User Experience Hierarchy

Nu toate informațiile sunt egale.

## Level 1 — Action

> Eat this.

## Level 2 — Useful summary

```text
620 kcal
48g protein
```

## Level 3 — Explanation

> Why this meal?

## Level 4 — Advanced data

Full micronutrients, scoring, detailed nutrition.

Default UI trebuie să prioritizeze Level 1 și 2.

---

# 89. Main Product Loop

```text
UNDERSTAND
Who am I?
What do I need?
What do I like?

        ↓

PLAN
What should I eat?

        ↓

SHOP
What should I buy?

        ↓

COOK
What should I prepare?

        ↓

EAT / TRACK
What did I actually eat?

        ↓

LEARN
What worked?

        ↓

ADAPT
What should happen next?
```

Acesta este loop-ul principal al întregului produs.

---

# 90. Daily Loop

```text
Current state
    ↓
Generate recommendation
    ↓
Choose
    ↓
Eat
    ↓
Log
    ↓
Recalculate gaps
    ↓
Generate next recommendation
```

---

# 91. Weekly Loop

```text
Review week
    ↓
Set constraints
    ↓
Generate plan
    ↓
Generate groceries
    ↓
Shop
    ↓
Meal prep
    ↓
Execute
    ↓
Learn
    ↓
Next week improves
```

---

# 92. Household Loop

```text
Individual needs
      ↓
Shared meal search
      ↓
Personalized portions
      ↓
Combined cooking
      ↓
Shared groceries
      ↓
Individual tracking
```

---

# 93. Module Priority Classification

## Tier 1 — Product Core

Fără acestea produsul nu exprimă ideea principală:

* Profile;
* Household;
* Today;
* Planner;
* Recipes;
* Nutrition Engine;
* Nutrient Gap Engine;
* Recommendation Engine;
* Portion Solver.

---

## Tier 2 — Operational Value

Fac produsul mult mai util:

* Groceries;
* Pantry;
* Cook;
* Meal Prep;
* Preferences;
* Budget.

---

## Tier 3 — Intelligence & Convenience

Reduc friction și cresc diferențierea:

* Scan Meal;
* Scan Barcode;
* Scan Scale;
* Scan Receipt;
* Scan Fridge;
* Learning Engine;
* AI Coach.

---

## Tier 4 — Advanced

* price intelligence;
* advanced micronutrient optimization;
* sophisticated budget optimization;
* automatic adaptive targets;
* deep personalization;
* complex household roles.

Această clasificare nu reprezintă încă roadmap-ul oficial.

Va fi definit în `07 — MVP Scope`.

---

# 94. Product Surface Map

```text
APP
│
├── Today
│   ├── Nutrition Status
│   ├── Meals
│   ├── Generate Next Meal
│   ├── Quick Log
│   └── Daily Details
│
├── Plan
│   ├── Day
│   ├── Week
│   ├── Generate Week
│   ├── Constraints
│   └── Meal Details
│
├── Scan
│   ├── Meal
│   ├── Barcode
│   ├── Fridge
│   ├── Scale
│   └── Receipt
│
├── Cook
│   ├── Recipe
│   ├── Household Portions
│   ├── Meal Prep
│   ├── Containers
│   └── Leftovers
│
├── Groceries
│   ├── Current List
│   ├── Shopping Mode
│   ├── Products
│   └── Previous Trips
│
├── Pantry
│   ├── Fridge
│   ├── Freezer
│   ├── Cupboard
│   └── Expiring
│
├── Recipes
│   ├── Recommended
│   ├── Favorites
│   ├── Saved
│   └── Custom
│
├── Budget
│   ├── Weekly
│   ├── Monthly
│   ├── Meal Costs
│   └── Spending
│
├── Progress
│   ├── Weight
│   ├── Nutrition
│   ├── Adherence
│   └── Trends
│
└── Profile / Household
    ├── Members
    ├── Goals
    ├── Preferences
    ├── Schedule
    ├── Equipment
    └── Settings
```

---

# 95. Intelligence Surface Map

```text
INTELLIGENCE
│
├── Nutrition Engine
│
├── Nutrient Gap Engine
│
├── Recommendation Engine
│
├── Portion Solver
│
├── Weekly Planner
│
├── Meal Prep Engine
│
├── Grocery Optimizer
│
├── Budget Engine
│
├── Learning Engine
│
├── Vision Engine
│
└── AI Orchestration Layer
```

---

# 96. AI Orchestration Layer

Este util să diferențiem:

```text
AI Coach
```

de:

```text
AI Orchestration Layer
```

AI Coach este interfața.

Orchestration Layer interpretează intent și folosește sistemele potrivite.

Exemplu:

```text
User:
"Avem pui care expiră și vreau ceva sub 600 kcal."
```

Orchestrator:

```text
Constraint:
ingredient = chicken
priority = expiring

Nutrition:
maxCalories = 600

Action:
recommendMeal
```

Recommendation Engine execută decizia.

---

# 97. Product Boundary

Produsul trebuie să fie responsabil pentru:

* nutritional planning;
* meal selection;
* portioning;
* food logistics;
* groceries;
* pantry;
* budgeting;
* tracking;
* personalization.

Nu trebuie să devină implicit:

* full workout tracker;
* medical diagnostic platform;
* grocery marketplace;
* restaurant delivery service;
* social network.

Integrarea cu astfel de sisteme poate exista ulterior.

---

# 98. Most Important Module Relationship

Relația principală a produsului este:

```text
TODAY
  │
  ▼
NUTRITION STATE
  │
  ▼
NUTRIENT GAP
  │
  ▼
RECOMMENDATION
  │
  ▼
PORTION
  │
  ▼
MEAL
  │
  ▼
LOG
  │
  └──────────────► TODAY
```

Aceasta este bucla care face produsul fundamental diferit de un tracker clasic.

---

# 99. Most Important Household Relationship

```text
Member A target ────┐
                    │
Member B target ────┤
                    ▼
              Shared Recipe
                    │
             Portion Solver
                 ┌──┴──┐
                 ▼     ▼
             Portion A Portion B
                 │     │
                 └──┬──┘
                    ▼
              Cook Together
```

---

# 100. Most Important Weekly Relationship

```text
Nutrition
Preferences
Budget
Pantry
Schedule
Cooking frequency
Meal history
     │
     ▼
Weekly Planner
     │
     ├── Meal Plan
     ├── Meal Prep
     ├── Grocery List
     └── Cost Projection
```

---

# 101. Module Map Success Criteria

Module Map-ul este corect dacă:

1. fiecare responsabilitate are un singur owner principal;
2. logica critică nu este duplicată;
3. user-facing modules rămân simple;
4. motoarele pot fi dezvoltate și testate independent;
5. individual și household folosesc aceeași arhitectură;
6. weekly planning și daily planning folosesc aceleași concepte de bază;
7. AI-ul poate interacționa cu sistemul fără să devină sursa de adevăr;
8. noile features pot fi atașate unui modul existent fără să destabilizeze întreg produsul.

---

# 102. Core Module Rule

Pentru orice feature nou trebuie să putem răspunde:

> **Ce modul deține această responsabilitate?**

Dacă răspunsul este:

> „mai multe module în același timp”,

trebuie verificat dacă logica respectivă nu ar trebui extrasă într-un engine sau domain service comun.

---

# 103. Final Product Structure

La nivel conceptual, produsul devine:

```text
                    HOUSEHOLD
                        │
       ┌────────────────┼─────────────────┐
       ▼                ▼                 ▼
    PEOPLE          KITCHEN            MONEY
       │                │                 │
       ▼                ▼                 ▼
   Profiles           Pantry            Budget
 Preferences         Products           Prices
  Schedule           Groceries         Receipts
       │                │                 │
       └────────────────┼─────────────────┘
                        ▼
                INTELLIGENCE CORE
                        │
       ┌────────────────┼─────────────────┐
       ▼                ▼                 ▼
   Nutrition       Recommendation       Planning
      Gap              Portion          Meal Prep
       │                │                 │
       └────────────────┼─────────────────┘
                        ▼
                   DAILY LIFE
                        │
          ┌─────────────┼─────────────┐
          ▼             ▼             ▼
        PLAN           COOK           EAT
          │             │             │
          └─────────────┴─────────────┘
                        │
                        ▼
                      LEARN
                        │
                        └──────► next decision
```

---

# 104. Guiding Architecture Principle

Produsul trebuie să se comporte ca un singur sistem inteligent, chiar dacă intern este compus din mai multe module.

Utilizatorul nu trebuie să simtă:

> „acum folosesc calorie tracker-ul, apoi meal planner-ul, apoi grocery app-ul.”

Trebuie să simtă:

> **„Aplicația știe contextul meu și se ocupă de alimentația mea ca de un singur sistem.”**
