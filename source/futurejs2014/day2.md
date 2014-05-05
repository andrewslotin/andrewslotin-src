FutureJS
========

_May 3rd, 2014, Barcelona, Spain_

1. [JS.Next and Coffeescript](#1)
2. [The Javascript of tomorrow, today](#2)
3. [How to build front-end web apps that scale](#3)
4. [Streaming and event-based programming using FRP and RxJS](#4)
5. [Web components — a whirlwind tour thru the future of the Web](#5)
6. [My girlfriend is a crypto-anarchist and she doesn't even know it](#6)
7. [The secrets of React's virtual DOM](#7)
8. [Terra.js — a semantic terrain engine](#8)

<a name="1"></a>
JS.Next and Coffeescript
------------------------
_[Jeremy Ashkenas](https://github.com/jashkenas) — creator of CoffeeScript_

> Programming is awesome, yay!

<a name="2"></a>
The Javascript of Tomorrow, Today
---------------------------------
_[Patrick Dubroy](https://github.com/pdubroy) — Chrome team_

Coming in ES6 (Harmony):

* Classes and modules
* Arrow functions
* Destructuring assignment
* Generators and iterators

* Promises
* Map, WeakMap and Set
* New Math functions

_Shortly: ES6 basically is Coffeescript._

[Traceur](https://github.com/google/traceur-compiler) compiles Javascript.js to the Javascript-of-today.

More on Generators:

* [Task.js](http://taskjs.org): Beautiful concurrency for Javascript
* [Regenerator](https://facebook.github.io/regenerator)

<a name="3"></a>
How to Build Front-end Web Apps That Scale
------------------------------------------
_[Phil Leggetter](https://github.com/leggetter)_

What makes front-end apps so complicated:

* Large Single Page Applications (SPAs)
* Complex functionality
* Complex iteractions
  * User
  * Technology
* Opened for the whole day

The Solutions:

1. Components/Widgest/modules

   Group assets by feature:

   ```
   assets/stylesheets     assets/my-widget/*.css
   assets/javascripts  →  assets/my-widget/*.js
   ...                    ...
   ```

2. A Services Layer

   > Services are a Contract/Protocol/Interface being used for to access shared resources.

   * Features should not directly communicate
   * Easily change implementation
   * Mocks/Stubs

3. MV*

   Decouple the view and exclude it from testing.
4. Efficient testing — quick and reliable
5. Tools

   * Define workflow & promote consistency
   * Scaffolding
   * Dev Server
   * Builds/Bundling
   * Dependency Analysis


<a name="4"></a>
Streaming and Event-based Programming Using FRP and RxJS
--------------------------------------------------------
_[Matthew Podwysocki](https://github.com/mattpodwysocki)_

* Push-based code with `Observers`

  Don't do this:

  ```javascript
  el.addEventListener('click', function () {
    try {
      // do stuff
    } catch (e) {
      recoverOrExit(e)
    }

    doCleanup()
  });
  ```

  Do that instead:

  ```javascript
  var collection = Observable.fromEvent(e, 'click');
  var observer   = Observer.create(
                     OnNext:   nextHandler,
                     OnCreate: createHandler,
                     OnError:  errorHandler
                   )

  subscription = observer.subscribe(collection);
  subscription.dispose(); // do cleanup
  ```
* Immutable data.
* Compose events and promises.
* Due to non-deterministic nature, testing more focused on results rather than steps.

More on [Reactive Streams](http://www.reactive-streams.org).

<a name="5"></a>
Web Components — a Whirlwind Tour Thru the Future of the Web
------------------------------------------------------------
_[Martin Naumann](https://github.com/martin-naumann)_

Web components standards:

* Template element

  E.g.

  ```html
  <!-- Template -->
  <template>
    <h1 select=".title"></h1>
    <p select=".body"></p>
  </template>

  <!-- This is how you add previously registered component "my-component" -->
  <my-component>
    <p class="title">Goes to &lt;h1&gt;</p>
    <p class="body">Goes to &lt;p&gt;</p>
    <p>Won't be rendered!</p>
  </my-component>
  ```

* Shadow DOM

  A DOM tree that is "disconnected" from the `document`'s DOM.

  ```javascript
  var container = document.createElement('div');
  var root      = container.createShadowRoot(); // ← behold!
  ```

  **HOW COOL IS THAT???**

* Custom elements

  ```javascript
  var elemPrototype = Object.create(HTMLElement.prototype);
  elemPrototype.createdCallback = ...; // Will be called on element create

  // The name should be unique within the page
  var XHeadline = document.registerElement("my-component", { prototype: elemPrototype });
  ```

  And than somewhere in HTML:

  ```html
  <my-component></my-component>
  ```

* HTML imports

**Polyfill & frameworks:**

* [Polymer](http://www.polymer-project.org/)
* [X-Tags](http://www.x-tags.org/)

**Try it:**

* [Brick](http://mozilla.github.io/brick/)
* [CustomElement.io](http://customelements.io/)
* [Mozilla AppMaker](https://apps.webmaker.org/)

<a name="6"></a>
My Girlfriend is a Crypto-anarchist and She Doesn't Even Know It
----------------------------------------------------------------
_[Mauro Pompilio](https://github.com/malditogeek)_

> My girlfriend flashes boobs to me via WebRTC-powered video chat, because I don't want a guy from NSA to stare on them.

<a name="7"></a>
The Secrets of React's Virtual DOM
----------------------------------
_[Pete Hunt](https://github.com/petehunt) — React main contributor_

What makes UIs difficult to build?

* > It just doesn't feel right"
* Even with test driven development you often need a human to verify
* UIs are deceptively complex
* Try to be reliable.
* They should at least break the same way every time.

### How do we make UIs more reliable?

* Data binding is what we do now
* Data binding makes the UI (a dynamic process) look like a static process.
* It's a polyfill for reactive JS in the DOM
* One of the solutions for syncing states is reactive programming.
* JS isn't actually reactive, so we need a non-trivial abstraction. This will be leaky.

### Key value observation

* The most popular way to handle binding today, pretty much every lib except Angular
* Observables & computed properties are what this is built on
* Ember is probably the premier way to implement this style. "If Ember can't make KVO simple, then who can?"
* With templating, you don't have a very expressive toolset to control flow.
* In Ember, you often have to know the implementation in order to do what you want.
* For deep observation, you have to use special notations, units of composition that the system gives you.

### Dirty Checking

* Angular composes pieces of functionality as directives.
* You still have to know the implementation, a domain specific language, essentially.

### Virtual DOM

* One way to do this
* Whenever anything may have changed, re-render everything
* Diff the previous representation with the next one
* Only hit the dom with minimal changes

### Static Analyzing

* Data binding systems are not statically analyzable. They can't lint, type check, minify, etc.
* Vanilla JS is.

### Mobile

* Memory is just as important as CPU. The OS will kill your app if it grows too large in memory
* KVO bookeeping costs CPU and memory
* With a virtual DOM approach, render code is usually very cheap. Few, if any tight loops.
* Your view is usually smaller than your model, and if it's not you don't typically have performance problems.

### Performance Benchmarks

`O(v)` vs. `O(m)` — model size vs view size
`v` is much less than `m` in most real world apps. On a single update, KVO CPU is `O(n)` where n is the number of operations. Virtual DOM is `O(v)`.

### Solving Performance Problems

KVO? Don't use data binding, do it manually.
Virtual DOM? Caching.
This is where shouldUpdate comes in — it allows you to use the already rendered (essentially cached) result instead of re-rendering.

### Simplicity

> If you're going to keep using the performance argument, eventually you're going to lose.

* Performance isn't the Virtual DOM's primary argument, simplicity is.
* It is the most expressive way to write your UI.
* To use something like Firebase in a KVO templated view, you have to create bindings that work with the template language. Not so with a Virtual DOM system - just use it.

<a name="8"></a>
Terra.js — a Semantic Terrain engine
------------------------------------
_[Felix Palmer](https://github.com/felixpalmer)_

### What?

Semantic terrain WebGL rendering engine.

### Why?

Originally motivated by visualizing train stations in Czech Republic. But actually for fun.

### How?

* Data sources
  * Aeral photography
  * Elevation data
  * Land cover data
* Tile loading
* Terrain generation for CPU
   * Primitive geometry in JS and passed to GPU.
   * Vertex shader reads in elevation data and displaces vertices.
   * Complication: level of detail.
* Rendering
  * Deformed terrain -> fragment shader.
  * GPU decides how to render based on land cover data.
