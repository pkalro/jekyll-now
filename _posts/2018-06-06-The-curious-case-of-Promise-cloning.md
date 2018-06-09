---
layout: post
title:  "The curious case of Promise cloning in JavaScript"
date:  2018-06-06
excerpt: "Promise cloning requires a deep understanding of the basic mechanism of how Promises work in JavaScript"
image: "/images/2018-06-06_promiseStates.png"
---

## The problem at hand
Recently I came across a rare scenario where i had to "reuse" a promise to periodically make a http request to  a server. Something like this...

```
  const promise = myPromise;

  setInterval(() => {
      promise.then(() => {}).catch(() => {});
    }, 1000);


```

However, if you are well versed with the concept of Promises in JavaScript, you would have caught the loophole the moment I described the problem
## What's causing it
#### Before we get there, let's understand the promise lifecycle...


Let's imagine we use a Promise to make a HTTP request

Since the server in question doesn't respond immediately, the Promise is said to be in **Pending** state.

Once the server responds, the Promise moves to one of the following states:

  * **Fulfilled**- Given the server was successful in returning what we asked for in the HTTP request
  * **Rejected** - Given the server failed at giving us what we asked for

#### Coming back to what's causing the problem..

In the example above, when i try "reusing" the promise inside the setInterval (which i hate) ,it gets **fulfilled** state after the first time it resolves.

At the first look, it would look like we are storing a 'reference' to the Promise when we do something like...

```
  const promise = myPromise;
```

However, the Promise has already been **fulfilled**. When we try reusing it , it has already resolved. Hence the HTTP call is not triggered again..

```

    promise.then(() => {}).catch(() => {});


```

## How do we solve it

```
const promise = myPromise;

setInterval(() => {
    const clone = promise.then();
    clone.then(() => {}).catch(() => {});
  }, 1000);
```

**I DO NOT RECOMMEND** reusing promises as it is non standard. However, Web developers have to cater to weird feature requirements at times.
