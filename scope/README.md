# Scope

> Scope is the visibility of functions and variables in the different parts of your code during runtime.

In other words, the scope of a variable or a function is the part of the code in which it is available. Working with variables and scope comes intuitively to most developers. We have some mental models that help us understand when something can be off limits.

To master the JavaScript language we must be looking to build on top of those existing mental models and go into the specifics. While I'm sure that every developer would do fine writing code without a deep understanding of scope, only those who have put in the time to read the small print will be able to avoid all pitfalls and answer the trickiest interview questions.

## Types of Scope

In JavaScript there are three types of scope:

* **Global Scope** - variables can be accessed everywhere.
* **Functon Scope** - variables can be accessed in the boundaries of the function in which they are defined.
* **Block Scope** - variables can be accessed in the block in which they are defined. A block is separated by `{` and `}`.

```javascript
const global = 'I am global scoped'

function fn() {
    const global = 'I am function scoped';
    console.log(global); // I am function scoped
}

fn(); 
console.log(global); // I am global scoped
```


As you can see in the example, even though the `global` variable is assigned a different value inside the function, it is only retained in the boundaries of the same function. Outside of it, the variable has a different value  —  the one declared in the global scope. We also won't get an error for using the same variable name.

```javascript
const global = 'global'
const anotherGlobal = 'also global'

function fn () {

    const global = 'function scoped'
    console.log(global) // function scope
    const scoped = 'also function scoped'

    function inner () {
        console.log(scoped); // also function scope
        console.log(anotherGlobal) // also global
    }

    inner();

}

console.log(global) // global
console.log(anotherGlobal) // also global
console.log(scoped) // ReferenceError

fn();
inner(); // ReferenceError
```

Here we can see that the `inner()` function will have access to the variables declared in its parent function - `fn()`. Whenever we need a variable inside a function, the engine will first look for it in the scope of the current function. Then it will go one level up in the function containing it. If it doesn't find it there it will continue to go up until it reaches the global scope. If the variable is not found there we will get a `ReferenceError`.

## Undeclared variables

JavaScript won't stop you from using undeclared variables. This means that at any point you can assign a value to a variable without using `const`, `let` or `var`. When you do this the engine will try to look for the variable bubbling up to the global scope. If it doesn't find it there it will create a global variable for you. 

As a rule of thumb, avoid poluting the global scope. If a variable is meant to be globally used then declare it in the global scope so your intentions can be clear. Otherwise keep it inside the scope it is used in.

## Hoisting

The hoisting of functions and variables in JavaScript is the reason why we can use some of them before they are actually declared.

```javascript
test(); // ???

function test() {
  console.log('working');
}
```

The code above will log the string `"working"` even though the function is called before it's declared.

The common explanation that is given for this behaviour is that function and variable declarations are pulled to the top of **their scope**. So when you try to access them they have already been declared. To the example above will actually become:

```javascript
function test() {
  console.log('working');
}

test(); // working
```

Variables are also hoisted but in a different way. Only the declaration of the variable is "pulled" to the top. The value assignment will stay where it is. In other words, we **can't** access the value of a variable the same way we can call functions.

```javascript
console.log(test) // undefined
var test = 'working'
console.log(test) // working?
```

This is how the above code will look after the variables were hoisted to the top:

```javascript
var test;
console.log(test) // undefined
test = 'working'
console.log(test) // working?
```

This is why we need to be careful when we are using **function expressions**.

```javascript
console.log(test) // undefined
console.log(test()) // TypeError: test is not a function
var test = function () {
    console.log('test')
}
```

Again, only the declaration is pulled to the top while the actual assignment of the function as a value will stay where it is. So when we try to call `test` as a function we will get an error.

## Block Scope

In ES6 we got two new variable declaration keywords - `let` and `const`. The main difference between them and `let` is that variables declared with the ES6 keywords are block scoped. This means that they are available only in the code block they were defined in. A code block is separated by curly braces.

Since we are discussing scope, it's important to note that variables created with `let` or `const` are not hoisted. This means that they definition is not pulled to the top like variables declared with `var`.

```javascript
console.log(test) // ReferenceError: test is not defined
const test = 'working'
```

One way to make sure that your code is free from reference errors is to make sure that you are using only `let` and `const` for variable declaration.

## Compilation

Now that we understand the concept of hoisting we will look into the internals of the language and understand how it actually works. The V8 engine increases performance by compiling JavaScript to mchine code before executing it. What happens in the process of compilation is that the engine makes multiple runs through our code. In one of the early runs it will declare all functions and variables. So when the code is executed they will already be defined.

So, hoisting is actually a mental image to help us illustrate why we can access functions and variables before they are defined. The common explanation of hoisting is just a picture of how code is reorganized before execution. When the engine is actually executing our code our function and variable declarations are no longer there at all. They have already been declared in a previous run.

## Conclusion

The concept of scope comes intuitively to developers, but in order to achieve mastery in a language we must be aware of its specifics. The things that we need to keep in mind as JavaScript developers are:

* **Undeclared Variables**
* **Block Scoped Variables**
* **Hoisting**