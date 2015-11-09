---
title: 'dotGoEu 2015 Notes'
---

dotGoEu 2015
============

_November 9, 2015, Paris, France_

1. [A case for microservices](#a-case-for-microservices)
2. [Tools for working with Go code](#tools-for-working-with-go-code)
3. [Beyond the Distributed System](#beyond-the-distributed-system)
4. [Functional Go](#functional-go)
5. [Lightning Talks](#lightning-talks)
   * [Gopy](#gopy)
   * [Encrypt all transports](#encrypt-all-transports)
   * [How can I get visibility into my open source projects?](#vossibility)
   * [HTTP/2 support in 1.6](#http-2-support-in-go-1-6)
6. [Go internals](#go-internals)
7. [The other side of Go](#the-other-side-of-go)
8. [A tour of the bleve](#a-tour-of-the-bleve)
9. [Living with C](#living-with-c)
10. [Concurrency in Go is not as easy as it sounds](#concurrency-in-go-is-not-as-easy-as-it-sounds)
11. [The Docker trail](#the-docker-trail)
12. [Simplicity is complicated](#simplicity-is-complicated)

<a name="a-case-for-microservices"></a>
A case for microservices
------------------------
_[Peter Bourgon](http://peter.bourgon.org)_

Problems:

* Distribution -- network overhead, etc.
* Perfomance -- a single slow part slows down the whole system
* Architectural -- code duplication, multiple languages
* Testing -- acceptance testing is hard
* Operations

Go:

* Lacks all those nifty features (generics, blah-blah-blah)
* Tooling is awesome
* GC eliminates memory errors

With all its disadvantages microservices architecture has a virtue: The Bounded Context.

Code is sustainable as long as you're able change what you ought to change at any time.

Code _is_ mutable. Great tooling is needed to maintain the code sustainability and maintainability.

[Go kit](http://gokit.io) -- a set of tools for building and maitaining microservices.

<a name="tools-for-working-with-go-code"></a>
Tools for working with Go code
------------------------------
_[Fatih Arslan](http://arslan.io)_

> Go was designed to make tools easy to write
_Rob Pike_

### Formatting

* `gofmt`
* `goimports` -- `gofmt` + maintaining imports section

### Navigation & insight

* `godef` -- prints the location of the symbol referred to
```
godef -f main.go fmt.Println
```
* `godepgraph` -- dependency graph
* `oracle` -- a tool for answering questions about Go source code
  Freevars mode for method extraction
```
oracle -pos=main.go:#214 mode <pkgs>
```

### Refactoring

* `gorename`
* `gomvpkg` -- moves packages, updates references
* `fiximports`

### Code generation

* `stringer`
* `jsonenums`
* `impl` -- generates method stubs for implementing an interface

### Linters and checkers

* `gometalinter` -- `govet` + `errcheck` + `golint`

### Testing

* `benchcmp` -- compare benchmark results

### Dependency management

* `/vendor` folder

<a name="beyond-the-distributed-system"></a>
Beyond the Distributed System
-----------------------------
_[Verónica López](https://about.me/verolop)_

### The reasons why developers are choos choose a programming language

* It's cool
* They saw it on HN
* It's actually a great tool
* Their employeer requires them to use it
* It makes money (means of social progress)

### Developers pyramide

1. Contributors
2. ~Cool projects~people
3. Real-life programmers who use the lang for day-to-day tasks

### Reasons why people choose programming languages

* they've been told to use it at schools
* this is a "default" language, the one that people coose to in order code fast and (maybe) change it later
* user groups = actual users with many particular interests

<a name="functional-go"></a>
Functional Go
-------------
_[Francesc Campoy Flores](http://campoy.cat)_

### What is functional programming

* No mutable state -- recursion is significantly slower in Go because there is no tail recursion
  - simply a choice
  - no `for` loops
* Functions are everywhere -- no templates -> passing functions is painful and requires to use `interface{}` for parameter types
  - as parameters
  - as returned values
  - in Go they can be used anywhere

Getting rid of `interface{}` in `Map` function:

```go
type Func interface {
	Call(interface{}) interface{}
}

func Map(f Func, vs interface{}) interface{} {
	// ...
}
```

Functors -- useful to abstract the argument type, e.g.

```go
type List struct {
	// ...
}

func (list *List) Map(f Func) *List {
	// ...
}
```

* Maps
* Lists
* ...


### Useful functors

* `Fmap` -- map collection given a function
* `Maybe` -- `nil` value wrapper
* `Many` -- collection container

### FP in Go

* Doable
  - slower
  - requires reflection magic

<a name="lightning-talks"></a>
Lightning Talks
---------------

<a name="gopy"></a>
### Gopy

_[Sebastien Binet](https://github.com/sbinet)_

_[Slides](https://github.com/sbinet/talks/tree/master/2015/20151109-gopy-dotgo)_

[gopy](github.com/go-python/gopy) -- build cpython modules out of Go packages. For reasons.

<a name="encrypt-all-transports"></a>
### Encrypt all transports
_[Eleanor McHugh](https://about.me/eleanor_mchugh)_

HTTPS -> TLS -> TCP/TLS Client -> UDP/AES Server -> AES/RSA Client

<a name="vossibility"></a>
### How can I get visibility into my open source projects?
_[Arnaurd Portiere](https://github.com/icecrime)_

[Vossibility](https://github.com/icecrime/vossibility-stack) provides better visibility for your open source project.

<a name="http-2-support-in-go-1-6"></a>
### HTTP/2 support in 1.6
_[Brad Fitzpatrick](https://twitter.com/bradfitz)_

<a name="go-internals"></a>
Go internals
------------
_[Alan Shreve](https://inconshreveable.com)_

Programming is hard. Reading code is hard. Understanding the mental model of software is hard. Life is hard.

<a name="the-other-side-of-go"></a>
The other side of Go
--------------------
_[Anthony Starks](https://twitter.com/ajstarks)_

SVGGo

<a name="a-tour-of-the-bleve"></a>
A tour of the bleve
-------------------
_[Marty Schoch](https://twitter.com/mschoch)_

[Full-text search with golang](https://github.com/blevesearch/bleve)

### Language-specific analysis

1. Tokenization
2. Lowercase
3. Article Elision
4. Remove stop words
5. Stemming

<a name="living-with-c"></a>
Living with C
-------------
_[David Crawshaw](http://zentus.com)_

### Why use C?

* Stdlib still has some C code, but it is rare and optional
* System APIs of mobile OSes are exposed as userland C libraries and cannot be avoided

`x/mobile/gl` is a good example of unavoidable C.

<a name="concurrency-in-go-is-not-as-easy-as-it-sounds"></a>
Concurrency in Go is not as easy as it sounds
---------------------------------------------
_[Matt Aimonetti](http://matt.aimonetti.net)_

Sync primitives, channels, etc.

<a name="the-docker-trail"></a>
The Docker trail
----------------
_[Jessica Frazelle](https://blog.jessfraz.com)_

* Do not use `bool` as a compile-time value.
* The binary can `dlopen` itself.

<a name="simplicity-is-complicated"></a>
Simplicity is complicated
-------------------------
_[Rob Pike](https://twitter.com/rob_pike)_

By borowwing ideas from each other programming languages becoming more similar to each other.
Go is different, because it doesn't try to compete in features.