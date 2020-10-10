# clean-code-javascript

## Sadržaj

1. [Uvod](#uvod)
2. [Promenljive](#promenljive)
3. [Funkcije](#funkcije)
4. [Objekti i strukture podataka](#objekti-i-strukture-podataka)
5. [Klase](#klase)
6. [SOLID](#solid)
7. [Testiranje](#testiranje)
8. [Asihronost](#asihronost)
9. [Rad sa greškama](#rad-sa-greškama)
10. [Formatiranje](#formatiranje)
11. [Komentari](#komentari)
12. [Prevod](#prevod)

## Uvod

![Humorous image of software quality estimation as a count of how many expletives
you shout when reading code](https://www.osnews.com/images/comics/wtfm.jpg)

Principi programiranja, iz knjige Roberta C. Martina [_Clean Code_](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882), prilagođeni za JavaScript. Ovo nije style guide. Ovo je vodič za pisanje [čitljivog, ponovno upotrebljivog, i održivog](https://github.com/ryanmcdermott/3rs-of-software-architecture) programa u JavaScript-u.

Ne mora se svaki pricip koji je ovde opisan poštovati. Ovo su smernice i ništa više, ali smernice koje su autori _Clean Code_ napisali tokom dugogodišnjeg iskustva.

Zanat programiranja star je više od 50 godina, a mi još uvek mnogo učimo. Kada softverska arhitektura bude stara kao i sama arhitektura, možda ćemo tada imati strožija pravila koja će biti neophodna. Za sada neka ove smernice služe kao kamen temeljac uz pomoć koga ćete oceniti kvalitet JavaScript koda koji pišete.

Još nešto: poznavanje ovih principa neće vas odmah učiniti boljim programerom, a pridržavanje ovih principa dugi niz godina ne znači da nećete pogrešiti. Svaki komad koda započinje kao grubi nacrt, kao i komad mokre gline koji poprima svoj konačan oblik. Nedostatke ćemo ispraviti kada sa kolegama pregledamo kod. Ne udarajte po sebi zbog prvih nacrta kojima su potrebna poboljšanja. Umesto toga udrite po kodu! :)

## **Promenljive**

### Koristite imena koja otkrivaju namenu

**Loše:**

```javascript
const yyyymmdstr = moment().format("YYYY/MM/DD");
```

**Dobro:**

```javascript
const currentDate = moment().format("YYYY/MM/DD");
```

**[⬆ vrati se na početak](#sadržaj)**

### Koristite istu metodu za isti tip promenljive

**Loše:**

```javascript
getUserInfo();
getClientData();
getCustomerRecord();
```

**Dobro:**

```javascript
getUser();
```

**[⬆ vrati se na početak](#sadržaj)**

### Koristite imena koja se mogu lako pretraživati

Pročitaćemo više koda nego što ćemo ga ikada napisati. Važno je da kod koji mi pišemo bude čitljiv i da ga je lako pretražiti. Alati kao što su [buddy.js](https://github.com/danielstjules/buddy.js) i [ESLint](https://github.com/eslint/eslint/blob/660e0918933e6e7fede26bc675a0763a6b357c94/docs/rules/no-magic-numbers.md) nam mogu pomoći u identifikovanju neimenovanih konstanti.

**Loše:**

```javascript
// What the heck is 86400000 for?
setTimeout(blastOff, 86400000);
```

**Dobro:**

```javascript
// Declare them as capitalized named constants.
const MILLISECONDS_IN_A_DAY = 86_400_000;

setTimeout(blastOff, MILLISECONDS_IN_A_DAY);
```

**[⬆ vrati se na početak](#sadržaj)**

### Koristite imena koja imaju smisleno značenje

**Loše:**

```javascript
const address = "One Infinite Loop, Cupertino 95014";
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
saveCityZipCode(
  address.match(cityZipCodeRegex)[1],
  address.match(cityZipCodeRegex)[2]
);
```

**Dobro:**

```javascript
const address = "One Infinite Loop, Cupertino 95014";
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
const [_, city, zipCode] = address.match(cityZipCodeRegex) || [];
saveCityZipCode(city, zipCode);
```

**[⬆ vrati se na početak](#sadržaj)**

### Izbegavajte mentalno mapiranje

Eksplicitno je bolje od implicitnog. Čitaoci koda ne bi trebalo mentalno da prevode imena u druga imena koja već znaju. To je problem sa imenima promeljivih sa jednim slovom.

**Loše:**

```javascript
const locations = ["Austin", "New York", "San Francisco"];
locations.forEach(l => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  // Wait, what is `l` for again?
  dispatch(l);
});
```

**Dobro:**

```javascript
const locations = ["Austin", "New York", "San Francisco"];
locations.forEach(location => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  dispatch(location);
});
```

**[⬆ vrati se na početak](#sadržaj)**

### Nemojte dodavati nepotrebno značenje

Ako vam ime klase / objekta nešto govori, nemote to ponavljati u imenama promenljivih.

**Loše:**

```javascript
const Car = {
  carMake: "Honda",
  carModel: "Accord",
  carColor: "Blue"
};

function paintCar(car) {
  car.carColor = "Red";
}
```

**Dobro:**

```javascript
const Car = {
  make: "Honda",
  model: "Accord",
  color: "Blue"
};

function paintCar(car) {
  car.color = "Red";
}
```

**[⬆ vrati se na početak](#sadržaj)**

### Koristite podrazumevane vrednosti

**Loše:**

```javascript
function createMicrobrewery(name) {
  const breweryName = name || "Hipster Brew Co.";
  // ...
}
```

**Dobro:**

```javascript
function createMicrobrewery(name = "Hipster Brew Co.") {
  // ...
}
```

**[⬆ vrati se na početak](#sadržaj)**

## **Funkcije**

### Argumenti funkcije

Ograničavanje broja argumenata za funkciju je od velike važnosti jer olakšava testiranje funkcije. Imati više od tri argumenta dovodi do velikog broja kombinacija pri testiranju u kojem moramo da ponovimo veliki broj različitih slučajeva za svaki pojedinačni argument.

Idealan broj argumenata za funkciju je nula. Zatim jedan i dva. Tri argumenta treba izbegavati gde god je moguće. Kada se čini da je za funkciju potrebno više od dva ili tri argumenata, onda bi neki od njih trebalo da budu zamotani u objekat.

Da biste učinili očiglednim koje argumente funkcija očekuje, možete koristiti sintaksu destruktuiranja ES2015/ES6. 

**Loše:**

```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}

createMenu("Foo", "Bar", "Baz", true);

```

**Dobro:**

```javascript
function createMenu({ title, body, buttonText, cancellable }) {
  // ...
}

createMenu({
  title: "Foo",
  body: "Bar",
  buttonText: "Baz",
  cancellable: true
});
```

**[⬆ vrati se na početak](#sadržaj)**

### Funkcije treba da rade jednu stvar

Ovo je ubedljivo najvažnije pravilo u razvoju softvera. Kada funkcije rade više stvari, teže ih je kombinovati, testirati i razumeti. Kada funkciju svedemo samo na jednu radnju, mnogo je lakše refaktorizati je i kod postaje mnogo čitljiviji.

**Loše:**

```javascript
function emailClients(clients) {
  clients.forEach(client => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
```

**Dobro:**

```javascript
function emailActiveClients(clients) {
  clients.filter(isActiveClient).forEach(email);
}

function isActiveClient(client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```

**[⬆ vrati se na početak](#sadržaj)**

### Koristite opisna imena funkcija

**Loše:**

```javascript
function addToDate(date, month) {
  // ...
}

const date = new Date();

// It's hard to tell from the function name what is added
addToDate(date, 1);
```

**Dobro:**

```javascript
function addMonthToDate(month, date) {
  // ...
}

const date = new Date();
addMonthToDate(1, date);
```

**[⬆ vrati se na početak](#sadržaj)**

### Jedan nivo apstrakcije po funkciji

Ako funkcija ima više od jednog nivoa apstrakcije, ima tendenciju da radi previše. Odvajanjem takvih funkcija dovodi do ponovne upotrebe i lakšeg testiranja.

**Loše:**

```javascript
function parseBetterJSAlternative(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(" ");
  const tokens = [];
  REGEXES.forEach(REGEX => {
    statements.forEach(statement => {
      // ...
    });
  });

  const ast = [];
  tokens.forEach(token => {
    // lex...
  });

  ast.forEach(node => {
    // parse...
  });
}
```

**Dobro:**

```javascript
function parseBetterJSAlternative(code) {
  const tokens = tokenize(code);
  const syntaxTree = parse(tokens);
  syntaxTree.forEach(node => {
    // parse...
  });
}

function tokenize(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(" ");
  const tokens = [];
  REGEXES.forEach(REGEX => {
    statements.forEach(statement => {
      tokens.push(/* ... */);
    });
  });

  return tokens;
}

function parse(tokens) {
  const syntaxTree = [];
  tokens.forEach(token => {
    syntaxTree.push(/* ... */);
  });

  return syntaxTree;
}
```

**[⬆ vrati se na početak](#sadržaj)**

### Rešite se dupliranog koda

Potrudite se da izbegnete duplirani kod. Duplirani kod je štetan zato što potrazumeva više mesta koja treba izmetiti ako se algoritam promeni.

Zamislite da vodite restoran i pratite potrošnju namernica: paradajiz, luk, začine itd. Ako imate više spiskova na kojima ovo pratite, za servisiranje bilo kog jela sa paradajizom biće potrebne promene na svakom spisku. Ako postoji samo jedan spisak, biće potrebno samo jedno ažuriranje!

**Loše:**

```javascript
function showDeveloperList(developers) {
  developers.forEach(developer => {
    const expectedSalary = developer.calculateExpectedSalary();
    const experience = developer.getExperience();
    const githubLink = developer.getGithubLink();
    const data = {
      expectedSalary,
      experience,
      githubLink
    };

    render(data);
  });
}

function showManagerList(managers) {
  managers.forEach(manager => {
    const expectedSalary = manager.calculateExpectedSalary();
    const experience = manager.getExperience();
    const portfolio = manager.getMBAProjects();
    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```

**Dobro:**

```javascript
function showEmployeeList(employees) {
  employees.forEach(employee => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();

    const data = {
      expectedSalary,
      experience
    };

    switch (employee.type) {
      case "manager":
        data.portfolio = employee.getMBAProjects();
        break;
      case "developer":
        data.githubLink = employee.getGithubLink();
        break;
    }

    render(data);
  });
}
```

**[⬆ vrati se na početak](#sadržaj)**

### Podesite podrazumevane vrednosti objekta pomoću Object.assign metode

**Loše:**

```javascript
const menuConfig = {
  title: null,
  body: "Bar",
  buttonText: null,
  cancellable: true
};

function createMenu(config) {
  config.title = config.title || "Foo";
  config.body = config.body || "Bar";
  config.buttonText = config.buttonText || "Baz";
  config.cancellable =
    config.cancellable !== undefined ? config.cancellable : true;
}

createMenu(menuConfig);
```

**Dobro:**

```javascript
const menuConfig = {
  title: "Order",
  // User did not include 'body' key
  buttonText: "Send",
  cancellable: true
};

function createMenu(config) {
  let finalConfig = Object.assign(
    {
      title: "Foo",
      body: "Bar",
      buttonText: "Baz",
      cancellable: true
    },
    config
  );
  return finalConfig
  // config now equals: {title: "Order", body: "Bar", buttonText: "Send", cancellable: true}
  // ...
}

createMenu(menuConfig);
```

**[⬆ vrati se na početak](#sadržaj)**

### Ne koristite argumente pokazivače

Argumenti pokazivači, zastavice (engl. *flags*) su ružni.
Prosleđivanje logičke vrednosti u funkciju je zaista loša praksa. To odmah otežava razumevanje metode, naglašavajući da funkcija obavlja više stvari. Jedna stvar je ako je vrednost pokazivača istinita (engl. *true*), a druga ako je lažna (engl. *false*).

**Loše:**

```javascript
function createFile(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**Dobro:**

```javascript
function createFile(name) {
  fs.create(name);
}

function createTempFile(name) {
  createFile(`./temp/${name}`);
}
```

**[⬆ vrati se na početak](#sadržaj)**

### Izbegavajte neželjene efekte (deo 1)

Funkcija će imati sporedan efekat ako radi bilo šta drugo osim što uzima neku vrednost i vraća drugu vrednost ili vrednosti. Neželjeni efekti se mogu javiti u vidu izmena globalne varijable i slično.

**Loše:**

```javascript
// Global variable referenced by following function.
// If we had another function that used this name, now it'd be an array and it could break it.
let name = "Ryan McDermott";

function splitIntoFirstAndLastName() {
  name = name.split(" ");
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];
```

**Dobro:**

```javascript
function splitIntoFirstAndLastName(name) {
  return name.split(" ");
}

const name = "Ryan McDermott";
const newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Ryan McDermott';
console.log(newName); // ['Ryan', 'McDermott'];
```

**[⬆ vrati se na početak](#sadržaj)**

### Izbegavajte neželjene efekte (deo 2)

U JavaScript-u primitivni tipovi se čuvaju po vrednosti, a objketi i nizovi po referenci.
Referentani tip vrednosti se čuva u sporijem delu memorije i za razliku od primitivnog tipa  vrednosti referentni tip vrednost može da se menja tokom vremena.

Ukoliko neka funkcija izvrši promenu nad ulaznim nizom ili objketom, to će uticati na sve ostale funkcije koje koriste isti taj niz ili objekat kao ulazni parametar. Odlično rešenje bi bilo da nizove i objekte kao ulazne parametre, unutar funkcije uvek kloniramo.

Treba istaći i dva upozorenja ovom pristupu:
1.  Možda postoje slučajevi kada želimo da izmenimo ulazni objekat, ali kada usvojite ovu programsku praksu, otkrićete da su ti slučajevi prilično retki.

2.  Kloniranje velikih objekata može biti veoma skupo u pogledu performansi. Srećom ovo nije veliko pitanje u praksi, obzirom da postoje [sjajne biblioteke](https://facebook.github.io/immutable-js/) koje rešavaju probleme ovakvog pristupa.

**Loše:**

```javascript
const addItemToCart = (cart, item) => {
  cart.push({ item, date: Date.now() });
};
```

**Dobro:**

```javascript
const addItemToCart = (cart, item) => {
  return [...cart, { item, date: Date.now() }];
};
```

**[⬆ vrati se na početak](#sadržaj)**

### Nemojte dodavati globalne funkcije

Dodavanje funkcija globalnom objketu je loša praksa u Javascript-u jer može doći do sukoba sa drugim bibliotekama. Šta ako želimo da proširimo globalni objekat `Array` tako da ima `diff` metodu koja će prikazivati razliku između dva niza? Mogli bismo da  napišemo novu metodu na `Array.prototype`, ali možda će početi da se sukobljava sa drugom bibliotekom koja pokušava da učini isto. Šta ako druga biblioteka koristi `diff` da pokaže razliku između prvog i poslednjeg elementa u nizu? Zbog toga je mnogo bolje koristiti klase ES2015/ES6 i proširiti globalni objekat `Array`.

**Loše:**

```javascript
Array.prototype.diff = function diff(comparisonArray) {
  const hash = new Set(comparisonArray);
  return this.filter(elem => !hash.has(elem));
};
```

**Dobro:**

```javascript
class SuperArray extends Array {
  diff(comparisonArray) {
    const hash = new Set(comparisonArray);
    return this.filter(elem => !hash.has(elem));
  }
}
```

**[⬆ vrati se na početak](#sadržaj)**

### Dajte prednost funkcionalnom programiranju nad imperativnim programiranjem

JavaScript nije toliko funkcionalan kao Haskell, ali ima predispoziciju za to. Funkcionalni jezici su čistiji i lakši za testiranje. Preferirajte ovaj stil programiranja kad god možete.

**Loše:**

```javascript
const programmerOutput = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000
  }
];

let totalOutput = 0;

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}
```

**Dobro:**

```javascript
const programmerOutput = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000
  }
];

const totalOutput = programmerOutput.reduce(
  (totalLines, output) => totalLines + output.linesOfCode,
  0
);
```

**[⬆ vrati se na početak](#sadržaj)**

### Enkapsulacija uslova

**Loše:**

```javascript
if (fsm.state === "fetching" && isEmpty(listNode)) {
  // ...
}
```

**Dobro:**

```javascript
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === "fetching" && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```

**[⬆ vrati se na početak](#sadržaj)**

### Izbegavajte negativne uslove

**Loše:**

```javascript
function isDOMNodeNotPresent(node) {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // ...
}
```

**Dobro:**

```javascript
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}
```

**[⬆ vrati se na početak](#sadržaj)**

### Izbegavajte uslove

Čini se kao nemoguć zadatak. Većina ljudi, kada ovo prvi put čuje, kaže: "Kako da radim nešto bez `if`?" Odgovor je da u mnogim slučajevima možemo koristiti polimorfizam da bi postigli isti cilj. Zašto bi ovo radili leži u jednom od prethodnih principa: funkcija treba da radi samo jednu stvar. Čim funkcija ima uslov `if` to znači da ta funkcija radi više od jedne stvari.

**Loše:**

```javascript
class Airplane {
  // ...
  getCruisingAltitude() {
    switch (this.type) {
      case "777":
        return this.getMaxAltitude() - this.getPassengerCount();
      case "Air Force One":
        return this.getMaxAltitude();
      case "Cessna":
        return this.getMaxAltitude() - this.getFuelExpenditure();
    }
  }
}
```

**Dobro:**

```javascript
class Airplane {
  // ...
}

class Boeing777 extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount();
  }
}

class AirForceOne extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude();
  }
}

class Cessna extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure();
  }
}
```

**[⬆ vrati se na početak](#sadržaj)**

### Izbegavajte proveru tipa (deo 1)

**Loše:**

```javascript
function travelToTexas(vehicle) {
  if (vehicle instanceof Bicycle) {
    vehicle.pedal(this.currentLocation, new Location("texas"));
  } else if (vehicle instanceof Car) {
    vehicle.drive(this.currentLocation, new Location("texas"));
  }
}
```

**Dobro:**

```javascript
function travelToTexas(vehicle) {
  vehicle.move(this.currentLocation, new Location("texas"));
}
```

**[⬆ vrati se na početak](#sadržaj)**

### Izbegavajte proveru tipa (deo 2)

Ukoliko osećate da imate potrebu za proverom tipa, trebalo bi da razmislite o korišćenju TypeScript-a. TypeScript je programski jezik koji je baziran na JavaScript jeziku, ali je postavljen kao nad-jezik, tj. jezik koji proširuje funkcionalnosti JavaScript-a. Posebna osobina TypeScript jezika, u odnosu na JavaScript je ta što koristi mehanizam statički izričito definisanih tipova za promenljive, parametre funkcija, povratne tipove funkcija itd.

**Loše:**

```javascript
function combine(val1, val2) {
  if (
    (typeof val1 === "number" && typeof val2 === "number") ||
    (typeof val1 === "string" && typeof val2 === "string")
  ) {
    return val1 + val2;
  }

  throw new Error("Must be of type String or Number");
}
```

**Dobro:**

```javascript
function combine(val1, val2) {
  return val1 + val2;
}
```

**[⬆ vrati se na početak](#sadržaj)**

### Ne optimizujte preterano

Moderni pretraživači rade puno optimizacije tokom izvršavanja koda. [Postoje dobri resuri](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers) za otkrivanje nedostataka optimizacije, koristite ih.

**Loše:**

```javascript
// On old browsers, each iteration with uncached `list.length` would be costly
// because of `list.length` recomputation. In modern browsers, this is optimized.
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**Dobro:**

```javascript
for (let i = 0; i < list.length; i++) {
  // ...
}
```

**[⬆ vrati se na početak](#sadržaj)**

### Uklonite kod koji se ne koristi

Kod koji se ne koristi je jednako loš kao i duplirani kod. Nema razloga da čuvamo kod koji više ne koristimo.

**Loše:**

```javascript
function oldRequestModule(url) {
  // ...
}

function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

**Dobro:**

```javascript
function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

**[⬆ vrati se na početak](#sadržaj)**

## **Objekti i strukture podataka**

### Koristite getters i setters

Bolje je koristiti `get` i `set` metode kada pristupamo svojstvima objekta nego direktno pristupiti njima. Ako se pitate "Zašto?" Evo nekoliko razloga:

- Validacija je lako primeljiva na nivou `set` metode
- Enkapsulaciju promenljivih unutar objekta
- Jednostavnije je rukovanje greškama na nivou `get` i `set` metoda

**Loše:**

```javascript
function makeBankAccount() {
  // ...

  return {
    balance: 0
    // ...
  };
}

const account = makeBankAccount();
account.balance = 100;
```

**Dobro:**

```javascript
function makeBankAccount() {
  // this one is private
  let balance = 0;

  // a "getter", made public via the returned object below
  function getBalance() {
    return balance;
  }

  // a "setter", made public via the returned object below
  function setBalance(amount) {
    // ... validate before updating the balance
    balance = amount;
  }

  return {
    // ...
    getBalance,
    setBalance
  };
}

const account = makeBankAccount();
account.setBalance(100);
```

**[⬆ vrati se na početak](#sadržaj)**

### Koristite privatne članove

To se može postići pomoću *closure*

**Loše:**

```javascript
const Employee = function(name) {
  this.name = name;
};

Employee.prototype.getName = function getName() {
  return this.name;
};

const employee = new Employee("John Doe");
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: undefined
```

**Dobro:**

```javascript
function makeEmployee(name) {
  return {
    getName() {
      return name;
    }
  };
}

const employee = makeEmployee("John Doe");
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
```

**[⬆ vrati se na početak](#sadržaj)**

## **Classes**

### Preferirajte klase ES2015/ES6 u odnosu na konstruktor funkcije ES5

Pomoću ES5, veoma je teško napisati čitljivu konstruktor funkciju koja nasleđuje neke metode od druge konstruktor funkcije. Ukoliko je potrebno da koristite nasleđivanje, onda koristite klase iz ES2015/ES6. Najbolje je raditi sa malim funkcijama sve dok ne vidite potrebu za većim i složenijim objektom.

**Loše:**

```javascript
const Animal = function(age) {
  if (!(this instanceof Animal)) {
    throw new Error("Instantiate Animal with `new`");
  }

  this.age = age;
};

Animal.prototype.move = function move() {};

const Mammal = function(age, furColor) {
  if (!(this instanceof Mammal)) {
    throw new Error("Instantiate Mammal with `new`");
  }

  Animal.call(this, age);
  this.furColor = furColor;
};

Mammal.prototype = Object.create(Animal.prototype);
Mammal.prototype.constructor = Mammal;
Mammal.prototype.liveBirth = function liveBirth() {};

const Human = function(age, furColor, languageSpoken) {
  if (!(this instanceof Human)) {
    throw new Error("Instantiate Human with `new`");
  }

  Mammal.call(this, age, furColor);
  this.languageSpoken = languageSpoken;
};

Human.prototype = Object.create(Mammal.prototype);
Human.prototype.constructor = Human;
Human.prototype.speak = function speak() {};
```

**Dobro:**

```javascript
class Animal {
  constructor(age) {
    this.age = age;
  }

  move() {
    /* ... */
  }
}

class Mammal extends Animal {
  constructor(age, furColor) {
    super(age);
    this.furColor = furColor;
  }

  liveBirth() {
    /* ... */
  }
}

class Human extends Mammal {
  constructor(age, furColor, languageSpoken) {
    super(age, furColor);
    this.languageSpoken = languageSpoken;
  }

  speak() {
    /* ... */
  }
}
```

**[⬆ vrati se na početak](#sadržaj)**

### Koristite *method chaining*

Ovaj pattern je veoma koristan u JavaScript-u i možete ga videti u mnogim bibliotekama kao što su jQuery i Lodash.
Jednostavno u metodama vratite `this` na kraju svake funkcije i zatim kod poziva metoda, možete nadovezivati ostale metode.

**Loše:**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
  }

  setModel(model) {
    this.model = model;
  }

  setColor(color) {
    this.color = color;
  }

  save() {
    console.log(this.make, this.model, this.color);
  }
}

const car = new Car("Ford", "F-150", "red");
car.setColor("pink");
car.save();
```

**Dobro:**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
    // NOTE: Returning this for chaining
    return this;
  }

  setModel(model) {
    this.model = model;
    // NOTE: Returning this for chaining
    return this;
  }

  setColor(color) {
    this.color = color;
    // NOTE: Returning this for chaining
    return this;
  }

  save() {
    console.log(this.make, this.model, this.color);
    // NOTE: Returning this for chaining
    return this;
  }
}

const car = new Car("Ford", "F-150", "red").setColor("pink").save();
```

**[⬆ vrati se na početak](#sadržaj)**

### Dajte prednost kompoziciji (composition) nad nasleđivanjem (inheritance)

Postoji mnogo razloga kada treba upotrebiti nasleđivanje i mnogo razloga kada treba upotrebiti kompoziciju.
Ukoliko mislite da treba da implementirate nasleđivanje, pokušajte da razmislite da li bi kompozicija bila bolje rešenje. U nekim slučajevima će sigurno biti.

Ukoliko za primer uzmemo automobil: točkovi, motor, menjač itd. mogu biti posmatrani kao posebne klase. Klasa automobil bi predstavljala kompoziciju ovih pojedinačnih klasa.

**Loše:**

```javascript
class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // ...
}

// Bad because Employees "have" tax data. EmployeeTaxData is not a type of Employee
class EmployeeTaxData extends Employee {
  constructor(ssn, salary) {
    super();
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}
```

**Dobro:**

```javascript
class EmployeeTaxData {
  constructor(ssn, salary) {
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}

class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  setTaxData(ssn, salary) {
    this.taxData = new EmployeeTaxData(ssn, salary);
  }
  // ...
}
```

**[⬆ vrati se na početak](#sadržaj)**

## **SOLID**
SOLID je akronim koji se sastoji iz 5 principa: Single Responsibility, Open-Closed, Liskov Substitution, Interface Segregation i Dependency Inversion. Ovih 5 principa se najčešće koriste i imaju veoma velike pozitivne efekte na softver i kod u kome se primene. 

### Single Responsibility Principle (SRP)

Princip jednostruke odgovornosti govori o tome da svaka klasa treba da ima samo jednu odgovornost. To ne znači da treba da ima samo jednu funkciju. Ona može imati i više funkcija ako one zajedno izvršavaju jedan zadatak i zajedno čine samo jedan razlog za promenu te klase.

**Loše:**

```javascript
class UserSettings {
  constructor(user) {
    this.user = user;
  }

  changeSettings(settings) {
    if (this.verifyCredentials()) {
      // ...
    }
  }

  verifyCredentials() {
    // ...
  }
}
```

**Dobro:**

```javascript
class UserAuth {
  constructor(user) {
    this.user = user;
  }

  verifyCredentials() {
    // ...
  }
}

class UserSettings {
  constructor(user) {
    this.user = user;
    this.auth = new UserAuth(user);
  }

  changeSettings(settings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}
```

**[⬆ vrati se na početak](#sadržaj)**

### Open/Closed Principle (OCP)

Otvoren-zatvoren princip govori o tome da klasa treba biti otvorena za proširenja, a zatvorena za izmene. To znači da ako je potrebno uneti neke nove funkcionalnosti, onda ne treba menjati klasu i postojeće metode, već je proširiti. 

**Loše:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    if (this.adapter.name === "ajaxAdapter") {
      return makeAjaxCall(url).then(response => {
        // transform response and return
      });
    } else if (this.adapter.name === "nodeAdapter") {
      return makeHttpCall(url).then(response => {
        // transform response and return
      });
    }
  }
}

function makeAjaxCall(url) {
  // request and return promise
}

function makeHttpCall(url) {
  // request and return promise
}
```

**Dobro:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }

  request(url) {
    // request and return promise
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }

  request(url) {
    // request and return promise
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    return this.adapter.request(url).then(response => {
      // transform response and return
    });
  }
}
```

**[⬆ vrati se na početak](#sadržaj)**

### Liskov Substitution Principle (LSP)

Princip Liskove zamene kaže da sve podklase neke nadklase mogu da se zamene svojom nadklasom, a da se pri tome ponašanje programa ne promeni. 

**Loše:**

```javascript
class Rectangle {
  constructor() {
    this.width = 0;
    this.height = 0;
  }

  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }

  setWidth(width) {
    this.width = width;
  }

  setHeight(height) {
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  setWidth(width) {
    this.width = width;
    this.height = width;
  }

  setHeight(height) {
    this.width = height;
    this.height = height;
  }
}

function renderLargeRectangles(rectangles) {
  rectangles.forEach(rectangle => {
    rectangle.setWidth(4);
    rectangle.setHeight(5);
    const area = rectangle.getArea(); // Bad: Returns 25 for Square. Should be 20.
    rectangle.render(area);
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);
```

**Dobro:**

```javascript
class Shape {
  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }
}

class Rectangle extends Shape {
  constructor(width, height) {
    super();
    this.width = width;
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor(length) {
    super();
    this.length = length;
  }

  getArea() {
    return this.length * this.length;
  }
}

function renderLargeShapes(shapes) {
  shapes.forEach(shape => {
    const area = shape.getArea();
    shape.render(area);
  });
}

const shapes = [new Rectangle(4, 5), new Rectangle(4, 5), new Square(5)];
renderLargeShapes(shapes);
```

**[⬆ vrati se na početak](#sadržaj)**

### Interface Segregation Principle (ISP)

JavaScrip nema intefejse, pa se ovaj princip ne može u potpunosti iskoristiti. U ISP se naglašava da "Klijente ne treba prisiljavati da zavise od interfejsa koje ne koriste". Dobar primer koji se može primeniti u JavaScript-u jeste da prilikom kreiranja neke klase ne zahtevamo veliki broj ulaznih parametra koji se neće puno koristiti, već da ih ostavimo kao opcione.

**Loše:**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.settings.animationModule.setup();
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName("body"),
  animationModule() {} // Most of the time, we won't need to animate when traversing.
  // ...
});
```

**Dobro:**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.options = settings.options;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.setupOptions();
  }

  setupOptions() {
    if (this.options.animationModule) {
      // ...
    }
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName("body"),
  options: {
    animationModule() {}
  }
});
```

**[⬆ vrati se na početak](#sadržaj)**

### Dependency Inversion Principle (DIP)

Princip inverzije zavisnosti naglašava dve bitne stvari:

1.  Moduli višeg nivoa ne treba da zavise od modula nižeg nivoa.
2.  Apstrakcije ne treba da zavise od detalja, već detalji od apstrakcija.

Kao što smo ranije napomenuli, JavaScript nema iterfejse, to znači da treba da se kreiraju apstrakcije koje predstavljaju ugovor.
Apstrakcija samo govori šta neka klasa treba da radi, ne i kako. Klase koje će naslediti tu apstraktu klasu treba da znaju šta sve ta apstraktna klasa ima od funkcija i na taj način zapravo detalji zavise od apstrakcija, a apstrakcije ne zavise od detalja. 

**Loše:**

```javascript
class InventoryRequester {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryTracker {
  constructor(items) {
    this.items = items;

    // Bad: We have created a dependency on a specific request implementation.
    // We should just have requestItems depend on a request method: `request`
    this.requester = new InventoryRequester();
  }

  requestItems() {
    this.items.forEach(item => {
      this.requester.requestItem(item);
    });
  }
}

const inventoryTracker = new InventoryTracker(["apples", "bananas"]);
inventoryTracker.requestItems();
```

**Dobro:**

```javascript
class InventoryTracker {
  constructor(items, requester) {
    this.items = items;
    this.requester = requester;
  }

  requestItems() {
    this.items.forEach(item => {
      this.requester.requestItem(item);
    });
  }
}

class InventoryRequesterV1 {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryRequesterV2 {
  constructor() {
    this.REQ_METHODS = ["WS"];
  }

  requestItem(item) {
    // ...
  }
}

// By constructing our dependencies externally and injecting them, we can easily
// substitute our request module for a fancy new one that uses WebSockets.
const inventoryTracker = new InventoryTracker(
  ["apples", "bananas"],
  new InventoryRequesterV2()
);
inventoryTracker.requestItems();
```

**[⬆ vrati se na početak](#sadržaj)**

## **Testiranje**

Testovi su jednako važni za zdravlje projekta kao i sam kod. Možda su još i važniji, jer testovi čuvaju i poboljšavaju felksibilnost, održavanje i naknadnu upotrebu proizvodnog koda. Ako imamo testove ne plašimo se promene koda! Bez testova svaka promena predstavlja moguću grešku.

### Jedna tvrdnja po testu (single concept per test)

**Loše:**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles date boundaries", () => {
    let date;

    date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);

    date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);

    date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**Dobro:**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles 30-day months", () => {
    const date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);
  });

  it("handles leap year", () => {
    const date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);
  });

  it("handles non-leap year", () => {
    const date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**[⬆ vrati se na početak](#sadržaj)**

## **Asihronost**

### Koristite Obećanja (Promises), umesto povratnih poziva (callbacks)

Povratni pozivi (callbacks) dovode do prekomernog gnježđenja i loše čitljivosti koda.
U ES2015/ES6 Promisi su ugrađeni kao globalni tipovi. Koristite ih!

**Loše:**

```javascript
import { get } from "request";
import { writeFile } from "fs";

get(
  "https://en.wikipedia.org/wiki/Robert_Cecil_Martin",
  (requestErr, response, body) => {
    if (requestErr) {
      console.error(requestErr);
    } else {
      writeFile("article.html", body, writeErr => {
        if (writeErr) {
          console.error(writeErr);
        } else {
          console.log("File written");
        }
      });
    }
  }
);
```

**Dobro:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(body => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });
```

**[⬆ vrati se na početak](#sadržaj)**

### Async/Await čini kod čistijim više od Promisa

Promisi su vrlo dobra alternativa za callback, ali  ES2017/ES8 uvode  async/await koje predstavlja još bolje rešenje. Sve što treba da uradite jeste da napišete funkciju sa `async` prefiksom u kojoj možete da koristite svoju asihronu logiku.

**Loše:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(body => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });
```

**Dobro:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

async function getCleanCodeArticle() {
  try {
    const body = await get(
      "https://en.wikipedia.org/wiki/Robert_Cecil_Martin"
    );
    await writeFile("article.html", body);
    console.log("File written");
  } catch (err) {
    console.error(err);
  }
}

getCleanCodeArticle()
```

**[⬆ vrati se na početak](#sadržaj)**

## **Rad sa greškama**

### Ne ignorišite uhvaćene greške

Ne radeći ništa sa uhvaćenom greškom, gubimo priliku da je ispravimo ili da na nju ikada reagujemo. Ako obmotovamo deo koda u `try/catch` onda sumnjamo da se tu može javiti greška, tada trebamo imati plan šta ćemo uraditi sa njom.

**Loše:**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}
```

**Dobro:**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  // One option (more noisy than console.log):
  console.error(error);
  // Another option:
  notifyUserOfError(error);
  // Another option:
  reportErrorToService(error);
  // OR do all three!
}
```

### Nemojte zanemariti ni odbijene (rejected) promise

**Loše:**

```javascript
getdata()
  .then(data => {
    functionThatMightThrow(data);
  })
  .catch(error => {
    console.log(error);
  });
```

**Dobro:**

```javascript
getdata()
  .then(data => {
    functionThatMightThrow(data);
  })
  .catch(error => {
    // One option (more noisy than console.log):
    console.error(error);
    // Another option:
    notifyUserOfError(error);
    // Another option:
    reportErrorToService(error);
    // OR do all three!
  });
```

**[⬆ vrati se na početak](#sadržaj)**

## **Formatiranje**

Formatiranje je subjektivno. Kao i mnogo pravila u ovom dokumentu, ne postoji čvrsto i brzo pravilo koje morate poštovati. Glavna poenta je da se ne svađate oko formatiranja! :)
Postoji [mnogo alata](https://standardjs.com/rules.html) za automatizaciju formatiranja.

### Budite dosledni prilikom imenovanja

JavaScript je netipiziran, tako da vaš tim može izabrati imenovanje koje želi. Poenta je da bez obzira šta se odabere, budete dosledni.

**Loše:**

```javascript
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const songs = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const Artists = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restore_database() {}

class animal {}
class Alpaca {}
```

**Dobro:**

```javascript
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const SONGS = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const ARTISTS = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restoreDatabase() {}

class Animal {}
class Alpaca {}
```

**[⬆ vrati se na početak](#sadržaj)**

### Srodne funkcije bi trebale biti u blizini

Ako funkcija poziva drugu funkciju, držite te funkcije vertikalno blizu izvornoj funkciji. Idealno bi bilo da funkcija koja koristi drugu funkciju bude tačno iznad nje. Zato što obično čitamo od vrha do dna.

**Loše:**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**Dobro:**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**[⬆ vrati se na početak](#sadržaj)**

## **Komentari**

### Kometarišite samo stvari koje imaju složenu poslovnu logiku

Komentari nisu obavezni. Dobar kod se sam opisuje.

**Loše:**

```javascript
function hashIt(data) {
  // The hash
  let hash = 0;

  // Length of string
  const length = data.length;

  // Loop through every character in data
  for (let i = 0; i < length; i++) {
    // Get character code.
    const char = data.charCodeAt(i);
    // Make the hash
    hash = (hash << 5) - hash + char;
    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**Dobro:**

```javascript
function hashIt(data) {
  let hash = 0;
  const length = data.length;

  for (let i = 0; i < length; i++) {
    const char = data.charCodeAt(i);
    hash = (hash << 5) - hash + char;

    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**[⬆ vrati se na početak](#sadržaj)**

### Ne ostavljate zakomentarisan kod

Ostavite stari kod u istoriji kontroli verzije *(version control)*

**Loše:**

```javascript
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```

**Dobro:**

```javascript
doStuff();
```

**[⬆ vrati se na početak](#sadržaj)**

### Ne vodite dnevnik komentara

Zapamtite, koristite *(version control)*! Koristite `git log` da biste videli istoriju.

**Loše:**

```javascript
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Removed type-checking (LI)
 * 2015-03-14: Added combine with type-checking (JR)
 */
function combine(a, b) {
  return a + b;
}
```

**Dobro:**

```javascript
function combine(a, b) {
  return a + b;
}
```

**[⬆ vrati se na početak](#sadržaj)**

### Izbegavajte pozicione markere

Neka funckije i imena promenljivih zajedno sa odgovarajućim formatiranjem daju vizalnu strukturu vašem kodu.

**Loše:**

```javascript
////////////////////////////////////////////////////////////////////////////////
// Scope Model Instantiation
////////////////////////////////////////////////////////////////////////////////
$scope.model = {
  menu: "foo",
  nav: "bar"
};

////////////////////////////////////////////////////////////////////////////////
// Action setup
////////////////////////////////////////////////////////////////////////////////
const actions = function() {
  // ...
};
```

**Dobro:**

```javascript
$scope.model = {
  menu: "foo",
  nav: "bar"
};

const actions = function() {
  // ...
};
```

**[⬆ vrati se na početak](#sadržaj)**

## Prevod

Dostupno i na drugim jezicima

- ![am](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Armenia.png) **Armenian**: [hanumanum/clean-code-javascript/](https://github.com/hanumanum/clean-code-javascript)
- ![bd](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Bangladesh.png) **Bangla(বাংলা)**: [InsomniacSabbir/clean-code-javascript/](https://github.com/InsomniacSabbir/clean-code-javascript/)
- ![br](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Brazil.png) **Brazilian Portuguese**: [fesnt/clean-code-javascript](https://github.com/fesnt/clean-code-javascript)
- ![cn](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/China.png) **Simplified Chinese**:
  - [alivebao/clean-code-js](https://github.com/alivebao/clean-code-js)
  - [beginor/clean-code-javascript](https://github.com/beginor/clean-code-javascript)
- ![tw](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Taiwan.png) **Traditional Chinese**: [AllJointTW/clean-code-javascript](https://github.com/AllJointTW/clean-code-javascript)
- ![fr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/France.png) **French**: [GavBaros/clean-code-javascript-fr](https://github.com/GavBaros/clean-code-javascript-fr)
- ![de](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Germany.png) **German**: [marcbruederlin/clean-code-javascript](https://github.com/marcbruederlin/clean-code-javascript)
- ![id](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Indonesia.png) **Indonesia**: [andirkh/clean-code-javascript/](https://github.com/andirkh/clean-code-javascript/)
- ![it](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Italy.png) **Italian**: [frappacchio/clean-code-javascript/](https://github.com/frappacchio/clean-code-javascript/)
- ![ja](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Japan.png) **Japanese**: [mitsuruog/clean-code-javascript/](https://github.com/mitsuruog/clean-code-javascript/)
- ![kr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/South-Korea.png) **Korean**: [qkraudghgh/clean-code-javascript-ko](https://github.com/qkraudghgh/clean-code-javascript-ko)
- ![pl](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Poland.png) **Polish**: [greg-dev/clean-code-javascript-pl](https://github.com/greg-dev/clean-code-javascript-pl)
- ![ru](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Russia.png) **Russian**:
  - [BoryaMogila/clean-code-javascript-ru/](https://github.com/BoryaMogila/clean-code-javascript-ru/)
  - [maksugr/clean-code-javascript](https://github.com/maksugr/clean-code-javascript)
- ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Spain.png) **Spanish**: [tureey/clean-code-javascript](https://github.com/tureey/clean-code-javascript)
- ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Uruguay.png) **Spanish**: [andersontr15/clean-code-javascript](https://github.com/andersontr15/clean-code-javascript-es)
- ![tr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Turkey.png) **Turkish**: [bsonmez/clean-code-javascript](https://github.com/bsonmez/clean-code-javascript/tree/turkish-translation)
- ![ua](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Ukraine.png) **Ukrainian**: [mindfr1k/clean-code-javascript-ua](https://github.com/mindfr1k/clean-code-javascript-ua)
- ![vi](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Vietnam.png) **Vietnamese**: [hienvd/clean-code-javascript/](https://github.com/hienvd/clean-code-javascript/)

**[⬆ vrati se na početak](#sadržaj)**
