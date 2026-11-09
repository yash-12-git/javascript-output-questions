# JavaScript Output Questions

111 "what does this print?" questions, the kind frontend interviews use to test how well you really know JavaScript: closures, `this`, the event loop, hoisting, coercion and more.

Each question has the answer and a short explanation, hidden until you open it. Try to predict the output first. Better still, **play each one on [JSPrep Pro](https://jsprep.pro/javascript-output-questions?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**: type your prediction, check it, and see where your reasoning went wrong.

If this helps your prep, a star helps other people find it.

## Contents

- [Closures & Scope](#closures--scope) (13)
- ['this' Binding](#this-binding) (10)
- [Event Loop & Promises](#event-loop--promises) (22)
- [Type Coercion](#type-coercion) (15)
- [Hoisting](#hoisting) (7)
- [Prototypes & Inheritance](#prototypes--inheritance) (8)
- [Array Methods](#array-methods) (11)
- [Modern JS](#modern-js) (15)
- [Generators & Iterators](#generators--iterators) (6)
- [Misc Quirks](#misc-quirks) (3)
- [Type Conversion](#type-conversion) (1)

---

## Closures & Scope

### 1. Classic var in loop with setTimeout

**Difficulty:** Easy. **Topic:** Closures & Scope.

What does this print?

```javascript
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 0);
}
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-1-classic-var-in-loop-with-settimeout?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
3
3
3
```

var is function-scoped — all three callbacks close over the same i. By the time they run, the loop has finished and i is 3.

**Key insight:** var in loops creates one shared binding. Every closure captures the same variable, not its value at that iteration.

</details>

---

### 2. let in loop — each iteration gets own binding

**Difficulty:** Easy. **Topic:** Closures & Scope.

What does this print?

```javascript
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 0);
}
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-2-let-in-loop-each-iteration-gets-own-bind?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
0
1
2
```

let creates a new binding per loop iteration. Each callback closes over its own independent i.

**Key insight:** let creates a fresh binding per loop iteration — the solution to the classic var closure bug.

</details>

---

### 3. IIFE captures loop variable

**Difficulty:** Medium. **Topic:** Closures & Scope.

What does this print?

```javascript
const fns = [];
for (var i = 0; i < 3; i++) {
  fns.push((function(j) {
    return () => j;
  })(i));
}
console.log(fns[0]());
console.log(fns[2]());
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-3-iife-captures-loop-variable?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
0
2
```

The IIFE captures i as parameter j at call time. j is a fresh binding per IIFE invocation — not shared like var i.

**Key insight:** IIFE was the pre-let solution: capture the current value as a function argument to create a new binding.

</details>

---

### 4. Closure mutation persists across calls

**Difficulty:** Medium. **Topic:** Closures & Scope.

What does this print?

```javascript
function outer() {
  let x = 10;
  return function inner() {
    x++;
    return x;
  };
}
const fn = outer();
console.log(fn());
console.log(fn());
console.log(fn());
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-4-closure-mutation-persists-across-calls?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
11
12
13
```

inner closes over x. Each call increments the same x — closures capture the live variable binding, not a snapshot.

**Key insight:** Closures share a live variable binding. Mutations persist and are visible on every subsequent call.

</details>

---

### 5. let block scope — inner does not leak

**Difficulty:** Easy. **Topic:** Closures & Scope.

What does this print?

```javascript
let x = 'outer';
{
  let x = 'inner';
  console.log(x);
}
console.log(x);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-5-let-block-scope-inner-does-not-leak?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
inner
outer
```

let is block-scoped. The inner x exists only inside the block. The outer x is completely unaffected.

**Key insight:** let creates a new binding per block. Inner and outer x are independent — no leaking.

</details>

---

### 6. var leaks out of block, let does not

**Difficulty:** Easy. **Topic:** Closures & Scope.

What does this print?

```javascript
{
  var a = 1;
  let b = 2;
}
console.log(a);
console.log(typeof b);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-6-var-leaks-out-of-block-let-does-not?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
1
undefined
```

var is function-scoped (or global here) — it escapes the block. let is block-scoped — b is inaccessible. typeof returns "undefined" safely without throwing.

**Key insight:** var ignores block scope. typeof on a non-existent variable returns "undefined" instead of throwing.

</details>

---

### 7. Independent counter factory instances

**Difficulty:** Medium. **Topic:** Closures & Scope.

What does this print?

```javascript
function makeCounter() {
  let count = 0;
  return {
    inc() { count++; },
    get() { return count; },
  };
}
const a = makeCounter();
const b = makeCounter();
a.inc(); a.inc();
b.inc();
console.log(a.get());
console.log(b.get());
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-7-independent-counter-factory-instances?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
2
1
```

Each makeCounter() call creates a fresh closure scope with its own count. a and b are completely independent.

**Key insight:** Each factory call creates a new lexical environment. This is the module pattern for private state.

</details>

---

### 8. Closure captures binding not snapshot

**Difficulty:** Medium. **Topic:** Closures & Scope.

What does this print?

```javascript
let value = 1;
const fn = () => value;
value = 2;
console.log(fn());
value = 3;
console.log(fn());
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-8-closure-captures-binding-not-snapshot?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
2
3
```

fn closes over the variable value, not the value 1 at definition time. It always reads the current value of the binding.

**Key insight:** Closures capture variable bindings (references), not values. The latest assignment is always visible.

</details>

---

### 9. var inside function shadows outer

**Difficulty:** Medium. **Topic:** Closures & Scope.

What does this print?

```javascript
var x = 'global';
function test() {
  console.log(x);
  var x = 'local';
  console.log(x);
}
test();
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-9-var-inside-function-shadows-outer?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
undefined
local
```

var x inside test() is hoisted to the top of the function. First log sees the hoisted-but-uninitialized x (undefined), not the outer global.

**Key insight:** A local var declaration shadows the outer variable from the start of the function — not from where it appears.

</details>

---

### 10. Curried closure

**Difficulty:** Medium. **Topic:** Closures & Scope.

What does this print?

```javascript
function multiplier(x) {
  return (y) => x * y;
}
const double = multiplier(2);
const triple = multiplier(3);
console.log(double(5));
console.log(triple(double(2)));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-10-curried-closure?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
10
12
```

double(5) = 2*5 = 10. double(2) = 4, then triple(4) = 3*4 = 12.

**Key insight:** Currying returns a function that closes over the first argument. Each call creates a fresh closure.

</details>

---

### 11. Shared closure state across methods

**Difficulty:** Hard. **Topic:** Closures & Scope.

What does this print?

```javascript
function createObj() {
  let val = 0;
  return {
    inc()   { val++; },
    dec()   { val--; },
    reset() { val = 0; },
    get()   { return val; },
  };
}
const obj = createObj();
obj.inc(); obj.inc(); obj.inc(); obj.dec();
console.log(obj.get());
obj.reset();
console.log(obj.get());
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-11-shared-closure-state-across-methods?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
2
0
```

All four methods close over the same val variable. They share and mutate the same binding.

**Key insight:** All methods from a factory share one closure scope — one private variable, multiple public operations.

</details>

---

### 12. for...in includes inherited properties, for...of does not

**Difficulty:** Hard. **Topic:** Closures & Scope.

What does this print?

```javascript
const arr = [10, 20, 30];
arr.custom = 'extra';
const forIn = [], forOf = [];
for (const k in arr)  forIn.push(k);
for (const v of arr)  forOf.push(v);
console.log(forIn.join(','));
console.log(forOf.join(','));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-12-for-in-includes-inherited-properties-for?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
0,1,2,custom
10,20,30
```

for...in iterates all enumerable keys including custom properties. for...of uses the iterator and only yields the array elements.

**Key insight:** Never use for...in on arrays — it includes non-index properties. Use for...of for values.

</details>

---

### 13. Function is called with arguments object

**Difficulty:** Hard. **Topic:** Closures & Scope.

What does this print?

```javascript
function sum() {
  return Array.from(arguments).reduce((a, b) => a + b, 0);
}
const sumRest = (...args) => args.reduce((a, b) => a + b, 0);
console.log(sum(1, 2, 3, 4));
console.log(sumRest(1, 2, 3, 4));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-100-function-is-called-with-arguments-object?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
10
10
```

arguments is available in regular functions (not arrows). Rest params (...args) work in both and are more explicit. Both produce the same sum.

**Key insight:** arguments is only available in regular functions. Rest params (...args) work everywhere and are the modern replacement.

</details>

---

## 'this' Binding

### 14. Method call — this is the calling object

**Difficulty:** Easy. **Topic:** 'this' Binding.

What does this print?

```javascript
const obj = {
  name: 'Alice',
  greet() { return 'Hello, ' + this.name; },
};
console.log(obj.greet());
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-13-method-call-this-is-the-calling-object?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
Hello, Alice
```

When called as obj.greet(), this is set to obj — the object to the left of the dot.

**Key insight:** Method calls: this = the object before the final dot at the call site.

</details>

---

### 15. Arrow function has no own this — inherits lexically

**Difficulty:** Easy. **Topic:** 'this' Binding.

What does this print?

```javascript
'use strict';

function makeObj() {
  return {
    name: 'JSPrep',
    arrow: () => typeof this,
    regular() { return typeof this; },
  };
}

const obj = makeObj();
console.log(obj.arrow());
console.log(obj.regular());
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-14-arrow-function-has-no-own-this-inherits-?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
undefined
object
```

The arrow function is created inside makeObj, so it takes makeObj's this. makeObj is called as a plain function in strict mode, so that this is undefined. regular() is called as obj.regular(), so its this is obj.

**Key insight:** Arrow functions don't get their own this — they use the this of the code around them. Never use them as object methods.

</details>

---

### 16. bind permanently fixes this — call cannot override it

**Difficulty:** Medium. **Topic:** 'this' Binding.

What does this print?

```javascript
function getName() { return this.name; }
const alice = { name: 'Alice' };
const bob   = { name: 'Bob' };
const boundAlice = getName.bind(alice);
console.log(boundAlice());
console.log(boundAlice.call(bob));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-15-bind-permanently-fixes-this-call-cannot-?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
Alice
Alice
```

bind creates a permanently bound function. Even .call(bob) cannot override the bound this.

**Key insight:** bind() wins over call/apply. A bound function's this is permanent and cannot be overridden.

</details>

---

### 17. call vs apply — same result, different syntax

**Difficulty:** Medium. **Topic:** 'this' Binding.

What does this print?

```javascript
function greet(greeting) {
  return greeting + ', ' + this.name + '!';
}
const user = { name: 'Bob' };
console.log(greet.call(user, 'Hello'));
console.log(greet.apply(user, ['Hi']));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-16-call-vs-apply-same-result-different-synt?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
Hello, Bob!
Hi, Bob!
```

call and apply both set this explicitly. call takes arguments individually; apply takes an array.

**Key insight:** call(thisArg, ...args) vs apply(thisArg, [args]) — same result, different argument style.

</details>

---

### 18. Arrow callback inside method preserves this

**Difficulty:** Medium. **Topic:** 'this' Binding.

What does this print?

```javascript
const timer = {
  seconds: 0,
  start() {
    const tick = () => ++this.seconds;
    tick(); tick(); tick();
    return this.seconds;
  }
};
console.log(timer.start());
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-17-arrow-callback-inside-method-preserves-t?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
3
```

Arrow function tick inherits this from start's context (timer). Each tick() increments timer.seconds.

**Key insight:** Arrow callbacks inside methods inherit the method's this — solving the classic callback this-loss problem.

</details>

---

### 19. Method chaining — return this

**Difficulty:** Medium. **Topic:** 'this' Binding.

What does this print?

```javascript
class Builder {
  constructor() { this.parts = []; }
  add(part) { this.parts.push(part); return this; }
  build()   { return this.parts.join(' + '); }
}
console.log(new Builder().add('A').add('B').add('C').build());
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-18-method-chaining-return-this?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
A + B + C
```

add() returns this, enabling chaining. build() collects all parts.

**Key insight:** Returning this from methods enables fluent interfaces and method chaining.

</details>

---

### 20. this in nested object — direct parent wins

**Difficulty:** Medium. **Topic:** 'this' Binding.

What does this print?

```javascript
const outer = {
  name: 'outer',
  inner: {
    name: 'inner',
    greet() { return this.name; },
  },
};
console.log(outer.inner.greet());
const fn = outer.inner.greet;
console.log(fn?.call(outer));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-19-this-in-nested-object-direct-parent-wins?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
inner
outer
```

outer.inner.greet() — this is inner (the direct calling object). fn.call(outer) explicitly sets this to outer.

**Key insight:** this is always the object immediately before the final dot — not a parent object.

</details>

---

### 21. bind with partial application

**Difficulty:** Hard. **Topic:** 'this' Binding.

What does this print?

```javascript
function multiply(a, b) { return a * b; }
const double = multiply.bind(null, 2);
const triple = multiply.bind(null, 3);
console.log(double(5));
console.log(triple(4));
console.log(double(triple(2)));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-20-bind-with-partial-application?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
10
12
12
```

bind(null, 2) pre-fills a=2. double(5)=10. triple(2)=6. double(6)=12.

**Key insight:** bind can partially apply arguments. The first call fills early arguments — remaining ones come from later calls.

</details>

---

### 22. Class method loses this when destructured

**Difficulty:** Hard. **Topic:** 'this' Binding.

What does this print?

```javascript
class Greeter {
  constructor(name) { this.name = name; }
  greet() { return 'Hi, ' + this.name; }
}
const g = new Greeter('Alice');
console.log(g.greet());
const { greet } = g;
console.log(greet?.call(g));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-21-class-method-loses-this-when-destructure?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
Hi, Alice
Hi, Alice
```

g.greet() has this = g (correct). Destructuring detaches the method but .call(g) explicitly provides the context.

**Key insight:** Destructuring methods detaches them from their object. Always bind or use .call() when passing methods as callbacks.

</details>

---

### 23. new vs plain call — different returns

**Difficulty:** Hard. **Topic:** 'this' Binding.

What does this print?

```javascript
function Person(name) { this.name = name; }
const p1 = new Person('Alice');
const p2 = Person.call({}, 'Bob');
console.log(p1.name);
console.log(p2);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-22-new-vs-plain-call-different-returns?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
Alice
undefined
```

new creates an object and returns it. Person.call({}, "Bob") sets this on the passed object but since the function returns nothing, call returns undefined.

**Key insight:** new returns the constructed object. A plain call returns the function's explicit return value — undefined if nothing.

</details>

---

## Event Loop & Promises

### 24. Synchronous code runs before setTimeout

**Difficulty:** Easy. **Topic:** Event Loop & Promises.

What does this print?

```javascript
console.log('1');
setTimeout(() => console.log('2'), 0);
console.log('3');
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-23-synchronous-code-runs-before-settimeout?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
1
3
2
```

Synchronous code runs first (1, 3). setTimeout callbacks are macrotasks — they run after all synchronous code.

**Key insight:** All sync code finishes before any macrotask (setTimeout) runs — even with 0ms delay.

</details>

---

### 25. Promise microtask before setTimeout macrotask

**Difficulty:** Medium. **Topic:** Event Loop & Promises.

What does this print?

```javascript
console.log('start');
setTimeout(() => console.log('timeout'), 0);
Promise.resolve().then(() => console.log('promise'));
console.log('end');
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-24-promise-microtask-before-settimeout-macr?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
start
end
promise
timeout
```

Sync runs first (start, end). Then microtask queue drains (promise). Then macrotask queue (timeout).

**Key insight:** Order: sync → microtasks (Promise.then) → macrotasks (setTimeout). Microtasks always run before the next macrotask.

</details>

---

### 26. Promise chain passes values

**Difficulty:** Medium. **Topic:** Event Loop & Promises.

What does this print?

```javascript
Promise.resolve(2)
  .then(v => v * 3)
  .then(v => v + 4)
  .then(v => console.log(v));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-25-promise-chain-passes-values?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
10
```

Each .then receives the return value of the previous. 2*3=6, 6+4=10.

**Key insight:** Promise chains pass return values downstream. Each .then transforms the value.

</details>

---

### 27. Two Promise chains interleave in microtask queue

**Difficulty:** Hard. **Topic:** Event Loop & Promises.

What does this print?

```javascript
Promise.resolve()
  .then(() => console.log(1))
  .then(() => console.log(2));

Promise.resolve()
  .then(() => console.log(3))
  .then(() => console.log(4));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-26-two-promise-chains-interleave-in-microta?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
1
3
2
4
```

After sync code, both first .then callbacks are queued (1 and 3). After 1 runs, 2 is queued. Then 3 runs, queueing 4. Then 2, then 4.

**Key insight:** Multiple Promise chains interleave by round-robin. Each .then schedules the NEXT .then — they don't all run back-to-back.

</details>

---

### 28. await suspends function — caller continues

**Difficulty:** Medium. **Topic:** Event Loop & Promises.

What does this print?

```javascript
console.log('1');
async function main() {
  console.log('2');
  await Promise.resolve();
  console.log('3');
}
main();
console.log('4');
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-27-await-suspends-function-caller-continues?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
1
2
4
3
```

1 logs. main() starts, logs 2. await suspends main (schedules resume as microtask). 4 logs. Then microtask runs: 3 logs.

**Key insight:** await suspends the async function. The caller continues synchronously. The resumed code runs as a microtask.

</details>

---

### 29. setTimeout fires in delay order

**Difficulty:** Medium. **Topic:** Event Loop & Promises.

What does this print?

```javascript
setTimeout(() => console.log('c'), 100);
setTimeout(() => console.log('a'), 0);
setTimeout(() => console.log('b'), 50);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-28-settimeout-fires-in-delay-order?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
a
b
c
```

Timers fire after their delay expires. 0ms → a, 50ms → b, 100ms → c.

**Key insight:** setTimeout fires after AT LEAST the specified delay, ordered by when they expire.

</details>

---

### 30. Promise executor runs synchronously

**Difficulty:** Medium. **Topic:** Event Loop & Promises.

What does this print?

```javascript
console.log('a');
new Promise(resolve => {
  console.log('b');
  resolve();
}).then(() => console.log('c'));
console.log('d');
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-29-promise-executor-runs-synchronously?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
a
b
d
c
```

The executor function runs synchronously (b logs immediately). resolve() schedules .then as a microtask. d logs sync. Then microtask: c.

**Key insight:** The Promise constructor executor is synchronous. Only .then callbacks are asynchronous.

</details>

---

### 31. Nested setTimeout creates new macrotask

**Difficulty:** Hard. **Topic:** Event Loop & Promises.

What does this print?

```javascript
setTimeout(() => {
  console.log('outer');
  setTimeout(() => console.log('inner'), 0);
}, 0);
console.log('sync');
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-30-nested-settimeout-creates-new-macrotask?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
sync
outer
inner
```

sync runs first. outer fires in macrotask 1. Inside it, inner is registered as a new macrotask. inner runs in macrotask 2.

**Key insight:** A setTimeout inside a setTimeout callback schedules a new macrotask — runs in a future event loop iteration.

</details>

---

### 32. Promise.then is always async — even when resolved

**Difficulty:** Medium. **Topic:** Event Loop & Promises.

What does this print?

```javascript
let x = 0;
Promise.resolve().then(() => { x = 1; });
console.log(x);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-31-promise-then-is-always-async-even-when-r?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
0
```

Even though Promise.resolve() is already resolved, .then is always async (microtask). x is still 0 when synchronously logged.

**Key insight:** Promise .then callbacks are ALWAYS asynchronous — guaranteed by the spec, even for already-resolved promises.

</details>

---

### 33. async function always returns a Promise

**Difficulty:** Medium. **Topic:** Event Loop & Promises.

What does this print?

```javascript
async function getValue() { return 42; }
const result = getValue();
console.log(result instanceof Promise);
result.then(v => console.log(v));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-32-async-function-always-returns-a-promise?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
true
42
```

async functions always return a Promise. result is a Promise, not 42. .then unwraps the value.

**Key insight:** async functions always return a Promise wrapping their return value.

</details>

---

### 34. Multiple awaits — each is a microtask suspension

**Difficulty:** Hard. **Topic:** Event Loop & Promises.

What does this print?

```javascript
async function run() {
  console.log('A');
  await null;
  console.log('B');
  await null;
  console.log('C');
}
run();
console.log('D');
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-33-multiple-awaits-each-is-a-microtask-susp?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
A
D
B
C
```

A logs sync. First await suspends. D logs sync. Microtask resumes: B. Second await suspends. Microtask resumes: C.

**Key insight:** Each await is a suspension point. Code between awaits runs as separate microtasks.

</details>

---

### 35. Promise.reject caught by .catch — chain continues

**Difficulty:** Medium. **Topic:** Event Loop & Promises.

What does this print?

```javascript
Promise.reject(42)
  .catch(v => v + 1)
  .then(v => console.log(v));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-34-promise-reject-caught-by-catch-chain-con?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
43
```

.catch handles the rejection (v=42), returns 43. The chain continues — next .then receives 43.

**Key insight:** .catch can return a value to convert rejection back to fulfillment and continue the chain.

</details>

---

### 36. Promise.all preserves order

**Difficulty:** Medium. **Topic:** Event Loop & Promises.

What does this print?

```javascript
Promise.all([
  Promise.resolve(1),
  Promise.resolve(2),
  Promise.resolve(3),
]).then(values => console.log(values.join(',')));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-35-promise-all-preserves-order?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
1,2,3
```

Promise.all waits for all and resolves with an array of values in the original order.

**Key insight:** Promise.all preserves input order in results regardless of which promise resolves first.

</details>

---

### 37. queueMicrotask runs before setTimeout

**Difficulty:** Medium. **Topic:** Event Loop & Promises.

What does this print?

```javascript
console.log('1');
setTimeout(() => console.log('2'), 0);
queueMicrotask(() => console.log('3'));
console.log('4');
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-36-queuemicrotask-runs-before-settimeout?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
1
4
3
2
```

Sync (1, 4). Microtask: queueMicrotask callback (3). Macrotask: setTimeout (2).

**Key insight:** queueMicrotask schedules a microtask — same queue as Promise.then, runs before any macrotask.

</details>

---

### 38. throw inside .then becomes rejection, caught by .catch

**Difficulty:** Hard. **Topic:** Event Loop & Promises.

What does this print?

```javascript
Promise.resolve('ok')
  .then(() => { throw new Error('oops'); })
  .catch(e => e.message)
  .then(msg => console.log(msg));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-37-throw-inside-then-becomes-rejection-caug?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
oops
```

throw inside .then converts to rejected Promise. .catch handles it and returns e.message. Next .then receives "oops".

**Key insight:** throw inside .then = return Promise.reject(). Errors propagate down the chain until a .catch handles them.

</details>

---

### 39. async try/catch catches awaited rejection

**Difficulty:** Hard. **Topic:** Event Loop & Promises.

What does this print?

```javascript
async function failingTask() {
  throw new Error('task failed');
}
async function main() {
  try {
    await failingTask();
    console.log('success');
  } catch (e) {
    console.log('caught: ' + e.message);
  }
}
main();
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-99-async-try-catch-catches-awaited-rejectio?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
caught: task failed
```

async functions wrap throws in rejected Promises. await converts rejection back to a thrown error that try/catch handles.

**Key insight:** await unwraps Promise rejections into thrown errors. try/catch works naturally with async/await.

</details>

---

### 40. Deep Copy vs Shallow Copy in Promise Resolution

**Difficulty:** Medium. **Topic:** Event Loop & Promises.

What does this print?

```javascript
let originalObject = { a: 1, b: { c: 2 } }
let shallowCopy = { ...originalObject }
let deepCopy = JSON.parse(JSON.stringify(originalObject))

Promise.resolve().then(() => {
  originalObject.b.c = 3
  console.log('Shallow Copy:', shallowCopy.b.c)
  console.log('Deep Copy:', deepCopy.b.c)
})
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-deep-copy-vs-shallow-copy-in-promise-resolution?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
Shallow Copy: 3
Deep Copy: 2
```

When we create a shallow copy using the spread operator, it only copies the references to the original object's properties. In this case, both the original object and the shallow copy point to the same object { c: 2 }. When we modify this nested object, the change is reflected in both the original object and the shallow copy. On the other hand, when we create a deep copy using JSON.parse(JSON.stringify()), it creates a completely new object with new references. Therefore, modifying the original object does not affect the deep copy.

**Key insight:** The difference between shallow and deep copying in JavaScript, and how it affects the behavior of objects when modified

</details>

---

### 41. Understanding Call Stack Flow with Nested Function Calls

**Difficulty:** Easy. **Topic:** Event Loop & Promises.

What does this print?

```javascript
function a() { 
  console.log("Inside A"); 
  b(); 
  console.log("A finished"); 
} 
function b() { 
  console.log("Inside B"); 
} 
a();
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-understanding-call-stack-flow-with-nested-function-calls?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
Inside A
Inside B
A finished
```

The call stack flow starts at the global execution context (GEC). When function a() is called, it is added to the stack. Inside a(), it logs 'Inside A', then calls b(), which is also added to the stack. b() logs 'Inside B' and then finishes, so it is removed from the stack. After b() finishes, a() continues and logs 'A finished' before it is removed from the stack, returning to the GEC. This sequence reflects the stack flow: [GEC] → push a() → push b() → pop b() → pop a() → back to GEC.

**Key insight:** Call stack and the order of function execution

</details>

---

### 42. Deep Copy vs Shallow Copy with Promises

**Difficulty:** Medium. **Topic:** Event Loop & Promises.

What does this print?

```javascript
let original = { a: 1, b: { c: 2 } }
let shallowCopy = { ...original }
let deepCopy = JSON.parse(JSON.stringify(original))

Promise.resolve().then(() => {
  shallowCopy.a = 10
  shallowCopy.b.c = 20
  deepCopy.a = 30
  deepCopy.b.c = 40
  console.log(original.a, original.b.c)
  console.log(shallowCopy.a, shallowCopy.b.c)
  console.log(deepCopy.a, deepCopy.b.c)
})
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-deep-copy-vs-shallow-copy-with-promises?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
1 20
10 20
30 40
```

The spread copies the top level only, so shallowCopy.b is the same object as original.b. Setting shallowCopy.a = 10 doesn't affect the original, but shallowCopy.b.c = 20 changes the shared object, so original.b.c is 20 too. The JSON copy is fully independent.

**Key insight:** Deep copies create new objects for all elements, while shallow copies create new objects but insert references to the original elements for nested objects.

</details>

---

### 43. Deep Copy and Shallow Copy in Asynchronous Operations

**Difficulty:** Medium. **Topic:** Event Loop & Promises.

What does this print?

```javascript
let originalObject = { a: 1, b: { c: 2 } }
let shallowCopy = { ...originalObject }
let deepCopy = JSON.parse(JSON.stringify(originalObject))
originalObject.b.c = 3
console.log(shallowCopy.b.c)
console.log(deepCopy.b.c)
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-deep-copy-and-shallow-copy-in-asynchronous-operations?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
3
2
```

The shallow copy operation only copies the top-level properties of the original object. When the original object's nested property 'c' is modified, the shallow copy's 'c' is also modified because they both reference the same object. On the other hand, the deep copy operation recursively copies all properties, including nested objects, resulting in a completely independent copy. Therefore, modifying the original object does not affect the deep copy.

**Key insight:** Deep copy creates a completely independent copy of an object, whereas shallow copy only copies the top-level properties and references the same nested objects

</details>

---

### 44. Chaining Promises with Optional Chaining and Nullish Coalescing

**Difficulty:** Hard. **Topic:** Event Loop & Promises.

What does this print?

```javascript
function fetchUserData() {
  return Promise.resolve({ id: 1, name: 'John Doe' })
}

function fetchOrderData(userId) {
  return Promise.resolve([{ id: 101, total: 100 }, { id: 102, total: 200 }])
}

fetchUserData().then(user => {
  const orderId = user?.orders?.[0]?.id ?? 'No orders found'
  console.log(orderId)
  return fetchOrderData(user.id)
}).then(orders => {
  console.log(orders.map(o => o.id).join(','))
  const total = orders?.[0]?.total ?? 0
  console.log(total)
})
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-chaining-promises-with-optional-chaining-and-nullish-coalesc?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
No orders found
101,102
100
```

The code first fetches the user data, which returns a promise that resolves to an object with id and name properties. However, this object does not have an orders property. Therefore, when trying to access the id of the first order using optional chaining (?.), it returns undefined. The nullish coalescing operator (??) then returns the default value 'No orders found'. The code then fetches the order data for the user, which returns a promise that resolves to an array of order objects. Finally, it logs the total of the first order, again using optional chaining and nullish coalescing to handle the case where the orders array is empty.

**Key insight:** JavaScript Optional Chaining and Nullish Coalescing

</details>

---

### 45. Resolving Nested Promises with Optional Chaining and Nullish Coalescing

**Difficulty:** Hard. **Topic:** Event Loop & Promises.

What does this print?

```javascript
function getPokemon() {
  return new Promise((resolve) => {
    resolve({
      name: 'Pikachu',
      abilities: {
        ability1: 'Static',
        ability2: null
      }
    })
  })
}

getPokemon().then((pokemon) => {
  console.log(pokemon.name)
  console.log(pokemon.abilities?.ability2 ?? 'Not found')
  console.log(pokemon.abilities?.ability3 ?? 'Not found')
})
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-resolving-nested-promises-with-optional-chaining-and-nullish?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
Pikachu
Not found
Not found
```

?? returns the right-hand side when the left side is null or undefined. ability2 is null, so the first lookup falls back to 'Not found'. ability3 doesn't exist (undefined), so it falls back too.

**Key insight:** JavaScript Optional Chaining and Nullish Coalescing

</details>

---

## Type Coercion

### 46. Plus operator: string concat vs numeric add

**Difficulty:** Easy. **Topic:** Type Coercion.

What does this print?

```javascript
console.log(1 + '2');
console.log('3' - 1);
console.log(true + false);
console.log(null + 1);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-38-plus-operator-string-concat-vs-numeric-a?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
12
2
1
1
```

1+"2": + with string → "12". "3"-1: - forces numeric → 2. true+false: 1+0=1. null+1: null→0, 0+1=1.

**Key insight:** + prefers string concatenation when either side is a string. - always forces numeric conversion.

</details>

---

### 47. Loose equality edge cases with null

**Difficulty:** Medium. **Topic:** Type Coercion.

What does this print?

```javascript
console.log(0 == false);
console.log('' == false);
console.log(null == undefined);
console.log(null == false);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-39-loose-equality-edge-cases-with-null?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
true
true
true
false
```

0==false: both→0. ""==false: both→0. null==undefined: spec rule. null==false: null only equals null or undefined.

**Key insight:** null is only == to null and undefined — NOT to 0, false, or "".

</details>

---

### 48. typeof for common values

**Difficulty:** Easy. **Topic:** Type Coercion.

What does this print?

```javascript
console.log(typeof null);
console.log(typeof undefined);
console.log(typeof []);
console.log(typeof function(){});
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-40-typeof-for-common-values?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
object
undefined
object
function
```

typeof null = "object" is a famous JS bug. typeof [] = "object" (arrays are objects). Functions have their own typeof result.

**Key insight:** typeof null = "object" is a historic bug. Use Array.isArray() for arrays.

</details>

---

### 49. Truthy and falsy — empty array and object are truthy

**Difficulty:** Medium. **Topic:** Type Coercion.

What does this print?

```javascript
console.log(Boolean(0));
console.log(Boolean(''));
console.log(Boolean(null));
console.log(Boolean([]));
console.log(Boolean({}));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-41-truthy-and-falsy-empty-array-and-object-?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
false
false
false
true
true
```

Falsy: 0, "", null, undefined, NaN, false. Everything else is truthy — including empty arrays and objects.

**Key insight:** Empty arrays [] and objects {} are TRUTHY. This surprises many developers.

</details>

---

### 50. Unary plus converts to number

**Difficulty:** Medium. **Topic:** Type Coercion.

What does this print?

```javascript
console.log(+'');
console.log(+null);
console.log(+undefined);
console.log(+true);
console.log(+'3.14');
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-42-unary-plus-converts-to-number?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
0
0
NaN
1
3.14
```

+"" → 0. +null → 0. +undefined → NaN. +true → 1. +"3.14" → 3.14.

**Key insight:** Unary + is the shortest coercion to number. undefined and non-numeric strings produce NaN.

</details>

---

### 51. null comparison inconsistency

**Difficulty:** Hard. **Topic:** Type Coercion.

What does this print?

```javascript
console.log(null > 0);
console.log(null == 0);
console.log(null >= 0);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-43-null-comparison-inconsistency?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
false
false
true
```

null>0: null→0, 0>0=false. null==0: false (null only equals undefined). null>=0: null→0, 0>=0=true. Famous inconsistency!

**Key insight:** null coerces to 0 for relational ops (>, <, >=, <=) but NOT for ==. This creates the famous null>=0 && !(null==0) quirk.

</details>

---

### 52. Short circuit: || vs ?? difference

**Difficulty:** Easy. **Topic:** Type Coercion.

What does this print?

```javascript
console.log(0 || 'default');
console.log(1 && 'value');
console.log(null ?? 'fallback');
console.log(0 ?? 'not this');
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-44-short-circuit-vs-difference?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
default
value
fallback
0
```

||: first truthy or last. &&: first falsy or last. ??: right only if left is null/undefined. 0 is not null, so ?? keeps 0.

**Key insight:** ?? only triggers on null/undefined. || also triggers on 0, "", false. Use ?? when 0 or "" are valid values.

</details>

---

### 53. Object valueOf for arithmetic, toString for template

**Difficulty:** Hard. **Topic:** Type Coercion.

What does this print?

```javascript
const obj = {
  valueOf()  { return 42; },
  toString() { return 'obj'; },
};
console.log(obj + 1);
console.log(`${obj}`);
console.log(String(obj));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-45-object-valueof-for-arithmetic-tostring-f?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
43
obj
obj
```

obj+1: + with number prefers valueOf (42+1=43). Template literal prefers toString ("obj"). String() calls toString ("obj").

**Key insight:** For arithmetic +: valueOf is preferred. For template literals and String(): toString is preferred.

</details>

---

### 54. parseInt stops at first invalid character

**Difficulty:** Medium. **Topic:** Type Coercion.

What does this print?

```javascript
console.log(parseInt('10px'));
console.log(parseInt('0x1F'));
console.log(parseInt('10', 2));
console.log(parseInt('hello'));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-46-parseint-stops-at-first-invalid-characte?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
10
31
2
NaN
```

"10px"→10 (stops at p). "0x1F" is hex=31. Base 2: "10"=2. "hello"→NaN (no valid digits).

**Key insight:** parseInt stops at the first non-parseable character. Always specify the radix to avoid ambiguity.

</details>

---

### 55. String comparison is lexicographic

**Difficulty:** Medium. **Topic:** Type Coercion.

What does this print?

```javascript
console.log('5' > 3);
console.log('5' > '30');
console.log('10' > '9');
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-47-string-comparison-is-lexicographic?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
true
true
false
```

"5">3: coerces to numbers, 5>3=true. "5">"30": string compare, "5" char > "3" char=true. "10">"9": "1"<"9"=false.

**Key insight:** Number vs string uses numeric comparison. String vs string uses char codes (lexicographic).

</details>

---

### 56. Array coercion with plus operator

**Difficulty:** Hard. **Topic:** Type Coercion.

What does this print?

```javascript
console.log([] + []);
console.log([] + {});
console.log(+[]);
console.log(+[42]);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-48-array-coercion-with-plus-operator?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```

[object Object]
0
42
```

[]+[]: ""+"" = "". []+{}: ""+"[object Object]". +[]: []→""→0. +[42]: "42"→42.

**Key insight:** Arrays convert to their string representation for coercion: [] → "", [1,2] → "1,2".

</details>

---

### 57. NaN is not equal to anything including itself

**Difficulty:** Easy. **Topic:** Type Coercion.

What does this print?

```javascript
console.log(NaN === NaN);
console.log(NaN == NaN);
console.log(isNaN('hello'));
console.log(Number.isNaN('hello'));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-49-nan-is-not-equal-to-anything-including-i?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
false
false
true
false
```

NaN is the only value not equal to itself. isNaN("hello") coerces to NaN first (true). Number.isNaN doesn't coerce — "hello" is not NaN (false).

**Key insight:** NaN !== NaN always. Number.isNaN() is strict (no coercion). isNaN() converts first.

</details>

---

### 58. Object equality is reference-based

**Difficulty:** Medium. **Topic:** Type Coercion.

What does this print?

```javascript
const a = { x: 1 };
const b = { x: 1 };
const c = a;
console.log(a == b);
console.log(a === b);
console.log(a === c);
console.log(a.x === c.x);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-50-object-equality-is-reference-based?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
false
false
true
true
```

a and b are different objects — same content but different references. c = a copies the reference, so a===c is true.

**Key insight:** Object equality compares references, not content. Two objects with identical properties are NOT equal unless same reference.

</details>

---

### 59. String spread — unicode-safe character split

**Difficulty:** Medium. **Topic:** Type Coercion.

What does this print?

```javascript
const str = 'hello';
const chars = [...str];
console.log(chars.length);
console.log(chars.reverse().join(''));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-98-string-spread-unicode-safe-character-spl?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
5
olleh
```

Strings are iterable — spread gives an array of characters. reverse() and join() produce the reversed string.

**Key insight:** [...str] splits a string into characters correctly, even for multi-byte Unicode (unlike split("")).

</details>

---

### 60. JavaScript Optional Chaining and Nullish Coalescing

**Difficulty:** Easy. **Topic:** Type Coercion.

What does this print?

```javascript
let person = { name: 'John', address: { street: '123 Main St' } }
let unknownPerson = null
console.log(person.address?.street)
console.log(unknownPerson?.address?.street ?? 'Unknown')
console.log(person.phone ?? '(no phone)')
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-javascript-optional-chaining-and-nullish-coalescing?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
123 Main St
Unknown
(no phone)
```

The code uses optional chaining (?.) to safely navigate through nested objects. When person.address?.street is accessed, it returns '123 Main St' because person.address exists and has a street property. When unknownPerson?.address?.street is accessed, it returns undefined because unknownPerson is null. The nullish coalescing operator (??) then returns 'Unknown' as the default value. Finally, person.phone ?? '(no phone)' returns '(no phone)' because person.phone is undefined.

**Key insight:** JavaScript Optional Chaining and Nullish Coalescing

</details>

---

## Hoisting

### 61. var declaration hoisted as undefined

**Difficulty:** Easy. **Topic:** Hoisting.

What does this print?

```javascript
console.log(x);
var x = 5;
console.log(x);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-51-var-declaration-hoisted-as-undefined?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
undefined
5
```

var declarations are hoisted (moved up) but not their assignments. First log sees the hoisted-but-uninitialized x.

**Key insight:** var hoisting: declaration moves to the top, assignment stays. Value is undefined before assignment.

</details>

---

### 62. Function declaration fully hoisted

**Difficulty:** Easy. **Topic:** Hoisting.

What does this print?

```javascript
console.log(greet('Alice'));
function greet(name) { return 'Hello, ' + name; }
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-52-function-declaration-fully-hoisted?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
Hello, Alice
```

Function declarations are fully hoisted — name and body both. You can call them before they appear in source.

**Key insight:** Function declarations hoist completely. Function expressions do not.

</details>

---

### 63. Function expression not hoisted

**Difficulty:** Easy. **Topic:** Hoisting.

What does this print?

```javascript
console.log(typeof greet);
var greet = function() { return 'hello'; };
console.log(typeof greet);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-53-function-expression-not-hoisted?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
undefined
function
```

var greet is hoisted as undefined. The assignment happens at runtime. Before it, greet is undefined.

**Key insight:** Function expressions (var fn = function(){}) — only the var declaration is hoisted, as undefined.

</details>

---

### 64. Local var shadows outer — from start of function

**Difficulty:** Medium. **Topic:** Hoisting.

What does this print?

```javascript
var a = 1;
function test() {
  console.log(a);
  var a = 2;
  console.log(a);
}
test();
console.log(a);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-54-local-var-shadows-outer-from-start-of-fu?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
undefined
2
1
```

var a inside test() is hoisted — it shadows the global a from the start. First log: hoisted undefined. Then assigned 2. Global a stays 1.

**Key insight:** Local var declarations shadow the outer variable from the BEGINNING of the function — not just from where they appear.

</details>

---

### 65. Function declaration takes priority over var in hoisting

**Difficulty:** Medium. **Topic:** Hoisting.

What does this print?

```javascript
console.log(typeof foo);
var foo = 1;
function foo() { return 2; }
console.log(typeof foo);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-55-function-declaration-takes-priority-over?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
function
number
```

Function declarations win over var during hoisting phase. At start, foo is the function. Then var foo=1 executes at runtime — overwriting with a number.

**Key insight:** Function declarations beat var declarations in the hoisting phase. But var assignments at runtime overwrite them.

</details>

---

### 66. which function declaration wins?

**Difficulty:** Medium. **Topic:** Hoisting.

What does this print?

```javascript
console.log(fn());
function fn() { return 1; }
function fn() { return 2; }
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-56-last-function-declaration-wins?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
2
```

Both function declarations are hoisted. The second overwrites the first. Only the last one remains.

**Key insight:** Multiple function declarations with the same name: the last one wins.

</details>

---

### 67. var escapes block, let does not

**Difficulty:** Hard. **Topic:** Hoisting.

What does this print?

```javascript
function test() {
  if (true) {
    var x = 10;
    let y = 20;
  }
  console.log(x);
  console.log(typeof y);
}
test();
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-57-var-escapes-block-let-does-not?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
10
undefined
```

var x escapes the if block (function-scoped). let y is block-scoped — inaccessible outside. typeof y returns "undefined" safely.

**Key insight:** var escapes blocks. let/const are block-scoped. typeof on a block-scoped variable outside its block returns "undefined".

</details>

---

## Prototypes & Inheritance

### 68. instanceof traverses the prototype chain

**Difficulty:** Easy. **Topic:** Prototypes & Inheritance.

What does this print?

```javascript
console.log([] instanceof Array);
console.log([] instanceof Object);
console.log({} instanceof Array);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-58-instanceof-traverses-the-prototype-chain?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
true
true
false
```

Arrays inherit from Array.prototype AND Object.prototype. [] instanceof Object is true. {} does not inherit from Array.

**Key insight:** instanceof traverses the full prototype chain. All objects are ultimately instanceof Object.

</details>

---

### 69. hasOwnProperty vs in operator

**Difficulty:** Medium. **Topic:** Prototypes & Inheritance.

What does this print?

```javascript
function Animal(name) { this.name = name; }
Animal.prototype.speak = function() { return this.name; };
const dog = new Animal('Rex');
console.log(dog.hasOwnProperty('name'));
console.log(dog.hasOwnProperty('speak'));
console.log('speak' in dog);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-59-hasownproperty-vs-in-operator?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
true
false
true
```

name is own. speak is on the prototype — hasOwnProperty is false. in checks the whole chain — true.

**Key insight:** hasOwnProperty = own only. in = entire chain. Use hasOwnProperty to distinguish instance vs inherited.

</details>

---

### 70. Class method override and instanceof

**Difficulty:** Medium. **Topic:** Prototypes & Inheritance.

What does this print?

```javascript
class Animal {
  speak() { return 'generic'; }
}
class Dog extends Animal {
  speak() { return 'woof'; }
}
const d = new Dog();
console.log(d.speak());
console.log(d instanceof Dog);
console.log(d instanceof Animal);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-60-class-method-override-and-instanceof?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
woof
true
true
```

Dog.speak() overrides Animal.speak(). d is instanceof both since Dog extends Animal.

**Key insight:** Subclass instances are instanceof both the subclass and all parent classes.

</details>

---

### 71. super calls the parent method

**Difficulty:** Medium. **Topic:** Prototypes & Inheritance.

What does this print?

```javascript
class Shape {
  describe() { return 'shape'; }
}
class Circle extends Shape {
  describe() { return super.describe() + ' (circle)'; }
}
console.log(new Circle().describe());
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-61-super-calls-the-parent-method?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
shape (circle)
```

super.describe() calls the parent's version. The result is concatenated in the subclass.

**Key insight:** super.method() calls the parent class implementation — enabling extension rather than full replacement.

</details>

---

### 72. Object.create sets prototype directly

**Difficulty:** Medium. **Topic:** Prototypes & Inheritance.

What does this print?

```javascript
const proto = {
  greet() { return 'Hello, ' + this.name; }
};
const obj = Object.create(proto);
obj.name = 'Alice';
console.log(obj.greet());
console.log(Object.getPrototypeOf(obj) === proto);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-62-object-create-sets-prototype-directly?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
Hello, Alice
true
```

Object.create(proto) creates a new object with proto as its prototype. greet() is found via chain.

**Key insight:** Object.create(proto) is the direct way to set up prototype inheritance without classes.

</details>

---

### 73. Own property shadows prototype property

**Difficulty:** Medium. **Topic:** Prototypes & Inheritance.

What does this print?

```javascript
const proto = { x: 1, y: 2 };
const obj = Object.create(proto);
obj.x = 10;
console.log(obj.x);
console.log(obj.y);
console.log(obj.hasOwnProperty('y'));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-63-own-property-shadows-prototype-property?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
10
2
false
```

obj.x=10 shadows proto.x=1. y is not on obj, found on proto. hasOwnProperty("y") is false.

**Key insight:** Own properties shadow prototype properties. The prototype is consulted only for missing own properties.

</details>

---

### 74. Prototype changes visible to existing instances

**Difficulty:** Hard. **Topic:** Prototypes & Inheritance.

What does this print?

```javascript
function Vehicle(type) { this.type = type; }
const car = new Vehicle('car');
Vehicle.prototype.describe = function() {
  return 'I am a ' + this.type;
};
console.log(car.describe());
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-64-prototype-changes-visible-to-existing-in?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
I am a car
```

Adding to the prototype AFTER creating instances still works — prototype lookup is dynamic, not a snapshot at creation time.

**Key insight:** Prototype changes are immediately reflected in all existing instances — the chain is looked up dynamically at call time.

</details>

---

### 75. Constructor property points back to the class

**Difficulty:** Hard. **Topic:** Prototypes & Inheritance.

What does this print?

```javascript
class Point {
  constructor(x, y) { this.x = x; this.y = y; }
}
const p = new Point(1, 2);
console.log(p.constructor === Point);
console.log(p.constructor === Object);
console.log(p instanceof Point);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-65-constructor-property-points-back-to-the-?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
true
false
true
```

p.constructor is inherited from Point.prototype and points to Point. It is not Object.

**Key insight:** constructor property is inherited from the class prototype — it's useful for identifying an object's constructor.

</details>

---

## Array Methods

### 76. map transforms each element

**Difficulty:** Easy. **Topic:** Array Methods.

What does this print?

```javascript
const nums = [1, 2, 3, 4, 5];
console.log(nums.map(n => n * n).join(','));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-66-map-transforms-each-element?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
1,4,9,16,25
```

map creates a new array by applying the callback to each element.

**Key insight:** map always returns a new array of the same length. It transforms without mutating.

</details>

---

### 77. filter then map chain

**Difficulty:** Medium. **Topic:** Array Methods.

What does this print?

```javascript
const result = [1, 2, 3, 4, 5, 6]
  .filter(n => n % 2 === 0)
  .map(n => n * 3);
console.log(result.join(','));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-67-filter-then-map-chain?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
6,12,18
```

filter keeps evens [2,4,6], map multiplies each by 3.

**Key insight:** Array methods chain fluently — each returns a new array for the next method.

</details>

---

### 78. reduce accumulates to a single value

**Difficulty:** Medium. **Topic:** Array Methods.

What does this print?

```javascript
const items = ['hello', 'world', 'foo'];
const total = items.reduce((acc, w) => acc + w.length, 0);
console.log(total);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-68-reduce-accumulates-to-a-single-value?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
13
```

5+5+3=13. reduce folds the array into one value.

**Key insight:** reduce is the most powerful array method — it can produce any output type.

</details>

---

### 79. find returns first match; filter returns all

**Difficulty:** Medium. **Topic:** Array Methods.

What does this print?

```javascript
const users = [
  { id: 1, name: 'Alice' },
  { id: 2, name: 'Bob' },
  { id: 3, name: 'Alice' },
];
const first = users.find(u => u.name === 'Alice');
const all   = users.filter(u => u.name === 'Alice');
console.log(first.id);
console.log(all.length);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-69-find-returns-first-match-filter-returns-?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
1
2
```

find returns the first Alice (id=1). filter returns all Alices — 2 results.

**Key insight:** find = first match (or undefined). filter = all matches (always an array).

</details>

---

### 80. reduce to build a frequency object

**Difficulty:** Medium. **Topic:** Array Methods.

What does this print?

```javascript
const chars = ['a', 'b', 'a', 'c', 'b', 'a'];
const counts = chars.reduce((acc, c) => {
  acc[c] = (acc[c] || 0) + 1;
  return acc;
}, {});
console.log(counts.a);
console.log(counts.b);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-70-reduce-to-build-a-frequency-object?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
3
2
```

reduce builds a frequency map. a=3, b=2.

**Key insight:** reduce can build any shape — objects, maps, nested structures, not just numbers.

</details>

---

### 81. some and every — one fail vs all pass

**Difficulty:** Easy. **Topic:** Array Methods.

What does this print?

```javascript
const nums = [2, 4, 6, 7, 8];
console.log(nums.every(n => n % 2 === 0));
console.log(nums.some(n => n % 2 !== 0));
console.log(nums.every(n => n > 0));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-71-some-and-every-one-fail-vs-all-pass?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
false
true
true
```

every: 7 fails even check. some: 7 is odd (at least one). every positive: all > 0.

**Key insight:** every = all must pass. some = at least one must pass.

</details>

---

### 82. flatMap — map then flatten one level

**Difficulty:** Medium. **Topic:** Array Methods.

What does this print?

```javascript
const sentences = ['hello world', 'foo bar'];
const words = sentences.flatMap(s => s.split(' '));
console.log(words.length);
console.log(words[1]);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-72-flatmap-map-then-flatten-one-level?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
4
world
```

flatMap maps to arrays then flattens one level. ["hello","world"] + ["foo","bar"] → 4 words.

**Key insight:** flatMap = map + flat(1). Perfect when each element produces multiple values.

</details>

---

### 83. Default sort is lexicographic — not numeric

**Difficulty:** Medium. **Topic:** Array Methods.

What does this print?

```javascript
const nums = [10, 9, 2, 1, 100];
console.log([...nums].sort().join(','));
console.log([...nums].sort((a, b) => a - b).join(','));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-73-default-sort-is-lexicographic-not-numeri?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
1,10,100,2,9
1,2,9,10,100
```

Default sort converts to strings: "10" < "2" because "1"<"2". With comparator (a-b): numeric sort works.

**Key insight:** Default Array.sort() is lexicographic. Always pass (a,b) => a-b for numeric sorting.

</details>

---

### 84. Array destructuring with skip and rest

**Difficulty:** Medium. **Topic:** Array Methods.

What does this print?

```javascript
const [first, , third, ...rest] = [1, 2, 3, 4, 5];
console.log(first);
console.log(third);
console.log(rest.join(','));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-74-array-destructuring-with-skip-and-rest?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
1
3
4,5
```

first=1, second skipped, third=3, rest=[4,5].

**Key insight:** Destructuring supports skipping (empty comma) and rest to collect remaining elements.

</details>

---

### 85. Spread copy before sort preserves original

**Difficulty:** Medium. **Topic:** Array Methods.

What does this print?

```javascript
const original = [3, 1, 2];
const sorted = [...original].sort();
console.log(original.join(','));
console.log(sorted.join(','));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-75-spread-copy-before-sort-preserves-origin?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
3,1,2
1,2,3
```

[...original] creates an independent copy. sort() mutates sorted, not original.

**Key insight:** sort() mutates in place. Always spread first to avoid mutating the source.

</details>

---

### 86. reduce to group and aggregate

**Difficulty:** Hard. **Topic:** Array Methods.

What does this print?

```javascript
const orders = [
  { cat: 'A', val: 10 },
  { cat: 'B', val: 20 },
  { cat: 'A', val: 30 },
];
const totals = orders.reduce((acc, o) => {
  acc[o.cat] = (acc[o.cat] || 0) + o.val;
  return acc;
}, {});
console.log(totals.A);
console.log(totals.B);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-96-reduce-to-group-and-aggregate?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
40
20
```

reduce builds a totals map. A: 10+30=40. B: 20.

**Key insight:** reduce is ideal for grouping and aggregating — a single pass over an array to build any shape of output.

</details>

---

## Modern JS

### 87. Destructuring defaults apply only for undefined

**Difficulty:** Easy. **Topic:** Modern JS.

What does this print?

```javascript
const { a = 1, b = 2, c = 3 } = { a: 10, b: null };
console.log(a);
console.log(b);
console.log(c);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-76-destructuring-defaults-apply-only-for-un?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
10
null
3
```

a=10 (provided). b=null — null is not undefined, so default 2 does not apply. c missing → default 3.

**Key insight:** Destructuring defaults only trigger on undefined. null, 0, "" all suppress the default.

</details>

---

### 88. Object spread — later properties win

**Difficulty:** Easy. **Topic:** Modern JS.

What does this print?

```javascript
const base     = { x: 1, y: 2 };
const override = { y: 20, z: 3 };
const merged   = { ...base, ...override };
console.log(merged.x);
console.log(merged.y);
console.log(merged.z);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-77-object-spread-later-properties-win?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
1
20
3
```

override.y=20 overwrites base.y=2. x and z are unique to their source.

**Key insight:** In object spread, later properties override earlier ones. Order matters.

</details>

---

### 89. Optional chaining prevents TypeError on missing properties

**Difficulty:** Medium. **Topic:** Modern JS.

What does this print?

```javascript
const user = { profile: { city: 'NY' } };
console.log(user?.profile?.city);
console.log(user?.settings?.theme);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-78-optional-chaining-prevents-typeerror-on-?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
NY
undefined
```

user?.profile?.city: chain exists, returns "NY". user?.settings returns undefined, ?.theme short-circuits to undefined — no TypeError.

**Key insight:** ?. returns undefined instead of throwing when a link in the chain is null/undefined.

</details>

---

### 90. Nullish coalescing ?? vs OR ||

**Difficulty:** Medium. **Topic:** Modern JS.

What does this print?

```javascript
const config = { timeout: 0, debug: null };
console.log(config.timeout  ?? 1000);
console.log(config.timeout  || 1000);
console.log(config.debug    ?? true);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-79-nullish-coalescing-vs-or-?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
0
1000
true
```

timeout=0: ?? keeps 0 (not null/undefined). || treats 0 as falsy → 1000. debug=null: ?? triggers → true.

**Key insight:** ?? only triggers on null/undefined. || triggers on any falsy value including 0.

</details>

---

### 91. Template literal expressions

**Difficulty:** Easy. **Topic:** Modern JS.

What does this print?

```javascript
const a = 5, b = 10;
console.log(`${a} + ${b} = ${a + b}`);
console.log(`${a > b ? 'a wins' : 'b wins'}`);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-80-template-literal-expressions?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
5 + 10 = 15
b wins
```

Template literals evaluate any expression inside ${}. a+b=15. a>b is false → "b wins".

**Key insight:** Template literals evaluate any JS expression inside ${} — not just variables.

</details>

---

### 92. Computed property names with expressions

**Difficulty:** Medium. **Topic:** Modern JS.

What does this print?

```javascript
const key = 'name';
const obj = {
  [key]: 'Alice',
  [`get${key[0].toUpperCase() + key.slice(1)}`]() {
    return this[key];
  }
};
console.log(obj.name);
console.log(obj.getName());
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-81-computed-property-names-with-expressions?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
Alice
Alice
```

[key] = "name". The computed method name evaluates to "getName".

**Key insight:** Computed property names [expr] let you use dynamic keys and method names in object literals.

</details>

---

### 93. Symbol keys hidden from Object.keys

**Difficulty:** Medium. **Topic:** Modern JS.

What does this print?

```javascript
const id = Symbol('id');
const user = { name: 'Alice', [id]: 42 };
console.log(user.name);
console.log(user[id]);
console.log(Object.keys(user).length);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-82-symbol-keys-hidden-from-object-keys?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
Alice
42
1
```

Symbol keys must be accessed directly with [id]. Object.keys only returns string keys — length is 1.

**Key insight:** Symbol keys are non-enumerable by default — hidden from Object.keys, for...in, and JSON.stringify.

</details>

---

### 94. Getter and setter intercept access

**Difficulty:** Medium. **Topic:** Modern JS.

What does this print?

```javascript
const obj = {
  _name: 'alice',
  get name()  { return this._name.toUpperCase(); },
  set name(v) { this._name = v.trim().toLowerCase(); }
};
console.log(obj.name);
obj.name = '  Bob  ';
console.log(obj.name);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-83-getter-and-setter-intercept-access?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
ALICE
BOB
```

Getter transforms to uppercase. Setter trims and lowercases. Both used with property syntax.

**Key insight:** Getters/setters intercept property access and assignment. They look like properties but run code.

</details>

---

### 95. Logical assignment operators

**Difficulty:** Medium. **Topic:** Modern JS.

What does this print?

```javascript
let a = null;
let b = 0;
let c = 'existing';
a ??= 'default';
b ||= 'fallback';
c &&= 'updated';
console.log(a);
console.log(b);
console.log(c);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-84-logical-assignment-operators?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
default
fallback
updated
```

a??=: null, so assign. b||=: 0 is falsy, so assign. c&&=: truthy, so assign.

**Key insight:** ??= assigns only if null/undefined. ||= if falsy. &&= only if truthy. They short-circuit.

</details>

---

### 96. Object.freeze prevents mutation silently

**Difficulty:** Medium. **Topic:** Modern JS.

What does this print?

```javascript
const obj = Object.freeze({ x: 1, y: 2 });
obj.x = 99;
obj.z = 3;
console.log(obj.x);
console.log(obj.z);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-85-object-freeze-prevents-mutation-silently?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
1
undefined
```

Object.freeze makes all properties non-writable. Assignments silently fail in non-strict mode.

**Key insight:** Object.freeze: mutations silently fail (non-strict mode). Frozen objects cannot be changed.

</details>

---

### 97. Map accepts any key type

**Difficulty:** Medium. **Topic:** Modern JS.

What does this print?

```javascript
const map = new Map();
map.set('name', 'Alice');
map.set(42, 'answer');
map.set(true, 'yes');
console.log(map.size);
console.log(map.get(42));
console.log(map.has('missing'));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-86-map-accepts-any-key-type?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
3
answer
false
```

Map keys can be any type. size=3. get(42) retrieves by number key. has("missing") is false.

**Key insight:** Map accepts any value as key. Object keys are always coerced to strings.

</details>

---

### 98. Set deduplicates and preserves insertion order

**Difficulty:** Easy. **Topic:** Modern JS.

What does this print?

```javascript
const set = new Set([1, 2, 3, 2, 1, 3, 4]);
console.log(set.size);
set.add(5); set.add(1);
console.log(set.size);
console.log([...set][0]);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-87-set-deduplicates-and-preserves-insertion?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
4
5
1
```

Duplicates removed: {1,2,3,4}=size 4. add(5)→5. add(1) already exists, no change. First element=1.

**Key insight:** Set stores unique values in insertion order. Adding a duplicate is silently ignored.

</details>

---

### 99. Promise.allSettled handles mixed results

**Difficulty:** Hard. **Topic:** Modern JS.

What does this print?

```javascript
Promise.allSettled([
  Promise.resolve(1),
  Promise.reject('err'),
  Promise.resolve(3),
]).then(results => {
  console.log(results[0].status);
  console.log(results[1].status);
  console.log(results[1].reason);
});
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-94-promise-allsettled-handles-mixed-results?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
fulfilled
rejected
err
```

allSettled never rejects. Each result has status + value (fulfilled) or reason (rejected).

**Key insight:** Promise.allSettled waits for ALL regardless of outcome. Promise.all fails fast on first rejection.

</details>

---

### 100. Proxy intercepts property access

**Difficulty:** Medium. **Topic:** Modern JS.

What does this print?

```javascript
const handler = {
  get(target, prop) {
    return prop in target ? target[prop] : prop + ' not found';
  }
};
const obj = new Proxy({ x: 1, y: 2 }, handler);
console.log(obj.x);
console.log(obj.z);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-95-proxy-intercepts-property-access?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
1
z not found
```

The get trap intercepts ALL property access. x exists → returns 1. z missing → returns "z not found".

**Key insight:** Proxy get trap intercepts all property reads. Use it for default values, validation, or virtual properties.

</details>

---

### 101. Array.from with mapping function as range factory

**Difficulty:** Medium. **Topic:** Modern JS.

What does this print?

```javascript
const zeros = Array.from({ length: 3 }, () => 0);
const range = Array.from({ length: 5 }, (_, i) => i + 1);
console.log(zeros.join(','));
console.log(range.join(','));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-97-array-from-with-mapping-function-as-rang?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
0,0,0
1,2,3,4,5
```

Array.from with a length property creates an array. The second argument maps each element — _ is the value (undefined), i is the index.

**Key insight:** Array.from({length:n}, (_, i) => expr) is the idiomatic way to create filled arrays of specific length.

</details>

---

## Generators & Iterators

### 102. Generator pauses at yield, done true at end

**Difficulty:** Medium. **Topic:** Generators & Iterators.

What does this print?

```javascript
function* gen() {
  yield 1;
  yield 2;
}
const g = gen();
console.log(g.next().value);
console.log(g.next().value);
console.log(g.next().done);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-88-generator-pauses-at-yield-done-true-at-e?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
1
2
true
```

First next(): value=1, done=false. Second: value=2, done=false. Third: function ends, done=true.

**Key insight:** Generators pause at yield. When the function completes, done becomes true.

</details>

---

### 103. return in generator — spread ignores return value

**Difficulty:** Medium. **Topic:** Generators & Iterators.

What does this print?

```javascript
function* gen() {
  yield 1;
  return 99;
  yield 2;
}
const values = [...gen()];
console.log(values.length);
console.log(values.join(','));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-89-return-in-generator-spread-ignores-retur?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
1
1
```

Spread stops at done:true. return gives {value:99, done:true} — spread ignores it. yield 2 unreachable.

**Key insight:** return in a generator terminates it. Spread/for...of ignores the return value — only yields are collected.

</details>

---

### 104. yield* delegates to another iterable

**Difficulty:** Medium. **Topic:** Generators & Iterators.

What does this print?

```javascript
function* inner() { yield 'a'; yield 'b'; }
function* outer() {
  yield 1;
  yield* inner();
  yield 2;
}
console.log([...outer()].join(','));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-90-yield-delegates-to-another-iterable?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
1,a,b,2
```

yield* delegates to inner(). outer yields 1, then all of inner (a, b), then 2.

**Key insight:** yield* delegates iteration to any iterable — another generator, array, string, etc.

</details>

---

### 105. Generator as lazy range

**Difficulty:** Easy. **Topic:** Generators & Iterators.

What does this print?

```javascript
function* range(start, end) {
  for (let i = start; i <= end; i++) yield i;
}
const sum = [...range(1, 5)].reduce((a, b) => a + b, 0);
console.log(sum);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-91-generator-as-lazy-range?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
15
```

range(1,5) yields 1,2,3,4,5. Spread collects them. reduce sums: 15.

**Key insight:** Generators produce values lazily — perfect for ranges, sequences, and infinite streams.

</details>

---

### 106. Custom iterable with Symbol.iterator

**Difficulty:** Medium. **Topic:** Generators & Iterators.

What does this print?

```javascript
const range = {
  from: 1, to: 3,
  [Symbol.iterator]() {
    let cur = this.from;
    const last = this.to;
    return {
      next() {
        return cur <= last
          ? { value: cur++, done: false }
          : { value: undefined, done: true };
      }
    };
  }
};
console.log([...range].join(','));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-92-custom-iterable-with-symbol-iterator?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
1,2,3
```

[Symbol.iterator]() returns a fresh iterator. Spread calls it and collects until done.

**Key insight:** Any object with [Symbol.iterator]() is iterable. It must return an object with a next() method.

</details>

---

### 107. next() argument becomes the value of the previous yield

**Difficulty:** Hard. **Topic:** Generators & Iterators.

What does this print?

```javascript
function* dialog() {
  const name = yield 'What is your name?';
  yield 'Hello, ' + name;
}
const g = dialog();
console.log(g.next().value);
console.log(g.next('Alice').value);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-93-next-argument-becomes-the-value-of-the-p?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
What is your name?
Hello, Alice
```

First next() starts the generator, returns "What is your name?". next("Alice") resumes — "Alice" becomes the result of the yield expression.

**Key insight:** next(val) injects val as the resolved value of the previous yield. The first next() argument is always ignored.

</details>

---

## Misc Quirks

### 108. Primitive vs Reference Type Variable Mutation

**Difficulty:** Hard. **Topic:** Misc Quirks.

What does this print?

```javascript
function change(obj) { obj = { age: 30 }; } 
let person = { name: 'John', age: 25 }; 
change(person); 
console.log(person.name); 
console.log(person.age);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-primitive-vs-reference-type-variable-mutation?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
John
25
```

The function change(obj) takes an object as a parameter and reassigns it to a new object. However, this reassignment does not affect the original object that was passed to the function. In JavaScript, when an object is passed to a function, it is passed by reference, but reassigning the parameter does not change the original reference. So, when we log person.name and person.age, we still get the original values.

**Key insight:** The difference between reassigning a variable and mutating an object

</details>

---

### 109. Comma Operator Evaluation

**Difficulty:** Hard. **Topic:** Misc Quirks.

What does this print?

```javascript
let a = (1 + 2, 3 + 4);
console.log(a);
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-comma-operator-evaluation?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
7
```

The comma operator evaluates each expression from left to right and returns the value of the rightmost expression. In this case, (1 + 2) evaluates to 3 and (3 + 4) evaluates to 7. Since the comma operator returns the value of the rightmost expression, the value of 'a' is 7.

**Key insight:** The comma operator's behavior and its use in expression evaluation

</details>

---

### 110. Pass by Value and Object Mutation

**Difficulty:** Hard. **Topic:** Misc Quirks.

What does this print?

```javascript
function change(obj) { obj = { age: 30 }; }
let person = { name: 'John', age: 25 };
console.log('Before:', JSON.stringify(person));
change(person);
console.log('After:', JSON.stringify(person));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-pass-by-value-and-object-mutation?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
Before: {"name":"John","age":25}
After: {"name":"John","age":25}
```

When the function change is called with the person object, a copy of the reference to the person object is passed to the function. Inside the function, a new object is assigned to the local variable obj, which does not affect the original person object. So, the person object remains unchanged.

**Key insight:** Pass by value for primitive types and pass by reference for objects, but reassigning an object variable only changes the local reference

</details>

---

## Type Conversion

### 111. Implicit Numeric Conversion

**Difficulty:** Hard. **Topic:** Type Conversion.

What does this print?

```javascript
console.log(Number(undefined));
console.log(Number(null));
console.log(Number(true));
console.log(Number(false));
console.log(Number('   123   '));
console.log(Number('123abc'));
console.log(Number('   '));
```

**[Predict the output and check it on JSPrep Pro](https://jsprep.pro/q/output-implicit-numeric-conversion?utm_source=github&utm_medium=oss&utm_campaign=output-questions)**

<details>
<summary>Show the answer</summary>

Output:

```
NaN
0
1
0
123
NaN
0
```

The code demonstrates implicit numeric conversion using the Number() function. The conversion rules are as follows: undefined becomes NaN, null becomes 0, true becomes 1, false becomes 0. For strings, leading and trailing whitespaces are removed. If the resulting string is empty, the result is 0. If the string can be parsed as a number, the number is 'read' from the string. Otherwise, the result is NaN. Therefore, Number(undefined) is NaN, Number(null) is 0, Number(true) is 1, Number(false) is 0, Number('   123   ') is 123, Number('123abc') is NaN because '123abc' cannot be parsed as a number, and Number('   ') is 0 because the string is empty after removing whitespaces.

**Key insight:** Numeric conversion rules in JavaScript

</details>

---

## More practice

- [Interview Sprint](https://jsprep.pro/sprint?utm_source=github&utm_medium=oss&utm_campaign=output-questions): a timed mix of theory, output and debugging questions, with a shareable result.
- [Debug Lab](https://jsprep.pro/debug-lab?utm_source=github&utm_medium=oss&utm_campaign=output-questions): find and fix the bug in real code.
- [Polyfill Lab](https://jsprep.pro/polyfill-lab?utm_source=github&utm_medium=oss&utm_campaign=output-questions): write `Promise.all`, `bind`, `debounce` and more, with tests that run in your browser.

## Found a mistake?

[Open an issue](../../issues) with the question number and what's wrong. Questions are maintained on [jsprep.pro](https://jsprep.pro?utm_source=github&utm_medium=oss&utm_campaign=output-questions), and this list is regenerated from there, so fixes land in both.

## License

The questions and explanations are licensed under [CC BY-NC-SA 4.0](LICENSE.md). You can share and adapt them for non-commercial use, with credit and a link to [jsprep.pro](https://jsprep.pro).
