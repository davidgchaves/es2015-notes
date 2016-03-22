# ES2015 Notes

## Resources

- [ECMAScript® 2015 Language Specification](http://www.ecma-international.org/ecma-262/6.0/)
- [ES6 Features](http://es6-features.org/)
- [ES2015 Compatibility Table](https://kangax.github.io/compat-table/es6/)
- [Babel](http://babeljs.io/)


## 1. Block Scope

- A `block` is anything within curly braces `{ .... }`.

### `let`, `const` and `var`

- `let` and `const` honor **block scope**.
- `var` works exactly the same as before, no **block scope** for `var`.
- Variables declared with `let` and `const` are not hoisted to the top of the **block scope**.

## 2. Default parameters

- Allow us to specify default values for parameters.
- The default parameter value kicks in if:
	- the parameter is not passed
	- the passed value is `undefined`
- The default parameter value **does not** kick in, if:
	- the passed value is `null`


## 3. Rest parameters and the Spread operator

The `spread` operator manifests inside a function as a **true** array (unlike the `arguments` variable).

```javascript
const func = (...iAmATrueArray) {
  iAmATrueArray.join(' ');  // it works
};
```

## 4. Template Strings

- A new type of string literal.
- Supports:
	- multiline strings
	- expression interpolation using `${ .... }`

### Tagged template strings

- Useful when doing `GraphQL`.

This code:

```javascript
const foo = 'xxx';
const bar = 'yyy';

const tagging = (tokens, ...values) => {
  return tokens[1] + values[1] + tokens[0] + values[0];
};

tagging`foo is ${foo} and bar is ${bar}`;
```

produces the string:

```javascript
' and bar is yyyfoo is xxx'
```

## 5. Additions to the String Object

### Predicates

- `myString.startsWith('foo')`
- `myString.endsWith('bar')`
- `myString.includes('baz')`

### Static

- `String.fromCodePoint(unicodeCodePoint)`

## 6. Additions to the Number Object

### Static predicates

- `Number.isFinite(15.34)`
- `Number.isInteger(4)`
- `Number.isSafeInteger(4)`
- **GOTCHA**: `Number.isInteger(4.000)` and `Number.isSafeInteger(4.000)` return `true`

### New static functions

#### Old `isNaN()` vs new `Number.isNaN()`

The new `Number.isNaN()` **does not** coerce.

```javascript
isNaN('NaN')        // true  (OUCH!) <-- Coercion
Number.isNaN('NaN') // false (YAY!)  <-- No coercion
isNaN(NaN)          // true
Number.isNaN(NaN)   // true
```

### Old `parseInt()` vs new `Number.parseInt()`

Work exactly the same way.

```javascript
parseInt('3')         // 3
Number.parseInt('3')  // 3
```

### Old `parseFloat()` vs new `Number.parseFloat()`

Work exactly the same way.

```javascript
parseFloat('3.14')         // 3.14
Number.parseFloat('3.14')  // 3.14
```

## 7. Additions to the Array Object

### `Array.of()`

```javascript
Array.of(1, '3', {foo: 'bar'})
// [1, "3", {foo: 'bar'}]
```

The main difference between the `Array.of()` function and the `Array` `new()` constructor is:

```javascript
Array.of(3)  // [3]
new Array(3) // [undefined, undefined, undefined]
```

### `Array.from()`

```javascript
() => {
  arguments;              // Array-like object
  Array.from(arguments);  // Real array object
};
```

### `.find(predicate)`

```javascript
const fruits = [
  { spanish: 'Naranja', english: 'Orange' },
  { spanish: 'Manzana', english: 'Apple' }
];

const findFruitInSpanish = (inSpanish) => {
  return fruits.find((fruit) => fruit.spanish === inSpanish);
};

findFruitInSpanish('Naranja'); // { spanish: "Naranja", english: "Orange" }
findFruitInSpanish('Plátano'); // undefined (NOT FOUND)
```

### `.findIndex(predicate)`

```javascript
const fruits = [
  { spanish: 'Naranja', english: 'Orange' },
  { spanish: 'Manzana', english: 'Apple' }
];

const findFruitIndex = (inSpanish) => {
  return fruits.findIndex((fruit) => fruit.spanish === inSpanish);
};

findFruitIndex('Naranja'); // 0
findFruitIndex('Manzana'); // 1
findFruitIndex('Plátano'); // -1 (NOT FOUND)
```

## 8. Improvements in Object literals

### New syntax for defining object properties

```javascript
const foo = 24;
const bar = 'bar';

const obj = { foo, bar };

obj.foo // 24
obj.bar // "bar"
```

### New syntax for defining functions on objects

```javascript
const calculator = {
  sum (x, y) {
    return x + y;
  }
};

calculator.sum(3, 4); // 7
```

### Computed property names (from expressions) in object literals

**NOTE TO SELF**: Useful for metaprogramming?

```javascript
const x = 16;
const y = 4;

const obj = {
  [x + y]: 'whatever',
  [x - y]: (z) => { return 'meta ' + z; }
};

obj[20]           // "whatever"
obj[12]           // (z) => { return 'meta ' + z; }
obj[12]('hello!') // "meta hello!"
```

This also works:

```javascript
const x = 16;
const y = 4;

const obj = {
  [x - y] (z) { return 'meta ' + z; }
};

obj[12]           // (z) => { return 'meta ' + z; }
obj[12]('hello!') // "meta hello!"
```

### `Object.assign(target, ....)`

```javascript
const calcSum = {
  sum (x, y) { return x + y; }
};

const calcSub = {
  sub (x, y) { return x - y; }
};

const calc = Object.assign({}, calcSum, calcSub);

calc.sum(3,2); // 5
calc.sub(3,2); // 1
```

**Last wins** (great to update object values), but be careful:

```javascript
const calcSum = {
  sum (x, y) { return x + y; }
};

// This object does not do what its name say
const calcSub = {
  sum (x, y) { return x - y; }
};

const calc = Object.assign({}, calcSum, calcSub);

calc.sum(3,2); // 1
calc.sub(3,2); // Uncaught TypeError: calc.sub is not a function
```

## 9. Sets

- New data structure type.
- Store unique values of any type.
- There's no literal form (you need to use `new`)
- Unlike Arrays and Objects, there's no `index` or `key`.
- Mutators:
	- `mySet.add(x)`
	- `mySet.delete(x)`
	- `mySet.clear()`
- Predicates:
	- `mySet.has('something')`

```javascript
const duplicateArray = [1,2,3,1,2,3,4,5];
const setFromArray = new Set(duplicateArray);

duplicateArray; // [1, 2, 3, 1, 2, 3, 4, 5]
setFromArray;   // Set {1, 2, 3, 4, 5}

duplicateArray.length // 8
setFromArray.size     // 5
```

### `forEach` on Sets

```javascript
const numbers = new Set([1,2,3,4,5]);

numbers.forEach((number, _, set) => {
  console.log('number: ', number);
  console.log('_     : ', _);
  console.log('set   : ', set);
});

// output
number:  1
_     :  1
set   :  Set {1, 2, 3, 4, 5}

number:  2
_     :  2
set   :  Set {1, 2, 3, 4, 5}

number:  3
_     :  3
set   :  Set {1, 2, 3, 4, 5}

number:  4
_     :  4
set   :  Set {1, 2, 3, 4, 5}

number:  5
_     :  5
set   :  Set {1, 2, 3, 4, 5}
```

## 10. Maps

- Like Sets but for `key`-`value` pairs
- Similar API to Sets
- Mutators:
	- `myMap.set(key, value)`
	- `myMap.delete(key)`
	- `myMap.clear()`
- Getter:
	- `myMap.get(key)`

```javascript
const aMap = new Map([
  ['x', 1],
  ['y', 4],
  ['z', 7]
]);

aMap; // Map {"x" => 1, "y" => 4, "z" => 7}
```

## 11. WeakSets

- Can only be composed of Objects.
- The contained Objects are weakly referenced.
- Much narrow API than Sets, only:
	- Mutators:
		- `myWeakSet.add(obj)`
		- `myWeakSet.delete(obj)`
	- Predicates:
		- `myWeakSet.has(obj)`


## 12. WeakMaps

- The `keys` must be Objects.
- The `keys` are weakly referenced.
- Much narrow API than Maps, only:
	- Mutators:
		- `myMap.set(keyObj, value)`
		- `myMap.delete(keyObj)`
	- Predicates:
		- `myWeakMap.has(keyObj)`
	- Getter:
		- `myWeakMap.get(keyObj)`



## 13. Weak{Sets,Maps} vs {Sets,Maps}

- The objects held in the `Weak{Set,Map}` counterpart are held weakly (once nobody references one of those objects they can be **garbage collected**, reducing the memory footprint of your code).
- `Weak{Sets,Maps}` are **not** enumerable (can't be iterated).


## 14. Iterators

- Provide sequential access to collections of items, keeping track of what items have been accessed.
- Implemented as Objects with a `next()` function returning another Object with two properties:
	- `value` holds the current value,
	- `done` indicates if there still are more items left.

### Set and Map iterators

- `.entries()`
- `.values()`
- `.keys()` (only on Maps)


## 15. The new `for of` loop vs the old `for in` loop

- `for of` iterates over `values` (on Arrays, Maps and Sets)
- `for in` iterates over `keys` (on Object Literals)

```javascript
const as = [1,2,3,4,5];
const ss = new Set(as);
const ms = new Map([[1, 'a'], [2, 'e'], [3, 'i']]);

for (let x of as) { console.log(x); } // yields values: 1 2 3 4 5
for (let x in as) { console.log(x); } // yields indexes: 0 1 2 3 4

for (let x of ss) { console.log(x); } // yields values: 1 2 3 4 5
for (let x in ss) { console.log(x); } // undefined

for (let x in ms) { console.log(x); } // yields values: [1, "a"] [2, "e"] [3, "i"]
for (let x of ms) { console.log(x); } // undefined
```

## 16. Arrow functions

- Syntactic sugar on top of function expressions but with a twist (in the form of lexical `this`).
- You **can not** change `this` in an arrow function (even when invoked through `call` or `apply`).

### Implicit return on expressions

`sum1`, `sum2` and `sum3` are equivalent:

```javascript
const sum1 = (x,y) => x + y;
const sum2 = (x,y) => (x + y);
const sum3 = (x,y) => { return x + y; };
```

### GOTCHA: Implicit return and object literals

We need to wrap an implicit return in parenthesis if we want to return an object literal:

```javascript
const func = (x,y,z) => ({ xyz: x + y + z });
```

## 17. Generators

- New type of function that can be *paused* and *resumed* at will (using `yield`).
- Not only allows to return values multiple times, but it can take values multiple times too.

### Generator basics

We invoke a `generator` via an `iterator`, like this:

```javascript
function* calc (x,y) {
  console.log('ONE!');
  yield x + y;

  console.log('TWO!');
  yield x - y;

  console.log('THREE!');
}

const calcGenerator = calc(5,3);

calcGenerator.next();
// triggers
//    ONE!
//    {value: 8, done: false}

calcGenerator.next();
// triggers
//    TWO!
//    {value: 2, done: false}

calcGenerator.next();
// triggers
//    THREE!
//    {value: undefined, done: true}
```

### Pushing values into a Generator

We can push values into a `generator`, like this:

```javascript
function* calc (x,y) {
  console.log('ONE!');
  a = yield x + y;

  console.log(`Yielded ${x+y} from the Generator and got ${a} back into the Generator`);
  console.log('TWO!');
  b = yield x - y;

  console.log(`Yielded ${x-y} from the Generator and got ${b} back into the Generator`);
  console.log('THREE!');
}

const calcGenerator = calc(5,3);

calcGenerator.next(10);
// The 10 goes to a black hole because we need at least one yield to inject back values into the Generator.
// triggers:
//    ONE!
//    {value: 8, done: false}

calcGenerator.next(100);
// This time the 100 finds its way into the Generator.
// triggers:
//   Yielded 8 from the Generator and got 100 back into the Generator
//   TWO!
//   {value: 2, done: false}

calcGenerator.next(1000);
// This time the 1000 finds its way into the Generator.
// triggers:
//   Yielded 8 from the Generator and got 100 back into the Generator
//   THREE!
//   {value: undefined, done: true}
```

### GOTCHA: `=` does NOT assign

```javascript
function* calc (x,y) {
  console.log('ONE!');
  // a is NOT EQUAL to x + y:
  //   - x + y is returned
  //   - a gets the whatever value from next(whatever)
  //   BUT we need to previouly have called next() once before
  a = yield x + y;

  console.log(`Yielded ${x+y} from the Generator and got ${a} back into the Generator`);
}

const calcGenerator = calc(5,3);

calcGenerator.next();
// triggers:
//    ONE!
//    {value: 8, done: false}

calcGenerator.next('YOLO');
// triggers:
//    Yielded 8 from the Generator and got YOLO!!! back into the Generator
//    {value: undefined, done: true}
```

### GOTCHA: `=` does NOT assign (!!!)

```javascript
function* calc (x,y) {
  console.log('ONE!');
  // a is NOT EQUAL to x + y:
  //   - x + y is returned
  //   - a gets undefined from next()
  //   BUT we need to previouly have called next() once before
  a = yield x + y;

  console.log(`Yielded ${x+y} from the Generator and got ${a} back into the Generator`);
}

const calcGenerator = calc(5,3);

calcGenerator.next();
// triggers:
//    ONE!
//    {value: 8, done: false}

calcGenerator.next();
// triggers:
//    Yielded 8 from the Generator and got undefined back into the Generator
//    {value: undefined, done: true}
```

## 18. Promises

- Native.
- Async actions.

### Basic Promise Example

```javascript
const randomPromise = new Promise(
  (resolve, reject) => {
    (Math.random() < .5) ? resolve(':)') : reject(':(');
  }
);

randomPromise
  .then(
    (msg) => {console.log(msg, ' resolved!')},
    (err) => {console.log(err, ' rejected!')}
  );
```

Output:

```javascript
Promise { <state>: "pending" }
:(  rejected!

or

Promise { <state>: "pending" }
:)  resolved!
```

### States and Fates in Promises

Check the [States and Fates](https://github.com/domenic/promises-unwrapping/blob/master/docs/states-and-fates.md) article.


## 19. Classes

- Under the hood inheritance is still based on `prototypes`.
- `function` declarations are *hoisted*.
- `class` declarations **are NOT** *hoisted*.
- There are `function` declarations and `function` expressions.
- There are `class` declarations and `class` expressions.

### Instance (Prototype) methods

Available through instances of a `class`

```javascript
class Human = {
  constructor (name) {
    this.name = name;
  }

  shoutName () = {
    return this.name.toUpperCase();
  }
}

const tarkovsky = new Human('tarkovsky');
console.log(tarkovsky.shoutName());
```

### Static Methods

Available through the Object Type

```javascript
class Human = {
  constructor (name) {
    this.name = name;
  }

  static reverseName (person) {
    person.name =
      person.name.split('').reverse().join('');
  }
}

const tarkovsky = new Human('tarkovsky');
Human.reverseName(tarkovsky);
```

### `class` declaration

```javascript
class Human = { .... }
```

### Anonymous `class` declaration

```javascript
const Human = class { .... };
```

### Named `class` declaration

```javascript
const Human = class Human { .... };
```

### Extending a `class`

Useful for `react` components

```javascript
import React from 'react';

class TodoList extends React.Component {
  render () {
    return (
      <div>Whatever</div>
    );
  }
}
```

## 20. Destructuring Arrays and Objects

### Basic Array Destructuring

```javascript
const [,y,z] = [3,6,9];
console.log(y,z); // 6 9
```

### Basic Object Destructuring

```javascript
const {x, y, z} = {x: 5, y: 9};
console.log(x,y); // 5 9 undefined
```

### Nested scenario

```javascript
const { firstName, lastName, doing: { status } } = {
  firstName: 'Andrei',
  lastName: 'Tarkovsky',
  doing: {
    status: 'is filming'
  }

console.log(firstName, lastName, status);
// Andrei Tarkovsky is filming
}
```
