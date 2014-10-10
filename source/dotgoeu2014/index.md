---
title: 'dotGoEu 2014 Notes'
---

dotGoEu 2014
============

_October 10, 2014, Paris, France_

[Go meetups all over the world](http://go-meetups.appspot.com/)

1. [Five things I love in Go](#1)
2. [Building better packages](#2)
3. [Managing dependencies with godep](#3)
4. [Vegeta: HTTP load testing in Go](#4)
5. [Adoption](#5)
6. [I came for the easy concurrency. I stayed for the easy composition.](#6)
7. [Lightning Talks](#7)
  * [How do I mock in Go](#7.1)
  * [Plan9 Go compiler](#7.2)
  * [A Taxi service written in Go](#7.3)
  * [Why Go is such a great language?](#7.4)
  * [How do you easily opeare 40 nodes remotely?](#7.5)
8. [Functional options for friendly APIs](#8)
9. [Dependencies](#9)
10. [GoDrone. Writing a quadcopter firmwire in Go.](#10)
11. [The State of the Gopher](#11)

<a name="1"></a>
Five things I love in Go
------------------------
_[Andrew Gerrand](http://nf.wh3rd.net/)_

* Simplicity
* Workspaces — `$GOPATH` etc.
* Interfaces
* gofmt — stop the war on spaces :)
* goimports — manages imports section for you.

_Check `go tool rename`_

<a name="2"></a>
Building better packages
------------------------
_[Jeremy Saenz](http://codegangsta.io/)_

Simplicity, flexibility and continuity.

> I want to make sure we're building letter openers, not Swiss Army knives.

Interfaces should be used ~~all~~ most of the time:

* Interfaces — argument values
* Structs — return values

<a name="3"></a>
Managing dependencies with godep
--------------------------------
_[Keith Rarick](http://xph.us/)_

* Vendoring dependencies.
* Monkey patching!

**How to patch a third-party dependency**

1. Fork it.
2. `go get` the original version.
3. Add your fork as a second remote.
4. ...
5. PROFIT!!!

<a name="4"></a>
Vegeta: HTTP load testing in Go
-------------------------------
_[Tomás Senart](http://tsenart.me/)_

**Load testing is not a benchmarking!** It's about predicting the future and see how
your service deals with different amounts of requests.

> How many requests/s can we do **sustainably**.

* Never look at averages. They are hiding the peaks and outlines.
* Look at your higher percentiles.
* Define acceptable behavior.

**Why?**

To avoid the disaster. Namely to make sure that your infrastucture can handle the load.

**Where?**

* ~~http://localhost/~~
* ~~http://staging/~~
* http://production/ — the only way to have meaningful results (including cahing, routing,
  latency, etc.)

Do not DDoS yourself! :-D

**What?**

Isolated API calls.

**How?**

1. Load Generation
  * Constant
  * Dynamic
2. Metrics Collection
  * Blackbox
  * Whitebox
3. Analysis and reporting
  * Manual
  * Automated

[Vegeta](https://github.com/<username>/vegeta) generates load constantly, collects metrics
in a blackbox way and performs the manual analysis.

<a name="5"></a>
Lessons learned from worldwide Go evangelism
--------------------------------------------
_[Francesc Campoy Flores](http://www.campoy.cat/)_

Accents when writing the code — use idiomatic approach, no Java-style getters, Python-style
underscores, etc. The way to improve it is to write Go code.

Interfaces are sets of constraints on cocrete types. Implicit satisfaction: `methods(I)` is
a subset of `methods(T)`.

If the type `S` satisfies interface `I`, then `*S` satisfies it too. The opposite is not
necessary true.

Interface is a tuple `(type, value)`.


```go
var p *Person = nil
var f Stringer = p

fmt.Printf("%v", p == nil) // "true"
fmt.Printf("%v", s == nil) // "false"
```

In this case `f` is `(*Person, nil)` which is not `nil`.

The following example outputs `<nil>`.

```go
func Do() error {
  var err *doError
  // ...

  return nil
}

func main() {
  if err := Do(); err != nil {
    fmt.Printf("%s", err)
  }
}
```

<a name="6"></a>
I came for the easy concurrency. I stayed for the easy composition.
-------------------------------------------------------------------
_[John Graham-Cumming](http://jgc.org/)_

*A story about processing of 2kk entries list of web-sites and getting their NS*

* 18 hours with `xargs` and `dig` one-liner.
* 1.5 hours with 75 LOC in Go and 1000 goroutines.

Go version has been generalized and reused by introducing a `Task` interface and a
factory building a specific type of tasks.

```go
type Task {
  Run() Result
}

type Factory interface {
  Make() Task
}

func doStuff(f Factory) {
  for _, task := f.Make() {
    go task.Run()
  }
}

func main() {
  doStuff(&FactoryImpl{})
}
```

<a name="7"></a>
Lightning Talks
---------------

<a name="7.1"></a>
### How do I mock in Go

_[Gabriel Aszalos](https://twitter.com/gabrielaszalos)_

Pass interfaces and while testing pass a type satisfying them but with (optionally)
empty function bodies.

Check later: [Gomez](https://github.com/gbbr/gomez), [mocks](https://github.com/gbbr/mocks).

<a name="7.2"></a>
### Plan9 Go compiler

_[]()_

The guy works on the Plan9 Go compiler and it's doable. Mapping types is a mess.

<a name="7.3"></a>
### A Taxi service written in Go

_[Matt Heath]()_

NSQ — distributed queue written in Go.

_Re-visit this talk later!_

<a name="7.4"></a>
### Why Go is such a great language?

_[John Cinnamond](https://twitter.com/jcinnamond)_

Alan Key — f great place to start learning the OOP.

Go is OO language. Go is **great** OO language.

* Messaging as a first-class concern.
* Easy interfaces.
* Asyncronous messages using channels.
* Go is pragmatic.
* Go is opinionatd but not dogmatic.

<a name="7.5"></a>
### How do you easily opeare 40 nodes remotely?

_[Anthony Eden](https://twitter.com/)_

~~You could use SSH~~ — tedious.

You can use [Go Remote Control](https://github.com/aetrion/go-remote-control):

* Small Go Service on each node.
* Each node executes only one command (start/restart/etc.).
* Provides HTTPS endpoint.
* Hubot performs HTTPS calls.
* Go invokes scripts on the node.

<a name="8"></a>
Functional options for friendly APIs
------------------------------------

_[Dave Cheney](http://dave.cheney.net/)_

Putting lots of configuration parameters into constructor function is bad and
brittle.

How to solve:

* Function currying and decomposing a huge constructon into set of small
  functions? Enormous number of ways to initialize an object.
* Pass a configuration object? Limited options because of zero values. How to pass a
  default configuration?
* Pass `nil` as a configuration? Then you pass the config object by reference.
* Variadic configuration? You need a special type etc.

  ```go
    func NewServer(addr string, config ...Config) (*Server, error)

    func main() {
      _ := NewServer("localhost")
      _ := NewServer("localhost", Config{})
    }
  ```
* **Solution**: pass functions configuring the server instead of configuration values!

  ```go
    func NewServer(addr string, options ...func(*Server)) (*Server, error)
  ```

  Pro-tip: define such functions in the same package to access private fields.

<a name="9"></a>
Dependencies
------------

_[Blake Mizerany](https://github.com/bmizerany)_

When we do something new, its easy to get overexcited and try to use old ideas, that
are inappropriate.

**Fallacy #1. Go is doing it wrong.**

Go is doing it Go's way.

E.g. instead of complaining about the lack of generics maybe you should step back an
learn a little bit ;-)

**Fallacy #2. Predetermination.**

You should be aware about the dependencies before start writing the code. Better have
a look at stdlib first ;-)

**Fallacy #3. If it's on GitHub, it's ready for production.**

No. You don't pickup random things on the streets and try to eat them. Neither should
you try this with your code.

**Most Go programs don't require external dependencies.**

<a name="10"></a>
GoDrone. Writing a quadcopter firmwire in Go.
---------------------------------------------

_[Felix Geisendörfer](http://felixge.de/)_

[Parrot AR Drone 2.0 firmware in Go](http://www.godrone.io/en/latest/)

<a name="11"></a>
The State of the Gopher
-----------------------

_[Brad Fitzpatrick](https://twitter.com/bradfitz)_

#### The Past

* Weekly than monthly releases, `gofix` to automate the migration.
* 1.0: `go` tool has been introduced. stdlib is stabilized and frozen.
* 1.1: value methods, stop-the-world GC
* 1.2: 6 months release cycle, slice capacity, precise GC almost everywhere.
  > Precise vs. conservative GC:
  >
  > * Conservative GC: if it looks like a pointer — it is a pointer. ARM users are in despair.
  > * Precise GC: GC knows for every addr in memory what it’s supposed to be allowing GC to only
      have to look at pointers.
* 1.3: GOOS=solaris,dragonfly,plan9,nacl, precise GC even on stack.
  > Segmented vs. Contiguous stacks
  >
  > * C++/java one huge stack per thread
  > * Go <= 1.2 multiple discontiguous 4K stacks, bounce between
  > * Go 1.3: one little stack, grow & shrink if needed

#### The Present

* More ports (windows etc.)
* Better docs.
* User groups, meetups, blogging, buiding the community.
* Tools:
  * go/types, gofix + types
  * eg, example-based refactoring
  * gorename, safe global renaming tool
  * goimports + go/types + go/parser = goreturns (lazy "return err")

#### The Future

**1.4:**

* Fully precise GC
* Segmented stacks
* [GOOS=android](http://golang.org/s/go14android)

**1.5:**

* Concurrent GC
* GOOS=ios revival?
* GOARCH=ppc64 (minux)
* GOARCH=amd64 (aram,dfc)
* GOTRACE + Chrome trace viewer

**1.6:**

* More GC work
* Ongoing compiler-in-Go cleanup & optimizations

#### Notable compilers

* [llgo](https://github.com/go-llvm/llgo), Go → LLVM → PNaCl, etc.
* [TARDIS Go](http://tardisgo.github.io/), Go → Haxe → JS
* [GopherJS](https://github.com/gopherjs/gopherjs), Go → JS
