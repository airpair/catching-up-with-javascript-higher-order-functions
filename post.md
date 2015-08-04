They are all around you, your code, your company, your community, your frameworks. You may not be aware of them or perhaps you never properly understood what is going on about them, but trust me: your life would never be the same without them. They are unique and pave the way for **Functional programming** (although Functional programming is not only about them). With you, **Higher-order functions**!

## Higher-order functions 101

According to [Wikipedia](https://en.wikipedia.org/wiki/Higher-order_function), a **higher-order function** is a function that does at least one of the following:
1. takes one or more functions as an input
1. outputs a function

All other functions are **first-order functions**.

In JavaScript, functions are first-class citizens, which means they can go anywhere other data types would go (like integers, strings, arrays and objects). While this might not be big news for you (I'd bet you wrote callbacks once or twice), it means that you can write optimized code for higher-order functions and take advantage of its properties.

Ok, but why should you bother with those fancy-pants functions?

In a glance: they are powerful, very expressive, easy to read, great to reuse, effective to maintain and will also tidy your code.

Just to name a few use cases:

* You can abstract your work into small chunks (think work steps), and feed them to other functions.
* You can decorate functions in order to augment their action.
* You can write functions that create other functions dynamically.
* You can compose a serie of functions to generate other functions.

## Programming paradigms

Before we proceed, let's take a quick dive into some fundamental programming paradigms:

**Imperative programming** is one of the most common programming paradigms around us, found in programs written in languages as C++, Python, Ruby, Java and C#. I would bet this paradigm was being used when you learned how to program.
 
This is about *telling your computer HOW to do its work and figuring out the details yourself*, so that the code:

- is based on a sequence of low-level/detailed steps that must happen in order
- describes each operation in minimal details
- makes changes to the data (mutable)
- manipulates the application state (stateful)

On the other hand, **Declarative programming**, found in programs written in languages as SQL, Haskell, Clojure and Scala, is about *telling your computer WHAT you want and letting him figure out the details*. This way, the code:

- is based on a sequence of high-level/abstracted steps that might not require an order
- relies on an engine, interpreter or API to fill in the minimal details of each operation
- doesn't make changes to the data (immutable)
- keeps the application state intact (stateless)

Now, **Functional programming** is a powerful programming paradigm under Declarative programming, which stands on the shoulders of *higher-order functions*. It is based out of math, striving to describe computation as the evaluation of mathematical functions.

There are some basic principles that must always be followed:

1. **Functions as a flow**: functions should be used to abstract operations, and your code should be described by a sequence of high-level functions instead of a sequence of low-level statements.
1. **Data immutability**: data in functional programming should be immutable, which means any operation should be made over a new copy of the data, leaving  the original data untouched.
1. **Function purity**: functions should be pure, which means they should be indefinetely repeatable (idempotent) and should not keep track of its past (stateless). This way, they should always return the same output for a particular input and won't generate any side-effect.

Actually, Functional programming is way more than that! There are great concepts as currying, composing, functors, point-free, monads... Anyhow, this article is really about Higher-order functions, so we won't be going any deeper in Functional programming for now.

JavaScript is considered a hybrid language, so that it is possible to write code either following an Imperative or a Declarative/Functional approach.

## Imperative vs Functional approach

In order to illustrate the power of higher-order functions, there is nothing better than looking into different implementations of the same program. Suppose your requirement is:

> Write a ```Number``` function which takes a number and implements the operations ```half()```, ```third()``` and ```multiplyBy(anotherNumber)```.

Let's first have a look at a typical object-oriented Imperative implementation in JavaScript:

```javascript
// Number definition
var Number = function(number) {
    this.number = number;
};

Number.prototype.half = function() {
    this.number /= 2;
};

Number.prototype.third = function() {
    this.number /= 3;
};

Number.prototype.multiplyBy = function(anotherNumber) {
    this.number *= anotherNumber.number;
};
```

```javascript
// Calculate ( ( 270 / 3 / 2 / 3 ) * ( 270 / 3 / 3 / 3 ) ) / 2
var n1 = new Number(270);
n1.third();
n1.half();
n1.third();

var n2 = new Number(270);
n2.third();
n2.third();
n2.third();

n1.multiplyBy(n2);
n1.half();
// n1.number = 75, n2.number = 10
```

Here are some observations about this implementation:
- Notice how each operation changes the state of the instances of ```Number```. Thus, data is mutable.
- The method ```third``` is called multiple times in a row to modify the internal state of the instances, which can be misleading. Just ask yourself: can you easily tell what is the current state of each instance after each operation?. 
- Two different instances of ```Number(270)``` need to be created so that the method ```multiplyBy``` can be called. 
- The operations' definitions only work with ```Number```, which is not much reusable.

Now, the same program in a more Functional approach, using the higher-order function ```apply``` behind the scenes:

```javascript
// Number definition
var Number = function(number) {
    var apply = function(fn) {
        return function(n) {
            return new Number(fn(number, n && n.number));
        };
    };
    return {
        number: number,
        half: apply(function(a) {
            return a / 2;
        }),
        third: apply(function(a) {
            return a / 3;
        }),
        multiplyBy: apply(function(a, b) {
            return a * b;
        })
    };
};
```

```javascript
// Calculate ( ( 270 / 3 / 2 / 3 ) * ( 270 / 3 / 3 / 3 ) ) / 2
var n = new Number(270);
var result = n.third().third().third()      // temp result = 15
    .multiplyBy(
        n.third().half().third()            // temp result = 10
    )                                       // temp result = 150
    .half();
// result.number = 75, n.number = 270
```

Follow the observations about this implementation:
- In this program, every operation returns a new instance of ```Number```. Thus, data is immutable.
- The operations can be chained in such a way the intermediate results need not to be stored. This chained API is easy to use and understand.
- ```third```, ```half``` and ```multiplyBy``` can be called multiple times in a row with no consequence to the internal state of the instances. This way, we don't need to worry with side-effects.
- Also, the operations' definitions are very generic, as the arguments are plain numbers as ```a``` and ```b``` instead of instances of ```Number```. This way, they are way more reusable.
- Because of that, only one instance of ```Number(270)``` is needed so that the method ```multiplyBy``` can be called.
- We are using here the concept of **partial application** with the higher-order function ```apply```, as this function is generating another function where we are partially applying the 1st argument as the internal value of ```Number```.

## To be continued...

The intent of this article is to illustrate the power of higher-order functions and hopefully to motivate you, my fellow reader, to start using them in your everyday work.

Little by little, you will get so used to them in JavaScript that you may even feel a bit helpless if you go back to a language that doesn't support them. Besides, as a bonus, you will get your foot in the door of Functional programming!
