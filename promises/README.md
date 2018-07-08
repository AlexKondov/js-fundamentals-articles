# Promises

Promises are a  relatively new feature to JavaScript and can be a bit hard to grasp at first. Essentialy, a Promise represents a future action. Something that will happen in the future but you are not yet sure what will be the outcome of it. It may happen or it may fail, but you do not yet know that. Therefore we can say that a Promise has three possible states:

* pending
* succeeded
* failed

When we construct a promise we need to provide it with a function that takes two parameters - `ressolve` and `reject`. Of course, they can be called any way we want but this is the accepted naming convention. Those two values are functions which we can call from our Promise to signal if it succeeds or fails.

```javascript
const mood = 'happy'

const goingOut = new Promise((resolve, reject) => {
  if (mood === 'happy') {
    resolve()
  } else {
    reject()
  }
})
```

The way we consume a promise is to specify what will happen after it succeeds or fails.

```javascript
goingOut
  .then(() => /* The promise resolved successfuly, we take some action */)
  .catch(() => /* The promise got rejected, so we can handle the error */)
```

Promises can also pass values when they resolve or are being rejected.

```javascript
const goingOut = new Promise((resolve, reject) => {
  if (mood === 'happy') {
    resolve('We are going out.')
  } else {
    reject('We are staying home...')
  }
})

goingOut
  .then(msg => console.log(msg)) // We are going out.
  .catch(err => console.log(err)) // We are staying home...
```

Promises provide a clean API when you need to chain a few operations together. We can call a function inside `then` and if the returned value is a promise we can chain it to another `then`.

```javascript
const pickDestination = (msg) => {
  return new Promise((resolve, reject) => {
    // Do something here...
    resolve(`${msg} We are going to a restaurant.`)
  })
}

goingOut
  .then(pickDestination)
  .then(msg => console.log(msg)) // We are going out. We are going to a restaurant.
```

If we want to return a resolved or rejected promise we can use the `.resolve` and `.reject` functions on the `Promise` object directly. The `pickDestination` function will then look like this.

```javascript
const pickDestination = (msg) => {
  return Promise.resolve(`${msg} We are going to a restaurant.`)
}
```

Promises are used mostly for asynchronous operations. Tasks such as a request to a service that you are not sure exactly when are going to be completed or if they will succeed.

## ES7 - async/await

In ES7 we can use the async/await syntax for asynchronous operations. The benefits of the new keywords is that our asynchronous code will look more synchronous and we can handle errors using simplle try/catch blocks.

```javascript
async function willGoOut = () => {
  try {
    const goingOut = await goingOut()
    const message = await pickDestination(goingOut)
  } catch (err) {
    console.log(err) // We will be staying home.
  }
}
```

Each function in which you want to take advantage of `await` must be specified as `async`. Another thing to be aware of is that each `await` will pause the execution of your program. Chaining a few awaits one after another can lead to slow execution.

In cases in which you need multiple requests resolved, which are not depending on each other, you can use `Promise.all`

```javascript
const [ value1, value2 ] = await Promise.all([
  firstRequest(),
  secondRequest()
])
```