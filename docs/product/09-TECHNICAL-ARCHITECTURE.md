# 09 — Technical Architecture

## 1. Scopul documentului

Acest document definește arhitectura tehnică a produsului descris în documentele `00–08`.

El stabilește:

* stack-ul tehnologic;
* structura repository-ului;
* arhitectura frontend;
* arhitectura backend;
* organizarea modulelor;
* comunicarea dintre module;
* API contracts;
* autentificarea;
* authorization;
* persistence;
* database conventions;
* nutrition calculation architecture;
* recommendation architecture;
* planning architecture;
* AI integration;
* background processing;
* caching;
* file storage;
* observability;
* testing;
* deployment;
* security;
* versioning;
* extensibility;
* strategia de evoluție tehnică între iterații.

Obiectivul este:

> **să putem construi un produs foarte simplu astăzi fără să sacrificăm arhitectura necesară produsului foarte complex de mâine.**

---

# 2. Architectural North Star

Arhitectura trebuie să satisfacă simultan două cerințe aparent contradictorii:

### Cerința 1

Iteration 0 trebuie să fie extrem de simplă.

### Cerința 2

Iteration 10 nu trebuie să necesite rescrierea fundamentelor.

Prin urmare:

> **Simple implementation. Strong boundaries.**

---

# 3. Architecture Style

Alegerea principală:

> **Modular Monolith**

Nu microservices.

Nu serverless functions împrăștiate.

Nu 15 servicii separate.

---

# 4. Why Modular Monolith

Produsul are multe domain-uri:

```text
nutrition
meals
recipes
planning
household
pantry
shopping
budget
learning
ai
```

dar acestea comunică intens.

Exemplu:

```text
FoodLog
   ↓
Nutrition State
   ↓
Nutrient Gap
   ↓
Recommendation
   ↓
Meal Plan
   ↓
Shopping
```

Separarea prematură în microservices ar transforma operații simple în:

```text
HTTP calls
message queues
distributed transactions
network failures
service discovery
```

fără beneficiu real.

---

# 5. Modular Monolith Does NOT Mean Spaghetti Monolith

Trebuie să avem:

```text
ONE deployment
ONE application
ONE database
```

dar:

```text
CLEAR module boundaries
CLEAR domain ownership
CLEAR interfaces
```

---

# 6. High-Level Architecture

```text
┌─────────────────────────────────────┐
│              CLIENT                 │
│                                     │
│        Next.js / React PWA          │
│                                     │
│  Today · Plan · Add · Shopping · Me │
└──────────────────┬──────────────────┘
                   │
                   │ HTTPS / JSON
                   │ Bearer JWT
                   ▼
┌─────────────────────────────────────┐
│             FASTAPI API             │
│                                     │
│        Modular Python Backend       │
│                                     │
│ Household  Nutrition  Recipes       │
│ Meals      Planning   Pantry        │
│ Shopping   Budget     Progress      │
│ Learning   AI                         │
└──────────────────┬──────────────────┘
                   │
                   ▼
┌─────────────────────────────────────┐
│             PostgreSQL              │
│                                     │
│     Canonical Application State     │
└─────────────────────────────────────┘
```

Ulterior:

```text
            FastAPI
               │
        ┌──────┴───────┐
        ▼              ▼
   PostgreSQL       Job Queue
                       │
                       ▼
                  Python Worker
                       │
              AI / Vision / Solver
```

---

# 7. Final Stack Direction

## Frontend

```text
Next.js
React
TypeScript
Tailwind CSS
shadcn/ui
TanStack Query
PWA
```

---

## Backend

```text
Python
FastAPI
Pydantic
SQLAlchemy 2
Alembic
```

---

## Persistence

```text
PostgreSQL
```

---

## Python Tooling

```text
uv
Ruff
Pyright
pytest
```

---

## Future Optimization

```text
Python heuristics
↓
OR-Tools
```

---

## Future AI

```text
Provider-agnostic AI adapters
Vision models
LLMs
Embeddings only if justified
```

---

## Future Infrastructure

```text
Object Storage
Job Queue
Worker
Redis only if justified
```

---

# 8. Why Next.js

Next.js App Router este router-ul modern al framework-ului și suportă arhitectura bazată pe React Server Components.

Pentru produsul nostru este potrivit deoarece avem:

* aplicație React foarte interactivă;
* mobile-first UX;
* server-rendered initial shell;
* authenticated application;
* eventual public marketing site;
* PWA;
* posibilitate de streaming/loading states.

---

# 9. Next.js Is The UI Layer

Foarte important:

Next.js NU devine al doilea backend.

Nu implementăm:

```text
Nutrition Engine in Next.js
Planner in Next.js
Budget Engine in Next.js
```

Canonical business logic rămâne în Python.

---

# 10. Server Components

Pot fi folosite pentru:

* application shell;
* auth bootstrap;
* static/reference data;
* initial route hydration.

---

# 11. Client Components

Sunt naturale pentru:

* Today interactions;
* meal logging;
* bottom sheets;
* shopping checklist;
* drag/drop planning;
* Cook mode;
* camera flows.

---

# 12. Frontend Server State

Recomand:

```text
TanStack Query
```

pentru datele dinamice venite din FastAPI.

TanStack Query este construit explicit în jurul queries, mutations și query invalidation/caching.

---

# 13. Server State ≠ UI State

Exemple server state:

```text
Today
Meal Plan
Pantry
Shopping List
Preferences
```

→ TanStack Query.

Exemple UI state:

```text
bottom sheet open
selected calendar day
temporary portion slider
```

→ React state.

---

# 14. Avoid Global State Store Initially

Nu introducem din prima:

```text
Redux
Zustand
MobX
```

doar pentru că „poate vom avea nevoie”.

Majoritatea stării este:

```text
server state
```

și aparține backend-ului.

---

# 15. Add Global Client Store Only When Proven Necessary

Dacă ulterior există:

* complex offline state;
* long multi-screen drafts;
* camera session state;

putem introduce un store dedicat.

---

# 16. Frontend Feature Structure

```text
apps/web/src/

├── app/
│
├── features/
│   ├── today/
│   ├── household/
│   ├── profile/
│   ├── progress/
│   ├── recipes/
│   ├── planner/
│   ├── cooking/
│   ├── shopping/
│   ├── pantry/
│   ├── budget/
│   └── ai/
│
├── components/
│   └── ui/
│
├── api/
│
├── hooks/
│
├── lib/
│
└── styles/
```

---

# 17. Feature-First Frontend

Greșit:

```text
components/
  Button
  Card
  MealCard
  PantryThing

hooks/
services/
pages/
```

pentru tot produsul.

Preferăm:

```text
features/today/
    components/
    hooks/
    queries/
    utils/
```

---

# 18. Shared Components

Doar componente generic reutilizabile intră în:

```text
components/ui/
```

Exemple:

```text
Button
Sheet
Dialog
Tabs
Progress
Card
```

---

# 19. Domain-Aware Components Stay In Feature

```text
MealCard
NutritionSummary
ShoppingItem
```

nu trebuie transformate prematur în „universal components”.

---

# 20. Frontend API Contract

Frontend-ul nu trebuie să definească manual tipurile backend-ului.

Flow:

```text
Pydantic
   ↓
FastAPI OpenAPI
   ↓
TypeScript Types / Client
   ↓
Next.js
```

FastAPI este construit pe OpenAPI și JSON Schema și permite automat client generation din contractele API.

---

# 21. Generated API Client

Recomand:

```text
OpenAPI schema
↓
openapi-typescript
↓
openapi-fetch
```

sau un generator echivalent.

`openapi-fetch` oferă un client TypeScript construit în jurul schema OpenAPI generată.

---

# 22. API Contract Rule

Niciodată:

```text
backend changed response
frontend type manually forgotten
```

CI trebuie să detecteze drift-ul.

---

# 23. CI Contract Check

Pipeline:

```text
generate openapi.json
↓
generate TS types
↓
git diff
```

Dacă generated client nu este actualizat:

CI fail.

---

# 24. Forms

Frontend:

```text
React Hook Form
+
Zod
```

poate fi folosit pentru UX.

Dar:

> **backend validation remains authoritative.**

---

# 25. Backend Choice

Backend-ul recomandat:

> **Python + FastAPI**

FastAPI folosește tipurile Python/Pydantic pentru validation și generează automat OpenAPI și JSON Schema.

---

# 26. Why Python Fits This Product

Majoritatea complexității viitoare va fi:

```text
nutrition calculations
constraint solving
optimization
recommendations
AI
vision
data processing
statistics
```

Python este foarte bine poziționat pentru toate.

---

# 27. FastAPI Responsibility

FastAPI trebuie să ofere:

```text
HTTP layer
authentication integration
authorization
request validation
response serialization
dependency injection
API documentation
```

Nu trebuie să conțină business logic direct în route handlers.

---

# 28. Pydantic

Pydantic este folosit pentru:

```text
API input
API output
application commands
engine inputs
engine outputs
configuration
```

Pydantic models definesc și validează structuri folosind Python type annotations.

---

# 29. Pydantic ≠ Database ORM

Important:

```text
Pydantic Model
≠
SQLAlchemy Model
```

Nu încercăm să avem o singură clasă magică pentru:

* API;
* domain;
* database.

---

# 30. Three Model Categories

## Persistence Models

```text
SQLAlchemy
```

## Application / Domain DTOs

```text
Pydantic / dataclasses
```

## API Schemas

```text
Pydantic
```

Uneori pot coincide.

Dar conceptual sunt diferite.

---

# 31. SQLAlchemy

Recomand:

```text
SQLAlchemy 2.x
```

Documentația curentă SQLAlchemy 2.0 este pe seria 2.0.52, iar SQLAlchemy oferă ORM, session management și suport asyncio.

---

# 32. SQLAlchemy Async

Putem folosi:

```text
AsyncEngine
AsyncSession
```

pentru request handling async.

SQLAlchemy oferă explicit `create_async_engine()` și API-uri async pentru Core și ORM.

---

# 33. Async Does Not Mean Parallel Everything

Nu folosim:

```text
asyncio.gather()
```

peste 20 queries doar pentru că putem.

Chiar documentația SQLAlchemy avertizează că asemenea pattern-uri pot pierde transactional safety și pot adăuga overhead.

---

# 34. Transaction First

Pentru business operations:

```text
one application operation
=
one explicit transaction boundary
```

acolo unde este posibil.

---

# 35. PostgreSQL

Canonical database:

> **PostgreSQL**

La data acestui document, versiunea curentă PostgreSQL este 18.4; proiectul PostgreSQL menține fiecare major release timp de aproximativ cinci ani.

Nu este însă necesar să folosim features specifice PG18.

---

# 36. Database Portability Rule

Folosim o versiune recentă și supported de provider.

Evităm:

```text
PG18-only critical feature
```

dacă deployment provider este încă pe 17.

---

# 37. Alembic

Schema migrations:

```text
Alembic
```

Alembic este migration tool-ul oficial folosit în ecosistemul SQLAlchemy și suportă inclusiv autogeneration.

---

# 38. Migration Rule

Nicio schimbare de DB în production fără migration.

Nu:

```text
create_all()
```

în production.

---

# 39. Migration Files Are Code

Trebuie:

* versionate;
* review;
* testate;
* deployment-safe.

---

# 40. Python Dependency Management

Recomand:

```text
uv
```

cu:

```text
pyproject.toml
uv.lock
```

`uv` suportă proiecte Python, lockfile reproducibil și dependency management direct din `pyproject.toml`.

---

# 41. Python Formatting & Linting

Recomand:

```text
Ruff
```

pentru:

* formatting;
* linting;
* import rules.

Ruff oferă atât linter cât și formatter într-un singur toolchain.

---

# 42. Python Type Checking

Recomand:

```text
Pyright
```

strictness progresivă.

Domain engines trebuie să fie puternic tipate.

---

# 43. Repository

Recomand:

> **monorepo**

---

# 44. Why Monorepo

Avem:

```text
frontend
backend
docs
infra
generated contracts
```

care evoluează împreună.

Un singur PR poate modifica:

```text
API schema
+
Python implementation
+
TypeScript client
+
UI
```

---

# 45. Repository Structure

```text
meal-os/
│
├── apps/
│   │
│   ├── web/
│   │   └── Next.js
│   │
│   ├── api/
│   │   └── FastAPI
│   │
│   └── worker/              # appears only when needed
│
├── packages/
│   │
│   └── api-client/
│       └── generated TypeScript client
│
├── docs/
│   │
│   ├── product/
│   ├── architecture/
│   └── adr/
│
├── infra/
│
├── scripts/
│
├── docker-compose.yml
│
├── Makefile / justfile
│
└── README.md
```

---

# 46. No Worker In Iteration 0

Folderul poate să nu existe încă.

Roadmap structure este conceptuală.

---

# 47. Backend Structure

```text
apps/api/app/

├── main.py
│
├── core/
│   ├── config.py
│   ├── database.py
│   ├── auth.py
│   ├── logging.py
│   ├── errors.py
│   ├── security.py
│   └── telemetry.py
│
├── modules/
│   │
│   ├── household/
│   ├── nutrition/
│   ├── catalog/
│   ├── recipes/
│   ├── meals/
│   ├── progress/
│   ├── planning/
│   ├── cooking/
│   ├── pantry/
│   ├── shopping/
│   ├── budget/
│   ├── preferences/
│   ├── learning/
│   └── ai/
│
└── api/
    └── v1/
```

---

# 48. Backend Module Structure

Un modul mic:

```text
nutrition/

├── models.py
├── schemas.py
├── repository.py
├── service.py
├── router.py
├── engine/
│   ├── calculations.py
│   ├── targets.py
│   └── gaps.py
└── tests/
```

---

# 49. Module Grows Only When Needed

Dacă nutrition devine complex:

```text
nutrition/
├── domain/
├── application/
├── infrastructure/
├── api/
└── tests/
```

Dar nu în V0.

---

# 50. Avoid Ceremony Architecture

Nu construim:

```text
UseCase
UseCaseHandler
CommandBus
Mediator
FactoryFactory
```

pentru fiecare CRUD simplu.

---

# 51. Domain Boundary Is More Important Than Number Of Layers

Trebuie să putem spune:

```text
nutrition module owns nutrition logic
```

Aceasta contează mai mult decât dacă avem:

```text
3 sau 5 folders
```

---

# 52. Module Dependency Rule

Modulele nu trebuie să importe persistence intern al altui modul.

Greșit:

```python
from modules.pantry.models import PantryItem
```

în Recommendation Engine și query direct.

Preferat:

```text
PantryService
PantryQuery
RecommendationContextBuilder
```

---

# 53. Public Module Interface

Fiecare modul expune:

```text
commands
queries
services
domain events
```

necesare celorlalte.

---

# 54. Internal Models Stay Internal

Exemplu:

```text
pantry.repositories.sqlalchemy
```

nu este public API pentru planning.

---

# 55. Dependency Direction

```text
API
 ↓
Application Services
 ↓
Domain / Engines
 ↓
Repository Interfaces
 ↓
SQLAlchemy Implementations
```

---

# 56. Engines Must Be Framework-Free

Foarte important.

```text
Nutrition Engine
Portion Solver
Recommendation Engine
Weekly Planner
```

nu trebuie să importe:

```text
FastAPI
SQLAlchemy
HTTP
Supabase
```

---

# 57. Pure Engine Example

```text
NutritionContext
        ↓
calculate_daily_state()
        ↓
DailyNutritionState
```

---

# 58. Benefits

Aceleași engines pot fi:

* unit tested;
* benchmarked;
* simulate;
* folosite într-un worker;
* mutate ulterior într-un serviciu separat;

fără rewrite.

---

# 59. Application Services

Application service coordonează:

```text
read domain data
↓
build engine input
↓
execute engine
↓
validate output
↓
persist result
```

---

# 60. Example — Log Meal

```text
LogMealService

1. authorize member
2. load meal
3. validate quantity
4. calculate nutrition
5. create FoodLog
6. create NutritionSnapshot
7. commit transaction
8. invalidate DailyNutritionState
9. record learning event
```

---

# 61. Example — Recommend Meal

```text
RecommendMealService

1. build RecommendationContext
2. calculate gaps
3. load eligible recipes
4. rank candidates
5. solve portions
6. validate
7. record recommendation
8. return view model
```

---

# 62. RecommendationContextBuilder

Trebuie să existe ca application component.

```text
RecommendationContextBuilder
```

centralizează:

```text
member
nutrition state
preferences
schedule
pantry
budget
recent history
locks
```

---

# 63. Why Context Builder Matters

Fără el:

Recommendation Engine ajunge să facă:

```text
20 DB queries
+
knowledge about 8 modules
```

și devine imposibil de testat.

---

# 64. Engine Input

```python
RecommendationContext
```

este un obiect complet, immutable pentru execuția curentă.

---

# 65. API Style

Recomand:

> **REST + domain-oriented endpoints**

Nu GraphQL inițial.

---

# 66. API Version

```text
/api/v1/
```

---

# 67. Do Not Expose Database CRUD As API

Greșit:

```text
POST /meal-portions
PATCH /nutrition-snapshots
```

pentru UI normal.

---

# 68. Expose User Actions

Mai bine:

```text
GET  /v1/today
POST /v1/meals/{mealId}/consume
POST /v1/meals/{mealId}/skip
POST /v1/meals/{mealId}/replace

POST /v1/recommendations/next-meal

GET  /v1/plans/current
POST /v1/plans/generate

GET  /v1/shopping-lists/current
POST /v1/weight-logs
```

---

# 69. API Mirrors UX Intent

User apasă:

```text
Am mâncat
```

→ endpoint:

```text
consume meal
```

Nu frontend-ul coordonează 7 table mutations.

---

# 70. Thin Frontend Rule

Frontend nu trebuie să știe:

> ce recalculări trebuie făcute după FoodLog.

Backend știe.

---

# 71. Query Endpoints

Trebuie optimizate pentru ecrane.

Exemplu:

```text
GET /today
```

poate returna:

```text
date
member
nutritionSummary
nextAction
meals[]
insights[]
```

Nu forțăm frontend-ul să facă 8 requests.

---

# 72. Read Models

Backend-ul poate avea:

```text
TodayView
PlanDayView
MealDetailView
CookView
ShoppingView
ProgressView
```

---

# 73. Read Models Are Not Database Entities

`TodayView` poate agrega 10 tabele.

Este normal.

---

# 74. API Error Contract

Toate erorile trebuie să aibă formă standard:

```text
code
message
details?
requestId
```

---

# 75. Example

```json
{
  "code": "NO_FEASIBLE_RECOMMENDATION",
  "message": "No meal satisfies all current constraints.",
  "details": {
    "relaxableConstraints": []
  }
}
```

Frontend transformă asta în UX potrivit.

---

# 76. No Exception Text Leakage

Nu trimitem:

```text
SQLAlchemyIntegrityError(...)
```

către client.

---

# 77. Authentication Strategy

Recomand ca authentication să fie delegated.

O opțiune foarte potrivită:

> **Supabase Auth**

Supabase Auth folosește JWT și se integrează cu Postgres; documentația suportă inclusiv server-side auth și RLS.

---

# 78. Auth Architecture

```text
Next.js
↓
Supabase Auth
↓
JWT
↓
FastAPI
↓
verify JWT
↓
Account
```

---

# 79. Frontend Does NOT Use Supabase DB Directly

Foarte important.

```text
Next.js
    X
    ↓
Supabase DB direct
```

pentru domain operations.

---

# 80. All Domain Operations Go Through FastAPI

```text
Next.js
↓
FastAPI
↓
Domain
↓
Postgres
```

---

# 81. Why

Altfel avem două business logic paths:

```text
Frontend → Supabase
Frontend → FastAPI
```

și authorization/domain logic se fragmentează.

---

# 82. Supabase Used As Infrastructure

Poate oferi:

```text
Auth
Postgres
Storage later
```

dar:

> **FastAPI remains application authority.**

---

# 83. Database Schema Exposure

Dacă folosim Supabase:

aplicația noastră poate sta într-un schema Postgres:

```text
app
```

care nu este expus direct browserului.

---

# 84. RLS

Dacă orice app table este expus către Supabase APIs, Row Level Security trebuie configurat riguros; Supabase recomandă RLS pentru tabelele din exposed schemas.

În arhitectura preferată însă:

> frontend-ul nu citește domain data direct.

---

# 85. Authorization Lives In Application Layer

Exemplu:

```text
Can account A read member B?
```

se determină prin:

```text
HouseholdMembership
visibility rules
```

---

# 86. Authentication ≠ Authorization

JWT spune:

> cine ești.

Household logic spune:

> ce ai voie să vezi/modifici.

---

# 87. Request Identity

FastAPI dependency:

```text
CurrentActor
```

conceptual:

```text
accountId
householdMemberships
permissions
```

---

# 88. No Client-Supplied Ownership

Frontend nu spune:

```text
householdId = some random UUID
```

și backend presupune că are voie.

Authorization se validează pentru fiecare operation.

---

# 89. Privacy Model

Din 06:

```text
PRIVATE
HOUSEHOLD
OWNER_ONLY
```

trebuie implementat pentru date precum:

* weight;
* progress;
* personal targets.

---

# 90. Database Conventions

Toate tabelele:

```text
snake_case
```

Python models:

```text
PascalCase / snake_case fields
```

JSON API:

preferabil:

```text
camelCase
```

pentru TypeScript.

Pydantic aliases pot gestiona mapping-ul.

---

# 91. Primary Keys

Recomand:

```text
UUID
```

pentru domain entities.

---

# 92. Why UUID

* offline/client-generated IDs possible later;
* no sequential data leakage;
* easy distributed generation;
* migration-friendly.

---

# 93. Timestamps

Folosește:

```text
TIMESTAMPTZ
```

pentru momente.

Intern:

> UTC.

---

# 94. Dates Are Different

Meal date:

```text
DATE
```

în timezone-ul household-ului.

Nu transformăm totul în timestamp dacă semantic este o zi.

---

# 95. Money

Nu folosim:

```text
float
```

pentru bani.

Python:

```text
Decimal
```

DB:

```text
NUMERIC
```

---

# 96. Quantities

Pentru:

* grams;
* ml;
* prices;
* nutrients;

preferăm numeric precis în persistence.

Solver-ul poate converti intern unde este necesar.

---

# 97. Nutrition Calculation Numeric Strategy

DB:

```text
Decimal / NUMERIC
```

Domain calculations:

```text
Decimal
```

sau controlled floating-point unde performanța justifică.

---

# 98. Optimization Solver Numeric Strategy

OR-Tools CP-SAT lucrează foarte bine cu integer models.

Prin urmare putem transforma:

```text
42.5 g
```

în:

```text
425 units of 0.1g
```

atunci când intrăm în solver.

---

# 99. Unit Canonicalization

Intern:

```text
mass → grams
liquid → milliliters
energy → kcal
currency → native currency Decimal
```

Nutrienții au propria canonical unit.

---

# 100. Preparation State

Orice food quantity relevantă:

```text
RAW
COOKED
DRAINED
PREPARED
```

conform `04`.

---

# 101. Enums

În Python folosim enums/domain types.

În DB:

putem prefera:

```text
VARCHAR + check constraint
```

pentru enums care pot evolua.

Nu neapărat Postgres ENUM peste tot.

---

# 102. Why

Postgres ENUM migrations pot deveni mai incomode când domain-ul evoluează frecvent.

---

# 103. JSONB

Este util pentru date flexibile.

Exemple bune:

```text
AI raw payload
Recommendation score breakdown
Nutrition snapshot V0
External API raw response
Structured audit context
```

---

# 104. JSONB Should Not Replace Domain Modeling

Nu:

```text
meal = giant JSON document
```

dacă vrem să query-uim:

* members;
* dates;
* recipes;
* portions.

---

# 105. Nutrition Snapshot V0

Pentru Iteration 0 putem păstra:

```text
nutrition_snapshot.values JSONB
```

de forma:

```json
{
  "energy_kcal": 610,
  "protein_g": 48,
  "carbs_g": 63,
  "fat_g": 17
}
```

---

# 106. Future Nutrition Snapshot

Dacă analytics pentru zeci de nutrients necesită:

```text
nutrition_snapshot_values
```

normalizat, putem migra.

---

# 107. Historical Snapshots Are Immutable

FoodLog:

```text
nutrition_snapshot_id
```

nu recalculează istoria după schimbarea product data.

---

# 108. Current Food Data

Separat:

```text
food_nutrient_values
product_nutrient_values
```

care pot fi actualizate.

---

# 109. Database Indexing

Index-uri încă din start pe:

```text
household_id
member_id
date
created_at
status
meal_plan_id
recipe_id
food_id
product_id
barcode
```

în funcție de queries.

---

# 110. Composite Index Examples

```text
(member_id, date)
(household_id, start_date)
(pantry_id, status)
(product_id, observed_at DESC)
```

---

# 111. Don't Index Everything

Indexurile trebuie derivate din query patterns reale.

---

# 112. Database Constraints

Folosește DB constraints pentru invariants simple:

```text
quantity >= 0
price >= 0
valid_until >= valid_from
```

---

# 113. Unique Constraints

Exemplu:

```text
product.barcode
```

unde este valid semantic.

---

# 114. Database Is Last Safety Net

Validation:

```text
UI
↓
Pydantic
↓
Domain Rules
↓
DB Constraints
```

---

# 115. Transactions

Operațiile cross-domain importante trebuie să fie atomice.

---

# 116. Example — Consume Planned Meal

În aceeași tranzacție:

```text
FoodLog
NutritionSnapshot
Meal status
LearningEvent
```

unde este posibil.

---

# 117. Derived State Can Be Recalculated After Commit

Exemplu:

```text
DailyNutritionState cache
```

poate fi recalculat imediat sau invalidat.

---

# 118. Source Of Truth vs Cache

Source:

```text
FoodLogs
Meal Plans
Nutrition Targets
```

Derived:

```text
DailyNutritionState
NutrientGap
```

---

# 119. Derived Tables Can Be Deleted

Teoretic:

```text
DROP derived cache
↓
rebuild
```

fără pierderea truth.

Aceasta este o proprietate bună.

---

# 120. Event Architecture

Nu avem nevoie de Kafka.

Dar domain events sunt utile conceptual.

---

# 121. In-Process Events Initially

Exemplu:

```text
meal_logged
```

declanșează:

```text
invalidate nutrition state
record learning signal
```

în același process.

---

# 122. Domain Event Interface

Definim simplu:

```python
DomainEvent
```

cu:

```text
type
entityId
occurredAt
payload
```

---

# 123. No Infrastructure Event Bus Initially

Event dispatcher poate fi pur Python.

---

# 124. When Async Side Effects Arrive

Exemple:

```text
notifications
receipt processing
AI scan
```

atunci putem introduce:

> **Transactional Outbox**

---

# 125. Transactional Outbox

Flow:

```text
DB transaction
├── domain change
└── outbox event
      ↓
worker publishes/processes
```

Astfel evităm:

```text
DB committed
but queue publish failed
```

---

# 126. Background Jobs

FastAPI oferă in-process Background Tasks.

Acestea sunt potrivite pentru:

* foarte mici side effects;
* non-critical work.

---

# 127. Do Not Use In-Process Background Tasks For Critical Heavy Work

Nu pentru:

```text
receipt processing
weekly solver lasting seconds
vision analysis
```

pentru că request process-ul poate muri.

---

# 128. Durable Job Architecture Later

```text
FastAPI
  ↓
Job Queue
  ↓
Worker
```

---

# 129. Do Not Pick Job Infrastructure In V0

Definim:

```text
JobDispatcher
```

interface.

Implementarea poate fi adăugată în Iteration 7.

---

# 130. Potential Future Job Stack

Un simplu:

```text
Redis-backed queue
+
Python worker
```

este suficient.

Nu Kafka.

---

# 131. Job Entity

Conform `05/06`:

```text
AIJob
```

ține application state:

```text
PENDING
PROCESSING
READY_FOR_REVIEW
FAILED
```

independent de queue technology.

---

# 132. This Decouples UX From Queue

Frontend nu știe dacă folosim:

```text
Celery
Dramatiq
custom worker
```

Vede doar:

```text
AIJob.status
```

---

# 133. Realtime Updates

În V0:

nu avem nevoie.

---

# 134. Later Realtime Uses

* receipt finished;
* partner checked shopping item;
* meal plan updated;
* AI scan completed.

---

# 135. Initial Strategy

Polling / query invalidation poate fi suficient.

---

# 136. Add Realtime Only When User Value Exists

Poate fi:

```text
Server-Sent Events
WebSocket
Supabase Realtime
```

ulterior.

FastAPI suportă inclusiv WebSockets și SSE-related patterns prin ecosistemul Starlette/FastAPI.

---

# 137. Caching Strategy

Nu Redis din prima zi.

---

# 138. Layer 1 — Browser Query Cache

TanStack Query.

---

# 139. Layer 2 — Database Derived Cache

Exemple:

```text
RecipeNutritionCache
DailyNutritionState
```

---

# 140. Layer 3 — In-Process Cache

Pentru:

```text
static reference rules
nutrient taxonomy
```

unde este sigur.

---

# 141. Redis Only When Necessary

Potential triggers:

* multiple workers;
* rate limiting;
* durable queue;
* expensive hot cache;
* distributed locks.

---

# 142. Cache Invalidation

Trebuie explicită.

Exemplu:

```text
FoodLog created
↓
invalidate DailyNutritionState(member,date)
↓
invalidate NutrientGap(member,date)
```

---

# 143. Do Not Cache Source Of Truth Aggressively

Mai bine corect și puțin mai lent decât stale nutrition.

---

# 144. Food Catalog Architecture

Trebuie să existe:

```text
FoodRepository
ProductRepository
```

independent de provider.

---

# 145. External Nutrition Provider Interface

```text
NutritionDataProvider
```

implementări:

```text
OpenFoodFactsProvider
GenericFoodProvider
ManualProvider
```

---

# 146. Provider Result

Normalized într-un:

```text
ExternalFoodCandidate
```

înainte să intre în domain.

---

# 147. Never Leak External Schema Into Domain

Nu vrem:

```text
open_food_facts_field_x
```

în Recipe Engine.

---

# 148. Adapter Layer

```text
External API
↓
Adapter
↓
Canonical Food/Product
```

---

# 149. External API Responses

Pot fi cached/persisted pentru:

* provenance;
* debugging;
* rate limits.

---

# 150. Product Resolution Service

```text
barcode
↓
local product DB
↓
provider
↓
normalize
↓
store locally
```

---

# 151. Local-First Product Catalog

După ce un product a fost scanat:

următoarea scanare trebuie să folosească local DB.

---

# 152. Nutrition Engine Architecture

```text
modules/nutrition/engine/

├── units.py
├── vectors.py
├── calculation.py
├── targets.py
├── daily_state.py
├── gaps.py
├── references.py
├── confidence.py
└── validators.py
```

---

# 153. Nutrition Engine Must Have Zero Database Queries

Inputurile sunt trimise de application layer.

---

# 154. Nutrition Vector

Canonical Python object:

```text
NutritionVector
```

cu operations:

```text
add
subtract
scale
coverage
```

---

# 155. Nutrient Registry

Nu hardcodăm:

```python
if nutrient == "vitamin_c"
```

peste tot.

Există:

```text
NutrientRegistry
```

---

# 156. Nutrition Reference Provider

Conform `04`:

```text
NutritionReferenceProvider
```

permite:

```text
EU / EFSA
Custom
Professional
```

---

# 157. Portion Solver Architecture

```text
modules/planning/solvers/portion/
```

---

# 158. V1 Portion Solver

Plain Python:

```text
bounded search
recipe ratios
macro fit
```

---

# 159. Solver Contract

Input:

```text
PortionProblem
```

Output:

```text
PortionSolution
```

---

# 160. Never Bind API Directly To Solver Internals

API primește:

```text
MealRecommendation
```

nu:

```text
optimization_variable_42
```

---

# 161. Recommendation Engine

Architecture:

```text
eligibility
↓
candidate generation
↓
feature calculation
↓
scoring
↓
diversification
↓
portion solving
↓
validation
```

---

# 162. Feature Vector

Pentru recipe candidate:

```text
nutrition_fit
preference_fit
pantry_fit
cost_fit
time_fit
variety_fit
```

---

# 163. Scoring Config

Ponderile trebuie să fie data/config:

```text
RecommendationStrategy
```

nu:

```python
nutrition * 0.35
```

repetat prin codebase.

---

# 164. Strategy Version

```text
balanced_v1
save_money_v1
```

permite reproducibilitate.

---

# 165. Weekly Planner Evolution

## Iteration 3

```text
heuristic planner
```

---

# 166. Heuristic Planner

```text
lock fixed slots
↓
find shared meals
↓
rank recipes
↓
fill slots
↓
validate days
↓
repair
```

---

# 167. Iteration 4–6

Adăugăm:

```text
meal prep heuristics
ingredient reuse
pantry
budget
```

---

# 168. Advanced Optimization

Abia când problema devine suficient de bine înțeleasă:

```text
OR-Tools
```

Google OR-Tools oferă în Python solvers pentru linear, mixed-integer și constraint optimization, inclusiv scheduling problems.

---

# 169. Solver Adapter

```text
WeeklyPlanner
    ↓
OptimizationBackend
```

implementări:

```text
HeuristicPlanner
ORToolsPlanner
```

---

# 170. This Prevents Rewrite

API și application layer nu știu care solver rulează.

---

# 171. AI Architecture

AI-ul este adapter/orchestration layer.

Nu domain truth.

---

# 172. AI Provider Interface

```text
LLMProvider
VisionProvider
EmbeddingProvider
```

---

# 173. No OpenAI/Anthropic-Specific Types In Domain

Greșit:

```text
OpenAIResponse
```

în Recommendation Engine.

---

# 174. Correct

```text
IntentParseResult
MealVisionResult
ReceiptParseResult
```

---

# 175. AI Router

```text
task
↓
AIModelRouter
↓
provider/model
```

---

# 176. AI Prompt Registry

```text
ai/prompts/
```

cu:

```text
intent_parser_v1
meal_vision_v1
receipt_parser_v1
```

---

# 177. Prompts Are Versioned Assets

Orice prompt change:

* PR;
* tests;
* version increment.

---

# 178. Structured AI Output

Pydantic validează output-ul.

```text
LLM JSON
↓
Pydantic
↓
valid structured result
```

---

# 179. Invalid Output

Retry logic poate:

1. repair/retry;
2. alternate model;
3. fail safely.

---

# 180. AI Never Writes Database Directly

```text
AI
↓
structured action
↓
Application Service
↓
Domain validation
↓
DB
```

---

# 181. Vision Architecture

```text
Asset Upload
↓
AIJob
↓
Worker
↓
VisionProvider
↓
Structured Result
↓
Review
↓
Confirmation
↓
Domain Mutation
```

---

# 182. File Storage

Introducem când apare prima imagine.

Recomand:

```text
S3-compatible object storage
```

Supabase Storage este o opțiune naturală dacă folosim deja Supabase.

---

# 183. Files Are Not Stored In Postgres

DB păstrează:

```text
asset_id
storage_key
metadata
```

Nu image blob.

---

# 184. Signed URLs

Imaginile private sunt accesate prin:

```text
temporary signed URLs
```

sau backend-authorized access.

---

# 185. Progress Photos Need Stronger Privacy

Storage bucket separat / policy mai restrictiv.

---

# 186. Asset Retention

Diferite tipuri pot avea reguli diferite:

```text
receipt
meal scan
scale
progress photo
```

---

# 187. AI Image Retention

După confirmarea structured data:

raw image poate eventual fi ștearsă conform product/privacy settings.

---

# 188. Deployment Architecture — Initial

Recomand o variantă foarte simplă:

```text
Next.js → Vercel

FastAPI → managed container platform

Postgres/Auth → Supabase

Object Storage → Supabase later
```

---

# 189. Why Separate Web And API Deployments

Next.js este optimizat pentru platforma web.

FastAPI are:

* long-lived Python process;
* solver;
* worker evolution;

și merită propriul runtime.

---

# 190. API Deployment Requirement

Platforma trebuie să suporte:

```text
Docker
persistent service
environment variables
health check
autoscaling optional
```

---

# 191. Do Not Use Vercel Python Functions As Core Backend

Ar limita/controla inutil:

* long-running planning;
* worker architecture;
* AI processing;
* optimization.

FastAPI trebuie tratat ca service real.

---

# 192. Reference Production Topology

```text
             ┌──────────────┐
             │   Vercel     │
             │   Next.js    │
             └──────┬───────┘
                    │
               api.domain
                    │
             ┌──────▼───────┐
             │ FastAPI API  │
             │  Container   │
             └──────┬───────┘
                    │
             ┌──────▼───────┐
             │  Supabase    │
             │ PostgreSQL   │
             │ Auth         │
             └──────────────┘
```

---

# 193. Future Topology

```text
                    FastAPI
                 ┌────┴─────┐
                 │          │
             PostgreSQL   Redis
                             │
                          Worker(s)
                             │
                      AI / OR-Tools
```

---

# 194. Local Development

Docker Compose pentru:

```text
Postgres
optional Redis later
```

Frontend/API pot rula nativ pentru rapid dev.

---

# 195. Development Commands

Root commands:

```text
make dev
make test
make lint
make migrate
make api-client
```

sau echivalent prin `just`.

---

# 196. One Command To Start

Developer experience trebuie să fie:

```bash
make dev
```

nu 12 terminale configurate manual.

---

# 197. Environment Configuration

```text
.env.example
```

fără secrets reale.

---

# 198. Environment Separation

```text
local
preview
staging
production
```

---

# 199. Preview Environments

Frontend PR:

Vercel preview.

Backend/database preview poate fi adăugat ulterior.

Nu este obligatoriu în V0.

---

# 200. Secrets

Gestionate prin deployment provider.

Nu:

```text
.env committed
```

---

# 201. API URLs

Frontend folosește:

```text
NEXT_PUBLIC_API_URL
```

doar pentru public API endpoint.

Secrets nu trebuie să aibă prefix public.

---

# 202. CORS

FastAPI acceptă doar origins cunoscute:

```text
production web
preview rules
localhost
```

Nu:

```text
*
```

în production cu credentials.

---

# 203. Security Headers

Frontend/API trebuie să includă:

* HTTPS only;
* appropriate CSP later;
* HSTS production;
* secure cookies where applicable.

---

# 204. Rate Limiting

Nu este critic în V0 pentru internal users.

Devine important pentru:

```text
public auth
AI endpoints
scan endpoints
```

---

# 205. AI Abuse Protection

Limite:

```text
uploads/hour
AI calls/day
image size
request size
```

---

# 206. Upload Validation

Verificăm:

```text
mime type
file signature
size
dimensions
```

nu doar filename.

---

# 207. Authorization Tests

Trebuie teste pentru:

```text
Member A cannot access unrelated household
```

Acestea sunt security-critical.

---

# 208. Household Query Rule

Orice repository query user-owned trebuie să fie scoped.

Exemplu:

```text
WHERE household_id = authorized_household
```

---

# 209. Avoid IDOR

Nu:

```text
GET /meals/{uuid}
```

și returnare fără ownership validation.

---

# 210. Audit Logging

Pentru:

```text
target changes
member changes
AI mutations
receipt confirmations
```

păstrăm audit.

---

# 211. Application Logging

Structured JSON logs.

Fields:

```text
timestamp
level
request_id
account_id? hashed/internal
route
duration
error_code
```

---

# 212. Request ID

Fiecare request primește:

```text
requestId
```

returnat inclusiv în error responses.

---

# 213. Observability V0

Minimum:

```text
structured logs
error reporting
health endpoint
```

---

# 214. Error Monitoring

Recomand ceva precum:

```text
Sentry
```

pentru frontend + backend.

---

# 215. Metrics Later

Când produsul crește:

```text
request latency
DB query latency
recommendation latency
solver latency
AI latency
AI cost
```

---

# 216. OpenTelemetry Later

Nu obligatoriu în V0.

Dar architecture nu trebuie să-l împiedice.

---

# 217. Health Endpoint

```text
GET /health/live
GET /health/ready
```

---

# 218. Readiness

Verifică:

```text
DB connectivity
```

Nu face expensive external AI check.

---

# 219. Testing Strategy

Testing este parte din arhitectură.

---

# 220. Backend Unit Tests

`pytest`.

Pentru:

```text
Nutrition Calculation Engine
Daily Nutrition State
Portion Solver
Recommendation scoring
```

---

# 221. Engine Tests Must Be Fast

Nu DB.

Nu HTTP.

---

# 222. Example

```text
given:
100g food × nutrient values

expect:
nutrition vector
```

---

# 223. Application Integration Tests

Testează:

```text
SQLAlchemy
Postgres
Service
```

---

# 224. API Tests

FastAPI test client / HTTPX.

FastAPI include tooling de testing prin Starlette/HTTPX ecosystem.

---

# 225. Use Real Postgres In Integration Tests

Nu SQLite.

Pentru că production este PostgreSQL.

---

# 226. Why

SQL behaviour diferă în:

* JSONB;
* constraints;
* transactions;
* date/time;
* SQL features.

---

# 227. Test Database

CI poate porni:

```text
Postgres container
```

---

# 228. Frontend Unit Tests

```text
Vitest
React Testing Library
```

pentru logică/component behavior.

---

# 229. E2E

```text
Playwright
```

pentru critical UX flows.

---

# 230. Iteration 0 E2E

```text
login
↓
Today
↓
consume coach meal
↓
macro totals update
↓
refresh
↓
state persists
```

---

# 231. Iteration 1 E2E

```text
two members
↓
shared recipe
↓
different portions
↓
consume
```

---

# 232. Iteration 2 E2E

```text
breakfast logged
↓
recommend lunch
↓
accept
↓
state changes
```

---

# 233. Contract Tests

Generated TypeScript client trebuie să compileze against OpenAPI.

---

# 234. Migration Tests

CI:

```text
empty database
↓
alembic upgrade head
```

trebuie să funcționeze.

---

# 235. Upgrade Tests Later

Pentru production safety:

```text
previous schema
↓
new migrations
```

---

# 236. AI Testing

Conform `05`:

fixtures versionate.

---

# 237. AI Tests Do Not Run Live Provider For Every CI

Use:

```text
recorded fixtures
structured mocked results
```

plus periodic eval suite live.

---

# 238. Solver Regression Tests

Dataset:

```text
household scenarios
```

cu expected invariants.

Nu neapărat exact same recipe.

---

# 239. Invariant-Based Tests

Exemplu:

```text
No allergic ingredient
Budget <= hard limit
Portion >= minimum
```

---

# 240. Property-Based Testing

Foarte util ulterior pentru nutrition/solver.

Exemplu:

> scaling a recipe by 2 should double nutrients.

Python `hypothesis` poate fi considerat.

---

# 241. Code Quality Gates

Backend:

```text
ruff check
ruff format --check
pyright
pytest
```

---

# 242. Frontend

```text
typecheck
lint
unit tests
build
```

---

# 243. CI Pipeline

```text
Install
↓
Lint
↓
Typecheck
↓
Backend tests
↓
Frontend tests
↓
Migration test
↓
Generate OpenAPI client
↓
Build web
↓
Build API image
```

---

# 244. Deployment Pipeline

Main:

```text
CI success
↓
DB migrations
↓
API deploy
↓
web deploy
```

cu backward-compatible API changes pe cât posibil.

---

# 245. Zero-Downtime Migration Philosophy

Prefer:

```text
expand
↓
deploy code
↓
migrate data
↓
contract later
```

pentru breaking schema changes.

---

# 246. Example

Nu:

```text
rename column instantly
```

Prefer:

```text
add new column
write both
migrate
read new
remove old later
```

când production usage justifică.

---

# 247. API Backward Compatibility

Pentru propriul frontend putem evolua rapid.

Dar mobile/PWA cached clients pot rămâne pe versiune veche temporar.

Prin urmare evităm breaking responses gratuit.

---

# 248. Feature Flags

Backend:

```text
FeatureFlags
```

simple.

---

# 249. Use Feature Flags For Deployment, Not Product Debt

Feature:

```text
meal_recommendations
```

rămâne hidden până complet.

---

# 250. Feature Flag Is Not Permission

Authorization rămâne separat.

---

# 251. Iteration-Based Technical Growth

## Iteration 0

```text
Next.js
FastAPI
Postgres
Auth
Nutrition Calculation
Food Logs
Weight Logs
```

---

# 252. No Redis

No AI.

No Worker.

No OR-Tools.

No vector DB.

---

# 253. Iteration 1

Add:

```text
Target Engine
Portion Solver
Preferences
Household portions
```

Plain Python.

---

# 254. Iteration 2

Add:

```text
DailyNutritionState
NutrientGap
Recommendation Engine
Recommendation Context
```

Still single API service.

---

# 255. Iteration 3

Add:

```text
Planner
Schedule
Meal Plan
```

Heuristic algorithm.

---

# 256. Iteration 4

Add:

```text
CookingBatch
MealPrep
Containers
Leftovers
```

---

# 257. Iteration 5

Add:

```text
Product
PriceObservation
Shopping
Budget
```

---

# 258. Iteration 6

Add:

```text
Pantry
PantryTransactions
Expiry
```

---

# 259. Iteration 7.1

Add:

```text
Barcode provider integration
```

Still synchronous if fast enough.

---

# 260. Iteration 7.2+

As soon as vision arrives:

```text
Object Storage
AIJobs
```

---

# 261. When First Heavy Vision Flow Arrives

Introduce:

```text
durable Job Queue
worker
```

---

# 262. Iteration 8

Add:

```text
AI Orchestrator
LLM adapters
structured intents
```

Existing services remain unchanged.

---

# 263. Iteration 9

Add:

```text
LearningEvent
LearnedAffinity
```

---

# 264. No ML Model Required

Learning can initially use deterministic weighted statistics.

---

# 265. Iteration 10

Add:

```text
trend engine
target adjustment proposals
```

---

# 266. Iteration 11

Potentially introduce:

```text
OR-Tools
```

through existing Planner interface.

---

# 267. No Architectural Rewrite

Desired evolution:

```text
HeuristicPlanner
       ↓ replaced internally by
ORToolsPlanner
```

not:

```text
rewrite Plan API
rewrite UI
rewrite DB
```

---

# 268. AI Provider Evolution

Same:

```text
VisionProviderA
↓
VisionProviderB
```

without domain rewrite.

---

# 269. Nutrition Data Provider Evolution

```text
OpenFoodFacts
GenericFoodDB
Custom Product
```

all normalize into canonical catalog.

---

# 270. External Integration Boundary

Every external system gets:

```text
adapter
```

Examples:

```text
SupabaseAuthAdapter
OpenFoodFactsAdapter
AIProviderAdapter
ObjectStorageAdapter
```

---

# 271. Dependency Inversion

Core domain nu știe:

```text
Supabase
OpenAI
OpenFoodFacts
Vercel
```

---

# 272. Configuration

Pydantic Settings / environment-based config.

```text
DatabaseSettings
AuthSettings
AISettings
StorageSettings
```

---

# 273. Environment-Specific Behavior

Nu:

```python
if os.getenv("PRODUCTION"):
    weird logic
```

împrăștiat.

Config centralizat.

---

# 274. Timezone Architecture

Household are:

```text
timezone
```

---

# 275. Store Timestamps In UTC

Dar:

```text
today
meal date
week boundaries
```

se calculează în household timezone.

---

# 276. Do Not Use Server Timezone For Nutrition Day

Server poate fi UTC.

Household poate fi Europe/Bucharest.

---

# 277. Date Boundary Example

23:30 Romania:

food log aparține zilei locale corecte.

---

# 278. Currency

Household:

```text
RON
```

dar currency este field.

---

# 279. Locale

UI:

```text
ro-RO
```

dar domain labels canonical.

---

# 280. Localization

Nu hardcodăm texte user-facing în domain/backend.

Frontend i18n layer.

---

# 281. Food Localization

Canonical:

```text
greek_yogurt
```

aliases:

```text
iaurt grecesc
Greek yogurt
```

---

# 282. API Does Not Need Translated Food Name Only

Poate returna:

```text
canonicalName
localizedDisplayName
```

prin catalog/read model.

---

# 283. Search Architecture

V0:

Postgres search:

```text
ILIKE
trigram
aliases
```

---

# 284. Do Not Add Elasticsearch

Nu este necesar.

---

# 285. PostgreSQL Search Can Handle Large Initial Catalog

Add:

```text
pg_trgm
```

if needed.

---

# 286. Embeddings Later

Doar pentru:

* semantic recipe similarity;
* substitution;
* AI search;

dacă keyword/search nu este suficient.

---

# 287. Vector DB

Nu introduce external vector DB by default.

Dacă apare nevoie:

```text
pgvector
```

poate fi evaluat mai întâi.

---

# 288. Analytics Architecture

Product analytics:

```text
meal_logged
recommendation_accepted
plan_generated
shopping_completed
```

---

# 289. Analytics Is Not Domain Truth

Analytics pipeline separat.

Nu query Mixpanel pentru:

> user preference.

---

# 290. Domain Events Can Feed Analytics

```text
meal.logged
↓
analytics adapter
```

---

# 291. Analytics Provider Abstraction

Poate fi:

```text
PostHog
Amplitude
etc.
```

fără coupling în domain.

---

# 292. Privacy

Health-adjacent data trebuie tratată conservator.

---

# 293. Collect Minimum Necessary Data

Nu cerem:

* CNP;
* address;
* alte date fără nevoie.

---

# 294. User Data Export/Delete

Architecture trebuie să permită:

```text
export member history
delete member/account
```

---

# 295. Cascading Delete Must Be Deliberate

Nu:

```text
ON DELETE CASCADE EVERYTHING
```

fără analiză.

---

# 296. Household Removal

Trebuie business operation explicită.

---

# 297. Backups

Managed Postgres cu:

```text
automated backups
point-in-time recovery
```

când intrăm în production real.

---

# 298. Database Restore Must Be Tested

Backup care nu a fost testat:

nu este backup de încredere.

---

# 299. Seed Architecture

Seed categories:

```text
system nutrients
reference rules
coach foods
coach recipes
```

---

# 300. Static Reference Seeds Are Versioned

Exemplu:

```text
nutrition_reference_rules_v1.json
```

---

# 301. No Manual Production DB Editing

Changes prin:

```text
admin tools
migration
seed
application action
```

nu random SQL din dashboard, except emergency.

---

# 302. Admin/Internal Tools

Mai târziu putem avea:

```text
/admin
```

pentru:

* food correction;
* recipe validation;
* AI result debugging;
* product merge.

Nu este user UI.

---

# 303. Admin Is Not Public Role Initially

Internal-only.

---

# 304. Recipe Content Management

System recipes pot fi gestionate prin:

```text
seed files
admin tool later
```

Nu avem nevoie de CMS din V0.

---

# 305. Recipe Validation Pipeline

```text
draft
↓
nutrition calculated
↓
constraints validated
↓
active
```

---

# 306. CI For Seed Data

Validate:

* referenced Food exists;
* quantities valid;
* recipe nutrition calculable.

---

# 307. Performance Targets

Nu trebuie premature exact values.

Dar critical requests:

```text
Today
Meal detail
Shopping list
```

trebuie să fie fast.

---

# 308. Heavy Planner Can Be Slower

Weekly generation este user-initiated heavy operation.

Poate avea separat latency budget.

---

# 309. Avoid N+1 Queries

Especially:

```text
MealPlan
→ Meals
→ Portions
→ Ingredients
```

Use explicit eager loading/read queries.

---

# 310. Read Optimized Queries

Nu trebuie să folosim ORM entity graph naiv pentru fiecare view.

SQLAlchemy Core/select poate construi read models eficient.

---

# 311. Explain Analyze

Folosit când queries devin lente.

Nu guess performance.

---

# 312. Database Connection Pool

Manage via SQLAlchemy engine.

Pool size adaptat platformei/Postgres limits.

---

# 313. Serverless Connection Explosion Avoided

Un motiv în plus pentru persistent FastAPI service.

---

# 314. API Horizontal Scaling

FastAPI instances trebuie să fie stateless.

---

# 315. No Important Session State In Memory

User state în:

```text
Postgres
```

cache optional.

---

# 316. This Enables

```text
1 API instance
↓
N API instances
```

fără rewrite.

---

# 317. Worker Scaling

Likewise:

```text
1 worker
↓
N workers
```

prin queue.

---

# 318. Idempotency

Critical commands suportă:

```text
Idempotency-Key
```

---

# 319. Important For Mobile

Double tap / network retry nu trebuie să creeze:

```text
2 FoodLogs
2 Receipts
```

---

# 320. Optimistic UI + Idempotent Backend

Foarte bună combinație pentru UX rapid.

---

# 321. Concurrency

Shopping list este primul loc unde household concurrency devine importantă.

---

# 322. Optimistic Concurrency

Entity `version` / updated timestamp poate detecta conflicts.

---

# 323. Shopping Item Check

Poate fi atomic:

```text
UPDATE ... WHERE id ...
```

---

# 324. Pantry Conflicts

Pantry updates ar trebui preferabil exprimate prin:

```text
PantryTransaction
```

nu overwrite cantitate arbitrar.

---

# 325. Why Event-Like Inventory Is Useful

Două devices:

```text
consume 100g
purchase 500g
```

pot fi reconciliate mai bine ca deltas.

---

# 326. Source Of Truth Map

## Auth

```text
Supabase Auth
```

## Household/member

```text
Postgres app DB
```

## Nutrition values

```text
canonical catalog + snapshots
```

## Consumed food

```text
FoodLog
```

## Planned food

```text
MealPlan / Meal
```

## Pantry

```text
PantryItem + transactions
```

## Prices

```text
PriceObservation
```

## Preferences

```text
explicit Preferences + LearnedAffinity
```

---

# 327. Never Have Two Authorities

Example:

Nu avem:

```text
calorie target in user profile
AND
calorie target in settings table
```

cu valori diferite.

---

# 328. Documentation Architecture

Repo trebuie să păstreze documentele create:

```text
docs/product/
00-PRODUCT-VISION.md
01-USER-MODEL-AND-USE-CASES.md
02-MODULE-MAP.md
03-CORE-ENGINES.md
04-NUTRITION-SPEC.md
05-AI-SPEC.md
06-DOMAIN-MODEL.md
07-ROADMAP.md
08-UX-FLOWS.md
09-TECHNICAL-ARCHITECTURE.md
```

---

# 329. Architecture Decision Records

Pentru decizii importante:

```text
docs/adr/
```

---

# 330. Example ADRs

```text
ADR-001 Modular Monolith
ADR-002 Python FastAPI Backend
ADR-003 PostgreSQL
ADR-004 Frontend Does Not Access DB Directly
ADR-005 Household Is First-Class
ADR-006 Nutrition Snapshots Immutable
```

---

# 331. Why ADR

Peste un an putem înțelege:

> de ce am făcut alegerea.

Nu doar:

> așa era în cod.

---

# 332. Architecture Guardrails

Aceste reguli trebuie considerate locked.

### Guardrail 1

```text
Frontend never calculates canonical nutrition.
```

### Guardrail 2

```text
Frontend never accesses app database directly.
```

### Guardrail 3

```text
AI never writes directly to database.
```

### Guardrail 4

```text
Engines never query database.
```

### Guardrail 5

```text
Historical nutrition logs are snapshots.
```

### Guardrail 6

```text
Module persistence is not another module's public API.
```

### Guardrail 7

```text
External provider data is normalized before domain use.
```

### Guardrail 8

```text
Derived state can always be rebuilt.
```

### Guardrail 9

```text
Every persistent mutation passes authorization + domain validation.
```

### Guardrail 10

```text
New infrastructure is introduced only when a shipped feature needs it.
```

---

# 333. Things Explicitly NOT In V0

```text
Redis
Celery
Kafka
Kubernetes
GraphQL
Elasticsearch
Vector database
OR-Tools
LLM
Vision
WebSockets
Event sourcing framework
Microservices
CQRS framework
```

---

# 334. But Architecture Supports Them Where Appropriate

Example:

```text
Planner interface
```

permite OR-Tools.

```text
JobDispatcher
```

permite queue.

```text
AIProvider
```

permite LLM.

```text
DomainEvents
```

permit async side effects.

---

# 335. Iteration 0 Deployment

Minimum viable production architecture:

```text
Vercel
   │
Next.js
   │
HTTPS
   ▼
FastAPI container
   │
   ▼
PostgreSQL
```

plus auth.

---

# 336. Iteration 0 Backend Modules

Doar:

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

# 337. Iteration 0 Database Tables

Strictly needed subset:

```text
accounts
households
household_memberships
members

nutrition_target_profiles
nutrient_targets

foods
food_nutrient_values

recipes
recipe_ingredients

food_logs
food_log_items
nutrition_snapshots

weight_logs
```

plus meal-plan structure minimă dacă planul antrenorului este modelat ca plan.

---

# 338. Coach Plan Should Be Real Domain Data

Nu:

```python
COACH_MEALS = {...}
```

hardcoded în frontend.

---

# 339. Seed It Through Domain

Planul antrenorului devine:

```text
Recipes
+
Meal template / simple plan
```

---

# 340. Why

Iteration 1 folosește aceleași Recipes pentru portion solver.

Iteration 2 le folosește pentru recommendation.

Nu aruncăm nimic.

---

# 341. V0 Nutrition Target

Manual.

Dar stocat ca:

```text
NutritionTargetProfile
mode = USER_CONFIGURED
```

---

# 342. V1 Auto Target

Adaugă:

```text
mode = SYSTEM_ESTIMATED
```

fără schema rewrite.

---

# 343. V0 Food Log

Deja include:

```text
NutritionSnapshot
```

astfel Iteration 2 poate reconstrui DailyNutritionState istoric.

---

# 344. V0 Weight Logs

Deja timestamps/source.

Iteration 10 poate calcula trends peste aceleași date.

---

# 345. Build For Data Compounding

Aceasta este o regulă majoră:

> **Datele create astăzi trebuie să devină inteligența de mâine.**

---

# 346. Architecture Success Criterion — Iteration 0

Trebuie să putem implementa Iteration 0 fără:

* future services;
* future tables inutile;
* future AI dependencies.

---

# 347. Architecture Success Criterion — Iteration 1

Trebuie să putem adăuga Portion Solver prin:

```text
new module logic
+
a few new fields/tables
```

nu rewrite.

---

# 348. Iteration 2

Recommendation Engine trebuie să consume date existente.

---

# 349. Iteration 3

Meal Plan devine richer, dar Today continuă să folosească aceeași API philosophy.

---

# 350. Iteration 7

Vision trebuie să se conecteze prin:

```text
AIJob
↓
confirmed domain command
```

fără bypass.

---

# 351. Iteration 11

OR-Tools trebuie să poată înlocui planning backend fără schimbarea UX-ului.

---

# 352. Developer Experience North Star

Un developer trebuie să poată răspunde rapid:

> Unde implementez feature-ul X?

Exemplu:

> „protein gap calculation”

→

```text
modules/nutrition
```

---

# 353. Bad Architecture Signal

Dacă feature necesită modificări în:

```text
8 modules
12 repositories
frontend utilities
database triggers
```

pentru logică simplă:

boundaries sunt greșite.

---

# 354. Database Trigger Policy

Folosește triggers foarte puțin.

Business logic trebuie să rămână vizibilă în Python.

---

# 355. Good DB Trigger Use

Potential:

* generic updated_at;
* auth integration if required.

Nu:

```text
meal logged → calculate nutrition
```

în Postgres trigger.

---

# 356. Why

Nutrition logic trebuie:

* versionată;
* testată;
* rulată în simulations;
* shared cu solver.

Python este locul corect.

---

# 357. Stored Procedures

Similar:

nu pentru core domain by default.

---

# 358. Postgres Is Persistence Engine

Nu trebuie să devină al doilea application runtime.

---

# 359. Data Access Patterns

Commands:

```text
load only required aggregate
```

Queries:

```text
optimized read projections
```

---

# 360. Lightweight CQRS Concept

Fără framework.

Doar conceptual:

```text
WRITE MODEL
≠
READ MODEL
```

---

# 361. Example

Write:

```text
Meal + Portion + FoodLog
```

Read:

```text
TodayView
```

---

# 362. API Pagination

Pentru history:

```text
cursor pagination
```

preferabil în data sets mari.

---

# 363. Today Does Not Need Pagination

Domain-specific endpoints.

---

# 364. Rate / Numerical Units In API

Prefer:

```json
{
  "value": 180,
  "unit": "g"
}
```

unde unitatea este semantic relevantă.

---

# 365. Nutrition API

Poate returna normalized presentation:

```json
{
  "energyKcal": 610,
  "proteinG": 48,
  "carbsG": 63
}
```

pentru common macros.

Extended nutrients pot avea generic list structure.

---

# 366. Client Does Not Do Unit Conversion For Truth

Backend returnează canonical/presentation-ready values.

Client poate doar schimba afișarea.

---

# 367. AI Cost Tracking

Când apare AI:

fiecare AIJob poate salva:

```text
provider
model
input units
output units
estimated cost
latency
```

---

# 368. Why

Trebuie să știm:

> cât costă efectiv fiecare feature.

---

# 369. Solver Observability

RecommendationRecord:

```text
candidate
score
score breakdown
engine version
```

---

# 370. Developer Debug Mode

Internal only:

```text
Why was this recommended?
```

poate arăta:

```text
Nutrition 0.91
Preference 0.84
Pantry 0.77
```

---

# 371. Production User Does Not See It By Default

Conform UX Spec.

---

# 372. Engine Versioning

Configuration:

```text
nutrition_engine_v1
recommendation_engine_v1
portion_solver_v1
planner_v1
```

---

# 373. Why Versioning

Dacă recommendation quality scade după deployment:

putem compara.

---

# 374. Reproducibility

RecommendationRecord păstrează:

```text
engineVersion
strategyVersion
```

plus enough context.

---

# 375. Feature Analytics Version

Event poate include:

```text
recommendationEngineVersion
```

pentru A/B analysis ulterior.

---

# 376. No A/B Testing Infrastructure Early

Dar event model permite ulterior.

---

# 377. Failure Philosophy

Sistemul trebuie să fail:

> **safe and explainable.**

---

# 378. Recommendation Engine Failure

Return:

```text
NO_FEASIBLE_SOLUTION
```

nu arbitrary meal.

---

# 379. Nutrition Provider Failure

Folosește:

```text
existing cached data
```

sau:

> product unavailable.

Nu inventează.

---

# 380. AI Provider Failure

Manual flow remains.

---

# 381. Worker Failure

AIJob:

```text
FAILED
```

și poate fi retried.

---

# 382. Database Failure

Request fails.

Nu pretend success optimistically forever.

Frontend rollback optimistic action.

---

# 383. Data Correction

First-class operations:

```text
correct_food_log
correct_weight
correct_pantry_quantity
```

nu raw DB edits.

---

# 384. Corrections Preserve Audit

Important pentru learning.

Dacă AI a greșit:

trebuie să putem ști.

---

# 385. Architecture Anti-Patterns

Trebuie evitate:

### 1.

Business logic în React.

### 2.

Business logic în FastAPI route handlers.

### 3.

One giant `services.py`.

### 4.

One giant `models.py` pentru toată aplicația.

### 5.

Frontend direct la Supabase tables.

### 6.

AI direct la DB.

### 7.

External product schema used directly.

### 8.

Micronutrients ca JSON fără provenance forever.

### 9.

SQL triggers pentru nutrition.

### 10.

Redis pentru „future scale”.

### 11.

Microservices pentru module care încă nu există.

### 12.

Vector DB pentru 30 rețete.

### 13.

OR-Tools înainte să înțelegem planning problem.

### 14.

Async everything without transaction reasoning.

### 15.

Duplicate business types în Python și TypeScript manually.

---

# 386. Architecture Decision Summary

```text
FRONTEND
Next.js + React + TypeScript

UI
Tailwind + shadcn/ui

SERVER STATE
TanStack Query

BACKEND
Python + FastAPI + Pydantic

ORM
SQLAlchemy 2

MIGRATIONS
Alembic

DATABASE
PostgreSQL

AUTH
Supabase Auth recommended

CONTRACT
OpenAPI → generated TypeScript client

PYTHON TOOLING
uv + Ruff + Pyright + pytest

ARCHITECTURE
Modular Monolith

PLANNING V1
Python heuristics

PLANNING ADVANCED
OR-Tools if justified

AI
Provider abstraction

FILES
S3-compatible storage when needed

ASYNC WORK
Durable queue + Python worker when needed

CACHE
No Redis initially
```

---

# 387. Core Technical Principle

Arhitectura trebuie să permită ca:

```text
V0
manual tracking
```

și:

```text
V10
AI + vision + optimization + learning
```

să fie aceeași aplicație.

Nu două generații complet diferite de cod.

---

# 388. Ultimate Technical Flow

În versiunea matură:

```text
USER
"Ce mâncăm diseară?"
        ↓
Next.js
        ↓
FastAPI
        ↓
AI Intent Interpreter
        ↓
RecommendationContextBuilder
        │
        ├── Household
        ├── Nutrition
        ├── Preferences
        ├── Pantry
        ├── Budget
        └── Schedule
        ↓
Recommendation Engine
        ↓
Portion Solver
        ↓
Nutrition Validator
        ↓
Budget Validator
        ↓
Meal Result
        ↓
PostgreSQL / Recommendation Audit
        ↓
FastAPI View Model
        ↓
Next.js
        ↓
USER

Turkey Fajita Bowl
610 kcal · 48g protein
~31 lei pentru 4 porții

[ Aleg masa ]
```

Userul vede un card simplu.

Arhitectura din spate poate fi extrem de sofisticată.

---

# 389. Technical North Star

Trebuie să putem spune despre orice feature:

> **„Știm ce modul îl deține, știm care este sursa lui de adevăr, știm ce engine îl calculează și îl putem testa fără UI.”**

Dacă nu putem:

feature-ul nu este încă arhitecturat corect.

---

# 390. Final Architecture Principle

> **Build the simplest implementation capable of respecting the final domain boundaries.**

Nu construim infrastructura produsului din 2028 în 2026.

Dar nici nu construim Iteration 0 într-un mod care trebuie aruncat la Iteration 2.

Începem cu:

```text
Next.js
      ↓
FastAPI modular monolith
      ↓
PostgreSQL
```

și lăsăm produsul real să justifice fiecare nouă piesă de infrastructură.

Aceasta este fundația tehnică a întregului sistem.
