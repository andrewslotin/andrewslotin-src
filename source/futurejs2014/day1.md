FutureJS
========

_May 2, 2014, Barcelona, Spain_

1. [Invent the future, don't recereate the past](#1)
2. [A language in 20 minutes](#2)
3. [Everybody dance now with web audio API](#3)
4. [Five keys to success when building HTML5 games](#4)
5. [A conceptual future for the multi-device web](#5)
6. [Beyond the bar graph: creative visualization with d3.js](#6)
7. [Immutability, interactivity & Javascript](#7)
8. [Lightning Talks]()
  * [...](#8.1)
  * [Streaming Bittorrent in Javascript in real-time](#8.2)
  * [MongoJS](#8.3)
  * [Minimalist software engineers](#8.4)
  * [GIFs ae awesome!](#8.5)
  * [Open web apps](#8.6)

<a name="1"></a>
Invent the Future, Don't Recereate the Past
-------------------------------------------
_[Reginald Braithwaite](https://github.com/raganwald) — JavaScript Allongé author_

_Inspiring and motivating talk._

<a name="2"></a>
A Language in 20 Minutes
------------------------
_[James Coglan](https://jcoglan.com/) — Faye, author of JavaScript Testing Recipes_

Watch screencast ↓

<iframe src="//player.vimeo.com/video/92661664" width="656" height="370" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

Check the code → [jcoglan/3s](https://github.com/jcoglan/3s)

<a name="3"></a>
Everybody Dance Now With Web Audio API
--------------------------------------
_[Jaume Sánchez](https://github.com/spite)_


```
  var context = new AudioContext(); // one per page, might be prefixed (Chrome, Safari)
```

* **Nodes** are modules performing FX, filters, etc.
  * `GainNode` to control the volume
  * `DelayNode` to add delay
  * Delay + Gain = Reverb
* **Sources** to generate input
  * Oscillator: `context.createOscillator() // OscillatorNode`
  * `context.getUserData` to get the sound from mic.
* **Destination** — node or the mixer

`AudioContext#createAnalyser` creates an FFT analyzer.

`AudioParam` to set a float value with easings.

Links:

* [AudioContext Docs at Mozilla Developer Network](https://developer.mozilla.org/en-US/docs/Web/API/AudioContext)
* [Getting Started with Web Audio API](http://www.html5rocks.com/en/tutorials/webaudio/intro/)

<a name="4"></a>
Five Keys to Success When Building HTML5 Games
----------------------------------------------
_[Jesse Freeman](https://github.com/jessefreeman)_

1. Create, refine, iterate.
2. Design for mobile first. Fit into 320 x 420 for mobile.
3. ...
4. Analyze your game.
5. Release ASAP.

<a name="5"></a>
A Conceptual Future for the Multi-device Web
--------------------------------------------
_[Ben Foxall](http://benjaminbenben.com/)_

* [peerjs](http://peerjs.com/) — WebRTC
* [binaryjs](http://binaryjs.com/) — large file transfers over websocket connection
* [three.js](http://threejs.org/) — nice d3 graphs transitions

### Transports

* Polling
* Long polling
* Websockets
* WebRTC

<a name="6"></a>
Beyond the Bar Graph: Creative Visualization With d3.js
-------------------------------------------------------
_[Joanne Cheng](https://github.com/joannecheng) — Web developer at Thoughtbot_

```
d3d3d3d3d3d3d3 \o/
taka-taka-taka \o/
```

<a name="7"></a>
Immutability, Interactivity & Javascript
----------------------------------------
_[David Nolen](https://github.com/swannodette) — ClojureScript main contributor_

* Function Reactive Programming (FRP)
* Rx
* Communicating Sequential Processes (CSP)

### Bitmapped Vector Trie

* data lives in leaves
* e.g. prefix tree

### Persistent Vector

* B-tree
* Copy-on-write

[React](http://facebook.github.io/react/) — a UI framework from Facebook

[Om](https://github.com/swannodette/om) — ClojureScript library on top of React

[mori](http://swannodette.github.io/mori/) — ClojureScript's persistent data structures in valilla Javascript.

<a name="8"></a>
Lightning Talks
---------------

<a name="8.1"></a>
### _&lt;Missed it&gt;_

> I'm doing another implementation of Type/Clojure/Coffee/YounameitScript

<a name="8.2"></a>
### Streaming Bittorrent in Javascript in Realtime

[torrent-stream](https://github.com/mafintosh/torrent-stream)

<a name="8.3"></a>
### MongoJS

> This is how you use a MongoJS REPL in your node.js app.

<a name="8.4"></a>
### Minimalist Software Engineers

> Be a minimalist. KISS, YAGNI, DRY — all these abbreviations.

<a name="8.5"></a>
### GIFs Are Awesome!

> Streaming GIFs!

Pure evil!

<a name="8.6"></a>
### Open Web Apps

[MDN App Center](https://developer.mozilla.org/en/Apps)
