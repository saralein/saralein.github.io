---
layout: post
title:  "RxJS: map vs. flatMap"
date:   2018-01-12 10:00:00 -0600
categories: jekyll update
---

This week I spent some time pairing with a crafter in an Angular application and working with Observables. Observables are so reminiscent of Promises that the concept was familiar enough for me to follow along. However, I wanted to truly understand what we did. In particular, I was curious about the different types of maps for Observables in RxJS. My preliminary reading on the topic consisted mostly of small blogs and the official docs. I still did not feel like I understood.

I started reading this blog: https://gist.github.com/staltz/868e7e9bc2a7b8c1f754. Shortly after, one of my mentors recommended it in case I hadn't seen it. So, I started reading it from the beginning.  Slower, much, much slower.

While I still feel like there are loose ends in my mind, I feel like I do have a better understanding and would encourage everyone to give the gist a look if you are interested in Reactive Programming, and would like to see examples with RxJS in particular.

I combined the knowledge from this blog with code snippets from the official docs to gain a better understanding of map and flatMap.

## `map`

First of all, what do you think of when you think of a `map`? In most programming contexts, a map simply calls a function on each element of an array and creates a new array of the results.

A typical `map` might be something like:

```javascript
=> [1, 2, 3, 4, 5].map(val => val + 10);
   [11, 12, 13, 14, 15]
```

So, what is a map of an Observable? Instead of an array, we are using a stream. Using map on an Observable calls a function on each value emitted from a stream and returns a new stream of the results. The docs provides this example:

```javascript
//emit (1,2,3,4,5)
const source = Rx.Observable.from([1, 2, 3, 4, 5]);
//add 10 to each value
const example = source.map(val => val + 10);
//output: 11,12,13,14,15
const subscribe = example.subscribe(val => console.log(val));
```

Map can also be used to create a stream of streams, or a metastream. Very similarly, imagine you have this code:

```javascript
const source = Rx.Observable.from([1,2,3,4,5]);
const example = source.map(val => Rx.Observable.of(val + 10));
const subscribe = example.subscribe(val => console.log(val));
```

If you were to run this, our output is now 5 Observables instead of 11, 12, 13, 14, 15. We have an Observable of Observables, or a stream of streams.

This is where flatMap comes in.

## `flatMap`

`flatMap` is RxJS's answer to smushing our Observable of Observables above into an Observable of values.

```javascript
const source = Rx.Observable.from([1,2,3,4,5]);
const example = source.flatMap(val => Rx.Observable.of(val + 10));
const subscribe = example.subscribe(val => console.log(val));
```

With a change from `map` to `flatMap` in the above code, we are back to an output of 11, 12, 13, 14, 15. The `flatMap` flattens the inner Observable.

## `mergeMap`

During time spent with RxJS, you may see `mergeMap` used. Note that `mergeMap` is simply an alias for `flatMap`.
