# 04 — Nutrition Specification

## 1. Scopul documentului

Acest document definește modelul nutrițional canonical al produsului.

El stabilește:

* cum estimăm necesarul energetic;
* cum definim obiectivele de calorii;
* cum calculăm macronutrienții;
* cum tratăm micronutrienții;
* cum calculăm fibrele;
* cum tratăm zahărul, grăsimile saturate, sodiul și alte limite;
* cum diferențiem target, minimum, maximum și interval;
* cum tratăm raw vs cooked;
* cum calculăm nutriția rețetelor;
* cum gestionăm informațiile nutriționale incomplete;
* cum gestionăm incertitudinea;
* cum funcționează Nutrient Gap Engine;
* cum optimizăm mesele succesive;
* cum evaluăm nutriția zilnic vs săptămânal;
* cum folosim weight trend;
* cum propunem adaptări de target;
* ce poate și ce nu poate automatiza produsul.

Acesta trebuie să fie:

> **single source of truth pentru toate deciziile nutriționale ale aplicației.**

---

# 2. Nutrition Philosophy

Produsul nu trebuie construit în jurul ideii:

> „Atinge exact toate numerele în fiecare zi.”

Trebuie construit în jurul:

> **Adequacy + Balance + Moderation + Diversity + Adherence**

Acestea sunt compatibile cu principiile actuale WHO pentru o alimentație sănătoasă.

Prin urmare, Nutrition Engine trebuie să optimizeze pentru:

```text
1. suficiență
2. echilibru
3. limite rezonabile
4. varietate
5. realism
6. consistență în timp
```

nu pentru perfecțiune matematică zilnică.

---

# 3. Nutrition Is a Model, Not Absolute Truth

Chiar și cele mai bune calcule au incertitudine.

Necesarul energetic real al unei persoane nu poate fi cunoscut perfect doar din:

```text
age
weight
height
activity
```

Valorile nutriționale ale alimentelor au și ele variație.

Porțiile estimate din fotografie au variație și mai mare.

Din acest motiv, fiecare calcul important trebuie să distingă:

```text
CALCULATED
MEASURED
DECLARED
ESTIMATED
INFERRED
```

---

# 4. Scientific Reference Layer

Aplicația trebuie să aibă un concept explicit:

```text
NutritionReferenceRuleSet
```

Exemplu:

```text
region: EU
authority: EFSA
version: 2026-compatible
```

Pentru ghiduri generale poate utiliza și WHO.

EFSA publică DRV-uri pentru energie, proteină, carbohidrați, grăsimi, fibre, apă, vitamine și minerale și oferă un DRV Finder dedicat.

---

# 5. DRV Is Not an Individual Diagnosis

Foarte important:

EFSA precizează că Dietary Reference Values sunt valori de referință pentru populații sănătoase și nu reprezintă automat necesarul exact al unui individ.

Prin urmare aplicația nu trebuie să spună:

> „Ai nevoie exact de 3.500 mg potassium.”

Mai corect:

> **Reference target: 3.500 mg**

și:

> **Estimated intake: ...**

---

# 6. Never Diagnose Deficiency

Dacă sistemul observă:

```text
Vitamin C
54% of reference
```

NU trebuie să spună:

> „Ai deficit de vitamina C.”

Trebuie să spună:

> **Vitamin C intake is currently below your reference target.**

sau simplificat:

> **Vitamin C — low coverage today**

Diagnosticul unei deficiențe poate necesita evaluare medicală și/sau analize.

---

# 7. Three Target Sources

Orice target trebuie să aibă:

```text
target_source
```

cu una dintre valorile:

### SYSTEM_ESTIMATED

Calculat de produs.

### USER_CONFIGURED

Setat de utilizator.

### PROFESSIONAL_CONFIGURED

Setat conform unui plan primit de la:

* dietetician;
* medic;
* alt profesionist relevant.

---

# 8. Priority of Target Sources

În mod normal:

```text
PROFESSIONAL_CONFIGURED
        >
USER_CONFIGURED
        >
SYSTEM_ESTIMATED
```

Sistemul nu trebuie să rescrie automat un target profesional.

---

# 9. Target Versioning

Fiecare target trebuie să păstreze:

```text
valid_from
valid_until
source
calculation_method
engine_version
created_at
```

Astfel putem răspunde:

> „Ce target avea utilizatorul în luna iunie?”

---

# 10. Supported Population — Automatic Mode

Auto Nutrition Planning trebuie lansat inițial pentru:

> **adulți sănătoși, 18+**

pentru care nu există situații cunoscute care necesită management nutrițional medical specializat.

---

# 11. Protected / Professional Mode

Pentru:

* minori;
* sarcină;
* alăptare;
* boli diagnosticate care modifică necesarul alimentar;
* diete terapeutice;
* alte situații speciale;

aplicația nu trebuie să genereze automat targeturi agresive de slăbit sau recomandări terapeutice.

Poate:

```text
use professional-defined targets
+
plan meals around those targets
```

---

# 12. Energy Architecture

Trebuie separate trei concepte:

```text
REE
TDEE
ENERGY TARGET
```

---

# 13. Resting Energy Expenditure — REE

REE reprezintă energia estimată consumată în repaus.

Pentru adulți, fallback-ul standard al produsului poate fi ecuația Mifflin–St Jeor.

Formula publicată este:

```text
Male:
REE =
10 × weightKg
+ 6.25 × heightCm
- 5 × age
+ 5
```

```text
Female:
REE =
10 × weightKg
+ 6.25 × heightCm
- 5 × age
- 161
```

Ecuația a fost derivată pe adulți sănătoși de greutăți diferite și rămâne un instrument predictiv, nu o măsurătoare directă.

---

# 14. REE Source Priority

Dacă avem:

```text
measured REE
```

dintr-o măsurătoare validă:

aceasta poate avea prioritate.

Ordinea conceptuală:

```text
1. measured valid REE
2. professional configured REE
3. system predicted REE
```

---

# 15. Physical Activity Level

EFSA folosește pentru estimarea necesarului energetic valori PAL precum:

```text
1.4  low activity
1.6  moderately active
1.8  active
2.0  very active
```

Acestea pot constitui baza modelului inițial.

---

# 16. Estimated Maintenance Energy

Conceptual:

```text
TDEE_estimated =
REE × PAL
```

Rezultatul trebuie prezentat ca:

> **Estimated maintenance**

nu:

> „Tu arzi exact 2.743 kcal.”

---

# 17. No False Precision

Intern putem calcula:

```text
2743.18 kcal
```

UI trebuie să afișeze:

```text
~2,750 kcal
```

sau:

```text
Estimated maintenance:
2,700–2,800 kcal
```

acolo unde UX-ul permite.

---

# 18. Activity Calculation Modes

Trebuie să avem două modele distincte.

## PAL Mode

Activitatea obișnuită și workout-urile sunt reflectate în PAL.

Nu adăugăm separat:

```text
+ 600 kcal workout
```

pentru că riscăm double-counting.

---

## Dynamic Activity Mode

Versiune avansată.

Poate folosi:

* steps;
* training schedule;
* wearable data;
* activity history.

Dar nu trebuie să presupună că `exercise calories` raportate de un wearable sunt perfect exacte.

---

# 19. MVP Energy Mode

Pentru MVP:

> **PAL-based maintenance + weight-trend feedback**

este preferabil unui sistem complicat de exercise-calorie compensation.

---

# 20. Goal Energy

După maintenance:

```text
goal_energy =
maintenance
+ goal_adjustment
```

---

# 21. Maintenance Goal

```text
goal_adjustment = 0
```

---

# 22. Fat Loss Goal

Conceptual:

```text
goal_energy =
estimated_maintenance
× (1 - deficit_rate)
```

Produsul trebuie să folosească implicit doar un deficit moderat preaprobat pentru modul automat.

CDC descrie pierderea graduală în greutate drept abordarea preferabilă față de scăderea rapidă.

---

# 23. Deficit Is a Configuration, Not a Constant

Nu hardcodăm:

```text
everyone = -500 kcal
```

Deficitul poate depinde de:

* maintenance;
* greutate;
* obiectiv;
* activity;
* training;
* adherence;
* professional target.

---

# 24. Product Deficit Presets

Conceptual:

```text
Gentle
Moderate
Custom
Professional
```

În Auto Mode nu oferim preset agresiv.

Valorile exacte ale benzilor trebuie păstrate într-un:

```text
NutritionSafetyRuleSet
```

care poate fi actualizat fără schimbarea engine-ului.

---

# 25. Muscle Gain Goal

Conceptual:

```text
goal_energy =
maintenance
+ controlled_surplus
```

Surplusul trebuie să fie configurabil și conservator în Auto Mode.

---

# 26. Eat Better Goal

Nu necesită neapărat deficit sau surplus.

```text
energy target ≈ maintenance
```

iar recommendation scoring crește greutatea pentru:

* nutrient coverage;
* fibre;
* diversity;
* food groups;
* adherence.

---

# 27. Energy Is Better Evaluated Over Time

O diferență de:

```text
+150 kcal
```

într-o zi nu trebuie prezentată ca eșec.

Trebuie să putem analiza:

```text
daily intake
weekly average
rolling energy average
```

---

# 28. Flexible Energy Budget

Dacă targetul săptămânal permite:

```text
Monday     2100
Tuesday    2200
Wednesday  2050
Thursday   2200
```

aceasta poate fi perfect valid.

Nu trebuie să forțăm aceeași valoare în fiecare zi.

---

# 29. Locked Meal Reservation

Exemplu:

```text
Daily target:
2100 kcal

Breakfast eaten:
450

Dinner locked:
850
```

Engine-ul calculează:

```text
Available for lunch + snacks:
800 kcal
```

---

# 30. Energy State

Pentru fiecare zi:

```text
target_energy
consumed_energy
planned_energy
locked_energy
unallocated_energy
```

---

# 31. Macronutrient Architecture

Macros:

```text
Protein
Carbohydrates
Fat
Fiber
```

Nu trebuie să fie tratate toate identic.

---

# 32. Protein — General Health Baseline

EFSA stabilește pentru adulți un Population Reference Intake de:

```text
0.83 g protein / kg body weight / day
```

În produs aceasta reprezintă un:

> **general-health reference baseline**

nu automat targetul optim pentru orice utilizator activ.

---

# 33. Protein — Active Profiles

Pentru persoane care fac exerciții regulat, țintele pot fi mai ridicate.

Materialele ACSM indică aproximativ:

```text
1.2–1.7 g/kg/day
```

pentru persoane active, iar poziția ISSN pentru indivizi care se antrenează indică un interval de aproximativ:

```text
1.4–2.0 g/kg/day
```

în funcție de context.

---

# 34. Protein Target Modes

Conceptual:

```text
GENERAL_HEALTH
ACTIVE
RESISTANCE_TRAINING
PERFORMANCE
CUSTOM
PROFESSIONAL
```

Fiecare are propriul rule set.

---

# 35. Protein Reference Weight

Nu trebuie să presupunem mereu:

```text
protein =
current_body_weight × multiplier
```

Pentru anumite compoziții corporale acest lucru poate produce targeturi nepotrivite.

Trebuie să existe:

```text
protein_reference_weight_mode
```

cu opțiuni precum:

```text
CURRENT_WEIGHT
TARGET_WEIGHT
REFERENCE_WEIGHT
LEAN_MASS
PROFESSIONAL_VALUE
```

---

# 36. Protein Solver

Targetul final:

```text
protein_target =
reference_weight
× profile_multiplier
```

apoi se verifică:

* compatibilitatea energetică;
* contextul utilizatorului;
* target profesional;
* safety rule set.

---

# 37. Protein Is a Target Range

Preferabil:

```text
minimum
preferred
upper_planning_range
```

nu:

```text
exactly 172.00g
```

Exemplu conceptual:

```text
min: 150g
preferred: 165g
soft upper: 180g
```

---

# 38. Protein Scoring

Dacă user are:

```text
target: 165g
actual: 162g
```

scorul trebuie să fie practic maxim.

Nu penalizăm diferența de `3g`.

---

# 39. Protein Distribution

Produsul poate favoriza distribuția proteinei în mai multe mese, mai ales pentru profilurile active.

Dar acest lucru este:

```text
soft optimization
```

nu hard constraint.

---

# 40. Fat Target

EFSA indică pentru adulți un interval de referință de aproximativ:

```text
20–35% din energie
```

pentru grăsimi totale.

---

# 41. Fat Architecture

Trebuie separate:

```text
total_fat
saturated_fat
monounsaturated_fat
polyunsaturated_fat
trans_fat
omega_3
omega_6
```

dacă datele există.

---

# 42. Saturated Fat

WHO recomandă ca grăsimile saturate să reprezinte sub:

```text
10% din energia zilnică
```

și trans fat sub:

```text
1%
```

Acestea trebuie tratate mai mult ca:

```text
upper-limit optimization
```

decât ca target de atins.

---

# 43. Unsaturated Fat

Recommendation Engine trebuie să poată favoriza sursele de grăsimi nesaturate în locul celor saturate atunci când două opțiuni sunt altfel similare.

---

# 44. Omega-3

EFSA a utilizat o valoare de aproximativ:

```text
250 mg/day
```

pentru EPA + DHA la adulți.

Poate fi urmărit ca nutrient avansat acolo unde Food Data are acoperire suficientă.

---

# 45. Carbohydrate Target

Pentru alimentația generală, EFSA folosește un interval de:

```text
45–60% din energie
```

pentru carbohidrați.

WHO descrie un interval mai larg în cadrul dietelor sănătoase și pune accentul pe surse precum cereale integrale, legume, fructe și leguminoase.

---

# 46. Carbohydrates As Flexible Macro

Pentru multe profiluri:

```text
carbohydrates =
remaining energy
after protein + fat
```

dar numai după validarea unui interval compatibil cu profilul selectat.

---

# 47. Balanced Macro Solver

Conceptual:

```text
1. establish protein range
2. establish fat range
3. allocate remaining energy to carbohydrates
4. validate all macro ranges
5. iterate if necessary
```

---

# 48. Carbohydrates Should Not Be Automatically Penalized

Nu construim logică:

```text
fewer carbs = healthier
```

sau:

```text
more carbs = healthier
```

Contextul determină targetul.

---

# 49. Fiber

EFSA consideră:

```text
25 g/day
```

un aport adecvat pentru funcția intestinală normală la adulți, cu beneficii asociate și unor aporturi mai mari.

WHO recomandă de asemenea cel puțin 25 g pentru persoanele de peste 10 ani.

---

# 50. Fiber Is Minimum-Oriented

Model:

```text
minimum_reference: 25g
preferred_target: profile dependent
```

Recommendation Engine poate favoriza suplimentar fibrele atunci când aportul este redus.

---

# 51. Free Sugars

WHO recomandă:

```text
free sugars < 10% of energy
```

iar sub:

```text
5%
```

poate oferi beneficii suplimentare.

---

# 52. Total Sugar ≠ Free Sugar

Trebuie diferențiate:

```text
total_sugars
added_sugars
free_sugars
```

Un măr nu trebuie tratat la fel ca o băutură îndulcită.

---

# 53. Missing Sugar Classification

Multe baze de date oferă doar:

```text
total sugar
```

Dacă `free_sugar` este necunoscut:

nu îl inferăm automat ca fiind egal cu total sugar.

---

# 54. Sodium

WHO recomandă pentru adulți sub:

```text
2 g sodium/day
```

echivalent cu aproximativ:

```text
5 g salt/day
```

---

# 55. Sodium Is an Upper-Bound Nutrient

Nu încercăm să „atingem 100%”.

Model:

```text
preferred zone
approaching limit
above guideline
```

---

# 56. Potassium

EFSA stabilește pentru adulți un Adequate Intake de:

```text
3,500 mg/day
```

Acesta poate fi folosit ca reference coverage, nu diagnostic.

---

# 57. Fruit & Vegetable Signal

WHO recomandă pentru adulți și persoanele peste 10 ani cel puțin:

```text
400g fruits + vegetables/day
```

Aceasta trebuie să existe separat de micronutrienți.

---

# 58. Why Food-Group Signals Matter

Două meniuri pot avea macro-uri identice:

```text
Protein   ✓
Carbs     ✓
Fat       ✓
Calories  ✓
```

dar calitate alimentară foarte diferită.

De aceea Nutrition Engine trebuie să suporte:

```text
FoodGroupSignals
```

---

# 59. Food Group Signals

Exemple:

```text
fruit
vegetables
legumes
whole_grains
nuts_seeds
fish
dairy_or_alternatives
protein_sources
```

Nu trebuie toate transformate în targeturi rigide.

---

# 60. Hydration

EFSA consideră aporturi totale de apă de aproximativ:

```text
2.0 L/day women
2.5 L/day men
```

adecvate în condiții moderate, iar aceste valori includ apa provenită atât din alimente, cât și din băuturi.

---

# 61. Do Not Convert Total Water Directly Into Bottle Target

Nu spunem automat:

> „Trebuie să bei 2.5L de apă.”

pentru că referința include apa din alimente.

Hydration poate avea:

```text
total_water_reference
beverage_tracking
estimated_food_water
```

---

# 62. Hydration Context

Necesarul poate varia cu:

* temperatură;
* transpirație;
* activitate;
* mediu.

De aceea hidratarea trebuie tratată ca target adaptabil, nu constant absolut.

---

# 63. Micronutrient Architecture

Micronutrienții trebuie modelați individual.

Exemple:

### Vitamins

```text
A
B1
B2
B3
B5
B6
B7
B9
B12
C
D
E
K
```

### Minerals

```text
calcium
iron
magnesium
phosphorus
potassium
sodium
zinc
copper
manganese
selenium
iodine
etc.
```

---

# 64. Micronutrient Reference Types

Fiecare nutrient poate avea:

```text
AR   = Average Requirement
PRI  = Population Reference Intake
AI   = Adequate Intake
RI   = Reference Intake Range
UL   = Tolerable Upper Intake Level
```

Nu toate au toate tipurile.

EFSA folosește exact această familie de valori de referință.

---

# 65. Micronutrient Rule Object

Conceptual:

```text
NutrientReference {
    nutrient
    population
    reference_type
    value
    unit
    source
    version
    applicability
}
```

---

# 66. Never Hardcode All DRVs in Business Logic

Greșit:

```text
if vitaminD < 15:
```

Corect:

```text
referenceRules
  .get("vitamin_d", populationProfile)
```

---

# 67. Reference Rules Must Be Updateable

EFSA își actualizează periodic evaluările, inclusiv limitele superioare pentru nutrienți. Tabelele EFSA pentru UL au fost actualizate în 2024 pentru mai multe vitamine și minerale.

Prin urmare:

```text
nutrition_rule_version
```

este obligatoriu.

---

# 68. Upper Limits Are Nutrient-Specific

Nu putem implementa generic:

```text
if intake > 200% target:
    dangerous
```

Este greșit.

Fiecare nutrient are propriile reguli.

---

# 69. UL Applicability

Unele limite superioare se aplică:

* tuturor surselor;
* doar suplimentelor;
* anumitor forme chimice.

Acest lucru trebuie reprezentat explicit.

---

# 70. UL Object

```text
UpperLimit {
    value
    unit
    source_scope:
        FOOD
        SUPPLEMENT
        TOTAL
        SPECIFIC_FORM
}
```

---

# 71. No UL Does Not Mean Unlimited

Dacă nu există un UL definit:

NU înseamnă:

> orice cantitate este sigură.

Înseamnă:

> nu avem un upper limit stabilit în acel rule set.

---

# 72. Supplements Are Separate

Trebuie diferențiat:

```text
food intake
supplement intake
```

---

# 73. Supplement Model

Exemple:

```text
Whey
Creatine
Vitamin D
Magnesium
Multivitamin
```

Dar nu toate sunt nutrienți în sensul Nutrient Gap Engine.

---

# 74. No Automatic Supplement Prescription

Recommendation Engine trebuie să optimizeze în primul rând:

> alimentele.

Nu trebuie să spună automat:

> „Ia vitamina D.”

sau:

> „Ia magneziu.”

pe baza unui gap alimentar estimat.

---

# 75. Supplements Can Contribute To Nutrient Totals

Dacă user loghează:

```text
Vitamin D supplement
```

cantitatea poate intra în nutrient state.

Dar trebuie păstrată separat pentru verificarea UL.

---

# 76. Creatine

Creatina poate exista în:

```text
Supplement Tracker
```

dar nu trebuie introdusă în:

```text
Micronutrient Gap Solver
```

ca și cum ar fi vitamina/mineralul care trebuie completat zilnic prin mâncare.

---

# 77. Food Composition Data

Nutrition Calculation Engine are nevoie de informații despre:

```text
energy
macros
micros
serving
preparation state
```

EFSA subliniază că bazele de food composition sunt fundamentale pentru evaluarea nutrițională și că valorile se pot modifica în timp pe măsură ce produsele sunt reformulate.

---

# 78. Nutrition Data Provenance

Fiecare nutrient trebuie să poată avea:

```text
value
unit
source
method
confidence
updated_at
```

---

# 79. NutrientValue

Conceptual:

```text
NutrientValue {
    nutrient: protein
    value: 8.7
    unit: g/100g
    source: MANUFACTURER_LABEL
    confidence: VERIFIED
}
```

---

# 80. Source Priority — Commercial Products

Pentru un produs ambalat:

```text
1. current manufacturer data
2. verified product database
3. barcode database
4. manually entered label
5. matched generic food
6. AI inference
```

---

# 81. Source Priority — Generic Foods

Pentru:

```text
chicken breast
apple
rice
broccoli
```

folosim o food-composition database recunoscută.

---

# 82. Hybrid Nutrition Records

Un produs comercial poate avea:

```text
calories ✓
protein ✓
carbs ✓
fat ✓

vitamin C ?
magnesium ?
potassium ?
```

Nu completăm automat lipsurile cu `0`.

---

# 83. Missing ≠ Zero

Aceasta trebuie să fie regulă fundamentală.

```text
null != 0
```

`0` înseamnă:

> știm că valoarea este zero.

`null` înseamnă:

> nu știm valoarea.

---

# 84. Micronutrient Estimation From Generic Match

Versiune avansată:

```text
Commercial product
    ↓
Generic food match
    ↓
Estimated micronutrients
```

Dar acestea trebuie marcate:

```text
INFERRED
```

și cu confidence mai mic.

---

# 85. Example

Pilos Greek Yogurt:

```text
Calories:
DECLARED

Protein:
DECLARED

Calcium:
INFERRED_FROM_GENERIC_GREEK_YOGURT
```

UI-ul nu trebuie neapărat să arate permanent diferența, dar engine-ul trebuie să o știe.

---

# 86. Nutrition Completeness Score

Fiecare food/meal/day poate avea:

```text
nutrition_completeness
```

Exemplu:

```text
macros: 100%
core micros: 72%
extended micros: 38%
```

---

# 87. Do Not Optimize Unknown Data As Zero

Dacă `magnesium` lipsește din trei produse consumate:

nu putem spune:

```text
magnesium coverage = 15%
```

cu confidence ridicat.

Trebuie:

```text
observed magnesium coverage: 15%
data confidence: LOW
```

---

# 88. Gap Confidence

Nutrient Gap Engine trebuie să producă:

```text
gap
confidence
```

---

# 89. Example

```text
Vitamin C:

estimated coverage:
54%

data completeness:
95%

gap confidence:
HIGH
```

vs:

```text
Magnesium:

observed coverage:
38%

data completeness:
42%

gap confidence:
LOW
```

---

# 90. Low-Confidence Gap Behavior

Dacă confidence este mic:

Recommendation Engine nu trebuie să facă o optimizare agresivă în jurul nutrientului respectiv.

Poate utiliza în schimb:

> **food diversity signals**

---

# 91. Raw vs Cooked

Toate cantitățile relevante trebuie să aibă:

```text
preparation_state
```

Exemple:

```text
RAW
COOKED
BOILED
BAKED
FRIED
DRAINED
DRY
READY_TO_EAT
```

---

# 92. User Must Know What 200g Means

Un plan nu trebuie să spună:

```text
200g rice
```

fără să poată determina dacă înseamnă:

```text
200g dry
```

sau:

```text
200g cooked
```

---

# 93. Canonical Recipe Quantities

Ideal, ingredientele rețetei sunt definite într-o stare explicită.

Exemplu:

```text
Rice
90g
DRY
```

și Cook View poate afișa și:

```text
≈260g cooked
```

---

# 94. Cooking Changes Weight

Apa poate fi:

* absorbită;
* evaporată;
* eliminată.

Din acest motiv:

```text
100g raw
```

nu poate fi convertit universal în:

```text
Xg cooked
```

fără yield information.

---

# 95. Recipe Batch Method

Cea mai bună metodă pentru meal prep precis:

```text
1. calculate nutrition of all ingredients
2. cook entire batch
3. weigh final cooked batch
4. divide nutrition by final cooked mass
```

---

# 96. Example

Ingrediente totale:

```text
Total nutrition:
2400 kcal
```

Preparatul după gătire:

```text
final batch weight:
1800g
```

Rezultă:

```text
1.333 kcal/g
```

Porție:

```text
450g
≈600 kcal
```

---

# 97. Batch Nutrition

Conceptual:

```text
nutrition_per_cooked_gram =
batch_total_nutrition
/
final_cooked_weight
```

---

# 98. Why This Matters For Household Portioning

Putem spune:

```text
Alberto:
520g

Partner:
370g
```

din aceeași tavă.

Este mult mai simplu decât cântărirea separată a fiecărui ingredient după gătire.

---

# 99. Ingredient-Based Portioning Mode

Alternativ:

pentru preparate unde componentele rămân separate:

```text
chicken
rice
vegetables
```

Cook View poate spune exact:

```text
Alberto:
180g chicken
210g rice
200g vegetables

Partner:
125g chicken
145g rice
200g vegetables
```

---

# 100. Two Portion Modes

### COMPONENT_PORTIONING

Pentru bowl-uri, garnituri etc.

### BATCH_WEIGHT_PORTIONING

Pentru:

* paste;
* tocănițe;
* curry;
* casseroles;
* chili;
* soups.

---

# 101. Edible Portion

Unele alimente includ:

* coajă;
* os;
* sâmbure;
* lichid de conservare.

Quantity model trebuie să poată diferenția:

```text
purchased_weight
edible_weight
```

---

# 102. Food Yield

Grocery Engine poate cumpăra:

```text
1kg whole food
```

dar Nutrition Engine poate calcula doar:

```text
800g edible
```

unde yield data este disponibil.

---

# 103. Cooking Fat

Uleiul nu trebuie ignorat.

Recipe trebuie să includă:

```text
oil_added
```

și eventual:

```text
oil_consumed_estimate
```

dacă metoda justifică diferența.

---

# 104. Sauces And Condiments

Acestea trebuie incluse dacă au impact caloric/nutrițional semnificativ.

Una dintre erorile frecvente ale tracking-ului este ignorarea:

* uleiului;
* dressingului;
* sosurilor;
* toppingurilor.

---

# 105. Label Energy Is Canonical For Packaged Food

Dacă produsul declară:

```text
73 kcal / 100g
```

aceasta este valoarea principală.

Nu recalculăm obligatoriu:

```text
protein×4 + carbs×4 + fat×9
```

deoarece:

* rounding;
* fibre;
* polyols;
* organic acids;
* metodologii de etichetare;

pot produce diferențe.

---

# 106. Macro Energy Is Validation, Not Primary Truth

Putem calcula intern:

```text
estimated_energy_from_macros
```

pentru sanity check.

Dar:

```text
declared_energy
```

rămâne prioritar pentru produsul etichetat.

---

# 107. Internal Precision

Intern păstrăm suficientă precizie.

Exemplu:

```text
protein = 47.382g
```

---

# 108. Display Precision

UI:

```text
47g protein
```

sau:

```text
47.4g
```

în Precision Mode.

Nu:

```text
47.382g
```

---

# 109. Portion Solver Tolerances

Un target de:

```text
600 kcal
```

nu necesită exact:

```text
600.00 kcal
```

Trebuie să existe:

```text
acceptable range
preferred range
```

---

# 110. Example Energy Tolerance

Conceptual, nu guideline medical:

```text
meal target:
600 kcal

preferred:
570–630

acceptable:
540–660
```

Valorile pot fi calibrate ulterior.

---

# 111. Macro Tolerances

Similar:

```text
protein target:
45g

preferred:
42–50g
```

Scorul se degradează gradual.

Nu folosim:

```text
44.9 = fail
45.0 = success
```

---

# 112. Target Functions

Există patru tipuri fundamentale.

### MINIMUM

Exemplu:

```text
fiber
```

### TARGET

Exemplu:

```text
protein preferred
```

### RANGE

Exemplu:

```text
fat energy %
```

### MAXIMUM / LIMIT

Exemplu:

```text
saturated fat
```

---

# 113. NutrientRule

```text
NutrientRule {
    nutrient
    type:
        MINIMUM
        TARGET
        RANGE
        MAXIMUM

    preferred
    lower
    upper
}
```

---

# 114. Scoring Minimum Nutrient

Conceptual:

```text
if intake >= minimum:
    score ≈ 1

else:
    score proportional to coverage
```

Nu primește bonus infinit dacă:

```text
fiber = 80g
```

---

# 115. Scoring Target Nutrient

Conceptual:

```text
best around preferred range
```

cu toleranță.

---

# 116. Scoring Maximum Nutrient

Conceptual:

```text
good well below limit
warning approaching limit
penalty above limit
```

---

# 117. Nutrient Gap Engine — Inputs

Pentru fiecare membru:

```text
reference targets
consumed foods
planned meals
locked meals
supplements
data completeness
time remaining
meal slots remaining
```

---

# 118. Two Different Gaps

Trebuie separate:

### CURRENT GAP

```text
target - consumed
```

### UNALLOCATED GAP

```text
target
- consumed
- locked/planned nutrition
```

---

# 119. Example

```text
Protein target:
170g

Consumed:
70g

Dinner locked:
50g
```

Current gap:

```text
100g
```

Unallocated gap:

```text
50g
```

Lunch recommendation trebuie să folosească mai ales:

> **unallocated gap**

---

# 120. Gap Profile

```text
NutrientGap {
    nutrient
    current_intake
    planned_intake
    reference
    gap
    status
    urgency
    confidence
}
```

---

# 121. Gap Status

```text
UNKNOWN
LOW_COVERAGE
BELOW_PREFERRED
ON_TRACK
SATISFIED
APPROACHING_UPPER
ABOVE_UPPER_REFERENCE
```

---

# 122. Urgency Is Contextual

Un gap nu este la fel de important la:

```text
08:00
```

ca la:

```text
20:30
```

dacă mai există o singură masă.

---

# 123. Remaining Meal Factor

Conceptual:

```text
urgency =
gap_size
× nutrient_priority
× remaining_meal_factor
```

---

# 124. No Micronutrient Whack-A-Mole

Nu vrem comportament:

```text
Breakfast low magnesium
→ lunch overloaded with magnesium

Lunch low calcium
→ dinner overloaded with calcium
```

Planner-ul trebuie să optimizeze holistic.

---

# 125. Diminishing Nutrient Benefit

Dacă targetul este aproape acoperit:

beneficiul suplimentar trebuie să scadă.

Conceptual:

```text
benefit =
coverage_gain
until target
```

Aportul dincolo de zona utilă nu continuă să crească scorul.

---

# 126. Nutrient Gap Benefit

Pentru un candidate meal:

```text
benefit_i =
gap_before_i
-
gap_after_i
```

normalizat.

---

# 127. Nutrition Fit

Conceptual:

```text
NutritionFit =
MacroFit
+
GapReduction
+
FoodQuality
-
UpperLimitRisk
```

---

# 128. Macro Fit vs Micro Fit

Recommendation Engine trebuie să aibă:

```text
MacroFit
```

separat de:

```text
MicroFit
```

Astfel putem controla ponderile.

---

# 129. Micro Fit Must Respect Data Confidence

Conceptual:

```text
effective_micro_score =
micro_score
× data_confidence
```

Dar pentru coverage reporting este preferabil să afișăm explicit:

```text
coverage + confidence
```

decât să inventăm un număr ajustat greu de explicat.

---

# 130. Daily vs Rolling Nutrition

Nu toți nutrienții trebuie optimizați perfect în fiecare zi.

Trebuie să avem:

```text
MEAL
DAILY
ROLLING_7_DAY
LONG_TERM
```

---

# 131. Meal-Level Optimization

Prioritate:

* energy;
* protein;
* meal practicality;
* current major gaps.

---

# 132. Daily Optimization

Prioritate:

* calories;
* macros;
* fibre;
* food groups;
* main micronutrient coverage;
* limit nutrients.

---

# 133. Rolling 7-Day Optimization

Foarte util pentru:

* micronutrient adequacy;
* food variety;
* plant diversity;
* protein-source diversity;
* fish frequency;
* recurring imbalances.

---

# 134. Why Rolling Matters

Dacă marți aportul unui nutrient este mai mic și miercuri este mai mare:

nu înseamnă automat că dieta este problematică.

Aplicația nu trebuie să transforme nutriția într-o obligație de:

> „100% la fiecare bară, zilnic.”

---

# 135. Daily View vs Nutrition Analysis View

Today poate afișa:

```text
Protein
Fiber
Calories
```

plus câteva insights.

Advanced Nutrition poate afișa:

```text
7-day micronutrient coverage
```

---

# 136. Micronutrient UI Philosophy

Nu afișăm implicit 30 de bare roșii.

Mai bine:

```text
Nutrition coverage:
Good

Could improve:
Fiber
Potassium
Vitamin C
```

---

# 137. Recommendation Behavior

Intern sistemul poate analiza tot.

Extern poate spune simplu:

> **Îți recomand Chicken Fajita Bowl.**

---

# 138. Why This Meal

La cerere:

> High protein
> Adds fibre and vitamin C
> Uses peppers from your fridge
> Fits the calories remaining

---

# 139. Weight Tracking Architecture

Weight este:

```text
observed outcome
```

nu nutrient input direct în fiecare zi.

---

# 140. Weight Measurement

Poate veni din:

```text
manual
scale photo
connected scale
```

---

# 141. Weight Log

```text
WeightLog {
    value
    timestamp
    source
    confidence
}
```

---

# 142. Scale Photo

AI poate detecta:

```text
94.7kg
```

dar user confirmă înainte de log permanent.

---

# 143. Do Not React To One Weigh-In

Greutatea zilnică poate varia din motive care nu reprezintă schimbări reale de țesut corporal.

Target Engine nu trebuie să reacționeze la:

```text
yesterday 94.8
today 95.6
```

---

# 144. Weight Trend

Trebuie calculat un trend robust.

Opțiuni:

```text
7-day rolling average
exponential moving average
robust regression
weekly median
```

---

# 145. Recommended Architecture

Pentru UI:

```text
7-day smoothed weight
```

Pentru adaptation engine:

```text
14–28 day trend model
```

---

# 146. Minimum Data Before Adaptation

Nu propunem schimbări de target după:

```text
2 weigh-ins
```

Trebuie să existe:

* suficiente observații;
* suficiente zile;
* suficient adherence data.

---

# 147. Suggested Product Rule

Conceptual:

```text
minimum observation period:
14 days

preferred:
21–28 days
```

înainte de ajustări semnificative.

---

# 148. Weight Trend Confidence

```text
HIGH
MEDIUM
LOW
```

în funcție de:

* număr măsurători;
* consistență;
* outliers;
* adherence.

---

# 149. Measurement Consistency

Aplicația poate recomanda, fără să impună:

> cântărire în condiții aproximativ similare

pentru un trend mai stabil.

---

# 150. Adaptation Engine Input

```text
target goal
estimated maintenance
current energy target
weight trend
nutrition adherence
meal logging completeness
activity changes
```

---

# 151. Never Infer Maintenance From Poor Tracking

Dacă user loghează doar:

```text
60% of meals
```

nu putem concluziona:

> „Maintenance-ul tău este greșit.”

Adaptation Engine trebuie să verifice:

```text
tracking confidence
```

---

# 152. Adherence Confidence

Exemplu:

```text
Meal logging:
94%

Weight data:
HIGH

Activity pattern:
stable
```

→ adaptarea poate avea confidence mare.

---

# 153. Weight Trend Feedback

Dacă obiectivul este fat loss și trendul:

```text
decreases approximately as expected
```

→ keep target.

Dacă:

```text
flat for sufficient period
```

→ investigate.

Nu modificăm imediat.

---

# 154. Reasons For Trend Mismatch

Sistemul trebuie să considere:

```text
tracking incomplete
activity changed
weight measurement noisy
planned != actual
maintenance estimate inaccurate
```

înainte să schimbe caloriile.

---

# 155. Adaptive Target Changes

În Auto Mode:

> propunere, nu modificare silențioasă.

Exemplu:

```text
Your weight trend has been stable for 3 weeks.

Would you like to adjust your target slightly?
```

---

# 156. Bounded Adjustments

Adjustments trebuie să fie:

```text
small
infrequent
reversible
```

Nu:

```text
-400 kcal overnight
```

pe baza unui algoritm opac.

---

# 157. Adaptation Version History

```text
2100 kcal
→ 2025 kcal

reason:
3-week trend review

accepted_by_user:
YES
```

---

# 158. Never Use 7,700 kcal/kg As Exact Feedback Rule

Conversia simplă:

```text
1kg = X kcal
```

nu trebuie folosită ca mecanism exact de recalculare a metabolismului.

Weight change este mai complex.

Feedback controller-ul trebuie să reacționeze gradual la trend.

---

# 159. Plan vs Actual Nutrition

Trebuie separate permanent:

```text
PLANNED
ACTUAL
```

---

# 160. Example

Plan:

```text
600 kcal
45g protein
```

Actual:

```text
720 kcal
39g protein
```

Next Meal Engine folosește:

> actual

nu planul original.

---

# 161. Estimated Meal Logging

Meal photo poate produce:

```text
~700 kcal
```

cu confidence.

Nutrition State trebuie să știe:

```text
ACTUAL_ESTIMATED
```

nu:

```text
ACTUAL_VERIFIED
```

---

# 162. Nutrition State Confidence

Daily state poate calcula:

```text
calorie confidence
macro confidence
micro confidence
```

---

# 163. Example

```text
Calories:
HIGH

Protein:
HIGH

Micronutrients:
MEDIUM
```

---

# 164. Meal Photo Precision Policy

Din fotografie:

AI poate detecta:

```text
Chicken
Rice
Broccoli
```

dar nu trebuie să pretindă:

```text
Chicken: 183g
```

cu exactitate artificială.

Mai bine:

```text
Chicken: ~180g
```

---

# 165. Planned Meal Photo Is Better

Dacă sistemul știe că masa planificată este:

```text
180g chicken
200g rice
```

și imaginea pare compatibilă:

user poate apăsa:

> **Log planned meal**

Aceasta trebuie preferată unei estimări vizuale complet noi.

---

# 166. Meal Logging Priority

Pentru precizie:

```text
1. Planned verified meal
2. Weighed/manual quantities
3. Barcode product
4. Saved recipe
5. Meal photo estimate
6. Natural-language estimate
```

---

# 167. Daily Nutrient State

Conceptual:

```text
DailyNutritionState {
    target
    consumed
    planned
    locked
    remaining
    observed_coverage
    confidence
}
```

---

# 168. Nutrition State Recalculation

Se recalculează la:

```text
meal.logged
meal.adjusted
meal.deleted
meal.skipped
supplement.logged
target.changed
```

---

# 169. Planned Nutrition Does Not Equal Consumed Nutrition

În analytics:

```text
planned coverage
```

și:

```text
actual coverage
```

trebuie separate.

---

# 170. Nutrition Adherence

Nu trebuie calculată doar:

```text
calories actual / calories target
```

Poate fi compusă din:

```text
energy consistency
protein consistency
food quality
logging completeness
```

dar fără un singur „health score” moralizator.

---

# 171. No Good Food / Bad Food Binary

Foods nu trebuie clasificate:

```text
GOOD
BAD
```

Mai util:

```text
nutrient dense
high sodium
high saturated fat
high protein
high fiber
```

în context.

---

# 172. Cravings Are Constraints

Dacă user spune:

> „Vreau paste.”

Nutrition Engine nu trebuie să lupte cu alegerea.

Recipe/Portion engines optimizează:

```text
pasta meal
```

în jurul nutrienților rămași.

---

# 173. Nutritional Compensation

Compensation trebuie să însemne:

> optimizează mesele viitoare

nu:

> pedepsește utilizatorul.

---

# 174. Example

Dinner locked:

```text
Pizza
900 kcal
high sodium
low fiber
```

Lunch poate favoriza:

```text
lean protein
vegetables
fiber
lower sodium
```

---

# 175. No Extreme Compensation

Nu:

```text
skip lunch because pizza tonight
```

automat.

Planner-ul trebuie să păstreze un pattern alimentar rezonabil.

---

# 176. Meal Energy Allocation

Meal targets trebuie să fie flexibile.

Nu hardcodăm:

```text
Breakfast = 25%
Lunch = 35%
Dinner = 30%
Snack = 10%
```

pentru toată lumea.

---

# 177. Meal Distribution Inputs

Poate învăța:

```text
meal history
hunger preference
work schedule
training schedule
locked meals
```

---

# 178. Personalized Meal Distribution

Exemplu:

User preferă breakfast mic:

```text
Breakfast 350
Lunch 700
Dinner 750
Snack 300
```

Alt user poate prefera complet diferit.

Ambele pot fi valide.

---

# 179. Training Day Context

Training poate influența distribuția:

* energy;
* carbohydrate;
* protein;

dar nu trebuie să schimbe automat totalul zilei fără un rule set explicit.

---

# 180. Workout Calories

Nu trebuie făcut automat:

```text
watch says 700 kcal burned
→ eat 700 kcal extra
```

în MVP.

Acest lucru poate produce erori mari și double-counting.

---

# 181. Rest Day Context

Calorie cycling poate fi:

```text
OPTIONAL
```

Nu default obligatoriu.

---

# 182. Food Diversity

Nutrition Engine trebuie să urmărească și:

```text
food diversity
```

pe intervale săptămânale.

---

# 183. Diversity Dimensions

Exemple:

```text
unique vegetables
unique fruits
protein sources
whole grains
legumes
nuts/seeds
fish
```

---

# 184. Diversity Is Not Infinite Variety

Nu vrem 60 de ingrediente diferite dacă asta:

* crește costul;
* crește waste-ul;
* complică meal prep.

De aceea:

```text
Nutrition Diversity
```

este echilibrată cu:

```text
Operational Simplicity
```

---

# 185. Nutrition vs Ingredient Reuse

Exemplu bun:

Broccoli apare în două mese.

Asta nu este o problemă.

Problema este dacă întregul plan devine:

```text
chicken + rice + broccoli
× 14
```

---

# 186. Nutrition Quality Over A Week

Weekly Planner poate verifica:

```text
macro consistency
fiber coverage
micronutrient coverage
food-group diversity
upper-limit nutrients
```

înainte să accepte planul.

---

# 187. Weekly Nutrition Validator

```text
validateWeek(plan)
```

produce:

```text
PASS
PASS_WITH_WARNINGS
NEEDS_REOPTIMIZATION
```

---

# 188. Example Warning

```text
Protein coverage:
good

Fiber:
low on 4/7 days

Vitamin C:
low rolling coverage

Sodium:
high on 3 days
```

Planner-ul încearcă o optimizare înainte de prezentarea planului.

---

# 189. Reoptimization Should Preserve Stability

Dacă trebuie crescută fibra:

nu regenerăm toată săptămâna.

Mai întâi încercăm:

```text
ingredient adjustment
side addition
fruit snack
recipe substitution
```

cu minimum changes.

---

# 190. Nutritional Repair Operations

Ordinea preferată:

```text
1. adjust portion/component
2. add compatible food
3. substitute ingredient
4. replace meal
5. replan multiple meals
```

---

# 191. Household Nutrition

Fiecare membru are propriul:

```text
NutritionTarget
NutritionState
NutrientGap
```

---

# 192. No Household-Average Nutrition

Greșit:

```text
Household protein target:
250g
```

Corect:

```text
Member A:
165g

Member B:
100g
```

---

# 193. Shared Meal Nutrition

Recipe este comună.

Porțiile sunt individuale.

```text
Recipe
    ↓
Portion A
Portion B
```

Fiecare este validată separat.

---

# 194. Household Shared Base

Uneori:

```text
same meal
```

poate folosi:

```text
same protein
same vegetables
different carb portion
different sauce amount
```

pentru a satisface nevoi diferite.

---

# 195. Household Fairness

Nutrition Solver nu trebuie să sacrifice sistematic targeturile unui membru pentru a simplifica gătitul.

Trebuie să existe:

```text
member_nutrition_score
```

pentru fiecare.

---

# 196. Household Meal Validation

Un shared meal este valid dacă:

```text
all hard constraints pass
+
individual portion quality acceptable
```

---

# 197. Food Preference Never Overrides Allergy

Prioritate:

```text
Safety
>
Restriction
>
Nutrition
>
Preference
```

---

# 198. Data Quality Score

Fiecare zi poate avea:

```text
NutritionDataQuality
```

bazat pe:

* log completeness;
* source quality;
* micronutrient completeness;
* estimated portions.

---

# 199. Example

```text
Daily data quality:
87%
```

Acest număr poate rămâne intern.

---

# 200. Insights Must Respect Data Quality

Nu generăm:

> „Ai consumat prea puțin magnesium săptămâna aceasta”

dacă:

```text
magnesium data completeness = 32%
```

---

# 201. Unknown State

Pentru orice nutrient:

```text
UNKNOWN
```

este o stare validă și importantă.

---

# 202. Nutrition Data Correction

User poate corecta:

```text
product nutrition
portion
recipe ingredient
meal quantity
```

iar toate state-urile dependente se recalculează.

---

# 203. Nutrition Cache Invalidation

Dacă se schimbă un produs dintr-o rețetă:

```text
Product
↓
Recipe Nutrition
↓
Meal Nutrition
↓
Daily State
↓
Weekly Analytics
```

trebuie invalidat controlat.

---

# 204. Nutrition Snapshot

Pentru istoricul unui meal log:

ar trebui să salvăm un:

```text
nutrition_snapshot
```

---

# 205. Why Snapshot Matters

Dacă peste 6 luni baza de date schimbă:

```text
product calories
```

nu vrem ca jurnalul de acum 6 luni să se modifice retroactiv.

---

# 206. Historical Nutrition Is Immutable

Food Log salvează:

```text
nutrition as known at consumption time
```

Recipe poate evolua ulterior.

---

# 207. Current Product Data vs Historical Log

Separat:

```text
current_food_definition
```

de:

```text
consumption_snapshot
```

---

# 208. Recipe Nutrition Recalculation

Recipe template poate fi recalculată când datele ingredientelor se schimbă.

Dar:

> historical consumed meal

rămâne snapshot.

---

# 209. Unit System

Canonical:

```text
mass → grams
liquid → milliliters
energy → kcal + kJ support
```

---

# 210. Household Units

UI poate permite:

```text
g
kg
ml
L
piece
cup
tbsp
tsp
```

dar Nutrition Engine normalizează.

---

# 211. Unit Conversion Requires Food Context

```text
1 cup rice
```

nu are aceeași masă ca:

```text
1 cup oil
```

Conversiile volum → masă trebuie să fie food-specific.

---

# 212. Piece Conversion

Exemplu:

```text
1 egg
```

poate avea:

```text
default edible mass
```

dar user poate schimba:

```text
small
medium
large
```

sau gramaj exact.

---

# 213. Precision Modes

### SIMPLE

```text
2 eggs
1 banana
1 yogurt
```

### PRECISE

```text
112g eggs
126g banana
200g yogurt
```

Același engine trebuie să suporte ambele.

---

# 214. Nutrition Engine Should Support Imperfect Users

Aplicația nu poate necesita cântărirea fiecărui aliment pentru a fi utilă.

Trebuie să funcționeze cu:

```text
exact
estimated
approximate
```

și să păstreze incertitudinea.

---

# 215. AI And Nutrition Boundary

AI poate spune:

> „Imaginea pare să conțină orez.”

AI nu trebuie să spună:

> „Acest orez are exact 274 kcal.”

Pipeline:

```text
AI detects food
↓
Food Resolver
↓
Nutrition Database
↓
Nutrition Calculation Engine
```

---

# 216. Natural-Language Meal Logging

User:

> „Am mâncat 2 ouă, două felii de pâine și niște cottage.”

AI poate extrage:

```text
eggs: 2
bread: 2 slices
cottage: unspecified
```

Pentru cottage:

```text
quantity = unknown
```

sau cere quick estimate.

Nu inventează automat `83g`.

---

# 217. Confidence Propagation

Dacă ingredient quantity are confidence:

```text
0.6
```

meal nutrition trebuie să reflecte incertitudinea.

---

# 218. Nutrition Range Estimate

Pentru meal photo putem avea intern:

```text
estimated calories:
550–720
```

cu central estimate:

```text
~630
```

---

# 219. Range-Aware State

Versiune avansată:

```text
consumed_energy_low
consumed_energy_estimate
consumed_energy_high
```

Acest lucru poate preveni false precision.

---

# 220. User-Facing Simplicity

UI poate afișa doar:

```text
~630 kcal
```

și:

> Estimated from photo

---

# 221. Nutrition Safety Validator

Înainte de a publica targeturi automate:

```text
NutritionSafetyValidator
```

verifică profilul.

---

# 222. Safety Validator Questions

Conceptual:

```text
Is automatic targeting allowed?
Is weight-loss planning allowed?
Are professional targets required?
Are any nutrition rules overridden?
```

---

# 223. Safety Rule Pack

Nu hardcodăm aceste reguli în Recommendation Engine.

```text
NutritionSafetyRuleSet
```

separat și versionat.

---

# 224. Special Cases Must Not Leak Into Generic Solver

Nu vrem:

```text
if pregnant
if child
if kidney_disease
...
```

împrăștiate în 15 servicii.

Safety/Target layer decide ce mod este permis.

---

# 225. Nutrition Profile Mode

```text
GENERAL_AUTO
CUSTOM_TARGETS
PROFESSIONAL_TARGETS
PROTECTED
```

---

# 226. Weight-Loss Automation

Doar:

```text
GENERAL_AUTO
```

poate primi automat deficit calculat de sistem.

---

# 227. Professional Mode

Planner-ul poate continua să ofere:

* recipes;
* shopping;
* portions;
* meal prep;

în jurul targeturilor profesionale.

Aceasta păstrează valoarea produsului fără să facă recomandări medicale.

---

# 228. Nutrition Quality vs Weight Goal

Chiar în fat-loss mode:

Recommendation Engine nu trebuie să optimizeze doar:

```text
lowest calories
```

Trebuie în continuare să țină cont de:

* protein;
* fiber;
* micronutrients;
* diversity;
* preference.

---

# 229. Calorie Efficiency Is Not Nutrition

O masă de:

```text
350 kcal
```

nu este automat mai bună decât una de:

```text
600 kcal
```

Contextul decide.

---

# 230. Protein Efficiency

Metrică internă opțională:

```text
protein / 100 kcal
```

poate ajuta solver-ul.

Dar nu trebuie să devină singurul criteriu.

---

# 231. Cost-Nutrition Metrics

Budget Engine poate calcula:

```text
cost per meal
cost per 100 kcal
cost per 25g protein
```

dar Nutrition Engine rămâne independent de preț.

Recommendation Engine combină cele două.

---

# 232. Nutrient Density

Putem calcula:

```text
nutrient contribution
relative to energy
```

pentru ranking.

Dar trebuie evitat un singur scor magic care pretinde că măsoară „sănătatea” alimentului.

---

# 233. Nutrition Reason Codes

Engine-ul poate produce:

```text
HIGH_PROTEIN_GAP_MATCH
HIGH_FIBER_CONTRIBUTION
VITAMIN_C_GAP_MATCH
LOW_SATURATED_FAT
SODIUM_LIMIT_CONCERN
FRUIT_VEG_CONTRIBUTION
```

---

# 234. Meal Validation

Înainte de recomandare:

```text
validateNutrition(meal, member)
```

returnează:

```text
VALID
VALID_WITH_TRADEOFFS
INVALID
```

---

# 235. Valid With Tradeoffs

Exemplu:

```text
Protein:
excellent

Calories:
slightly above preferred meal range

Sodium:
higher than ideal
```

Poate fi totuși cea mai bună opțiune.

---

# 236. No Single-Nutrient Tyranny

Un singur nutrient imperfect nu trebuie să distrugă o masă altfel bună.

Recommendation Engine trebuie să considere întreg contextul.

---

# 237. Meal Nutrition Utility

Conceptual:

```text
MealNutritionUtility =
energy_fit
+ macro_fit
+ gap_reduction
+ food_quality
- limit_risk
- distortion_penalty
```

---

# 238. Nutrition Is One Objective Among Several

Recommendation final:

```text
Nutrition
+
Preference
+
Budget
+
Time
+
Pantry
+
Meal Prep
+
Variety
```

Nutrition Spec definește doar componenta nutrițională.

---

# 239. Daily Nutrition Score

Dacă produsul folosește un score:

acesta trebuie să fie explicabil.

Nu:

```text
Health Score = 73
```

fără sens.

---

# 240. Prefer Component Scores

Exemplu:

```text
Energy         On track
Protein        Excellent
Fiber          Could improve
Micros         Good coverage
Variety        Good
```

---

# 241. Weekly Nutrition Summary

Exemplu:

```text
Protein target:
6/7 days within preferred range

Fiber:
average 28g/day

Fruit & vegetables:
average 430g/day

Micronutrient coverage:
generally good

Sodium:
higher than preferred on 2 days
```

---

# 242. No Shame Language

Nu:

```text
BAD DAY
CHEAT MEAL
FAILED
```

Mai bine:

```text
Above target
Different from plan
Rebalance remaining meals
```

---

# 243. Eating Out

Restaurant meals au:

```text
higher uncertainty
```

Nutrition Engine trebuie să accepte estimări.

---

# 244. Restaurant Reservation

Dacă user știe că va mânca în oraș:

poate rezerva:

```text
~800 kcal
```

fără să știe preparatul.

---

# 245. Placeholder Nutrition

Concept:

```text
NutritionReservation
```

Exemplu:

```text
Dinner out:
800 ± 200 kcal
```

---

# 246. Reconcile Later

După cină:

placeholder-ul este înlocuit cu:

```text
actual/estimated meal
```

și ziua se recalculează.

---

# 247. Flexible Tracking Is Better Than Missing Tracking

O estimare marcată corect:

```text
~700 kcal
```

este adesea mai utilă pentru continuity decât zero informație.

Dar confidence trebuie păstrat.

---

# 248. Nutrition Engine APIs — Conceptual

```text
calculateFoodNutrition()
calculateProductNutrition()
calculateRecipeNutrition()
calculateBatchNutrition()
calculatePortionNutrition()
calculateMealNutrition()
calculateDailyState()
calculateWeeklyNutrition()
calculateNutrientGaps()
validateNutrition()
```

---

# 249. Target APIs — Conceptual

```text
estimateREE()
estimateMaintenance()
generateEnergyTarget()
generateMacroTargets()
getReferenceNutrients()
validateTargetProfile()
```

---

# 250. Trend APIs — Conceptual

```text
calculateWeightTrend()
calculateTrackingConfidence()
evaluateGoalProgress()
proposeTargetAdjustment()
```

---

# 251. Nutrition Engine Must Be Pure Where Possible

Exemplu:

```text
calculateNutrition(food, quantity)
```

cu aceleași inputs trebuie să producă același output.

---

# 252. No LLM Inside Core Calculation

Nu:

```text
ask GPT how many calories
```

pentru calculele centrale.

---

# 253. Engine Versioning

Rezultatele trebuie să știe:

```text
nutrition_engine_version
target_engine_version
reference_rule_version
```

---

# 254. Test — Raw vs Cooked

Input:

```text
200g cooked rice
```

trebuie să folosească:

```text
COOKED rice record
```

nu:

```text
200g raw rice
```

---

# 255. Test — Missing Micronutrient

Product:

```text
protein known
calcium unknown
```

Expected:

```text
calcium = null
```

nu:

```text
0
```

---

# 256. Test — Locked Dinner

Target:

```text
2000 kcal
```

Consumed:

```text
500
```

Locked:

```text
900
```

Recommendation context trebuie să considere:

```text
600 kcal unallocated
```

---

# 257. Test — Household

Member A:

```text
protein remaining 60g
```

Member B:

```text
protein remaining 35g
```

Shared recipe trebuie să poată avea porții diferite.

---

# 258. Test — Upper Limit

Un candidate meal care împinge un nutrient limitat mult peste reference upper boundary trebuie să primească penalizarea corespunzătoare.

---

# 259. Test — Data Quality

Dacă 70% dintre micronutrient values sunt necunoscute:

system nu trebuie să afișeze:

> „Micronutrients 30% complete”

ca și cum ar fi adevărul nutrițional al zilei.

Trebuie să afișeze incertitudine.

---

# 260. Test — Weight Trend

Un singur weigh-in mare nu trebuie să schimbe calorie target.

---

# 261. Test — Recipe Batch

Total:

```text
2400 kcal
1800g cooked batch
```

Portion:

```text
450g
```

Expected:

```text
600 kcal
```

---

# 262. Test — Meal Photo

Photo estimate:

```text
~600 kcal
confidence medium
```

trebuie să rămână:

```text
ESTIMATED
```

în Nutrition State.

---

# 263. Data Integrity Rule

Orice valoare nutrițională trebuie să poată răspunde:

> **De unde vine?**

---

# 264. Target Integrity Rule

Orice target trebuie să poată răspunde:

> **De ce are utilizatorul această valoare?**

---

# 265. Recommendation Integrity Rule

Orice recomandare trebuie să poată răspunde:

> **Ce gap nutrițional ajută să completeze?**

---

# 266. Historical Integrity Rule

Orice Food Log trebuie să poată răspunde:

> **Ce valori nutriționale au fost folosite atunci când a fost logat?**

---

# 267. Nutrition Data Trust Model

Niveluri:

```text
VERIFIED
HIGH
MEDIUM
LOW
UNKNOWN
```

---

# 268. UI Trust Communication

Nu trebuie să afișăm confidence peste tot.

Dar acolo unde contează:

```text
Estimated from photo
```

sau:

```text
Some micronutrient data unavailable
```

---

# 269. Nutrition Insights Must Be Actionable

Nu:

> „Potassium 61%.”

Mai bine:

> **Potassium is lower than your current reference coverage. Your next meal can help improve it.**

și Recommendation Engine rezolvă problema.

---

# 270. The App Should Think, Not Just Report

Tracker clasic:

```text
Fiber:
14 / 25g
```

Produsul nostru:

```text
Fiber:
14 / 25g

Next meal recommendation
already accounts for the remaining gap.
```

---

# 271. Nutrient Gap Is Internal Intelligence

Utilizatorul nu trebuie să gestioneze manual:

```text
17 micronutrient gaps
```

Engine-ul trebuie să le folosească în fundal.

---

# 272. Core Nutrition Loop

```text
TARGETS
   ↓
FOOD CONSUMED
   ↓
NUTRITION CALCULATION
   ↓
DAILY STATE
   ↓
NUTRIENT GAPS
   ↓
NEXT MEAL OPTIMIZATION
   ↓
PORTION
   ↓
CONSUMPTION
   ↓
NEW STATE
```

---

# 273. Weekly Nutrition Loop

```text
DAILY STATES
      ↓
7-DAY AGGREGATION
      ↓
NUTRIENT COVERAGE
      ↓
DIVERSITY ANALYSIS
      ↓
PATTERN DETECTION
      ↓
NEXT WEEK OPTIMIZATION
```

---

# 274. Long-Term Feedback Loop

```text
CALORIE TARGET
      ↓
REAL FOOD INTAKE
      ↓
WEIGHT TREND
      ↓
ADHERENCE
      ↓
TARGET REVIEW
      ↓
PROPOSED ADJUSTMENT
```

---

# 275. Nutrition Reference Source Strategy

Pentru produsul orientat inițial către România/UE:

### Primary

EFSA Dietary Reference Values.

### General healthy-diet guardrails

WHO.

### Performance contexts

surse specializate precum ACSM/ISSN, atunci când profilul utilizatorului justifică acest lucru.

EFSA dispune de valori de referință pentru 34 de nutrienți și un instrument dedicat de interogare a acestora.

---

# 276. Reference Provider Abstraction

Trebuie să existe conceptual:

```text
NutritionReferenceProvider
```

Astfel ulterior putem suporta:

```text
EU / EFSA
US / DRI
country-specific
professional custom
```

fără să rescriem Nutrition Engine.

---

# 277. Romanian Localization

UI poate prezenta:

```text
kcal
g
mg
µg
```

și guideline-uri relevante regiunii.

Dar domain model-ul rămâne universal.

---

# 278. Core Nutrition Invariants

Aceste reguli trebuie considerate locked.

### Invariant 1

```text
Unknown nutrient data != zero
```

### Invariant 2

```text
Planned nutrition != consumed nutrition
```

### Invariant 3

```text
Estimated != verified
```

### Invariant 4

```text
Raw quantity != cooked quantity
```

### Invariant 5

```text
Population reference != individual diagnosis
```

### Invariant 6

```text
AI estimate != nutrition source of truth
```

### Invariant 7

```text
One-day micronutrient target != mandatory perfection
```

### Invariant 8

```text
One weigh-in != trend
```

---

# 279. Nutrition Engine North Star

Nutrition Engine este reușit dacă poate răspunde în orice moment:

> **Ce știm că această persoană a consumat, cât de sigur suntem, ce are planificat, ce targeturi de referință folosim și ce ar fi cel mai util să completeze în continuare?**

---

# 280. Nutrient Gap North Star

Nutrient Gap Engine este reușit dacă:

după fiecare masă poate transforma automat starea:

```text
„Ai mâncat X”
```

în:

```text
„Acum următoarea decizie alimentară ar trebui să optimizeze Y.”
```

fără ca utilizatorul să facă vreun calcul.

---

# 281. Product Nutrition North Star

Utilizatorul nu trebuie să deschidă aplicația și să gândească:

> „Mai am 63g proteină, 14g fibre, vitamina C e jos, am prea mult sodium și mai am 700 kcal. Ce naiba mănânc?”

Acesta este job-ul sistemului.

Utilizatorul trebuie să poată întreba:

> **„Ce mănânc?”**

iar produsul să răspundă cu o opțiune care a luat deja în calcul toate aceste lucruri.

---

# 282. Final Nutrition Principle

Scopul Nutrition Engine nu este să transforme utilizatorul într-un nutriționist.

Scopul este exact invers:

> **Sistemul gestionează complexitatea nutrițională pentru ca utilizatorul să poată lua o decizie simplă.**
