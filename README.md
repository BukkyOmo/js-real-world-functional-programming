# Real world functional programming in JS

Tips and guidelines for scalable and easily maintainable code bases!

![](https://raw.github.com/fantasyland/fantasy-land/master/logo.png)

## Summary

- [Recommended libraries](#recommended-libraries)
  - [Ramda](#ramda)
  - [Ramda Fantasy](#ramda-fantasy)
- [Do](#do)
  - [Return everything](#return-everything)
  - [Tacit programming](#tacit-programming)
  - [Use modules](#use-modules)
  - [Composition over inheritance](#composition-over-inheritance)
- [Avoid](#avoid)
  - [Mutability](#mutability)
  - [Loops](#loops)
  - [Switch](#switch)
  - [Try](#try)
  - [Classes](#classes)
  - [Callbacks](#callbacks)
- [Advantages](#advantages)
  - [Optimization](#optimization)
  - [Testing](#testing)
  - [Scalability](#scalability)

## Recommended libraries

### Ramda

Ramda is like Lodash made right. It has a lot of functions to work with data
and to work with composition. Unlike Lodash, in Ramda, functions come before
data.

### Ramda Fantasy

Ramda Fantasy is a set of common monadic structures to work with values, errors,
side-effects and state.

## Do

### Return everything

Everything should return, also functions that emit side-effects. Try to preserve
homomorphism. Try to keep the return type of a function consistent. Don't mix
computations that transform data with things like writing to screen. Modular code
is easier to maintain. Use parameters for replaceable data instead of hard-coding.

#### Don't

```js
let result = 1;
for (let i = 2; i <= 5; i++) {
    result *= i;
}

console.log('Fact of 5: ', result);
```

#### Do

```js
const fact(n) = n === 0
    ? 1
    : n * fact(n - 1);

console.log('Fact of 5: ', fact(5));
```


### Tacit programming

Tacit programming is also known as point-free programming. It means, basically, using
simple functions to compose more complexes functions (and omitting arguments). One of
the functional programming pillars is composition.

### Use modules

Isolate your logic inside modules that do one thing, and do that well. Modules
should export functions and, when using a type checker, types.

### Composition over inheritance

Inheritance is definitely **not** how you deal with data and behavior in functional programming.
Computations are modeled using behaviors. Some languages call them type classes.

## Avoid

### Mutability

Functional programming heavily relies on immutability. Redefining a value or the property of an
object is therefore forbidden. Don't use neither `var` nor `let`. Everything should be a `const`.
Don't use functions like `.push` or `.splice` because they change the value of the original
parameter and are error-prone.

#### Don't

```js
var approved = []

for (var i = 0; i < approved.length; i++) {
    if (users[i].score >= 7) {
        approved.push(approved)
    }
}
```

#### Do

```js
const approved = filter(user => user.score >= 7, users)
```

### Bare code

Code outside a function is a side-effect inside a module. Effects should be explicit. Only
static declarations are allowed.

#### Don't

- `user.js`
```js
const usersList = User.find().asArray()

usersList.forEach(user => {
    console.log(user.name)
})
```

#### Do

- `user.js`
```js
const listUsers = () => User.find().asArray()

export const printNames = () => listUsers().forEach(user => {
    console.log(user.name)
})
```

- `index.js`
```js
import { printNames } from './user'

printNames()
```

### Loops

Native statement loops are forbidden. Loops are made to enforce side-effects and there is no
case of a loop where a side-effect wouldn't be expected. You don't need to use recursion most of
the time. There is a lot of functions and compositions that you can use to achieve your logic.
Ramda provides some functions like `map`, `filter` and `reduce` that make loops completely
unnecessary.

#### Don't

```js
const even = [];
for (let i = 0; i <= 300; i++) {
    if (i % 2 === 0) {
        even.push(i)
    }
}

console.log(even) // [0, 2, 4, 6, 8 ...]
```

#### Do

```js
import { filter, range } from 'ramda'

const even = filter(n => n % 2 === 0)

console.log(even(range(0, 300))) // [0, 2, 4, 6, 8 ...]
```

### Switch

In functional programming, imperative structures do not exist. `switch` is intended to have
effects and known to have a complex flux. You can use the function `cond` from Ramda instead.
`cond` receives a list of pairs of functions where the first one is the predicate and the
second is the transformation.

#### Don't

```js
const person = { name: 'Wesley' }
let result;

switch (person.name) {
    case 'Dayana':
        result = person.name + ' is cool!'
        break
    case 'Wesley':
        result = person.name + ' likes farting'
        break
    default:
        result = 'Who is ' + person.name + '?'
}

console.log(result) // Wesley likes farting
```

### Do

```js
import { T, cond, propEq } from 'ramda'

const getDescription = cond([
    [propEq('name', 'Dayana'), ({ name }) => `${name} is cool!'],
    [propEq('name', 'Wesley'), ({ name }) => `${name} likes farting'],
    [T, ({ name }) => `Who is ${name}?`]
])

console.log(getDescription({ name: 'Wesley' })) // Wesley likes farting
```

### Try

Error handling shouldn't be handled by exceptions, but by either monads or promises.

#### Don't

```js
try {
    undefined.property
} catch (err) {
    console.log(err)
}
```

#### Do

```js
import { tryCatch } from 'ramda'
import { Either } from 'ramda-fantasy'

const computation = tryCatch(
    () => undefined.property,
    Either.Right,
    Either.Left
)

console.log(computation()) // Left<TypeError>
```

### Classes

In general, using classes enforce effects and directly mutability. You can replace them by literal
objects and functions that work on these objects.

### Callbacks

Callbacks can guide you easily to _Hadouken_ code. Promises or _futures_ are the way to go here.
If you are using a library that uses callbacks, you can _promisify_ the function to transform the
callback to a promise.

## Advantages

### Optimization

Pure functions are easier to optimize. They can cached with their parameters, as long as having
the same input will always generate the same output.

### Testing

TDD is the way to go here. Pure functions are very easier to test and have no dependencies in
general. Having a 100% coverage on a functional code base is a lot easier than in an imperative
one.

### Scalability

Functional code is easier to scale. Having, for example, a stateless server will allow you to
have multiple instances of it in different machines without worrying with shared and dependent
data. Running on clusters and multiple processors is possible and V8 can optimize to run
pure computations on different processes.
