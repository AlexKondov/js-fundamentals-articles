# Coercion

Coercion is the term that is used for unexpected type casting in JavaScript. Often when you are working with data you will need to transform it from one data type to another — something that every developer does on a daily basis. Coercion refers to those not-obvious type casts that happen as a side-effect of different operations.

Most developers that are new to the language see this as something that is here to make their lives harder. Some see it as a flaw in the design of JavaScript and try to avoid it. And those who learn the details of coercion in JS can use it as a powerful feature of the language.

Unless you want to write your own JavaScript engine, chances are you will have to work with coercion. Even if you decide to avoid it as much as possible, it’s essential to know what to work around and how.

Of course, if not handled properly coercion can be destructive and lead to unwanted bugs if not taken into consideration. In statically typed languages we are warned beforehand for any errors that may occur when we use the wrong data type for a function or operation.

Let's consider coercion as a useful feature that is put in place in order to give us more flexibility when doing operations between different data types.

## Strings and Numbers

One approach to convert a number to a string is to add an empty string to it. Let's examine how this actually works.

```javascript
14 + ""; // "14"
```

Whenever we are using “+” or “-” the values that we are adding or subtracting must be of the same type. Now in the above example they are obviously not.

So what will happen is that JavaScript will try to convert them to the same type and then do the operation. A useful hint to remember is that any "+" operation involving a string will result in a string.

```javascript
42 + "0"; // "420"
```

We can concat strings using "+" and from the example above we see that we achieve the same behavior when we use a number with a string. Again, JavaScript will need to coerce them to the same type - in this case a string. Then it will add them together and since they are strings they will be glued together

We can concat strings using “+”, but what will happen if we use “-”? This operator makes sense to be used only with numbers so JS will try to cast the values to that type this time.

```javascript
"42" - 7; // 35
"42" - 0; // 42
"42" - "9"; // 33
```

Okay that’s not that hard, but there is always a catch. Let’s try adding two arrays together and see what happens:

```javascript
["alex", "sam"] + ["jon", "mary"]; // "alex,samjon,mary"
```

This one is a bit trickier. If you cast an array to string its values will be printed as a comma-separated list. Following the logic above, JS will cast both arrays to strings and then concatenate them together.

Another interesting case is what happens when we use the “-” operator. As I mentioned earlier, it is reserved only for numeric types, so once again, the useful JavaScript language will cast the values to numbers (if possible) and then execute that operation on the resulting values.

```javascript
[3] -
[1][("alex", "sam")] - // 2
  ["jon", "mary"]; // NaN
```

JavaScript just tries to save you some time whenever it can. Some developers expect it to behave like other languages and that leaves them unhappy with the results. The more you get used to the JS specifics the less bugs you will introduce in your code.

## Booleans

This has undoubtedly caused trouble even for more experienced developers. Boolean coercion is often used in if statements, loop checks, ternary and logical operators.

Whenever you’re using one of those, if the variable in it is not a boolean it will be coerced to one. But the most important thing to remember is that boolean operations return a value. The returned value is one of the two operands and it depends on the result of the operation.

```javascript
const a = 100;
const b = "test";
const c = null;

a || b; // 100
a && b; // "test"
a || c; // 100
a && c; // null
b || c; // "test"
b && c; // null
```

The value that we get with those operations depends first on the operator and then on the values that we use.

When we use || if the first operand is a truthy value (if it casts to true) we will get that value returned. In all other cases we will get the second. Even if it's not a truthy value.

In the case of && you will always get the second value if they are both coerced to true. If the first one casts to false then you will get it’s value returned.

Right now you’re probably thinking about the flaws of the JavaScript language and considering your career choices, but let me just remind you that this is the exact same behavior you get in Python and Ruby (surprise). Their inheritance implementation also behaves in a similar matter to prototypical inheritance but this is a different topic.

Now that we are familiar with how those operators work we need to examine how this behavior can be useful. A common use case is to specify a default value when assigning a value to a variable.

```javascript
const userName = "";
const name = userName || "Guest";

name; // 'Guest'
```

Here we use the || operator to check whether a user name exists. As we know if the first value does not coerce to true we will always get the second one returned.

In ES6 we got default values in function parameters but before that this was the only way to specify default values. It was a common occurance to have a long list of similar statements on top of a function's body.

```javascript
const value = obj.property && obj.property.nestedProperty;
```

When accessing deeply nested properties there is always the risk of one of the properties in the chain missing or being of an incorrect type.

To avoid errors we can make a conditional check to make sure everything is in place but by using the && operator we can cut a corner here.

If the first value is truthy it means that it exists, so it will always return the second one - the one we actually want.

## Equality

When talking about coercion we must also mention the equality operators and common misconceptions about them.

The popular explanation is that == compares values without checking types and === compares both values and types. This explanation is not wrong but it is not entirely correct.

As we mentioned earlier, some operations cannot be run on values of different types and checking for equality is one of them. And we know that JavaScript is capable of casting values to different types on the fly to make sure that we get the behavior we want. This is in the core of equality checking as well.

Whenever we compare two values using ==, JavaScript will use coercion to cast the values to be of the same type. On the other hand when we use strict equality === JS won’t type cast the values (meaning that if they are of different types they’re not equal).

Simply put, == is comparison which allows coercion, === is comparison without coercion.

```javascript
42 == "42"; // true
42 === "42"; // false
```

## Conclusion

In conclusion, coercion is something that you will encounter on a daily basis as a JS developer, so understanding it is crucial. The information in this article is enough to get a general understanding but there is a lot more to learn about the nature of the helpful language that is JavaScript.
