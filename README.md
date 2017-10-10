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
homomorphism. Try to keep the return type of a function consistent.

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

const printNames = () => listUsers().forEach(user => {
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

### Switch

In functional programming, imperative structures do not exist. `switch` is intended to have
effects and known to have a complex flux.

### Try

Error handling shouldn't be handled by exceptions, but by either monads or promises.

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
