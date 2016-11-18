---
title: 'Gotham Go Notes'
---

1. [Codebase Refactoring (with help from Go)](#codebase-refactoring)
2. [Dissection of Gophers: The Golang Community](#dissection-of-gophers)
3. [Cloning Git in Go](#cloning-git-in-go)
4. [To Go or Not to Go?](#to-go-or-not-to-go)
5. [How to Write Your Own Go Tool?](#how-to-write-your-own-go-tool)
6. [10 Times Go was Totally Perfect for BuzzFeed's API Gateway](#go-at-buzzfeed)
7. [How to Raise a Gopher in Record Time](#how-to-raise-a-gopher-in-record-time)
8. [Demystifying Channels](#demystifying-channels)
9. [Building a Go Debugger](#building-a-go-debugger)
10. [Quality Tests in Go](#quality-tests-in-go)
11. [How the Go Open Source Project's Inards](#how-the-go-project-operates)
12. [Mind the Gap](#mind-the-gap)

GothamGo 2016
=============

_November 18, 2016, New York, USA_

<a name="codebase-refactoring"></a>
Codebase Refactoring (with help from Go)
----------------------------------------
<i>[Russ Cox](https://twitter.com/_rsc)</i>

### Why?

- Split into manageable pieces
- Improve naming
- Lighten dependencies
- Change the dependency graph to avoid circular dependencies

### How?

size(code repair) >>> size(API change)

Atomic code repair: make API change and code repairs in a single commit.

- Easier reviews
- Easier rollbacks

Steps:

1. Add new API
2. Code repairs (use new API)
3. Remove old API

Atomical commits may not work for large codebases.

1. Add new API, _interchangeable_ with old API
2. Convert uses of old API to new API
3. Remove old API


* Moving a Constant
  - Introduce a new constant in a new package, while keeping the old one
* Moving Functions
  - Call the new function from an old one
* Moving Variables
  - Reuse the value of new var in an old one.
* Moving Types
  - ~~Make a type alias from old to a new one~~ Fork Go and modify the compiler.

### The Bright Future

Aliases!

In large codebases, refactoring needs to be able to be done in gradual commits,
but this cannot be done for types.

<a name="dissection-of-gophers"></a>
Dissection of Gophers: The Golang Community
-------------------------------------------
_[Cassandra Salisbury](https://twitter.com/cassandraoid)_

<script async class="speakerdeck-embed" data-id="6506cf8d9468435d83c723adaa650750" data-ratio="1.77777777777778" src="//speakerdeck.com/assets/embed.js"></script>

* Dyeversiteee!!! Ineqwaliteee!!!
* Spoke "too white" and had an afro back when she was a kid in California.
* According to Red Monk Go was ranked to be the same as Visual Basic.

### Why Dyeversitee is Important?

* Diverse teams are smarter and more creative
* Diverse companies can better serve a diverse user base
* Diversity attracts talent

### Is Go Dyeverse?

Of course not.

### What To Do?

Be dyeverse.

<a name="cloning-git-in-go"></a>
Cloning Git in Go
-----------------
_[Aditya Mukerjee](https://twitter.com/chimeracoder)_

<script async class="speakerdeck-embed" data-id="230a7ade05b2420792a0822bf618de7c" data-ratio="1.77777777777778" src="//speakerdeck.com/assets/embed.js"></script>

[gitgo](https://github.com/ChimeraCoder/gitgo) - Git implementated in Go

* Git operations from Go apps
* Portability
* Simplicity
* ... why not?

Git is a decentralized, userland filesystem for preserving historical state and synchronizing data.

    $ man git
    NAME
           git - the stupid content tracker

Commit objects are human-readable (after decompressed) and reference tree objects.

Objects in `.git/objects` are of 3 types:

- commits
- trees
- blobs

`git checkout` just replaces current content with the content of corresponding blob objects.

Packfiles are compressed (using a custom-built almost undocumented algorithm) binary diffs used to compact the changes.

Supplementary read: [Unpacking Git Packfiles](https://codewords.recurse.com/issues/three/unpacking-git-packfiles)

Findings:

* Layered interfaces are more flexible than union types.
* Pay attention to interface contracts.
* Use concurrency to define state machines, and use channels thos the input/output.
  - Goroutines as states
  - Channels represent transitions
* Treat errors as values, and ask how they behave.
  - Make errors typed and handle them based on their kind
* Well-architected interfaces perform very well.

<a name="to-go-or-not-to-go"></a>
To Go or Not to Go?
-------------------
<i>[Peggy Li](https://twitter.com/_peggyli)</i>

"Selling" Go as a programming language to go with to your company.

### Considerations before adopting a new tool

* Common sense
* Team unification
* Progress acceleration

A tool should unite an engineering team, if it polarizes it instead - it will fail.

### Keep the big picture in mind

- What you're building?
- Who you're building it for?
- Why are you building it at all?

### Short vs. long-term thinking

1. Decide if you're prototyping or building a business?
2. Optimize for time to market vs. long-time maintainability and stability

### Why use Go?

* Static binaries
  - simple deployments
  - easier dependency management
* Cross-compilation/multi-platform
  - support more users
  - offer consistent experience
* Extensieve tooling
  - focus on building and shipping
* Performance
* Simplicity

### Where to use Go?

* Frontend (GopherJS)
* Data-science (GopherDS)
* ML
* Building DSL
* ...

<a name="how-to-write-your-own-go-tool"></a>
How to Write Your Own Go Tool?
------------------------------
_[Fatih Arslan](https://twitter.com/fatih)_

<script async class="speakerdeck-embed" data-id="902501f3d5ce4dc68baf24c0c2ec8d99" data-ratio="1.77777777777778" src="//speakerdeck.com/assets/embed.js"></script>

* `go/token` defines constants for lexical tokens of Go language
  - `Position`
  - `Token`
  - `File`
  - `FileSet`
* `go/scanner` (a.k.a. lexer) reads source code and emits tokens
  - `Scanner`
* `go/ast` declares the types used to represent syntax of Go
  - `Node`
  - `Inspect()` - traverse an AST in a depth-order and call a visitor function with each node.
* `go/parser` parses source code and builds an AST
  1. Start with `*go.GenDecl`
  2. Continue with `valueSpec.Values`
  3. Proceed to `BinaryExpr`
* `go/printer` prints AST nodes in a human-readable format

### Steps to write a Go tool

1. Read with `go/parser`
2. Inspect with `go/ast`
3. Write out with `go/printer` or custom format (JSON, text, etc.)
4. Create a CLI

### More packages

* `go/types` - a type checker
* `go/ssa` - SSA emitter
* ...

<a name="go-at-buzzfeed"></a>
5 Times Go was Totally Perfect for BuzzFeed's API Gateway
---------------------------------------------------------
_[Jon Morehouse](https://twitter.com/jonmorehouse)_

* Prototyping
* Cncurrency
* Stdlib
* Performance
* Future proof

<a name="how-to-raise-a-gopher-in-record-time"></a>
How to Raise a Gopher in Record Time
------------------------------------
_[Tiffany Jernigan](https://twitter.com/tiffanyfayj)_

* Read the docs (`go doc` is your friend)
* Visit [some](https://www.socketloop.com) [websites](https://gobyexample.com)
* Use Playground to excercise
* Use `godef` to investigate the code

<a name="demystifying-channels"></a>
Demystifying Channels
---------------------
_[Cindy Sridharan](https://twitter.com/copyconstruct)_

> Mutexes are to serialize, channels are to orchestrate.

_Rob Pike_

Channels are just moody queues.

### What is a channel anyway?

A ring buffer, guarded by a mutex.

<a name="building-a-go-debugger"></a>
Building a Go Debugger
----------------------
_[Abel Mathew](https://twitter.com/nullisnt0)_

### ELF and DWARF

Inspecting ELF binary:

    $ go build -o test test.go
    $ readelf -f test

ELF file structure:

    +----------------------+
    |      ELF header      |
    +----------------------+
    | Program header table |
    +----------------------+
    |        .text         |
    +----------------------+
    |       .rodata        |
    +----------------------+
    +----------------------+
    |        .data         |
    +----------------------+
    | Section header table |
    +----------------------+


Debugger translates program state (registers, memory) into goroutines, variables and
callstack using debug information.

DWARF format data frames:

* `.debug_line` maps memory addresses to line numbers
* `.debug_info` types, variables and fucntions
* `.debug_frame` unwinding information by memory addresses

Inspecting DWARF data:

	$ readelf --debug-dump=info -f test

### What does the debugger do?

1. List goroutines
2. Unwind callstack
3. Resolve variable names

<a name="quality-tests-in-go"></a>
Quality Tests in Go
-------------------
_[Mat Ryer](https://medium.com/@matryer)_

Why write tests?

- Demonstrate the intent
- Code becomes documentation

* Don't skip the design step, but write tests before you start the implementation.
* E2E tests to ensure, unit tests to verify.
* Shared setup and teardown

        func setup(t*testing.T) (*db, func()) {
            // ...
            return db, db.Close
        }
        
* Use different package name for tests to prove that your interface is usable.
    
    > If you don't trust yourself to not use private methods in unit tests, then I cannot help you.
    
    _Dave Cheney_
  
* Interface tests

**Test code is your code**

* Treat is as a first class concern
* Look after it
* Clear away old tests

**#protip** use interfaces to inject dependencies and mock them in unit tests.

<a name="how-the-go-project-operates"></a>
How the Go Open Source Project's Inards
---------------------------------------
_[Brad Fitzpatrick](https://twitter.com/bradfitz)_

Q: Why not GitHub + Travis?<br/>
A: Long story:
    svn -> p4 -> hg -> git,
    mondrian -> rietveld -> gerrit

Q: What's Go release cycle?<br/>
A: 6 month: 3 for features + 3 for bugs

Q: Why mailing lists?<br/>
A: Because we're old

Currently available mailing lists:

* golang-nuts@
* golang-announce@
* golang-dev@

Q: Why do you have CLA?<br/>
A: Neccessary evil

[Go build dashboard](https://build.golang.org) for different configurations and environments.

Q: So why no Travis?<br/>
A: Because it doesn't do what we already have. It's getting better,
   but we already have it and it's written in Go.

<a name="mind-the-gap"></a>
Mind the Gap
------------
_[Katrina Owen](https://twitter.com/kytrinyx)_

To do something you need to decide to start and then you need to keep deciding it over and over again.

> Science knows a lot more about motivation then the life coaches do.

Motivation variables:

* Subjective value
* Expectation of success
* Environment

To increase achievability:

1. Have actionable plan
2. Believe it will work
3. Believe that **you personally** can do it

Experts are not able good resources for beginners - they don't realize how many shortcuts
they take and how many steps they skip to achieve the goal.

Teaching in small steps with isolated contexts and repetitive small task.

**Fluency != Proficiency**

Levels of proficiency:

* Level 1
  - Write syntax
* Level 2
  - Simple programs
* Level 3
  - Real-world programming
* Level 4
  - Esoteric and philosophic knowledge about programming

[exercism.io](http://exercism.io/) - level up your programming skills.

