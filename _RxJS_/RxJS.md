# Introduction

RxJS is a library for composing asynchronous and event-based programs by using observable sequences. It provides one core type, the [Observable](https://rxjs.dev/guide/observable), satellite types (Observer, Schedulers, Subjects) and operators inspired by `Array` methods (`map`, `filter`, `reduce`, `every`, etc) to allow handling asynchronous events as collections.

*Think of RxJS as Lodash for events.*

ReactiveX combines the [Observer pattern](https://en.wikipedia.org/wiki/Observer_pattern) with the [Iterator pattern](https://en.wikipedia.org/wiki/Iterator_pattern) and [functional programming with collections](http://martinfowler.com/articles/collection-pipeline/#NestedOperatorExpressions) to fill the need for an ideal way of managing sequences of events.

The essential concepts in RxJS which solve async event management are:

- **Observable:** represents the idea of an invokable collection of future values or events.
- **Observer:** is a collection of callbacks that knows how to listen to values delivered by the Observable.
- **Subscription:** represents the execution of an Observable, is primarily useful for cancelling the execution.
- **Operators:** are pure functions that enable a functional programming style of dealing with collections with operations like `map`, `filter`, `concat`, `reduce`, etc.
- **Subject:** is equivalent to an EventEmitter, and the only way of multicasting a value or event to multiple Observers.
- **Schedulers:** are centralized dispatchers to control concurrency, allowing us to coordinate when computation happens on e.g. `setTimeout` or `requestAnimationFrame` or others.



## First examples

Normally you register event listeners.

```ts
document.addEventListener('click', () => console.log('Clicked!'));
```

Using RxJS you create an observable instead.

```ts
import { fromEvent } from 'rxjs';

fromEvent(document, 'click').subscribe(() => console.log('Clicked!'));
```

### Purity

What makes RxJS powerful is its ability to produce values using pure functions. That means your code is less prone to errors.

Normally you would create an impure function, where other pieces of your code can mess up your state.

```ts
let count = 0;
document.addEventListener('click', () => console.log(`Clicked ${++count} times`));
```

Using RxJS you isolate the state.

```ts
import { fromEvent, scan } from 'rxjs';

fromEvent(document, 'click')
  .pipe(scan((count) => count + 1, 0))
  .subscribe((count) => console.log(`Clicked ${count} times`));
```

The **scan** operator works just like **reduce** for arrays. It takes a value which is exposed to a callback. The returned value of the callback will then become the next value exposed the next time the callback runs.

### Flow

RxJS has a whole range of operators that helps you control how the events flow through your observables.

This is how you would allow at most one click per second, with plain JavaScript:

```ts
let count = 0;
let rate = 1000;
let lastClick = Date.now() - rate;
document.addEventListener('click', () => {
  if (Date.now() - lastClick >= rate) {
    console.log(`Clicked ${++count} times`);
    lastClick = Date.now();
  }
});
```

With RxJS:

```ts
import { fromEvent, throttleTime, scan } from 'rxjs';

fromEvent(document, 'click')
  .pipe(
    throttleTime(1000),
    scan((count) => count + 1, 0)
  )
  .subscribe((count) => console.log(`Clicked ${count} times`));
```

Other flow control operators are [**filter**](https://rxjs.dev/api/operators/filter), [**delay**](https://rxjs.dev/api/operators/delay), [**debounceTime**](https://rxjs.dev/api/operators/debounceTime), [**take**](https://rxjs.dev/api/operators/take), [**takeUntil**](https://rxjs.dev/api/operators/takeUntil), [**distinct**](https://rxjs.dev/api/operators/distinct), [**distinctUntilChanged**](https://rxjs.dev/api/operators/distinctUntilChanged) etc.

### Values

You can transform the values passed through your observables.

Here's how you can add the current mouse x position for every click, in plain JavaScript:

```ts
let count = 0;
const rate = 1000;
let lastClick = Date.now() - rate;
document.addEventListener('click', (event) => {
  if (Date.now() - lastClick >= rate) {
    count += event.clientX;
    console.log(count);
    lastClick = Date.now();
  }
});
```

With RxJS:

```ts
import { fromEvent, throttleTime, map, scan } from 'rxjs';

fromEvent(document, 'click')
  .pipe(
    throttleTime(1000),
    map((event) => event.clientX),
    scan((count, clientX) => count + clientX, 0)
  )
  .subscribe((count) => console.log(count));
```

Other value producing operators are [**pluck**](https://rxjs.dev/api/operators/pluck), [**pairwise**](https://rxjs.dev/api/operators/pairwise), [**sample**](https://rxjs.dev/api/operators/sample) etc.



## Observables



### Observable

Observables are lazy Push collections of multiple values. They fill the missing spot in the following table:

|          | SINGLE                                                       | MULTIPLE                                                     |
| :------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| **Pull** | [`Function`](https://developer.mozilla.org/en-US/docs/Glossary/Function) | [`Iterator`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols) |
| **Push** | [`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) | [`Observable`](https://rxjs.dev/api/index/class/Observable)  |

**Example.** The following is an Observable that pushes the values `1`, `2`, `3` immediately (synchronously) when subscribed, and the value `4` after one second has passed since the subscribe call, then completes:

```ts
import { Observable } from 'rxjs';

const observable = new Observable((subscriber) => {
  subscriber.next(1);
  subscriber.next(2);
  subscriber.next(3);
  setTimeout(() => {
    subscriber.next(4);
    subscriber.complete();
  }, 1000);
});
```

To invoke the Observable and see these values, we need to *subscribe* to it:

```ts
import { Observable } from 'rxjs';

const observable = new Observable((subscriber) => {
  subscriber.next(1);
  subscriber.next(2);
  subscriber.next(3);
  setTimeout(() => {
    subscriber.next(4);
    subscriber.complete();
  }, 1000);
});

console.log('just before subscribe');
observable.subscribe({
  next(x) {
    console.log('got value ' + x);
  },
  error(err) {
    console.error('something wrong occurred: ' + err);
  },
  complete() {
    console.log('done');
  },
});
console.log('just after subscribe');
```

Which executes as such on the console:

```none
just before subscribe
got value 1
got value 2
got value 3
just after subscribe
got value 4
done
```



### Pull versus Push

*Pull* and *Push* are two different protocols that describe how a data *Producer* can communicate with a data *Consumer*.

**What is Pull?** In Pull systems, the Consumer determines when it receives data from the data Producer. The Producer itself is unaware of when the data will be delivered to the Consumer.

Every JavaScript Function is a Pull system. The function is a Producer of data, and the code that calls the function is consuming it by "pulling" out a *single* return value from its call.

ES2015 introduced [generator functions and iterators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*) (`function*`), another type of Pull system. Code that calls `iterator.next()` is the Consumer, "pulling" out *multiple* values from the iterator (the Producer).

|          | PRODUCER                                   | CONSUMER                                    |
| :------- | :----------------------------------------- | :------------------------------------------ |
| **Pull** | **Passive:** produces data when requested. | **Active:** decides when data is requested. |
| **Push** | **Active:** produces data at its own pace. | **Passive:** reacts to received data.       |

**What is Push?** In Push systems, the Producer determines when to send data to the Consumer. The Consumer is unaware of when it will receive that data.

Promises are the most common type of Push system in JavaScript today. A Promise (the Producer) delivers a resolved value to registered callbacks (the Consumers), but unlike functions, it is the Promise which is in charge of determining precisely when that value is "pushed" to the callbacks.

RxJS introduces Observables, a new Push system for JavaScript. An Observable is a Producer of multiple values, "pushing" them to Observers (Consumers).

- A **Function** is a lazily evaluated computation that synchronously returns a single value on invocation.
- A **generator** is a lazily evaluated computation that synchronously returns zero to (potentially) infinite values on iteration.
- A **Promise** is a computation that may (or may not) eventually return a single value.
- An **Observable** is a lazily evaluated computation that can synchronously or asynchronously return zero to (potentially) infinite values from the time it's invoked onwards.

*For more info about what to use when converting Observables to Promises, please refer to [this guide](https://rxjs.dev/deprecations/to-promise).*



### Observables as generalizations of functions

Contrary to popular claims, Observables are not like EventEmitters nor are they like Promises for multiple values. Observables *may act* like EventEmitters in some cases, namely when they are multicasted using RxJS Subjects, but usually they don't act like EventEmitters.

*Observables are like functions with zero arguments, but generalize those to allow multiple values.*



Consider the following:

```ts
function foo() {
  console.log('Hello');
  return 42;
}

const x = foo.call(); // same as foo()
console.log(x);
const y = foo.call(); // same as foo()
console.log(y);
```

We expect to see as output:

```none
content_copyopen_in_new"Hello"
42
"Hello"
42
```

You can write the same behavior above, but with Observables:

```ts
import { Observable } from 'rxjs';

const foo = new Observable((subscriber) => {
  console.log('Hello');
  subscriber.next(42);
});

foo.subscribe((x) => {
  console.log(x);
});
foo.subscribe((y) => {
  console.log(y);
});
```

And the output is the same:

```none
"Hello"
42
"Hello"
42
```

This happens because both functions and Observables are lazy computations. If you don't call the function, the `console.log('Hello')` won't happen. Also with Observables, if you don't "call" it (with `subscribe`), the `console.log('Hello')` won't happen. Plus, "calling" or "subscribing" is an isolated operation: two function calls trigger two separate side effects, and two Observable subscribes trigger two separate side effects. As opposed to EventEmitters which share the side effects and have eager execution regardless of the existence of subscribers, Observables have no shared execution and are lazy.



*Subscribing to an Observable is analogous to calling a Function.*

Some people claim that Observables are asynchronous. That is not true. If you surround a function call with logs, like this:

```ts
console.log('before');
console.log(foo.call());
console.log('after');
```

You will see the output:

```none
"before"
"Hello"
42
"after"
```

And this is the same behavior with Observables:

```ts
console.log('before');
foo.subscribe((x) => {
  console.log(x);
});
console.log('after');
```

And the output is:

```none
"before"
"Hello"
42
"after"
```

Which proves the subscription of `foo` was entirely synchronous, just like a function.



*Observables are able to deliver values either synchronously or asynchronously.*

What is the difference between an Observable and a function? **Observables can "return" multiple values over time**, something which functions cannot. You can't do this:

```ts
function foo() {
  console.log('Hello');
  return 42;
  return 100; // dead code. will never happen
}
```

Functions can only return one value. Observables, however, can do this:

```ts
import { Observable } from 'rxjs';

const foo = new Observable((subscriber) => {
  console.log('Hello');
  subscriber.next(42);
  subscriber.next(100); // "return" another value
  subscriber.next(200); // "return" yet another
});

console.log('before');
foo.subscribe((x) => {
  console.log(x);
});
console.log('after');
```

With synchronous output:

```none
"before"
"Hello"
42
100
200
"after"
```

But you can also "return" values asynchronously:

```ts
import { Observable } from 'rxjs';

const foo = new Observable((subscriber) => {
  console.log('Hello');
  subscriber.next(42);
  subscriber.next(100);
  subscriber.next(200);
  setTimeout(() => {
    subscriber.next(300); // happens asynchronously
  }, 1000);
});

console.log('before');
foo.subscribe((x) => {
  console.log(x);
});
console.log('after');
```

With output:

```none
"before"
"Hello"
42
100
200
"after"
300
```

Conclusion:

- `func.call()` means "*give me one value synchronously*"
- `observable.subscribe()` means "*give me any amount of values, either synchronously or asynchronously*"



### Anatomy of an Observable

​	Observables are **created** using `new Observable` or a creation operator, are **subscribed** to with an Observer, **execute** to deliver `next` / `error` / `complete` notifications to the Observer, and their execution may be **disposed**. These four aspects are all encoded in an Observable instance, but some of these aspects are related to other types, like Observer and Subscription.

Core Observable concerns:

- **Creating** Observables
- **Subscribing** to Observables
- **Executing** the Observable
- **Disposing** Observables



#### 1.Creating Observables

The `Observable` constructor takes one argument: **the `subscribe` function**.

The following example creates an Observable to emit the string `'hi'` every second to a subscriber.

```ts
import { Observable } from 'rxjs';

const observable = new Observable(function subscribe(subscriber) {
  const id = setInterval(() => {
    subscriber.next('hi');
  }, 1000);
});
```

*Observables can be created with `new Observable`. Most commonly, observables are created using creation functions, like `of`, `from`, `interval`, etc.*



In the example above, the `subscribe` function is the most important piece to describe the Observable. Let's look at what subscribing means.



#### 2.Subscribing to Observables

The Observable `observable` in the example can be *subscribed* to, like this:

```ts
observable.subscribe((x) => console.log(x));
```

It is not a coincidence that `observable.subscribe` and `subscribe` in `new Observable(function subscribe(subscriber) {...})` have the same name. In the library, they are different, but for practical purposes you can consider them conceptually equal.

This shows how `subscribe` calls are not shared among multiple Observers of the same Observable. When calling `observable.subscribe` with an Observer, the function `subscribe` in `new Observable(function subscribe(subscriber) {...})` is run for that given subscriber. Each call to `observable.subscribe` triggers its own independent setup for that given subscriber.



*Subscribing to an Observable is like calling a function, providing callbacks where the data will be delivered to.*



This is drastically different to event handler APIs like `addEventListener` / `removeEventListener`. With `observable.subscribe`, the given Observer is not registered as a listener in the Observable. The Observable does not even maintain a list of attached Observers.

A `subscribe` call is simply a way to start an "Observable execution" and deliver values or events to an Observer of that execution.



#### 3.Executing Observables

The code inside `new Observable(function subscribe(subscriber) {...})` represents an "Observable execution", a lazy computation that only happens for each Observer that subscribes. The execution produces multiple values over time, either synchronously or asynchronously.

There are three types of values an Observable Execution can deliver:

- "Next" notification: sends a value such as a Number, a String, an Object, etc.
- "Error" notification: sends a JavaScript Error or exception.
- "Complete" notification: does not send a value.

"Next" notifications are the most important and most common type: they represent actual data being delivered to a subscriber. "Error" and "Complete" notifications may happen only once during the Observable Execution, and there can only be either one of them.



These constraints are expressed best in the so-called *Observable Grammar* or *Contract*, written as a regular expression:

```none
next*(error|complete)?
```



*In an Observable Execution, zero to infinite Next notifications may be delivered. If either an Error or Complete notification is delivered, then nothing else can be delivered afterwards.*



The following is an example of an Observable execution that delivers three Next notifications, then completes:

```ts
import { Observable } from 'rxjs';

const observable = new Observable(function subscribe(subscriber) {
  subscriber.next(1);
  subscriber.next(2);
  subscriber.next(3);
  subscriber.complete();
});
```

Observables strictly adhere to the Observable Contract, so the following code would not deliver the Next notification `4`:

```ts
import { Observable } from 'rxjs';

const observable = new Observable(function subscribe(subscriber) {
  subscriber.next(1);
  subscriber.next(2);
  subscriber.next(3);
  subscriber.complete();
  subscriber.next(4); // Is not delivered because it would violate the contract
});
```

It is a good idea to wrap any code in `subscribe` with `try`/`catch` block that will deliver an Error notification if it catches an exception:

```ts
import { Observable } from 'rxjs';

const observable = new Observable(function subscribe(subscriber) {
  try {
    subscriber.next(1);
    subscriber.next(2);
    subscriber.next(3);
    subscriber.complete();
  } catch (err) {
    subscriber.error(err); // delivers an error if it caught one
  }
});
```





#### 4.Disposing Observable Executions

Because Observable Executions may be infinite, and it's common for an Observer to want to abort execution in finite time, we need an API for canceling an execution. Since each execution is exclusive to one Observer only, once the Observer is done receiving values, it has to have a way to stop the execution, in order to avoid wasting computation power or memory resources.

When `observable.subscribe` is called, the Observer gets attached to the newly created Observable execution. This call also returns an object, the `Subscription`:

```ts
const subscription = observable.subscribe((x) => console.log(x));
```

The Subscription represents the ongoing execution, and has a minimal API which allows you to cancel that execution. Read more about the [`Subscription` type here](https://rxjs.dev/guide/subscription). With `subscription.unsubscribe()` you can cancel the ongoing execution:

```ts
import { from } from 'rxjs';

const observable = from([10, 20, 30]);
const subscription = observable.subscribe((x) => console.log(x));
// Later:
subscription.unsubscribe();
```



*When you subscribe, you get back a Subscription, which represents the ongoing execution. Just call `unsubscribe()` to cancel the execution.*



Each Observable must define how to dispose resources of that execution when we create the Observable using `create()`. You can do that by returning a custom `unsubscribe` function from within `function subscribe()`.

For instance, this is how we clear an interval execution set with `setInterval`:

```ts
import { Observable } from 'rxjs';

const observable = new Observable(function subscribe(subscriber) {
  // Keep track of the interval resource
  const intervalId = setInterval(() => {
    subscriber.next('hi');
  }, 1000);

  // Provide a way of canceling and disposing the interval resource
  return function unsubscribe() {
    clearInterval(intervalId);
  };
});
```

Just like `observable.subscribe` resembles `new Observable(function subscribe() {...})`, the `unsubscribe` we return from `subscribe` is conceptually equal to `subscription.unsubscribe`. In fact, if we remove the ReactiveX types surrounding these concepts, we're left with rather straightforward JavaScript.

```ts
function subscribe(subscriber) {
  const intervalId = setInterval(() => {
    subscriber.next('hi');
  }, 1000);

  return function unsubscribe() {
    clearInterval(intervalId);
  };
}

const unsubscribe = subscribe({ next: (x) => console.log(x) });

// Later:
unsubscribe(); // dispose the resources
```

The reason why we use Rx types like Observable, Observer, and Subscription is to get safety (such as the Observable Contract) and composability with Operators.





## Observer

**What is an Observer?** An Observer is a consumer of values delivered by an Observable. Observers are simply a set of callbacks, one for each type of notification delivered by the Observable: `next`, `error`, and `complete`. The following is an example of a typical Observer object:

```ts
const observer = {
  next: x => console.log('Observer got a next value: ' + x),
  error: err => console.error('Observer got an error: ' + err),
  complete: () => console.log('Observer got a complete notification'),
};
```

To use the Observer, provide it to the `subscribe` of an Observable:

```ts
observable.subscribe(observer);
```

*Observers are just objects with three callbacks, one for each type of notification that an Observable may deliver.*

Observers in RxJS may also be *partial*. If you don't provide one of the callbacks, the execution of the Observable will still happen normally, except some types of notifications will be ignored, because they don't have a corresponding callback in the Observer.

The example below is an `Observer` without the `complete` callback:

```ts
const observer = {
  next: x => console.log('Observer got a next value: ' + x),
  error: err => console.error('Observer got an error: ' + err),
};
```

When subscribing to an `Observable`, you may also just provide the next callback as an argument, without being attached to an `Observer` object, for instance like this:

```ts
observable.subscribe(x => console.log('Observer got a next value: ' + x));
```

Internally in `observable.subscribe`, it will create an `Observer` object using the callback argument as the `next` handler.



## Operators

RxJS is mostly useful for its *operators*, even though the Observable is the foundation. Operators are the essential pieces that allow complex asynchronous code to be easily composed in a declarative manner.

### What are operators?

Operators are **functions**. There are two kinds of operators:

#### 1.**Pipeable Operators** 

are the kind that can be piped to Observables using the syntax :

`observableInstance.pipe(operator)` or, more commonly, `observableInstance.pipe(operatorFactory())`. 

Operator factory functions include, [`filter(...)`](https://rxjs.dev/api/operators/filter), and [`mergeMap(...)`](https://rxjs.dev/api/operators/mergeMap).

When Pipeable Operators are called, they do not *change* the existing Observable instance. Instead, they return a ***new*** Observable, whose subscription logic is based on the first Observable.



***A Pipeable Operator is a function that takes an Observable as its input and returns another Observable. It is a pure operation: the previous Observable stays unmodified.***

***A Pipeable Operator Factory is a function that can take parameters to set the context and return a Pipeable Operator. The factory’s arguments belong to the operator’s lexical scope.***



A Pipeable Operator is essentially a pure function which takes one Observable as input and generates another Observable as output. Subscribing to the output Observable will also subscribe to the input Observable.



#### 2.**Creation Operators**

 are the other kind of operator, which can be called as standalone functions to create a new Observable. For example: `of(1, 2, 3)` creates an observable that will emit 1, 2, and 3, one right after another. Creation operators will be discussed in more detail in a later section.





For example, the operator called [`map`](https://rxjs.dev/api/operators/map) is analogous to the Array method of the same name. Just as `[1, 2, 3].map(x => x * x)` will yield `[1, 4, 9]`, the Observable created like this:

```ts
import { of, map } from 'rxjs';

of(1, 2, 3)
  .pipe(map((x) => x * x))
  .subscribe((v) => console.log(`value: ${v}`));

// Logs:
// value: 1
// value: 4
// value: 9
```

will emit `1`, `4`, `9`.



Another useful operator is [`first`](https://rxjs.dev/api/operators/first):

```ts
import { of, first } from 'rxjs';

of(1, 2, 3)
  .pipe(first())
  .subscribe((v) => console.log(`value: ${v}`));

// Logs:
// value: 1
```



Note that `map` logically must be constructed on the fly, since it must be given the mapping function to. By contrast, `first` could be a constant, but is nonetheless constructed on the fly. As a general practice, all operators are constructed, whether they need arguments or not.



### Piping

Pipeable operators are functions, so they *could* be used like ordinary functions: `op()(obs)` — but in practice, there tend to be many of them convolved together, and quickly become unreadable: `op4()(op3()(op2()(op1()(obs))))`. For that reason, Observables have a method called `.pipe()` that accomplishes the same thing while being much easier to read:

```ts
obs.pipe(op1(), op2(), op3(), op4());
```

As a stylistic matter, `op()(obs)` is never used, even if there is only one operator; `obs.pipe(op())` is universally preferred.



### Creation Operators

**What are creation operators?** Distinct from pipeable operators, creation operators are functions that can be used to create an Observable with some common predefined behavior or by joining other Observables.

A typical example of a creation operator would be the `interval` function. It takes a number (not an Observable) as input argument, and produces an Observable as output:\

```ts
import { interval } from 'rxjs';

const observable = interval(1000 /* number of milliseconds */);
```



### Higher-order Observables

Observables most commonly emit ordinary values like strings and numbers, but surprisingly often, it is necessary to handle Observables *of* Observables, so-called higher-order Observables. For example, imagine you had an Observable emitting strings that were the URLs of files you wanted to see. The code might look like this:

```ts
const fileObservable = urlObservable.pipe(map((url) => http.get(url)));
```

`http.get()` returns an Observable (of string or string arrays probably) for each individual URL. Now you have an Observable *of* Observables, a higher-order Observable.

But how do you work with a higher-order Observable? Typically, by *flattening*: by (somehow) converting a higher-order Observable into an ordinary Observable. For example:

```ts
const fileObservable = urlObservable.pipe(
  map((url) => http.get(url)),
  concatAll()
);
```

The [`concatAll()`](https://rxjs.dev/api/operators/concatAll) operator subscribes to each "inner" Observable that comes out of the "outer" Observable, and copies all the emitted values until that Observable completes, and goes on to the next one. All of the values are in that way concatenated. Other useful flattening operators (called [*join operators*](https://rxjs.dev/guide/operators#join-operators)) are

- [`mergeAll()`](https://rxjs.dev/api/operators/mergeAll) — subscribes to each inner Observable as it arrives, then emits each value as it arrives
- [`switchAll()`](https://rxjs.dev/api/operators/switchAll) — subscribes to the first inner Observable when it arrives, and emits each value as it arrives, but when the next inner Observable arrives, unsubscribes to the previous one, and subscribes to the new one.
- [`exhaustAll()`](https://rxjs.dev/api/operators/exhaustAll) — subscribes to the first inner Observable when it arrives, and emits each value as it arrives, discarding all newly arriving inner Observables until that first one completes, then waits for the next inner Observable.

Just as many array libraries combine [`map()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) and [`flat()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/flat) (or `flatten()`) into a single [`flatMap()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/flatMap), there are mapping equivalents of all the RxJS flattening operators [`concatMap()`](https://rxjs.dev/api/operators/concatMap), [`mergeMap()`](https://rxjs.dev/api/operators/mergeMap), [`switchMap()`](https://rxjs.dev/api/operators/switchMap), and [`exhaustMap()`](https://rxjs.dev/api/operators/exhaustMap).



### Marble diagrams

To explain how operators work, textual descriptions are often not enough. Many operators are related to time, they may for instance delay, sample, throttle, or debounce value emissions in different ways. Diagrams are often a better tool for that. *Marble Diagrams* are visual representations of how operators work, and include the input Observable(s), the operator and its parameters, and the output Observable.



### Categories of operators

There are operators for different purposes, and they may be categorized as: creation, transformation, filtering, joining, multicasting, error handling, utility, etc. In the following list you will find all the operators organized in categories.

For a complete overview, see the [references page](https://rxjs.dev/api).



#### 1.Creation Operators

- [`ajax`](https://rxjs.dev/api/ajax/ajax)
- [`bindCallback`](https://rxjs.dev/api/index/function/bindCallback)
- [`bindNodeCallback`](https://rxjs.dev/api/index/function/bindNodeCallback)
- [`defer`](https://rxjs.dev/api/index/function/defer)
- [`empty`](https://rxjs.dev/api/index/function/empty)
- [`from`](https://rxjs.dev/api/index/function/from)
- [`fromEvent`](https://rxjs.dev/api/index/function/fromEvent)
- [`fromEventPattern`](https://rxjs.dev/api/index/function/fromEventPattern)
- [`generate`](https://rxjs.dev/api/index/function/generate)
- [`interval`](https://rxjs.dev/api/index/function/interval)
- [`of`](https://rxjs.dev/api/index/function/of)
- [`range`](https://rxjs.dev/api/index/function/range)
- [`throwError`](https://rxjs.dev/api/index/function/throwError)
- [`timer`](https://rxjs.dev/api/index/function/timer)
- [`iif`](https://rxjs.dev/api/index/function/iif)



#### 2.Join Creation Operators

These are Observable creation operators that also have join functionality -- emitting values of multiple source Observables.

- [`combineLatest`](https://rxjs.dev/api/index/function/combineLatest)
- [`concat`](https://rxjs.dev/api/index/function/concat)
- [`forkJoin`](https://rxjs.dev/api/index/function/forkJoin)
- [`merge`](https://rxjs.dev/api/index/function/merge)
- [`partition`](https://rxjs.dev/api/index/function/partition)
- [`race`](https://rxjs.dev/api/index/function/race)
- [`zip`](https://rxjs.dev/api/index/function/zip)

#### 3.Transformation Operators

- [`buffer`](https://rxjs.dev/api/operators/buffer)
- [`bufferCount`](https://rxjs.dev/api/operators/bufferCount)
- [`bufferTime`](https://rxjs.dev/api/operators/bufferTime)
- [`bufferToggle`](https://rxjs.dev/api/operators/bufferToggle)
- [`bufferWhen`](https://rxjs.dev/api/operators/bufferWhen)
- [`concatMap`](https://rxjs.dev/api/operators/concatMap)
- [`concatMapTo`](https://rxjs.dev/api/operators/concatMapTo)
- [`exhaust`](https://rxjs.dev/api/operators/exhaust)
- [`exhaustMap`](https://rxjs.dev/api/operators/exhaustMap)
- [`expand`](https://rxjs.dev/api/operators/expand)
- [`groupBy`](https://rxjs.dev/api/operators/groupBy)
- [`map`](https://rxjs.dev/api/operators/map)
- [`mapTo`](https://rxjs.dev/api/operators/mapTo)
- [`mergeMap`](https://rxjs.dev/api/operators/mergeMap)
- [`mergeMapTo`](https://rxjs.dev/api/operators/mergeMapTo)
- [`mergeScan`](https://rxjs.dev/api/operators/mergeScan)
- [`pairwise`](https://rxjs.dev/api/operators/pairwise)
- [`partition`](https://rxjs.dev/api/operators/partition)
- [`pluck`](https://rxjs.dev/api/operators/pluck)
- [`scan`](https://rxjs.dev/api/operators/scan)
- [`switchScan`](https://rxjs.dev/api/operators/switchScan)
- [`switchMap`](https://rxjs.dev/api/operators/switchMap)
- [`switchMapTo`](https://rxjs.dev/api/operators/switchMapTo)
- [`window`](https://rxjs.dev/api/operators/window)
- [`windowCount`](https://rxjs.dev/api/operators/windowCount)
- [`windowTime`](https://rxjs.dev/api/operators/windowTime)
- [`windowToggle`](https://rxjs.dev/api/operators/windowToggle)
- [`windowWhen`](https://rxjs.dev/api/operators/windowWhen)

#### 4.Filtering Operators

- [`audit`](https://rxjs.dev/api/operators/audit)
- [`auditTime`](https://rxjs.dev/api/operators/auditTime)
- [`debounce`](https://rxjs.dev/api/operators/debounce)
- [`debounceTime`](https://rxjs.dev/api/operators/debounceTime)
- [`distinct`](https://rxjs.dev/api/operators/distinct)
- [`distinctUntilChanged`](https://rxjs.dev/api/operators/distinctUntilChanged)
- [`distinctUntilKeyChanged`](https://rxjs.dev/api/operators/distinctUntilKeyChanged)
- [`elementAt`](https://rxjs.dev/api/operators/elementAt)
- [`filter`](https://rxjs.dev/api/operators/filter)
- [`first`](https://rxjs.dev/api/operators/first)
- [`ignoreElements`](https://rxjs.dev/api/operators/ignoreElements)
- [`last`](https://rxjs.dev/api/operators/last)
- [`sample`](https://rxjs.dev/api/operators/sample)
- [`sampleTime`](https://rxjs.dev/api/operators/sampleTime)
- [`single`](https://rxjs.dev/api/operators/single)
- [`skip`](https://rxjs.dev/api/operators/skip)
- [`skipLast`](https://rxjs.dev/api/operators/skipLast)
- [`skipUntil`](https://rxjs.dev/api/operators/skipUntil)
- [`skipWhile`](https://rxjs.dev/api/operators/skipWhile)
- [`take`](https://rxjs.dev/api/operators/take)
- [`takeLast`](https://rxjs.dev/api/operators/takeLast)
- [`takeUntil`](https://rxjs.dev/api/operators/takeUntil)
- [`takeWhile`](https://rxjs.dev/api/operators/takeWhile)
- [`throttle`](https://rxjs.dev/api/operators/throttle)
- [`throttleTime`](https://rxjs.dev/api/operators/throttleTime)

#### 5.Join Operators

Also see the [Join Creation Operators](https://rxjs.dev/guide/operators#join-creation-operators) section above.

- [`combineLatestAll`](https://rxjs.dev/api/operators/combineLatestAll)
- [`concatAll`](https://rxjs.dev/api/operators/concatAll)
- [`exhaustAll`](https://rxjs.dev/api/operators/exhaustAll)
- [`mergeAll`](https://rxjs.dev/api/operators/mergeAll)
- [`switchAll`](https://rxjs.dev/api/operators/switchAll)
- [`startWith`](https://rxjs.dev/api/operators/startWith)
- [`withLatestFrom`](https://rxjs.dev/api/operators/withLatestFrom)

#### 6.Multicasting Operators

- [`multicast`](https://rxjs.dev/api/operators/multicast)
- [`publish`](https://rxjs.dev/api/operators/publish)
- [`publishBehavior`](https://rxjs.dev/api/operators/publishBehavior)
- [`publishLast`](https://rxjs.dev/api/operators/publishLast)
- [`publishReplay`](https://rxjs.dev/api/operators/publishReplay)
- [`share`](https://rxjs.dev/api/operators/share)

#### 7.Error Handling Operators

- [`catchError`](https://rxjs.dev/api/operators/catchError)
- [`retry`](https://rxjs.dev/api/operators/retry)
- [`retryWhen`](https://rxjs.dev/api/operators/retryWhen)

#### 8.Utility Operators

- [`tap`](https://rxjs.dev/api/operators/tap)
- [`delay`](https://rxjs.dev/api/operators/delay)
- [`delayWhen`](https://rxjs.dev/api/operators/delayWhen)
- [`dematerialize`](https://rxjs.dev/api/operators/dematerialize)
- [`materialize`](https://rxjs.dev/api/operators/materialize)
- [`observeOn`](https://rxjs.dev/api/operators/observeOn)
- [`subscribeOn`](https://rxjs.dev/api/operators/subscribeOn)
- [`timeInterval`](https://rxjs.dev/api/operators/timeInterval)
- [`timestamp`](https://rxjs.dev/api/operators/timestamp)
- [`timeout`](https://rxjs.dev/api/operators/timeout)
- [`timeoutWith`](https://rxjs.dev/api/operators/timeoutWith)
- [`toArray`](https://rxjs.dev/api/operators/toArray)

#### 9.Conditional and Boolean Operators

- [`defaultIfEmpty`](https://rxjs.dev/api/operators/defaultIfEmpty)
- [`every`](https://rxjs.dev/api/operators/every)
- [`find`](https://rxjs.dev/api/operators/find)
- [`findIndex`](https://rxjs.dev/api/operators/findIndex)
- [`isEmpty`](https://rxjs.dev/api/operators/isEmpty)

#### 10.Mathematical and Aggregate Operators

- [`count`](https://rxjs.dev/api/operators/count)
- [`max`](https://rxjs.dev/api/operators/max)
- [`min`](https://rxjs.dev/api/operators/min)
- [`reduce`](https://rxjs.dev/api/operators/reduce)





### Creating custom operators

#### 1.Use the `pipe()` function to make new operators

If there is a commonly used sequence of operators in your code, use the `pipe()` function to extract the sequence into a new operator. Even if a sequence is not that common, breaking it out into a single operator can improve readability.

For example, you could make a function that discarded odd values and doubled even values like this:

```ts
import { pipe, filter, map } from 'rxjs';

function discardOddDoubleEven() {
  return pipe(
    filter((v) => !(v % 2)),
    map((v) => v + v)
  );
}
```

(The `pipe()` function is analogous to, but not the same thing as, the `.pipe()` method on an Observable.)



#### 2.Creating new operators from scratch

It is more complicated, but if you have to write an operator that cannot be made from a combination of existing operators (a rare occurrence), you can write an operator from scratch using the Observable constructor, like this:

```ts
import { Observable, of } from 'rxjs';

function delay<T>(delayInMillis: number) {
  return (observable: Observable<T>) =>
    new Observable<T>((subscriber) => {
      // this function will be called each time this
      // Observable is subscribed to.
      const allTimerIDs = new Set();
      let hasCompleted = false;
      const subscription = observable.subscribe({
        next(value) {
          // Start a timer to delay the next value
          // from being pushed.
          const timerID = setTimeout(() => {
            subscriber.next(value);
            // after we push the value, we need to clean up the timer timerID
            allTimerIDs.delete(timerID);
            // If the source has completed, and there are no more timers running,
            // we can complete the resulting observable.
            if (hasCompleted && allTimerIDs.size === 0) {
              subscriber.complete();
            }
          }, delayInMillis);

          allTimerIDs.add(timerID);
        },
        error(err) {
          // We need to make sure we're propagating our errors through.
          subscriber.error(err);
        },
        complete() {
          hasCompleted = true;
          // If we still have timers running, we don't want to complete yet.
          if (allTimerIDs.size === 0) {
            subscriber.complete();
          }
        },
      });

      // Return the finalization logic. This will be invoked when
      // the result errors, completes, or is unsubscribed.
      return () => {
        subscription.unsubscribe();
        // Clean up our timers.
        for (const timerID of allTimerIDs) {
          clearTimeout(timerID);
        }
      };
    });
}

// Try it out!
of(1, 2, 3).pipe(delay(1000)).subscribe(console.log);
```

Note that you must

1. implement all three Observer functions, `next()`, `error()`, and `complete()` when subscribing to the input Observable.
2. implement a "finalization" function that cleans up when the Observable completes (in this case by unsubscribing and clearing any pending timeouts).
3. return that finalization function from the function passed to the Observable constructor.

Of course, this is only an example; the [`delay()`](https://rxjs.dev/api/operators/delay) operator already exists.





## Subscription

**What is a Subscription?** A Subscription is an object that represents a disposable resource, usually the execution of an Observable. A Subscription has one important method, `unsubscribe`, that takes no argument and just disposes the resource held by the subscription. In previous versions of RxJS, Subscription was called "Disposable".

```ts
import { interval } from 'rxjs';

const observable = interval(1000);
const subscription = observable.subscribe(x => console.log(x));
// Later:
// This cancels the ongoing Observable execution which
// was started by calling subscribe with an Observer.
subscription.unsubscribe();
```

***A Subscription essentially just has an* `unsubscribe()` *function to release resources or cancel Observable executions.***



Subscriptions can also be put together, so that a call to an `unsubscribe()` of one Subscription may unsubscribe multiple Subscriptions. You can do this by "adding" one subscription into another:

```ts
import { interval } from 'rxjs';

const observable1 = interval(400);
const observable2 = interval(300);

const subscription = observable1.subscribe(x => console.log('first: ' + x));
const childSubscription = observable2.subscribe(x => console.log('second: ' + x));

subscription.add(childSubscription);

setTimeout(() => {
  // Unsubscribes BOTH subscription and childSubscription
  subscription.unsubscribe();
}, 1000);
```

When executed, we see in the console:

```none
second: 0
first: 0
second: 1
first: 1
second: 2
```

Subscriptions also have a `remove(otherSubscription)` method, in order to undo the addition of a child Subscription.



## Subject



### What is a Subject? 

An RxJS Subject is a special type of Observable that allows values to be multicasted to many Observers. While plain Observables are unicast (each subscribed Observer owns an independent execution of the Observable), Subjects are multicast.



***A Subject is like an Observable, but can multicast to many Observers. Subjects are like EventEmitters: they maintain a registry of many listeners.***



**Every Subject is an Observable.** Given a Subject, you can `subscribe` to it, providing an Observer, which will start receiving values normally. From the perspective of the Observer, it cannot tell whether the Observable execution is coming from a plain unicast Observable or a Subject.

Internally to the Subject, `subscribe` does not invoke a new execution that delivers values. It simply registers the given Observer in a list of Observers, similarly to how `addListener` usually works in other libraries and languages.

**Every Subject is an Observer.** It is an object with the methods `next(v)`, `error(e)`, and `complete()`. To feed a new value to the Subject, just call `next(theValue)`, and it will be multicasted to the Observers registered to listen to the Subject.



In the example below, we have two Observers attached to a Subject, and we feed some values to the Subject:

```ts
import { Subject } from 'rxjs';

const subject = new Subject<number>();

subject.subscribe({
  next: (v) => console.log(`observerA: ${v}`),
});
subject.subscribe({
  next: (v) => console.log(`observerB: ${v}`),
});

subject.next(1);
subject.next(2);

// Logs:
// observerA: 1
// observerB: 1
// observerA: 2
// observerB: 2
```

Since a Subject is an Observer, this also means you may provide a Subject as the argument to the `subscribe` of any Observable, like the example below shows:

```ts
import { Subject, from } from 'rxjs';

const subject = new Subject<number>();

subject.subscribe({
  next: (v) => console.log(`observerA: ${v}`),
});
subject.subscribe({
  next: (v) => console.log(`observerB: ${v}`),
});

const observable = from([1, 2, 3]);

observable.subscribe(subject); // You can subscribe providing a Subject

// Logs:
// observerA: 1
// observerB: 1
// observerA: 2
// observerB: 2
// observerA: 3
// observerB: 3
```

With the approach above, we essentially just converted a unicast Observable execution to multicast, through the Subject. This demonstrates how Subjects are the only way of making any Observable execution be shared to multiple Observers.

There are also a few specializations of the `Subject` type: `BehaviorSubject`, `ReplaySubject`, and `AsyncSubject`.



### Multicasted Observables

A "multicasted Observable" passes notifications through a Subject which may have many subscribers, whereas a plain "unicast Observable" only sends notifications to a single Observer.



***A multicasted Observable uses a Subject under the hood to make multiple Observers see the same Observable execution.***



Under the hood, this is how the `multicast` operator works: Observers subscribe to an underlying Subject, and the Subject subscribes to the source Observable. The following example is similar to the previous example which used `observable.subscribe(subject)`:

```ts
import { from, Subject, multicast } from 'rxjs';

const source = from([1, 2, 3]);
const subject = new Subject();
const multicasted = source.pipe(multicast(subject));

// These are, under the hood, `subject.subscribe({...})`:
multicasted.subscribe({
  next: (v) => console.log(`observerA: ${v}`),
});
multicasted.subscribe({
  next: (v) => console.log(`observerB: ${v}`),
});

// This is, under the hood, `source.subscribe(subject)`:
(multicasted as ConnectableObservable<number>).connect();
```



#### Reference counting

Calling `connect()` manually and handling the Subscription is often cumbersome. Usually, we want to *automatically* connect when the first Observer arrives, and automatically cancel the shared execution when the last Observer unsubscribes.

Consider the following example where subscriptions occur as outlined by this list:

1. First Observer subscribes to the multicasted Observable
2. **The multicasted Observable is connected**
3. The `next` value `0` is delivered to the first Observer
4. Second Observer subscribes to the multicasted Observable
5. The `next` value `1` is delivered to the first Observer
6. The `next` value `1` is delivered to the second Observer
7. First Observer unsubscribes from the multicasted Observable
8. The `next` value `2` is delivered to the second Observer
9. Second Observer unsubscribes from the multicasted Observable
10. **The connection to the multicasted Observable is unsubscribed**

To achieve that with explicit calls to `connect()`, we write the following code:

```ts
import { interval, Subject, multicast } from 'rxjs';

const source = interval(500);
const subject = new Subject();
const multicasted = source.pipe(multicast(subject));
let subscription1, subscription2, subscriptionConnect;

subscription1 = multicasted.subscribe({
  next: (v) => console.log(`observerA: ${v}`),
});
// We should call `connect()` here, because the first
// subscriber to `multicasted` is interested in consuming values
subscriptionConnect = multicasted.connect();

setTimeout(() => {
  subscription2 = multicasted.subscribe({
    next: (v) => console.log(`observerB: ${v}`),
  });
}, 600);

setTimeout(() => {
  subscription1.unsubscribe();
}, 1200);

// We should unsubscribe the shared Observable execution here,
// because `multicasted` would have no more subscribers after this
setTimeout(() => {
  subscription2.unsubscribe();
  subscriptionConnect.unsubscribe(); // for the shared Observable execution
}, 2000);
```



If we wish to avoid explicit calls to `connect()`, we can use ConnectableObservable's `refCount()` method (reference counting), which returns an Observable that keeps track of how many subscribers it has. When the number of subscribers increases from `0` to `1`, it will call `connect()` for us, which starts the shared execution. Only when the number of subscribers decreases from `1` to `0` will it be fully unsubscribed, stopping further execution.



**`refCount` *makes the multicasted Observable automatically start executing when the first subscriber arrives, and stop executing when the last subscriber leaves.***



Below is an example:

```ts
import { interval, Subject, multicast, refCount } from 'rxjs';

const source = interval(500);
const subject = new Subject();
const refCounted = source.pipe(multicast(subject), refCount());
let subscription1, subscription2;

// This calls `connect()`, because
// it is the first subscriber to `refCounted`
console.log('observerA subscribed');
subscription1 = refCounted.subscribe({
  next: (v) => console.log(`observerA: ${v}`),
});

setTimeout(() => {
  console.log('observerB subscribed');
  subscription2 = refCounted.subscribe({
    next: (v) => console.log(`observerB: ${v}`),
  });
}, 600);

setTimeout(() => {
  console.log('observerA unsubscribed');
  subscription1.unsubscribe();
}, 1200);

// This is when the shared Observable execution will stop, because
// `refCounted` would have no more subscribers after this
setTimeout(() => {
  console.log('observerB unsubscribed');
  subscription2.unsubscribe();
}, 2000);

// Logs
// observerA subscribed
// observerA: 0
// observerB subscribed
// observerA: 1
// observerB: 1
// observerA unsubscribed
// observerB: 2
// observerB unsubscribed
```

The `refCount()` method only exists on ConnectableObservable, and it returns an `Observable`, not another ConnectableObservable.



### BehaviorSubject

One of the variants of Subjects is the `BehaviorSubject`, which has a notion of "the current value". It stores the latest value emitted to its consumers, and whenever a new Observer subscribes, it will immediately receive the "current value" from the `BehaviorSubject`.



***BehaviorSubjects are useful for representing "values over time". For instance, an event stream of birthdays is a Subject, but the stream of a person's age would be a BehaviorSubject.***



In the following example, the BehaviorSubject is initialized with the value `0` which the first Observer receives when it subscribes. The second Observer receives the value `2` even though it subscribed after the value `2` was sent.

```ts
import { BehaviorSubject } from 'rxjs';
const subject = new BehaviorSubject(0); // 0 is the initial value

subject.subscribe({
  next: (v) => console.log(`observerA: ${v}`),
});

subject.next(1);
subject.next(2);

subject.subscribe({
  next: (v) => console.log(`observerB: ${v}`),
});

subject.next(3);

// Logs
// observerA: 0
// observerA: 1
// observerA: 2
// observerB: 2
// observerA: 3
// observerB: 3
```





### ReplaySubject

A `ReplaySubject` is similar to a `BehaviorSubject` in that it can send old values to new subscribers, but it can also *record* a part of the Observable execution.

***A `ReplaySubject` records multiple values from the Observable execution and replays them to new subscribers.***

When creating a `ReplaySubject`, you can specify how many values to replay:

```ts
import { ReplaySubject } from 'rxjs';
const subject = new ReplaySubject(3); // buffer 3 values for new subscribers

subject.subscribe({
  next: (v) => console.log(`observerA: ${v}`),
});

subject.next(1);
subject.next(2);
subject.next(3);
subject.next(4);

subject.subscribe({
  next: (v) => console.log(`observerB: ${v}`),
});

subject.next(5);

// Logs:
// observerA: 1
// observerA: 2
// observerA: 3
// observerA: 4
// observerB: 2
// observerB: 3
// observerB: 4
// observerA: 5
// observerB: 5
```

You can also specify a *window time* in milliseconds, besides of the buffer size, to determine how old the recorded values can be. In the following example we use a large buffer size of `100`, but a window time parameter of just `500` milliseconds.

```ts
import { ReplaySubject } from 'rxjs';
const subject = new ReplaySubject(100, 500 /* windowTime */);

subject.subscribe({
  next: (v) => console.log(`observerA: ${v}`),
});

let i = 1;
setInterval(() => subject.next(i++), 200);

setTimeout(() => {
  subject.subscribe({
    next: (v) => console.log(`observerB: ${v}`),
  });
}, 1000);

// Logs
// observerA: 1
// observerA: 2
// observerA: 3
// observerA: 4
// observerA: 5
// observerB: 3
// observerB: 4
// observerB: 5
// observerA: 6
// observerB: 6
// ...
```



### AsyncSubject

The AsyncSubject is a variant where only the last value of the Observable execution is sent to its observers, and only when the execution completes.

```js
import { AsyncSubject } from 'rxjs';
const subject = new AsyncSubject();

subject.subscribe({
  next: (v) => console.log(`observerA: ${v}`),
});

subject.next(1);
subject.next(2);
subject.next(3);
subject.next(4);

subject.subscribe({
  next: (v) => console.log(`observerB: ${v}`),
});

subject.next(5);
subject.complete();

// Logs:
// observerA: 5
// observerB: 5
```

The AsyncSubject is similar to the [`last()`](https://rxjs.dev/api/operators/last) operator, in that it waits for the `complete` notification in order to deliver a single value.





### Void subject

Sometimes the emitted value doesn't matter as much as the fact that a value was emitted.

For instance, the code below signals that one second has passed.

```ts
const subject = new Subject<string>();
setTimeout(() => subject.next('dummy'), 1000);
```

Passing a dummy value this way is clumsy and can confuse users.

By declaring a *void subject*, you signal that the value is irrelevant. Only the event itself matters.

```ts
const subject = new Subject<void>();
setTimeout(() => subject.next(), 1000);
```

A complete example with context is shown below:

```ts
import { Subject } from 'rxjs';

const subject = new Subject(); // Shorthand for Subject<void>

subject.subscribe({
  next: () => console.log('One second has passed'),
});

setTimeout(() => subject.next(), 1000);
```

*Before version 7, the default type of Subject values was `any`. `Subject<any>` disables type checking of the emitted values, whereas `Subject<void>` prevents accidental access to the emitted value. If you want the old behavior, then replace `Subject` with `Subject<any>`.*







## Scheduler

### **What is a Scheduler?** 

A scheduler controls when a subscription starts and when notifications are delivered. It consists of three components.

- **A Scheduler is a data structure.** It knows how to store and queue tasks based on priority or other criteria.
- **A Scheduler is an execution context.** It denotes where and when the task is executed (e.g. immediately, or in another callback mechanism such as setTimeout or process.nextTick, or the animation frame).
- **A Scheduler has a (virtual) clock.** It provides a notion of "time" by a getter method `now()` on the scheduler. Tasks being scheduled on a particular scheduler will adhere only to the time denoted by that clock.

*A Scheduler lets you define in what execution context will an Observable deliver notifications to its Observer.*

In the example below, we take the usual simple Observable that emits values `1`, `2`, `3` synchronously, and use the operator `observeOn` to specify the `async` scheduler to use for delivering those values.

```ts
import { Observable, observeOn, asyncScheduler } from 'rxjs';

const observable = new Observable((observer) => {
  observer.next(1);
  observer.next(2);
  observer.next(3);
  observer.complete();
}).pipe(
  observeOn(asyncScheduler)
);

console.log('just before subscribe');
observable.subscribe({
  next(x) {
    console.log('got value ' + x);
  },
  error(err) {
    console.error('something wrong occurred: ' + err);
  },
  complete() {
    console.log('done');
  },
});
console.log('just after subscribe');
```

Which executes with the output:

```none
content_copyopen_in_newjust before subscribe
just after subscribe
got value 1
got value 2
got value 3
done
```

Notice how the notifications `got value...` were delivered after `just after subscribe`, which is different to the default behavior we have seen so far. This is because `observeOn(asyncScheduler)` introduces a proxy Observer between `new Observable` and the final Observer. Let's rename some identifiers to make that distinction obvious in the example code:

```ts
import { Observable, observeOn, asyncScheduler } from 'rxjs';

const observable = new Observable((proxyObserver) => {
  proxyObserver.next(1);
  proxyObserver.next(2);
  proxyObserver.next(3);
  proxyObserver.complete();
}).pipe(
  observeOn(asyncScheduler)
);

const finalObserver = {
  next(x) {
    console.log('got value ' + x);
  },
  error(err) {
    console.error('something wrong occurred: ' + err);
  },
  complete() {
    console.log('done');
  },
};

console.log('just before subscribe');
observable.subscribe(finalObserver);
console.log('just after subscribe');
```

The `proxyObserver` is created in `observeOn(asyncScheduler)`, and its `next(val)` function is approximately the following:

```ts
const proxyObserver = {
  next(val) {
    asyncScheduler.schedule(
      (x) => finalObserver.next(x),
      0 /* delay */,
      val /* will be the x for the function above */
    );
  },

  // ...
};
```

The `async` Scheduler operates with a `setTimeout` or `setInterval`, even if the given `delay` was zero. As usual, in JavaScript, `setTimeout(fn, 0)` is known to run the function `fn` earliest on the next event loop iteration. This explains why `got value 1` is delivered to the `finalObserver` after `just after subscribe` happened.

The `schedule()` method of a Scheduler takes a `delay` argument, which refers to a quantity of time relative to the Scheduler's own internal clock. A Scheduler's clock need not have any relation to the actual wall-clock time. This is how temporal operators like `delay` operate not on actual time, but on time dictated by the Scheduler's clock. This is specially useful in testing, where a *virtual time Scheduler* may be used to fake wall-clock time while in reality executing scheduled tasks synchronously.



### Scheduler Types

The `async` Scheduler is one of the built-in schedulers provided by RxJS. Each of these can be created and returned by using static properties of the `Scheduler` object.

| SCHEDULER                 | PURPOSE                                                      |
| :------------------------ | :----------------------------------------------------------- |
| `null`                    | By not passing any scheduler, notifications are delivered synchronously and recursively. Use this for constant-time operations or tail recursive operations. |
| `queueScheduler`          | Schedules on a queue in the current event frame (trampoline scheduler). Use this for iteration operations. |
| `asapScheduler`           | Schedules on the micro task queue, which is the same queue used for promises. Basically after the current job, but before the next job. Use this for asynchronous conversions. |
| `asyncScheduler`          | Schedules work with `setInterval`. Use this for time-based operations. |
| `animationFrameScheduler` | Schedules task that will happen just before next browser content repaint. Can be used to create smooth browser animations. |



### Using Schedulers

You may have already used schedulers in your RxJS code without explicitly stating the type of schedulers to be used. This is because all Observable operators that deal with concurrency have optional schedulers. If you do not provide the scheduler, RxJS will pick a default scheduler by using the principle of least concurrency. This means that the scheduler which introduces the least amount of concurrency that satisfies the needs of the operator is chosen. For example, for operators returning an observable with a finite and small number of messages, RxJS uses no Scheduler, i.e. `null` or `undefined`. For operators returning a potentially large or infinite number of messages, `queue` Scheduler is used. For operators which use timers, `async` is used.

Because RxJS uses the least concurrency scheduler, you can pick a different scheduler if you want to introduce concurrency for performance purpose. To specify a particular scheduler, you can use those operator methods that take a scheduler, e.g., `from([10, 20, 30], asyncScheduler)`.

**Static creation operators usually take a Scheduler as argument.** For instance, `from(array, scheduler)` lets you specify the Scheduler to use when delivering each notification converted from the `array`. It is usually the last argument to the operator. The following static creation operators take a Scheduler argument:

- `bindCallback`
- `bindNodeCallback`
- `combineLatest`
- `concat`
- `empty`
- `from`
- `fromPromise`
- `interval`
- `merge`
- `of`
- `range`
- `throw`
- `timer`

**Use `subscribeOn` to schedule in what context will the `subscribe()` call happen.** By default, a `subscribe()` call on an Observable will happen synchronously and immediately. However, you may delay or schedule the actual subscription to happen on a given Scheduler, using the instance operator `subscribeOn(scheduler)`, where `scheduler` is an argument you provide.

**Use `observeOn` to schedule in what context will notifications be delivered.** As we saw in the examples above, instance operator `observeOn(scheduler)` introduces a mediator Observer between the source Observable and the destination Observer, where the mediator schedules calls to the destination Observer using your given `scheduler`.

**Instance operators may take a Scheduler as argument.**

Time-related operators like `bufferTime`, `debounceTime`, `delay`, `auditTime`, `sampleTime`, `throttleTime`, `timeInterval`, `timeout`, `timeoutWith`, `windowTime` all take a Scheduler as the last argument, and otherwise operate by default on the `asyncScheduler`.

Other instance operators that take a Scheduler as argument: `cache`, `combineLatest`, `concat`, `expand`, `merge`, `publishReplay`, `startWith`.

Notice that both `cache` and `publishReplay` accept a Scheduler because they utilize a ReplaySubject. The constructor of a ReplaySubjects takes an optional Scheduler as the last argument because ReplaySubject may deal with time, which only makes sense in the context of a Scheduler. By default, a ReplaySubject uses the `queue` Scheduler to provide a clock.
