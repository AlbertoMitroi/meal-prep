# Product Vision & Principles

## 1. Product Vision

Construim un sistem inteligent de nutriție și meal planning care înțelege persoana sau household-ul, obiectivele lor, preferințele alimentare, programul, bugetul, alimentele disponibile și istoricul alimentar și transformă toate aceste informații în decizii concrete despre ce să mănânce, cât să mănânce, ce să cumpere și ce să gătească.

Produsul nu trebuie să fie doar un loc în care utilizatorul înregistrează ce a mâncat.

Produsul trebuie să reducă numărul de decizii pe care utilizatorul trebuie să le ia zilnic în legătură cu alimentația.

### Promisiunea produsului

> **Spune-mi cine ești și eu te ajut să decizi ce să mănânci, cât să mănânci, ce să cumperi și ce să gătești!**

---

# 2. Problema pe care o rezolvăm

Majoritatea aplicațiilor de nutriție pornesc de la întrebarea:

> „Ce ai mâncat?”

și apoi calculează calorii.

Problema reală apare însă înainte:

* Ce mănânc astăzi?
* Ce cantitate ar trebui să mănânc?
* Ce gătim împreună dacă avem nevoi calorice diferite?
* Ce pot lua la birou?
* Ce pot pregăti pentru mai multe zile?
* Ce mai trebuie să mănânc astăzi ca să-mi ating necesarul?
* Ce ingrediente am deja?
* Ce cumpăr pentru toată săptămâna?
* Cum mă încadrez într-un buget?
* Cum evit să cumpăr ingrediente pe care apoi le arunc?
* Cum mănânc variat fără să planific fiecare masă?
* Ce fac dacă într-o zi vreau pizza, paste sau burger?
* Cum adaptez restul zilei în jurul acelei alegeri?

Produsul nostru trebuie să rezolve în primul rând aceste întrebări.

Tracking-ul este important, dar reprezintă doar una dintre componentele sistemului.

---

# 3. Product Thesis

Credem că alimentația devine mult mai ușor de gestionat atunci când aplicația cunoaște suficient context despre utilizator încât să poată transforma obiectivele nutriționale în acțiuni concrete.

În loc ca utilizatorul să gestioneze separat:

* calorii;
* macronutrienți;
* micronutrienți;
* rețete;
* cumpărături;
* buget;
* meal prep;
* produse;
* frigider;
* progres;

produsul trebuie să le trateze ca pe componente ale aceluiași sistem.

O alegere făcută într-o parte a sistemului trebuie să influențeze automat restul.

Exemplu:

**Micul dejun consumat → modifică necesarul rămas → influențează prânzul recomandat → influențează cina → influențează ingredientele necesare → influențează lista de cumpărături → influențează bugetul.**

---

# 4. Pentru cine construim

Produsul trebuie să funcționeze pentru mai multe configurații.

## Individual

O singură persoană care vrea să:

* slăbească;
* își mențină greutatea;
* crească în masă;
* mănânce mai echilibrat;
* își gestioneze mai bine mesele.

## Couple

Două persoane care locuiesc împreună și vor să mănânce aceleași preparate, dar au:

* targeturi calorice diferite;
* necesar proteic diferit;
* preferințe diferite;
* programe diferite;
* porții diferite.

## Household / Family

Mai multe persoane care împart:

* cumpărăturile;
* bucătăria;
* ingredientele;
* mesele;
* bugetul;

dar pot avea nevoi individuale diferite.

---

# 5. Core Value Proposition

Produsul trebuie să combine șase lucruri care în aplicațiile clasice sunt de obicei separate.

### Understand me

Aplicația știe:

* cine sunt;
* ce obiectiv am;
* greutatea și evoluția mea;
* necesarul meu;
* ce îmi place;
* ce nu îmi place;
* ce preparate prefer;
* cât de multă varietate vreau;
* programul meu;
* zilele de antrenament;
* programul de birou;
* restricțiile mele.

### Understand my household

Aplicația înțelege că mai multe persoane pot mânca aceeași mâncare, dar în cantități diferite.

### Understand my day

Fiecare masă modifică starea nutrițională a zilei.

Următoarea recomandare trebuie să țină cont de tot ce s-a întâmplat anterior.

### Understand my kitchen

Aplicația știe ce:

* avem în frigider;
* avem în congelator;
* avem în cămară;
* trebuie consumat;
* am cumpărat recent.

### Understand my money

Aplicația știe:

* bugetul săptămânal;
* bugetul lunar;
* costurile produselor;
* costul per masă;
* costul planului săptămânal.

### Reduce my decisions

Obiectivul final nu este să arătăm utilizatorului mai multe date.

Obiectivul este să transformăm datele în:

> **„Asta este cea mai bună opțiune pentru tine acum.”**

---

# 6. Principiile produsului

## Principle 1 — Decision First, Tracking Second

Produsul trebuie să ajute utilizatorul să decidă **înainte** să mănânce, nu doar să-i spună ce a făcut după.

Tracking-ul este input pentru sistem.

Recomandarea și planificarea reprezintă output-ul valoros.

---

## Principle 2 — Same Meal, Personalized Portions

Într-un household nu trebuie să generăm automat câte o mâncare diferită pentru fiecare persoană.

În primul rând încercăm să găsim:

> **un preparat comun + porții personalizate.**

Acesta este unul dintre principiile fundamentale ale produsului.

---

## Principle 3 — Every Meal Understands the Day

Nicio masă nu trebuie recomandată izolat.

Sistemul trebuie să știe:

* ce s-a mâncat deja;
* ce este planificat;
* kcal rămase;
* macros rămase;
* micronutrient gaps;
* mesele locked;
* antrenamentul;
* ora;
* preferințele.

Următoarea masă trebuie să completeze ziua, nu să fie doar o rețetă care „arată sănătos”.

---

## Principle 4 — Nutrition Is Deterministic

AI-ul nu trebuie să inventeze:

* calorii;
* valori nutriționale;
* targeturi;
* gramaje;
* micronutrienți.

Acestea trebuie calculate folosind:

* baze de date nutriționale;
* formule;
* algoritmi;
* constraint solving;
* reguli verificabile.

AI-ul poate propune și interpreta.

Motorul nutrițional calculează.

---

## Principle 5 — AI Is the Intelligence Layer, Not the Source of Truth

AI-ul este folosit pentru:

* conversație;
* computer vision;
* interpretarea fotografiilor;
* înțelegerea intenției;
* recomandări;
* ranking;
* personalizare;
* explicații.

Datele critice trebuie păstrate într-un model structurat și verificabil.

---

## Principle 6 — Adapt, Don't Punish

Dacă utilizatorul spune:

> „Diseară vreau burger.”

sistemul nu trebuie să răspundă:

> „Nu ai voie.”

Trebuie să răspundă logic prin sistem:

> **Burger locked → optimizează restul zilei.**

Produsul trebuie să se adapteze vieții utilizatorului.

Nu să ceară utilizatorului să-și adapteze viața aplicației.

---

## Principle 7 — Preference Is Data

Preferințele nu reprezintă doar un onboarding checkbox.

Sistemul trebuie să învețe continuu.

Trebuie diferențiate:

### Food Preference

Exemple:

* iubesc ouăle;
* îmi place puiul;
* evit avocado.

### Meal Preference

Exemple:

* iubesc omleta;
* îmi place Chicken Rice Bowl;
* nu îmi place Egg Salad.

Sistemul trebuie să folosească atât preferințele declarate, cât și comportamentul observat.

---

## Principle 8 — Variety Without Chaos

Utilizatorul trebuie să controleze cât de multă varietate dorește.

Sistemul nu trebuie:

* să recomande aceeași masă permanent;
* dar nici să introducă zeci de ingrediente și preparate diferite fără motiv.

Varietatea trebuie optimizată împreună cu:

* preferințele;
* bugetul;
* ingredient reuse;
* meal prep;
* food waste.

---

## Principle 9 — Optimize the Household, Not Just the Meal

O masă ieftină nu înseamnă neapărat o săptămână ieftină.

Planner-ul trebuie să optimizeze întregul sistem:

* ingredient reuse;
* număr de produse cumpărate;
* package sizes;
* leftovers;
* ingrediente deja disponibile;
* cost total;
* food waste.

---

## Principle 10 — Time Is a Nutritional Constraint

O recomandare perfectă nutrițional pe care utilizatorul nu are timp să o pregătească este o recomandare proastă.

Sistemul trebuie să considere:

* timpul disponibil;
* numărul de sesiuni de gătit;
* meal prep;
* leftovers;
* lunchboxes;
* frigider;
* congelator;
* microwave availability.

---

## Principle 11 — Meal Prep Is a First-Class Feature

Meal prep-ul nu este un simplu număr de servings.

Produsul trebuie să înțeleagă:

* când este gătită masa;
* câte porții există;
* cui îi aparțin;
* pentru ce zi;
* pentru ce masă;
* cât rezistă;
* dacă poate fi congelată;
* câte porții rămân.

---

## Principle 12 — Budget Is Part of Planning

Bugetul nu trebuie să fie doar un dashboard financiar.

El trebuie să influențeze recomandările.

Dacă avem:

> 150 lei rămași pentru următoarele trei zile,

planner-ul trebuie să știe acest lucru înainte să genereze mesele.

---

## Principle 13 — Pantry First

Înainte să cumpărăm ceva nou, sistemul trebuie să se întrebe:

> „Putem folosi ceva ce avem deja?”

Acest principiu reduce simultan:

* costul;
* waste-ul;
* cumpărăturile;
* aglomerația din frigider.

---

## Principle 14 — Minimize Manual Input

Introducerea manuală trebuie redusă cât mai mult.

Produsul trebuie să folosească unde este potrivit:

* barcode scanning;
* meal photos;
* scale photos;
* fridge scanning;
* receipt scanning;
* favorites;
* recent meals;
* saved products;
* recurring meals;
* one-tap logging.

---

## Principle 15 — Confirmation Over False Precision

Computer vision-ul poate greși.

Dacă AI-ul detectează:

> `94.7 kg`

sau:

> `~180 g chicken`

utilizatorul trebuie să poată confirma sau corecta.

Produsul nu trebuie să pretindă o precizie pe care tehnologia nu o poate oferi.

---

## Principle 16 — Explain When Useful, Don't Overwhelm

Sistemul poate ști foarte multe lucruri în spate.

Utilizatorul nu trebuie să vadă permanent:

* 30 micronutrienți;
* formule;
* scoring;
* algoritmi;
* toate motivele recomandării.

Default-ul trebuie să fie simplu:

> **„Îți recomand asta.”**

Iar pentru cine dorește:

> **„Why this?”**

poate vedea explicația.

---

## Principle 17 — Progressive Personalization

Aplicația trebuie să fie utilă din prima zi.

Dar trebuie să devină semnificativ mai bună după:

* 7 zile;
* 30 zile;
* 3 luni;
* 1 an.

Cu cât utilizatorul o folosește mai mult, cu atât sistemul înțelege mai bine:

* gusturile;
* rutina;
* porțiile;
* magazinele;
* produsele;
* bugetul;
* comportamentul alimentar.

---

# 7. What We Are Not

Produsul nu este:

### Doar un calorie tracker

Caloriile sunt o componentă.

### Doar o aplicație de rețete

Rețetele sunt materia primă pentru planner.

### Doar un AI chat

Conversația este una dintre interfețele sistemului.

### Doar un meal planner

Planificarea trebuie conectată cu tracking, pantry, budget și nutrition.

### Un AI care inventează sfaturi medicale

Produsul trebuie să aibă limite clare pentru situații medicale sau categorii care necesită recomandări profesionale.

---

# 8. Product North Star

Întrebarea principală după care evaluăm produsul este:

> **Cât de multă muncă mentală eliminăm din alimentația utilizatorului fără să-i eliminăm libertatea de alegere?**

Produsul ideal trebuie să permită unui utilizator să ajungă de la:

> „Ce naiba mâncăm săptămâna asta?”

la:

> **plan nutrițional + porții + meal prep + cumpărături + cost**

cu cât mai puține decizii manuale.

---

# 9. Ideal User Experience

Experiența ideală ar trebui să arate astfel:

### Început de săptămână

Utilizatorul setează:

* buget;
* program;
* zile de birou;
* zile de antrenament;
* numărul de sesiuni de gătit;
* eventualele mese dorite.

Apasă:

> **Plan my week**

Primește:

* meniul;
* porțiile fiecăruia;
* meal prep plan;
* lunchboxes;
* shopping list;
* cost estimat.

### În timpul zilei

Apasă:

> **What should I eat?**

și primește o recomandare bazată pe starea actuală a zilei.

### Dacă își schimbă planul

Spune:

> „Diseară vreau paste.”

Sistemul se adaptează.

### După cumpărături

Scanează bonul.

Pantry-ul și prețurile se actualizează.

### La masă

Poate:

* confirma masa planificată;
* scana preparatul;
* modifica porția;
* introduce manual.

### Dimineața

Face poză cântarului.

Greutatea intră în progress tracking.

În timp, întregul sistem învață și devine mai personalizat.

---

# 10. Ultimate Product Goal

Produsul trebuie să devină sistemul care cunoaște suficient de bine utilizatorul și household-ul încât întrebarea:

> **„Ce mâncăm?”**

să nu mai fie o problemă care trebuie rezolvată manual în fiecare zi.
