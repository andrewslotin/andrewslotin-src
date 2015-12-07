---
title: 'dotJS 2015 Notes'
---

dotJS 2015
==========

_December 7, 2015, Paris, France_

1. [Modern Async JS](#modern-async-js)
2. [Hyperdrive](#hyperdrive)
3. [Dealing With Garbage](#dealing-with-garbage)
4. [http/2 is here, now let's make it easy](#http-2)
5. [Practical ES6 for the Modern Javascript Tinkerer](#practical-es6)
6. [The Whole Future Declared in a `var`](#var)
7. [Real World WebRTC](#real-world-webrtc)
8. [Templating Engines](#templating-engines)
9. [Teaching Kids to Code](#teaching-kids-to-code)
10. [Pocket-sized JS](#pocket-sized-js)
11. [Web Evolution in Action 2](#web-evolution-in-action-2)

<a name="modern-async-js"></a>
Modern Async JS
---------------
_[Christophe Porteneuve]()_

### Callbacks

* No return values
* Errors are often not properly propagated
* Untrustworthy: there is no guarantee the callback will be executed
* Composition doesn't work well

* Async.js
  - No extra oncepts
  - No abstraction penalty
  - Wide range of use cases
  - Well-tested

### Promises

* State of the art: wide support in modern libraries
* Native in ES6
* Trustworthy: only one callback, only once, guaranteed
* Exceptions!
* Composable: chainable, with injection and casting

However

* New abstraction
* Slight perfomance penalty
* Code-flow is often not intuitive

### Generators

Allows to do lazy eval in JS

* Coroutines
* Composable

But not async :(

### Generators + Promises!

* Code is async, while looking as sync

But:

* This is hacky
* Added weigth
* ...

### async/await

Borrowed from C# .NET 5.0. Currently ES Stage 3, almost certainly ES7, available in Edge.

* Suboptimal for now

<a name="hyperdrive"></a>
Hyperdrive
----------
_[Mathias Buus]()_

Hyperdrive is a p2p file-sharing network.

[Rabin fingerprinting](https://en.wikipedia.org/wiki/Rabin_fingerprint) for chunk deduplication.

<a name="dealing-with-garbage"></a>
Dealing With Garbage
--------------------
_[Samuel Saccone]()_

Momory profiling like a PRO

1. Identify clean action sets
2. Measure what is happening: timeline recording + heap snapshots
3. Analyze results
4. Apply patches
5. Rinse and repeat

Automate this with Drool.

<a name="http-2"></a>
http/2 is here, now let's make it easy
--------------------------------------
_[Rebecca Murphey]()_

* One connection per host
* Server push
* Prioritization
* HTTPS is required by browsers

Server push doesn't really work atm, it gets accepted too late.

<a name="practical-es6"></a>
Practical ES6 for the Modern Javascript Tinkerer
------------------------------------------------
_[Nicolas Bevacqua]()_

* Arrow function
* Destructive assignment
* Rest parameters
* Spread operator
* Template literals
* `let` variables
* Constants
* ... and many more

<a name="var"></a>
The Whole Future Declared in a `var`
------------------------------------
_[Andre Medeiros]()_

Use Observer pattern to bind values instead of evaluating them in place. Reactive programming and so on.

RxJS provides `Observable` type.

<a name="real-world-webrtc"></a>
Real World WebRTC
-----------------
_[Eric Schoffstall]()_

### Camera

```javascript
var opt = {video: true};
navigator.getUserMedia(opt, onSuccess, onError);
```

HTTPS-only!

### Peer-to-peer

```javascript
// Sender
var pc = new PeerConnection();
ps.assStream(/* ... */);
```

Works over:

* TCP -- reliable, ordered, byte-oriented
* UDP -- unreliable, unordered, message-oriented
* SCTP -- reliability and order are configurable, message-oriented

Neither Safari nor IE (surprise!) has no support for WebRTC.

* iosrtc for iOS
* Temasys for IE/Safare

[RTC Everywhere](http://rtc.works) -- cross-platform WebRTC modules.

**Pro Tip:** Mechanical Turk allow one to organize political protest and a movements.

<a name="templating-engines"></a>
Templating Engines
------------------
_[Forbes Lindesay]()_

[Pug]() -- yet another templating engine.

```
article
  h1 Title
  p Body
  include ./content.pug
```

**Pro Tip**: when parsing includes, merge ASTs, not the content.

<a name="Teaching-kids-to-code"></a>
Teaching Kids to Code
---------------------
_[Tim Caswell]()_

<a name="pocket-sized-js"></a>
Pocket-sized JS
---------------
_[Henrik Joreteg]()_

[Slides](https://slides.joreteg.com/dotjs/)

### You can't expect fast connection

_Actually you can't expect connection at all._

Optimize code size, images, don't overuse frameworks.

Soundslice -- a music sheet editor in 94Kb of JS code.

### You can't expect the hardware to be fast

Don't try to fit desktop app into mobile.

* Test on real mobile device
* Pre-render HTML
* Ship less code
  - Don't use templates, complete re-render is cheap enough
  - Don't use observable models (Redux might be an option)
  - Don't need a router, browser does it better
  - Replace `react` with `virtual-dom` if only virtual DOM is needed

<a name="web-evolution-in-action-2"></a>
Web Evolution in Action 2
-------------------------
_[Brendan Eich]()_

The web is messy because of compatability which is growing.

asm.js is a genisis of JS compilation.

WebAssembly
