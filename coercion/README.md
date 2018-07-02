# Coercion

Coercion is the term that is used for unexpected type casting in JavaScript. Often when you are working with data you will need to transform it from one data type to another — something that every developer does on a daily basis. Coercion refers to those not-obvious type casts that happen as a side-effect of different operations.

Most people see this as something that is here to make their lives harder. Some see it as a flaw in the design of the language and try to avoid it. However, I suggest we take a deeper dive, understand how it works and embrace the JS chaos. Unless you want to write your own JavaScript engine, chances are you will have to work with coercion. Even if you decide to avoid it as much as possible, it’s essential to know what to work around and how.

Of course, if not handled properly coercion can be destructive and lead to unwanted bugs and complications in your codebase. But for the time being, let’s focus on another point of view — that coercion is necessary in order to simplify our code and get rid of unnecessary boilerplate that would make our code messier.

## Strings and Numbers
Let’s start easy — how can we combine numbers and strings? How do we make a string out of a number and vice versa? The simplest and often used way to cast a number to a string is by using the + operator.

```javascript
14 + "" // "14"
```

Whenever you are using “+” or “-” the values that you are adding or subtracting must be of the same type. Now in the above example they obviously are not. So what will happen is that JS will try to even them out — in this case cast our number to a string and then add them together. In other words every “+” expression that involves a string will result in a string.

```javascript
42 + "0" // "420"
```

We can concat strings using “+”, but what will happen if we use “-”? It can be used only on numbers, so once again, JS will cast the values to be of the same type — this time numbers.

```javascript
"42" - 7 // 35
"42" - 0 // 42
"42" - "9" // 33
```

Okay that’s not that hard, but there is always a catch. Let’s try adding two arrays together and see what happens:

```javascript
["alex", "sam"] + ["jon", "mary"] // "alex,samjon,mary"
```

Wait, what just happened? Now, whenever you use the “+” operator, the values will concatenate if one is already a string or they will be cast to strings if possible. Therefore, you get the two arrays’ string representation which are then added together. It’s logical once you understand it, but it can lead to disappointments when it happens unexpectedly.

Another interesting case is what happens when we use the “-” operator. As I mentioned earlier, it is reserved only for numeric types, so once again, the useful JavaScript language will cast the values to numbers (if possible) and then execute that operation on the resulting values.

```javascript
[3] - [1] // 2
["alex", "sam"] - ["jon", "mary"] // NaN
```

JavaScript just tries to save you some time whenever it can. Some developers expect it to behave like other languages and that leaves them unhappy with the results. The more you get used to the JS specifics the less bugs you will introduce in your code.

## Booleans
This has undoubtedly caused trouble even for more experienced developers. Boolean coercion is often used in if statements, loop checks, ternary and logical operators. Whenever you’re using one of those, if the variable in it is not a boolean it will be coerced to one. More importantly, the result of a logical expression, for example is not necessarily a boolean but the value of one of the two operands that are used. Maybe an example would be better:

```javascript
const a = 100
const b = "test"
const c = null
a || b // 100
a && b // "test"
a || c // 100
a && c // null
b || c // "test"
b && c // null
```

Let’s break down what’s going on here. Developers coming from other languages might expect this to return true or false when they use it in conditional checks, for example. In fact what you get is the value of one of the operands.

When you use the || operator, if the first value casts to true you will get that value returned. Otherwise, you will always get the second one. In the case of && you will always get the second value if they are both coerced to true. If the first one casts to false then you will get it’s value returned.

Right now you’re probably thinking about the flaws of the JavaScript language and considering your career choices, but let me just remind you that this is the exact same behavior you get in Python and Ruby (surprise). Their inheritance implementation also behaves in a similar matter to prototypical inheritance but this is a different topic.

So, why is this useful? Something that can often be seen is initializing variables like this:

```javascript
function greet (name) {
    name = name || 'visitor'
    console.log(`Hello, ${name}!`)
}
greet() // Hello, visitor!
```

Here we’re using this exact behavior to check if a value is missing and assign another one if it is. You can see this in many codebases. As a side note, in ES6 you can define default values directly in the function arguments in order to avoid checking all the passed values in the beginning of the function.

## Equality
When talking about coercion we must also mention the equality operators and common misconceptions about them. The popular explanation is that == compares values without checking types and === compares both values and types. While this is a simple way to explain what’s going on it’s not entirely true.

As we mentioned earlier, some operations cannot be run on values of different types, so behind the curtains JavaScript will use coercion to make our lives easier. Whenever we compare two values using ==, JavaScript will use coercion to cast the values to be of the same type. On the other hand when we use strict equality === JS won’t type cast the values (meaning that if they are of different types they’re not equal).

Simply put, == is comparison which allows coercion, === is comparison without coercion.

```javascript
42 == "42" // true
42 === "42" // false
```

## Conclusion
In conclusion, coercion is something that you will encounter on a daily basis as a JS developer, so understanding it is crucial. The information in this article is enough to get a general understanding but there is a lot more to learn about the nature of the helpful language that is JavaScript.