# 03 — Core Engines

## 1. Scopul documentului

Acest document definește sistemele interne care transformă datele despre utilizator și household în decizii alimentare concrete.

Motoarele trebuie să răspundă împreună la întrebări precum:

* De câte calorii și nutrienți are nevoie fiecare persoană?
* Ce a consumat deja?
* Ce îi mai lipsește astăzi?
* Ce masă se potrivește cel mai bine acum?
* Ce cantitate trebuie să mănânce fiecare?
* Putem găti aceeași mâncare pentru mai multe persoane?
* Ce preparate ar trebui distribuite pe întreaga săptămână?
* Cum reducem numărul de sesiuni de gătit?
* Ce ingrediente trebuie cumpărate?
* Cum ne încadrăm în buget?
* Cum folosim ce există deja în casă?
* Ce trebuie consumat înainte să expire?
* Cum învață sistemul ce îi place utilizatorului?
* Cum reacționează sistemul când utilizatorul schimbă planul?

Acest document stabilește:

* responsabilitatea fiecărui engine;
* datele de intrare;
* rezultatele produse;
* dependențele;
* ordinea de execuție;
* diferența dintre hard constraints și soft objectives;
* unde este permis AI-ul;
* unde este necesară logică deterministă;
* cum tratăm incertitudinea;
* cum testăm motoarele independent.

---

# 2. Core Architecture Principle

Aplicația nu trebuie să aibă un singur „AI brain” care primește toate datele și generează un răspuns.

Arhitectura trebuie să fie:

```text
STRUCTURED DATA
      ↓
DETERMINISTIC ENGINES
      ↓
OPTIMIZATION / RANKING
      ↓
AI INTERPRETATION & EXPLANATION
      ↓
USER
```

AI-ul poate:

* interpreta intenția;
* înțelege fotografii;
* genera explicații;
* propune variații;
* ajuta la clasificare;
* organiza context.

Dar sistemul trebuie să poată demonstra matematic:

* de unde vin kcal;
* cum s-au calculat gramajele;
* de ce o masă respectă un target;
* cum s-a calculat costul;
* cum s-a obținut shopping list-ul.

---

# 3. Engine Map

Nucleul produsului este format din:

```text
1. Nutrition Target Engine
2. Nutrition Calculation Engine
3. Daily Nutrition State Engine
4. Nutrient Gap Engine
5. Constraint Engine
6. Recipe Eligibility Engine
7. Recommendation Engine
8. Portion Solver
9. Household Meal Solver
10. Weekly Planning Engine
11. Meal Prep Engine
12. Pantry Engine
13. Grocery Optimizer
14. Product Resolution Engine
15. Budget Engine
16. Price Intelligence Engine
17. Learning Engine
18. Variety Engine
19. Waste & Expiry Engine
20. Adaptation / Replanning Engine
21. Confidence & Uncertainty Engine
22. AI Orchestration Layer
```

Unele pot fi implementate inițial ca servicii în aceeași aplicație.

Separarea de aici este **logică**, nu neapărat microservices.

---

# 4. Engine Dependency Overview

```text
                      MEMBER PROFILE
                            │
                            ▼
                 Nutrition Target Engine
                            │
                            ▼
                  Nutrition Targets
                            │
                            ▼
FOOD DATA ──────► Nutrition Calculation Engine
                            │
                            ▼
                    Nutrition State
                            │
                            ▼
                    Nutrient Gap Engine
                            │
                            ▼
                        GAP PROFILE
                            │
           ┌────────────────┼───────────────────┐
           │                │                   │
           ▼                ▼                   ▼
     Preferences         Pantry             Schedule
           │                │                   │
           └────────────────┼───────────────────┘
                            ▼
                    Constraint Engine
                            │
                            ▼
                  Recipe Eligibility
                            │
                            ▼
                 Recommendation Engine
                            │
                            ▼
                    Portion Solver
                            │
                            ▼
                  Household Meal Solver
                            │
                            ▼
                         MEAL
```

Pentru săptămână:

```text
Nutrition Targets
Preferences
Schedule
Pantry
Budget
Locked Meals
Cooking Strategy
Meal History
      │
      ▼
Weekly Planning Engine
      │
      ├── Recommendation Engine
      ├── Portion Solver
      ├── Household Solver
      ├── Variety Engine
      ├── Budget Engine
      ├── Waste Engine
      └── Meal Prep Engine
      │
      ▼
Weekly Plan
      │
      ▼
Grocery Optimizer
```

---

# 5. Fundamental Data Classes

Motoarele trebuie să consume obiecte standardizate.

---

## 5.1 NutritionTarget

Pentru fiecare membru:

```text
NutritionTarget

energy:
  calories

macros:
  protein
  carbohydrates
  fat
  fiber

micronutrients:
  calcium
  iron
  magnesium
  potassium
  sodium
  vitamin_a
  vitamin_c
  vitamin_d
  vitamin_b12
  folate
  etc.

ranges:
  minimum
  target
  maximum
```

Important:

Nu toți nutrienții trebuie tratați ca:

> „trebuie să ating exact 100%”.

Unii au:

* minimum desirable;
* target;
* tolerable upper level;
* safety maximum.

Detaliile vor fi definite în `04 — Nutrition Spec`.

---

# 6. Nutrition Target Engine

## Rol

Calculează sau selectează targeturile nutriționale individuale.

---

## Input

* age;
* biological sex when relevant;
* height;
* weight;
* activity;
* goal;
* rate of weight change;
* workout context;
* manually configured targets;
* professional-defined targets.

---

## Modes

### Auto

Sistemul calculează.

### Manual

User setează valorile.

### Professional

Targeturile sunt introduse extern și sistemul nu le recalculază automat.

### Adaptive

Ulterior, sistemul poate propune ajustări bazate pe evoluție.

---

## Output

```text
NutritionTargetProfile
```

cu:

```text
calculation_source
calculated_at
confidence
version
```

---

## Important Principle

Nutrition Target Engine nu trebuie să modifice silencios targeturile.

Dacă targeturile se schimbă:

> sistemul trebuie să păstreze istoricul.

---

# 7. Nutrition Calculation Engine

## Rol

Este calculatorul nutrițional canonical al aplicației.

Dacă orice alt modul are nevoie de kcal sau nutrients, apelează acest engine.

---

## Input

Poate primi:

```text
Food + quantity
Product + quantity
Recipe + ingredient quantities
Meal + portions
Cooking batch
```

---

## Output

```text
NutritionVector
```

De exemplu:

```text
calories: 624
protein: 49.2
carbs: 71.4
fat: 17.1
fiber: 9.8
calcium: ...
iron: ...
...
```

---

# 8. NutritionVector

Toată nutriția trebuie să folosească același model.

Conceptual:

```text
NutritionVector = [
 calories,
 protein,
 carbs,
 fat,
 fiber,
 calcium,
 iron,
 magnesium,
 potassium,
 sodium,
 ...
]
```

Această reprezentare face posibil:

* adunarea meselor;
* compararea cu targeturi;
* optimizarea;
* scoring-ul.

---

# 9. Food Data Priority

Pentru valori nutriționale, prioritatea trebuie să fie:

```text
1. Product verified nutrition
2. Trusted external food database
3. Generic canonical food data
4. User-entered nutrition
5. AI estimate
```

AI estimate trebuie tratat separat ca informație cu incertitudine mare.

---

# 10. Cooked vs Raw State

Nutrition Engine trebuie să cunoască explicit:

```text
RAW
COOKED
DRAINED
PREPARED
```

Exemplu:

```text
100g rice dry ≠ 100g rice cooked
```

Nu trebuie permisă ambiguitatea.

Fiecare food quantity trebuie să aibă:

```text
amount
unit
preparation_state
```

---

# 11. Daily Nutrition State Engine

## Rol

Menține starea nutrițională curentă a unei zile.

---

## Pentru fiecare membru

```text
DailyNutritionState

target
consumed
planned
remaining
reserved
```

---

## Exemplu

```text
Target
2200 kcal

Consumed
840 kcal

Planned
760 kcal

Remaining unallocated
600 kcal
```

Este util să diferențiem:

### Remaining

Ce nu s-a consumat.

### Available

Ce mai poate fi alocat după mesele deja planificate.

---

# 12. Consumed vs Planned vs Reserved

Trebuie separate.

Exemplu:

```text
TARGET
2200

CONSUMED
800

LOCKED DINNER
850

UNALLOCATED
550
```

Dacă utilizatorul spune:

> „Vreau burger diseară.”

burgerul intră în:

```text
reserved / planned
```

iar recommendation engine-ul pentru prânz vede doar bugetul nutrițional rămas real.

---

# 13. Nutrient Gap Engine

## Rol

Transformă:

```text
Target - current state
```

într-un profil utilizabil pentru recomandări.

---

## Nu trebuie să fie doar subtraction

Pentru fiecare nutrient trebuie să determine:

```text
deficiency urgency
remaining amount
acceptable range
upper-bound risk
priority
```

---

# 14. Gap Types

### Critical Gap

Foarte important pentru optimizarea curentă.

### Moderate Gap

Ar fi bine să fie acoperit.

### Satisfied

Nu trebuie prioritizat.

### Near Upper Limit

Nu mai trebuie favorizat.

### Exceeded

Recommendation engine-ul trebuie să evite creșterea inutilă.

---

# 15. Gap Profile Example

```text
protein:
 remaining: 62g
 priority: HIGH

fiber:
 remaining: 14g
 priority: HIGH

calcium:
 coverage: 72%
 priority: MEDIUM

vitamin_c:
 coverage: 35%
 priority: HIGH

sodium:
 coverage: 112%
 priority: AVOID_MORE
```

---

# 16. Macro and Micro Separation

Engine-ul trebuie să permită două niveluri.

### Primary constraints

* calories;
* protein;
* carbs;
* fats.

### Optimization dimensions

* fiber;
* micronutrients;
* food diversity.

Nu vrem ca o cantitate minusculă dintr-un micronutrient să distrugă complet optimizarea macro.

---

# 17. Nutrient Priority Function

Conceptual:

```text
priority(nutrient) =
  gap_size
  × importance_weight
  × remaining_meals_factor
  × upper_limit_factor
```

Exemplu:

Dacă este ora 20:00 și mai există o singură masă:

> gaps importante primesc greutate mai mare.

Dacă este ora 08:00:

> există mai mult spațiu de recuperare.

---

# 18. Constraint Engine

## Rol

Centralizează toate regulile care trebuie aplicate înainte de recomandare.

Este important să nu avem constraints implementate separat peste tot.

---

# 19. Constraint Classes

## Hard constraints

Nu pot fi încălcate.

Exemple:

* allergy;
* prohibited food;
* dietary restriction;
* unavailable equipment;
* meal locked;
* member not participating;
* food safety rule.

---

## Strong constraints

Pot fi relaxate doar cu aprobarea explicită a userului.

Exemple:

* budget maximum;
* max preparation time;
* office lunch requirements.

---

## Soft constraints

Intră în scoring.

Exemple:

* prefer chicken;
* variety;
* ingredient reuse;
* cost minim;
* food preference;
* cuisine preference.

---

# 20. Constraint Resolution

Înainte de recomandare:

```text
All recipes
   ↓
Hard filters
   ↓
Logistical filters
   ↓
Feasible candidates
   ↓
Soft scoring
```

Nu:

```text
score everything
then discover allergy conflict
```

---

# 21. Constraint Object

Conceptual:

```text
Constraint

type
scope
strength
source
value
valid_from
valid_until
```

---

## Exemple

```text
ingredient != mushrooms
strength: HARD
```

```text
prep_time <= 20min
strength: STRONG
```

```text
prefer chicken
strength: SOFT
```

---

# 22. Recipe Eligibility Engine

## Rol

Decide ce rețete pot fi luate în calcul.

Recommendation Engine nu trebuie să primească toate rețetele existente.

---

## Filters

* meal type;
* restrictions;
* allergies;
* available equipment;
* schedule;
* location;
* meal prep suitability;
* lunchbox suitability;
* ingredients forbidden;
* time constraints;
* recipe availability.

---

# 23. Candidate Pool

Exemplu:

```text
Database:
1,500 recipes

After restrictions:
1,120

Meal = lunch:
420

Office compatible:
190

Prep <= 20 min:
85

Candidate pool:
85
```

Abia acestea intră în ranking.

---

# 24. Recommendation Engine

## Rol

Alege **ce preparat** este cel mai potrivit în context.

Nu calculează targeturi.

Nu calculează singur nutriția.

Nu decide exact gramajele.

Alege recipe candidates.

---

# 25. Recommendation Context

Input:

```text
RecommendationContext

member(s)
meal_slot
nutrition_gap
planned_future_meals
preferences
learned_preferences
recent_meals
pantry
expiry
budget
schedule
equipment
location
cooking_time
planning_strategy
```

---

# 26. Candidate Score

Conceptual:

```text
Score(recipe) =

NutritionFit
+ MicronutrientFit
+ PreferenceFit
+ PantryFit
+ BudgetFit
+ ScheduleFit
+ MealPrepFit
+ IngredientReuse
+ ExpiryFit
+ VarietyFit
+ LearningFit
```

---

# 27. Weighted Score

Exemplu inițial:

```text
Nutrition fit          25%
Micronutrient fit      12%
Preference fit         15%
Pantry fit             10%
Budget fit             10%
Time/logistics fit     10%
Meal prep fit           6%
Ingredient reuse        4%
Expiry optimization     4%
Variety                  4%
```

Aceste procente NU sunt încă reguli finale.

Trebuie calibrate și pot varia în funcție de Planning Strategy.

---

# 28. Planning Strategy Changes Weights

## Balanced

Weights echilibrate.

## Nutrition Max

Crește:

```text
nutrition
micronutrients
food diversity
```

## Save Money

Crește:

```text
budget
pantry
ingredient reuse
batch cooking
```

## Minimal Cooking

Crește:

```text
meal prep
batch compatibility
leftover reuse
prep simplicity
```

## High Variety

Crește:

```text
novelty
recipe rotation
cuisine diversity
```

---

# 29. Recommendation Engine Output

Nu produce doar un nume.

Produce:

```text
RecommendationCandidate

recipe_id
score
score_breakdown
reason_codes
constraints_satisfied
portioning_requirements
estimated_cost_range
confidence
```

---

# 30. Reason Codes

Exemple:

```text
HIGH_PROTEIN_GAP_MATCH
USES_EXPIRING_CHICKEN
LUNCHBOX_FRIENDLY
FAVORITE_RECIPE
LOW_COST
HIGH_FIBER
NOT_EATEN_RECENTLY
PANTRY_MATCH_80
```

AI Coach poate transforma aceste coduri în explicații naturale.

---

# 31. Recipe Diversity Guard

Recommendation Engine nu trebuie să returneze:

```text
Chicken Rice Bowl
Chicken Rice Bowl spicy
Chicken Rice Bowl garlic
Chicken Rice Bowl Mediterranean
```

ca patru opțiuni aparent diferite.

Trebuie să existe diversity între top candidates.

---

# 32. Portion Solver

## Rol

După alegerea rețetei:

> Ce cantități trebuie să aibă ingredientele pentru persoana respectivă?

Aici este una dintre cele mai importante componente matematice.

---

# 33. Portion Solver Inputs

```text
Recipe
Member nutrition state
Meal target
Remaining nutrition gaps
Recipe scaling rules
Ingredient min/max
Locked ingredient quantities
Personal preferences
```

---

# 34. Recipe Variable Model

O rețetă trebuie să diferențieze:

### Fixed ingredients

Nu se modifică ușor.

```text
spices
garlic
small sauce components
```

### Scalable ingredients

```text
chicken
rice
potatoes
```

### Optional ingredients

```text
cheese
sauce
toppings
```

### Substitutable ingredients

```text
rice ↔ potatoes
chicken ↔ turkey
```

---

# 35. Example Recipe Variables

```text
Chicken Rice Bowl

Chicken:
min 100g
preferred 160g
max 250g

Rice cooked:
min 80g
preferred 180g
max 300g

Vegetables:
min 150g
preferred 250g
max 400g

Oil:
min 3g
preferred 8g
max 15g
```

---

# 36. Optimization Goal

Solver-ul poate încerca să minimizeze:

```text
error =
 calories_error
+ protein_error
+ carbs_error
+ fat_error
+ nutrient_gap_error
+ recipe_distortion_penalty
```

---

# 37. Recipe Distortion Penalty

Foarte important.

Matematic ai putea face:

```text
400g chicken
20g rice
0g vegetables
```

și poate macros ies bine.

Dar nu mai este o masă normală.

Trebuie penalizată abaterea de la proporțiile culinare rezonabile.

---

# 38. Portion Quality Constraints

Porția trebuie să respecte:

* min/max ingredient;
* ratio limits;
* realistic serving size;
* palatability;
* calorie range;
* protein minimum where relevant.

---

# 39. Solver Output

```text
PersonalizedPortion

ingredient quantities
nutrition vector
target deviation
solver score
```

---

# 40. Household Meal Solver

## Rol

Rezolvă una dintre cele mai importante promisiuni ale produsului:

> aceeași masă, porții diferite.

---

# 41. Process

```text
Shared Recipe
     ↓
Solve Member A
     ↓
Solve Member B
     ↓
Solve Member C
     ↓
Combine quantities
```

---

# 42. Household Compatibility

Uneori aceeași rețetă nu poate satisface rezonabil pe toată lumea.

Trebuie calculat:

```text
shared_meal_compatibility_score
```

---

## Exemplu bun

```text
Chicken Rice Bowl
A: 680 kcal
B: 490 kcal

Compatibility: 96%
```

---

## Exemplu slab

Persoana A:

```text
high-carb need
```

Persoana B:

```text
very low-carb constraint
```

Poate fi mai bine:

```text
same protein + vegetables
different side dish
```

---

# 43. Shared Meal Decomposition

Household Solver poate folosi:

```text
Shared Base
+
Personal Components
```

Exemplu:

```text
Shared:
Chicken
Vegetables
Sauce

A:
Rice 220g

B:
Rice 120g
```

Sau:

```text
A:
Rice

B:
Potatoes
```

fără să transforme masa în două preparate complet diferite.

---

# 44. Household Optimization Hierarchy

Sistemul încearcă în ordinea:

```text
1. Same recipe, different portions
2. Same base, different side
3. Same core ingredients, minor variation
4. Separate meal
```

Separarea completă este ultima variantă.

---

# 45. Weekly Planning Engine

## Rol

Rezolvă întreaga săptămână ca pe o problemă de optimizare.

Nu trebuie să facă:

```text
Generate Monday
Generate Tuesday
Generate Wednesday
```

independent.

Pentru că deciziile unei zile influențează:

* cumpărăturile;
* leftovers;
* ingredient reuse;
* budget;
* cooking schedule;
* variety.

---

# 46. Weekly Planning Inputs

```text
Household
Member nutrition targets
Week schedule
Meal participation
Office meals
Training
Locked meals
Eating out
Cooking frequency
Budget
Pantry
Expiring items
Preferences
Learned preferences
Recipe history
Planning strategy
```

---

# 47. Weekly Planning Objectives

Trebuie optimizate simultan:

```text
nutrition quality
individual target adherence
household meal sharing
budget
number of cooking sessions
ingredient reuse
food waste
variety
preference satisfaction
meal-prep compatibility
schedule fit
```

---

# 48. Weekly Planning Is Multi-Objective Optimization

Nu există „cea mai bună săptămână” absolut.

Există:

> cea mai bună săptămână pentru ponderile și constrângerile curente.

Conceptual:

```text
WeeklyScore =
 NutritionScore
+ PreferenceScore
+ BudgetScore
+ LogisticsScore
+ VarietyScore
+ PantryScore
+ WasteScore
```

---

# 49. Weekly Planning Strategy

Proces recomandat:

### Step 1

Fixează toate constrângerile.

### Step 2

Rezervă locked meals.

### Step 3

Rezervă external meals.

### Step 4

Identifică shared meal opportunities.

### Step 5

Identifică cooking sessions.

### Step 6

Alege meal-prep anchors.

### Step 7

Completează mesele rămase.

### Step 8

Optimizează micronutrients.

### Step 9

Optimizează ingredient overlap.

### Step 10

Calculează cost.

### Step 11

Iterează dacă bugetul sau alte constraints sunt depășite.

---

# 50. Meal Anchor Concept

Unele mese pot servi drept:

```text
Weekly Anchors
```

Exemplu:

```text
Sunday Chicken Batch
```

poate alimenta:

* Sunday dinner;
* Monday lunch;
* Tuesday lunch.

Planner-ul trebuie să construiască în jurul acestor anchors.

---

# 51. Global Optimization vs Greedy

Nu vrem algoritm complet greedy:

```text
alege cea mai bună masă acum
```

pentru fiecare slot.

Poate produce:

* ingrediente complet diferite;
* buget mare;
* prea mult cooking.

Weekly Planner trebuie să poată accepta o masă cu score local 88 în loc de 94 dacă:

> reduce cu 100 lei costul total și elimină o sesiune de gătit.

---

# 52. Meal Prep Engine

## Rol

Transformă Meal Plan în cooking operations.

---

# 53. Meal Prep Inputs

* planned meals;
* recipe shelf life;
* storage requirements;
* reheating compatibility;
* number of cooking sessions;
* freezer support;
* meal participation;
* portions.

---

# 54. Meal Prep Output

```text
CookingSession

date
recipes
total quantities
estimated prep time
storage instructions
containers
meal allocations
```

---

# 55. Batch Compatibility

Nu toate mesele trebuie gătite împreună.

Engine-ul trebuie să țină cont de:

* shelf life;
* texture degradation;
* freezer compatibility;
* food safety;
* reheating.

---

# 56. Meal Prep Scheduling

Exemplu:

User:

```text
Cook maximum 3 times/week
```

Engine:

```text
Sunday
Wednesday
Friday
```

și atribuie meals.

---

# 57. Container Solver

Pentru batch:

```text
Chicken Pasta
Total cooked: X
```

trebuie să producă:

```text
Container A
Member 1
Monday lunch
420g

Container B
Member 2
Monday lunch
310g
```

---

# 58. Leftover Engine

Leftover nu trebuie tratat ca accident.

Este o resursă.

---

## Leftover Entity

```text
Recipe
quantity
nutrition
created_at
expires_at
storage
allocated/unallocated
```

Recommendation Engine trebuie să poată prioritiza leftovers.

---

# 59. Pantry Engine

## Rol

Menține starea estimată a alimentelor disponibile.

---

# 60. Pantry State Complexity

Nu toate cantitățile vor fi exacte.

Trebuie suportate:

```text
EXACT
ESTIMATED
STATUS_ONLY
```

---

## Exemple

```text
Rice
624g
confidence: exact
```

```text
Milk
~half bottle
confidence: estimated
```

```text
Eggs
In stock
confidence: status_only
```

---

# 61. Pantry Mutation Sources

* manual entry;
* receipt;
* barcode;
* fridge scan;
* grocery purchase;
* cooking consumption;
* user correction;
* discard;
* expiry.

---

# 62. Pantry Consumption

Când utilizatorul gătește:

> sistemul poate scădea ingredientele.

Dar trebuie să permitem diferența dintre:

```text
planned usage
actual usage
```

---

# 63. Waste & Expiry Engine

## Rol

Calculează riscul de waste și îl introduce în planning.

---

## Inputs

```text
quantity
expiry
estimated consumption
future planned recipes
storage
```

---

# 64. Waste Risk

Conceptual:

```text
WasteRisk =
 probability_unused_before_expiry
 × quantity
 × price
```

Recommendation Engine poate favoriza ingrediente cu waste risk ridicat.

---

# 65. Expiry Priority

Exemplu:

```text
Chicken:
expires tomorrow
high priority

Yogurt:
expires in 4 days
medium

Rice:
months
low
```

---

# 66. Grocery Optimizer

## Rol

Transformă ingredient demand într-o listă reală de cumpărături.

---

# 67. Input

```text
Weekly Ingredient Demand
Pantry
Preferred Products
Package Sizes
Prices
Stores
Budget
```

---

# 68. Core Formula

Pentru fiecare ingredient:

```text
needed =
 planned_demand
 - usable_pantry
 - allocated_leftovers
```

---

# 69. Package Optimization

Dacă:

```text
needed = 1430g
```

și package:

```text
400g
```

atunci:

```text
4 packages
1600g
surplus = 170g
```

Surplusul trebuie introdus în pantry forecast.

---

# 70. Package Waste Cost

Uneori:

```text
Product A
cheaper per kg
large package
```

poate produce mai mult waste decât:

```text
Product B
slightly more expensive
smaller package
```

Grocery Optimizer trebuie să poată considera:

```text
effective_cost =
 purchase_price
 + expected_waste_cost
```

în versiunile avansate.

---

# 71. Product Resolution Engine

## Rol

Leagă:

```text
generic Food
```

de:

```text
real Product
```

---

## Exemplu

Recipe:

```text
Greek Yogurt
```

Household preferred:

```text
Pilos Greek Yogurt 2%
```

Product Resolver selectează produsul respectiv.

---

# 72. Product Selection Priority

```text
1. User preferred product
2. Household preferred product
3. Previously purchased
4. Best-known compatible product
5. Generic food fallback
```

---

# 73. Product Substitution

Dacă produsul nu este disponibil:

```text
same food class
similar nutrition
similar package
similar price
```

dar userul trebuie să poată confirma.

---

# 74. Budget Engine

## Rol

Menține și proiectează costurile.

---

# 75. Cost States

Trebuie diferențiate:

```text
ESTIMATED
PROJECTED
ACTUAL
```

---

## Exemplu

Înainte de shopping:

```text
Projected week:
472 lei
```

După bon:

```text
Actual:
489 lei
```

---

# 76. Meal Cost

```text
MealCost =
 Σ ingredient quantity
 × unit price
```

Pentru ingrediente cumpărate în pachete, trebuie diferențiat:

### Consumption cost

Costul efectiv al cantității consumate.

### Purchase cash-out

Cât ai plătit efectiv pe pachet.

Ambele sunt utile.

---

# 77. Example

Ai nevoie de 100g parmezan.

Pachet:

```text
200g = 20 lei
```

### Meal consumption cost

```text
10 lei
```

### Grocery cash cost

```text
20 lei
```

Nu trebuie confundate.

---

# 78. Weekly Budget Constraint

Dacă user setează:

```text
500 lei
```

Weekly Planner trebuie să lucreze cu:

```text
projected purchase cost
```

nu doar costul consumat.

---

# 79. Budget Relaxation

Dacă nu există plan feasible sub buget:

nu trebuie să inventăm.

Sistemul spune:

```text
Lowest feasible projected cost: 537 lei.
```

și poate sugera:

* relax budget;
* reduce variety;
* replace expensive ingredients;
* use more pantry.

---

# 80. Price Intelligence Engine

## Rol

Menține cea mai bună estimare a prețului unui produs.

---

# 81. Price Sources

Prioritate:

```text
1. Recent verified receipt
2. User-entered price
3. Store-specific known price
4. Historical median
5. Generic estimate
```

---

# 82. Price History

```text
Product
Store
Date
Price
Package
Confidence
```

---

# 83. Price Freshness

Prețurile vechi trebuie să primească confidence mai mic.

Exemplu:

```text
7 days → high confidence
90 days → medium
1 year → low
```

---

# 84. Learning Engine

## Rol

Învață preferințele reale fără să transforme comportamentul recent într-o regulă absolută.

---

# 85. Learning Dimensions

Trebuie să învețe separat:

```text
food affinity
recipe affinity
cuisine affinity
texture affinity
meal-type affinity
time-of-day affinity
repetition tolerance
price sensitivity
novelty preference
prep tolerance
```

---

# 86. Explicit vs Implicit Preferences

## Explicit

User spune:

```text
I love eggs.
```

Are weight mare.

## Implicit

User acceptă 8/10 egg meals.

Are weight gradual.

---

# 87. Learning Events

```text
meal_viewed
meal_accepted
meal_regenerated
meal_skipped
meal_replaced
meal_favorited
meal_rated
ingredient_removed
ingredient_added
recipe_repeated
recipe_completed
```

---

# 88. Negative Signals

Nu toate acțiunile înseamnă dislike.

Exemplu:

`Regenerate`

poate însemna:

* nu am chef azi;
* nu-mi place rețeta;
* nu am ingredientele;
* vreau varietate.

De aceea Learning Engine trebuie să folosească context.

---

# 89. Feedback Reasons

Ideal, uneori userul poate spune:

```text
Not today
Don't like it
Too much work
Too expensive
Missing ingredients
Had it recently
```

Acestea produc semnale complet diferite.

---

# 90. Preference Decay

Preferințele implicite foarte vechi pot pierde treptat din weight.

Explicit:

```text
Never mushrooms
```

nu trebuie să expire automat.

---

# 91. Variety Engine

## Rol

Controlează repetarea și explorarea.

---

# 92. Variety Dimensions

Nu doar recipe repetition.

Trebuie urmărite:

* recipe;
* protein source;
* carbohydrate source;
* cuisine;
* cooking style;
* vegetable diversity;
* breakfast type.

---

# 93. Variety Score

Exemplu:

Dacă în ultimele 3 zile ai avut:

```text
chicken
chicken
chicken
```

o a patra masă cu chicken primește penalty.

Dar dacă user:

```text
Love chicken
Variety = Familiar
```

penalty este mai mic.

---

# 94. Novelty Budget

Concept util:

```text
80% familiar
20% exploration
```

pentru un user cu Variety moderat.

Weekly Planner poate controla noutatea la nivel de săptămână.

---

# 95. Adaptation / Replanning Engine

## Rol

Reacționează când realitatea diferă de plan.

Aceasta este o componentă critică.

---

# 96. Replan Triggers

* skipped meal;
* different portion;
* unplanned meal;
* restaurant;
* ingredient unavailable;
* household member absent;
* changed budget;
* changed schedule;
* craving;
* missed cooking session.

---

# 97. Replanning Principle

Nu regenerăm totul inutil.

Trebuie să avem:

```text
minimum necessary change
```

---

# 98. Replan Scope

### Meal

Înlocuiește o singură masă.

### Remaining day

Păstrează istoricul.

### Remaining week

Păstrează tot ce este consumed și locked.

### Shopping

Recalculează doar necesarul relevant.

---

# 99. Plan Stability Penalty

Un planner bun nu trebuie să schimbe 14 mese pentru că userul a schimbat una.

Trebuie să existe:

```text
change_penalty
```

care favorizează păstrarea planului existent.

---

# 100. Example Replanning

Plan:

```text
Lunch 600 kcal
Dinner 700 kcal
```

User mănâncă:

```text
Lunch 900 kcal
```

System:

```text
Consumed state updated
Dinner target recalculated
```

Poate ajusta dinner portion.

Nu trebuie neapărat să schimbe complet dinner recipe.

---

# 101. Locked Meal Semantics

Locked trebuie să aibă niveluri.

### Recipe Locked

Vreau exact preparatul.

### Meal Type Locked

Vreau paste, dar rețeta poate varia.

### Ingredient Locked

Vreau somon.

### Calorie Reservation

Rezerv aproximativ 900 kcal pentru restaurant.

Acestea trebuie diferențiate.

---

# 102. Confidence & Uncertainty Engine

## Rol

Produsul va lucra cu date imperfecte.

Trebuie să știe **cât de sigur este**.

---

# 103. Confidence Sources

Exemple:

### Weight photo

```text
94.7kg
confidence 0.98
```

### Meal photo

```text
Chicken
confidence 0.95

Quantity 170g
confidence 0.56
```

### Receipt product match

```text
Pilos Yogurt
confidence 0.82
```

---

# 104. Confidence Levels

Conceptual:

```text
VERIFIED
HIGH
MEDIUM
LOW
UNKNOWN
```

---

# 105. Precision Policy

Dacă avem:

```text
estimated chicken portion
```

nu afișăm:

```text
173.4g
```

ca și cum ar fi exact.

Afișăm:

```text
~170g
```

---

# 106. Confirmation Threshold

Exemplu conceptual:

```text
confidence > high threshold
→ quick confirm

medium
→ ask user

low
→ show candidate options
```

---

# 107. AI Orchestration Layer

## Rol

Leagă limbajul natural și vision-ul de motoarele deterministe.

---

# 108. Example

User:

> „Mâine ea merge la birou, eu am sală și avem pui care expiră. Vreau să nu cheltuim peste 50 lei.”

AI Orchestration produce:

```text
date = tomorrow

member_partner:
 lunch_location = office
 lunchbox_required = true

member_user:
 workout = true

ingredient_priority:
 chicken
 expiry_priority = high

budget:
 max_daily_purchase_cost = 50

action:
 replan_day
```

Apoi engine-urile execută.

---

# 109. AI Does Not Return Final Nutrition Truth

Flow corect:

```text
User language
↓
AI intent extraction
↓
Structured request
↓
Core engines
↓
Structured result
↓
AI explanation
```

---

# 110. AI Function Classes

### Interpretation

„Vreau ceva ușor.”

→ `meal_style = light`

### Extraction

Bon / imagine / conversație.

### Ranking assistance

Poate ajuta semantic la similaritate.

### Explanation

De ce această masă?

### Recipe authoring assistance

Poate propune recipe template, dar acesta trebuie validat nutrițional.

---

# 111. AI Recipe Generation

Dacă permitem AI să creeze o rețetă nouă:

```text
AI creates recipe structure
↓
Ingredients normalized
↓
Nutrition Engine calculates
↓
Constraint validator
↓
Recipe quality validation
↓
Eligible for recommendation
```

Nu intră direct în planner.

---

# 112. Core Decision Pipeline — Next Meal

Aceasta este una dintre cele mai importante secvențe.

```text
1. Identify meal context

2. Load member/household

3. Load daily nutrition state

4. Load future locked meals

5. Calculate nutrient gaps

6. Build constraints

7. Filter eligible recipes

8. Score candidates

9. Select diverse top candidates

10. Solve portions

11. Validate nutrition

12. Calculate cost

13. Generate explanation

14. Present user
```

---

# 113. Core Decision Pipeline — Shared Meal

```text
1. Identify participants

2. Build individual nutrition states

3. Calculate each nutrient gap

4. Find recipes compatible with all

5. Rank shared recipes

6. Solve individual portions

7. Calculate shared cooking quantities

8. Validate each person's result

9. Calculate household cost

10. Present
```

---

# 114. Core Decision Pipeline — Weekly Plan

```text
1. Build WeeklyPlanningContext

2. Apply hard constraints

3. Lock fixed meals/events

4. Identify shared meal opportunities

5. Identify meal-prep opportunities

6. Generate candidate weekly structures

7. Populate anchor meals

8. Fill remaining slots

9. Solve portions for each member

10. Calculate nutrition per day

11. Optimize micronutrient coverage

12. Optimize variety

13. Optimize ingredient reuse

14. Optimize cooking sessions

15. Calculate pantry usage

16. Generate grocery demand

17. Calculate package purchases

18. Calculate projected cost

19. Validate budget

20. Iterate if needed

21. Produce final plan
```

---

# 115. Optimization Hierarchy

Când obiectivele intră în conflict, avem nevoie de ordine.

Baseline recomandat:

```text
1. Safety
2. Hard dietary constraints
3. Nutrition viability
4. User-stated constraints
5. Household feasibility
6. Logistics
7. Budget
8. Preferences
9. Variety
10. Convenience optimizations
```

Planning Strategy poate modifica doar nivelurile inferioare.

Nu poate muta:

```text
budget
```

peste:

```text
allergy safety
```

---

# 116. Hard Safety Layer

Înainte ca orice recomandare să ajungă la user:

```text
Safety Validator
```

trebuie să verifice:

* allergies;
* prohibited ingredients;
* known restrictions;
* unsafe upper nutrition limits where appropriate;
* problematic food storage;
* expired food.

---

# 117. Feasibility

Uneori constraints sunt incompatibile.

Exemplu:

```text
500 kcal dinner
60g protein
vegan
only pantry ingredients
pantry = bread + tomatoes
```

Nu există soluție.

Engine-ul trebuie să returneze:

```text
NO_FEASIBLE_SOLUTION
```

nu să inventeze.

---

# 118. Constraint Relaxation

Pentru soft constraints:

```text
Constraint Solver
```

poate relaxa în ordine.

Exemplu:

```text
1. variety
2. cuisine preference
3. pantry-only
4. prep time
```

dar spune userului ce a relaxat dacă este relevant.

---

# 119. Recipe Scoring vs Portion Solving

Trebuie clar separate.

### Recipe Scoring

> Ce mâncare?

### Portion Solver

> Cât din fiecare ingredient?

Nu combinăm acestea într-un singur prompt AI.

---

# 120. Daily vs Weekly Optimization

## Daily

Prioritizează:

* current nutrient gaps;
* current cravings;
* immediate pantry;
* convenience.

## Weekly

Prioritizează:

* consistency;
* ingredient reuse;
* cooking frequency;
* budget;
* variety;
* waste.

Aceeași rețetă poate avea scor diferit în cele două contexte.

---

# 121. Nutrition Across Time Horizons

Nu totul trebuie optimizat la nivel de zi.

Trebuie să existe:

### Per meal

Protein / calorie fit.

### Per day

Macros + major micronutrients.

### Rolling 7 days

Food diversity și micronutrients unde daily perfection nu este necesară.

Acest lucru va fi definit mai precis în Nutrition Spec.

---

# 122. Avoid Micronutrient Overfitting

Nu vrem:

> „azi ai doar 82% magnesium, deci trebuie obligatoriu să mănânci X”.

Unele targeturi trebuie evaluate:

```text
daily
```

iar altele mai util:

```text
rolling average
```

Nutriția reală nu trebuie transformată într-un joc obsesiv de 100% zilnic.

---

# 123. Recommendation Explainability

Orice recomandare trebuie să poată răspunde intern:

```text
Why this?
```

---

## Exemplu

```text
Chicken Fajita Bowl

+ 92% household compatibility
+ covers 48g protein gap
+ high vitamin C contribution
+ uses peppers expiring soon
+ 75% ingredients already in pantry
+ lunchbox-friendly
+ estimated 18 lei for household
```

UI-ul poate afișa doar 1–3 motive.

---

# 124. Recommendation Audit Record

Pentru debugging și learning, putem salva:

```text
recommendation_id
context_version
candidates
scores
selected
user_action
```

Nu este necesar să păstrăm absolut tot pe termen nelimitat, dar conceptul este important.

---

# 125. Engine Versioning

Algoritmii se vor schimba.

Rezultatele importante trebuie să poată avea:

```text
engine_version
```

Exemplu:

```text
portion_solver_v2
recommendation_v4
```

Ajută enorm la debugging.

---

# 126. Determinism

Cu același:

```text
context
constraints
engine version
```

motoarele matematice ar trebui să producă rezultate reproductibile.

AI poate introduce variație doar acolo unde este acceptată.

---

# 127. Testing Philosophy

Fiecare engine trebuie testabil fără UI.

---

# 128. Nutrition Engine Tests

Input:

```text
100g product
```

Output:

nutriție exactă.

---

# 129. Portion Solver Tests

Input:

```text
recipe
target
```

Validate:

* min/max;
* calorie deviation;
* protein target;
* realistic ratios.

---

# 130. Recommendation Tests

Scenario:

```text
user loves eggs
high protein gap
no eggs eaten last 3 days
eggs available
```

Egg-based breakfast trebuie să primească scor ridicat.

---

# 131. Constraint Tests

User:

```text
mushrooms = NEVER
```

Nicio recomandare nu conține mushrooms.

---

# 132. Household Solver Test

Members:

```text
2200 kcal target
1600 kcal target
```

Shared meal trebuie să producă porții distincte.

---

# 133. Budget Tests

Plan:

```text
budget 500
```

Projected purchases nu trebuie să depășească fără un explicit feasibility warning.

---

# 134. Replanning Tests

Dacă o singură masă este schimbată:

> restul săptămânii trebuie să rămână cât mai stabil.

---

# 135. Learning Tests

Un singur reject nu trebuie să transforme o rețetă în dislike permanent.

---

# 136. Engine Observability

Pentru development avem nevoie să putem vedea:

```text
Why did the engine choose this?
```

Dev mode poate afișa:

```text
Recipe A     91.2
Recipe B     88.7
Recipe C     80.4
```

cu breakdown.

Extrem de important pentru calibrare.

---

# 137. Performance Strategy

Nu toate motoarele trebuie să ruleze la fiecare click.

---

## Cacheable

* nutrition calculations;
* recipe nutrition;
* food data;
* product data.

## Recompute on state change

* Daily Nutrition State;
* Nutrient Gap.

## Heavy operation

* Weekly Planning.

---

# 138. Incremental Recalculation

Dacă user loghează breakfast:

nu regenerăm:

```text
entire weekly plan
```

Doar:

```text
daily state
gap
future recommendations
```

---

# 139. Core State Transition Example

```text
Breakfast planned
      ↓
User eats 80% portion
      ↓
Food Log
      ↓
Nutrition State recalculated
      ↓
Gap recalculated
      ↓
Lunch ranking changes
      ↓
Weekly plan remains stable
```

---

# 140. User Freedom

Motoarele sunt optimizatoare, nu autoritate.

User poate întotdeauna:

```text
override
lock
replace
adjust
ignore
```

Sistemul trebuie să se adapteze după.

---

# 141. Preference vs Nutrition Conflict

Dacă user iubește o masă care nu se potrivește perfect:

sistemul poate:

1. ajusta porția;
2. ajusta ingredientele;
3. compensa prin alte mese;
4. propune o alternativă.

Nu trebuie să elimine automat favoritele.

---

# 142. Budget vs Nutrition Conflict

În modul Save Money:

sistemul nu optimizează pentru:

```text
lowest possible cost
```

ci:

```text
lowest reasonable cost while maintaining nutrition constraints
```

---

# 143. Minimal Cooking Conflict

Dacă:

```text
Cook 1 time/week
```

nu poate produce un plan rezonabil din motive de siguranță/quality:

sistemul trebuie să spună:

> 2 cooking sessions would produce a materially better feasible plan.

---

# 144. Household Fairness

Weekly Planner nu trebuie să optimizeze masiv pentru un membru și să ignore altul.

Trebuie să existe fairness între:

```text
Member satisfaction scores
```

---

# 145. Household Objective

Conceptual:

```text
HouseholdScore =
 average(member_scores)
 - unfairness_penalty
```

---

# 146. Meal Acceptance Probability

Ulterior Learning Engine poate estima:

```text
P(user will accept recipe)
```

Recommendation Engine poate folosi probabilitatea.

O masă nutrițional perfectă, dar refuzată constant, este o recomandare proastă.

---

# 147. Practical Adherence Score

Pe termen lung putem optimiza:

```text
Nutrition Quality × Acceptance Probability
```

nu doar Nutrition Quality.

Acesta este un principiu extrem de important.

---

# 148. Real-World Optimization Principle

Planul perfect matematic care nu este executat are valoare zero.

Prin urmare:

```text
practical utility
>
theoretical perfection
```

---

# 149. Core Engine Boundaries

## Nutrition Target Engine

Decide:

> Cât are nevoie?

## Nutrition Calculation Engine

Decide:

> Ce conține alimentul/masa?

## Daily Nutrition State

Decide:

> Unde este acum?

## Nutrient Gap

Decide:

> Ce lipsește?

## Constraint Engine

Decide:

> Ce este permis?

## Recommendation Engine

Decide:

> Ce preparat?

## Portion Solver

Decide:

> Cât?

## Household Solver

Decide:

> Cum împărțim aceeași masă?

## Weekly Planner

Decide:

> Când mâncăm fiecare lucru?

## Meal Prep Engine

Decide:

> Când și cât gătim?

## Pantry Engine

Decide:

> Ce avem?

## Grocery Optimizer

Decide:

> Ce trebuie cumpărat?

## Budget Engine

Decide:

> Cât costă și ne încadrăm?

## Learning Engine

Decide:

> Ce am învățat despre preferințe?

## Replanning Engine

Decide:

> Ce trebuie schimbat după un eveniment?

## AI Orchestration

Decide:

> Ce vrea userul și ce engine trebuie apelat?

---

# 150. Engines Must Not Duplicate Truth

Exemplu:

Recommendation Engine nu păstrează propria valoare:

```text
protein_remaining
```

O cere de la Daily Nutrition State.

Weekly Planner nu păstrează propriile prețuri.

Le cere de la Budget / Price Engine.

---

# 151. Canonical Core Loop

Întregul produs poate fi redus la:

```text
OBSERVE
   ↓
UNDERSTAND STATE
   ↓
IDENTIFY GAPS
   ↓
APPLY CONSTRAINTS
   ↓
GENERATE OPTIONS
   ↓
OPTIMIZE
   ↓
ACT
   ↓
TRACK
   ↓
LEARN
   ↓
REPEAT
```

---

# 152. Daily Engine Loop

```text
Food consumed
      ↓
Nutrition Calculation
      ↓
Daily State
      ↓
Nutrient Gap
      ↓
Constraint Context
      ↓
Recommendation
      ↓
Portion
      ↓
Meal
      ↓
Food consumed
```

Bucla continuă după fiecare masă.

---

# 153. Weekly Engine Loop

```text
Household Context
      ↓
Weekly Constraints
      ↓
Weekly Optimization
      ↓
Meal Plan
      ↓
Meal Prep Plan
      ↓
Grocery Demand
      ↓
Shopping
      ↓
Execution
      ↓
Actual Data
      ↓
Learning
      ↓
Next Week
```

---

# 154. What Makes the Product Different

Diferențiatorul nu este un singur engine.

Este faptul că toate sunt conectate:

```text
WHAT I ATE
changes
WHAT I NEED

WHAT I NEED
changes
WHAT I SHOULD EAT

WHAT I SHOULD EAT
changes
WHAT WE COOK

WHAT WE COOK
changes
WHAT WE BUY

WHAT WE BUY
changes
WHAT WE HAVE

WHAT WE HAVE
changes
WHAT WE SHOULD EAT NEXT
```

Acesta este sistemul.

---

# 155. Architectural North Star

La orice decizie tehnică viitoare trebuie să putem răspunde:

> **Avem o sursă clară de adevăr, un engine clar responsabil și un rezultat explicabil?**

Dacă nu:

arhitectura trebuie revizuită.

---

# 156. Core Engine North Star

Engine-urile sunt reușite atunci când utilizatorul poate spune simplu:

> **„Ce mănânc acum?”**

iar sistemul poate răspunde folosind simultan:

* cine este utilizatorul;
* ce obiectiv are;
* ce a mâncat;
* ce îi lipsește;
* ce va mânca mai târziu;
* ce îi place;
* ce nu îi place;
* ce are în frigider;
* ce expiră;
* cât timp are;
* unde mănâncă;
* cu cine mănâncă;
* cât vrea să cheltuiască;
* cât a cheltuit deja;
* ce a mâncat recent;
* cât de multă varietate dorește;

și să transforme toate aceste informații într-o masă **realistă, calculată, personalizată și executabilă**.

Aceasta este funcția centrală a întregului produs.
