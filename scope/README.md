# Scope

Like everything else in JavaScript, scope isn’t anything special. Even though most developers don’t spend much time reading about it, having a deeper understanding of it will help you write cleaner applications, reduce errors and implement better design patterns.

> Scope is the visibility of functions and variables in the different parts of your code during runtime.

Most developers take scope understanding for granted, but to no surprise, JavaScript has it’s specifics. Variables are either global scoped or function scoped. That means that they either exist globally and can be accessed everywhere or in the boundaries of the function in which they were declared.

```javascript
var global = 'I am global scoped'

function testingFunctionScope() {
    var global = 'I am function scoped';
    console.log(global); // I am function scoped
}

testingFunctionScope(); 
console.log(global); // I am global scoped
```


As you can see in the example, even though the global variable is assigned a different value inside the function, it is only retained in the boundaries of the same function. Outside, the variable has a different value — the one declared in the global scope.

```javascript
var global = 'global'
var anotherGlobal = 'also global'

function functionScope () {

    var global = 'function scope'
    console.log(global) // function scope
    var scoped = 'also function scope'

    function inner () {
        console.log(scoped); // also function scope
        console.log(anotherGlobal) // also global
    }

    inner();

}

console.log(global) // global
console.log(anotherGlobal) // also global
console.log(scoped) // ReferenceError

functionScope();
inner(); // ReferenceError
```

Here’s another example with a nested function and how it can access variables declared in it’s parent scope. Just like it’s parent function functionScope(), every variable declared in it is only available inside it and it’s nested functions.

Something to note here: NEVER use undeclared variables. By doing that, the engine will bubble up to the parent scope looking for the variable. If it doesn’t find it, being the useful language that JS is, it will declare it for you. That way you may unintentionally create global variables and clutter the global scope.

Also, don’t think of undefined and undeclared as the same thing. Undefined means that a given declared variable does not have a defined value, while undeclared means that a variable has not yet been declared at all.

Okay, up until now there isn’t anything too special going on, but what would this code return:

```javascript
test(); // ???
function test() {
  console.log('working?!');
}
```

Well to the surprise of some, this will actually print “working?!”. In order to understand why this is working we must go into another JavaScript concept.

## Hoisting
Here I am going to do something most people won’t. I will describe what hoisting is and then explain how this is not true. Confused? Good — JavaScript should be confusing. By hoisting, we mean this exact strange behavior in which you can call a function before it was defined.

In JavaScript, BEFORE the code is executed variables and functions are moved to the top. So the strange piece of code above, actually becomes:

```javascript
function test() {
  console.log('working?!');
}
test(); // working?!
```

Therefore, what we write is not exactly what is being executed. The same happens with variable definitions — they are all moved to the top of their scope (if they are declared with the ‘var’ keyword; ‘let’ variables are not being hoisted). What’s different about variables is that only their definition is moved to the top. The value assignment stays where it is, but the definition of the variable is hoisted. Therefore if we try to use a variable before a value is assigned to it we will get undefined, not an error.

```javascript
console.log(test) // undefined
var test = 'working?'
console.log(test) // working?
```

This is how the above code is looking after the variables were hoisted to the top:

```javascript
var test;
console.log(test) // undefined
test = 'working?'
console.log(test) // working?
```

So be careful when you’re using function expressions, because only their declaration will hoist to the top. We’ve now got a basic understanding of how scoping and hoisting works in JavaScript, but in fact it’s quite different. The whole process of executing JS code is quite different and hoisting is not happening like most people believe. It is more of a mind concept that we carry.

## Compilation
Contrary to most beliefs, JavaScript does compile. The V8 Engine increases performance by compiling JavaScript to native machine code before executing it, instead of executing byte code or interpreting it. What is actually happening is that the engine is making multiple runs through the code. During one of them, it declares all functions and variables, so on run-time they already exist. With hoisting we just create this mental image of how code is reorganized before execution, even though explaining the compilation process is a lot easier.

Simply put — before JS code is actually executed, the engine has already ran through the whole script and declared all variables and function declarations in their particular scopes. So on runtime, they already exist, there isn’t any black magic that pulls them to the top of the scope. If you understand this, you will have more knowledge than 90% of other JavaScript developers.

## Conclusion
JavaScript scoping is quite the topic and there is much more to be learned. I haven’t even mentioned LHS and RHS lookups in order to keep this short and give you only the basics. JavaScript can and will have unexpected behavior if you don’t know what you’re doing. All in all, don’t redeclare variables, use good descriptive names and try not to call or execute anything before it was actually declared to save yourself some headache.