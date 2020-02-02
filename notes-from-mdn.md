# Notes from MDN

## JavaScript Guide

### Control flow and error handling

If the `finally` block returns a value, this value becomes the return value of the entire `try...catch...finally` production, regardless of any `return` statements in the `try` and `catch` blocks:

```javascript
function f() {
  try {
    console.log(0);
    throw 'bogus';
  } catch(e) {
    console.log(1);
    return true;    // this return statement is suspended
                    // until finally block has completed
    console.log(2); // not reachable
  } finally {
    console.log(3);
    return false;   // overwrites the previous "return"
    console.log(4); // not reachable
  }
  // "return false" is executed now  
  console.log(5);   // not reachable
}
console.log(f()); // 0, 1, 3, false
```

Overwritting of return values by the `finally` block also applies to exceptions thrown or re-thrown inside of the `catch` block.

```javascript
function f() {
  try {
    throw 'bogus';
  } catch(e) {
    console.log('caught inner "bogus"');
    throw e; // this throw statement is suspended until 
             // finally block has completed
  } finally {
    return false; // overwrites the previous "throw"
  }
  // "return false" is executed now
}

try {
  console.log(f());
} catch(e) {
  // this is never reached! 
  // while f() executes, the `finally` block returns false, 
  // which overwrites the `throw` inside the above `catch`
  console.log('caught outer "bogus"');
}

// OUTPUT
// caught inner "bogus"
// false
```

### Loops and iteration

A `label` provides a statement with an identifier that lets you refer to it else where in your program. The labeled statement can be used with `break` or `continue` statements. It is prefixing a statement with an identifier which you can refer to.

```javascript
/*
  Given an array of items and an array of tests,
  this example counts the number of items that passes all the tests.
*/
var itemsPassed = 0;
var i, j;

top:
for (i = 0; i < items.length; i++) {
  for (j = 0; j < tests.length; j++) {
    if (!tests[j].pass(items[i])) {
      continue top;
    }
  }

  itemsPassed++;
}
```

The `for...in` statement iterates a specified variable over all the enumerable properties of an object. Although it may be tempting to use this as a way to iterate over `Array` elements, the `for...in` statement will return the name of your user-defined properties in addition to the numeric indexes. Therefore, it is better to use a traditional `for` loop with a numeric index when iterating over arrays, because the `for...in` statement iterates over user-defined properties in addition to the array elements, if you modify the Array object \(such as adding custom properties or methods\).

 The following example shows the difference between a `for...of` loop and a `for...in` loop. While `for...in` iterates over property names, `for...of` iterates over property values:

```javascript
const arr = [3, 5, 7];
arr.foo = 'hello';

for (let i in arr) {
   console.log(i); // logs "0", "1", "2", "foo"
}

for (let i of arr) {
   console.log(i); // logs 3, 5, 7
}
```

### Functions

While a function can be anonymous, providing a name allows the function to refer to itself.

```javascript
const factorial = function fac(n) { return n < 2 ? 1 : n * fac(n - 1) }

console.log(factorial(3))
```

There are three ways for a function to refer to itself.

1. The function's name
2. `arguments.callee`
3. An in-scope variable that refers to the funciton

JavaScript allows for the nesting of functions and grants the inner function full access to all the variables and functions defined inside the outer function \(and all other variables and functions that the outer function has access to\). Since the inner function has access to the scope of the outer function, the variables and functions defined in the outer function will live longer than the duration of the outher function execution, if the inner function manages to survive beyond the life of the outer function. A closure is created when the inner function is somehow made available to any scope outside the outher function.

```javascript
var pet = function(name) {   // The outer function defines a variable called "name"
  var getName = function() {
    return name;             // The inner function has access to the "name" variable of the outer 
                             //function
  }
  return getName;            // Return the inner function, thereby exposing it to outer scopes
}
myPet = pet('Vivie');
   
myPet();                     // Returns "Vivie"
```

An object containing methods for manipulating the inner variables of the outer function can be returned.

```javascript
var createPet = function(name) {
  var sex;
  return {
    setName: function(newName) {
      name = newName;
    },
    getName: function() {
      return name;
    },
    getSex: function() {
      return sex;
    },
    setSex: function(newSex) {
      if(typeof newSex === 'string' && (newSex.toLowerCase() === 'male' || 
        newSex.toLowerCase() === 'female')) {
        sex = newSex;
      }
    }
  }
}

var pet = createPet('Vivie');
pet.getName();                  // Vivie
pet.setName('Oliver');
pet.setSex('male');
pet.getSex();                   // male
pet.getName();                  // Oliver
```

Using the `arguments` object, you can call a function with more arguments than it is formally declared to accept.

```javascript
function myConcat(separator) {
   var result = ''; // initialize list
   var i;
   // iterate through arguments
   for (i = 1; i < arguments.length; i++) {
      result += arguments[i] + separator;
   }
   return result;
}
// returns "red, orange, blue, "
myConcat(', ', 'red', 'orange', 'blue');
// returns "elephant; giraffe; lion; cheetah; "
myConcat('; ', 'elephant', 'giraffe', 'lion', 'cheetah');
// returns "sage. basil. oregano. pepper. parsley. "
myConcat('. ', 'sage', 'basil', 'oregano', 'pepper', 'parsley');
```

Without default parameters to set default value:

```javascript
function multiply(a, b) {
  b = typeof b !== 'undefined' ?  b : 1;
  return a * b;
}
multiply(5); // 5
```

With default parameters to set default value:

```javascript
function multiply(a, b = 1) {
  return a * b;
}
multiply(5); // 5
```

 The rest parameter syntax allows us to represent an indefinite number of arguments as an array.

```javascript
function multiply(multiplier, ...theArgs) {
  return theArgs.map(x => multiplier * x);
}
var arr = multiply(2, 1, 2, 3);
console.log(arr); // [2, 4, 6]
```

An arrow function expression does not have its own `this`, `arguments`, `super` or `new.target`.

```javascript
// Shorter functions
var a = [
  'Hydrogen',
  'Helium',
  'Lithium',
  'Beryllium'
];

var a2 = a.map(function(s) { return s.length; });
console.log(a2); // logs [8, 6, 7, 9]

var a3 = a.map(s => s.length);
console.log(a3); // logs [8, 6, 7, 9]


// No separate this

// Set property without arrow functions
function Person() {
  var self = this; // Some choose `that` instead of `self`. 
                   // Choose one and be consistent.
  self.age = 0;
  setInterval(function growUp() {
    // The callback refers to the `self` variable of which
    // the value is the expected object.
    self.age++;
  }, 1000);
}

// Set property with arrow functions
function Person() {
  this.age = 0;
  setInterval(() => {
    this.age++; // |this| properly refers to the person object
  }, 1000);
}
```

### Expressions and Operators

 The `void` operator specifies an expression to be evaluated without returning a value. `expression` is a JavaScript expression to evaluate. The parentheses surrounding the expression are optional, but it is good style to use them.

```markup
<a href="javascript:void(0)">Click here to do nothing</a>
<a href="javascript:void(document.form.submit())">
Click here to submit</a>
```

 The `instanceof` operator returns `true` if the specified object is of the specified object type.

```javascript
var theDay = new Date(1995, 12, 17);
if (theDay instanceof Date) {
  // statements to execute
}
```

Operator precedence from highest to lowest

| Operator Type | Individual operators |
| :--- | :--- |
| member | . \[\] |
| call / create instance | \(\) new |
| negation / increment | ! ~ - + ++ -- typeof void delete |
| multiply / divide | \* / %  |
| addition / subtraction | + - |
| bitwise shift | &lt;&lt; &gt;&gt; &gt;&gt;&gt; |
| relational | &lt; &lt;= &gt; &gt;= in instanceof |
| equality | == != === !== |
| bitwise-and | & |
| bitwise-xor | ^ |
| bitwise-or | \| |
| logical-and | && |
| logical-or | \|\| |
| conditional | ?: |
| assignment | = += -= \*= /= %= &lt;&lt;= &gt;&gt;= &gt;&gt;&gt;= &= ^= \|= |
| comma | , |

 The spread operator allows an expression to be expanded in places where multiple arguments \(for function calls\) or multiple elements \(for array literals\) are expected.

```javascript
var parts = ['shoulders', 'knees'];
var lyrics = ['head', ...parts, 'and', 'toes'];

function f(x, y, z) { }
var args = [0, 1, 2];
f(...args);

const newObject = {...oldObject, newProp: 5};
```

The destructuring assignment syntax is a JavaScript expression that makes it possible to extract data from arrays or objects using a syntax that mirrors the construction of array and object literals.

```javascript
[a, b] = ['Hello', 'Max']
console.log(a)    // Hello
console.log(b)    // Max

{name} = {name: 'Max', age: 28}
console.log(name);    // Max
console.log(age);    // undefined

const numbers = [1, 2, 3, 4];
[num1, , num3] = numbers;
console.log(num1, num3); // 1  3
```

### Numbers and dates

Decimal literals can start with a zero \(`0`\) followed by another decimal digit, but if every digit after the leading `0` is smaller than 8, the number gets parsed as an octal number.

### [Regular Expressions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions)

### Indexed Collections

If supply a non-integer value to the array operator, a property will be created in the object representing the array, instead of an array element.

```javascript
let arr = []
arr[3.4] = 'Oranges'
console.log(arr.length)                        // 0
console.log(arr.hasOwnProperty(3.4))           // true
```

Assigning a value to the length property that is shorter than the number of stored items truncates the array. Writing 0 empties it entirely

```javascript
let cats = ['Dusty', 'Misty', 'Twiggy']
console.log(cats.length)  // 3

cats.length = 2
console.log(cats)  // logs "Dusty, Misty" - Twiggy has been removed

cats.length = 0 
console.log(cats)  // logs []; the cats array is empty

cats.length = 3 
console.log(cats)  // logs [ <3 empty items> ]
```

The elements of an array that are omitted when the array is defined are not listed when iterating by `forEach`, but are listed when `undefined` has been manually assigned to the element.

```javascript
let array = ['first', 'second', , 'fourth']
// (4) ["first", "second", empty, "fourth"]

array.forEach(function(element) {
  console.log(element)
})
// first
// second
// fourth

if (array[2] === undefined) { 
  console.log('array[2] is undefined')  // true
} 

array = ['first', 'second', undefined, 'fourth']

array.forEach(function(element) {
  console.log(element)
})
// first
// second
// undefined
// fourth
```

The `arguments` object provides a `length` attribute but does not implement the `forEach()` method, but Array prototype methods can be called against other array-like objects.

```javascript
function printArguments() {
  Array.prototype.forEach.call(arguments, function(item) {
    console.log(item)
  })
}

Array.prototype.forEach.call('a string', function(chr) {
  console.log(chr)
})
```

### Keyed Collections

Use a `for...of` loop to return an array of `[key, value]` for each iteration in `Map` object

```javascript
let sayings = new Map();
sayings.set('dog', 'woof');
sayings.set('cat', 'meow');
sayings.set('elephant', 'toot');
sayings.size; // 3
sayings.get('fox'); // undefined
sayings.has('bird'); // false
sayings.delete('dog');
sayings.has('dog'); // false

for (let [key, value] of sayings) {
  console.log(key + ' goes ' + value);
}
// "cat goes meow"
// "elephant goes toot"

sayings.clear();
sayings.size; // 0 
```

The `WeakMap` object is a collection of key/value pairs in which the **keys are objects** only and the values can be arbitrary values.

One use case of `WeakMap` objects is to store private data for an object, or to hide implementation details.

```javascript
const privates = new WeakMap();

function Public() {
  const me = {
    // Private data goes here
  };
  privates(this, me);
}

Public.prototype.method = function() {
  const me = privates(this);
  // Do stuff with private data in `me`...
};

module.exports = Public;
```

Create an Array from a Set using Array.from or the spread operator.

```javascript
Array.from(mySet);
[...mySet2];
```

`WeakSet` objects are collections of objects.

References to objects in the `WeakMap` and `WeakSet` colletion are held weakly. If there is no other reference to an object stored in the `Weak` object, they can be garbage collected.

`Weak` object are not enumerable \(i.e., there is no method giving you a list of the keys\).

The use cases of `WeakSet` objects are limited. They will not leak memory, so it can be safe to use DOM elements as a key and mark them for tracking purposes.

### Working with Objects

All keys in the square bracket notation are converted to string unless they're Symbols, since JavaScript object property names \(keys\) can only be strings or Symbols.

Use the bracket notation with `for...in` to iterate over all the enumearble properties of an object.

```javascript
function showProps(obj, objName) {
  var result = ``;
  for (var i in obj) {
    // obj.hasOwnProperty() is used to filter out properties from the object's prototype chain
    if (obj.hasOwnProperty(i)) {
      result += `${objName}.${i} = ${obj[i]}\n`;
    }
  }
  return result;
}
```

Three native ways to list/traverse object properties

1. `for...in` loops: Traverses all enumerable properties of an object and its prototye chain.
2. `Object.keys(o)`: Returns an array with all the own \(not in the prototype chain\) enumerable properties' names \("keys"\) of an object `o`.
3. `Object.getOwnPropertyNames(o)`: Returns an array containing all own properties' names \(enumerable or not\) of an object `o`.

If the object appears where a statement is expected, the object literal need to be wrapped in parenthese, so as not to have the literal be confused with a block statement.

Object initializers are expressions, and each object initializer results in a new object being created whenever the statement in which it appears is executed. Identical object initializers create distinct objects that will not compare to each other as equal.

Create objects using `Object.create()` method. This method can be very useful, because it allows you to choose the prototype object for the object you want to create, without having to define a constructor function.

```javascript
// Animal properties and method encapsulation
var Animal = {
  type: 'Invertebrates', // Default value of properties
  displayType: function() {  // Method which will display type of Animal
    console.log(this.type);
  }
};

// Create new animal type called animal1 
var animal1 = Object.create(Animal);
animal1.displayType(); // Output:Invertebrates

// Create new animal type called Fishes
var fish = Object.create(Animal);
fish.type = 'Fishes';
fish.displayType(); // Output:Fishes
```

#### [Prototype and Inheritance](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)

All objects in JavaScript inherit from at least one other object. The object being inherited from is known as the protype, and the inherited properties can be found in the `prototype` object of the constructor. Each object has a private property which holds a link to another object called its **prototype**. That prototype object has a prototype of its own, and so on until an object is reached with `null` as its prototype. By definition, `null` has no prototype, and acts as the final link in this **prototype chain**.

When trying to access a property of an object, the property will not only be sought on the object but on the prototype of the object, the prototype of the prototype, and so on until either a property with a matching name is found or the end of the prototype chain is reached.

When an inherited function is executed, the value of `this` points to the inheriting object, not to the prototype object where the function is an own property.

```javascript
var o = {
  a: 2,
  m: function() {
    return this.a + 1;
  }
};

console.log(o.m()); // 3
// When calling o.m in this case, 'this' refers to o

var p = Object.create(o);
// p is an object that inherits from o

p.a = 4; // creates a property 'a' on p
console.log(p.m()); // 5
// when p.m is called, 'this' refers to p.
// So when p inherits the function m of o, 
// 'this.a' means p.a, the property 'a' of p
```

All functions have a special property named `prototype`.

#### Javascript object method in constructor function vs. in prototype

The two are different: The first one will store the reference to the method _only_ on the prototype object whereas the second solution will store the method on _each_ of the object. This means that each object will contain an extra pointer and thus take up a bit more memory each.

The per-object method allows the method to refer to variables in the constructor \(a closure\) and it therefore allows you to access some data that you cannot access from a prototype methods.

Finally, a prototype method can be _changed later_, that is you can redefine `Bark` at runtime on the prototype object, and this change will work for all objects with this prototype \(since the method is always looked up through the prototype\).

`hasOwnProperty` is the only thing in JavaScript which deals with properties and does **not** traverse the prototype chain.

It is **not** enough to check whether a property is `undefined`. The property might very well exist, but its value just happens to be set to `undefined`.

#### 4 Ways of extending the prototype chain

```javascript
// New-initialization
function foo(){}
foo.prototype = {
  foo_prop: "foo val"
};
function bar(){}
var proto = new foo;
proto.bar_prop = "bar val";
bar.prototype = proto;
var inst = new bar;
console.log(inst.foo_prop);  // foo val
console.log(inst.bar_prop);  // bar val

// Object.create
function foo(){}
foo.prototype = {
  foo_prop: "foo val"
};
function bar(){}
var proto = Object.create(
  foo.prototype
);
proto.bar_prop = "bar val";
bar.prototype = proto;
var inst = new bar;
console.log(inst.foo_prop);
console.log(inst.bar_prop);

function foo(){}
foo.prototype = {
  foo_prop: "foo val"
};
function bar(){}
var proto = Object.create(
  foo.prototype,
  {
    bar_prop: {
      value: "bar val"
    }
  }
);
bar.prototype = proto;
var inst = new bar;
console.log(inst.foo_prop);
console.log(inst.bar_prop)

// Object.setPrototypeOf
function foo(){}
foo.prototype = {
  foo_prop: "foo val"
};
function bar(){}
var proto = {
  bar_prop: "bar val"
};
Object.setPrototypeOf(
  proto, foo.prototype
);
bar.prototype = proto;
var inst = new bar;
console.log(inst.foo_prop);
console.log(inst.bar_prop);

function foo(){}
foo.prototype = {
  foo_prop: "foo val"
};
function bar(){}
var proto;
proto=Object.setPrototypeOf(
  { bar_prop: "bar val" },
  foo.prototype
);
bar.prototype = proto;
var inst = new bar;
console.log(inst.foo_prop);
console.log(inst.bar_prop)

// __proto__
function foo(){}
foo.prototype = {
  foo_prop: "foo val"
};
function bar(){}
var proto = {
  bar_prop: "bar val",
  __proto__: foo.prototype
};
bar.prototype = proto;
var inst = new bar;
console.log(inst.foo_prop);
console.log(inst.bar_prop);

var inst = {
  __proto__: {
    bar_prop: "bar val",
    __proto__: {
      foo_prop: "foo val",
      __proto__: Object.prototype
    }
  }
};
console.log(inst.foo_prop);
console.log(inst.bar_prop)
```

| Name | Pro\(s\) | Con\(s\) |
| :--- | :--- | :--- |
| New-initialization | Supported in every browser imaginable \(support goes all the way back to IE 5.5!\). Also, it is very fast, very standard, and very JIST-optimizable. | In order to use this method, the function in question must be initialized. During this initialization, the constructor may store unique information that must be generated per-object. However, this unique information would only be generated once, potentially leading to problems. Additionally, the initialization of the constructor may put unwanted methods onto the object. However, both these are generally not problems at all \(in fact, usually beneficial\) if it is all your own code and you know what does what where. |
| Object.create | Support in all in-use-today browsers which are all non-microsoft browsers plus IE9 and up. Allows the direct setting of \_\_proto\_\_ in a way that is one-time-only so that the browser can better optimize the object. Also allows the creation of objects without a prototype via `Object.create(null)`. | Not supported in IE8 and below. However, as Microsoft has discontinued extended support for systems running these old browsers, this should not be a concern for most applications. Additionally, the slow object initialization can be a performance black hole if using the second argument because each object-descriptor property has its own separate descriptor object. When dealing with hundreds of thousands of object descriptors in the form of object, there can arise a serious issue with lag. |
| Object.setPrototypeOf | Support in all in-use-today browsers which are all non-microsoft browsers plus IE9 and up. Allows the dynamic manipulation of an objects prototype and can even force a prototype on a prototype-less object created with `Object.create(null)`. | Should-be-deprecated and ill-performant. Making your Javascript run fast is completely out of the question if you dare use this in the final production code because many browsers optimize the prototype and try to guess the location of the method in the memory when calling an instance in advance, but setting the prototype dynamically disrupts all these optimizations and can even force some browsers to recompile for deoptimization your code just to make it work according to the specs. Not supported in IE8 and below. |
| \_\_proto\_\_ | Support in all in-use-today browsers which are all non-microsoft browsers plus IE11 and up. Setting \_\_proto\_\_ to something that is not an object only fails silently. It does not throw an exception. | Grossly deprecated and non-performant. Making your Javascript run fast is completely out of the question if you dare use this in the final production code because many browsers optimize the prototype and try to guess the location of the method in the memory when calling an instance in advance, but setting the prototype dynamically disrupts all these optimizations and can even force some browsers to recompile for deoptimization your code just to make it work according to the specs. Not supported in IE10 and below. |

When you call

```javascript
var o = new Foo();
```

JavaScript actually just does

```javascript
var o = new Object();
o.[[Prototype]] = Foo.prototype;
Foo.call(o);
```

\(or something like that\) and when you later do

```javascript
o.someProp;
```

it checks whether `o` has a property `someProp`. If not, it checks `Object.getPrototypeOf(o).someProp`, and if that doesn't exist it checks `Object.getPrototypeOf(Object.getPrototypeOf(o)).someProp`, and so on.

You can add a property to a previously defined object type by using the `prototype` property. This defines a property that is shared by all objects of the specified type, rather than by just one instance of the object. The following code adds a `color` property to all objects of type `Car`, and then assigns a value to the `color` property of the object `car1`.

 A getter is a method that gets the value of a specific property. A setter is a method that sets the value of a specific property. You can define getters and setters on any predefined core object or user-defined object that supports the addition of new properties. The syntax for defining getters and setters uses the object literal syntax.

```javascript
var o = {
  a: 7,
  get b() { 
    return this.a + 1;
  },
  set c(x) {
    this.a = x / 2;
  }
};

console.log(o.a); // 7
console.log(o.b); // 8
o.c = 50;
console.log(o.a); // 25


var d = Date.prototype;
Object.defineProperty(d, 'year', {
  get: function() { return this.getFullYear(); },
  set: function(y) { this.setFullYear(y); }
});
var now = new Date();
console.log(now.year); // 2000
now.year = 2001; // 987617605170
console.log(now);
// Wed Apr 18 11:13:25 GMT-0700 (Pacific Daylight Time) 2001
```



