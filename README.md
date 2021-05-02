# Prototypes in JavaScript

## The traditional way of creating an object

```javascript
const doryTheBlueTang = {};

doryTheBlueTang.name = "Dory the Blue Tang";
doryTheBlueTang.food = "algae";
doryTheBlueTang.energy = 200000;

doryTheBlueTang.snackTime = function (energyIncrease) {
  console.log(`${this.name} loves to eat ${this.food}`);
  this.energy += energyIncrease;
};

doryTheBlueTang.justKeepSwimming = function (energyDecrease) {
  console.log(`${this.name} is swimming`);
  this.energy -= energyDecrease;
};

doryTheBlueTang.justKeepSwimming(); // 'Dory the Blue Tang is swimming'
```

This can become tedious if we want to create several marine animals in our application.

To avoid this, we can encapsulate this logic into a function that we can call as we need.

## Functional Instantiation

We can create a new marine animals object using a function. This is called function constructor pattern.

```javascript
function MarineAnimal(name, food, energy) {
  const marineAnimal = {};
  marineAnimal.name = name;
  marineAnimal.food = food;
  marineAnimal.energy = energy;
  marineAnimal.snackTime = function (energyIncrease) {
    console.log(`${this.name} loves to eat ${this.food}`);
    this.energy += energyIncrease;
  };
  marineAnimal.justKeepSwimming = function (energyDecrease) {
    console.log(`${this.name} is swimming`);
    this.energy -= energyDecrease;
  };

  return marineAnimal;
}

const doryTheBlueTang = marineAnimal("Dory the Blue Tang", "algae", 50000);
const crushTheTurtle = marineAnimal("Crush the Turtle", "seagrass", 3);

crushTheTurtle.snackTime(5); // 'Crush the Turtle loves to eat seagrass'
doryTheBlueTang.justKeepSwimming(18); // 'Dory the Blue Tang is swimming'
```

![turtle](./assets/crush-the-turtle.png)

Both crushTheTurtle and doryTheBlueTang both have their own copy of the methods snackTime and justKeepSwimming.

This takes up more space in memory and makes each marine animal object bigger than necessary.

Instead, they could share these methods.

## Functional Instantiation with Shared Methods

```javascript
const marineAnimalMethods = {
  snackTime: function (energyIncrease) {
    console.log(`${this.name} loves to eat ${this.food}`);
    this.energy += energyIncrease;
  },
  justKeepSwimming: function (energyDecrease) {
    console.log(`${this.name} is swimming`);
    this.energy -= energyDecrease;
  },
};

function MarineAnimal(name, food, energy) {
  const marineAnimal = {};
  marineAnimal.name = name;
  marineAnimal.food = food;
  marineAnimal.energy = energy;
  marineAnimal.snackTime = marineAnimalMethods.snackTime;
  marineAnimal.justKeepSwimming = marineAnimalMethods.justKeepSwimming;

  return marineAnimal;
}

const nemoTheClownfish = MarineAnimal(
  "Nemo the Clownfish",
  "dunno, where is my dad?",
  10
);

nemoTheClownfish.snackTime(6); // Nemo loves to eat dunno, where's my dad?
```

![nemo](./assets/nemo.jpg)

## Object.create()

Object.create allows you to create an object which will delegate to another object on failed property lookups.

## Functional Instantiation with Shared Methods and Object.create

```javascript
const marineAnimalMethods = {
  snackTime: function (energyIncrease) {
    console.log(`${this.name} loves to eat ${this.food}`);
    this.energy += energyIncrease;
  },
  justKeepSwimming: function (energyDecrease) {
    console.log(`${this.name} is swimming`);
    this.energy -= energyDecrease;
  },
};

const hankTheOctopus = Object.create(marineAnimalMethods);
hankTheOctopus.name = "Hank the Octopus";
hankTheOctopus.food = "shrimp (sooo unsustainable)";

hankTheOctopus.snackTime(10);
// looks for snackTime method on the hankTheOctopus object, which will fail
// because of Object.create, JS will delegate to the marineAnimalMethods object
// this lookup for snackTime() will be sucessful.
```

![octopus](./assets/octopus.jpg)

An advantage of Object.create over Functional Instantiation is that in the event that you add a new method to your `marineAnimalMethods` object, all instances created with `Object.create(marineAnimalMethods)` will have access to the new method.

This works, but it's not ideal to keep our methods on a seperate object to share it across our other objects.

## What is the prototype?

```javascript
function fishAreFriends() {}
console.log(fishAreFriends.prototype); // {}
```

Every function in JavaScript has a prototype property that references an object.

What about adding our shared methods to a function's prototype, instead of managing them in a seperate methods object?

## Prototypal Instantiation

```javascript
function MarineAnimal(name, food, energy) {
  const marineAnimal = Object.create(MarineAnimal.prototype);
  marineAnimal.name = name;
  marineAnimal.food = food;
  marineAnimal.energy = energy;
  return marineAnimal;
}

MarineAnimal.prototype.snackTime = function (energyIncrease) {
  console.log(`${this.name} loves to eat ${this.food}`);
  this.energy += energyIncrease;
};
MarineAnimal.prototype.justKeepSwimming = function (energyDecrease) {
  console.log(`${this.name} is swimming`);
  this.energy -= energyDecrease;
};

const bruceTheShark = MarineAnimal(
  "Bruce the Shark",
  "scuba divers that touch the reef",
  22
);
bruceTheShark.snackTime(4); // Bruce the Shark loves to eat scuba divers that touch the reef
```

![sharks](./assets/bruce.jpeg)

## Pseudoclassical Instantiation

```javascript
function MarineAnimal(name, food, energy) {
  const marineAnimal = Object.create(MarineAnimal.prototype);
  marineAnimal.name = name;
  marineAnimal.food = food;
  marineAnimal.energy = energy;
  return marineAnimal;
}
```

In the MarineAnimal constructor function we created, the most important parts were

- Creating the object
- Returning it

By using the `new` keyword, we can remove these steps as they are done implicitly - the object that is created and returned is `this`.

```javascript
function MarineAnimal(name, food, energy) {
  //  const marineAnimal = Object.create(MarineAnimal.prototype);

  //  marineAnimal.name = name;
  this.name = name;
  //  marineAnimal.food = food;
  this.food = food;
  //  marineAnimal.energy = energy;
  this.energy = energy;

  //  return marineAnimal;
}

MarineAnimal.prototype.snackTime = function (energyIncrease) {
  console.log(`${this.name} loves to eat ${this.food}`);
  this.energy += energyIncrease;
};
MarineAnimal.prototype.justKeepSwimming = function (energyDecrease) {
  console.log(`${this.name} is swimming`);
  this.energy -= energyDecrease;
};

const destinyTheWhaleShark = new MarineAnimal(
  "Destiny the Whale Shark",
  "aaaaall the plankton",
  90
);
destinyTheWhaleShark.justKeepSwimming(2);
```

![whale-shark](./assets/whale-shark.jpeg)

## ES6 Class Constructor

Simply syntactial sugar over pseudoclassical instantiation.

```javascript
class MarineAnimal {
  constructor(name, food, energy) {
    this.name = name;
    this.food = food;
    this.energy = energy;
  }
  snackTime(energyIncrease) {
    console.log(`${this.name} loves to eat ${this.food}`);
    this.energy += energyIncrease;
  }
  justKeepSwimming(energyDecrease) {
    console.log(`${this.name} is swimming`);
    this.energy -= energyDecrease;
  }
}
const harleyTheHarlequinShrimp = new MarineAnimal(
  "Harley the Harlequin Shrimmp",
  "starfish",
  3
);
harleyTheHarlequinShrimp.snackTime(6);
```

![shrimp](./assets/harlequin-shrimp.jpeg)

This Class definition transpiles to the following ES5 code:

```javascript
"use strict";

function _classCallCheck(instance, Constructor) {
  if (!(instance instanceof Constructor)) {
    throw new TypeError("Cannot call a class as a function");
  }
}

function _defineProperties(target, props) {
  for (var i = 0; i < props.length; i++) {
    var descriptor = props[i];
    descriptor.enumerable = descriptor.enumerable || false;
    descriptor.configurable = true;
    if ("value" in descriptor) descriptor.writable = true;
    Object.defineProperty(target, descriptor.key, descriptor);
  }
}

function _createClass(Constructor, protoProps, staticProps) {
  if (protoProps) _defineProperties(Constructor.prototype, protoProps);
  if (staticProps) _defineProperties(Constructor, staticProps);
  return Constructor;
}

var MarineAnimal = /*#__PURE__*/ (function () {
  function MarineAnimal(name, food, energy) {
    _classCallCheck(this, MarineAnimal);

    this.name = name;
    this.food = food;
    this.energy = energy;
  }

  _createClass(MarineAnimal, [
    {
      key: "snackTime",
      value: function snackTime(energyIncrease) {
        console.log("".concat(this.name, " loves to eat ").concat(this.food));
        this.energy += energyIncrease;
      },
    },
    {
      key: "justKeepSwimming",
      value: function justKeepSwimming(energyDecrease) {
        console.log("".concat(this.name, " is swimming"));
        this.energy -= energyDecrease;
      },
    },
  ]);

  return MarineAnimal;
})();
```

## Static Methods

```javascript
function MarineAnimal(name, food, energy) {
  const marineAnimal = {};
  marineAnimal.name = name;
  marineAnimal.food = food;
  marineAnimal.energy = energy;
  marineAnimal.snackTime = function (energyIncrease) {
    console.log(`${this.name} loves to eat ${this.food}`);
    this.energy += energyIncrease;
  };
  marineAnimal.justKeepSwimming = function (energyDecrease) {
    console.log(`${this.name} is swimming`);
    this.energy -= energyDecrease;
  };

  return marineAnimal;
}

function whoIsTheHungriest(arrayOfFish) {
  const arrayOfFishSortedByHunger = arrayOfFish.sort((fish1, fish2) => {
    return fish1.energy - fish2.energy;
  });
  return arrayOfFishSortedByHunger[0].name;
}

const destinyTheWhaleShark = MarineAnimal(
  "Destiny the Whale Shark",
  "aaaaall the plankton",
  90
);
const bruceTheShark = MarineAnimal(
  "Bruce the Shark",
  "scuba divers that touch the reef",
  22
);

whoIsTheHungriest([destinyTheWhaleShark, bruceTheShark]); // 'Bruce the Shark'
```

What about methods that are important to the Class, but not to the individual instances?

```javascript
class MarineAnimal {
  constructor(name, food, energy) {
    this.name = name;
    this.food = food;
    this.energy = energy;
  }
  snackTime(energyIncrease) {
    console.log(`${this.name} loves to eat ${this.food}`);
    this.energy += energyIncrease;
  }
  justKeepSwimming(energyDecrease) {
    console.log(`${this.name} is swimming`);
    this.energy -= energyDecrease;
  }
  static whoIsTheHungriest(arrayOfFish) {
    const arrayOfFishSortedByHunger = arrayOfFish.sort((fish1, fish2) => {
      return fish1.energy - fish2.energy;
    });
    return arrayOfFishSortedByHunger[0].name;
  }
}
```

This Class definition transpiles to the following ES5 code:

```javascript
"use strict";

function _classCallCheck(instance, Constructor) {
  if (!(instance instanceof Constructor)) {
    throw new TypeError("Cannot call a class as a function");
  }
}

function _defineProperties(target, props) {
  for (var i = 0; i < props.length; i++) {
    var descriptor = props[i];
    descriptor.enumerable = descriptor.enumerable || false;
    descriptor.configurable = true;
    if ("value" in descriptor) descriptor.writable = true;
    Object.defineProperty(target, descriptor.key, descriptor);
  }
}

function _createClass(Constructor, protoProps, staticProps) {
  if (protoProps) _defineProperties(Constructor.prototype, protoProps);
  if (staticProps) _defineProperties(Constructor, staticProps);
  return Constructor;
}

var MarineAnimal = /*#__PURE__*/ (function () {
  function MarineAnimal(name, food, energy) {
    _classCallCheck(this, MarineAnimal);

    this.name = name;
    this.food = food;
    this.energy = energy;
  }

  _createClass(
    MarineAnimal,
    [
      {
        key: "snackTime",
        value: function snackTime(energyIncrease) {
          console.log("".concat(this.name, " loves to eat ").concat(this.food));
          this.energy += energyIncrease;
        }
      },
      {
        key: "justKeepSwimming",
        value: function justKeepSwimming(energyDecrease) {
          console.log("".concat(this.name, " is swimming"));
          this.energy -= energyDecrease;
        }
      }
    ],
    [
      {
        key: "whoIsTheHungriest",
        value: function whoIsTheHungriest(arrayOfFish) {
          var arrayOfFishSortedByHunger = arrayOfFish.sort(function (
            fish1,
            fish2
          ) {
            return fish1.energy - fish2.energy;
          });
          return arrayOfFishSortedByHunger[0].name;
        }
      }
    ]
  );

  return MarineAnimal;
})();

```

```javascript
const destinyTheWhaleShark = new MarineAnimal(
  "Destiny the Whale Shark",
  "aaaaall the plankton",
  90
);
const bruceTheShark = new MarineAnimal(
  "Bruce the Shark",
  "scuba divers that touch the reef",
  22
);

console.log(destinyTheWhaleShark.whoIsTheHungriest([destinyTheWhaleShark, bruceTheShark])) // Uncaught TypeError: destinyTheWhaleShark.whoIsTheHungriest is not a function
console.log(MarineAnimal.whoIsTheHungriest([destinyTheWhaleShark, bruceTheShark])); // 'Bruce the Shark'
```

We are adding `whoIsTheHungriest` as a property on the *function* `MarineAnimal`. However, the properties `snackTime` and `justKeepSwimming` are added to the *prototype* of the `MarineAnimal` function, which the constructed instances of `MarineAnimal` delegate to.

> Remember when constructing an object using the `new` keyword, the object that is returned delegates to the prototype of the function created it.

```javascript
function FooBar() {
    this.message = 'foofoo';
    // the function returns `this`
    // `this` delegates property lookups to the prototype of FooBar
}
FooBar.prototype.printMessage = function () {
    console.log(this.message)
}
const foo = new FooBar();
foo.printMessage(); 
// print message does not exist on the object foo
// however, it does exist on the prototype of the function created it i.e. the prototype of FooBar.
```

## Inheritance

```javascript
class Animal {
    constructor(name) {
        this.name = name
    }
    makeNoise() {
        throw Error('Not implemented');
    }
}

class Dog extends Animal {
    constructor(name, favouriteHuman) {
        super(name);
        this.favouriteHuman = favouriteHuman;
    }
    makeNoise() {
        console.log('woof')
    }
}
```

![best dog ever](./assets/dog.jpg)

```javascript

class Cat extends Animal {
    constructor(name, leastOffensiveHuman) {
        super(name);
        this.leastOffensiveHuman = leastOffensiveHuman;
    }
     makeNoise() {
        console.log('meow')
    }
}
```

![angry-cat](./assets/angry-cat.jpg)

## References

- [Babeljs.io repl](https://babeljs.io/repl#?browsers=defaults%2C%20not%20ie%2011%2C%20not%20ie_mob%2011&build=&builtIns=false&corejs=3.6&spec=false&loose=false&code_lz=Q&debug=false&forceAllTransforms=true&shippedProposals=false&circleciRepo=&evaluate=true&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=env%2Cstage-3&prettier=true&targets=&version=7.14.0&externalPlugins=)

- [A beginner's guide to Javascript's prototypes](https://ui.dev/beginners-guide-to-javascript-prototype/)
