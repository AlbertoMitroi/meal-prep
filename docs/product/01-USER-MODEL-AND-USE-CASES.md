# User Model & Use Cases

## 1. Scopul documentului

Acest document definește:

* cine sunt utilizatorii produsului;
* cum sunt organizați într-un household;
* ce informații are sistemul despre fiecare persoană;
* ce informații sunt comune household-ului;
* ce obiective și preferințe pot exista;
* ce contexte de viață trebuie înțelese;
* ce acțiuni trebuie să poată face utilizatorii;
* ce scenarii trebuie să rezolve produsul;
* cum interacționează utilizatorul cu sistemul în viața de zi cu zi.

Acest document nu definește încă:

* structura exactă a bazei de date;
* endpoint-uri;
* UI final;
* algoritmii de optimizare;
* tehnologiile folosite.

Acestea vor deriva ulterior din modelele și use case-urile de aici.

---

# 2. Core User Model

Unitatea de bază a produsului nu este doar `User`.

Produsul trebuie să lucreze cu trei niveluri:

```text
Account
   │
   ▼
Household
   │
   ├── Member A
   ├── Member B
   ├── Member C
   └── ...
```

## Account

Reprezintă identitatea care se autentifică în aplicație.

Un account poate:

* aparține unui household;
* avea propriul profil nutrițional;
* invita alte persoane;
* administra anumite setări;
* vedea propriile date;
* vedea datele household-ului în funcție de permisiuni.

## Household

Reprezintă unitatea comună de planificare.

Household-ul poate conține:

* o singură persoană;
* un cuplu;
* o familie;
* mai mulți adulți;
* membri care participă doar la anumite mese.

Household-ul deține informațiile comune precum:

* pantry;
* frigider;
* congelator;
* shopping lists;
* buget;
* meal plan comun;
* cooking sessions;
* meal prep;
* produse favorite comune;
* magazine preferate.

## Member

Fiecare membru are propriul profil individual.

Membrii pot avea:

* targeturi diferite;
* porții diferite;
* preferințe diferite;
* restricții diferite;
* program diferit;
* obiective diferite;
* zile de antrenament diferite;
* mese luate în locații diferite.

---

# 3. Household Types

Produsul trebuie să suporte nativ mai multe configurații.

## 3.1 Individual

Un singur utilizator.

Exemplu:

```text
Household
└── Alex
```

Toate mesele și cumpărăturile sunt optimizate pentru el.

---

## 3.2 Couple

Două persoane care împart o parte mare din alimentație.

```text
Household
├── Alberto
└── Partner
```

Principiul implicit:

> aceeași masă, porții personalizate.

Pot exista însă excepții.

Exemplu:

* aceeași cină;
* mic dejun diferit;
* ea are lunchbox la birou;
* el mănâncă acasă;
* unul merge la sală;
* unul nu.

---

## 3.3 Family

Mai multe persoane.

```text
Household
├── Adult A
├── Adult B
├── Child A
└── Child B
```

Planner-ul încearcă să reducă numărul de preparate diferite.

Copiii sau alte categorii speciale nu trebuie să primească automat recomandări restrictive pe baza unor formule generice fără mecanisme de siguranță.

---

## 3.4 Flexible Household

Nu toată lumea mănâncă întotdeauna împreună.

Exemplu:

```text
Monday Dinner
Alberto      ✓
Partner      ✓

Tuesday Lunch
Alberto      ✕
Partner      ✓

Friday Dinner
Alberto      ✕
Partner      ✕
```

Planner-ul trebuie să înțeleagă participarea la fiecare masă.

---

# 4. Member Profile

Profilul unui membru trebuie să fie mai mult decât:

> vârstă + greutate + calorii.

El reprezintă contextul folosit de sistem pentru toate recomandările.

---

# 5. Identity & Basic Profile

Pentru fiecare membru:

* nume;
* avatar;
* sex biologic atunci când este necesar pentru formule fiziologice;
* data nașterii / vârsta;
* înălțime;
* greutate curentă;
* unități preferate;
* limbă;
* timezone.

---

# 6. Goal Profile

Utilizatorul trebuie să poată avea un obiectiv principal.

Exemple:

### Fat loss

Scădere în greutate.

### Maintain

Menținerea greutății.

### Muscle gain

Creștere în greutate / masă musculară.

### Eat better

Fără target agresiv de greutate.

Prioritate pe:

* calitatea alimentației;
* micronutrienți;
* varietate;
* consistență.

### Performance

Focus pe sport / antrenament.

### Custom

Target definit manual.

---

# 7. Nutrition Target Profile

Sistemul trebuie să poată lucra cu:

* calorii;
* proteină;
* carbohidrați;
* grăsimi;
* fibre;
* micronutrienți.

Targeturile pot fi:

### Auto-calculated

Sistemul estimează necesarul.

### Manually configured

Utilizatorul sau un profesionist introduce valorile.

### Hybrid

Sistemul calculează inițial și utilizatorul ajustează.

---

# 8. Activity Profile

Pentru fiecare membru:

* nivel general de activitate;
* job sedentar / activ;
* pași aproximativi;
* workout frequency;
* tip de antrenament;
* zile de antrenament;
* durata aproximativă;
* eventual ora antrenamentului.

Exemplu:

```text
Monday
Gym — 18:30

Tuesday
Rest

Wednesday
Gym — 19:00
```

Planner-ul poate folosi aceste date pentru organizarea meselor.

---

# 9. Schedule Profile

Aplicația trebuie să înțeleagă programul real.

Exemplu:

```text
Monday
Office: 09:00–17:00
Gym: 18:30

Tuesday
Remote work

Wednesday
Office
```

Nu este necesară integrarea cu calendarul în prima versiune.

Este suficient un program configurabil.

---

# 10. Meal Location Context

Pentru fiecare interval, membrul poate specifica unde mănâncă.

Exemple:

* home;
* office;
* university;
* traveling;
* restaurant;
* other.

Acest context influențează recomandarea.

---

# 11. Work Lunch Profile

Exemplu:

```text
Office lunch
Monday–Friday

Needs lunchbox: YES
Fridge available: YES
Microwave: YES
Kitchen: NO
```

Planner-ul știe astfel că masa trebuie să fie:

* transportabilă;
* meal-prep-friendly;
* potrivită pentru păstrare;
* încălzibilă dacă este necesar.

---

# 12. Food Preferences

Preferințele trebuie tratate separat de preferințele pentru preparate.

## Preference levels

```text
❤️ Love
👍 Like
😐 Neutral
👎 Avoid
🚫 Never
```

Exemplu:

```text
Eggs            ❤️
Chicken         ❤️
Potatoes        ❤️
Greek yogurt    👍
Salmon          👍
Tuna            😐
Avocado         👎
Mushrooms       🚫
```

---

# 13. Meal Preferences

Separat:

```text
Omelette                ❤️
Chicken Rice Bowl       ❤️
Turkey Pasta            👍
Tuna Salad              👎
Egg Salad               🚫
```

Sistemul nu trebuie să presupună:

> dacă îți plac ouăle, îți plac toate preparatele cu ouă.

---

# 14. Cuisine Preferences

Utilizatorul poate prefera:

* Romanian;
* Italian;
* Mediterranean;
* Asian;
* Mexican;
* Middle Eastern;
* American;
* etc.

Acestea sunt semnale, nu restricții rigide.

---

# 15. Texture & Cooking Preferences

În timp, sistemul poate învăța lucruri precum:

* crispy;
* creamy;
* spicy;
* mild;
* grilled;
* baked;
* soups;
* cold meals;
* warm meals.

Acestea pot influența recommendation ranking.

---

# 16. Dietary Restrictions

Trebuie diferențiate clar de preferințe.

Exemple:

* vegetarian;
* vegan;
* pescatarian;
* lactose-free;
* gluten-free;
* allergies;
* intolerances;
* religious restrictions.

O restricție este un hard constraint.

O preferință este un soft constraint.

---

# 17. Allergies

Alergiile trebuie tratate ca restricții critice.

Un ingredient incompatibil nu trebuie introdus doar pentru că optimizează mai bine macro-urile.

---

# 18. Budget Profile

Bugetul poate exista la nivel de household.

Exemple:

```text
Weekly grocery budget
500 lei
```

sau:

```text
Monthly food budget
2,200 lei
```

Se pot defini separat:

* groceries;
* dining out;
* snacks;
* supplements;
* other.

---

# 19. Cooking Profile

Household-ul trebuie să configureze modul în care vrea să gătească.

## Cooking Frequency

Exemple:

```text
Every day
5 times/week
3 times/week
2 times/week
Meal prep heavy
```

---

# 20. Cooking Time

Exemplu:

```text
Weekdays
Maximum 25 min

Weekend
Maximum 60 min
```

---

# 21. Cooking Skill

Opțional:

```text
Beginner
Intermediate
Advanced
```

Acest lucru poate afecta complexitatea rețetelor.

---

# 22. Available Equipment

Exemple:

* oven;
* stove;
* microwave;
* air fryer;
* blender;
* grill;
* rice cooker;
* multicooker.

Nu recomandăm preparate dependente de echipamente inexistente.

---

# 23. Meal Prep Preference

Household-ul poate declara:

```text
Meal prep:
OFF
LIGHT
MEDIUM
HEAVY
```

Sau direct:

```text
I want to cook:
3 times this week
```

Planner-ul optimizează în jurul acestei constrângeri.

---

# 24. Variety Preference

Slider:

```text
Familiar ───────────── Adventurous
```

Exemplu semantic:

```text
0%     aproape exclusiv favorite
25%    majoritar mese cunoscute
50%    mix echilibrat
75%    multe preparate noi
100%   explorare ridicată
```

Nu trebuie implementat neapărat liniar, dar conceptul trebuie să existe.

---

# 25. Pantry Preference

Household-ul poate activa:

```text
✓ Use pantry first
✓ Minimize food waste
✓ Prioritize expiring items
```

---

# 26. Cost Preference

Poate selecta o strategie:

```text
Balanced
Save Money
Nutrition First
Minimal Cooking
Variety
```

Acestea modifică ponderile recommendation engine-ului.

---

# 27. User Control Philosophy

Automatizarea trebuie să fie puternică, dar utilizatorul trebuie să poată interveni.

Orice plan trebuie să accepte:

* lock;
* replace;
* regenerate;
* skip;
* adjust portion;
* eat something else;
* move meal;
* change participant;
* mark unavailable.

---

# 28. Core User States

În orice moment, sistemul trebuie să cunoască mai multe tipuri de state.

## Daily Nutrition State

Pentru fiecare membru:

```text
Target
Consumed
Planned
Remaining
```

pentru:

* kcal;
* macros;
* micronutrients.

---

## Household Pantry State

```text
Available food
Quantity
Expiry
Storage location
```

---

## Budget State

```text
Weekly budget
Projected spending
Actual spending
Remaining budget
```

---

## Meal Plan State

```text
Planned meals
Locked meals
Completed meals
Skipped meals
Changed meals
```

---

## Preference State

Explicit preferences + learned preferences.

---

## Progress State

* weight;
* weight trend;
* adherence;
* target progression.

---

# 29. Primary Use Case Categories

Use case-urile produsului se împart în:

1. Onboarding & Personalization
2. Daily Meal Decisions
3. Weekly Planning
4. Household Planning
5. Nutrition Optimization
6. Meal Prep & Cooking
7. Pantry Management
8. Grocery Management
9. Budget Management
10. Food Logging
11. Vision & Scanning
12. Learning System
13. Progress Tracking
14. Plan Adaptation
15. Exploration & Discovery

---

# 30. UC-01 — Create Personal Profile

## User intent

> „Vreau ca aplicația să știe cine sunt și cât ar trebui să mănânc.”

### User provides

* age;
* sex;
* height;
* weight;
* goal;
* activity;
* workouts;
* optional target weight.

### System produces

* estimated energy needs;
* suggested calorie target;
* macro targets;
* initial nutrition profile.

### User can

* accept;
* modify;
* use custom values.

---

# 31. UC-02 — Create Household

## Intent

> „Locuim împreună și vrem să planificăm alimentația împreună.”

User:

1. creates household;
2. invites another person;
3. chooses which meals are normally shared.

Example:

```text
Breakfast   Separate
Lunch       Mostly separate
Dinner      Shared
Weekend     Shared
```

---

# 32. UC-03 — Configure Food Preferences

User poate:

* căuta alimente;
* marca Love / Like / Neutral / Avoid / Never;
* face onboarding rapid;
* modifica ulterior.

Sistemul folosește preferințele pentru ranking.

---

# 33. UC-04 — Configure Meal Preferences

După ce există rețete:

User poate:

* favorite;
* rate;
* dislike;
* remove from recommendations.

---

# 34. UC-05 — Generate Breakfast

## Intent

> „Nu vreau să mă gândesc ce mănânc dimineața.”

User apasă:

> Generate breakfast

System considers:

* daily target;
* current state;
* time of day;
* preferences;
* pantry;
* planned meals later;
* training schedule;
* variety;
* budget.

Output:

1–3 recomandări potrivite.

---

# 35. UC-06 — Regenerate Meal

User:

> „Nu vreau asta.”

Apasă:

> Generate another

System păstrează toate constrângerile și schimbă preparatul.

Nu trebuie să piardă contextul nutrițional.

---

# 36. UC-07 — Generate Lunch Based on Breakfast

Breakfast a fost consumat.

System recalculates:

* macros remaining;
* micronutrient gaps;
* energy remaining.

Lunch recommendation trebuie să completeze aceste lipsuri.

Aceasta este una dintre funcțiile centrale ale produsului.

---

# 37. UC-08 — Nutrient Gap Optimization

Exemplu:

```text
After breakfast

Protein        28%
Fiber          18%
Calcium        61%
Vitamin C      12%
Iron           35%
```

Sistemul caută mese care maximizează progresul către targeturi fără să compromită:

* calorii;
* preferințe;
* buget;
* program.

---

# 38. UC-09 — Lock Desired Dinner

User spune:

> „Diseară vreau carbonara.”

System:

1. adaugă carbonara drept locked meal;
2. estimează/calculază porția;
3. recalculează restul zilei;
4. adaptează eventual snack-ul / prânzul.

Principiu:

> Adapt, Don't Punish.

---

# 39. UC-10 — Ask „What Should I Eat?”

User intră în aplicație fără să specifice tipul mesei.

System knows:

* time;
* meals consumed;
* schedule;
* remaining nutrition;
* pantry;
* preferences.

Returnează recomandarea cea mai relevantă.

---

# 40. UC-11 — Quick Meal Replacement

Plan:

> Chicken Rice Bowl

User:

> „Nu am chef.”

Apasă:

> Replace

System găsește o alternativă cu:

* valoare nutrițională apropiată;
* ingrediente compatibile;
* cost rezonabil;
* timp similar.

---

# 41. UC-12 — Generate Weekly Meal Plan

User setează:

```text
Budget: 500 lei
Cooking: 3 times
Meal prep: yes
Variety: medium
Use pantry first: yes
```

System generates:

* breakfast;
* lunch;
* dinner;
* optional snacks;
* portions;
* cooking sessions;
* leftovers;
* grocery list;
* cost projection.

---

# 42. UC-13 — Configure Week Constraints

Înainte de generare:

```text
Monday
Dinner at home

Tuesday
Partner office lunch
Dinner together

Wednesday
Dinner out

Thursday
Office lunch

Friday
Pizza night
```

Planner-ul respectă aceste constrângeri.

---

# 43. UC-14 — Shared Meal, Personalized Portions

Dinner:

> Chicken Pasta

System calculates:

```text
Member A
720 kcal

Member B
510 kcal
```

și gramajele fiecărui ingredient.

---

# 44. UC-15 — Combined Cooking Quantities

După calculul porțiilor individuale:

```text
Member A
Chicken 180g
Pasta 100g

Member B
Chicken 125g
Pasta 65g
```

Cook view afișează:

```text
Total to cook

Chicken 305g
Pasta 165g
```

și separat instrucțiuni de porționare.

---

# 45. UC-16 — Office Lunch Planning

Membrul configurează lunchbox.

Planner-ul prioritizează rețete:

* transportabile;
* potrivite la frigider;
* microwave-friendly;
* meal prep friendly.

---

# 46. UC-17 — Batch Cooking

User spune:

> „Vreau să gătesc de 3 ori săptămâna asta.”

System grupează mesele în cooking sessions.

Exemplu:

```text
Sunday
Cook 6 servings

Wednesday
Cook 6 servings

Friday
Cook 4 servings
```

---

# 47. UC-18 — Meal Prep Containers

După gătit:

```text
Container 1
Alberto — Monday Lunch

Container 2
Partner — Monday Lunch

Container 3
Partner — Tuesday Lunch
```

Fiecare container are porția individuală.

---

# 48. UC-19 — Track Leftovers

După masă:

```text
2 servings remaining
```

Sistemul:

* adaugă leftovers;
* estimează data limită;
* încearcă să le reutilizeze în plan.

---

# 49. UC-20 — Pantry Manual Entry

User poate adăuga:

```text
Chicken
600g

Eggs
8

Rice
1kg
```

---

# 50. UC-21 — Barcode Product Entry

User scanează produsul.

System:

1. detectează barcode;
2. caută produsul;
3. afișează nutriția;
4. user confirmă;
5. salvează produsul.

---

# 51. UC-22 — Set Preferred Product

Exemplu:

> Greek Yogurt → Pilos Greek Yogurt 2%

De atunci, rețetele generice pot utiliza implicit produsul real preferat.

---

# 52. UC-23 — Scan Fridge

User fotografiază frigiderul.

AI detectează:

* ouă;
* iaurt;
* legume;
* pui;
* etc.

System afișează:

> We found these items.

User confirmă.

Doar după confirmare se actualizează pantry.

---

# 53. UC-24 — „What Can I Cook With What I Have?”

System uses:

* pantry;
* remaining nutrition;
* household participants;
* preferences;
* expiry;
* cooking time.

Output:

rețete direct executabile.

---

# 54. UC-25 — Prioritize Expiring Food

Exemplu:

```text
Chicken expires tomorrow
Greek yogurt expires in 2 days
```

Planner-ul crește scorul rețetelor care folosesc aceste produse.

---

# 55. UC-26 — Generate Grocery List

Din weekly plan:

System agregă toate ingredientele.

Exemplu:

```text
Chicken
1.8kg

Rice
900g

Greek yogurt
1.4kg
```

---

# 56. UC-27 — Subtract Pantry From Shopping

Necesar:

```text
Rice
1,000g
```

Pantry:

```text
Rice
350g
```

Shopping:

```text
Buy 650g
```

---

# 57. UC-28 — Package Rounding

Dacă produsul preferat vine la:

```text
400g
```

și necesarul este:

```text
1,430g
```

System recomandă:

```text
Buy 4 × 400g
```

și știe că rămân aproximativ:

```text
170g
```

---

# 58. UC-29 — Grocery Checklist

În magazin:

```text
☐ Chicken
☐ Greek yogurt
☐ Eggs
☐ Rice
```

User bifează cumpărăturile.

---

# 59. UC-30 — Scan Receipt

După cumpărături:

User fotografiază bonul.

AI încearcă să extragă:

* produs;
* cantitate;
* preț;
* magazin;
* total.

User confirmă.

---

# 60. UC-31 — Receipt → Pantry

Produsele confirmate pot fi adăugate automat în pantry.

---

# 61. UC-32 — Receipt → Price History

System salvează:

```text
Product
Store
Price
Date
Package size
```

Construind în timp o bază locală de prețuri.

---

# 62. UC-33 — Meal Cost

Pentru fiecare masă:

System calculează costul ingredientelor folosite.

Exemplu:

```text
Chicken Rice Bowl
9.84 lei
```

---

# 63. UC-34 — Household Meal Cost

```text
Dinner for household
17.20 lei
```

---

# 64. UC-35 — Weekly Budget Planning

Planner-ul încearcă să genereze:

```text
Projected cost ≤ Weekly budget
```

Dacă nu este posibil, sistemul semnalează.

---

# 65. UC-36 — Adapt Plan to Remaining Budget

Este joi.

```text
Weekly budget: 500
Spent: 390
Remaining: 110
```

System ajustează opțiunile pentru zilele următoare.

---

# 66. UC-37 — Optimize for Cheap Week

User selectează:

> Save Money

Planner-ul prioritizează:

* ingrediente ieftine;
* ingredient reuse;
* seasonal basics;
* pantry usage;
* batch cooking.

Dar trebuie în continuare să respecte minimum nutrition quality.

---

# 67. UC-38 — Scan Scale

User fotografiază cântarul.

AI:

```text
Detected: 94.7 kg
```

User:

> Confirm

System creează weight log.

---

# 68. UC-39 — Manual Weight Entry

Alternativ:

```text
94.7 kg
```

fără fotografie.

---

# 69. UC-40 — Weight Trend

System nu pune accent pe fluctuația zilnică.

Afișează:

* current weight;
* rolling average;
* weekly trend;
* progression.

---

# 70. UC-41 — Meal Photo Tracking

User fotografiază masa.

AI detectează aproximativ:

```text
Chicken
Rice
Broccoli
```

și estimează porțiile.

User confirmă sau ajustează.

---

# 71. UC-42 — Planned Meal Photo Confirmation

Dacă masa era deja planificată:

AI încearcă mai întâi să confirme:

> This looks like your planned Chicken Rice Bowl.

User poate:

> Log planned meal

Această metodă este mai precisă decât estimarea completă din fotografie.

---

# 72. UC-43 — Quick Food Logging

User poate loga din:

* recent;
* favorite;
* planned;
* saved product;
* barcode;
* photo;
* manual.

---

# 73. UC-44 — One-Tap Planned Meal Log

În Today:

```text
Lunch
Chicken Rice Bowl

[ I ate this ]
```

Un tap.

---

# 74. UC-45 — Adjust Actual Portion

Plan:

```text
180g chicken
```

User a consumat aproximativ:

```text
150g
```

Poate ajusta și state-ul zilnic se recalculează.

---

# 75. UC-46 — Skip Meal

User poate marca:

> Skip

Sistemul recalculează restul zilei.

---

# 76. UC-47 — Unplanned Restaurant Meal

User poate spune:

> „Am mâncat burger și cartofi.”

System:

1. estimează/loghează masa;
2. recalculează restul zilei;
3. adaptează recomandările.

Nu penalizează utilizatorul.

---

# 77. UC-48 — Dining Out Planned in Advance

User:

> Friday dinner out.

Planner-ul poate rezerva un buget caloric estimat și nu planifică ingrediente inutile pentru acea masă.

---

# 78. UC-49 — Food Preference Learning

Sistemul urmărește:

* meals accepted;
* meals regenerated;
* meals skipped;
* ratings;
* favorites.

---

# 79. UC-50 — Meal Preference Learning

Dacă user refuză constant:

> Tuna Salad

dar acceptă:

> Tuna Pasta

sistemul învață diferența.

---

# 80. UC-51 — Implicit Preference Learning

Exemplu:

```text
Recommended 8 times
Accepted 7 times
Rated 5/5 twice
```

Recipe affinity crește automat.

---

# 81. UC-52 — Novelty Management

Sistemul urmărește istoricul recent.

Nu recomandă aceeași masă prea des dacă Variety este ridicat.

---

# 82. UC-53 — Discover New Meal

System poate sugera:

> „Based on foods you love, you might like this.”

Dar preparatul trebuie în continuare să respecte contextul nutrițional.

---

# 83. UC-54 — „I Have 15 Minutes”

User:

> „Am doar 15 minute.”

System filtrează variantele disponibile.

---

# 84. UC-55 — „I Don't Want to Cook”

System poate recomanda:

* leftovers;
* no-cook meals;
* quick assembly;
* saved convenience products;
* eventual eating out, dacă user a configurat astfel.

---

# 85. UC-56 — „Use This Ingredient”

User:

> „Vreau să folosesc somonul.”

Ingredientul devine constraint.

System găsește cea mai bună masă în jurul lui.

---

# 86. UC-57 — „This Expires Tomorrow”

User poate marca manual expiry sau sistemul îl cunoaște.

Recommendation engine prioritizează folosirea lui.

---

# 87. UC-58 — „I Want Pasta Tonight”

Ingredient / dish craving devine soft sau hard constraint.

Sistemul optimizează porția și restul zilei.

---

# 88. UC-59 — „Generate Something Different”

User cere noutate.

System crește temporar novelty weight.

---

# 89. UC-60 — „Keep This Meal Every Monday”

Recurring / locked meal.

Exemplu:

```text
Monday breakfast
Greek Yogurt Bowl
```

Planner-ul construiește restul planului în jurul lui.

---

# 90. UC-61 — Lock Breakfast for Entire Week

Exemplu:

> „Vreau același mic dejun toată săptămâna.”

System respectă.

---

# 91. UC-62 — Mixed Household Preferences

Exemplu:

```text
Alberto loves rice
Partner avoids rice
```

System încearcă:

1. shared meal cu componente ajustabile;
2. side diferit;
3. doar dacă este necesar, preparate separate.

---

# 92. UC-63 — One Member Not Eating

Partner:

> Dinner elsewhere.

System:

* scoate porția;
* recalculează cantitatea de gătit;
* actualizează grocery needs dacă este înainte de cumpărături.

---

# 93. UC-64 — Add Guest

Temporar:

```text
Friday dinner
+1 guest
```

System crește cantitatea, fără să necesite profil nutrițional complet.

---

# 94. UC-65 — Separate Meal for One Member

Exemplu:

un membru are o restricție alimentară incompatibilă.

Planner-ul poate genera o masă diferită doar pentru acea persoană.

---

# 95. UC-66 — Replan Entire Day

Dacă planul se schimbă:

> Replan today

System păstrează:

* ce a fost deja mâncat;
* locked meals;

și regenerează viitorul zilei.

---

# 96. UC-67 — Replan Remaining Week

User:

> „Nu mai avem timp să gătim miercuri.”

System reproiectează doar zilele viitoare.

Nu modifică istoricul.

---

# 97. UC-68 — Increase/Decrease Budget Midweek

Dacă bugetul se schimbă:

System recalculates planned meals și shopping.

---

# 98. UC-69 — Pantry Unexpected Change

Exemplu:

> „Am terminat puiul.”

System marchează cantitatea zero și poate recomanda alternative.

---

# 99. UC-70 — Training Day Meal Adaptation

În ziua de workout:

System poate ajusta distribuția meselor în funcție de strategia configurată.

Nu trebuie să presupună automat un anumit model.

---

# 100. UC-71 — Rest Day Adaptation

Dacă profilul folosește calorie/macro cycling:

rest day poate avea target diferit.

---

# 101. UC-72 — Daily Summary

La sfârșitul zilei:

```text
Calories
Protein
Fiber
Micronutrient coverage
Plan adherence
Budget impact
```

În mod simplu și necritic.

---

# 102. UC-73 — Weekly Summary

System poate afișa:

* average calories;
* protein consistency;
* nutrient coverage;
* weight trend;
* spending;
* grocery waste;
* cooking sessions;
* favorite meals;
* plan adherence.

---

# 103. UC-74 — Learning Summary

Opțional:

> „Ai preferat mesele cu ouă și pui și ai înlocuit 4 dintre cele 5 mese cu ton.”

Utilizatorul poate corecta învățarea.

---

# 104. UC-75 — Correct AI

Dacă AI spune:

> Greek yogurt

și de fapt este cottage cheese:

User corectează.

Această corecție poate fi utilizată în contextul local pentru personalizare.

---

# 105. UC-76 — AI Coach Natural Language

User poate scrie:

> „Mâine suntem plecați până la 18:00 și vrem ceva rapid seara.”

AI transformă mesajul în constraints și propune plan.

---

# 106. UC-77 — AI Coach Multi-Constraint Request

Exemplu:

> „Mâine vrem sub 60 lei, eu merg la sală, ea are nevoie de pachet la birou și avem pui care expiră.”

System interprets:

```text
Budget constraint
Workout context
Lunchbox constraint
Expiring ingredient constraint
```

și generează soluția.

---

# 107. UC-78 — AI Explains Recommendation

User:

> „De ce mi-ai recomandat asta?”

System poate explica pe scurt:

* completează proteina;
* crește fibrele;
* folosește ce există în frigider;
* respectă bugetul;
* este potrivit pentru lunchbox.

---

# 108. UC-79 — Hidden Complexity

Default user experience nu trebuie să forțeze utilizatorul să configureze fiecare detaliu.

Exemplu:

> Generate lunch

trebuie să funcționeze chiar dacă utilizatorul nu configurează manual toate cele 30 de preferințe.

Sistemul utilizează best defaults.

---

# 109. UC-80 — Progressive Onboarding

Onboarding-ul inițial trebuie să colecteze doar informațiile esențiale.

Restul poate fi învățat progresiv.

### Essential

* basic profile;
* goal;
* activity;
* major restrictions;
* câteva preferințe;
* household.

### Later

* detailed schedule;
* equipment;
* cuisines;
* budget;
* advanced nutrient preferences.

---

# 110. Important Edge Cases

## User does not know exact weight of food

Allow estimate.

## Product is missing from database

Manual/custom product.

## Meal has incomplete nutritional data

Mark uncertainty rather than fabricate.

## Budget price is unknown

Use estimate and mark it.

## Pantry quantity is unknown

Allow:

```text
In stock
Low
Out
```

instead of forcing grams.

## AI cannot recognize image

Fall back to manual input.

## Household members want totally different meals

Support exceptions.

## No recommendation satisfies all constraints

System relaxes soft constraints and explains.

Hard constraints remain respected.

---

# 111. Hard Constraints vs Soft Preferences

Acest model trebuie să existe conceptual peste tot.

## Hard constraints

Nu pot fi încălcate.

Exemple:

* allergies;
* dietary restriction;
* locked meal;
* unavailable equipment;
* excluded ingredient.

## Soft constraints

Pot fi compromise pentru a obține o soluție mai bună.

Exemple:

* favorite foods;
* budget preference;
* variety;
* prep time target;
* ingredient reuse.

---

# 112. Recommendation Context

Orice recomandare serioasă trebuie să poată avea acces la un context asemănător cu:

```text
WHO
- member
- household participants

GOALS
- calories
- macros
- micronutrients

TODAY
- consumed
- planned
- remaining

TIME
- current time
- schedule
- workout

FOOD
- preferences
- restrictions
- recent history

KITCHEN
- pantry
- expiry
- leftovers
- equipment

LOGISTICS
- home/office
- lunchbox
- meal prep
- cooking time

MONEY
- budget remaining
- ingredient costs

PLAN
- locked meals
- upcoming meals
```

Acesta este „creierul contextual” al aplicației.

---

# 113. Primary Daily User Flow

Un scenariu ideal:

```text
Morning

Weight photo
      ↓
Weight logged
      ↓
Generate breakfast
      ↓
Eat / confirm
      ↓
Daily Nutrition State updated
      ↓

Noon

Generate lunch
      ↓
Nutrient Gap Engine evaluates breakfast
      ↓
Lunch suggestion
      ↓
Eat / log
      ↓

Afternoon

Optional snack recommendation
      ↓

Evening

User:
"I want pasta"
      ↓
Pasta becomes constraint
      ↓
System determines best portion / recipe
      ↓
Dinner
      ↓

End of day

Daily nutrition completed
```

---

# 114. Primary Weekly User Flow

```text
Thursday / Friday / Sunday

Configure next week
      ↓
Schedule
      ↓
Budget
      ↓
Cooking frequency
      ↓
Locked meals
      ↓
Office lunches
      ↓
Training
      ↓
Pantry
      ↓
Generate Week
      ↓
Review
      ↓
Lock / Regenerate
      ↓
Generate Grocery List
      ↓
Shop
      ↓
Scan receipt
      ↓
Pantry updated
      ↓
Meal Prep
      ↓
Execute week
```

---

# 115. Product Personas

Nu trebuie neapărat folosite ca marketing personas, dar ajută la validarea produsului.

## Persona A — „I don't want to think about food”

Vrea:

* să slăbească;
* să nu numere manual;
* să primească recomandări.

Main value:

> Decision reduction.

---

## Persona B — The Couple

Vrea:

* să mănânce împreună;
* să nu gătească două mese;
* să aibă porții diferite;
* o singură listă de cumpărături.

Main value:

> One meal, personalized portions.

---

## Persona C — Busy Office Worker

Vrea:

* pachet;
* meal prep;
* mese rapide;
* minimum cooking.

Main value:

> Logistics optimization.

---

## Persona D — Nutrition Optimizer

Vrea:

* macros;
* micronutrients;
* tracking;
* precision;
* detailed insights.

Main value:

> Nutrition optimization.

---

## Persona E — Budget Conscious Household

Vrea:

* cost predictibil;
* grocery optimization;
* minimum waste;
* ingredient reuse.

Main value:

> Cost optimization.

---

# 116. Jobs To Be Done

## Functional Job

> Când trebuie să decid ce mănânc, vreau ca sistemul să ia în calcul nevoile și contextul meu și să-mi ofere o opțiune potrivită, astfel încât să nu trebuiască să fac singur toate calculele și planificarea.

## Household Job

> Când gătim pentru mai multe persoane, vreau să gătim cât mai mult aceeași mâncare, dar să avem porții potrivite fiecăruia.

## Weekly Planning Job

> Când începe o nouă săptămână, vreau să primesc un plan realist, o listă de cumpărături și un plan de gătit care respectă timpul și bugetul nostru.

## Nutrition Job

> După fiecare masă, vreau ca restul zilei să fie adaptat automat astfel încât să mă apropii cât mai mult de nevoile mele nutriționale.

## Budget Job

> Când planific alimentația, vreau să știu cât mă costă și să evit surprizele la finalul lunii.

## Learning Job

> Cu cât folosesc aplicația mai mult, vreau să devină mai bună la a-mi recomanda lucruri pe care chiar vreau să le mănânc.

---

# 117. User Success Definition

Un utilizator are succes dacă, după o perioadă de utilizare:

* petrece mai puțin timp gândindu-se ce să mănânce;
* își atinge mai consistent targeturile;
* face cumpărături mai organizate;
* aruncă mai puțină mâncare;
* gătește conform timpului disponibil;
* respectă mai bine bugetul;
* găsește recomandările din ce în ce mai relevante;
* poate schimba planul fără să „strice” întreaga săptămână.

---

# 118. Product Success Definition

Produsul funcționează dacă ajunge să răspundă constant la:

> **„Ce ar trebui să mănânc acum?”**

cu un răspuns care este simultan:

* potrivit nutrițional;
* potrivit preferințelor;
* realist logistic;
* compatibil cu household-ul;
* realizabil cu timpul disponibil;
* rezonabil ca preț;
* conectat cu restul zilei și al săptămânii.

---

# 119. Guiding Use Case

Dacă trebuie să alegem un singur scenariu după care să judecăm întreaga arhitectură, acesta este:

> Două persoane locuiesc împreună, au necesar caloric și preferințe diferite, una are nevoie de prânz la pachet, vor să gătească de trei ori pe săptămână, au un buget limitat, au deja câteva ingrediente în frigider și vor ca aplicația să le genereze o săptămână de mese comune cu porții personalizate, să optimizeze nutrienții fiecăruia după fiecare masă și să producă automat lista de cumpărături și planul de meal prep.

Dacă produsul poate rezolva elegant acest scenariu, majoritatea scenariilor individuale devin subseturi ale aceleiași arhitecturi.

---

# 120. Core Product Rule

În orice situație, sistemul trebuie să încerce să răspundă la cinci întrebări:

1. **Cine mănâncă?**
2. **De ce are nevoie fiecare?**
3. **Ce s-a mâncat deja și ce urmează?**
4. **Ce este realist acum din punct de vedere al timpului, alimentelor și banilor?**
5. **Care este cea mai bună decizie pe care o putem lua cu toate aceste informații?**

Aceste cinci întrebări trebuie să stea la baza tuturor modulelor care urmează.
