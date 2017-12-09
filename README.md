# clean-code-javaScript-translation
 A "clean-code-javascript" translation
## 介绍

Robert C. Martin's在他的《Clean Code》一书中，更新了`JavaScript`相关的内容。这部分内容并不是代码风格的指导，而是教你如何使用`JavaScript`开发出**可读性强、复用性高，可拓展性高**的软件。

下面的准则中，不是每条都会被人们严格地遵守，甚至只有少数几条能得到广泛的认同。另外，它们只是指导方针而并非是严格的标准，但是这都是由《Clean Code》的作者们的多年经验总结而来。

软件工程技术的历史才50多年，现在我们每天仍在学习新的知识。当软件架构和架构本身一样古老时，可能到时候我们就需要更去遵循更难的规则。现在，就让这些准则作为评估你和你的团队`JavaScript`代码质量的试金石。

另外，知道这些准则并不会立刻让你成为一个更好的软件开发者，而持续多年的遵循准则也不意味着你不会犯错。每一块代码最开始时只是初稿，都会经历一段修改、优化的过程，就像黏土只有经过不断的塑形才会成为艺术品。最终，当我们和同行一起审查代码时，我们会将不好的部分剔除。改进代码并不是为了打败自己，而是为了打败不好的代码！

## 变量

### 使用有意义的、可读的变量名

**Bad:**

```javascript
const yyyymmdstr = moment().format('YYYY/MM/DD');
```

**Good:**

```javascript
const cuurentDate = moment().format('YYYY/MM/DD');
```

### 使用相同的名称来描述同类型的变量

**Bad:**

```javascript
getUserInfo();
getClientData();
getCustomerRecord();
```

**Good:**

```javascript
getUser();
```

### 对于常量进行赋值（可检索）

我们读的代码比我们写的代码要多。确保我们写出来的代码具有很高的可读性和可检索性是很重要的。使用没有命名的常量变量，会让我们的代码变得难以理解，最终会伤害阅读我们代码的人。

**Bad：**

```javascript
// What the heck is 86400000 for?
setTimeout(blastOff, 86400000);
```

**Good:**

```javascript
// Declare them as capitalized `const` globals.
const MILLISECONDS_IN_A_DAY = 86400000;

setTimeout(blastOff, MILLISECONDS_IN_A_DAY);
```

### 使用说明性的中间变量

使用变量来说明传入函数的参数具有什么意义。如下代码中，如果不去看函数具体实现，将很难知道两个参数的含义。

**Bad:**

```javascript
const address = 'One Infinite Loop, Cupertino 95014';
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
saveCityZipCode(address.match(cityZipCodeRegex)[1], address.match(cityZipCodeRegex)[2]);
```

**Good:**

```javascript
const address = 'One Infinite Loop, Cupertino 95014';
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
const [, city, zipCode] = address.match(cityZipCodeRegex) || [];
saveCityZipCode(city, zipCode);
```

### 使用详细的变量名

在回调函数中，使用更易于理解的参数名称。

**Bad:**

```javascript
const locations = ['Austin', 'New York', 'San Francisco'];
locations.forEach((l) => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  // Wait, what is `l` for again?
  dispatch(l);
});
```

**Good:**

```javascript
const locations = ['Austin', 'New York', 'San Francisco'];
locations.forEach((location) => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  dispatch(location);
});
```

### 不要增加不必要的内容

在对象/类当中，如果对象/类的名称已经说明了一些东西，那么在它当中的属性、方法、变量就不要再重复这些东西了。

**Bad:**

```javascript
const Car = {
  carMake: 'Honda',
  carModel: 'Accord',
  carColor: 'Blue'
};

function paintCar(car) {
  car.carColor = 'Red';
}
```

**Good:**

```javascript
const Car = {
  make: 'Honda',
  model: 'Accord',
  color: 'Blue'
};

function paintCar(car) {
  car.color = 'Red';
}
```

### 使用默认参数来代替赋值语句

默认参数比赋值语句更加清晰、简洁。但是要注意的是，当你对参数设置了默认值，函数中只会将`undefiend`替换为默认值，其他的`falsy`值，例如`'' "" null false 0 NaN`都不会被替换。

**Bad:**

```javascript
function createMicrobrewery(name) {
  const breweryName = name || 'Hipster Brew Co.';
  // ...
}
```

**Good**:

```javascript
function createMicrobrewery(breweryName = 'Hipster Brew Co.') {
  // ...
}
```

## 函数

### 函数参数不超过两个

限制函数参数的数量是十分重要的，因为这让你可以更加容易地测试你的函数。当参数超过三个，产生的组合结果将成爆炸式增长。因此你需要在每个单独测试中考虑大量的测试。

一到两个参数是最理想的状况，应该尽量避免三个及以上的参数出现。其余更多的东西应该被整合在一起。通常情况下，如果你的函数具有超过两个参数，那么这个函数一定尝试去做过多的事情。除此之外的大多数情况下，一个高级别的对象作为参数已经足够了。

由于`JavaScript`允许你快速创建对象，而不需要很多样板类，所以当你发现你需要使用很多参数的时候，你可以把他们整合到一个对象中去。

为了在函数中明确它所需要的属性，可以使用ES2015/ES6中的解构语法，这具有如下几个优点：

- 当有人看这个函数的声明/调用时，可以立刻清楚了解到函数使用了哪些属性。
- 解构可以克隆传入函数的参数对象中特定的初始值。这可以防止出现副作用（引用类型）。注意：在解构之后仍是对象/数组的情况下，还是引用类型。
- Linters可以检测到你未使用的属性。

**Bad:**

```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}
```

**Good:**

```javascript
function createMenu({ title, body, buttonText, cancellable }) {
  // ...
}

createMenu({
  title: 'Foo',
  body: 'Bar',
  buttonText: 'Baz',
  cancellable: true
});
```

### 函数只应该做一件事

这是软件工程中最重要的原则。当一个函数做的事情超过一件事，它就会变得难以撰写、测试和推导。当你将函数独立成只实现一个功能时，他们可以轻松地被重构，你的代码也会变得更简洁，可读性更强。

**Bad:**

```javascript
function emailClients(clients) {
  clients.forEach((client) => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
```

**Good:**

```javascript
function emailActiveClients(clients) {
  clients
    .filter(isActiveClient)
    .forEach(email);
}

function isActiveClient(client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```

### 函数名应明确其功能

**Bad:**

```javascript
function addToDate(date, month) {
  // ...
}

const date = new Date();

// It's hard to to tell from the function name what is added
addToDate(date, 1);
```

**Good:**

```javascript
function addMonthToDate(month, date) {
  // ...
}

const date = new Date();
addMonthToDate(1, date);
```

### 函数只应该具有一层抽象

当你的函数具有不止一层的抽象时，通常是因为它做了太多的事情。将这些功能解耦可以让你更容易的重构与测试。

**Bad:**

```javascript
function parseBetterJSAlternative(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(' ');
  const tokens = [];
  REGEXES.forEach((REGEX) => {
    statements.forEach((statement) => {
      // ...
    });
  });

  const ast = [];
  tokens.forEach((token) => {
    // lex...
  });

  ast.forEach((node) => {
    // parse...
  });
}
```

**Good:**

```javascript
function tokenize(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(' ');
  const tokens = [];
  REGEXES.forEach((REGEX) => {
    statements.forEach((statement) => {
      tokens.push( /* ... */ );
    });
  });

  return tokens;
}

function lexer(tokens) {
  const ast = [];
  tokens.forEach((token) => {
    ast.push( /* ... */ );
  });

  return ast;
}

function parseBetterJSAlternative(code) {
  const tokens = tokenize(code);
  const ast = lexer(tokens);
  ast.forEach((node) => {
    // parse...
  });
}
```

### 移除重复的代码

竭尽所能去避免写重复的代码，重复的代码意味着当你想要修改这些代码中的逻辑时，你需要更改很多地方。

想象一下：你正在经营一家饭店，你时刻都在追踪这店里的库存，包括所有番茄、洋葱、大蒜…如果你有很多重复的库存表的话，当你卖出一盘番茄，那你需要将所有的库存表都更新一边。但是如果你只有一个库存表，那每次库存变化时，你只需要改一个地方。

通常重复代码的出现是因为你有多处只有细微差别的功能需要实现，它们中大部分代码是一样的，但是差异之处让你不得不用两个或者更多的独立功能来实现大部分相同的事情。**移除重复代码意味着创造出一个抽象函数/模块/类来实现这些只有细微差别的功能**。

关键之处在于如何正确的抽象，因此你需要遵循`类`章节中列出的`SOLID`原则。错误的抽象比重复的代码更加糟糕，所以一定要谨慎小心。所以当你可以做出好的抽象时，就去将这部分抽象出来。否则使用重复的代码只会让你的项目难以维护，一次小小的逻辑修改，将会让你修改很多地方。

**Bad:**

```javascript
function showDeveloperList(developers) {
  developers.forEach((developer) => {
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
  managers.forEach((manager) => {
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

**Good:**

```javascript
function showEmployeeList(employees) {
  employees.forEach((employee) => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();

    const data = {
      expectedSalary,
      experience
    };

    switch (employee.type) {
      case 'manager':
        data.portfolio = employee.getMBAProjects();
        break;
      case 'developer':
        data.githubLink = employee.getGithubLink();
        break;
    }

    render(data);
  });
}
```

### 使用`Object.assign`来设置对象参数的默认值

**Bad:**

```javascript
const menuConfig = {
  title: null,
  body: 'Bar',
  buttonText: null,
  cancellable: true
};

function createMenu(config) {
  config.title = config.title || 'Foo';
  config.body = config.body || 'Bar';
  config.buttonText = config.buttonText || 'Baz';
  config.cancellable = config.cancellable !== undefined ? config.cancellable : true;
}

createMenu(menuConfig);
```

**Good:**

```javascript
const menuConfig = {
  title: 'Order',
  // User did not include 'body' key
  buttonText: 'Send',
  cancellable: true
};

function createMenu(config) {
  config = Object.assign({
    title: 'Foo',
    body: 'Bar',
    buttonText: 'Baz',
    cancellable: true
  }, config);

  // config now equals: {title: "Order", body: "Bar", buttonText: "Send", cancellable: true}
  // ...
}

createMenu(menuConfig);
```

### 不要在函数参数中使用标记

通常在函数的参数如果包含了信号值，则意味着这个函数做了不止一件事情。如上文中提到的，函数应该专注于一件事，如果你的函数通过一个布尔值来决定不同功能的话，最好将它进行拆分。

**Bad:**

```javascript
function createFile(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**Good:**

```javascript
function createFile(name) {
  fs.create(name);
}

function createTempFile(name) {
  createFile(`./temp/${name}`);
}
```

### 避免副作用（part 1）

如果一个函数做了读取参数，并返回值之外的事情，我们称这个函数具有副作用。副作用可能是对文件进行写入操作、对全局变量进行更改，或者意外的把你得钱全部汇给了陌生人。

当你的确需要函数具有副作用时，比如对文件进行写入操作，你需要注意将写入操作集中起来。避免出现多个函数(类)可以进行对文件进行写入操作，一个就够了。

最主要的一点就是避免常见的陷阱：

- 避免对象之间共享状态。
- 避免使用可变的数据类型。
- 避免分散副作用操作。

**Bad：**

```javascript
// Global variable referenced by following function.
// If we had another function that used this name, now it'd be an array and it could break it.
let name = 'Ryan McDermott';

function splitIntoFirstAndLastName() {
  name = name.split(' ');
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];
```

**Good:**

```javascript
function splitIntoFirstAndLastName(name) {
  return name.split(' ');
}

const name = 'Ryan McDermott';
const newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Ryan McDermott';
console.log(newName); // ['Ryan', 'McDermott'];
```

### 避免副作用（part2）

在`javascript`中，简单类型是值传递，而`object/array`则是引用传递，这就意味着当你在函数中对数组或者对象进行操作时，会对原来的值产生影响，这种影响有时候是符合预期的，但是有时候会造成意料之外的影响，所以尽量避免这种情况。

**Bad:**

```javascript
const addItemToCart = (cart, item) => {
  cart.push({ item, date: Date.now() });
};
```

**Good:**

```javascript
const addItemToCart = (cart, item) => {
  return [...cart, { item, date: Date.now() }];
};
```

### 不要写全局函数

污染全局环境是一件很不好的事，可能会与其他库产生冲突，并且用户在使用你的库，调用你的api之前，他们并不会发现有什么问题。

**Bad:**

```javascript
Array.prototype.diff = function diff(comparisonArray) {
  const hash = new Set(comparisonArray);
  return this.filter(elem => !hash.has(elem));
};
```

**Good:**

```javascript
class SuperArray extends Array {
  diff(comparisonArray) {
    const hash = new Set(comparisonArray);
    return this.filter(elem => !hash.has(elem));
  }
}
```

### 采用函数式编程

`JavaScript`并不是`Haskell`那样的函数式语言，但是它具有函数式的特点。函数式编程更加简洁也更加易于测试。

**Bad:**

```javascript
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

let totalOutput = 0;

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}
```

**Good:**

```javascript
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

const INITIAL_VALUE = 0;

const totalOutput = programmerOutput
  .reduce((acc, { linesOfCode }) => acc + linesOfCode, INITIAL_VALUE);
```

### 封装判断条件

**Bad:**

```javascript
if (fsm.state === 'fetching' && isEmpty(listNode)) {
  // ...
}
```

**Good:**

```javascript
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === 'fetching' && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```

### 避免非判断

**Bad:**

```javascript
function isDOMNodeNotPresent(node) {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // ...
}
```

**Good:**

```javascript
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}
```

### 避免条件判断

避免使用条件判断看起来像是一个不可能的任务。绝大多数人第一次听到这个时，都会说：“如果没有`if`语句，我可什么都做不了！“大多数情况下，我们可以使用多态来解决这个问题。但是随之而来的问题是，我们为什么要这么做？在之前的简洁代码准则中，我们提到了一个函数应当只具有一种功能，保持函数的功能单一性是非常重要的。当你的类、函数中具有`if`语句的时候，说明它并不仅仅在做一件事情，所以记住，**保持功能单一性**。

**Bad:**

```javascript
class Airplane {
  // ...
  getCruisingAltitude() {
    switch (this.type) {
      case '777':
        return this.getMaxAltitude() - this.getPassengerCount();
      case 'Air Force One':
        return this.getMaxAltitude();
      case 'Cessna':
        return this.getMaxAltitude() - this.getFuelExpenditure();
    }
  }
}
```

**Good:**

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

### 避免类型判断（part1）

`JavaScript`是弱类型语言，意味着你的函数可以接受任何类型的参数。有时候你会为这种自由付出一定代价，你需要在函数中进行类型判断。有许多办法可以避免这种代价，首先就是保持函数功能单一性。

**Bad:**

```javascript
function travelToTexas(vehicle) {
  if (vehicle instanceof Bicycle) {
    vehicle.pedal(this.currentLocation, new Location('texas'));
  } else if (vehicle instanceof Car) {
    vehicle.drive(this.currentLocation, new Location('texas'));
  }
}
```

**Good:**

```javascript
function travelToTexas(vehicle) {
  vehicle.move(this.currentLocation, new Location('texas'));
}
```

### 避免类型判断（part2）

如果你正在使用基础类型值（`string integer`），并且你无法使用多态，但是你仍需要进行类型检查，这时你应当考虑使用`TypeScript`，它是`JavaScript`的极佳替代品。

[TypeScript文档](https://www.typescriptlang.org/docs/home.html)

**Bad:**

```javascript
function combine(val1, val2) {
  if (typeof val1 === 'number' && typeof val2 === 'number' ||
      typeof val1 === 'string' && typeof val2 === 'string') {
    return val1 + val2;
  }

  throw new Error('Must be of type String or Number');
}
```

**Good:**

```javascript
function combine(val1, val2) {
  return val1 + val2;
}
```

### 避免过度优化

现代浏览器在底层做了很多优化，所以大部分时候，人为的进行优化仅仅是浪费时间。[这里有几条优化建议](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers)，将优化目标锁定在它们身上，直到它们被修复。

**Bad:**

```javascript
// On old browsers, each iteration with uncached `list.length` would be costly
// because of `list.length` recomputation. In modern browsers, this is optimized.
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**Good:**

```javascript
for (let i = 0; i < list.length; i++) {
  // ...
}
```

### 移除无用代码

无用代码比复制的代码还要糟糕，它完全没理由继续存在，如果它不再被调用，直接删了它吧！如果你还需要，你可以在历史版本中找到它。

## 对象与数据结构

### 使用`getters setters`

使用`getters setters`来访问对象的数据比直接在对象上寻找一个属性要好，可能你会问为什么。原因有如下几条：

- 当你并不仅仅想获取对象属性的时候，还需要进行其他操作的时候时，使用`getter`会让你无需修改之前的代码。
- 使用`setter`给对象添加属性时，类型验证变得更加简单。
- 对内部逻辑进行了封装。
- 在存取时，可以方便的增加日志与错误处理功能。
- 可以懒加载对象属性，即可以从服务端获取数据。

**Bad:**

```javascript
function makeBankAccount() {
  // ...

  return {
    balance: 0,
    // ...
  };
}

const account = makeBankAccount();
account.balance = 100;
```

**Good:**

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
    setBalance,
  };
}

const account = makeBankAccount();
account.setBalance(100);
```

### 使对象拥有私有成员

可以使用闭包来完成这项功能。

**Bad:**

```javascript
const Employee = function(name) {
  this.name = name;
};

Employee.prototype.getName = function getName() {
  return this.name;
};

const employee = new Employee('John Doe');
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: undefined
```

**Good:**

```javascript
function makeEmployee(name) {
  return {
    getName() {
      return name;
    }
  };
}

const employee = makeEmployee('John Doe');
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
```

## 类

### 使用ES6的类

使用ES5类的构造方法，会让这个类的继承、构造、方法都变得可读性很差，如果你需要继承的话，尽量使用ES6的类。当然，如果你需要的对象不是那么复杂，还是使用简单的构造函数来完成这项功能吧。

**Bad:**

```javascript
const Animal = function(age) {
  if (!(this instanceof Animal)) {
    throw new Error('Instantiate Animal with `new`');
  }

  this.age = age;
};

Animal.prototype.move = function move() {};

const Mammal = function(age, furColor) {
  if (!(this instanceof Mammal)) {
    throw new Error('Instantiate Mammal with `new`');
  }

  Animal.call(this, age);
  this.furColor = furColor;
};

Mammal.prototype = Object.create(Animal.prototype);
Mammal.prototype.constructor = Mammal;
Mammal.prototype.liveBirth = function liveBirth() {};

const Human = function(age, furColor, languageSpoken) {
  if (!(this instanceof Human)) {
    throw new Error('Instantiate Human with `new`');
  }

  Mammal.call(this, age, furColor);
  this.languageSpoken = languageSpoken;
};

Human.prototype = Object.create(Mammal.prototype);
Human.prototype.constructor = Human;
Human.prototype.speak = function speak() {};
```

**Good:**

```javascript
class Animal {
  constructor(age) {
    this.age = age;
  }

  move() { /* ... */ }
}

class Mammal extends Animal {
  constructor(age, furColor) {
    super(age);
    this.furColor = furColor;
  }

  liveBirth() { /* ... */ }
}

class Human extends Mammal {
  constructor(age, furColor, languageSpoken) {
    super(age, furColor);
    this.languageSpoken = languageSpoken;
  }

  speak() { /* ... */ }
}
```

### 链式调用方法

这种模式十分有用并且你可以在很多著名的开源库（`jQuery Lodash`）中看见它。他让你的代码变得更加生动简洁，当你使用链式调用时，你会发现你的代码是多么的简洁。

只需要在你类的方法的最后`return this`，你就可以链式调用类的方法了。

**Bad:**

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

const car = new Car('Ford','F-150','red');
car.setColor('pink');
car.save();
```

**Good:**

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

const car = new Car('Ford','F-150','red')
  .setColor('pink')
  .save();
```

### 优先使用组合模式而非继承模式

著名的[《设计模式》](https://en.wikipedia.org/wiki/Design_Patterns)中提到，应尽量使用组合模式而非继承模式，两者都各有好处。最主要的一点是，当你已经习惯于使用继承来解决问题时，试着想一想，组合模式是不是能更好的去解决当前的问题。有时它的确可以。

你可能会问，那我什么时候才能使用继承呢？这取决于你当前的问题。在下面几种情况时，继承会比组合模式更有意义：

- 你的继承代表`属于`关系而非`拥有`关系时，例如 人类 -> 动物，而非 用户 -> 用户详细信息。
- 你可以复用基类的代码时，例如人类可以像动物一样移动。
- 你想通过改变基类代码来全局修改所有派生类时，例如修改动物移动时的卡路里消耗。

**Bad:**

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

**Good:**

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

## SOLID

### 单一职责原则(SRP)

正如本文之前所提到的，“让类产生修改的原因应该不超过一个”。即一个类的职责应当是单一的。

将很多功能函数塞进一个类中的做法往往十分诱人，就像你坐飞机时只需要一个行李箱来装下你所有行李一样。但这将导致你的类无法做到概念上的内聚，并且将经常由于各种各样的原因而被迫进行修改。降低修改类的次数是十分重要的。SRP能如此重要是因为如果你把很多功能函数放到一个类当中，一次小小的功能修改都变得十分困难，而且你很难完全预料这次修改会给其他依赖这个类的模块带来怎样的影响。

**Bad:**

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

**Good:**

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

### 开/闭原则(OCP)

正如Bertrand Meyer所说，“软件实体（类、模块、函数…）应该易拓展、难修改”。

这句话是什么意思呢？OCP告诉你，你应该让使用者很方便的去给你的类增加新功能，而不需要修改已有的代码。

**Bad:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'ajaxAdapter';
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'nodeAdapter';
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    if (this.adapter.name === 'ajaxAdapter') {
      return makeAjaxCall(url).then((response) => {
        // transform response and return
      });
    } else if (this.adapter.name === 'httpNodeAdapter') {
      return makeHttpCall(url).then((response) => {
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

**Good:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'ajaxAdapter';
  }

  request(url) {
    // request and return promise
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'nodeAdapter';
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
    return this.adapter.request(url).then((response) => {
      // transform response and return
    });
  }
}
```

### Liskov替代原则(LSP)

这是一个非常简单概念的术语，简而述知就是“子类对象能替代其超类对象被使用”。（原定义为"If S is a subtype of T, then objects of type T may be replaced with objects of type S (i.e., objects of type S may substitute objects of type T) without altering any of the desirable properties of that program (correctness, task performed, etc.)."）

关于定义的最好解释是，如果你有一个父类和子类，当采用子类来替代父类的时候，不应当产生任何错误结果。

**Bad:**

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
  rectangles.forEach((rectangle) => {
    rectangle.setWidth(4);
    rectangle.setHeight(5);
    const area = rectangle.getArea(); // BAD: Returns 25 for Square. Should be 20.
    rectangle.render(area);
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);
```

**Good:**

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
  shapes.forEach((shape) => {
    const area = shape.getArea();
    shape.render(area);
  });
}

const shapes = [new Rectangle(4, 5), new Rectangle(4, 5), new Square(5)];
renderLargeShapes(shapes);
```

**译者注：**经过查询，该原则的具体含义可以理解为如下几点：

- 子类可以实现父类的抽象方法，但是不能覆盖父类的非抽象方法。
- 子类中可以增加自己特有的方法。
- 当子类的方法重载父类的方法时，方法的前置条件（形参）要比父类方法的输入参数更加宽松。
- 当子类的方法实现父类的抽象方法的时，方法的后置条件（返回值）要比父类的更严格。

### 接口隔离原则(ISP)

`javaScript`不具有接口这一概念，所以这个准则在这里没有其他语言那么严格。然而就算`javaScript`缺少类型系统，它仍然是重要且有关的。

ISP的描述是“客户端不应该被迫依赖于它们不使用的接口”，接口在`javaScript`中是隐式协议，因为`javaScript`是动态类型语言。

从一个例子可以很好的看到ISP在`javaScript`中的作用。当类的初始化需要一个很大的配置对象时，不要求客户端手动设置每个属性是十分有益的，因为大部分时候他们并不需要所有的配置。使配置对象属性可选有效的防止了**胖接口**的产生。

**Bad:**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.animationModule.setup();
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName('body'),
  animationModule() {} // Most of the time, we won't need to animate when traversing.
  // ...
});
```

**Good:**

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
  rootNode: document.getElementsByTagName('body'),
  options: {
    animationModule() {}
  }
});
```

### 依赖反转原则(DIP)

该原则描述了两件必要的事：

- 高层模块不应该依赖于底层模块。它们都应当依赖于抽象接口。
- 抽象接口应当脱离具体实现，具体实现应该依赖于抽象接口。

刚读到这两句话的时候，可能有点生涩难懂，但是如果你曾经写过`AngularJS`，那么你肯定见过通过依赖注入(Dependency Injection)的方式来实现这个原则。虽然它们并不是完全相同的概念，但是DIP让高层模块在不知道底层模块具体实现的情况下可以设置它们，这可以通过DI来实现。这样做的最大的好处是可以让模块之间解耦，耦合真的是一个非常糟糕的开发模式，它会让你的代码变得难以重构。

如前文所说到的，`javaScript`中没有接口，所以依赖的抽象是隐式的约定，也就是说一个对象/类暴露给其他对象/类的方法、属性也是隐式约定的。正如下面例子中的隐式约定是**任何一个`InventoryTracker`中的`request`模块都应当具有`requestItems`方法**。

**Bad:**

```javascript
class InventoryRequester {
  constructor() {
    this.REQ_METHODS = ['HTTP'];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryTracker {
  constructor(items) {
    this.items = items;

    // BAD: We have created a dependency on a specific request implementation.
    // We should just have requestItems depend on a request method: `request`
    this.requester = new InventoryRequester();
  }

  requestItems() {
    this.items.forEach((item) => {
      this.requester.requestItem(item);
    });
  }
}

const inventoryTracker = new InventoryTracker(['apples', 'bananas']);
inventoryTracker.requestItems();
```

**Good:**

```javascript
class InventoryTracker {
  constructor(items, requester) {
    this.items = items;
    this.requester = requester;
  }

  requestItems() {
    this.items.forEach((item) => {
      this.requester.requestItem(item);
    });
  }
}

class InventoryRequesterV1 {
  constructor() {
    this.REQ_METHODS = ['HTTP'];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryRequesterV2 {
  constructor() {
    this.REQ_METHODS = ['WS'];
  }

  requestItem(item) {
    // ...
  }
}

// By constructing our dependencies externally and injecting them, we can easily
// substitute our request module for a fancy new one that uses WebSockets.
const inventoryTracker = new InventoryTracker(['apples', 'bananas'], new InventoryRequesterV2());
inventoryTracker.requestItems();
```



## 测试

测试比实现功能更重要，如果你没有测试或者是测试量不够，那么你每次实现新功能时，你都没法保证没有对其他功能产生破坏。不同的团队对足够的测试定义是不同的，但是具有100%覆盖率（所有声明与分支）的测试用例是你信心的保障。这就意味着你除了需要一个很好的测试框架之外，还需要一个好的[测试覆盖工具](http://gotwarlost.github.io/istanbul/)。

我们没有理由不去写测试用例，市面上有很多优秀的[JS测试框架](http://jstherightway.org/#testing-tools)，赶快选择一个你们团队喜欢的吧。当你选择了一款测试框架，那么你的目标就是致力于为每一个模块/特性编写测试用例。如果你喜欢测试驱动开发(TDD)的开发模式，那就最好了。记住最重要的事情是在你实现任何功能或重构现有功能之前，保证你的测试覆盖到了它们。

### 单一测试

**Bad:**

```javascript
import assert from 'assert';

describe('MakeMomentJSGreatAgain', () => {
  it('handles date boundaries', () => {
    let date;

    date = new MakeMomentJSGreatAgain('1/1/2015');
    date.addDays(30);
    assert.equal('1/31/2015', date);

    date = new MakeMomentJSGreatAgain('2/1/2016');
    date.addDays(28);
    assert.equal('02/29/2016', date);

    date = new MakeMomentJSGreatAgain('2/1/2015');
    date.addDays(28);
    assert.equal('03/01/2015', date);
  });
});
```

**Good:**

```javascript
import assert from 'assert';

describe('MakeMomentJSGreatAgain', () => {
  it('handles 30-day months', () => {
    const date = new MakeMomentJSGreatAgain('1/1/2015');
    date.addDays(30);
    assert.equal('1/31/2015', date);
  });

  it('handles leap year', () => {
    const date = new MakeMomentJSGreatAgain('2/1/2016');
    date.addDays(28);
    assert.equal('02/29/2016', date);
  });

  it('handles non-leap year', () => {
    const date = new MakeMomentJSGreatAgain('2/1/2015');
    date.addDays(28);
    assert.equal('03/01/2015', date);
  });
});
```

## 并发

### 使用`Promise`，不要使用回调

回调函数让代码不够简洁并且会造成大量的嵌套，在`ES6`当中内置了`Promise`,所以使用`Promise`吧。

**Bad:**

```javascript
import { get } from 'request';
import { writeFile } from 'fs';

get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin', (requestErr, response) => {
  if (requestErr) {
    console.error(requestErr);
  } else {
    writeFile('article.html', response.body, (writeErr) => {
      if (writeErr) {
        console.error(writeErr);
      } else {
        console.log('File written');
      }
    });
  }
});
```

**Good:**

```javascript
import { get } from 'request';
import { writeFile } from 'fs';

get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin')
  .then((response) => {
    return writeFile('article.html', response);
  })
  .then(() => {
    console.log('File written');
  })
  .catch((err) => {
    console.error(err);
  });
```

### `Async/Await`比`Promise`更加简洁

`Promise`相比回调函数来说更加简洁直观，但是`ES2017`中的`async/await`更加简洁。[async介绍](http://es6.ruanyifeng.com/#docs/async)。

**Bad:**

```javascript
import { get } from 'request-promise';
import { writeFile } from 'fs-promise';

get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin')
  .then((response) => {
    return writeFile('article.html', response);
  })
  .then(() => {
    console.log('File written');
  })
  .catch((err) => {
    console.error(err);
  });
```

**Good:**

```javascript
import { get } from 'request-promise';
import { writeFile } from 'fs-promise';

async function getCleanCodeArticle() {
  try {
    const response = await get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin');
    await writeFile('article.html', response);
    console.log('File written');
  } catch(err) {
    console.error(err);
  }
}
```

## 错误处理

错误抛出是一件好事，这意味着运行时的错误已经被成功地识别出来，并且通过停止当前堆栈上的函数执行，杀死进程(Node当中)，以及在控制台中展示堆栈追踪信息来通知您。

### 别忽视捕捉到的错误

对程序中捕捉到的错误不做处理是没有任何意义的。将错误信息打印在控制台上也并没有太多效果，因为它很快就会被淹没在控制台的信息当中。如果你在你的代码中用到了`try/catch`，意味着你已经预料到这里可能会有一个错误产生，所以你应该有一个应对方案，至少也是一个代码路径记录。

**Bad:**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}
```

**Good:**

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
### 不要忽视`rejected promise`

同样的，`promise`中的`reject`情况，也应该做相应的处理。

**Bad:**

```javascript
getdata()
  .then((data) => {
    functionThatMightThrow(data);
  })
  .catch((error) => {
    console.log(error);
  });
```

**Good:**

```javascript
getdata()
  .then((data) => {
    functionThatMightThrow(data);
  })
  .catch((error) => {
    // One option (more noisy than console.log):
    console.error(error);
    // Another option:
    notifyUserOfError(error);
    // Another option:
    reportErrorToService(error);
    // OR do all three!
  });
```

## 格式化

决定代码格式如何是一件主观的事情，就算这方面指定的规则很多，也没出现一条你必须遵守的又快严格的规则。不要争论到底该遵循那种格式化规则，这里有[很多工具](https://standardjs.com/rules.html)可以帮助你自动完成格式化，反复争论只会浪费你的金钱和时间。

针对那些没有工具自动格式化的情况，下面列举了一些指导方案：

### 大小写保持一致

`JavaScript`是弱类型语言，所以大小写可以告诉你很多信息。这些规则都是主观制定的，所以你的团队可以选择一条你们想要的。一旦团队内部制定了统一的规范，请保持一致性。

**Bad:**

```javascript
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const songs = ['Back In Black', 'Stairway to Heaven', 'Hey Jude'];
const Artists = ['ACDC', 'Led Zeppelin', 'The Beatles'];

function eraseDatabase() {}
function restore_database() {}

class animal {}
class Alpaca {}
```

**Good:**

```javascript
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const SONGS = ['Back In Black', 'Stairway to Heaven', 'Hey Jude'];
const ARTISTS = ['ACDC', 'Led Zeppelin', 'The Beatles'];

function eraseDatabase() {}
function restoreDatabase() {}

class Animal {}
class Alpaca {}
```

### 函数调用与函数定义应该放在较近的位置

如果一个函数调用了另一个函数，请让这些函数定义在代码中距离保持较近。最好让函数调用者在被调用函数的上方。（我们阅读代码喜欢从上往下阅读，就像看新闻一样，所以这样做的好处是易于代码阅读）。

**Bad:**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  lookupPeers() {
    return db.lookup(this.employee, 'peers');
  }

  lookupManager() {
    return db.lookup(this.employee, 'manager');
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

**Good:**

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
    return db.lookup(this.employee, 'peers');
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  lookupManager() {
    return db.lookup(this.employee, 'manager');
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

##代码注释

### 只在业务逻辑复杂的地方进行注释

代码注释并不是必须的，好的代码易于阅读，不需要进行注释。只有当业务逻辑复杂到一定程度之后，才需要注释。

**Bad:**

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
    hash = ((hash << 5) - hash) + char;
    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**Good:**

```javascript
function hashIt(data) {
  let hash = 0;
  const length = data.length;

  for (let i = 0; i < length; i++) {
    const char = data.charCodeAt(i);
    hash = ((hash << 5) - hash) + char;

    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

### 不要在代码库中遗留被注释掉的代码

版本控制的存在是有原因的，让老代码留在历史记录当中。

**Bad:**

```javascript
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```

**Good:**

```javascript
doStuff();
```

### 不要遗留版本更新注释

记住，使用版本控制功能。我们不需要废弃代码、注释代码、尤其是版本更新说明。使用`git log`来获取更新信息。

**Bad:**

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

**Good:**

```javascript
function combine(a, b) {
  return a + b;
}
```

### 避免位置标记

位置标记通常只会增加麻烦，适当的采用缩进和格式化来构造视觉上的结构。

**Bad:**

```javascript
////////////////////////////////////////////////////////////////////////////////
// Scope Model Instantiation
////////////////////////////////////////////////////////////////////////////////
$scope.model = {
  menu: 'foo',
  nav: 'bar'
};

////////////////////////////////////////////////////////////////////////////////
// Action setup
////////////////////////////////////////////////////////////////////////////////
const actions = function() {
  // ...
};
```

**Good:**

```javascript
$scope.model = {
  menu: 'foo',
  nav: 'bar'
};

const actions = function() {
  // ...
};
```

