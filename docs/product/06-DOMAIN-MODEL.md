# 06 — Domain & Data Model

## 1. Scopul documentului

Acest document definește modelul conceptual de date al produsului.

El stabilește:

* entitățile principale;
* relațiile dintre ele;
* ownership-ul datelor;
* diferența dintre template-uri și instanțe;
* diferența dintre planned și actual;
* diferența dintre food generic și product comercial;
* modul în care reprezentăm nutrition data;
* targeturile;
* preferințele;
* household-ul;
* pantry-ul;
* shopping-ul;
* bugetul;
* meal prep-ul;
* AI-ul;
* learning-ul;
* istoricul;
* confidence-ul;
* versionarea;
* snapshot-urile.

Acest document NU este încă:

* schema SQL finală;
* Prisma schema;
* Supabase migration;
* API specification.

Acestea trebuie să derive din acest domain model.

---

# 2. Domain Modeling Principles

## Principle 1 — Model Real Concepts

Nu creăm tabele doar pentru că UI-ul are un card.

Modelăm lucrurile reale:

```text
Person
Food
Product
Recipe
Meal
Purchase
Pantry Item
Cooking Batch
```

---

## Principle 2 — Templates ≠ Instances

Trebuie diferențiate clar:

```text
Recipe
```

de:

```text
Meal
```

și:

```text
Meal Plan
```

de:

```text
Food Log
```

---

## Principle 3 — Planned ≠ Actual

Un plan spune:

> ce intenționam să se întâmple.

Un log spune:

> ce s-a întâmplat efectiv.

Acestea nu trebuie suprascrise unul peste celălalt.

---

## Principle 4 — Generic Food ≠ Commercial Product

```text
Greek Yogurt
```

nu este același lucru cu:

```text
Pilos Greek Yogurt 2% — 400 g
```

---

## Principle 5 — Current Definition ≠ Historical Snapshot

Dacă produsul se reformulează peste 6 luni:

food log-ul de astăzi nu trebuie să se schimbe retroactiv.

---

## Principle 6 — Household Is First-Class

Nu construim:

```text
User → everything
```

ci:

```text
Account
   ↓
Household
   ↓
Members
```

---

# 3. High-Level Domain Map

```text
ACCOUNT
   │
   ▼
HOUSEHOLD
   │
   ├──────── MEMBERS
   │            │
   │            ├── PROFILE
   │            ├── NUTRITION TARGETS
   │            ├── PREFERENCES
   │            ├── SCHEDULE
   │            └── PROGRESS
   │
   ├──────── KITCHEN
   │            │
   │            ├── PANTRY
   │            ├── EQUIPMENT
   │            ├── LEFTOVERS
   │            └── COOKING BATCHES
   │
   ├──────── PLANNING
   │            │
   │            ├── MEAL PLANS
   │            ├── MEALS
   │            ├── PORTIONS
   │            └── CONSTRAINTS
   │
   ├──────── SHOPPING
   │            │
   │            ├── SHOPPING LISTS
   │            ├── RECEIPTS
   │            ├── PRODUCTS
   │            └── PRICES
   │
   └──────── INTELLIGENCE
                │
                ├── LEARNING EVENTS
                ├── AFFINITIES
                ├── AI JOBS
                └── RECOMMENDATION RECORDS
```

---

# 4. Identity Layer

## 4.1 Account

Reprezintă persoana care se autentifică.

Conceptual:

```text
Account {
    id
    email
    authProvider
    locale
    timezone
    createdAt
}
```

Account-ul nu este neapărat identic cu membrul nutrițional.

---

# 5. Why Account ≠ Member

Exemplu:

Un părinte poate avea account și poate administra:

```text
Household
├── Parent
├── Partner
└── Child
```

Child poate avea:

```text
Member
```

dar nu:

```text
Account
```

---

# 6. Household

Reprezintă unitatea comună de organizare.

```text
Household {
    id
    name
    currency
    timezone
    locale
    createdAt
}
```

---

# 7. Household Membership

Leagă Account de Household.

```text
HouseholdMembership {
    id
    accountId
    householdId
    role
    status
}
```

---

# 8. Household Roles

Conceptual:

```text
OWNER
ADMIN
MEMBER
LIMITED
```

MVP poate avea doar:

```text
OWNER
MEMBER
```

dar modelul trebuie să permită extinderea.

---

# 9. Member

Member este persoana nutrițională.

```text
Member {
    id
    householdId
    linkedAccountId?
    displayName
    avatar?
    status
    createdAt
}
```

---

# 10. Member Can Exist Without Account

Important pentru:

* child;
* guest-like permanent member;
* persoană administrată de alt account.

---

# 11. Guest

Nu este nevoie să creăm obligatoriu un Member complet pentru:

> +1 invitat vineri.

Putem avea:

```text
MealGuest {
    id
    mealId
    count
    optionalLabel
}
```

---

# 12. Member Profile

Separăm identity de date fiziologice.

```text
MemberProfile {
    memberId

    biologicalSex?
    birthDate?
    heightCm?
    currentWeightKg?

    preferredUnits
}
```

---

# 13. Why Separate Profile

Pentru că:

```text
Member
```

este identity/domain ownership.

```text
MemberProfile
```

este fiziologie și poate avea reguli de acces diferite.

---

# 14. Goal

Obiectivul trebuie versionat.

```text
MemberGoal {
    id
    memberId

    type
    targetWeight?
    targetRate?
    validFrom
    validUntil?

    source
}
```

---

# 15. Goal Type

```text
FAT_LOSS
MAINTAIN
MUSCLE_GAIN
EAT_BETTER
PERFORMANCE
CUSTOM
```

---

# 16. Activity Profile

```text
ActivityProfile {
    id
    memberId

    activityMode
    palValue?
    typicalSteps?
    jobActivityLevel?
    validFrom
    validUntil?
}
```

---

# 17. Workout Schedule

Workout-ul nu trebuie să fie o simplă coloană în Member.

```text
WorkoutScheduleEntry {
    id
    memberId

    dayOfWeek
    startTime?
    type?
    durationMinutes?
    recurrence?
}
```

---

# 18. Schedule Model

Programul poate conține mai multe evenimente recurente.

```text
ScheduleRule {
    id
    memberId

    type
    dayOfWeek
    startTime?
    endTime?
    location?
    recurrence
}
```

---

# 19. Schedule Types

```text
OFFICE
REMOTE_WORK
WORKOUT
UNIVERSITY
TRAVEL
EXTERNAL_MEAL
CUSTOM
```

---

# 20. Meal Context Rule

Separat de programul general:

```text
MealContextRule {
    id
    memberId

    mealType
    dayOfWeek

    location
    lunchboxRequired
    fridgeAvailable
    microwaveAvailable
    kitchenAvailable
}
```

---

# 21. Nutrition Target Profile

Trebuie să poată avea istoric.

```text
NutritionTargetProfile {
    id
    memberId

    mode
    source

    validFrom
    validUntil?

    calculationMethod?
    engineVersion?
    referenceRuleVersion?
}
```

---

# 22. Target Mode

```text
SYSTEM_ESTIMATED
USER_CONFIGURED
PROFESSIONAL_CONFIGURED
PROTECTED
```

---

# 23. Nutrient Target

Nu punem 40 de coloane pe NutritionTargetProfile.

```text
NutrientTarget {
    id
    nutritionTargetProfileId

    nutrientId
    ruleType

    minimum?
    preferred?
    maximum?

    unit
}
```

---

# 24. Nutrient Rule Types

```text
MINIMUM
TARGET
RANGE
MAXIMUM
REFERENCE_ONLY
```

---

# 25. Nutrient

Nutrient trebuie să fie canonical.

```text
Nutrient {
    id
    code
    name
    category
    canonicalUnit
}
```

---

# 26. Nutrient Categories

```text
ENERGY
MACRO
MICRO
FATTY_ACID
SUGAR
OTHER
```

---

# 27. Example Nutrients

```text
energy_kcal
protein
carbohydrate
fat
fiber

calcium
iron
magnesium
potassium
sodium

vitamin_c
vitamin_d
vitamin_b12
```

---

# 28. Nutrition Reference Rule

Pentru EFSA/alte sisteme:

```text
NutritionReferenceRule {
    id
    nutrientId

    authority
    region
    populationCriteria

    referenceType
    value
    unit

    validFrom
    version
}
```

---

# 29. Food Domain

Trebuie să avem o taxonomie clară:

```text
Food
Product
Recipe
Meal
```

---

# 30. Food

Reprezintă ingredient generic.

```text
Food {
    id

    canonicalName
    category
    defaultPreparationState?

    edibleYield?
    density?
}
```

---

# 31. Examples

```text
Chicken Breast
Rice
Potato
Greek Yogurt
Egg
Banana
Broccoli
```

---

# 32. Food Alias

Pentru multilingual/search:

```text
FoodAlias {
    id
    foodId

    locale
    alias
}
```

Exemple:

```text
brânză de vaci
cottage cheese
cottage
```

---

# 33. Food Category

```text
FoodCategory {
    id
    parentId?
    name
}
```

Exemplu:

```text
Protein
 ├ Poultry
 ├ Beef
 ├ Fish
 └ Eggs

Vegetables
Carbohydrates
Dairy
Fruit
```

---

# 34. Food Nutrient Record

```text
FoodNutrientValue {
    id
    foodId
    nutrientId

    value
    basisAmount
    basisUnit

    preparationState

    source
    confidence
    updatedAt
}
```

---

# 35. Product

Commercial SKU / produs comercial.

```text
Product {
    id

    foodId?
    brandId?

    name
    variant?
    barcode?

    packageQuantity?
    packageUnit?

    country?
    status
}
```

---

# 36. Product Can Map To Food

Exemplu:

```text
Product:
Pilos Greek Yogurt 2%

Food:
Greek Yogurt
```

---

# 37. Product Without Generic Mapping

Unele produse complexe:

```text
Protein Bar X
Frozen Lasagna Y
```

pot exista fără Food generic simplu.

---

# 38. Brand

```text
Brand {
    id
    name
}
```

---

# 39. Product Nutrient Value

```text
ProductNutrientValue {
    id
    productId
    nutrientId

    value
    basisAmount
    basisUnit

    source
    confidence
    updatedAt
}
```

---

# 40. Product Data Source

```text
MANUFACTURER
BARCODE_DATABASE
USER_LABEL_SCAN
USER_MANUAL
INFERRED_GENERIC
```

---

# 41. Product Package

Pentru aceeași mâncare pot exista mai multe package sizes.

Putem modela separat:

```text
ProductPackage {
    id
    productId

    quantity
    unit
    packagingType?
}
```

---

# 42. Product Preference

Household sau member poate prefera produsul.

```text
PreferredProduct {
    id

    memberId?
    householdId?

    foodId
    productId

    priority
}
```

---

# 43. Store

```text
Store {
    id
    chainName
    locationName?
    location?
}
```

MVP poate folosi doar:

```text
Lidl
Kaufland
Carrefour
```

fără location exact.

---

# 44. Price Observation

Foarte important pentru price intelligence.

```text
PriceObservation {
    id

    productId
    storeId?

    price
    currency

    packageQuantity
    packageUnit

    observedAt
    source

    confidence
}
```

---

# 45. Price Sources

```text
RECEIPT
USER_MANUAL
STORE_DATA
ESTIMATE
```

---

# 46. Recipe

Recipe este template.

```text
Recipe {
    id

    title
    description?

    cuisine?
    mealTypes[]

    prepMinutes?
    cookMinutes?
    difficulty?

    servingModel

    source
    status

    createdBy?
    createdAt
}
```

---

# 47. Recipe Source

```text
SYSTEM
USER
AI_GENERATED
IMPORTED
```

---

# 48. Recipe Status

```text
DRAFT
VALIDATED
ACTIVE
ARCHIVED
```

AI-generated recipe nu devine automat ACTIVE.

---

# 49. Recipe Ingredient

```text
RecipeIngredient {
    id
    recipeId

    foodId?
    productId?

    role
    quantityMode

    minimumQuantity?
    preferredQuantity?
    maximumQuantity?

    unit
    preparationState

    optional
}
```

---

# 50. Ingredient Role

```text
PROTEIN
CARBOHYDRATE
VEGETABLE
FAT
SAUCE
SPICE
TOPPING
OTHER
```

Ajută Portion Solver-ul.

---

# 51. Quantity Mode

```text
FIXED
SCALABLE
OPTIONAL
SUBSTITUTABLE
```

---

# 52. Recipe Ingredient Group

Pentru:

```text
Rice OR Potato
```

avem:

```text
RecipeIngredientGroup {
    id
    recipeId
    type
}
```

și membership.

---

# 53. Ingredient Group Types

```text
CHOOSE_ONE
OPTIONAL_GROUP
SUBSTITUTION_GROUP
```

---

# 54. Recipe Step

```text
RecipeStep {
    id
    recipeId

    order
    instruction
    durationMinutes?
}
```

---

# 55. Recipe Metadata

Nu toate trebuie coloane în Recipe.

Putem avea structured attributes.

Exemple:

```text
mealPrepScore
lunchboxFriendly
freezerFriendly
microwaveFriendly
onePot
cleanupScore
```

---

# 56. Recipe Capability

```text
RecipeCapability {
    recipeId
    capability
    value
}
```

sau coloane dedicate pentru cele importante.

---

# 57. Recipe Equipment

```text
RecipeEquipmentRequirement {
    recipeId
    equipmentType
    required
}
```

---

# 58. Recipe Nutrition

Nu trebuie stocată ca adevăr independent de ingrediente fără versioning.

Poate exista cache:

```text
RecipeNutritionCache {
    recipeId
    recipeVersion
    nutritionVector
    calculatedAt
}
```

---

# 59. Recipe Version

Foarte util:

```text
RecipeVersion {
    id
    recipeId
    version
    createdAt
}
```

Pentru început poate fi simplificat, dar domain-ul trebuie să permită.

---

# 60. Meal Plan

Reprezintă un plan pentru o perioadă.

```text
MealPlan {
    id
    householdId

    startDate
    endDate

    strategy
    status

    generatedBy
    createdAt
}
```

---

# 61. Meal Plan Status

```text
DRAFT
ACTIVE
COMPLETED
SUPERSEDED
ARCHIVED
```

---

# 62. Planning Strategy

```text
BALANCED
SAVE_MONEY
MINIMAL_COOKING
NUTRITION_MAX
HIGH_VARIETY
CUSTOM
```

---

# 63. Plan Configuration

```text
MealPlanConfiguration {
    mealPlanId

    weeklyBudget?
    cookingSessionsTarget?
    varietyLevel?
    usePantryFirst
    minimizeWaste
}
```

---

# 64. Meal Slot

Meal slot este poziția temporală.

```text
MealSlot {
    id
    mealPlanId

    date
    mealType
    scheduledTime?

    location?
    status
}
```

---

# 65. Meal Type

```text
BREAKFAST
LUNCH
DINNER
SNACK
OTHER
```

---

# 66. Why MealSlot ≠ Meal

Slot:

```text
Monday Lunch
```

poate exista înainte să știm ce mâncare va fi.

---

# 67. Planned Meal

```text
Meal {
    id
    mealSlotId?

    householdId

    recipeId?
    title?

    source
    status

    lockedType?
}
```

---

# 68. Meal Source

```text
PLANNER
USER
AI_ASSISTED
EXTERNAL
LEFTOVER
```

---

# 69. Meal Status

```text
SUGGESTED
PLANNED
LOCKED
PREPARED
CONSUMED
SKIPPED
REPLACED
CANCELLED
```

---

# 70. Meal Participant

```text
MealParticipant {
    id
    mealId
    memberId

    participationStatus
}
```

---

# 71. Participation Status

```text
PLANNED
CONFIRMED
NOT_PARTICIPATING
```

---

# 72. Portion

Foarte important.

```text
MealPortion {
    id
    mealId
    memberId

    portionMode

    plannedNutritionSnapshotId?
}
```

---

# 73. Portion Component

Pentru component-based meals:

```text
MealPortionComponent {
    id
    mealPortionId

    recipeIngredientId?
    foodId?
    productId?

    quantity
    unit
    preparationState
}
```

---

# 74. Batch Portion

Pentru preparate mixte:

```text
MealPortion {
    portionMode = BATCH_WEIGHT
    batchWeightGrams = 450
}
```

---

# 75. Portion Modes

```text
COMPONENT
BATCH_WEIGHT
SERVING_COUNT
ESTIMATED
```

---

# 76. Planned Nutrition Snapshot

La momentul planificării:

```text
NutritionSnapshot {
    id

    contextType
    contextId

    values
    sourceVersion
    createdAt
}
```

---

# 77. Snapshot Structure

Pentru scalabilitate:

```text
NutritionSnapshotValue {
    snapshotId
    nutrientId

    value
    unit
    confidence
}
```

---

# 78. Food Log

Actual consumption.

```text
FoodLog {
    id
    memberId

    mealId?
    timestamp

    source
    confidence

    nutritionSnapshotId
}
```

---

# 79. Food Log Source

```text
PLANNED_MEAL_CONFIRMATION
MANUAL
BARCODE
PHOTO
NATURAL_LANGUAGE
RECIPE
IMPORT
```

---

# 80. Food Log Item

Dacă log-ul conține componente:

```text
FoodLogItem {
    id
    foodLogId

    foodId?
    productId?
    recipeId?

    quantity
    unit
    preparationState

    confidence
}
```

---

# 81. Planned vs Actual Link

```text
FoodLog.mealId
```

poate lega actualul de planificat.

Astfel calculăm:

```text
planned vs actual
```

---

# 82. Actual Portion Adjustment

Plan:

```text
450g
```

Actual:

```text
380g
```

FoodLog reflectă:

```text
380g
```

Meal rămâne cu:

```text
450g planned
```

---

# 83. Constraint

Constraints trebuie să fie first-class.

```text
Constraint {
    id

    scopeType
    scopeId

    type
    strength

    key
    value

    source

    validFrom?
    validUntil?
}
```

---

# 84. Constraint Strength

```text
HARD
STRONG
SOFT
```

---

# 85. Constraint Source

```text
USER
SYSTEM
PROFILE
SCHEDULE
AI_INTERPRETED
PROFESSIONAL
```

---

# 86. Constraint Scope

```text
MEMBER
HOUSEHOLD
DAY
WEEK
MEAL
MEAL_SLOT
```

---

# 87. Examples

```text
Meal constraint:
ingredient = pasta
strength = HARD
```

```text
Day constraint:
prep_time <= 20 min
strength = STRONG
```

---

# 88. Locked Meal Model

Nu avem nevoie neapărat de entitate separată dacă Constraint poate reprezenta:

```text
LOCK_RECIPE
LOCK_DISH_TYPE
LOCK_INGREDIENT
CALORIE_RESERVATION
```

---

# 89. Pantry

Household are un singur logical pantry.

```text
Pantry {
    id
    householdId
}
```

---

# 90. Pantry Item

```text
PantryItem {
    id
    pantryId

    foodId?
    productId?

    quantity?
    unit?

    quantityMode
    storageLocation

    openedAt?
    expiresAt?

    source
    confidence

    status
}
```

---

# 91. Pantry Quantity Mode

```text
EXACT
ESTIMATED
STATUS_ONLY
```

---

# 92. Pantry Status

```text
IN_STOCK
LOW
OUT
DISCARDED
EXPIRED
```

---

# 93. Storage Location

```text
FRIDGE
FREEZER
CUPBOARD
OTHER
```

---

# 94. Pantry Mutation

În loc să modificăm doar current quantity, este foarte util să avem history.

```text
PantryTransaction {
    id
    pantryItemId

    type
    quantityDelta?
    unit?

    source
    relatedEntityId?

    createdAt
}
```

---

# 95. Pantry Transaction Types

```text
PURCHASE
CONSUMED
COOKED
MANUAL_ADD
MANUAL_REMOVE
DISCARDED
CORRECTION
TRANSFER
```

---

# 96. Why Pantry Transactions Matter

Putem explica:

> de ce avem 300g pui acum?

și putem calcula:

* waste;
* consumption;
* corrections.

---

# 97. Expiry Information

Poate fi exactă:

```text
2026-08-23
```

sau estimată.

```text
ExpiryEstimate {
    pantryItemId

    expiresAt
    confidence
    source
}
```

---

# 98. Leftover

Leftover este diferit de ingredient raw.

```text
Leftover {
    id
    householdId

    recipeId?
    cookingBatchId?

    quantity
    unit

    nutritionSnapshotId

    storageLocation
    createdAt
    expiresAt?

    status
}
```

---

# 99. Leftover Status

```text
AVAILABLE
ALLOCATED
CONSUMED
DISCARDED
EXPIRED
```

---

# 100. Cooking Batch

Reprezintă ceea ce gătim efectiv împreună.

```text
CookingBatch {
    id
    householdId

    recipeId
    plannedAt
    cookedAt?

    status

    finalCookedWeight?
}
```

---

# 101. Cooking Batch Ingredient

```text
CookingBatchIngredient {
    id
    cookingBatchId

    foodId?
    productId?

    quantity
    unit
    preparationState
}
```

---

# 102. Batch Nutrition Snapshot

După gătire:

```text
CookingBatch {
    nutritionSnapshotId
}
```

---

# 103. Batch Final Weight

Pentru mixed meals:

```text
finalCookedWeightGrams
```

permite:

```text
kcal per cooked gram
```

---

# 104. Container

Caserola trebuie să fie first-class.

```text
MealContainer {
    id
    cookingBatchId

    assignedMemberId?
    assignedMealId?

    quantity
    unit

    storageLocation
    status
}
```

---

# 105. Container Status

```text
PREPARED
REFRIGERATED
FROZEN
CONSUMED
DISCARDED
```

---

# 106. Why Container Matters

Permite:

> Caserola #3 → Partner → Tuesday Lunch.

Acesta este un diferențiator important.

---

# 107. Meal Prep Session

O sesiune poate include mai multe batches.

```text
MealPrepSession {
    id
    householdId

    plannedStart
    actualStart?

    estimatedDuration?
    status
}
```

---

# 108. Meal Prep Session Batch

```text
MealPrepSessionBatch {
    sessionId
    cookingBatchId
    order?
}
```

---

# 109. Shopping List

```text
ShoppingList {
    id
    householdId

    mealPlanId?

    name
    status

    estimatedCost?
    createdAt
}
```

---

# 110. Shopping List Status

```text
DRAFT
READY
SHOPPING
COMPLETED
ARCHIVED
```

---

# 111. Shopping List Item

```text
ShoppingListItem {
    id
    shoppingListId

    foodId?
    productId?

    requiredQuantity
    requiredUnit

    packageCount?
    productPackageId?

    estimatedPrice?

    status
}
```

---

# 112. Shopping Item Status

```text
NEEDED
IN_CART
PURCHASED
UNAVAILABLE
SUBSTITUTED
SKIPPED
```

---

# 113. Demand vs Purchase

Trebuie diferențiate:

```text
need 650g rice
```

de:

```text
buy 1kg package
```

---

# 114. Shopping Requirement

Conceptual putem avea:

```text
IngredientDemand {
    foodId
    quantity
}
```

generat din plan.

ShoppingListItem reprezintă soluția comercială.

---

# 115. Receipt

```text
Receipt {
    id
    householdId

    storeId?

    purchaseDate
    currency

    subtotal?
    discountTotal?
    total

    source
    status
}
```

---

# 116. Receipt Status

```text
PARSED
NEEDS_REVIEW
CONFIRMED
REJECTED
```

---

# 117. Receipt Item

```text
ReceiptItem {
    id
    receiptId

    rawText

    productId?
    foodId?

    quantity?
    unit?

    unitPrice?
    totalPrice

    discount?

    confidence
}
```

---

# 118. Non-Food Receipt Item

```text
category:
FOOD
NON_FOOD
UNKNOWN
```

---

# 119. Purchase

Receipt reprezintă documentul.

Purchase reprezintă evenimentul financiar/logistic.

În MVP pot fi aceeași structură.

Pe termen lung putem separa:

```text
Purchase {
    id
    householdId
    storeId
    purchasedAt
}
```

---

# 120. Budget

```text
Budget {
    id
    householdId

    periodType
    startDate
    endDate

    amount
    currency

    category?
}
```

---

# 121. Budget Period

```text
WEEKLY
MONTHLY
CUSTOM
```

---

# 122. Budget Category

```text
GROCERIES
DINING_OUT
SUPPLEMENTS
SNACKS
OTHER
TOTAL_FOOD
```

---

# 123. Financial Transaction

Pentru reporting:

```text
FoodExpense {
    id
    householdId

    receiptId?
    category

    amount
    currency

    occurredAt

    source
}
```

---

# 124. Consumption Cost ≠ Cash Expense

Important.

Meal cost:

```text
9.20 lei consumed
```

Purchase:

```text
20 lei package paid
```

Nu trebuie stocate ca aceeași metrică.

---

# 125. Recipe Ingredient Cost

Poate fi calculat runtime sau cache:

```text
IngredientCostEstimate {
    food/product
    quantity
    priceSource
    cost
}
```

---

# 126. Preference Domain

Trebuie separat explicit de Learning.

---

# 127. Food Preference

```text
FoodPreference {
    id
    memberId
    foodId

    level
    source
    createdAt
    updatedAt
}
```

---

# 128. Food Preference Levels

```text
LOVE
LIKE
NEUTRAL
AVOID
NEVER
```

---

# 129. Meal Preference

```text
RecipePreference {
    id
    memberId
    recipeId

    level
    rating?
    source
}
```

---

# 130. Cuisine Preference

```text
CuisinePreference {
    memberId
    cuisine
    level
}
```

---

# 131. Attribute Preference

Pentru:

* creamy;
* spicy;
* crispy;
* cold;
* warm.

```text
PreferenceAttribute {
    memberId

    type
    value
    affinity
}
```

---

# 132. Preference Source

```text
EXPLICIT
LEARNED
IMPORTED
```

Explicit și learned nu trebuie suprascrise unul pe altul fără logică.

---

# 133. Variety Preference

```text
VarietyPreference {
    memberId
    level
}
```

Poate fi:

```text
0..1
```

intern.

---

# 134. Cooking Preference

La household:

```text
HouseholdCookingPreference {
    householdId

    targetCookingSessionsPerWeek?
    weekdayMaxPrepMinutes?
    weekendMaxPrepMinutes?

    mealPrepLevel
}
```

---

# 135. Household Equipment

```text
HouseholdEquipment {
    householdId
    equipmentType
    available
}
```

---

# 136. Learning Event

Raw behavioral event.

```text
LearningEvent {
    id

    memberId?
    householdId?

    type

    subjectType
    subjectId

    context?

    occurredAt
}
```

---

# 137. Learning Event Types

```text
RECOMMENDATION_SHOWN
MEAL_ACCEPTED
MEAL_REGENERATED
MEAL_REJECTED
MEAL_SKIPPED
MEAL_REPEATED

FOOD_FAVORITED
RECIPE_FAVORITED

INGREDIENT_REMOVED
INGREDIENT_ADDED

RECIPE_RATED
```

---

# 138. Event Context

Important pentru interpretation.

```text
LearningEventContext {
    reason?
    mealType?
    time?
    price?
    pantryAvailability?
}
```

---

# 139. Learned Affinity

Learning Engine produce state agregat.

```text
LearnedAffinity {
    id
    memberId

    subjectType
    subjectId

    dimension
    score
    confidence

    calculatedAt
    engineVersion
}
```

---

# 140. Subject Types

```text
FOOD
RECIPE
CUISINE
TEXTURE
PROTEIN_SOURCE
COOKING_STYLE
```

---

# 141. Household Affinity

Separat:

```text
HouseholdAffinity
```

pentru:

> mese acceptate de household împreună.

---

# 142. Recommendation Record

Extrem de util pentru debugging și learning.

```text
RecommendationRecord {
    id

    memberId?
    householdId?

    contextType
    contextSnapshot?

    selectedRecipeId?

    engineVersion
    createdAt
}
```

---

# 143. Recommendation Candidate

```text
RecommendationCandidateRecord {
    id
    recommendationRecordId

    recipeId
    score
    rank

    scoreBreakdown
    reasonCodes[]
}
```

---

# 144. User Recommendation Action

```text
RecommendationAction {
    recommendationRecordId

    action
    reason?

    occurredAt
}
```

---

# 145. Actions

```text
ACCEPTED
REGENERATED
REJECTED
IGNORED
FAVORITED
```

---

# 146. Weight Log

```text
WeightLog {
    id
    memberId

    weightKg
    measuredAt

    source
    confidence

    imageAssetId?
}
```

---

# 147. Weight Source

```text
MANUAL
SCALE_PHOTO
CONNECTED_SCALE
IMPORT
```

---

# 148. Body Measurement

Generic pentru viitor:

```text
BodyMeasurement {
    id
    memberId

    type
    value
    unit

    measuredAt
}
```

---

# 149. Progress Photo

Dacă va exista:

```text
ProgressAsset {
    id
    memberId

    type
    assetId
    capturedAt
}
```

Trebuie permisiuni stricte.

---

# 150. Daily Nutrition State

Poate fi calculat runtime, dar cache-ul este util.

```text
DailyNutritionState {
    id
    memberId
    date

    targetSnapshotId

    consumedSnapshotId
    plannedSnapshotId
    lockedSnapshotId
    remainingSnapshotId

    dataQuality
    calculatedAt
}
```

---

# 151. Why State Can Be Derived

Source of truth:

```text
targets
food logs
planned meals
```

DailyNutritionState este:

```text
derived state
```

Poate fi recalculat.

---

# 152. Nutrient Gap Snapshot

```text
NutrientGapSnapshot {
    id
    memberId
    date

    calculatedAt
    engineVersion
}
```

---

# 153. Nutrient Gap Value

```text
NutrientGapValue {
    snapshotId
    nutrientId

    current
    planned
    reference

    gap
    status
    urgency
    confidence
}
```

---

# 154. AI Job

Pentru vision și procesări.

```text
AIJob {
    id
    householdId
    accountId

    type
    status

    model?
    pipelineVersion?

    createdAt
    completedAt?

    errorCode?
}
```

---

# 155. AI Job Types

```text
MEAL_SCAN
FRIDGE_SCAN
RECEIPT_SCAN
SCALE_SCAN
LABEL_SCAN
PRODUCT_SCAN
RECIPE_GENERATION
INTENT_PARSE
```

---

# 156. AI Job Result

Nu un JSON universal fără structură.

Conceptual:

```text
AIJobResult {
    jobId
    resultType
    structuredPayload
    confidence
}
```

În implementare pot exista modele dedicate.

---

# 157. Asset

Toate imaginile trebuie tratate separat.

```text
Asset {
    id

    ownerAccountId?
    householdId?

    type
    storageKey
    mimeType

    createdAt
}
```

---

# 158. Asset Types

```text
MEAL_PHOTO
FRIDGE_PHOTO
RECEIPT_PHOTO
SCALE_PHOTO
LABEL_PHOTO
PRODUCT_PHOTO
PROGRESS_PHOTO
```

---

# 159. AI Detection

Generic pentru vision:

```text
AIDetection {
    id
    aiJobId

    type
    candidateEntityType?
    candidateEntityId?

    label
    confidence

    rawPayload?
}
```

---

# 160. Confirmation Record

Pentru user-confirmed detections:

```text
AIConfirmation {
    id
    detectionId

    status
    correctedValue?

    confirmedBy
    confirmedAt
}
```

---

# 161. Why Confirmation Matters

Ajută:

* audit;
* quality metrics;
* training/evaluation datasets;
* learning.

---

# 162. AI Action

Separat de AIJob.

```text
AIAction {
    id

    actorAccountId
    householdId

    actionType
    status

    intentConfidence

    createdAt
}
```

---

# 163. AI Action Mutation

```text
AIActionMutation {
    aiActionId

    entityType
    entityId

    beforeSnapshot?
    afterSnapshot?
}
```

---

# 164. Undo

AIAction poate avea:

```text
reversible = true
```

și eventual:

```text
revertedAt
```

---

# 165. Notification

```text
Notification {
    id
    accountId

    type
    title
    payload

    scheduledAt?
    readAt?
}
```

---

# 166. Notification Types

```text
EXPIRY
MEAL_PREP
GROCERY_MISSING
PLAN_CONFLICT
WEIGHT_REVIEW
GENERAL
```

---

# 167. Favorite

Putem folosi preferințele pentru majoritatea lucrurilor.

Dar dacă vrem generic:

```text
Favorite {
    account/member
    entityType
    entityId
}
```

Totuși este mai bine ca:

* food favorite;
* recipe favorite;

să aibă semantică proprie prin preferences.

---

# 168. Data Ownership

Trebuie clarificat cine deține fiecare entitate.

---

# 169. Account-Owned

Exemple:

```text
auth settings
personal UI preferences
```

---

# 170. Member-Owned

```text
nutrition targets
weight logs
food preferences
meal preferences
individual progress
```

---

# 171. Household-Owned

```text
pantry
shopping lists
receipts
budget
meal plans
cooking batches
equipment
```

---

# 172. Global / System-Owned

```text
foods
products
brands
nutrients
reference rules
system recipes
```

---

# 173. Shared vs Private Data

Nu orice member data trebuie vizibilă household-ului.

Trebuie să existe conceptual:

```text
visibility
```

pentru:

* weight;
* goals;
* progress.

---

# 174. Data Visibility

```text
PRIVATE
HOUSEHOLD
OWNER_ONLY
```

---

# 175. Household Planner Needs Derived Access

Planner-ul poate avea voie să folosească:

```text
nutrition targets
```

fără să afișeze neapărat:

```text
exact weight
```

altui household member.

Important pentru privacy.

---

# 176. Data Provenance

Mai multe entități trebuie să aibă:

```text
source
confidence
```

---

# 177. Standard Source Concept

```text
SYSTEM
USER
AI
EXTERNAL_API
BARCODE
RECEIPT
PROFESSIONAL
DERIVED
```

---

# 178. Confidence

Unde datele pot fi incerte:

```text
confidenceScore?
confidenceLevel
```

---

# 179. Confidence Levels

```text
VERIFIED
HIGH
MEDIUM
LOW
UNKNOWN
```

---

# 180. Exact vs Estimated

Quantity poate avea:

```text
precisionType
```

---

# 181. Precision Types

```text
EXACT
ESTIMATED
RANGE
STATUS_ONLY
```

---

# 182. Temporal Data

Foarte multe date trebuie versionate în timp.

Nu doar:

```text
currentValue
```

---

# 183. Effective-Dated Entities

Exemple:

```text
NutritionTargetProfile
MemberGoal
ActivityProfile
Budget
ScheduleRule
```

folosesc:

```text
validFrom
validUntil
```

---

# 184. Soft Delete

Pentru date importante:

```text
deletedAt
```

poate fi preferabil ștergerii fizice.

Exemple:

* recipes;
* preferences;
* plans.

---

# 185. Immutable Events

Acestea ar trebui ideal să fie append-only:

```text
LearningEvent
WeightLog
PriceObservation
PantryTransaction
RecommendationAction
```

Corecțiile pot crea eveniment nou.

---

# 186. Snapshot Philosophy

Snapshot folosim când avem nevoie de adevărul istoric.

---

# 187. Snapshot Candidates

```text
meal nutrition
food log nutrition
recipe execution
cooking batch nutrition
target at time of logging
recommendation context
```

---

# 188. What Should NOT Be Snapshot Everywhere

Nu duplicăm toată baza de date în fiecare event.

Doar datele necesare pentru:

* reproducibility;
* analytics;
* history.

---

# 189. Derived Data

Trebuie diferențiat de source of truth.

---

# 190. Derived Examples

```text
DailyNutritionState
NutrientGap
WeightTrend
ProjectedWeeklyCost
RecipeNutritionCache
```

Acestea pot fi regenerate.

---

# 191. Source-of-Truth Examples

```text
FoodLog
NutritionTarget
MealPlan
PantryTransaction
Receipt
Preference
WeightLog
```

---

# 192. State vs Event

Exemplu Pantry:

### Current state

```text
PantryItem.quantity = 400g
```

### History

```text
PantryTransaction
```

Ideal avem ambele:

* rapid pentru queries;
* event history pentru audit.

---

# 193. Event Model

Evenimente importante conceptual:

```text
meal.planned
meal.logged
meal.skipped
meal.replaced

pantry.added
pantry.consumed
pantry.discarded

shopping.completed
receipt.confirmed

weight.logged

preference.changed

plan.generated
plan.replanned
```

---

# 194. Event Bus Is Implementation Detail

Nu înseamnă că trebuie Kafka.

Inițial poate fi:

```text
domain service
+
database transaction
```

Dar events trebuie gândite conceptual.

---

# 195. Example — Meal Logged

```text
FoodLog created
      ↓
DailyNutritionState invalidated
      ↓
NutrientGap invalidated
      ↓
LearningEvent created
      ↓
Progress analytics updated
```

---

# 196. Example — Receipt Confirmed

```text
Receipt confirmed
      ↓
FoodExpense created
      ↓
PriceObservations created
      ↓
PantryTransactions created
      ↓
ShoppingList updated
```

---

# 197. Example — Plan Regenerated

```text
new MealPlan version
      ↓
old future meals superseded
      ↓
shopping demand recalculated
      ↓
meal prep sessions recalculated
```

---

# 198. Plan Versioning

Nu ar trebui să distrugem planul anterior.

```text
MealPlanRevision {
    id
    mealPlanId

    revision
    createdAt
    reason
}
```

Sau fiecare generation produce plan version nou.

---

# 199. Minimum Necessary Change

Replanning trebuie să păstreze stable identifiers unde masa nu se schimbă.

Astfel putem vedea:

```text
4 meals changed
17 unchanged
```

---

# 200. Meal Replacement Link

```text
Meal {
    replacedMealId?
}
```

sau:

```text
MealReplacement {
    oldMealId
    newMealId
    reason
}
```

---

# 201. External Meal

Pentru:

* restaurant;
* event;
* unknown dinner.

Meal poate avea:

```text
recipeId = null
source = EXTERNAL
```

și:

```text
NutritionReservation
```

---

# 202. Nutrition Reservation

```text
NutritionReservation {
    id
    mealId
    memberId

    estimatedEnergy?
    tolerance?

    nutritionSnapshot?
}
```

---

# 203. Reconciliation

După masă:

```text
reservation
→ FoodLog
```

Nu păstrăm reservation ca actual.

---

# 204. Food Alias vs Personal Alias

Global:

```text
FoodAlias
```

Personal:

```text
SavedMealAlias {
    memberId/householdId
    alias
    recipeId
}
```

---

# 205. Example

```text
"omleta mea"
→ Recipe 812
```

---

# 206. Custom Product

Dacă barcode database nu găsește produsul:

```text
Product {
    source = USER
}
```

și nutrition poate veni din:

```text
LABEL_SCAN
```

---

# 207. Product Deduplication

Trebuie să evităm:

```text
Pilos Greek Yogurt 2%
Pilos Greek Yoghurt 2
Iaurt Grecesc Pilos
```

ca trei produse.

Matching:

```text
barcode
brand
normalized name
package
```

---

# 208. Canonicalization Layer

Pentru:

* foods;
* products;
* units;
* nutrients;
* brands.

Extrem de important pentru analytics și recommendation.

---

# 209. Recipe Tags

Nu trebuie să fie singurul model semantic.

Dar pot exista:

```text
RecipeTag {
    recipeId
    tag
}
```

---

# 210. Tags Examples

```text
HIGH_PROTEIN
QUICK
MEAL_PREP
OFFICE
ONE_POT
COMFORT_FOOD
```

Unele taguri pot fi calculate, nu manuale.

---

# 211. Derived Recipe Characteristics

Exemplu:

```text
HIGH_PROTEIN
```

poate fi evaluat în contextul porției.

Deci nu toate caracteristicile trebuie hardcodate ca tags.

---

# 212. Recipe Semantic Embedding

Pentru AI/recommendation semantic:

```text
RecipeEmbedding {
    recipeId
    modelVersion
    vector
}
```

Implementare opțională.

---

# 213. Food Embedding

Similar pentru semantic substitutions/search.

---

# 214. Search Index

Search trebuie să poată găsi:

```text
food
product
recipe
brand
alias
```

din același query.

---

# 215. AI Context Snapshot

Pentru recommendation audit, nu stocăm neapărat toate raw entities.

Putem salva:

```text
RecommendationContextSnapshot {
    id

    nutritionStateSummary
    constraintsSummary
    preferenceSummary
    pantrySummary
    budgetSummary

    generatedAt
}
```

---

# 216. Privacy-Sensitive Snapshotting

Nu salvăm inutil:

* weight exact;
* toate datele personale;

în fiecare recommendation trace.

---

# 217. Nutrition Data Completeness

```text
NutritionCompleteness {
    entityType
    entityId

    macroCoverage
    coreMicroCoverage
    extendedMicroCoverage
}
```

Poate fi calculat.

---

# 218. Confidence Propagation

Dacă:

```text
FoodLogItem quantity = estimated
```

snapshot-ul nutrițional trebuie să știe că are confidence redus.

---

# 219. Domain Invariant — Household Ownership

Orice:

```text
MealPlan
PantryItem
ShoppingList
Receipt
CookingBatch
```

trebuie să aparțină exact unui household.

---

# 220. Domain Invariant — Member Ownership

Orice:

```text
WeightLog
NutritionTargetProfile
FoodPreference
```

aparține exact unui member.

---

# 221. Domain Invariant — Target History

Un member nu trebuie să aibă două NutritionTargetProfiles active incompatibile pentru aceeași perioadă și același mode.

---

# 222. Domain Invariant — Nutrient Units

Nutrient values se normalizează la unitatea canonical.

Nu avem:

```text
calcium sometimes mg
calcium sometimes g
```

în calcule.

---

# 223. Domain Invariant — Food Quantity

Orice quantity nutrițională trebuie să poată identifica:

```text
amount
unit
preparation state
```

unde este relevant.

---

# 224. Domain Invariant — Historical Logs

FoodLog nutrition snapshot este immutable după confirmare, cu excepția unei operații explicite de correction.

---

# 225. Domain Invariant — Unknown Nutrient

Missing nutrient:

```text
null
```

niciodată:

```text
0
```

doar pentru lipsa informației.

---

# 226. Domain Invariant — Receipt Confirmation

Receipt parsed de AI nu produce expense/pantry permanent până nu atinge starea:

```text
CONFIRMED
```

sau un flow explicit de auto-confirm controlat.

---

# 227. Domain Invariant — AI Detection

AIDetection nu este automat domain truth.

---

# 228. Domain Invariant — Constraints

Hard constraints nu pot fi eliminate de recommendation output.

---

# 229. Domain Invariant — Shared Meal

O masă shared are:

```text
1 Meal
N MealParticipants
N MealPortions
```

nu N copii complet independente ale aceleiași mese.

---

# 230. Why This Matters

Altfel:

* Cook view devine dificil;
* costurile se dublează;
* recipe history se fragmentează;
* shopping aggregation devine greu.

---

# 231. Domain Invariant — Batch

Un CookingBatch poate alimenta mai multe:

```text
MealPortions
MealContainers
Leftovers
```

---

# 232. Domain Invariant — Pantry Current Quantity

Current quantity trebuie să poată fi derivată/reconciliată cu transactions, chiar dacă pentru performanță este stocată direct.

---

# 233. Domain Invariant — Price

PriceObservation trebuie să păstreze:

```text
package quantity
```

Nu doar:

```text
price = 7.99
```

pentru că altfel nu putem compara.

---

# 234. Price Normalization

Derivat:

```text
pricePer100g
pricePerKg
pricePerUnit
```

---

# 235. Domain Invariant — Budget

Budget amount:

```text
cash spending
```

nu consumption cost.

---

# 236. Domain Invariant — Learning

Explicit preference nu este suprascris direct de learned affinity.

---

# 237. Preference Resolution

Recommendation Context poate calcula:

```text
effectivePreference
```

din:

```text
explicitPreference
learnedAffinity
temporaryConstraint
```

---

# 238. Effective Preference Is Derived

Nu trebuie stocat neapărat ca source of truth.

---

# 239. User Correction Event

Corectarea unui AI detection trebuie să poată produce:

```text
AIConfirmation
+
optional Learning/Quality event
```

---

# 240. Audit Entity

Pentru mutations importante:

```text
AuditLog {
    id

    actorAccountId
    action
    entityType
    entityId

    source
    timestamp
}
```

---

# 241. Audit Sources

```text
USER_UI
AI_COACH
VISION_CONFIRMATION
SYSTEM
IMPORT
```

---

# 242. Deletion Strategy

User trebuie să poată șterge datele personale relevante.

Pentru analytics:

nu trebuie păstrate identificabil împotriva intenției utilizatorului.

---

# 243. Domain Boundary — Workout

Aplicația nu devine fitness tracker complet.

Păstrăm doar contextul necesar nutriției:

```text
WorkoutSchedule
WorkoutEvent?
```

nu:

```text
exercise sets/reps full history
```

în acest domain.

---

# 244. Domain Boundary — Health

Nu construim:

```text
Diagnosis
Medication
MedicalTreatment
```

ca parte a sistemului de bază.

Dacă sunt necesare integrarea/professional mode, vor avea un domain separat cu safety specific.

---

# 245. Domain Boundary — Grocery Marketplace

Store/Product/Price există pentru planning.

Nu modelăm încă:

```text
checkout
delivery
inventory at store
payment
```

---

# 246. Aggregate Roots

Conceptual, principalele aggregates sunt:

```text
Household
Member
Recipe
MealPlan
Meal
Pantry
CookingBatch
ShoppingList
Receipt
```

---

# 247. Household Aggregate

Responsabil pentru:

* membership;
* shared defaults;
* currency;
* kitchen;
* high-level settings.

Nu trebuie să încarce tot pantry-ul și toate mesele într-un singur object gigantic.

---

# 248. Member Aggregate

Responsabil pentru:

* profile;
* goals;
* personal settings.

Nutrition Target poate fi serviciu/domain separat versionat.

---

# 249. Recipe Aggregate

Conține:

* ingredients;
* steps;
* capabilities;
* substitutions.

---

# 250. Meal Aggregate

Conține:

* participants;
* portions;
* meal-level constraints;
* state.

---

# 251. Meal Plan Aggregate

Coordonează:

* meal slots;
* meal references;
* planning configuration;
* revisions.

---

# 252. Pantry Aggregate

Coordonează:

* current inventory;
* transactions;
* storage.

---

# 253. Cooking Batch Aggregate

Coordonează:

* batch ingredients;
* cooked weight;
* containers;
* leftovers.

---

# 254. Shopping Aggregate

Coordonează:

* demand;
* product packages;
* checklist;
* purchase state.

---

# 255. Receipt Aggregate

Coordonează:

* merchant;
* items;
* totals;
* confirmation.

---

# 256. Read Models

UI nu trebuie să consume direct toate aggregates.

Avem read models precum:

```text
TodayView
WeeklyPlanView
CookView
GroceryView
PantryView
ProgressView
```

---

# 257. TodayView

Poate agrega:

```text
Member
DailyNutritionState
Meals
Upcoming Workout
Top Nutrient Gaps
```

---

# 258. CookView

Agregă:

```text
Recipe
CookingBatch
Household portions
Containers
Instructions
```

---

# 259. GroceryView

Agregă:

```text
ShoppingList
Products
Prices
Store grouping
```

---

# 260. Query Models ≠ Source Models

Este ok ca TodayView să conțină:

```text
remainingProtein
```

chiar dacă nu există ca o coloană directă în Member.

Este derived.

---

# 261. Suggested Domain Packages

În codebase:

```text
/domain
    /identity
    /household
    /nutrition
    /catalog
    /recipes
    /planning
    /meals
    /pantry
    /cooking
    /shopping
    /budget
    /preferences
    /learning
    /progress
    /ai
```

---

# 262. Identity Package

```text
Account
HouseholdMembership
```

---

# 263. Household Package

```text
Household
Member
HouseholdEquipment
HouseholdCookingPreference
```

---

# 264. Nutrition Package

```text
Nutrient
NutritionReferenceRule
NutritionTargetProfile
NutrientTarget
NutritionSnapshot
DailyNutritionState
NutrientGapSnapshot
```

---

# 265. Catalog Package

```text
Food
FoodAlias
FoodCategory
FoodNutrientValue
Product
ProductPackage
Brand
ProductNutrientValue
Store
PriceObservation
```

---

# 266. Recipes Package

```text
Recipe
RecipeIngredient
RecipeIngredientGroup
RecipeStep
RecipePreference
RecipeVersion
```

---

# 267. Planning Package

```text
MealPlan
MealPlanConfiguration
MealSlot
Constraint
MealPlanRevision
NutritionReservation
```

---

# 268. Meals Package

```text
Meal
MealParticipant
MealPortion
MealPortionComponent
FoodLog
FoodLogItem
```

---

# 269. Pantry Package

```text
Pantry
PantryItem
PantryTransaction
Leftover
ExpiryEstimate
```

---

# 270. Cooking Package

```text
CookingBatch
CookingBatchIngredient
MealPrepSession
MealContainer
```

---

# 271. Shopping Package

```text
ShoppingList
ShoppingListItem
Receipt
ReceiptItem
Purchase
```

---

# 272. Budget Package

```text
Budget
FoodExpense
PriceObservation
```

PriceObservation aparține semantic Catalog/Pricing, dar este consumat de Budget.

---

# 273. Preferences Package

```text
FoodPreference
CuisinePreference
PreferenceAttribute
VarietyPreference
PreferredProduct
```

---

# 274. Learning Package

```text
LearningEvent
LearnedAffinity
HouseholdAffinity
RecommendationRecord
RecommendationAction
```

---

# 275. Progress Package

```text
WeightLog
BodyMeasurement
ProgressAsset
```

---

# 276. AI Package

```text
AIJob
AIJobResult
AIDetection
AIConfirmation
AIAction
AIActionMutation
Asset
```

---

# 277. Conceptual Relationship Diagram

```text
Household
│
├── Member
│   ├── MemberProfile
│   ├── MemberGoal
│   ├── NutritionTargetProfile
│   │      └── NutrientTarget
│   ├── FoodPreference
│   ├── RecipePreference
│   ├── ScheduleRule
│   └── WeightLog
│
├── MealPlan
│   └── MealSlot
│         └── Meal
│              ├── MealParticipant → Member
│              └── MealPortion → Member
│
├── Pantry
│   └── PantryItem
│         └── PantryTransaction
│
├── CookingBatch
│   ├── CookingBatchIngredient
│   ├── MealContainer
│   └── Leftover
│
├── ShoppingList
│   └── ShoppingListItem
│
├── Receipt
│   └── ReceiptItem
│
└── Budget
```

Catalog:

```text
Food
├── FoodNutrientValue
├── FoodAlias
└── Product
      ├── ProductPackage
      ├── ProductNutrientValue
      └── PriceObservation
```

Recipe:

```text
Recipe
├── RecipeIngredient → Food/Product
├── RecipeStep
└── Meal → Recipe
```

---

# 278. Core Relationship — Same Meal, Different Portions

```text
Meal
│
├── MealParticipant: Alberto
│      └── MealPortion A
│
└── MealParticipant: Partner
       └── MealPortion B
```

Meal-ul rămâne unul singur.

---

# 279. Core Relationship — Recipe to Real Meal

```text
Recipe
  ↓
Meal
  ↓
Portions
  ↓
Food Logs
```

---

# 280. Core Relationship — Plan vs Reality

```text
MealPlan
   ↓
Meal
   ↓
Planned Portion
   │
   ├────────────┐
   ▼            ▼
Food Log A   Food Log B
(actual)
```

---

# 281. Core Relationship — Shopping to Pantry

```text
Meal Plan
    ↓
Ingredient Demand
    ↓
Shopping List
    ↓
Receipt / Purchase
    ↓
Pantry Transaction
    ↓
Pantry Current State
```

---

# 282. Core Relationship — Pantry Back to Planning

```text
Pantry
   ↓
Recommendation Context
   ↓
Meal Plan
   ↓
Ingredient Demand
```

Loop complet.

---

# 283. Core Relationship — Learning

```text
Recommendation
     ↓
User Action
     ↓
Learning Event
     ↓
Learned Affinity
     ↓
Future Recommendation
```

---

# 284. Core Relationship — Nutrition

```text
Food/Product
    ↓
Nutrition Values
    ↓
Recipe
    ↓
Meal Portion
    ↓
Nutrition Snapshot
    ↓
Food Log
    ↓
Daily Nutrition State
    ↓
Nutrient Gap
```

---

# 285. Core Relationship — Weight Adaptation

```text
WeightLog
   ↓
WeightTrend
   ↓
Progress Evaluation
   ↓
Target Adjustment Proposal
   ↓
NutritionTargetProfile revision
```

---

# 286. Core Relationship — AI

```text
Asset / User Message
      ↓
AIJob
      ↓
AIDetection / AIIntent
      ↓
Confirmation / Validation
      ↓
Domain Action
      ↓
AIAction Audit
```

---

# 287. Example Domain Flow — Breakfast

User:

> Generate breakfast.

System:

```text
Member
↓
Current NutritionTarget
↓
DailyNutritionState
↓
NutrientGap
↓
Preferences
↓
Pantry
↓
RecommendationRecord
↓
Recipe
↓
Meal
↓
MealPortion
```

---

# 288. User Eats Breakfast

```text
Meal
↓
FoodLog
↓
NutritionSnapshot
↓
DailyNutritionState recalculated
↓
NutrientGap recalculated
↓
LearningEvent
```

---

# 289. Example Domain Flow — Grocery Trip

```text
MealPlan
↓
ShoppingList
↓
User shops
↓
Receipt photo
↓
AIJob
↓
Receipt
↓
User confirms
↓
FoodExpense
↓
PriceObservation
↓
PantryTransactions
```

---

# 290. Example Domain Flow — Meal Prep

```text
MealPlan
↓
MealPrepSession
↓
CookingBatch
↓
Final cooked weight
↓
MealContainers
↓
Assigned future Meals
↓
FoodLogs when consumed
```

---

# 291. Example Domain Flow — Fridge Scan

```text
Asset
↓
AIJob
↓
AIDetection[]
↓
AIConfirmation[]
↓
PantryTransactions
↓
PantryItems updated
```

---

# 292. Example Domain Flow — Scale Scan

```text
Asset
↓
AIJob
↓
Scale Detection
↓
Confirmation
↓
WeightLog
↓
WeightTrend
```

---

# 293. MVP-Friendly Simplifications

Modelul complet este intenționat bogat.

MVP-ul nu trebuie să implementeze toate entitățile separat din prima.

De exemplu putem simplifica:

```text
Account + Member
```

pentru utilizatorii autentificați.

Dar API/domain boundary trebuie să nu presupună că sunt întotdeauna identice.

---

# 294. Possible MVP Consolidations

Se pot combina temporar:

```text
MealSlot + Meal
```

dacă este foarte simplu.

```text
Receipt + Purchase
```

pot fi unul.

```text
NutritionSnapshot values
```

pot fi JSONB inițial.

```text
RecipeCapabilities
```

pot fi coloane/JSON.

---

# 295. What Must NOT Be Simplified Away

Chiar și în MVP trebuie păstrate semantic separat:

```text
Food vs Product
Recipe vs Meal
Plan vs Actual
Household vs Member
Nutrition Target vs Nutrition State
Explicit Preference vs Learned Preference
Pantry Current State vs Purchase
```

Aceste separări sunt arhitectural fundamentale.

---

# 296. JSONB vs Relational

Unele date sunt potrivite pentru JSONB:

```text
scoreBreakdown
AI structured payload
recommendation context snapshot
raw external API response
```

---

# 297. Should Stay Relational

```text
members
foods
products
recipes
meal plans
meal participants
food logs
pantry items
receipts
prices
preferences
```

pentru queryability și integrity.

---

# 298. Nutrition Vectors

Pentru calcule intensive putem utiliza intern:

```text
typed vector/object
```

dar canonical DB poate păstra:

```text
snapshot + nutrient values
```

sau JSONB controlat.

Decizia exactă se ia în Technical Architecture.

---

# 299. IDs

Toate entitățile trebuie să folosească IDs stabile, independent de:

* nume;
* barcode;
* external API IDs.

External identifiers trebuie stocate separat.

---

# 300. External Identifier

```text
ExternalIdentifier {
    entityType
    entityId

    provider
    externalId
}
```

Exemplu:

```text
OpenFoodFacts barcode/product id
```

---

# 301. Source Raw Data

Pentru debugging putem păstra:

```text
ExternalSourceRecord {
    provider
    externalId
    payload
    fetchedAt
}
```

cu retenție controlată.

---

# 302. Domain Validation Order

La orice mutation:

```text
AUTHORIZATION
      ↓
SCHEMA VALIDATION
      ↓
DOMAIN INVARIANTS
      ↓
SAFETY CONSTRAINTS
      ↓
TRANSACTION
      ↓
DERIVED STATE INVALIDATION
      ↓
EVENTS
```

---

# 303. Transaction Boundary Example

Confirm receipt trebuie să fie atomic pentru:

```text
Receipt CONFIRMED
FoodExpense
PriceObservations
PantryTransactions
```

sau sistemul trebuie să poată relua safely.

---

# 304. Idempotency

Acțiuni precum:

```text
receipt.confirm
meal.log
weight.log
```

trebuie să accepte:

```text
idempotencyKey
```

pentru AI/retry/mobile.

---

# 305. Optimistic Concurrency

Mai ales la household:

doi utilizatori pot modifica:

* shopping list;
* pantry;
* plan.

Trebuie să evităm silent overwrites.

---

# 306. Current State Version

Entități precum:

```text
MealPlan
PantryItem
ShoppingList
```

pot avea:

```text
version
updatedAt
```

---

# 307. Household Multi-User Scenario

Partner bifează:

```text
Eggs purchased
```

în timp ce alt user deschide lista.

UI trebuie să primească actualizarea fără să creeze duplicate.

---

# 308. Domain Query — What Should I Eat?

Trebuie să putem obține eficient:

```text
Member
Current Target
Daily State
Future Locked Meals
Preferences
Recent Meals
Pantry
Schedule
Household Participants
Budget
```

---

# 309. Domain Query — Generate Week

Trebuie să putem obține:

```text
Household
Members
Targets
Schedules
Preferences
Pantry
Budget
Cooking Preferences
Locked Meals
Recent Recipe History
```

---

# 310. Domain Query — Grocery List

Necesită:

```text
Active Meal Plan
Meal Portions
Recipe Ingredients
Pantry
Preferred Products
Product Packages
Price Observations
```

---

# 311. Indexing Implications

Ulterior SQL-ul trebuie optimizat pentru:

```text
householdId
memberId
date
mealPlanId
foodId
productId
barcode
recipeId
status
```

---

# 312. Data Retention

Nu toate datele trebuie păstrate pentru totdeauna.

Exemplu:

raw AI images pot avea retention diferit de:

```text
confirmed WeightLog
```

---

# 313. Derived AI Data

După confirmare putem păstra:

```text
structured detection
```

chiar dacă imaginea poate fi ștearsă ulterior.

---

# 314. User Export

Modelul trebuie să permită ulterior exportul:

```text
weight history
food logs
meal plans
preferences
receipts
```

într-o formă inteligibilă.

---

# 315. Domain Scalability

Modelul trebuie să funcționeze pentru:

```text
1 person
2 people
family
```

fără trei arhitecturi diferite.

---

# 316. Individual Is A Household Of One

Aceasta este regula cea mai elegantă.

```text
Household
└── Member
```

Nu avem:

```text
if singleUser...
else household...
```

în tot codul.

---

# 317. Household Default Participant Logic

Pentru un meal slot:

```text
resolveParticipants()
```

folosește:

* schedule;
* meal defaults;
* explicit overrides.

---

# 318. Domain Scalability — Professional Future

Mai târziu putem introduce:

```text
Coach / Nutrition Professional
```

care gestionează targeturi.

Nu trebuie inclus acum, dar `PROFESSIONAL_CONFIGURED` deja permite extensia.

---

# 319. Domain Scalability — Store Intelligence

PriceObservation permite ulterior:

```text
price comparison
preferred stores
weekly cheapest basket
```

fără schimbarea modelului de bază.

---

# 320. Domain Scalability — Integrations

External identifiers permit:

* Open Food Facts;
* wearables;
* connected scales;
* supermarket APIs.

---

# 321. Domain Scalability — Recipe Marketplace

Recipe ownership/source/status permit ulterior:

* public recipes;
* curated recipes;
* community recipes.

Nu este scope actual.

---

# 322. Data Model North Star

Domain model-ul este corect dacă putem răspunde fără hacks la:

> Cine mănâncă?

> Ce are nevoie?

> Ce s-a planificat?

> Ce a mâncat efectiv?

> Ce avem în casă?

> Ce trebuie cumpărat?

> Ce gătim?

> Cui îi aparține fiecare porție?

> Cât costă?

> Ce îi place?

> Ce a învățat sistemul?

> De unde vine fiecare valoare?

---

# 323. Core Domain Vocabulary

Vocabularul trebuie considerat locked:

```text
Account
Household
Member

Food
Product
Recipe

Meal Plan
Meal Slot
Meal
Meal Portion
Food Log

Nutrition Target
Nutrition Snapshot
Nutrition State
Nutrient Gap

Pantry Item
Leftover

Cooking Batch
Meal Container
Meal Prep Session

Shopping List
Receipt
Price Observation
Budget

Preference
Learning Event
Learned Affinity

AI Job
AI Detection
AI Action
```

---

# 324. Critical Distinctions

Aceste diferențe nu trebuie pierdute în implementare:

```text
Account ≠ Member

Food ≠ Product

Recipe ≠ Meal

Meal ≠ Food Log

Planned ≠ Consumed

Generic nutrition ≠ product nutrition

Current definition ≠ historical snapshot

Pantry item ≠ shopping list item

Ingredient demand ≠ package purchase

Consumption cost ≠ cash expense

Explicit preference ≠ learned affinity

AI detection ≠ confirmed domain data
```

---

# 325. Ultimate Domain Example

Household-ul are doi membri.

```text
Household
├── Alberto
└── Partner
```

Planner-ul creează:

```text
Tuesday Lunch
Chicken Fajita Bowl
```

cu:

```text
Meal
├── Alberto Portion
│     620 kcal
│
└── Partner Portion
      460 kcal
```

Este gătită duminică prin:

```text
CookingBatch
```

și împărțită în:

```text
Container A → Alberto → Tuesday
Container B → Partner → Tuesday
```

Ingredientele au fost cumpărate prin:

```text
ShoppingList
↓
Receipt
↓
Pantry
```

La consum:

```text
FoodLog
```

salvează nutriția reală.

Aceasta actualizează:

```text
DailyNutritionState
↓
NutrientGap
```

iar următoarea masă este recalculată.

Acțiunea de acceptare generează:

```text
LearningEvent
```

și influențează viitoarele recomandări.

Tot sistemul este conectat fără ca una dintre entități să fie obligată să joace cinci roluri diferite.

---

# 326. Final Domain Principle

Baza de date nu trebuie proiectată după ecranele aplicației.

Trebuie proiectată după **realitatea pe care aplicația încearcă să o înțeleagă**:

> persoane care au nevoi individuale, mănâncă mese, gătesc împreună, cumpără produse, țin alimente în casă, fac alegeri, își schimbă planurile și dezvoltă preferințe în timp.

Dacă modelăm corect această realitate, UI-ul, AI-ul și engine-urile pot evolua fără să fie nevoie să reconstruim fundația produsului.
