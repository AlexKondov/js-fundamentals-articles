# Value vs Reference

A big step to becoming a good developer in general is understanding how variables can sometimes contain a value and in others - a reference. This will save you a lot of debugging time and help you write better code.

## Data Types

The data types in JavaScript fall into two broader categories. Strings, numbers, booleans, null and undefined fall in the primitives category. The remaining - arrays, objects and functions fall into the second category which we will call objects.

We will not go into the details of each single data type. Instead we will take a more general look at primitives and objects and understand how each group stores its values.

## Primitives

Primitives are fairly easy to understand. Each time you create a variable it will have its own copy of the value that is passed in.

```javascript
const num = 42;
const str = "Hello!";
```

Each variable contains its own copy of the given value. In other words we can say that each variable points to a specific place in memory which contains its value.

So if we try to assign it to another variable:

```javascript
const a = 7;
let b = a;
b++;

console.log(a, b); // 7, 8
```

When we create the variable `b` it gets its own copy of the value contained in `a`. So when we increment `b`, the value of `a` will stay the same. Even though they contain the same value (the number 7), the value that they store it saved in different places in memory. So modifying one of them does not modify the other.

Let's take a look at how comparison works.

```javascript
const a = 7;
const b = a;

a === b; // true

const c = 7;
const d = 7;

c === d; // true
```

When we compare variables containing primitives, we compare their values. We find what they are storing, we retrieve it from memory and compare that.

## Objects

A common cause of headache for beginners is the expectation that objects should behave the same way primitives do. When I started I couldn't understand why two objects with identical structure would not be equal to each other in particular cases. And I couldn't understand why they would be in others.

```javascript
const obj1 = { a: 1, b: 2 };
const obj2 = { a: 1, b: 2 };
```

Those two objects are completely identical, so judging by what we know from primitives we would expect them to be equal to each other. But to our surprise, comparing them does not return the result we expect.

```javascript
obj1 === obj2; // false
```

Whenever we are creating an object (array, object or function) we will store its value in a particular place in memory and each object will have its contents stored in a different place.

But we already know that primitives behavve in a similar manner. So the problem does not come from how we store objects but how we compare them. When we try to compare two objects we are not comparing their contents but the place in memory that they are pointing to.

This si why two objects with identical structure are not equal to each other - they reference different places in memory.

This is where it gets tricky. Since objects contain a reference to a place in memory we must be really careful of how we use them.

```javascript
const obj1 = { a: 1 };
const obj2 = obj1;

obj1 === obj2; // true
```

Now the comparison returns true because both variables point to the same place in memory. When we assign `obj2` to be equal to `obj1` we are not giving it a separate copy of the object but a reference to the same one. They are sharing the same one. So changing one will also affect the other.

```javascript
const obj1 = { a: 1 };
let obj2 = obj1;

obj2.b = 10;

obj1; // { a: 1, b: 10 }
obj2; // { a: 1, b: 10 }
```

While primitives are fairly easy to understand, the improper understanding of objects and references will lead to errors.
A fairly simple example is mutating a value that is passed to a function

```javascript
const obj = { a: 1, b: 2 }

function mutate(someObject) {
  someObject.c = 3
  // Do something with the object
}

obj // { a: 1, b: 2 }
mutate()
obj // { a: 1, b: 2, c: 3 }
```

This is the reason you want to be extra careful when you are mutating an object, especially one you don't control.
However, you may still want to make a copy of some object and extend it or change its values. Using the new object spread operator
you can do something like this.

```javascript
const obj1 = { a: 1 }
const obj2 = { ...obj1 }

obj1 === obj2 // false
```

By using the spread operator we create a new object containing the values of `obj1`.
When we compare them we see that they have different references, so we can safely work with the second one.
This technique is often used to create a new object and add or change a property.

```javascript
const obj1 = { a: 1 }
const obj2 = { ...obj1, b: 10 }
const obj3 = { ...obj1, a: 2 }
```

In this example, `obj2` will have an extra property on it, while `obj3` will modify the existing property `a` without modifying the original object `obj1`.
