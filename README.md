# Coding Style Guide


## Table of Contents
1. [Types](#types)
1. [References](#references)
1. [Objects](#objects)
1. [Arrays](#arrays)
1. [Destructuring](#destructuring)
1. [Strings](#strings)
1. [Functions](#functions)
1. [Arrow Functions](#arrow-functions)
1. [Classes & Constructors](#classes--constructors)
1. [Modules](#modules)
1. [Iterators and Generators](#iterators-and-generators)
1. [Properties](#properties)
1. [Variables](#variables)
1. [Hoisting](#hoisting)
1. [Comparison Operators & Equality](#comparison-operators--equality)
1. [Blocks](#blocks)
1. [Control Statements](#control-statements)
1. [Comments](#comments)
1. [Whitespace](#whitespace)
1. [Commas](#commas)
1. [Semicolons](#semicolons)
1. [Type Casting & Coercion](#type-casting-&-coercion)
1. [Naming Conventions](#naming-conventions)
1. [Accessors](#accessors)
1. [Events](#events)
1. [Testing](#testing)
1. [Fragments](#fragments)
1. [Performance](#performance)
1. [Resources](#resources)

## Types
[1.1](#types--primitives) Primitives: When you access a primitive type you work directly on its value.

* `string`
* `number`
* `boolean`
* `null`
* `undefined`
* `symbol`
* `bigint`

```javascript
const foo = 1;
let bar = foo;

bar = 9;

console.log(foo, bar); // => 1, 9
```

* Symbols and BigInts cannot be faithfully polyfilled, so they should not be used when targeting browsers/environments that don’t support them natively.

[1.2](#types--complex)  Complex: When you access a complex type you work on a reference to its value.

* `object`
* `array`
* `function`

```javascript
const foo = [1, 2];
const bar = foo;

bar[0] = 9;

console.log(foo[0], bar[0]); // => 9, 9
```

[⬆ back to top](#table-of-contents)

## References

[2.1](#references--prefer-const) Use `const` for all of your references; avoid using `var`. eslint: `prefer-const`, `no-const-assign`

* Why? This ensures that you can’t reassign your references, which can lead to bugs and difficult to comprehend code.

```javascript
// bad
var a = 1;
var b = 2;

// good
const a = 1;
const b = 2;
```

[2.2](#references--disallow-var) If you must reassign references, use `let` instead of `va`r. eslint: `no-var`

* Why? `let` is block-scoped rather than function-scoped like `var`.

```javascript
// bad
var count = 1;
if (true) {
  count += 1;
}

// good, use the let.
let count = 1;
if (true) {
  count += 1;
}
```

[2.3](#references--block-scope) Note that both `le`t and `const` are block-scoped, whereas `var` is function-scoped.

```javascript
// const and let only exist in the blocks they are defined in.
{
  let a = 1;
  const b = 1;
  var c = 1;
}
console.log(a); // ReferenceError
console.log(b); // ReferenceError
console.log(c); // Prints 1
```

In the above code, you can see that referencing `a` and `b` will produce a ReferenceError, while `c` contains the number. This is because `a` and `b` are block scoped, while `c` is scoped to the containing function.

[⬆ back to top](#table-of-contents)

## Objects

[3.1](#objects--no-new) Use the literal syntax for object creation. eslint: `no-new-object`

```javascript

// bad
const item = new Object();

// good
const item = {};

```


[3.2](#es6-computed-properties) Use computed property names when creating objects with dynamic property names.

* Why? They allow you to define all the properties of an object in one place.

```javascript

function getKey(k) {
  return `a key named ${k}`;
}

// bad
const obj = {
  id: 5,
  name: 'San Francisco',
};
obj[getKey('enabled')] = true;

// good
const obj = {
  id: 5,
  name: 'San Francisco',
  [getKey('enabled')]: true,
};
```

[3.3](#es6-object-shorthand) Use object method shorthand. eslint: `object-shorthand`

```javascript

// bad
const atom = {
  value: 1,

  addValue: function (value) {
    return atom.value + value;
  },
};

// good
const atom = {
  value: 1,

  addValue(value) {
    return atom.value + value;
  },
};

```

[3.4](#es6-object-concise) Use property value shorthand. eslint: `object-shorthand`

* Why? It is shorter and descriptive.

```javascript
const lukeSkywalker = 'Luke Skywalker';

// bad
const obj = {
  lukeSkywalker: lukeSkywalker,
};

// good
const obj = {
  lukeSkywalker,
};
```

[3.5](#objects--grouped-shorthand) Group your shorthand properties at the beginning of your object declaration.

* Why? It’s easier to tell which properties are using the shorthand.

```javascript
const anakinSkywalker = 'Anakin Skywalker';
const lukeSkywalker = 'Luke Skywalker';

// bad
const obj = {
  episodeOne: 1,
  twoJediWalkIntoACantina: 2,
  lukeSkywalker,
  episodeThree: 3,
  mayTheFourth: 4,
  anakinSkywalker,
};

// good
const obj = {
  lukeSkywalker,
  anakinSkywalker,
  episodeOne: 1,
  twoJediWalkIntoACantina: 2,
  episodeThree: 3,
  mayTheFourth: 4,
};
```

[3.6](#objects--quoted-props) Only quote properties that are invalid identifiers. eslint: `quote-props`

* Why? In general we consider it subjectively easier to read. It improves syntax highlighting, and is also more easily optimized by many JS engines.

```javascript
// bad
const bad = {
  'foo': 3,
  'bar': 4,
  'data-blah': 5,
};

// good
const good = {
  foo: 3,
  bar: 4,
  'data-blah': 5,
};
```

[3.7](#objects--prototype-builtins) Do not call `Object.prototype` methods directly, such as `hasOwnProperty`, `propertyIsEnumerable`, and `isPrototypeOf`. eslint: `no-prototype-builtins`

* Why? These methods may be shadowed by properties on the object in question - consider `{ hasOwnProperty: false }` - or, the object may be a null object (`Object.create(null)`).

```javascript
// bad
console.log(object.hasOwnProperty(key));

// good
console.log(Object.prototype.hasOwnProperty.call(object, key));

// best
const has = Object.prototype.hasOwnProperty; // cache the lookup once, in module scope.
console.log(has.call(object, key));
/* or */
import has from 'has'; // <https://www.npmjs.com/package/has>
console.log(has(object, key));
```

[3.8](#objects--rest-spread) Prefer the object spread syntax over `Object.assign` to shallow-copy objects. Use the object rest parameter syntax to get a new object with certain properties omitted. eslint: `prefer-object-spread`

```javascript

// very bad
const original = { a: 1, b: 2 };
const copy = Object.assign(original, { c: 3 }); // this mutates `original` ಠ_ಠ
delete copy.a; // so does this

// bad
const original = { a: 1, b: 2 };
const copy = Object.assign({}, original, { c: 3 }); // copy => { a: 1, b: 2, c: 3 }

// good
const original = { a: 1, b: 2 };
const copy = { ...original, c: 3 }; // copy => { a: 1, b: 2, c: 3 }

const { a, ...noA } = copy; // noA => { b: 2, c: 3 }
```

[⬆ back to top](#table-of-contents)

## Arrays

[4.1](#arrays--literals) Use the literal syntax for array creation. eslint: `no-array-constructor`

```javascript

// bad
const items = new Array();

// good
const items = [];
```


[4.2](#arrays--push) Use [Array#push](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/push) instead of direct assignment to add items to an array.

```javascript
const someStack = [];

// bad
someStack[someStack.length] = 'abracadabra';

// good
someStack.push('abracadabra');
```


[4.3](#es6-array-spreads) Use array spreads `...` to copy arrays.

```javascript
// bad
const len = items.length;
const itemsCopy = [];
let i;

for (i = 0; i < len; i += 1) {
  itemsCopy[i] = items[i];
}

// good
const itemsCopy = [...items];
```


[4.4](#arrays--from-iterable) To convert an iterable object to an array, use spreads `...` instead of `Array.from`

```javascript
const foo = document.querySelectorAll('.foo');

// good
const nodes = Array.from(foo);

// best
const nodes = [...foo];
```


[4.5](#arrays--from-array-like) Use `Array.from` for converting an array-like object to an array.

```javascript
const arrLike = { 0: 'foo', 1: 'bar', 2: 'baz', length: 3 };

// bad
const arr = Array.prototype.slice.call(arrLike);

// good
const arr = Array.from(arrLike);
```


[4.6](#arrays--mapping) Use `Array.from` instead of spread `...` for mapping over iterables, because it avoids creating an intermediate array.

```javascript
// bad
const baz = [...foo].map(bar);

// good
const baz = Array.from(foo, bar);
```


[4.7](#arrays--callback-return) Use return statements in array method callbacks. It’s ok to omit the return if the function body consists of a single statement returning an expression without side effects, following [8.2](#arrows--implicit-return). eslint: `array-callback-return`

```javascript
// good
[1, 2, 3].map((x) => {
  const y = x + 1;
  return x * y;
});

// good
[1, 2, 3].map((x) => x + 1);

// bad - no returned value means `acc` becomes undefined after the first iteration
[[0, 1], [2, 3], [4, 5]].reduce((acc, item, index) => {
  const flatten = acc.concat(item);
});

// good
[[0, 1], [2, 3], [4, 5]].reduce((acc, item, index) => {
  const flatten = acc.concat(item);
  return flatten;
});

// bad
inbox.filter((msg) => {
  const { subject, author } = msg;
  if (subject === 'Mockingbird') {
    return author === 'Harper Lee';
  } else {
    return false;
  }
});

// good
inbox.filter((msg) => {
  const { subject, author } = msg;
  if (subject === 'Mockingbird') {
    return author === 'Harper Lee';
  }

  return false;
});
```


[4.8](#arrays--bracket-newline) Use line breaks after open and before close array brackets if an array has multiple lines

```javascript
// bad
const arr = [
  [0, 1], [2, 3], [4, 5],
];

const objectInArray = [{
  id: 1,
}, {
  id: 2,
}];

const numberInArray = [
  1, 2,
];

// good
const arr = [[0, 1], [2, 3], [4, 5]];

const objectInArray = [
  {
    id: 1,
  },
  {
    id: 2,
  },
];

const numberInArray = [
  1,
  2,
];
```

[⬆ back to top](#table-of-contents)

## Destructuring

[5.1](#destructuring--object) Use object destructuring when accessing and using multiple properties of an object. eslint: `prefer-destructuring`

* Why? Destructuring saves you from creating temporary references for those properties, and from repetitive access of the object. Repeating object access creates more repetitive code, requires more reading, and creates more opportunities for mistakes. Destructuring objects also provides a single site of definition of the object structure that is used in the block, rather than requiring reading the entire block to determine what is used.

```javascript
// bad
function getFullName(user) {
  const firstName = user.firstName;
  const lastName = user.lastName;

  return `${firstName} ${lastName}`;
}

// good
function getFullName(user) {
  const { firstName, lastName } = user;
  return `${firstName} ${lastName}`;
}

// best
function getFullName({ firstName, lastName }) {
  return `${firstName} ${lastName}`;
}
```

[5.2](#destructuring--array) Use array destructuring. eslint: `prefer-destructuring`

```javascript
const arr = [1, 2, 3, 4];

// bad
const first = arr[0];
const second = arr[1];

// good
const [first, second] = arr;
```


[⬆ back to top](#table-of-contents)

## Strings



[6.1](#strings--quotes) Use single quotes `''` for strings. eslint: `quotes`

```javascript
// bad
const name = "Capt. Janeway";

// bad - template literals should contain interpolation or newlines
const name = `Capt. Janeway`;

// good
const name = 'Capt. Janeway';
```

[6.2](#strings--line-length) Strings that cause the line to go over 100 characters should not be written across multiple lines using string concatenation.

* Why? Broken strings are painful to work with and make code less searchable.

```javascript
// bad
const errorMessage = 'This is a super long error that was thrown because \
of Batman. When you stop to think about how Batman had anything to do \
with this, you would get nowhere \
fast.';

// bad
const errorMessage = 'This is a super long error that was thrown because ' +
  'of Batman. When you stop to think about how Batman had anything to do ' +
  'with this, you would get nowhere fast.';

// good
const errorMessage = 'This is a super long error that was thrown because of Batman. When you stop to think about how Batman had anything to do with this, you would get nowhere fast.';
```

[6.3](#es6-template-literals) When programmatically building up strings, use template strings instead of concatenation. eslint: `prefer-template` `template-curly-spacing`

* Why? Template strings give you a readable, concise syntax with proper newlines and string interpolation features.

```javascript
// bad
function sayHi(name) {
  return 'How are you, ' + name + '?';
}

// bad
function sayHi(name) {
  return ['How are you, ', name, '?'].join();
}

// bad
function sayHi(name) {
  return `How are you, ${ name }?`;
}

// good
function sayHi(name) {
  return `How are you, ${name}?`;
}
```

[6.4](#strings--eval) Never use `eval()` on a string, it opens too many vulnerabilities. eslint: `no-eval`

[6.5](#strings--escaping) Do not unnecessarily escape characters in strings. eslint: `no-useless-escape`

* Why? Backslashes harm readability, thus they should only be present when necessary.

```javascript
// bad
const foo = '\'this\' \i\s \"quoted\"';

// good
const foo = '\'this\' is "quoted"';
const foo = `my name is '${name}'`;
```

[⬆ back to top](#table-of-contents)

## Functions

[7.1](#functions--declarations) Use named function expressions instead of function declarations. eslint: `func-style`

* Why? Function declarations are hoisted, which means that it’s easy - too easy - to reference the function before it is defined in the file. This harms readability and maintainability. If you find that a function’s definition is large or complex enough that it is interfering with understanding the rest of the file, then perhaps it’s time to extract it to its own module! Don’t forget to explicitly name the expression, regardless of whether or not the name is inferred from the containing variable (which is often the case in modern browsers or when using compilers such as Babel). This eliminates any assumptions made about the Error’s call stack. ([Discussion](https://github.com/airbnb/javascript/issues/794))

```javascript
// bad
function foo() {
  // ...
}

// bad
const foo = function () {
  // ...
};

// good
// lexical name distinguished from the variable-referenced invocation(s)
const short = function longUniqueMoreDescriptiveLexicalFoo() {
  // ...
};
```

[7.2](#functions--iife) Wrap immediately invoked function expressions in parentheses. eslint: `wrap-iife`

* Why? An immediately invoked function expression is a single unit - wrapping both it, and its invocation parens, in parens, cleanly expresses this. Note that in a world with modules everywhere, you almost never need an IIFE.

```javascript
// immediately-invoked function expression (IIFE)
(function () {
  console.log('Welcome to the Internet. Please follow me.');
}());
```

[7.3](#functions--in-blocks) Never declare a function in a non-function block (`if`, `while`, etc). Assign the function to a variable instead. Browsers will allow you to do it, but they all interpret it differently, which is bad news bears. eslint: `no-loop-func`

[7.4](#functions--note-on-blocks) **Note**: ECMA-262 defines a `block` as a list of statements. A function declaration is not a statement.

```javascript
// bad
if (currentUser) {
  function test() {
    console.log('Nope.');
  }
}

// good
let test;
if (currentUser) {
  test = () => {
    console.log('Yup.');
  };
}
```


[7.5](#functions--arguments-shadow) Never name a parameter `arguments`. This will take precedence over the `arguments` object that is given to every function scope.

```javascript
// bad
function foo(name, options, arguments) {
  // ...
}

// good
function foo(name, options, args) {
  // ...
}
```


[7.6](#es6-rest) Never use `arguments`, opt to use rest syntax `...` instead. eslint: `prefer-rest-params`

* Why? `...` is explicit about which arguments you want pulled. Plus, rest arguments are a real Array, and not merely Array-like like `arguments`.

```javascript
// bad
function concatenateAll() {
  const args = Array.prototype.slice.call(arguments);
  return args.join('');
}

// good
function concatenateAll(...args) {
  return args.join('');
}
```

[7.7](#es6-default-parameters) Use default parameter syntax rather than mutating function arguments.

```javascript
// really bad
function handleThings(opts) {
  // No! We shouldn’t mutate function arguments.
  // Double bad: if opts is falsy it'll be set to an object which may
  // be what you want but it can introduce subtle bugs.
  opts = opts || {};
  // ...
}

// still bad
function handleThings(opts) {
  if (opts === void 0) {
    opts = {};
  }
  // ...
}

// good
function handleThings(opts = {}) {
  // ...
}
```

[7.8](#functions--default-side-effects) Avoid side effects with default parameters.

* Why? They are confusing to reason about.

```javascript
var b = 1;
// bad
function count(a = b++) {
  console.log(a);
}
count();  // 1
count();  // 2
count(3); // 3
count();  // 3
```

[7.9](#functions--defaults-last) Always put default parameters last. eslint: `default-param-last`

```javascript
// bad
function handleThings(opts = {}, name) {
  // ...
}

// good
function handleThings(name, opts = {}) {
  // ...
}
```


[7.10](#functions--constructor) Never use the Function constructor to create a new function. eslint: `no-new-func`

* Why? Creating a function in this way evaluates a string similarly to `eval()`, which opens vulnerabilities.

```javascript
// bad
var add = new Function('a', 'b', 'return a + b');

// still bad
var subtract = Function('a', 'b', 'return a - b');
```



[7.11](#functions--signature-spacing) Spacing in a function signature. eslint: `space-before-function-paren` `space-before-blocks`

* Why? Consistency is good, and you shouldn’t have to add or remove a space when adding or removing a name.

```javascript
// bad
const f = function(){};
const g = function (){};
const h = function() {};

// good
const x = function () {};
const y = function a() {};
```

[7.12](#functions--mutate-params) Never mutate parameters. eslint: `no-param-reassign`

* Why? Manipulating objects passed in as parameters can cause unwanted variable side effects in the original caller.

```javascript
// bad
function f1(obj) {
  obj.key = 1;
}

// good
function f2(obj) {
  const key = Object.prototype.hasOwnProperty.call(obj, 'key') ? obj.key : 1;
}
```

[7.13](#functions--reassign-params) Never reassign parameters. eslint: `no-param-reassign`

* Why? Reassigning parameters can lead to unexpected behavior, especially when accessing the arguments object. It can also cause optimization issues, especially in V8.

```javascript
// bad
function f1(a) {
  a = 1;
  // ...
}

function f2(a) {
  if (!a) { a = 1; }
  // ...
}

// good
function f3(a) {
  const b = a || 1;
  // ...
}

function f4(a = 1) {
  // ...
}
```

[7.14](#functions--spread-vs-apply) Prefer the use of the spread syntax `...` to call variadic functions. eslint: `prefer-spread`

* Why? It’s cleaner, you don’t need to supply a context, and you can not easily compose `new` with `apply`.

```javascript
// bad
const x = [1, 2, 3, 4, 5];
console.log.apply(console, x);

// good
const x = [1, 2, 3, 4, 5];
console.log(...x);

// bad
new (Function.prototype.bind.apply(Date, [null, 2016, 8, 5]));

// good
new Date(...[2016, 8, 5]);
```

[7.15](#functions--signature-invocation-indentation) Functions with multiline signatures, or invocations, should be indented just like every other multiline list in this guide: with each item on a line by itself, with a trailing comma on the last item. eslint: `function-paren-newline`

```javascript
// bad
function foo(bar,
             baz,
             quux) {
  // ...
}

// good
function foo(
  bar,
  baz,
  quux,
) {
  // ...
}

// bad
console.log(foo,
  bar,
  baz);

// good
console.log(
  foo,
  bar,
  baz,
);
```

[⬆ back to top](#table-of-contents)

## Arrow Functions


[8.1](#arrows--use-them) When you must use an anonymous function (as when passing an inline callback), use arrow function notation. eslint: `prefer-arrow-callback, arrow-spacing`

* Why? It creates a version of the function that executes in the context of `this`, which is usually what you want, and is a more concise syntax.
* Why not? If you have a fairly complicated function, you might move that logic out into its own named function expression.

```javascript
// bad
[1, 2, 3].map(function (x) {
  const y = x + 1;
  return x * y;
});

// good
[1, 2, 3].map((x) => {
  const y = x + 1;
  return x * y;
});
```

[8.2](#arrows--implicit-return) If the function body consists of a single statement returning an [expression](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_Operators#Expressions) without side effects, omit the braces and use the implicit return. Otherwise, keep the braces and use a `return` statement. eslint: `arrow-parens`, `arrow-body-style`

* Why? Syntactic sugar. It reads well when multiple functions are chained together.

```javascript
// bad
[1, 2, 3].map((number) => {
  const nextNumber = number + 1;
  `A string containing the ${nextNumber}.`;
});

// good
[1, 2, 3].map((number) => `A string containing the ${number + 1}.`);

// good
[1, 2, 3].map((number) => {
  const nextNumber = number + 1;
  return `A string containing the ${nextNumber}.`;
});

// good
[1, 2, 3].map((number, index) => ({
  [index]: number,
}));

// No implicit return with side effects
function foo(callback) {
  const val = callback();
  if (val === true) {
    // Do something if callback returns true
  }
}

let bool = false;

// bad
foo(() => bool = true);

// good
foo(() => {
  bool = true;
});
```

[8.3](#arrows--paren-wrap) In case the expression spans over multiple lines, wrap it in parentheses for better readability.

* Why? It shows clearly where the function starts and ends.

```javascript
// bad
['get', 'post', 'put'].map((httpMethod) => Object.prototype.hasOwnProperty.call(
    httpMagicObjectWithAVeryLongName,
    httpMethod,
  )
);

// good
['get', 'post', 'put'].map((httpMethod) => (
  Object.prototype.hasOwnProperty.call(
    httpMagicObjectWithAVeryLongName,
    httpMethod,
  )
));
```

[8.4](#arrows--one-arg-parens) Always include parentheses around arguments for clarity and consistency. eslint: `arrow-parens`

* Why? Minimizes diff churn when adding or removing arguments.

```javascript
// bad
[1, 2, 3].map(x => x * x);

// good
[1, 2, 3].map((x) => x * x);

// bad
[1, 2, 3].map(number => (
  `A long string with the ${number}. It’s so long that we don’t want it to take up space on the .map line!`
));

// good
[1, 2, 3].map((number) => (
  `A long string with the ${number}. It’s so long that we don’t want it to take up space on the .map line!`
));

// bad
[1, 2, 3].map(x => {
  const y = x + 1;
  return x * y;
});

// good
[1, 2, 3].map((x) => {
  const y = x + 1;
  return x * y;
});
```

[8.5](#arrows--confusing) Avoid confusing arrow function syntax (`=>`) with comparison operators (`<=`, `>=`). eslint: `no-confusing-arrow`

```javascript
// bad
const itemHeight = (item) => item.height <= 256 ? item.largeSize : item.smallSize;

// bad
const itemHeight = (item) => item.height >= 256 ? item.largeSize : item.smallSize;

// good
const itemHeight = (item) => (item.height <= 256 ? item.largeSize : item.smallSize);

// good
const itemHeight = (item) => {
  const { height, largeSize, smallSize } = item;
  return height <= 256 ? largeSize : smallSize;
};
```


[8.6](#whitespace--implicit-arrow-linebreak) Enforce the location of arrow function bodies with implicit returns. eslint: `implicit-arrow-linebreak`

```javascript
// bad
(foo) =>
  bar;

(foo) =>
  (bar);

// good
(foo) => bar;
(foo) => (bar);
(foo) => (
   bar
)
```

[⬆ back to top](#table-of-contents)

## Classes & Constructors


[9.3](#constructors--chaining) Methods can return `this` to help with method chaining.

```javascript
// bad
Jedi.prototype.jump = function () {
  this.jumping = true;
  return true;
};

Jedi.prototype.setHeight = function (height) {
  this.height = height;
};

const luke = new Jedi();
luke.jump(); // => true
luke.setHeight(20); // => undefined

// good
class Jedi {
  jump() {
    this.jumping = true;
    return this;
  }

  setHeight(height) {
    this.height = height;
    return this;
  }
}

const luke = new Jedi();

luke.jump()
  .setHeight(20);
```


[9.4](#constructors--tostring) It’s okay to write a custom `toString()` method, just make sure it works successfully and causes no side effects.

```javascript
class Jedi {
  constructor(options = {}) {
    this.name = options.name || 'no name';
  }

  getName() {
    return this.name;
  }

  toString() {
    return `Jedi - ${this.getName()}`;
  }
}
```


[9.5](#constructors--no-useless) Classes have a default constructor if one is not specified. An empty constructor function or one that just delegates to a parent class is unnecessary. eslint: `no-useless-constructor`

```javascript
// bad
class Jedi {
  constructor() {}

  getName() {
    return this.name;
  }
}

// bad
class Rey extends Jedi {
  constructor(...args) {
    super(...args);
  }
}

// good
class Rey extends Jedi {
  constructor(...args) {
    super(...args);
    this.name = 'Rey';
  }
}
```


[9.7](#classes--methods-use-this) Class methods should use `this` or be made into a static method unless an external library or framework requires using specific non-static methods. Being an instance method should indicate that it behaves differently based on properties of the receiver. eslint: `class-methods-use-this`

```javascript
// bad
class Foo {
  bar() {
    console.log('bar');
  }
}

// good - this is used
class Foo {
  bar() {
    console.log(this.bar);
  }
}

// good - constructor is exempt
class Foo {
  constructor() {
    // ...
  }
}

// good - static methods aren't expected to use this
class Foo {
  static bar() {
    console.log('bar');
  }
}
```

[⬆ back to top](#table-of-contents)

## Modules

[⬆ back to top](#table-of-contents)

## Iterators and Generators

[⬆ back to top](#table-of-contents)

## Properties

[12.1](#properties--dot) Use dot notation when accessing properties. eslint: `dot-notation`

```javascript
const luke = {
  jedi: true,
  age: 28,
};

// bad
const isJedi = luke['jedi'];

// good
const isJedi = luke.jedi;
```


[12.2](#properties--bracket) Use bracket notation `[]` when accessing properties with a variable.

```javascript
const luke = {
  jedi: true,
  age: 28,
};

function getProp(prop) {
  return luke[prop];
}

const isJedi = getProp('jedi');
```


[12.3](#es2016-properties--exponentiation-operator) Use exponentiation operator `**` when calculating exponentiations. eslint: `no-restricted-properties`.

```javascript
// bad
const binary = Math.pow(2, 10);

// good
const binary = 2 ** 10;
```

[⬆ back to top](#table-of-contents)

## Variables

[13.1](#variables--const) Always use `const` or `let` to declare variables. Not doing so will result in global variables. We want to avoid polluting the global namespace. Captain Planet warned us of that. eslint: `no-undef` `prefer-const`

```javascript
// bad
superPower = new SuperPower();

// good
const superPower = new SuperPower();
```


[⬆ back to top](#table-of-contents)

## Hoisting

[14.1](#hoisting--about) `var` declarations get hoisted to the top of their closest enclosing function scope, their assignment does not. `const` and `let` declarations are blessed with a new concept called [Temporal Dead Zones (TDZ)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let#temporal_dead_zone_tdz). It’s important to know why [typeof is no longer safe](https://web.archive.org/web/20200121061528/http://es-discourse.com/t/why-typeof-is-no-longer-safe/15).

```javascript
// we know this wouldn’t work (assuming there
// is no notDefined global variable)
function example() {
  console.log(notDefined); // => throws a ReferenceError
}

// creating a variable declaration after you
// reference the variable will work due to
// variable hoisting. Note: the assignment
// value of `true` is not hoisted.
function example() {
  console.log(declaredButNotAssigned); // => undefined
  var declaredButNotAssigned = true;
}

// the interpreter is hoisting the variable
// declaration to the top of the scope,
// which means our example could be rewritten as:
function example() {
  let declaredButNotAssigned;
  console.log(declaredButNotAssigned); // => undefined
  declaredButNotAssigned = true;
}

// using const and let
function example() {
  console.log(declaredButNotAssigned); // => throws a ReferenceError
  console.log(typeof declaredButNotAssigned); // => throws a ReferenceError
  const declaredButNotAssigned = true;
}
```


[14.2](#hoisting--anon-expressions) Anonymous function expressions hoist their variable name, but not the function assignment.

```javascript
function example() {
  console.log(anonymous); // => undefined

  anonymous(); // => TypeError anonymous is not a function

  var anonymous = function () {
    console.log('anonymous function expression');
  };
}
```


[14.3](#hoisting--named-expressions) Named function expressions hoist the variable name, not the function name or the function body.

```javascript
function example() {
  console.log(named); // => undefined

  named(); // => TypeError named is not a function

  superPower(); // => ReferenceError superPower is not defined

  var named = function superPower() {
    console.log('Flying');
  };
}

// the same is true when the function name
// is the same as the variable name.
function example() {
  console.log(named); // => undefined

  named(); // => TypeError named is not a function

  var named = function named() {
    console.log('named');
  };
}
```


[14.4](#hoisting--declarations) Function declarations hoist their name and the function body.

```javascript
function example() {
  superPower(); // => Flying

  function superPower() {
    console.log('Flying');
  }
}
```

* For more information refer to [JavaScript Scoping & Hoisting](https://www.adequatelygood.com/2010/2/JavaScript-Scoping-and-Hoisting/) by [Ben Cherry](https://www.adequatelygood.com/).

[⬆ back to top](#table-of-contents)

## Comparison Operators & Equality

[15.1](#comparison--eqeqeq) Use `===` and `!==` over `==` and `!=`. eslint: `eqeqeq`

[15.2](#comparison--if) Conditional statements such as the `if` statement evaluate their expression using coercion with the `ToBoolean` abstract method and always follow these simple rules:

 * **Objects** evaluate to **true**
 * **Undefined** evaluates to **false**
 * **Null** evaluates to **false**
 * **Booleans** evaluate to the **value of the boolean**
 * **Numbers** evaluate to **false** if **+0**, **-0**, **or NaN**, otherwise **true**
 * **Strings** evaluate to **false** if an empty string `''`, otherwise **true**

```javascript
if ([0] && []) {
  // true
  // an array (even an empty one) is an object, objects will evaluate to true
}
```


[15.3](#comparison--shortcuts) Use shortcuts for booleans, but explicit comparisons for strings and numbers.

```javascript
// bad
if (isValid === true) {
  // ...
}

// good
if (isValid) {
  // ...
}

// bad
if (name) {
  // ...
}

// good
if (name !== '') {
  // ...
}

// bad
if (collection.length) {
  // ...
}

// good
if (collection.length > 0) {
  // ...
}
```


[15.4](#comparison--moreinfo) For more information see [Truth Equality and JavaScript](https://javascriptweblog.wordpress.com/2011/02/07/truth-equality-and-javascript/#more-2108) by Angus Croll.



[15.6](#comparison--nested-ternaries) Ternaries should not be nested and generally be single line expressions. eslint: `no-nested-ternary`

```javascript
// bad
const foo = maybe1 > maybe2
  ? "bar"
  : value1 > value2 ? "baz" : null;

// split into 2 separated ternary expressions
const maybeNull = value1 > value2 ? 'baz' : null;

// better
const foo = maybe1 > maybe2
  ? 'bar'
  : maybeNull;

// best
const foo = maybe1 > maybe2 ? 'bar' : maybeNull;
```


[15.7](#comparison--unneeded-ternary) Avoid unneeded ternary statements. eslint: `no-unneeded-ternary`

```javascript
// bad
const foo = a ? a : b;
const bar = c ? true : false;
const baz = c ? false : true;

// good
const foo = a || b;
const bar = !!c;
const baz = !c;
```


[⬆ back to top](#table-of-contents)

## Blocks

[16.1](#blocks--braces) Use braces with all multiline blocks. eslint: `nonblock-statement-body-position`

```javascript
// bad
if (test)
  return false;

// good
if (test) return false;

// good
if (test) {
  return false;
}

// bad
function foo() { return false; }

// good
function bar() {
  return false;
}
```


[16.2](#blocks--cuddled-elses) If you’re using multiline blocks with `if` and `else`, put `else` on the same line as your if block’s closing brace. eslint: `brace-style`

```javascript
// bad
if (test) {
  thing1();
  thing2();
}
else {
  thing3();
}

// good
if (test) {
  thing1();
  thing2();
} else {
  thing3();
}
```


[16.3](#blocks--no-else-return) If an `if` block always executes a `return` statement, the subsequent `else` block is unnecessary. A `return` in an `else if` block following an `if` block that contains a `return` can be separated into multiple `if` blocks. eslint: `no-else-return`

```javascript
// bad
function foo() {
  if (x) {
    return x;
  } else {
    return y;
  }
}

// bad
function cats() {
  if (x) {
    return x;
  } else if (y) {
    return y;
  }
}

// bad
function dogs() {
  if (x) {
    return x;
  } else {
    if (y) {
      return y;
    }
  }
}

// good
function foo() {
  if (x) {
    return x;
  }

  return y;
}

// good
function cats() {
  if (x) {
    return x;
  }

  if (y) {
    return y;
  }
}

// good
function dogs(x) {
  if (x) {
    if (z) {
      return y;
    }
  } else {
    return z;
  }
}
```

[⬆ back to top](#table-of-contents)

## Control Statements

[17.1](#single-line--braces) Don't use single line if statements without braces.

```javascript
// bad
if (!isRunning) startRunning();

// less bad
if (!isRunning) { startRunning(); }

// good
if (!isRunning) {
  startRunning();
}
```


[17.2](#control-statements--value-selection) Don't use selection operators in place of control statements.

```javascript
// bad
!isRunning && startRunning();

// good
if (!isRunning) {
  startRunning();
}
```

[⬆ back to top](#table-of-contents)

## Comments

[18.1](#comments--multiline) Use `/** ... */` for multiline comments.

```javascript
// bad
// make() returns a new element
// based on the passed in tag name
//
// @param {String} tag
// @return {Element} element
function make(tag) {

  // ...

  return element;
}

// good
/**
 * make() returns a new element
 * based on the passed-in tag name
 */
function make(tag) {

  // ...

  return element;
}
```


[18.2](#comments--singleline) Use `//` for single line comments. Place single line comments on a newline above the subject of the comment. Put an empty line before the comment unless it’s on the first line of a block.

```javascript
// bad
const active = true;  // is current tab

// good
// is current tab
const active = true;

// bad
function getType() {
  console.log('fetching type...');
  // set the default type to 'no type'
  const type = this.type || 'no type';

  return type;
}

// good
function getType() {
  console.log('fetching type...');

  // set the default type to 'no type'
  const type = this.type || 'no type';

  return type;
}

// also good
function getType() {
  // set the default type to 'no type'
  const type = this.type || 'no type';

  return type;
}
```


[18.3](#comments--spaces) Start all comments with a space to make it easier to read. eslint: `spaced-comment`

```javascript
// bad
//is current tab
const active = true;

// good
// is current tab
const active = true;

// bad
/**
 *make() returns a new element
 *based on the passed-in tag name
 */
function make(tag) {

  // ...

  return element;
}

// good
/**
 * make() returns a new element
 * based on the passed-in tag name
 */
function make(tag) {

  // ...

  return element;
}
```


[18.4](#comments--actionitems) Prefixing your comments with `FIXME` or `TODO` helps other developers quickly understand if you’re pointing out a problem that needs to be revisited, or if you’re suggesting a solution to the problem that needs to be implemented. These are different than regular comments because they are actionable. The actions are `FIXME: -- need to figure this out` or `TODO: -- need to implement`.



[18.5](#comments--fixme) Use `// FIXME:` to annotate problems.

```javascript
class Calculator extends Abacus {
  constructor() {
    super();

    // FIXME: shouldn’t use a global here
    total = 0;
  }
}
```


[18.6](#comments--todo) Use `// TODO:` to annotate solutions to problems.

```javascript
class Calculator extends Abacus {
  constructor() {
    super();

    // TODO: total should be configurable by an options param
    this.total = 0;
  }
}
```

[⬆ back to top](#table-of-contents)

## Whitespace


[19.1](#whitespace--spaces) Use soft tabs (space character) set to 2 spaces. eslint: `indent`

```javascript
// bad
function foo() {
∙∙∙∙let name;
}

// bad
function bar() {
∙let name;
}

// good
function baz() {
∙∙let name;
}
```


[19.2](#whitespace--before-blocks) Place 1 space before the leading brace. eslint: `space-before-blocks`

```javascript
// bad
function test(){
  console.log('test');
}

// good
function test() {
  console.log('test');
}

// bad
dog.set('attr',{
  age: '1 year',
  breed: 'Bernese Mountain Dog',
});

// good
dog.set('attr', {
  age: '1 year',
  breed: 'Bernese Mountain Dog',
});
```


[19.3](#whitespace--around-keywords) Place 1 space before the opening parenthesis in control statements (`if`, `while` etc.). Place no space between the argument list and the function name in function calls and declarations. eslint: `keyword-spacing`

```javascript
// bad
if(isJedi) {
  fight ();
}

// good
if (isJedi) {
  fight();
}

// bad
function fight () {
  console.log ('Swooosh!');
}

// good
function fight() {
  console.log('Swooosh!');
}
```


[19.4](#whitespace--infix-ops) Set off operators with spaces. eslint: `space-infix-ops`

```javascript
// bad
const x=y+5;

// good
const x = y + 5;
```


[19.5](#whitespace--newline-at-end) End files with a single newline character. eslint: `eol-last`

```javascript
// bad
import { es6 } from './AirbnbStyleGuide';
  // ...
export default es6;
```

```javascript
// bad
import { es6 } from './AirbnbStyleGuide';
  // ...
export default es6;↵
```

```javascript
// good
import { es6 } from './AirbnbStyleGuide';
  // ...
export default es6;↵
```


[19.6](#whitespace--chains) Use indentation when making long method chains (more than 2 method chains). Use a leading dot, whichemphasizes that the line is a method call, not a new statement. eslint: `newline-per-chained-call` `no-whitespace-before-property`

```javascript
// bad
$('#items').find('.selected').highlight().end().find('.open').updateCount();

// bad
$('#items').
  find('.selected').
    highlight().
    end().
  find('.open').
    updateCount();

// good
$('#items')
  .find('.selected')
    .highlight()
    .end()
  .find('.open')
    .updateCount();

// bad
const leds = stage.selectAll('.led').data(data).enter().append('svg:svg').classed('led', true)
    .attr('width', (radius + margin) * 2).append('svg:g')
    .attr('transform', `translate(${radius + margin},${radius + margin})`)
    .call(tron.led);

// good
const leds = stage.selectAll('.led')
    .data(data)
  .enter().append('svg:svg')
    .classed('led', true)
    .attr('width', (radius + margin) * 2)
  .append('svg:g')
    .attr('transform', `translate(${radius + margin},${radius + margin})`)
    .call(tron.led);

// good
const leds = stage.selectAll('.led').data(data);
const svg = leds.enter().append('svg:svg');
svg.classed('led', true).attr('width', (radius + margin) * 2);
const g = svg.append('svg:g');
g.attr('transform', `translate(${radius + margin},${radius + margin})`).call(tron.led);
```


[19.7](#whitespace--after-blocks) Leave a blank line after blocks and before the next statement.

```javascript
// bad
if (foo) {
  return bar;
}
return baz;

// good
if (foo) {
  return bar;
}

return baz;

// bad
const obj = {
  foo() {
  },
  bar() {
  },
};
return obj;

// good
const obj = {
  foo() {
  },

  bar() {
  },
};

return obj;

// bad
const arr = [
  function foo() {
  },
  function bar() {
  },
];
return arr;

// good
const arr = [
  function foo() {
  },

  function bar() {
  },
];

return arr;
```

<a name="whitespace--padded-blocks"></a><a name="18.8"></a>

[19.8](#whitespace--padded-blocks) Do not pad your blocks with blank lines. eslint: `padded-blocks`

```javascript
// bad
function bar() {

  console.log(foo);

}

// bad
if (baz) {

  console.log(qux);
} else {
  console.log(foo);

}

// bad
class Foo {

  constructor(bar) {
    this.bar = bar;
  }
}

// good
function bar() {
  console.log(foo);
}

// good
if (baz) {
  console.log(qux);
} else {
  console.log(foo);
}
```


[19.9](#whitespace--no-multiple-blanks) Do not use multiple blank lines to pad your code. eslint: `no-multiple-empty-lines`

<!-- markdownlint-disable MD012 -->

```javascript
// bad
class Person {
  constructor(fullName, email, birthday) {
    this.fullName = fullName;


    this.email = email;


    this.setAge(birthday);
  }


  setAge(birthday) {
    const today = new Date();


    const age = this.getAge(today, birthday);


    this.age = age;
  }


  getAge(today, birthday) {
    // ..
  }
}

// good
class Person {
  constructor(fullName, email, birthday) {
    this.fullName = fullName;
    this.email = email;
    this.setAge(birthday);
  }

  setAge(birthday) {
    const today = new Date();
    const age = getAge(today, birthday);
    this.age = age;
  }

  getAge(today, birthday) {
    // ..
  }
}
```


[19.10](#whitespace--in-parens) Do not add spaces inside parentheses. eslint: `space-in-parens`

```javascript
// bad
function bar( foo ) {
  return foo;
}

// good
function bar(foo) {
  return foo;
}

// bad
if ( foo ) {
  console.log(foo);
}

// good
if (foo) {
  console.log(foo);
}
```


[19.11](#whitespace--in-brackets) Do not add spaces inside brackets. eslint: `array-bracket-spacing`

```javascript
// bad
const foo = [ 1, 2, 3 ];
console.log(foo[ 0 ]);

// good
const foo = [1, 2, 3];
console.log(foo[0]);



[19.12](#whitespace--in-braces) Add spaces inside curly braces. eslint: `object-curly-spacing`

// bad
const foo = {clark: 'kent'};

// good
const foo = { clark: 'kent' };
```


[19.14](#whitespace--block-spacing) Require consistent spacing inside an open block token and the next token on the same line. This rule also enforces consistent spacing inside a close block token and previous token on the same line. eslint: `block-spacing`

```javascript
// bad
function foo() {return true;}
if (foo) { bar = 0;}

// good
function foo() { return true; }
if (foo) { bar = 0; }
```


[19.15](#whitespace--comma-spacing) Avoid spaces before commas and require a space after commas. eslint: `comma-spacing`

```javascript
// bad
var foo = 1,bar = 2;
var arr = [1 , 2];

// good
var foo = 1, bar = 2;
var arr = [1, 2];
```


[19.16](#whitespace--computed-property-spacing) Enforce spacing inside of computed property brackets. eslint: `computed-property-spacing`

```javascript
// bad
obj[foo ]
obj[ 'foo']
var x = {[ b ]: a}
obj[foo[ bar ]]

// good
obj[foo]
obj['foo']
var x = { [b]: a }
obj[foo[bar]]
```


[19.17](#whitespace--func-call-spacing) Avoid spaces between functions and their invocations. eslint: `func-call-spacing`

```javascript
// bad
func ();

func
();

// good
func();
```


[19.18](#whitespace--key-spacing) Enforce spacing between keys and values in object literal properties. eslint: `key-spacing`

```javascript
// bad
var obj = { foo : 42 };
var obj2 = { foo:42 };

// good
var obj = { foo: 42 };
```


[19.19](#whitespace--no-trailing-spaces) Avoid trailing spaces at the end of lines. eslint: `no-trailing-spaces`



[19.20](#whitespace--no-multiple-empty-lines) Avoid multiple empty lines, only allow one newline at the end of files, and avoid a newline at the beginning of files. eslint: `no-multiple-empty-lines`

<!-- markdownlint-disable MD012 -->

```javascript
// bad - multiple empty lines
var x = 1;


var y = 2;

// bad - 2+ newlines at end of file
var x = 1;
var y = 2;


// bad - 1+ newline(s) at beginning of file

var x = 1;
var y = 2;

// good
var x = 1;
var y = 2;
```

<!-- markdownlint-enable MD012 -->

[⬆ back to top](#table-of-contents)

## Commas


[20.1](#commas--leading-trailing) Leading commas: **Nope.** eslint: `comma-style`

```javascript
// bad
const story = [
    once
  , upon
  , aTime
];

// good
const story = [
  once,
  upon,
  aTime,
];

// bad
const hero = {
    firstName: 'Ada'
  , lastName: 'Lovelace'
  , birthYear: 1815
  , superPower: 'computers'
};

// good
const hero = {
  firstName: 'Ada',
  lastName: 'Lovelace',
  birthYear: 1815,
  superPower: 'computers',
};
```


[⬆ back to top](#table-of-contents)

## Semicolons


[⬆ back to top](#table-of-contents)

## Type Casting & Coercion


[22.1](#coercion--explicit) Perform type coercion at the beginning of the statement.


[22.2](#coercion--strings) Strings: eslint: `no-new-wrappers`

```javascript
// => this.reviewScore = 9;

// bad
const totalScore = new String(this.reviewScore); // typeof totalScore is "object" not "string"

// bad
const totalScore = this.reviewScore + ''; // invokes this.reviewScore.valueOf()

// bad
const totalScore = this.reviewScore.toString(); // isn’t guaranteed to return a string

// good
const totalScore = String(this.reviewScore);
```


[22.4](#coercion--comment-deviations) If for whatever reason you are doing something wild and `parseInt` is your bottleneck and need to use Bitshift for [performance reasons](https://jsperf.com/coercion-vs-casting/3), leave a comment explaining why and what you’re doing.

```javascript
// good
/**
 * parseInt was the reason my code was slow.
 * Bitshifting the String to coerce it to a
 * Number made it a lot faster.
 */
const val = inputValue >> 0;
```


[22.5](#coercion--bitwise) **Note:** Be careful when using bitshift operations. Numbers are represented as [64-bit values](https://es5.github.io/#x4.3.19), but bitshift operations always return a 32-bit integer ([source](https://es5.github.io/#x11.7)). Bitshift can lead to unexpected behavior for integer values larger than 32 bits. [Discussion](https://github.com/airbnb/javascript/issues/109). Largest signed 32-bit Int is 2,147,483,647:

```javascript
2147483647 >> 0; // => 2147483647
2147483648 >> 0; // => -2147483648
2147483649 >> 0; // => -2147483647
```


[22.6](#coercion--booleans) Booleans: eslint: `no-new-wrappers`

```javascript
const age = 0;

// bad
const hasAge = new Boolean(age);

// good
const hasAge = Boolean(age);

// best
const hasAge = !!age;
```

[⬆ back to top](#table-of-contents)


## Naming Conventions


[23.1](#naming--descriptive) Avoid single letter names. Be descriptive with your naming. eslint: `id-length`

```javascript
// bad
function q() {
  // ...
}

// good
function query() {
  // ...
}
```


[23.2](#naming--camelCase) Use camelCase when naming objects, functions, and instances. eslint: `camelcase`

```javascript
// bad
const OBJEcttsssss = {};
const this_is_my_object = {};
function c() {}

// good
const thisIsMyObject = {};
function thisIsMyFunction() {}
```


[23.3](#naming--PascalCase) Use PascalCase only when naming constructors or classes. eslint: `new-cap`

```javascript
// bad
function user(options) {
  this.name = options.name;
}

const bad = new user({
  name: 'nope',
});

// good
class User {
  constructor(options) {
    this.name = options.name;
  }
}

const good = new User({
  name: 'yup',
});
```


[23.5](#naming--self-this) Don’t save references to `this`. Use arrow functions or [Function#bind](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind).

```javascript
// bad
function foo() {
  const self = this;
  return function () {
    console.log(self);
  };
}

// bad
function foo() {
  const that = this;
  return function () {
    console.log(that);
  };
}

// good
function foo() {
  return () => {
    console.log(this);
  };
}
```


[23.6](#naming--filename-matches-export) A base filename should exactly match the name of its default export.

```javascript
// file 1 contents
class CheckBox {
  // ...
}
export default CheckBox;

// file 2 contents
export default function fortyTwo() { return 42; }

// file 3 contents
export default function insideDirectory() {}

// in some other file
// bad
import CheckBox from './checkBox'; // PascalCase import/export, camelCase filename
import FortyTwo from './FortyTwo'; // PascalCase import/filename, camelCase export
import InsideDirectory from './InsideDirectory'; // PascalCase import/filename, camelCase export

// bad
import CheckBox from './check_box'; // PascalCase import/export, snake_case filename
import forty_two from './forty_two'; // snake_case import/filename, camelCase export
import inside_directory from './inside_directory'; // snake_case import, camelCase export
import index from './inside_directory/index'; // requiring the index file explicitly
import insideDirectory from './insideDirectory/index'; // requiring the index file explicitly

// good
import CheckBox from './CheckBox'; // PascalCase export/import/filename
import fortyTwo from './fortyTwo'; // camelCase export/import/filename
import insideDirectory from './insideDirectory'; // camelCase export/import/directory name/implicit "index"
// ^ supports both insideDirectory.js and insideDirectory/index.js
```


[23.7](#naming--camelCase-default-export) Use camelCase when you export-default a function. Your filename should be identical to your function’s name.

```javascript
function makeStyleGuide() {
  // ...
}

export default makeStyleGuide;
```


[23.8](#naming--PascalCase-singleton) Use PascalCase when you export a constructor / class / singleton / function library / bare object.

```javascript
const AirbnbStyleGuide = {
  es6: {
  },
};

export default AirbnbStyleGuide;
```


[23.9](#) Use concise naming. Avoid adding type, module level context into locally scoped variables and method names

```javascript
// Show.js

// less concise
const showSchema = new Schema({...});
const Show = model('Show', showSchema);
module.exports = { Show };

// more concise
const schema = new Schema({...})
const Show = model('Show', schema);
module.exports = { Show };
```
 

[23.10](#) Creating better variable names for returning object when a child of a broader object AND when the parent object will be used.

```javascript
// deal.js

// less readable
const result = await mongoTransaction(async () => {
    const deal = await Deal.create(req.body);
    await Contract.create({ status: 'Pending', deals: [deal._id] });
    return deal;
  });
  if (result.error) {
    if (result.data.name === 'ValidationError') {
      return res.status(400).json({ error: result.data.name, message: result.data.message });
    } else {
      return res.status(500).json({ error: result.data.name, message: result.data.message });
    }
  }
  ....
  return res.status(201).json(result.data);

// more readable
const result = await mongoTransaction(async () => {
    const deal = await Deal.create(req.body);
    await Contract.create({ status: 'Pending', deals: [deal._id] });
    return deal;
  });
  
  const newDeal = result.data;
  if (result.error) {
    if (result.data.name === 'ValidationError') {
      return res.status(400).json({ error: result.data.name, message: result.data.message });
    } else {
      return res.status(500).json({ error: result.data.name, message: result.data.message });
    }
  }
  return res.status(201).json(newDeal);
```

[⬆ back to top](#table-of-contents)


## Accessors


[24.1](#accessors--not-required) Accessor functions for properties are not required.



[24.2](#accessors--no-getters-setters) Do not use JavaScript getters/setters as they cause unexpected side effects and are harder to test, maintain, and reason about. Instead, if you do make accessor functions, use `getVal()` and `setVal('hello')`.

```javascript
// bad
class Dragon {
  get age() {
    // ...
  }

  set age(value) {
    // ...
  }
}

// good
class Dragon {
  getAge() {
    // ...
  }

  setAge(value) {
    // ...
  }
}
```


[24.3](#accessors--boolean-prefix) If the property/method is a `boolean`, use `isVal()` or `hasVal()`.

```javascript
// bad
if (!dragon.age()) {
  return false;
}

// good
if (!dragon.hasAge()) {
  return false;
}
```


[24.4](#accessors--consistent) It’s okay to create `get()` and `set()` functions, but be consistent.

```javascript
class Jedi {
  constructor(options = {}) {
    const lightsaber = options.lightsaber || 'blue';
    this.set('lightsaber', lightsaber);
  }

  set(key, val) {
    this[key] = val;
  }

  get(key) {
    return this[key];
  }
}
```

[⬆ back to top](#table-of-contents)


## Events


[25.1](#events--hash) When attaching data payloads to events (whether DOM events or something more proprietary like Backbone events), pass an object literal (also known as a "hash") instead of a raw value. This allows a subsequent contributor to add more data to the event payload without finding and updating every handler for the event. For example, instead of:

```javascript
// bad
$(this).trigger('listingUpdated', listing.id);

// ...

$(this).on('listingUpdated', (e, listingID) => {
  // do something with listingID
});
```

```javascript
prefer:

// good
$(this).trigger('listingUpdated', { listingID: listing.id });

// ...

$(this).on('listingUpdated', (e, data) => {
  // do something with data.listingID
});
```

[⬆ back to top](#table-of-contents)


## Fragments

[27.1](#) Use `<></>` over `<Fragment>...</Fragment>`

* Why? ES6 React syntax for `<></>` is more concise, is less likely to be confused or suffer from namespace issues, and does not require additional file imports

```javascript
// bad
return (
  <Fragment> 
    { knocking ? <div> who's there? </div> : <Fragment />}
  </Fragment>
);

// good
return (
  <> 
    { knocking ? <div> who's there? </div> : <></>}
  </>
);
```

[⬆ back to top](#)


## Testing

[30.1](#testing--yup) **Yup**.

```javascript
function foo() {
  return true;
}
```

[30.2](#testing--for-real) ##No, but seriously:

  * Whichever testing framework you use, you should be writing tests!
  * Strive to write many small pure functions, and minimize where mutations occur.
  * Be cautious about stubs and mocks - they can make your tests more brittle.
  * We use `mocha`, `enzyme`, `sinon`, `cypress`, `supertest` and `jest` on the Onyx platform but are trying to consolidate & narrow framework usage. 
  * 100% test coverage is a good goal to strive for, even if it’s not always practical to reach it.
  * Whenever you fix a bug, _write a regression test_. A bug fixed without a regression test is almost certainly going to break again in the future.

[⬆ back to top](#table-of-contents)



## Performance

* [On Layout & Web Performance](https://www.kellegous.com/j/2013/01/26/layout-performance/)
* [String vs Array Concat](https://jsperf.com/string-vs-array-concat/2)
* [Try/Catch Cost In a Loop](https://jsperf.com/try-catch-in-loop-cost/12)
* [Bang Function](https://jsperf.com/bang-function)
* [innerHTML vs textContent for script text](https://jsperf.com/innerhtml-vs-textcontent-for-script-text)
* [Long String Concatenation](https://jsperf.com/ya-string-concat/38)
* [Are JavaScript functions like](https://www.quora.com/JavaScript-programming-language-Are-Javascript-functions-like-map-reduce-and-filter-already-optimized-for-traversing-array/answer/Quildreen-Motta) `map()`, `reduce()`, and `filter()` optimized for traversing arrays?
* Loading...

[⬆ back to top](#table-of-contents)


## Resources

**Learning ES6+**

* [Latest ECMA spec](https://tc39.github.io/ecma262/)
* [ExploringJS](https://exploringjs.com/)
* [ES6 Compatibility Table](https://kangax.github.io/compat-table/es6/)
* [Comprehensive Overview of ES6 Features](http://es6-features.org/)

**Read This**

* [Standard ECMA-262](https://www.ecma-international.org/ecma-262/6.0/index.html)

**Tools**

* Code Style Linters

  * [ESlint](https://eslint.org/) - [Airbnb Style .eslintrc](https://github.com/airbnb/javascript/blob/master/linters/.eslintrc)
  * [JSHint](https://jshint.com/) - [Airbnb Style .jshintrc](https://github.com/airbnb/javascript/blob/master/linters/.jshintrc)

* Neutrino Preset - [@neutrinojs/airbnb](https://neutrinojs.org/packages/airbnb/)

**Other Style Guides**

* [Google JavaScript Style Guide](https://google.github.io/styleguide/jsguide.html)
* [Google JavaScript Style Guide (Old)](https://google.github.io/styleguide/javascriptguide.xml)
* [jQuery Core Style Guidelines](https://contribute.jquery.org/style-guide/js/)
* [Principles of Writing Consistent, Idiomatic JavaScript](https://github.com/rwaldron/idiomatic.js)
* [StandardJS](https://standardjs.com/)

**Other Styles**

* [Naming this in nested functions](https://gist.github.com/cjohansen/4135065) - Christian Johansen
* [Conditional Callbacks](https://github.com/airbnb/javascript/issues/52) - Ross Allen
* [Popular JavaScript Coding Conventions on GitHub](http://sideeffect.kr/popularconvention/#javascript) - JeongHoon Byun
* [Multiple var statements in JavaScript, not superfluous](https://benalman.com/news/2012/05/multiple-var-statements-javascript/) - Ben Alman

**Further Reading**

* [Understanding JavaScript Closures](https://javascriptweblog.wordpress.com/2010/10/25/understanding-javascript-closures/) - Angus Croll
* [Basic JavaScript for the impatient programmer](https://www.2ality.com/2013/06/basic-javascript.html) - Dr. Axel Rauschmayer
* [You Might Not Need jQuery](https://youmightnotneedjquery.com/) - Zack Bloom & Adam Schwartz
* [ES6 Features](https://github.com/lukehoban/es6features) - Luke Hoban
* [Frontend Guidelines](https://github.com/bendc/frontend-guidelines) - Benjamin De Cock
