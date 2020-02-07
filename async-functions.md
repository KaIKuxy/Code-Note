---
description: >-
  Async functions - making promises friendly
  https://developers.google.com/web/fundamentals/primers/async-functions
---

# Async Functions

## Async Basics

Async functions allow developers to write promise-based code as if it were synchronous, but without blocking the main thread.

```javascript
async function myFirstAsyncFunction() {
  try {
    const fulfilledValue = await promise;
  }
  catch (rejectedValue) {
    // …
  }
}
```

When await a promise, the function is paused in a non-blocking way until the promise settles.

Using async functions, all the callbacks from promise are gone:

```javascript
// w/o async function
function logFetch(url) {
  return fetch(url)
    .then(response => response.text())
    .then(text => {
      console.log(text);
    }).catch(err => {
      console.error('fetch failed', err);
    });
}

// w/ async function
async function logFetch(url) {
  try {
    const response = await fetch(url);
    console.log(await response.text());
  }
  catch (err) {
    console.log('fetch failed', err);
  }
}
```

Async functions always return a promise, whether the developers use await or not. Anything awaited is passed through Promise.resolve\(\).

## An example

Stream a response while logging out the chunks and return the final size

```javascript
// pure promise
function getResponseSize(url) {
  return fetch(url).then(response => {
    const reader = response.body.getReader();
    let total = 0;

    return reader.read().then(function processResult(result) {
      if (result.done) return total;

      const value = result.value;
      total += value.length;
      console.log('Received chunk', value);

      return reader.read().then(processResult);
    })
  });
}

// async promise
async function getResponseSize(url) {
  const response = await fetch(url);
  const reader = response.body.getReader();
  let result = await reader.read();
  let total = 0;

  while (!result.done) {
    const value = result.value;
    total += value.length;
    console.log('Received chunk', value);
    // get the next result
    result = await reader.read();
  }

  return total;
}
```

Async with arrow functions

```javascript
// map some URLs to json-promises
const jsonPromises = urls.map(async url => {
  const response = await fetch(url);
  return response.json();
});
// array.map(func) doesn't care that I gave it an async function,
// it just sees it as a function that returns a promise.
// It won't wait for the first function to complete before calling the second.
```

Class constructors and getters / setters cannot be async.

## Avoid being parallel in async function

```javascript
async function series() {
  await wait(500); // Wait 500ms…
  await wait(500); // …then wait another 500ms.
  return "done!";
}

async function parallel() {
  const wait1 = wait(500); // Start a 500ms timer asynchronously…
  const wait2 = wait(500); // …meaning this timer happens in parallel.
  await wait1; // Wait 500ms for the first timer…
  await wait2; // …by which time this timer has already finished.
  return "done!";
}
```

A good way of fetching a series URLs and log them as soon as possible, in the correct order.

```javascript
async function logInOrder(urls) {
  // fetch all the URLs in parallel
  const textPromises = urls.map(async url => {
    const response = await fetch(url);
    return response.text();
  });

  // log them in sequence
  for (const textPromise of textPromises) {
    console.log(await textPromise);
  }
}
```





