# 10 — Iteration 0 Implementation Blueprint

## 1. Scopul documentului

Acest document definește exact prima versiune construibilă și utilizabilă a produsului.

Iteration 0 este:

> **Coach Plan Tracker**

Promisiunea produsului este:

> **Îmi urmăresc simplu planul alimentar, știu ce am mâncat astăzi, cât am consumat și îmi pot urmări progresul fără Notes, Excel sau calcule manuale.**

Iteration 0 NU încearcă să implementeze întreg Product Vision.

Trebuie însă să respecte toate fundamentele definite în:

```text
00 — Product Vision
01 — User Model & Use Cases
02 — Module Map
03 — Core Engines
04 — Nutrition Specification
05 — AI Specification
06 — Domain & Data Model
07 — Iterative MVP & Product Roadmap
08 — UX Architecture & User Flows
09 — Technical Architecture
```

Iteration 0 trebuie să fie:

* mică;
* rapidă;
* simplă;
* completă;
* production-ready;
* mobile-first;
* extensibilă;
* folosită real.

---

# 2. Iteration 0 North Star

După deployment trebuie să putem utiliza aplicația zilnic fără să mai avem nevoie de alt instrument pentru urmărirea planului alimentar.

Flow-ul principal trebuie să fie:

```text
Deschid aplicația
        ↓
Văd ce am de mâncat
        ↓
Aleg varianta
        ↓
Am mâncat
        ↓
Un tap
        ↓
Nutriția zilei se actualizează
        ↓
Gata
```

---

# 3. Definition of Product

Iteration 0 NU trebuie percepută de utilizator ca:

> „o versiune neterminată a unei aplicații mai mari”.

Trebuie percepută ca:

> **un tracker minimalist și foarte bun pentru planul meu alimentar.**

Funcțiile viitoare pur și simplu nu există încă în UI.

---

# 4. Product Surface

Navigation:

```text
┌─────────────┬─────────────┬─────────────┐
│     Azi     │      +      │      Eu     │
└─────────────┴─────────────┴─────────────┘
```

Atât.

---

# 5. Iteration 0 Screens

Există doar următoarele suprafețe principale:

```text
Authentication

Onboarding

Today

Add
├── Meal
└── Weight

Meal Detail / Adjustment

Me
├── Progress
├── Nutrition Targets
├── Food Plan
└── Household
```

Nu există:

```text
Plan tab
Groceries
Pantry
Budget
AI Coach
Scan
Meal Prep
Recipe Browser
Advanced Micronutrients
```

---

# 6. Primary Use Case

Utilizatorul are un plan alimentar primit de la antrenor.

Planul conține:

```text
Masa 1
Masa 2
Masa 3
Masa 4
```

și mai multe alternative pentru unele mese.

Exemplu din planul inițial:

```text
Masa 1

Varianta A
200g iaurt grecesc 2%
50g ovăz
50g fructe de pădure

Varianta B
2 ouă
3 albușuri
50g ovăz
cottage cheese
salată
```

Alte mese oferă alternative de:

* carbohidrați;
* surse de proteină;
* legume;
* shake-uri / fructe;

conform planului exact primit.

Cantitățile care nu au fost explicit furnizate nu trebuie inventate în seed data.

---

# 7. First Architectural Decision

Planul antrenorului NU este hardcodat în React.

Nu avem:

```typescript
const coachPlan = [...]
```

în frontend.

El este domain data.

---

# 8. MealPlanTemplate

Introducem pentru V0 conceptul:

```text
MealPlanTemplate
```

Acesta reprezintă:

> un plan reutilizabil primit de la antrenor sau definit ulterior de utilizator.

---

# 9. Why Template Instead Of Full Planner

În Iteration 0 nu avem nevoie de:

```text
Monday
Tuesday
Wednesday
...
```

generate individual.

Planul este același pattern repetabil.

Dar mai târziu:

```text
MealPlanTemplate
```

poate deveni sursă pentru:

```text
MealPlan
```

din Weekly Planner.

Nu aruncăm nimic.

---

# 10. Coach Plan Template

Seed:

```text
coach_plan_v1
```

Metadata:

```text
name:
Plan Antrenor

version:
1

slots:
4
```

---

# 11. Template Slots

```text
Slot 1
Masa 1

Slot 2
Masa 2

Slot 3
Masa 3

Slot 4
Masa 4
```

---

# 12. Slot Options

Fiecare slot poate avea:

```text
1..N recipe options
```

Exemplu:

```text
Masa 1

○ Greek Yogurt Bowl
○ Omelette Breakfast
```

---

# 13. Avoid Generic Meal Builder In V0

Dacă planul permite:

```text
4 carbohydrates
×
4 proteins
```

nu construim încă un generic meal-composition engine.

Pentru V0 putem crea un set finit de combinații validate.

Exemplu:

```text
Chicken + Rice
Chicken + Potato
Turkey + Rice
Turkey + Potato
...
```

Acest lucru păstrează V0 foarte simplu.

---

# 14. Why This Is Acceptable

Iteration 0 validează:

```text
tracking
UX
nutrition calculation
daily usage
```

nu:

```text
combinatorial meal generation
```

Recipe Ingredient Groups rămân disponibile în Domain Model pentru versiunile următoare.

---

# 15. Recipe Requirements V0

Fiecare recipe seed trebuie să aibă:

```text
title

ingredients:
  food
  exact quantity
  unit
  preparation state

nutrition:
  calculated
```

Nu introducem manual totalurile nutriționale dacă pot fi calculate din ingrediente.

---

# 16. Seed Validation Rule

CI trebuie să eșueze dacă o rețetă activă are:

```text
missing food

missing quantity

invalid unit

missing nutrition data required for V0
```

---

# 17. Food Data V0

Nu construim încă integrare externă.

Avem un catalog curated cu alimentele necesare planului.

---

# 18. Foods Needed

Doar alimentele necesare pentru:

```text
coach_plan_v1
```

plus câteva alimente utile pentru manual logging.

---

# 19. Example Food Catalog

Poate include:

```text
Greek Yogurt 2%
Oats
Mixed Berries

Egg
Egg White
Cottage Cheese
Salad / Leafy Vegetables

White Rice — Cooked
Brown Rice — Cooked
Potato — Baked
Sweet Potato — Cooked/Baked
Pasta — Cooked

Chicken Breast
Turkey Breast
Lean Beef
Fish variants defined by plan

Mixed Vegetables

Protein Powder

Banana
Other fruits required by plan
```

Lista finală trebuie derivată exact din plan.

---

# 20. Food Nutrition V0

Urmărim obligatoriu:

```text
energy_kcal
protein_g
carbohydrate_g
fat_g
```

Opțional, dacă datele sunt suficient de bune:

```text
fiber_g
```

---

# 21. Micronutrients Are Not Exposed Yet

Chiar dacă food model-ul poate suporta micronutrients:

Iteration 0 UI nu le afișează.

Nu vrem:

> „Micronutrients coming soon.”

Pur și simplu nu există în UX.

---

# 22. Food Nutrition Basis

Canonical:

```text
per 100 g
```

sau unitatea naturală documentată.

---

# 23. Preparation State Is Mandatory Where Relevant

Exemplu:

```text
Rice
COOKED
```

nu:

```text
Rice
```

ambiguu.

---

# 24. V0 Product Philosophy

Mai bine:

```text
20 foods with correct data
```

decât:

```text
10,000 foods with uncertain data
```

---

# 25. Nutrition Engine V0

Primul Core Engine implementat este:

> **Nutrition Calculation Engine**

---

# 26. V0 Responsibilities

Trebuie să poată calcula:

```text
Food × Quantity

Recipe

Scaled Recipe

FoodLog

Daily Totals
```

---

# 27. V0 Does NOT Calculate

```text
TDEE
automatic calorie targets
micronutrient gap
recommendation scoring
weekly planning
```

---

# 28. Nutrition Formula

Pentru food cu valori per 100g:

```text
nutrient_amount =
food_nutrient_per_100g
×
quantity_g
/
100
```

---

# 29. Recipe Nutrition

```text
RecipeNutrition =
Σ IngredientNutrition
```

---

# 30. Scaled Recipe

Dacă utilizatorul consumă:

```text
75%
```

din recipe:

```text
nutrition =
recipeNutrition × 0.75
```

---

# 31. Ingredient Adjustment

Dacă user modifică ingredient individual:

Nutrition Engine recalculează pornind de la valorile efective.

---

# 32. Nutrition Precision

Intern:

```text
high precision
```

UI:

```text
610 kcal
48g proteină
```

Nu:

```text
610.238 kcal
```

---

# 33. Manual Targets

V0 NU calculează automat necesarul nutrițional.

Utilizatorul poate introduce:

```text
Calories
Protein
Carbs
Fat
```

---

# 34. Important UX Improvement

Manual targets NU trebuie să fie obligatorii pentru folosirea produsului.

În onboarding:

```text
Ai targeturi de la antrenor?

[ Da, le introduc ]
[ Nu acum ]
```

---

# 35. Why Targets Are Optional

Dacă user nu are targeturile:

produsul trebuie totuși să permită:

```text
meal tracking
daily consumed totals
weight tracking
```

Nu blocăm produsul.

---

# 36. Today Without Targets

UI:

```text
Astăzi

1.340 kcal

Proteină   126g
Carbs      132g
Grăsimi     42g
```

---

# 37. Today With Targets

UI:

```text
1.340 / 2.150 kcal

Proteină
126 / 170g

P 126/170 · C 132/210 · G 42/65
```

---

# 38. Partial Targets

Dacă user știe doar:

```text
Calories
Protein
```

UI poate afișa target doar pentru acestea.

Nu forțăm completarea tuturor.

---

# 39. Active Target Profile

Dacă există:

```text
NutritionTargetProfile
mode = USER_CONFIGURED
```

Today îl folosește.

---

# 40. Target History

Chiar V0 păstrează:

```text
valid_from
valid_until
```

pentru a evita probleme mai târziu.

---

# 41. User Account Flow

Prima deschidere:

```text
Sign In / Sign Up
        ↓
Onboarding
        ↓
Today
```

---

# 42. Authentication

Infrastructure:

```text
Supabase Auth
```

Backend:

```text
FastAPI JWT verification
```

---

# 43. No Domain Data Direct From Frontend

Flow:

```text
Next.js
↓
FastAPI
↓
PostgreSQL
```

---

# 44. Account Creation

După primul login:

backend creează dacă este necesar:

```text
Account
```

din auth identity.

---

# 45. Onboarding Goal

Onboarding trebuie să ajungă la Today cât mai repede.

Nu trebuie să configureze întreg Product Vision.

---

# 46. Onboarding V0

Maximum trei pași logici.

---

# 47. Step 1 — Member

```text
Cum te cheamă?

[ Alberto ]

[ Continuă ]
```

Atât.

Nu cerem:

* age;
* height;
* activity;
* goal.

Acestea apar în Iteration 1.

---

# 48. Household Creation

Automat:

```text
Household
```

este creat în background.

Nu cerem:

> „Cum vrei să numești household-ul?”

în onboarding.

Default:

```text
Casa mea
```

Poate fi schimbat ulterior.

---

# 49. First Member

Se creează:

```text
Member
linkedAccountId = account
```

---

# 50. Step 2 — Plan

```text
Ce plan vrei să urmărești?

Plan Antrenor

4 mese / zi

[ Folosește planul ]
```

În V0 poate exista un singur template.

Nu avem nevoie de selector complex.

---

# 51. Step 3 — Targets

```text
Ai targeturi nutriționale?

○ Da
○ Nu acum
```

---

# 52. Target Form

Dacă Da:

```text
Calorii
[     ]

Proteină
[     ] g

Carbohidrați
[     ] g

Grăsimi
[     ] g

[ Salvează ]
```

---

# 53. Then

User ajunge direct în:

```text
Azi
```

---

# 54. Onboarding Completion Target

De la sign-in la Today:

ideal:

```text
sub 1 minut
```

pentru utilizator care are targeturile la îndemână.

---

# 55. No Tutorial Carousel

Nu avem:

```text
Welcome
Swipe
Swipe
Swipe
```

Produsul se explică prin UI.

---

# 56. Today Is The Product

Ruta principală:

```text
/today
```

---

# 57. Today Header

```text
Azi · Joi, 20 august

Alberto ⌄
```

---

# 58. Member Selector

Dacă există un singur member:

selectorul poate fi discret.

Dacă există doi:

```text
Alberto ⌄
```

permite switching.

---

# 59. Today Nutrition Summary

Compact.

Nu dashboard.

---

# 60. Today Meal List

```text
✓ Masa 1
  Greek Yogurt Bowl

○ Masa 2
  Alege varianta

○ Masa 3
  Alege varianta

○ Masa 4
  Alege varianta
```

---

# 61. Next Meal Highlight

Primul slot neconsumat trebuie să fie vizual dominant.

---

# 62. Example

```text
URMEAZĂ

Masa 2

Alege ce mănânci.

[ Vezi variantele ]
```

---

# 63. Selecting Meal Option

Tap:

```text
Vezi variantele
```

deschide bottom sheet.

---

# 64. Meal Options Sheet

```text
Masa 2

Chicken + Rice
620 kcal · 48g proteină

Chicken + Potato
590 kcal · 47g proteină

Turkey + Rice
605 kcal · 46g proteină
```

---

# 65. Option Card

Trebuie să afișeze:

```text
title
kcal
protein
```

Atât.

Nu full macro table.

---

# 66. Selecting Option

Tap pe recipe:

deschide Meal Detail.

---

# 67. Meal Detail

```text
Chicken + Rice

620 kcal · 48g proteină

Porția planului

180g pui
200g orez gătit
200g legume

[ Am mâncat ]

Ajustează
```

---

# 68. Common Flow

```text
Today
↓
Vezi variantele
↓
Recipe
↓
Am mâncat
```

3 actions.

---

# 69. Remember Today's Selection

Dacă user selectează recipe dar nu o loghează imediat:

selecția poate rămâne local/session.

În V0 nu trebuie neapărat persistată ca `planned meal`.

---

# 70. Preferred Simplicity

Mai simplu:

Meal option este considerată selectată doar când user o consumă.

Aceasta evită un nou state:

```text
selected but not eaten
```

în V0.

---

# 71. Logging Planned Option

CTA:

```text
Am mâncat
```

creează:

```text
FoodLog
FoodLogItems
NutritionSnapshot
```

---

# 72. FoodLog Links To

```text
member
date/time
recipe
meal_plan_template_slot
```

---

# 73. Today Completion Detection

Dacă există FoodLog pentru:

```text
member
local date
template slot
```

slotul este:

```text
CONSUMED
```

---

# 74. No Separate Daily Meal Records Needed

Aceasta simplifică masiv V0.

Today este derivat din:

```text
Plan Template
+
Today's Food Logs
```

---

# 75. Future Compatibility

Iteration 3 va introduce:

```text
MealPlan
MealSlot
Meal
```

dar FoodLog-urile V0 rămân perfect valide.

---

# 76. Adjust Flow

În Meal Detail:

```text
Ajustează
```

---

# 77. Adjustment Sheet — Level 1

```text
Cât ai mâncat?

50%
75%
100%
125%

[ Altă cantitate ]
```

---

# 78. 100% Default

Planul antrenorului = 100%.

---

# 79. Level 2 Precise Adjustment

```text
Pui
[ 170 ] g

Orez gătit
[ 180 ] g

Legume
[ 200 ] g
```

---

# 80. Important

Adjustarea logului NU modifică recipe template.

---

# 81. Recipe

Rămâne:

```text
planned/default quantity
```

---

# 82. FoodLogItem

Păstrează:

```text
actual quantity
```

---

# 83. Log Confirmation

NU afișăm modal:

> „Ești sigur?”

---

# 84. Optimistic Result

Meal card devine imediat:

```text
✓ Masa 2
```

---

# 85. Undo Snackbar

```text
Masa a fost înregistrată.

[ Anulează ]
```

---

# 86. Undo Implementation

Backend:

```text
DELETE /food-logs/{id}
```

sau domain action echivalent.

În V0 poate fi hard delete pentru log recent dacă audit requirements sunt mici.

Preferabil:

```text
deleted_at
```

pentru extensibilitate.

---

# 87. Manual Meal Logging

Buton:

```text
+
```

→

```text
Adaugă

Masă
Greutate
```

---

# 88. Manual Meal V0

Nu construim MyFitnessPal.

Manual meal flow este doar escape hatch.

---

# 89. Manual Meal Entry

User poate selecta:

```text
o recipe din plan
```

sau:

```text
un food din catalog
```

---

# 90. Search Scope V0

Search doar în:

```text
foods seeded
recipes seeded
```

Nu internet.

---

# 91. Manual Food Entry

Exemplu:

```text
Banana

[ 120 ] g

[ Adaugă ]
```

---

# 92. Manual Recipe Entry

Exemplu:

```text
Greek Yogurt Bowl

100%

[ Adaugă ]
```

---

# 93. Manual Log Has No Slot

```text
plan_template_slot_id = null
```

Este extra intake.

---

# 94. Why This Matters

Dacă user mănâncă:

```text
un fruct suplimentar
```

daily totals rămân corecte.

---

# 95. But Keep Manual Logging Minimal

Nu construim:

* custom foods;
* barcode;
* custom recipe creation;

în V0.

---

# 96. Weight Tracking

Acces:

```text
+
↓
Greutate
```

---

# 97. Weight Sheet

```text
Greutate

[ 94.7 ] kg

Astăzi · 08:15

[ Salvează ]
```

---

# 98. Input

Numeric keyboard.

---

# 99. Weight Validation

Acceptăm doar values în interval tehnic rezonabil.

Nu facem medical judgment.

---

# 100. Weight Log

```text
source = MANUAL
confidence = VERIFIED
```

---

# 101. Progress

Acces:

```text
Eu
↓
Progres
```

---

# 102. Progress V0

Doar:

```text
current weight
history
simple trend/chart
```

---

# 103. Example

```text
Greutate

94.7 kg

Ultimele 7 zile
↓ 0.3 kg
```

---

# 104. Chart

Simplu:

```text
one line
one metric
```

---

# 105. No Automatic Interpretation

V0 nu spune:

> „Slăbești prea încet.”

---

# 106. No Target Adjustment

V0 doar afișează date.

---

# 107. Me Screen

Ruta:

```text
/me
```

---

# 108. Content

```text
Alberto

Progres
Targeturi nutriționale
Plan alimentar
Household
Setări de bază
```

---

# 109. Nutrition Targets Screen

Permite:

```text
View
Edit
Remove
```

manual targets.

---

# 110. Editing Targets

Creează o nouă:

```text
NutritionTargetProfile
```

sau în V0 închide active profile și creează noul profil.

Nu modifică retrospectiv istoricul.

---

# 111. Food Plan Screen

Arată:

```text
Plan Antrenor

Masa 1
Masa 2
Masa 3
Masa 4
```

---

# 112. Slot Expansion

Tap:

```text
Masa 1
```

arată variantele.

---

# 113. Food Plan Is Read-Only In V0

User nu poate modifica recipe template.

---

# 114. Why

Custom plan editor este un alt feature.

Nu îl construim accidental.

---

# 115. Household Screen

```text
Casa mea

Alberto

[ + Adaugă persoană ]
```

---

# 116. Add Member V0

Cerem:

```text
Name
```

apoi opțional:

```text
same coach plan?
```

---

# 117. Example

```text
Maria

Folosește același plan?

[ Da ]
[ Nu ]
```

---

# 118. Member Without Plan

Este permis.

Today poate afișa:

```text
Nu are încă un plan alimentar.

[ Adaugă plan ]
```

---

# 119. Second Member Targets

Pot fi introduse separat.

---

# 120. Important V0 Limitation

Iteration 0 NU calculează porții diferite automat pentru household.

Fiecare member urmărește planul atribuit.

---

# 121. Why Household Still Exists

Iteration 1 va adăuga:

```text
shared recipes
different personalized portions
```

fără migration conceptual.

---

# 122. Member Switching

Today:

```text
Alberto ⌄
```

selectează member.

---

# 123. Default Member

Linked member al current account.

---

# 124. Last Selected Member

Poate fi memorat local în UI.

Nu este critical domain state.

---

# 125. Data Model V0

Tabele necesare efectiv.

---

# 126. accounts

```text
id UUID PK

auth_user_id UUID UNIQUE

email
locale
timezone

created_at
updated_at
```

---

# 127. households

```text
id UUID PK

name
currency
timezone
locale

created_at
updated_at
```

---

# 128. household_memberships

```text
id UUID PK

account_id FK
household_id FK

role
status

created_at
```

Unique:

```text
(account_id, household_id)
```

---

# 129. members

```text
id UUID PK

household_id FK
linked_account_id FK nullable

display_name

created_at
updated_at
deleted_at nullable
```

---

# 130. nutrients

Seed:

```text
energy_kcal
protein
carbohydrate
fat
fiber
```

Fiber poate exista chiar dacă UI-ul nu îl afișează.

---

# 131. nutrient columns

```text
id
code UNIQUE
name
category
canonical_unit
```

---

# 132. nutrition_target_profiles

```text
id UUID PK

member_id FK

mode
source

valid_from DATE
valid_until DATE nullable

created_at
```

V0:

```text
mode = USER_CONFIGURED
```

---

# 133. nutrient_targets

```text
id

nutrition_target_profile_id FK
nutrient_id FK

rule_type
minimum nullable
preferred nullable
maximum nullable

unit
```

---

# 134. foods

```text
id UUID PK

canonical_name
category

default_preparation_state nullable

created_at
updated_at
```

---

# 135. food_nutrient_values

```text
id

food_id FK
nutrient_id FK

value NUMERIC

basis_amount
basis_unit

preparation_state

source
confidence

created_at
updated_at
```

---

# 136. recipes

```text
id UUID PK

title
description nullable

source
status

version

created_at
updated_at
```

---

# 137. Recipe V0 source

Most seed recipes:

```text
source = SYSTEM
```

or:

```text
COACH
```

dacă introducem această source semantică.

---

# 138. recipe_ingredients

```text
id UUID PK

recipe_id FK
food_id FK

quantity
unit

preparation_state

sort_order

created_at
```

---

# 139. No Product Yet

V0 nu are:

```text
Product
Barcode
Brand
Package
Price
```

Acestea apar Iteration 5/7.

---

# 140. meal_plan_templates

```text
id UUID PK

code UNIQUE
name

version
status

created_at
```

---

# 141. Example

```text
code = coach_plan_v1
```

---

# 142. meal_plan_template_slots

```text
id UUID PK

meal_plan_template_id FK

slot_index
label

created_at
```

---

# 143. Unique

```text
(template_id, slot_index)
```

---

# 144. meal_plan_template_options

```text
id UUID PK

slot_id FK
recipe_id FK

sort_order
label nullable
```

---

# 145. member_plan_assignments

```text
id UUID PK

member_id FK
meal_plan_template_id FK

active_from
active_until nullable

created_at
```

---

# 146. Only One Active Assignment

V0 invariant:

un member are maximum un active plan template.

---

# 147. nutrition_snapshots

V0 simplificare intenționată:

```text
id UUID PK

values JSONB

engine_version

created_at
```

---

# 148. Example

```json
{
  "energy_kcal": 612.3,
  "protein_g": 48.2,
  "carbohydrate_g": 64.1,
  "fat_g": 17.6
}
```

---

# 149. food_logs

```text
id UUID PK

member_id FK

recipe_id FK nullable
plan_template_slot_id FK nullable

consumed_at TIMESTAMPTZ

source
precision_type

nutrition_snapshot_id FK

created_at
deleted_at nullable
```

---

# 150. food_log_items

```text
id UUID PK

food_log_id FK
food_id FK

quantity
unit
preparation_state

created_at
```

---

# 151. Why FoodLogItems

Păstrăm cantitatea reală consumată.

---

# 152. weight_logs

```text
id UUID PK

member_id FK

weight_kg NUMERIC

measured_at TIMESTAMPTZ

source
confidence

created_at
deleted_at nullable
```

---

# 153. No DailyNutritionState Table In V0

Daily totals sunt suficient de mici pentru:

```text
derive on request
```

din FoodLogs.

---

# 154. Why

Nu avem încă volume mari.

Evita cache invalidation inutil.

---

# 155. Future

Iteration 2 poate introduce:

```text
DailyNutritionState
```

ca derived cache.

---

# 156. Today Query Logic

Input:

```text
memberId
date
```

---

# 157. Backend Resolves

```text
authorized member

active plan assignment

plan template slots

today's FoodLogs

active NutritionTargetProfile
```

---

# 158. Today DTO

Conceptual:

```typescript
TodayView {
  date

  member

  nutrition

  slots

  extraLogs
}
```

---

# 159. Nutrition DTO

```typescript
{
  consumed: {
    energyKcal
    proteinG
    carbohydrateG
    fatG
  }

  targets?: {
    energyKcal?
    proteinG?
    carbohydrateG?
    fatG?
  }
}
```

---

# 160. Slot DTO

```typescript
{
  id
  index
  label

  status

  consumedLog?

  options[]
}
```

---

# 161. Slot Status V0

```text
PENDING
CONSUMED
```

Atât.

---

# 162. Avoid Premature Statuses

Nu avem încă:

```text
PLANNED
PREPARED
LOCKED
SKIPPED
```

în V0 template Today.

---

# 163. Skipping Meal

V0 poate pur și simplu lăsa slotul necompletat.

Nu avem nevoie de explicit Skip state.

---

# 164. Why

Tracking, nu adherence analytics.

---

# 165. API V0

Prefix:

```text
/api/v1
```

---

# 166. Authentication / Account

```text
GET /v1/me
```

Return:

```text
Account + current household summary
```

---

# 167. Onboarding

Prefer user-intent endpoint:

```text
POST /v1/onboarding
```

Body:

```text
displayName

planTemplateId

targets?
```

---

# 168. Why One Onboarding Transaction

Crează atomic:

```text
Account if needed
Household
Membership
Member
Plan assignment
Target profile if supplied
```

---

# 169. Today

```text
GET /v1/today
```

Query:

```text
memberId
date?
```

Dacă date lipsește:

backend folosește household timezone current date.

---

# 170. Template Options

Today response poate conține suficiente date pentru option cards.

Nu trebuie query separat pentru fiecare.

---

# 171. Meal Detail

```text
GET /v1/recipes/{recipeId}
```

Return:

```text
ingredients
nutrition
```

---

# 172. Consume Template Meal

```text
POST /v1/today/slots/{slotId}/consume
```

Body:

```text
memberId
recipeId

scale? optional

ingredientAdjustments? optional
```

---

# 173. Backend Validates

```text
member authorized

slot belongs to active template

recipe is allowed in slot

quantities valid
```

---

# 174. Then

Backend:

```text
calculates actual nutrition
creates FoodLogItems
creates NutritionSnapshot
creates FoodLog
commits
```

---

# 175. Response

Return updated:

```text
TodayView
```

sau:

```text
FoodLog + updated nutrition summary
```

Prefer:

> updated TodayView fragment.

---

# 176. Manual Food Log

```text
POST /v1/food-logs
```

Body supports:

```text
food
or
recipe
```

---

# 177. Delete / Undo Log

```text
DELETE /v1/food-logs/{id}
```

---

# 178. Weight

```text
POST /v1/weight-logs
```

---

# 179. Weight History

```text
GET /v1/members/{memberId}/weight
```

with optional range.

---

# 180. Targets

```text
GET /v1/members/{memberId}/nutrition-targets
```

---

# 181. Target Update

```text
PUT /v1/members/{memberId}/nutrition-targets
```

semantic behavior:

```text
close old active profile
create new profile
```

---

# 182. Household

```text
GET /v1/households/{id}
```

---

# 183. Add Member

```text
POST /v1/households/{id}/members
```

---

# 184. Assign Plan

```text
PUT /v1/members/{memberId}/plan
```

---

# 185. Plan Template

```text
GET /v1/plan-templates/{id}
```

---

# 186. Do Not Build Generic Admin CRUD API

No:

```text
POST /nutrients
DELETE /foods
PATCH /recipe-ingredients
```

public.

Seed/admin operations remain internal.

---

# 187. Backend Modules V0

Exact modules:

```text
core

household

nutrition

catalog

recipes

meals

progress
```

---

# 188. No Empty Modules

Nu creăm încă:

```text
planning/
pantry/
ai/
shopping/
```

goale.

---

# 189. Backend Structure V0

```text
apps/api/app/

├── main.py
│
├── core/
│   ├── config.py
│   ├── database.py
│   ├── auth.py
│   ├── errors.py
│   └── logging.py
│
├── modules/
│   │
│   ├── household/
│   ├── nutrition/
│   ├── catalog/
│   ├── recipes/
│   ├── meals/
│   └── progress/
│
└── api/
    └── v1/
```

---

# 190. Household Module

Owns:

```text
Household
Membership
Member
Member Plan Assignment
```

---

# 191. Nutrition Module

Owns:

```text
Nutrient
NutritionTargetProfile
NutrientTarget
NutritionSnapshot
Nutrition Calculation Engine
```

---

# 192. Catalog

Owns:

```text
Food
FoodNutrientValue
```

---

# 193. Recipes

Owns:

```text
Recipe
RecipeIngredient
MealPlanTemplate
TemplateSlot
TemplateOption
```

Template ar putea deveni `planning` ulterior.

În V0 poate sta în recipes/templates pentru a evita modul planning prematur.

---

# 194. Meals

Owns:

```text
FoodLog
FoodLogItem
Today use cases
```

---

# 195. Progress

Owns:

```text
WeightLog
weight read models
```

---

# 196. Nutrition Engine V0 File Structure

```text
nutrition/
└── engine/
    ├── vector.py
    ├── calculations.py
    ├── units.py
    └── rounding.py
```

---

# 197. Engine API

Conceptual:

```python
calculate_food_nutrition()

calculate_recipe_nutrition()

calculate_logged_items_nutrition()

scale_nutrition()

sum_nutrition()
```

---

# 198. Engine Has No SQLAlchemy Import

Locked.

---

# 199. Engine Has No FastAPI Import

Locked.

---

# 200. RecipeNutritionService

Application layer:

```text
load Recipe + Food nutrients
↓
convert to engine input
↓
calculate
```

---

# 201. Nutrition Snapshot

Creation:

```text
Nutrition Engine output
↓
snapshot
```

Snapshot becomes immutable log truth.

---

# 202. Frontend Routes

Iteration 0:

```text
/login

/onboarding

/today

/me

/me/progress

/me/targets

/me/plan

/me/household
```

---

# 203. Bottom Navigation

Persistent only inside authenticated app:

```text
Azi     +     Eu
```

---

# 204. +

`+` is an action button, not route.

Opens:

```text
Bottom Sheet
```

---

# 205. Today Frontend Structure

```text
features/today/

├── components/
│   ├── TodayHeader.tsx
│   ├── NutritionSummary.tsx
│   ├── NextMealCard.tsx
│   ├── MealSlotCard.tsx
│   ├── MealOptionsSheet.tsx
│   └── MealAdjustmentSheet.tsx
│
├── queries/
│   └── useToday.ts
│
├── mutations/
│   ├── useConsumeMeal.ts
│   └── useDeleteFoodLog.ts
│
└── types/
```

Types preferably generated/API-derived.

---

# 206. Frontend Profile

```text
features/profile/
```

for:

```text
targets
plan
household
```

---

# 207. Progress

```text
features/progress/
```

---

# 208. Design System

Start minimal.

Need:

```text
Button
Card
Sheet
Input
Tabs if necessary
Progress
Skeleton
Toast
Avatar
Dropdown
```

---

# 209. Do Not Build Huge Component Library First

Components emerge from real V0 screens.

---

# 210. Mobile First Width

Design primary at:

```text
~375–430 px
```

then responsive upward.

---

# 211. Desktop

Desktop layout can simply center:

```text
mobile-like content column
```

with extra whitespace.

No separate desktop dashboard needed.

---

# 212. Today UX Rule

Above fold trebuie să poată vedea:

```text
daily nutrition summary
+
next meal
```

pe telefon normal.

---

# 213. Touch Targets

Primary controls:

minimum comfortable mobile touch size.

---

# 214. Nutrition Colors

Neutral.

No aggressive red for macro deviation.

---

# 215. Meal Complete

Use:

```text
check
subtle success
```

---

# 216. Accessibility V0

Required:

```text
semantic buttons
labels
keyboard navigation
focus states
sufficient contrast
screen-reader accessible form labels
```

---

# 217. PWA V0

Implementăm:

```text
manifest
icons
mobile metadata
installable app shell
```

---

# 218. Offline Promise

Iteration 0 NU promite full offline support.

---

# 219. Cached UX

TanStack Query poate păstra current screen temporar.

Dar writes necesită network.

---

# 220. API Client

Generated din:

```text
FastAPI OpenAPI
```

---

# 221. packages/api-client

```text
packages/
└── api-client/
```

---

# 222. Never Manually Recreate Backend DTOs

Contract drift este CI error.

---

# 223. Server State

TanStack Query.

---

# 224. Core Query Keys

```text
today(memberId, date)

weight(memberId)

targets(memberId)

household(householdId)

planTemplate(id)
```

---

# 225. Mutation Invalidation

Consume meal:

```text
invalidate today
```

---

# 226. Delete log

```text
invalidate today
```

---

# 227. Weight save

```text
invalidate weight
```

---

# 228. Target update

```text
invalidate targets
invalidate today
```

---

# 229. Member add

```text
invalidate household
```

---

# 230. Optimistic Update

Recommended for:

```text
meal consume
meal undo
```

but only if rollback is correctly implemented.

---

# 231. Alternative

For first implementation:

server response can be sufficiently fast.

Do not complicate optimistic cache prematurely.

---

# 232. Authentication Frontend

Supabase client handles:

```text
session
login
logout
token refresh
```

---

# 233. API Requests

Attach:

```text
Bearer token
```

to FastAPI.

---

# 234. Backend JWT Validation

FastAPI validates token and resolves:

```text
CurrentActor
```

---

# 235. Authorization

Every member request verifies:

```text
member belongs to household
actor has access
```

---

# 236. Never Trust memberId From Client

It is an identifier.

Not authorization proof.

---

# 237. Timezone

Household default:

```text
Europe/Bucharest
```

for initial environment.

But stored as field.

---

# 238. Today Date

Backend resolves current date using:

```text
household.timezone
```

not server UTC date.

---

# 239. FoodLog Timestamp

Store:

```text
TIMESTAMPTZ
UTC
```

---

# 240. Date Grouping

Convert using household timezone.

---

# 241. Seed Data Architecture

Files:

```text
apps/api/seeds/

nutrients.yaml
foods.yaml
coach_plan_v1.yaml
```

---

# 242. nutrients.yaml

Example:

```yaml
- code: energy_kcal
  unit: kcal

- code: protein
  unit: g

- code: carbohydrate
  unit: g

- code: fat
  unit: g

- code: fiber
  unit: g
```

---

# 243. foods.yaml

Each item:

```yaml
code:
name:
preparation_state:

nutrition_per_100g:
  energy_kcal:
  protein:
  carbohydrate:
  fat:
  fiber:

source:
```

---

# 244. Provenance Required

Every seed food needs:

```text
source
```

even if V0 UI does not show it.

---

# 245. coach_plan_v1.yaml

Conceptual:

```yaml
code: coach_plan_v1
name: Plan Antrenor
version: 1

slots:
  - index: 1
    label: Masa 1
    recipes:
      - greek_yogurt_breakfast
      - omelette_breakfast

  - index: 2
    label: Masa 2
    recipes:
      ...
```

---

# 246. Recipe Definition

```yaml
recipes:

  greek_yogurt_breakfast:
    title: Iaurt cu ovăz și fructe
    ingredients:
      - food: greek_yogurt_2
        quantity: 200
        unit: g

      - food: oats
        quantity: 50
        unit: g

      - food: berries
        quantity: 50
        unit: g
```

---

# 247. Seed Quantity Rule

Use ONLY exact trainer-provided quantities.

If source plan says:

```text
cottage cheese
```

without quantity:

do not invent:

```text
100g
```

---

# 248. How To Handle Missing Quantity

Before activating the recipe:

either:

```text
retrieve quantity from original plan
```

or:

```text
mark recipe unavailable until specified
```

---

# 249. No Invalid Active Recipes

Seed script must reject incomplete active recipe.

---

# 250. Versioned Coach Plan

If trainer changes plan:

create:

```text
coach_plan_v2
```

Do not mutate:

```text
coach_plan_v1
```

used historically.

---

# 251. Existing Member

Can remain on v1 or be upgraded explicitly.

---

# 252. Database Migration 0001

Creates core tables.

---

# 253. Migration 0002

Could insert reference data? Prefer seed operation separate from schema migration.

---

# 254. Schema vs Content

Migrations:

```text
structure
```

Seed:

```text
system reference data
```

---

# 255. Seed Must Be Idempotent

Run twice:

no duplicates.

---

# 256. Seed Uses Stable Codes

Not IDs.

Example:

```text
greek_yogurt_2
```

---

# 257. Development Database

Docker Postgres.

---

# 258. Local Startup

Root:

```text
make dev
```

or:

```text
just dev
```

---

# 259. Development Processes

Starts:

```text
Postgres

FastAPI

Next.js
```

---

# 260. V0 No Worker

No Redis.

No AI.

---

# 261. Environment Variables

Frontend:

```text
NEXT_PUBLIC_API_URL

Supabase public auth config
```

---

# 262. Backend

```text
DATABASE_URL

SUPABASE_JWT_* / auth config

APP_ENV

CORS_ORIGINS
```

---

# 263. No Secrets In Frontend

Locked.

---

# 264. Backend Tests

Testing categories.

---

# 265. Nutrition Engine Unit Tests

Required.

---

# 266. Food Calculation Test

Given:

```text
100g Food
```

expected:

exact NutritionVector.

---

# 267. Quantity Test

```text
50g
```

returns exactly half per-100g nutrition.

---

# 268. Recipe Test

Sum ingredients.

---

# 269. Scaling Test

```text
75%
```

scales all nutrients.

---

# 270. Ingredient Adjustment Test

One changed quantity changes only corresponding nutrition correctly.

---

# 271. Missing Nutrition Test

Active V0 recipe with missing required macro data:

must fail validation.

---

# 272. Today Tests

No food logs:

all slots pending.

---

# 273. Consume Slot

Creates:

```text
FoodLog
Items
Snapshot
```

slot becomes consumed.

---

# 274. Double Consume

Should not accidentally create duplicate slot log.

---

# 275. V0 Invariant

Maximum one active slot-associated FoodLog per:

```text
member
date
slot
```

unless explicitly corrected.

---

# 276. Enforce

Application rule + optional DB-level uniqueness strategy.

Because local date is derived from timestamp, easier application enforcement initially.

---

# 277. Undo

Deleted log returns slot to pending.

---

# 278. Extra Manual Log

Does not complete a plan slot.

---

# 279. Targets Test

Without target:

Today works.

---

# 280. Partial Target Test

Only protein target:

UI/API returns target only for protein.

---

# 281. Target Version Test

Update:

old profile closes.

new profile activates.

---

# 282. Household Authorization Test

Account from household A cannot query member of household B.

Critical.

---

# 283. Weight Tests

Create weight log.

History sorted correctly.

Soft delete/correction works.

---

# 284. Timezone Tests

Food log around midnight must belong to correct local day.

Mandatory.

---

# 285. API Integration Tests

Run against real PostgreSQL.

Not SQLite.

---

# 286. Frontend Component Tests

Critical components:

```text
MealSlotCard
NutritionSummary
MealOptionsSheet
WeightForm
TargetForm
```

---

# 287. E2E Test 1 — New User

```text
sign up
↓
onboarding
↓
Today
```

No developer intervention.

---

# 288. E2E Test 2 — Log Day

```text
open Today
↓
select Masa 1
↓
consume
↓
totals update
↓
refresh
↓
state persists
```

---

# 289. E2E Test 3 — Adjust Meal

```text
meal
↓
adjust 75%
↓
consume
↓
nutrition reflects 75%
```

---

# 290. E2E Test 4 — Undo

```text
consume
↓
undo
↓
totals revert
```

---

# 291. E2E Test 5 — Weight

```text
+
↓
weight
↓
save
↓
Progress
↓
value visible
```

---

# 292. E2E Test 6 — Second Member

```text
add member
↓
assign plan
↓
switch Today
↓
independent logs
```

---

# 293. UX Acceptance Test

Give phone to user without explanation.

Task:

> „Ai mâncat prima masă. Înregistreaz-o.”

Expected:

completed immediately.

---

# 294. UX Acceptance Test 2

> „Ai mâncat doar trei sferturi.”

User must discover adjustment naturally.

---

# 295. UX Acceptance Test 3

> „Introdu greutatea de azi.”

Must take seconds.

---

# 296. UX Acceptance Test 4

> „Vezi ce a mâncat celălalt membru azi.”

Member switching must be obvious.

---

# 297. Performance V0

Targets should be practical.

---

# 298. Today Endpoint

Should feel instant under normal network conditions.

Avoid:

```text
10 sequential queries
```

---

# 299. Today Backend Query

Prefer explicit read query/load strategy.

Can fetch:

```text
plan
slots/options
logs
targets
```

efficiently.

---

# 300. Recipe Nutrition

May be:

```text
calculated on seed
+
cached
```

or calculated quickly on read.

---

# 301. Recommended V0

Store calculated recipe nutrition cache to avoid recalculating each Today load.

But canonical truth remains ingredients.

---

# 302. Recipe Cache

Could simply be:

```text
recipe_nutrition_cache JSONB
```

if helpful.

Optional.

---

# 303. Don't Optimize Prematurely

20 recipes do not require complex caching infrastructure.

---

# 304. Logging

Structured backend logging from day one.

---

# 305. Request Fields

```text
request_id
route
method
status
duration
```

---

# 306. Errors

Use centralized exception handling.

---

# 307. Error Contract

```text
code
message
requestId
```

---

# 308. User Errors

Examples:

```text
INVALID_QUANTITY
RECIPE_NOT_ALLOWED_FOR_SLOT
MEMBER_ACCESS_DENIED
PLAN_NOT_ASSIGNED
```

---

# 309. No Raw Exception UI

Locked.

---

# 310. Error Monitoring

Add Sentry or equivalent before production release.

---

# 311. Health

```text
/health/live
/health/ready
```

---

# 312. Analytics V0

Keep minimal.

Useful events:

```text
onboarding_completed

meal_option_opened
meal_logged
meal_adjusted
meal_undone

weight_logged

target_added
target_updated

member_added
member_switched
```

---

# 313. Why Analytics Already Matters

Iteration 0 needs to answer:

```text
Is logging easy?

Do we actually use the app?

How often do we adjust meals?
```

---

# 314. Do Not Store Nutrition Truth In Analytics

Analytics is observational only.

---

# 315. Analytics Provider

Can be introduced later.

At minimum:

event abstraction in frontend/backend.

Do not block V0 on a complex analytics vendor.

---

# 316. Product Success Metrics V0

Most important:

```text
days used consecutively
```

---

# 317. Primary Success Criterion

Use product:

```text
7 consecutive days
```

without external tracking.

---

# 318. Secondary

Percentage of plan meals logged.

---

# 319. Secondary

Median actions required to log a planned meal.

---

# 320. UX Target

Normal planned meal:

```text
1–3 taps
```

depending on whether option selection is necessary.

---

# 321. User Feedback

After real usage, track manually:

```text
What felt slow?
What did we repeatedly want to do but couldn't?
What information was annoying?
What did we ignore?
```

---

# 322. Iteration 0 Does NOT Add Features During Dogfooding Casually

Feedback goes into backlog.

Only fix:

```text
bugs
UX blockers
data correctness
```

before calling V0 complete.

---

# 323. Scope Discipline

Requests like:

> „Ar fi mișto să scanăm barcode acum.”

go to later iteration.

---

# 324. Iteration 0 Non-Goals

Explicitly:

```text
No automatic target calculation

No TDEE

No personalized portion solver

No meal recommendations

No regenerate

No weekly planner

No schedule

No lunchbox

No cooking mode

No meal prep

No pantry

No groceries

No budget

No prices

No barcode

No receipt scan

No meal vision

No scale vision

No fridge scan

No AI Coach

No learned preferences

No adaptive targets

No external food database dependency
```

---

# 325. V0 Is Still Complete

Because its promise is only:

> track the coach plan.

---

# 326. Implementation Strategy

We build V0 as vertical slices.

---

# 327. Slice 0 — Repository Foundation

Deliver:

```text
monorepo
Next.js
FastAPI
Postgres
Alembic
generated API client
CI
```

---

# 328. Exit Criteria

```text
web loads
API health works
DB migration works
frontend calls backend
CI green
```

---

# 329. Slice 1 — Authentication & Household

Deliver:

```text
Supabase Auth

Account

automatic Household

Member
```

---

# 330. User Value

User can log in and have persistent identity.

---

# 331. Slice 2 — Catalog & Nutrition

Deliver:

```text
Nutrients

Foods

Food nutrient values

Recipes

Nutrition Engine

coach_plan seed
```

---

# 332. Exit Criteria

CLI/test can calculate every recipe's:

```text
kcal
protein
carbs
fat
```

correctly.

---

# 333. Slice 3 — Onboarding

Deliver:

```text
Member setup

Plan assignment

Optional targets
```

---

# 334. Exit

New account can reach Today without manual DB manipulation.

---

# 335. Slice 4 — Today Read Model

Deliver:

```text
Today UI

plan slots

recipe options

nutrition summary
```

At this point no consume flow yet.

---

# 336. Slice 5 — Meal Logging

Deliver:

```text
Am mâncat

FoodLog

NutritionSnapshot

Today recalculation

Undo
```

---

# 337. First Real Product Moment

After Slice 5:

core product loop works.

---

# 338. Slice 6 — Adjustment & Manual Add

Deliver:

```text
portion %
ingredient adjustment

manual recipe/food log
```

---

# 339. Slice 7 — Weight & Progress

Deliver:

```text
manual weight

history

chart
```

---

# 340. Slice 8 — Household Multi-Member

Deliver:

```text
add member
assign plan
targets
member switching
```

---

# 341. Slice 9 — UX Polish

Deliver:

```text
responsive
mobile gestures where useful
loading
empty
error
undo
accessibility
PWA
```

---

# 342. Slice 10 — Production Hardening

Deliver:

```text
Sentry/errors
security review
authorization tests
migration validation
seed validation
deployment
backups
```

---

# 343. Slice 11 — Dogfood Release

Use for:

```text
7 real days
```

---

# 344. No Iteration 1 Before Dogfood

Important.

We do not immediately begin Portion Solver just because code compiles.

Use V0 first.

---

# 345. Why

Real use may reveal:

```text
meal option flow is wrong
target display too heavy
ingredient adjustments too annoying
trainer plan structure differs from assumptions
```

Fix fundamentals first.

---

# 346. Developer Backlog — Epic 1

## Foundation

Tasks:

```text
Initialize monorepo

Configure pnpm workspace

Create Next.js app

Create FastAPI app

Configure uv

Configure Ruff

Configure Pyright

Configure PostgreSQL local

Configure SQLAlchemy

Configure Alembic

Create health endpoints

Generate OpenAPI client

Configure CI
```

---

# 347. Epic 2 — Auth & Identity

```text
Configure Supabase Auth

JWT verification FastAPI

Account model

Household model

Membership model

Member model

CurrentActor dependency

Authorization helpers
```

---

# 348. Epic 3 — Nutrition Catalog

```text
Nutrient model

Food model

Food nutrient values

Seed loader

Unit normalization

NutritionVector

Nutrition calculations

Tests
```

---

# 349. Epic 4 — Recipes & Coach Plan

```text
Recipe model

Recipe ingredients

MealPlanTemplate

Template slots

Template options

coach_plan_v1 seed

seed validation

recipe nutrition calculation
```

---

# 350. Epic 5 — Onboarding

```text
POST onboarding

onboarding UI

member name

plan assignment

optional targets

redirect Today
```

---

# 351. Epic 6 — Today

```text
TodayView backend

Today API

Today page

member selector

nutrition summary

meal slot cards

next meal highlight
```

---

# 352. Epic 7 — Meal Logging

```text
FoodLog

FoodLogItem

NutritionSnapshot

Consume slot service

Consume API

UI Am mâncat

Undo

recalculate Today
```

---

# 353. Epic 8 — Adjustment

```text
Scale percentage

ingredient quantity edit

validation

nutrition recalculation

adjustment sheet
```

---

# 354. Epic 9 — Manual Add

```text
+ sheet

food search

recipe search

manual log

manual quantity
```

---

# 355. Epic 10 — Progress

```text
WeightLog

weight API

+ weight form

progress page

simple chart
```

---

# 356. Epic 11 — Household

```text
Household screen

add Member

assign plan

member targets

Today switching
```

---

# 357. Epic 12 — Quality

```text
E2E

accessibility

loading states

error states

Sentry

PWA

production deployment
```

---

# 358. Definition Of Done — Feature

Un feature NU este Done dacă are doar backend.

---

# 359. Functional

Must have:

```text
backend
frontend
validation
error state
loading state
tests
```

---

# 360. UX

Must be:

```text
usable on phone

clear without explanation

no dead end

no placeholder future feature
```

---

# 361. Domain

Must:

```text
respect module ownership

not duplicate truth

not bypass Nutrition Engine
```

---

# 362. Security

Must:

```text
authorize member/household

validate inputs
```

---

# 363. API

Must:

```text
be represented in OpenAPI

generated TS client updated
```

---

# 364. Tests

Critical happy path + failure path.

---

# 365. Definition Of Done — Iteration 0

Iteration 0 is DONE only when all following are true.

---

# 366. Account

User can:

```text
sign up
sign in
sign out
```

---

# 367. Onboarding

New user can configure product without developer intervention.

---

# 368. Plan

User sees all 4 coach meal slots.

---

# 369. Alternatives

Each slot exposes correct trainer-approved variants.

---

# 370. Nutrition

Every active recipe produces deterministic:

```text
kcal
protein
carbs
fat
```

---

# 371. Targets

User can:

```text
add
edit
remove/skip
```

manual targets.

---

# 372. Today

Daily totals are correct.

---

# 373. Consume

User can log a coach-plan meal.

---

# 374. Adjustment

User can log:

```text
less
more
different ingredient quantities
```

without modifying canonical recipe.

---

# 375. Extra Food

User can add a manual extra food/recipe.

---

# 376. Undo

User can correct accidental logging.

---

# 377. History

Refreshing or reopening app retains correct logs.

---

# 378. Day Boundary

Tomorrow starts with new pending slots.

Yesterday remains historical.

---

# 379. Weight

User can save and see weight history.

---

# 380. Household

At least two members can coexist with separate:

```text
logs
targets
weight
```

---

# 381. Authorization

No cross-household data access.

---

# 382. Mobile UX

All critical flows work comfortably on phone.

---

# 383. PWA

App is installable / behaves like a mobile web app.

---

# 384. Errors

Every major failure has understandable UX.

---

# 385. Tests

Critical E2E flows pass.

---

# 386. Production

Application is deployed with:

```text
HTTPS

production Postgres

auth

error monitoring

backups
```

---

# 387. Real-World Acceptance

Most important:

> **The product is used for seven consecutive days as the primary coach-plan tracker.**

---

# 388. Release Blockers

Cannot release if:

```text
nutrition totals are unreliable

coach plan quantities are incomplete

mobile logging is confusing

duplicate logs occur

authorization is broken

day timezone behavior is wrong

data disappears after refresh
```

---

# 389. Non-Blocking Imperfections

Can release with:

```text
basic chart styling

limited food catalog

minimal desktop layout

simple animations

no offline writes
```

because acestea nu compromise core promise.

---

# 390. Dogfood Checklist — Daily

During 7-day use record:

```text
Did I open it?

Did I log all relevant meals?

How many times did I avoid logging because it was annoying?

How many corrections did I make?

Did I need another app?

Was anything unclear?

Did I want a feature that belongs to Iteration 1?
```

---

# 391. Observe, Don't Immediately Build

If repeated issue:

```text
3+ times
```

then evaluate.

---

# 392. UX Friction Log

Create:

```text
docs/product-feedback/ITERATION-0-DOGFOOD.md
```

or equivalent product feedback location.

This is outside canonical `docs/product`.

---

# 393. Example Feedback

```text
Problem:
Adjusting rice every meal takes too long.

Frequency:
5 times/week

Possible future response:
Remember actual recurring portion
or Portion Solver
```

---

# 394. Iteration 1 Handoff Conditions

Do NOT start Iteration 1 until:

```text
V0 stable

coach-plan tracking usable

nutrition calculation trusted

household model proven

FoodLogs accumulating correctly
```

---

# 395. What Iteration 1 Will Reuse

Almost everything.

---

# 396. Accounts

Unchanged.

---

# 397. Household / Member

Unchanged.

---

# 398. Foods

Unchanged, expanded.

---

# 399. Recipes

Unchanged, enriched with:

```text
scalability
min/max quantities
ingredient roles
```

---

# 400. FoodLogs

Unchanged.

---

# 401. NutritionSnapshots

Unchanged.

---

# 402. WeightLogs

Unchanged.

---

# 403. Targets

Extended with:

```text
SYSTEM_ESTIMATED
```

---

# 404. New Iteration 1 Components

```text
MemberProfile

Goal

ActivityProfile

Target Engine

Portion Solver

Preferences
```

---

# 405. This Is The Architecture Test

If adding Iteration 1 requires replacing:

```text
FoodLog
Recipe
Member
NutritionTarget
```

then Iteration 0 was architected incorrectly.

---

# 406. V0 Database Evolution Rule

Implement only tables required now.

But use names/semantics compatible with Domain Model.

---

# 407. No `user_meals` Mega Table

Do not create:

```text
user_id
meal_name
calories
protein
weight
plan_json
```

just because V0 is small.

---

# 408. No Hardcoded Nutrition In UI

Locked.

---

# 409. No Hardcoded Coach Plan In UI

Locked.

---

# 410. No User Id On Everything

Use:

```text
Household
Member
```

correctly from day one.

---

# 411. No AI Placeholder Architecture

No:

```text
ai_service.py
```

unused.

---

# 412. No Planner Placeholder

No empty tab.

No empty module.

---

# 413. No Future Settings

Do not add:

```text
Budget
Variety
Meal Prep
Cooking sessions
```

to profile until those capabilities ship.

---

# 414. Minimal Settings Are A Feature

Iteration 0 should feel almost configuration-free.

---

# 415. Ideal First Day Experience

User signs in.

---

# 416. Onboarding

```text
Alberto

Plan Antrenor

Targeturi?
[ Nu acum ]
```

---

# 417. Today

Immediately:

```text
Azi

Masa 1
[ Vezi variantele ]

Masa 2
[ Vezi variantele ]

Masa 3
[ Vezi variantele ]

Masa 4
[ Vezi variantele ]
```

---

# 418. Breakfast

Tap:

```text
Masa 1
```

---

# 419. Options

```text
Iaurt + ovăz + fructe
Omletă
```

---

# 420. Select

```text
Iaurt + ovăz + fructe

200g iaurt
50g ovăz
50g fructe

[ Am mâncat ]
```

---

# 421. After Tap

```text
✓ Masa 1

390 kcal
24g proteină
```

Today summary updates.

---

# 422. That Is Enough

No AI.

No animation spectacle.

No giant dashboard.

Just:

> works perfectly.

---

# 423. Ideal Seventh Day Experience

User has:

```text
7 days meal history

weight history

macro consumption history

real behavior data
```

---

# 424. Data Is Already Becoming Valuable

Iteration 1 can use:

```text
actual portions
actual repeated choices
```

even before Learning Engine exists.

---

# 425. Future Preference Seed

We should NOT automatically convert history to preferences yet.

But data exists.

---

# 426. Future Target Adaptation

Weight history exists.

---

# 427. Future Recommendations

FoodLog history exists.

---

# 428. Future Weekly Planner

Recipes exist.

---

# 429. Future Product Vision Begins With These Data

This is why V0 is not disposable.

---

# 430. Repository State At V0 Completion

```text
/
├── apps/
│   ├── web/
│   └── api/
│
├── packages/
│   └── api-client/
│
├── docs/
│   ├── product/
│   └── adr/
│
├── infra/
├── scripts/
└── README.md
```

---

# 431. docs/product Final State

```text
docs/product/

00-PRODUCT-VISION.md

01-USER-MODEL-AND-USE-CASES.md

02-MODULE-MAP.md

03-CORE-ENGINES.md

04-NUTRITION-SPEC.md

05-AI-SPEC.md

06-DOMAIN-MODEL.md

07-ITERATIVE-MVP-AND-PRODUCT-ROADMAP.md

08-UX-ARCHITECTURE-AND-USER-FLOWS.md

09-TECHNICAL-ARCHITECTURE.md

10-ITERATION-0-IMPLEMENTATION-BLUEPRINT.md
```

---

# 432. docs/product Is Now Closed

`docs/product` definește:

```text
WHY
WHO
WHAT
HOW IT THINKS
HOW NUTRITION WORKS
HOW AI WORKS
WHAT THE DOMAIN IS
HOW IT EVOLVES
HOW THE USER EXPERIENCES IT
HOW IT IS BUILT
WHAT WE BUILD FIRST
```

Nu trebuie continuat cu:

```text
11
12
13
```

pentru implementation details.

---

# 433. Where Future Technical Documentation Goes

Architecture decisions:

```text
docs/adr/
```

---

# 434. Iteration-Specific Implementation Notes

Pot merge în:

```text
docs/iterations/
```

Exemplu:

```text
docs/iterations/
iteration-01/
iteration-02/
```

---

# 435. API Documentation

Generated automatically.

Nu duplicată în `docs/product`.

---

# 436. Database Documentation

Poate merge:

```text
docs/architecture/database/
```

dacă devine necesară.

---

# 437. Dogfood Notes

```text
docs/product-feedback/
```

---

# 438. Product Docs Must Stay Stable

Nu modificăm permanent Product Vision pentru fiecare mică implementare.

---

# 439. But Product Docs Are Not Sacred

Dacă dogfooding demonstrează că o presupunere fundamentală este greșită:

actualizăm documentul canonical relevant.

---

# 440. Architecture Decision Rule

Dacă implementarea necesită abatere majoră de la `09`:

scriem:

```text
ADR
```

---

# 441. Product Decision Rule

Dacă UX sau domain-ul se schimbă fundamental:

actualizăm:

```text
08
06
etc.
```

---

# 442. No Hidden Architectural Decisions

Nu luăm în cod decizii majore care contrazic documentele fără documentare.

---

# 443. Agent / Developer Implementation Rule

Un agent care implementează V0 trebuie să citească în primul rând:

```text
10
```

dar `10` presupune și respectarea:

```text
03
04
06
08
09
```

---

# 444. Priority On Conflict

Dacă există conflict:

```text
10
```

definește scope-ul Iteration 0.

Dar nu poate încălca invariants din:

```text
04 Nutrition
06 Domain
09 Architecture
```

fără decizie explicită.

---

# 445. Recommended Implementation Order For Agent

```text
Read 10
↓
Read 09
↓
Read 06
↓
Read 04
↓
Read 08
↓
Build foundation
```

Restul documentelor oferă context mai larg.

---

# 446. Implementation Anti-Patterns

Do not:

### 1.

Build future features.

### 2.

Hardcode coach plan in React.

### 3.

Calculate nutrition in TypeScript.

### 4.

Call Supabase tables directly from frontend.

### 5.

Create AI abstractions with no current use.

### 6.

Create microservices.

### 7.

Implement generic planner.

### 8.

Build custom recipe editor.

### 9.

Build giant food database.

### 10.

Require user to fill every target.

### 11.

Require exact grams for every log.

### 12.

Show disabled future tabs.

### 13.

Create a desktop-dashboard-first UI.

### 14.

Treat Account and Member as identical concepts.

### 15.

Lose historical nutrition snapshots.

---

# 447. Implementation Philosophy

For every decision ask:

> **What is the simplest correct implementation that respects the final domain?**

---

# 448. Example

Need daily macro total.

Bad:

```text
store daily_calories on Member
```

Simple but incorrect.

Correct:

```text
FoodLogs
↓
NutritionSnapshots
↓
sum for date
```

Still simple.

Future-proof.

---

# 449. Example

Need coach plan.

Bad:

```text
React array
```

Correct:

```text
MealPlanTemplate
```

Slightly more work.

Massively better foundation.

---

# 450. Example

Need target.

Bad:

```text
members.calorie_target
members.protein_target
```

Correct:

```text
NutritionTargetProfile
NutrientTarget
```

because target history matters.

---

# 451. Example

Need consumed meal.

Bad:

```text
meal.completed = true
```

Correct:

```text
FoodLog
```

because actual consumption becomes long-term truth.

---

# 452. This Is The Core Philosophy Of V0

Do not implement future functionality.

But do implement current functionality using the correct concepts.

---

# 453. Iteration 0 Product North Star

At any point during implementation ask:

> **Does this make tracking today's coach plan easier?**

If answer is no:

probably not part of Iteration 0.

---

# 454. Iteration 0 UX North Star

The most important action:

```text
Am mâncat
```

must feel nearly effortless.

---

# 455. Iteration 0 Technical North Star

The most important invariant:

```text
what the user ate
```

must be stored correctly and reproducibly.

---

# 456. Iteration 0 Data North Star

FoodLogs generated now must remain valid when:

```text
Recommendation Engine
Learning Engine
Adaptive Nutrition
```

exist later.

---

# 457. Iteration 0 Architecture North Star

The codebase after V0 must look like:

> **the beginning of the final product**

not:

> **a prototype that needs to be rewritten.**

---

# 458. Iteration 0 Release Statement

Iteration 0 is ready when we can truthfully say:

> **„Aplicația îmi pune planul alimentar într-o interfață foarte simplă, îmi permite să aleg ce variantă am mâncat, calculează automat nutriția zilei și îmi urmărește greutatea.”**

Nothing more is required.

Nothing less is acceptable.

---

# 459. The Next Upgrade

Only after V0 proves useful:

> **Iteration 1 — Personalized Nutrition & Portions**

will add:

```text
profile
goal
activity
automatic target estimation
portion solver
same recipe / different portions
explicit preferences
```

without replacing the V0 foundation.

---

# 460. Final Blueprint Principle

Iteration 0 succeeds when the user does not think:

> „Folosesc MVP-ul unei aplicații mari.”

The user thinks:

> **„În sfârșit am o aplicație simplă în care planul meu alimentar chiar este ușor de urmărit.”**

And when Iteration 1 ships, the reaction should not be:

> „Aplicația s-a schimbat complet.”

It should be:

> **„Acum face și mai mult pentru mine.”**

That principle must remain true for every iteration that follows.
