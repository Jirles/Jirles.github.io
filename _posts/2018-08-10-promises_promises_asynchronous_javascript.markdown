---
layout: post
title:      "Promises, promises: asynchronous Javascript"
date:       2018-08-10 19:01:29 +0000
permalink:  promises_promises_asynchronous_javascript
---


Before Promises, Javascript programmers handled asynchronous calls by passing success and failure callbacks: 

```

// passing in callbacks, as in olden times //

function successCallback(result) {
  console.log("Audio file ready at URL: " + result);
}

function failureCallback(error) {
  console.log("Error generating audio file: " + error);
}

createAudioFileAsync(audioSettings, successCallback, failureCallback);

```

Promises provide several advantages over this approach.  A Promise object makes it easier to compose callbacks by acting as a stand-in for the eventual response object, thereby allowing you to attach or chain the callbacks to the Promise through a `.then ( )` function. Promises also guarantee that these callbacks will not be invoked until a response is returned. This provides a more secure control flow while also providing greater flexibility -- you can add multiple callbacks and even other asynchronous calls through these `then( )` functions.  Perhaps most importantly, Promises represent a clearly defined [standard](https://promisesaplus.com/), replacing disparate methods with one common approach.

```

// the same example above, but using a Promise Object //

createAudioFileAsync(audioSettings).then(successCallback).catch(failureCallback);


```

### Schrödinger's Object

Promises can be a bit difficult to understand at first as they represent an eventuality as opposed to a certainty.  The Promise acts as a container or placeholder for an eventual response -- hopefully JSON but possibly an Error. The chained `then( )` and `catch( )` calls are ready to handle either eventuality. 

When I was reading up on Promises, it was helpful for me to compare a Promise to the thought experiment known as [Schrödinger's Cat](https://en.wikipedia.org/wiki/Schr%C3%B6dinger%27s_cat).  A Promise represents a future response, but since that response is not yet known,  the promise is _unsettled_ or _pending_. The cat may be alive (200 status code, JSON object) or dead (Error). The Promise is like a blackbox, you cannot spy on the Promise state until its been resolved. In fact, At any one time, a Promise object can be in one of three  mutually exclusive states:

- _Pending_: Before a result is ready to be acted upon, e.g. before an API has sent data back in response to a GET request, a Promise is considered pending
- _Fulfilled_: A fullfilled Promise means that everything went well, e.g. the GET request was successful and the JSON response is now available to chained callbacks
- _Rejected_: Rejection indicates an error was raised and the request could not be completed, the `catch( )` handles errors

Promise obejcts are also considered to be _unsettled_ (pending) or _settled_(fulfilled or rejected). Once a promise is settled it cannot be returned to an unsettled state or toggled between fulfilled and rejected. __A settled promise is immutable__. After all, once the lid is lifted and you know if the cat is alive or dead, it cannot return to it's previous flux state of alive/dead. 

### An Example with Fetch

I'm probably taking the Schrödinger methaphor a little too far, particularly since Schrödinger's throught experiement was meant to highlight the absurdity of the theory it is now used to illustrate. So code examples to the rescue. 

Javascript's `fetch ( )`  is a commonly used example of a Promise. It is called with at least one argument, the path for a resource one is making an asynchronus call to, and returns a Promise that contains the response. You can then chain `.then( )` and `.catch( )` functions. In fact, `.then( )` returns a Promise object too, so you can chain multiple `.then( )` functions to the initial Promise and each one will pass the resolved data down the chain. If an error occurs in any of the function calls, the error is passed into the `.catch( )` function: 

```

fetch('http://www.example.org')
 .then(resp => resp.json( ))
 .then(json => doSomething(json))
 .catch(err => console.log(err))

```

### An Example with Async/Await

Async/await is a little bit of syntactic sugar built over top of Promises that lets us write asynchronous JS in a synchronous fashion. To illustrate what this entails, let's shamelessly steal some examples from [MDN's article on using promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises):

```

// a Promise that does something

doSomething()
.then(result => doSomethingElse(result))
.then(newResult => doThirdThing(newResult))
.then(finalResult => console.log(`Got the final result: ${finalResult}`))
.catch(failureCallback);

```

This code mirrors the following synchronous code style: 

```

try {
  const result = syncDoSomething();
  const newResult = syncDoSomethingElse(result);
  const finalResult = syncDoThirdThing(newResult);
  console.log(`Got the final result: ${finalResult}`);
} catch(error) {
  failureCallback(error);
}

```

`async`/`await` allows programmers to have their cake and eat it too. These keywords allow us to write code that mimics the easy readibility of the synchronous code above while still taking advantage of asynchronous promises that don't block the execution of the rest of the code thread.

```

async function foo() {
  try {
    const result = await doSomething();
    const newResult = await doSomethingElse(result);
    const finalResult = await doThirdThing(newResult);
    console.log(`Got the final result: ${finalResult}`);
  } catch(error) {
    failureCallback(error);
  }
}

```

Using `async`/`await`, you declare an asynchronous function with the keyword `async`. Within the `async` function the keyword `await` pauses the function in a non-blocking way until the promise settles and returns a value. Essentially, it is taking the place of our `.then( )` function. Note that `await` can only be called within an `async` function block.

You'll probably be seeing `async`/`await` functions more in the future. After all, they have several important advantages over chaining `.then( )` and `.catch( )` calls, particularly in ease of readability and composition. But underneath all this syntactic sugar are the same old Promise objects, so it pays to understand a little bit of what goes on under the hood.




