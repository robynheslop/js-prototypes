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

We can create a new marine animals object using a function. The function we'll use to create our objects will be referred to as the _constructor function_.

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

Creating a MarineAnimal class in ES5:

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
```

Creating a MarineAnimal class in ES6:

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
```

Let's create a specific class for a Frogfish. Frogfish are know for lying very still and striking their prey extremely rapidly. They can also walk on their legs. Cute! 😍😍😍

![frogfish](./assets/frogfish.jpg)

```javascript
function FrogFish(name, food, energy, strikeSpeed) {
  this.name = name;
  this.food = food;
  this.energy = energy;
  this.strikeSpeed = strikeSpeed;
}

FrogFish.prototype.snackTime = function (energyIncrease) {
  console.log(`${this.name} loves to eat ${this.food}`);
  this.energy += energyIncrease;
};
FrogFish.prototype.justKeepSwimming = function (energyDecrease) {
  console.log(`${this.name} is swimming`);
  this.energy -= energyDecrease;
};
FrogFish.prototype.walk = function(energyDecrease){
  console.log('Just going for a stroll...');
    this.energy -= energyDecrease;
} 
};
FrogFish.prototype.strike = function(energyDecrease){
  console.log(`${this.name}, ATTACK!!`);
    this.energy -= energyDecrease;
}
const alanTheFrogFish = new FrogFish('Alan the Frogfish','cardinal fish',3000,'6ms'); 
```

What about other types of marine animal? If we wanted to create another marine animal, like a `HermitCrab`, We'd have to create new `HermitCrab` class and duplicate all of the logic from `MarineAnimal` and add the specific properties like we did for the `FrogFish`.

```javascript
function FrogFish(name, food, energy, strikeSpeed) {}
function HermitCrab(name, food, energy, shellColor, singingRange){}
```

How can we create a new marine animal, without having to repeatedly add all of the common properties to each new class?

```javascript
function FrogFish(name, food, energy, strikeSpeed) {
  this.name = name;
  this.food = food;
  this.energy = energy;
  this.strikeSpeed = strikeSpeed;
}

FrogFish.prototype.snackTime = function (energyIncrease) {
  console.log(`${this.name} loves to eat ${this.food}`);
  this.energy += energyIncrease;
};
FrogFish.prototype.justKeepSwimming = function (energyDecrease) {
  console.log(`${this.name} is swimming`);
  this.energy -= energyDecrease;
};
FrogFish.prototype.walk = function(energyDecrease){
  console.log('Just going for a stroll...');
    this.energy -= energyDecrease;
};
FrogFish.prototype.strike = function(energyDecrease){
  console.log(`${this.name}, ATTACK!!`);
    this.energy -= energyDecrease;
};
const alanTheFrogFish = new FrogFish('Alan the Frogfish','cardinal fish',3000,'6ms'); 
```

The `FrogFish` constructor takes in 4 arguments. Because we are invoking the `FrogFish` function with the `new` keyword, we will get a new `this` object. Finally, we need to invoke the `MarineAnimal` function so that our `FrogFish` will have a `name`, `food`, `energy` as well as being able to have `snackTime` and `justKeepSwimming`.  

This means that when we invoke `FrogFish` we need to also invoke `MarineAnimal` too.

```javascript
function MarineAnimal(name, food, energy) {
  this.name = name;
  this.food = food;
  this.energy = energy;
}

function FrogFish(name, food, energy, strikeSpeed) {
  // Invokes the MarineAnimal function and binds FrogFish's `this` to `this` in the `MarineAnimal` function. 
  MarineAnimal.call(this,name,food,energy); 
  this.strikeSpeed = strikeSpeed;
}
const alanTheFrogFish = new FrogFish('Alan the Frogfish','cardinal fish',3000,'6ms'); 
alanTheFrogFish.snackTime();  // Uncaught TypeError: alanTheFrogFish.snackTime is not a function 
```

We need to make the methods on the `MarineAnimal` prototype available to the `FrogFish`. We can use `Object.create` to delegate property lookups on `FrogFish` to `MarineAnimal.prototype`.

```javascript
function MarineAnimal(name, food, energy) {
  this.name = name;
  this.food = food;
  this.energy = energy;
}

MarineAnimal.prototype.snackTime = function (energyIncrease) {
  console.log(`${this.name} loves to eat ${this.food}`);
  this.energy += energyIncrease;
};
MarineAnimal.prototype.justKeepSwimming = function (energyDecrease) {
  console.log(`${this.name} is swimming`);
  this.energy -= energyDecrease;
};

function FrogFish(name, food, energy, strikeSpeed) {
  // Invokes the MarineAnimal function and binds FrogFish's `this` to `this` in the `MarineAnimal` function. 
  MarineAnimal.call(this,name,food,energy); 
  this.strikeSpeed = strikeSpeed;
}
// Any failed lookups on FrogFish will be delegated to the MarineAnimal prototype.
FrogFish.prototype = Object.create(MarineAnimal.prototype);

const alanTheFrogFish = new FrogFish('Alan the Frogfish','cardinal fish',3000,'6ms'); 
alanTheFrogFish.snackTime(); // Alan the FrogFish loves to eat cardinal fish
// Does the `snackTime` property exist on alanTheFrogFish? Nope.
// Does the `snackTime` property exist on the `FrogFish.prototype`? Nope.
// Does the `snackTime` property exist on the `MarineAnimal.prototype`? Yassss!
```

Why does the `prototype` of `FrogFish` get checked? This is because we created `alanTheFrogFish` using the `new` keyword. Behind the scenes, the Javascript rutime delegates all lookups on the `this` object created to the prototype of the function that was invoked `new FrogFish(....)`, i.e. `FrogFish.prototype`.

```javascript
function FrogFish(name, food, energy, strikeSpeed) {
  // this = Object.create(FrogFish.prototype);
  MarineAnimal.call(this,name,food,energy); 
  this.strikeSpeed = strikeSpeed;
  // return this;
}
```

Why does the `prototype` of `MarineAnimal` get checked? This is because we overwrote the `prototype` of `FrogFish` with the `MarineAnimal.protoype`. This means that failed lookups on `FrogFish` will be delegated to the `MarineAnimal.protoype` object.

```javascript
FrogFish.prototype = Object.create(MarineAnimal.prototype);
```

How can we share methods specific to `FrogFish` with all instances of `FrogFish`?

```javascript
function FrogFish(name, food, energy, strikeSpeed) {
  MarineAnimal.call(this,name,food,energy); 
  this.strikeSpeed = strikeSpeed;
}
FrogFish.prototype = Object.create(MarineAnimal.prototype);

FrogFish.prototype.walk = function(energyDecrease){
  console.log('Just going for a stroll...');
    this.energy -= energyDecrease;
};
FrogFish.prototype.strike = function(energyDecrease){
  console.log(`${this.name}, ATTACK!!`);
    this.energy -= energyDecrease;
};
```

We can access an instance's `constructor` function by using `instance.constructor`.  

```javascript
function MarineAnimal(name, food, energy) {
  this.name = name;
  this.food = food;
  this.energy = energy;
}

const sheldonTheSeahorse = new MarineAnimal('Sheldon the Seahorse', 'shrimps', 10);
// Log the constructor property for sheldonTheSeahorse 
console.log(sheldonTheSeahorse.constructor); // Function: MarineAnimal
```

So what will logging the `instance.constructor` property of `FrogFish` result in?  

```javascript
function MarineAnimal(name, food, energy) {
  this.name = name;
  this.food = food;
  this.energy = energy;
}

function FrogFish(name, food, energy, strikeSpeed) {
  // Invokes the MarineAnimal function and binds FrogFish's `this` to `this` in the `MarineAnimal` function. 
  MarineAnimal.call(this,name,food,energy); 
  this.strikeSpeed = strikeSpeed;
}
// Any failed lookups on FrogFish will be delegated to the MarineAnimal prototype.
FrogFish.prototype = Object.create(MarineAnimal.prototype);

const alanTheFrogFish = new FrogFish('Alan the Frogfish','cardinal fish',3000,'6ms'); 
console.log(alanTheFrogFish.constuctor); // Function: MarineAnimal
// Does the `constructor` property exist on alanTheFrogFish? Nope.
// Does the `constructor` property exist on the `FrogFish.prototype`? No, it doesn't, we overwrote the `FrogFish.prototype` with an object that delegates lookups to `MarineAnimal.protoype`.
// Does the `constructor` property exist on the `MarineAnimal.prototype`? Yassss!
```

We can correct this by setting the correct `constructor` property on the `FrogFish.prototype` after we overwrote it.

```javascript
function FrogFish(name, food, energy, strikeSpeed) {
  MarineAnimal.call(this,name,food,energy); 
  this.strikeSpeed = strikeSpeed;
}
FrogFish.prototype = Object.create(MarineAnimal.prototype);
FrogFish.prototype.constructor = FrogFish;

const alanTheFrogFish = new FrogFish('Alan the Frogfish','cardinal fish',3000,'6ms'); 
console.log(alanTheFrogFish.constuctor); // Function: FrogFish
```

So how can we use this approach to create a `SeaHorse` marine animal subclass?

```javascript
function SeaHorse(name, food, energy, swimmingSpeed) {
  MarineAnimal.call(this,name,food,energy); 
  this.swimmingSpeed = swimmingSpeed;
}

SeaHorse.prototype = Object.create(MarineAnimal.prototype);
SeaHorse.prototype.constructor = SeaHorse;

SeaHorse.prototype.lookAfterChildren = function () {
  console.log(`Hand 'em over, wifey!`);
}

const sheldonTheSeaHorse = new SeaHorse('Sheldon the Seahorse', 'shrimps', 10, `1 cm/hour`);
console.log(sheldonTheSeaHorse.constuctor); // Function: SeaHorse
```

How can we achieve what we've just done using ES6 classes?

In ES5 we had:

```javascript
function MarineAnimal(name, food, energy) {
  this.name = name;
  this.food = food;
  this.energy = energy;
}

MarineAnimal.prototype.snackTime = function (energyIncrease) {
  console.log(`${this.name} loves to eat ${this.food}`);
  this.energy += energyIncrease;
};
MarineAnimal.prototype.justKeepSwimming = function (energyDecrease) {
  console.log(`${this.name} is swimming`);
  this.energy -= energyDecrease;
};

const alanTheFrogFish = new MarineAnimal('Alan the Frogfish','cardinal fish',3000); 
```

Using ES6 `class` syntax, we have:

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

const alanTheFrogFish = new MarineAnimal('Alan the Frogfish','cardinal fish',3000); 
```

Let's see how we can refactor the `FrogFish` sub-class from ES5 into an ES6 `class`.

In ES5 we had:

```javascript
function FrogFish(name, food, energy, strikeSpeed) {
  MarineAnimal.call(this,name,food,energy); 
  this.strikeSpeed = strikeSpeed;
}
FrogFish.prototype = Object.create(MarineAnimal.prototype);
FrogFish.prototype.constructor = FrogFish;
FrogFish.prototype.walk = function(energyDecrease){
  console.log('Just going for a stroll...');
    this.energy -= energyDecrease;
};
FrogFish.prototype.strike = function(energyDecrease){
  console.log(`${this.name}, ATTACK!!`);
    this.energy -= energyDecrease;
};
```

In ES6 `class` syntax, we have:

```javascript
class FrogFish {
  constructor(name, food, energy, strikeSpeed) {
    this.strikeSpeed = strikeSpeed;
  }
  walk (energyDecrease) {
    console.log('Just going for a stroll...');
    this.energy -= energyDecrease;
  };
  strike (energyDecrease) {
    console.log(`${this.name}, ATTACK!!`);
    this.energy -= energyDecrease;
  };
}
```

In ES6, a class can extend another class using the `extends` syntax:

```javascript
class SubClass extends BaseClass{
  // ...
}
```

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

class FrogFish extends MarineAnimal {
  constructor(name, food, energy, strikeSpeed) {
    this.strikeSpeed = strikeSpeed;
  }
  walk (energyDecrease) {
    console.log('Just going for a stroll...');
    this.energy -= energyDecrease;
  };
  strike (energyDecrease) {
    console.log(`${this.name}, ATTACK!!`);
    this.energy -= energyDecrease;
  };
}
```

In ES5, we ensured that `FrogFish` had the `name`,`food` and `energy` properties by invoking the `MarineAnimal.call(this,name,food,energy)` in the `FrogFish` constructor function. In ES6, we can invoke the constructor of the base-class by  invoking the `super` function in the `constructor` function of the sub-class.

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

class FrogFish extends MarineAnimal {
  constructor(name, food, energy, strikeSpeed) {
    super(name, food, energy);
    this.strikeSpeed = strikeSpeed;
  }
  walk (energyDecrease) {
    console.log('Just going for a stroll...');
    this.energy -= energyDecrease;
  };
  strike (energyDecrease) {
    console.log(`${this.name}, ATTACK!!`);
    this.energy -= energyDecrease;
  };
}
```

Here's another example of inheritance:

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
- [Javascript inheritance and the prototype chain](https://ui.dev/javascript-inheritance-and-the-prototype-chain/)
