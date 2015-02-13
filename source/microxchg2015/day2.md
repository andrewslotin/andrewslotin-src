---
title: 'microxchg 2015 Notes. Day 2'
---

microxchg 2015: The Microservices Conference in Berlin
======================================================
_Berlin, Feb 12-13th, 2015_

<a name="1"></a>
~~Microservices for Speed~~
---------------------------
~~_Russell Miles_~~

{Nano|Micro|Mini}-Services? Modularization for Sustainable Systems
------------------------------------------------------------------
_[Stefan Tilkov](https://twitter.com/stilkov)_ — Co-founder and principal consultant at innoQ

**[Slides](https://www.innoq.com/en/talks/2014/08/talk-modularization-for-sustainable-systems-munich/)**

### Reviewing architectures

**Problems:**

* Building features takes too long.
* Technical debt is well-known and not addressed.
* Deployment us way too complicated and slow.
* Architectural quality has degraded.
* Scalability has reached its limit.
* "-ility" problems abound (maintainability, scalability, etc.)
* Replacement would be way too expensive.

**Solution:**

The best strategy is not to get there :-D

#### Conways's Law

Organization → Architecture

> Organizations which design systems are constrained to produce systems which are copies of the communication structures of these organizations
>
> _M.E. Conway_

Organization ← Architecture

> Choosing a particular architecture can be a means of optimizing for a desired organizational structure.

### System bounaries

Systems are build because of external reasons. Consolidation of two legacy systems is hard.
Context is set by a project bound.

Common approach: 1 project = 1 system? Modularization ends up with splitting into smaller systems.

```
single file → few files → library, class hierarchy → framework + application → multiple applications
```

### System Characteristics

* Separate persistence
* Internal, separate logic
* Domain models & implementation strategies
* Separate UI
* Separate development & evolution
* Limited interaction with other systems
* Autonomous deployment and operations

We don't do all of that, rather just accepting that the context already exists!

Domain decision — decoupling the problem.
Macro architecture — a decision on how do the domain systems interact with each other

Instead of designing UI + logic + persistense think of systems, each of which has its own UI+logic+persistence layers.

[The Twelve-Factor App](http://12factor.net)

### App Characteristics

* Separate, runnable processes
* Accessible via standard ports & protocols
* Shared-nothing model
* Horizontal scaling
* Fast startup & recovery

### Microservice Characteristics

* Small
* Lightweight communicating mechaisms
* Built around business capabilities
* Independently deployable
* Minimum of centralized management

Quite similar to system characteristics.

### Self-containing System Characteristics

* Autonomous web app
* Owned by one team
* No sync remote calls
* Service API (optional)
* Includes data and logic
* No shared UI
* No pull-based code sharing only

|                             | SCS              | App      | Microservice   |
|-----------------------------|------------------|----------|----------------|
| Size (kLoC)                 | 1-50             | 0.5-10   | 0.1-?          |
| State                       | Self-contained   | External | Self-contained |
| # per logical system        | 5-25             | >50      | >100           |
| Communication between units | No (if possible) | ?        | Yes            |
| UI                          | Included         | Included | External (?)   |

### But Why?

* Isolation!
* (Independent) Scalability — scale only the part that needs to be scaled
* Localized descisions — once taken the decision should not applied system-wide
* Replaceability

### Necessary Rules & Guidelines

* Cross-system: macro decisions, e.g. logging, BI interfaces, protocols, etc.
* Single System: patterns, programming language, technologies, etc.

### Client-side integration

* Thin client sucks because it's hard to maintain and it's slow.
* Fat client sucks because of Javascript hell.
* Links FTW! Each service has it's own interface.

### Server-side integration options

* Deployment (VCS, Gems, Maven artifacts)
* Development (Chef Puppet Build tools Asset Pipeline)
* Storage (DB replication, Feeds)
* Backend call (RMI, RPC)
* Edge (ESI, Portal server)


* Linking (cheap & self-contained)
* Replaceable link (unobtrusive JS, oEmbed, ROCA-style)
* Client call (SPA-style, JS Widgets)

### Summary

* Explicitly design system boundaries
* Modularize into independent, self-contained systems
* Separate micro and macro architectures
* Be aware of changing quality goals
* Strike a balance between control and decentralization

<a name="2"></a>
State of the Art in Microservices
---------------------------------
_[Adrian Cockcroft](https://twitter.com/adrianco)_ — cloud architect at Netflix 2010—2013

**[Slides](http://www.slideshare.net/adriancockcroft/dockercon-state-of-the-art-in-microservices)**

* Everyone seems to be accepted the use of clouds.
* Docker is now good for development and testing, will become a big thing in production by the end of 2015 (go check it!)

### Product Development Processes

Assumption: process prevents problems.
Organization build up slow, complex "Scar tissue" processes.

Continuous Delivery:

```
 Observe → Orient
    ↑        ↓
   Act   → Decide
```

* Observe: competitive move, measure customers, customer pain point, land grab opportunity (innovation)
* Orient: analysis, model hypoteses (big data)
* Decide: plan response, share plans (culture)
* Act: automatic deplot, launch A/B test, incremental features (cloud)

### Breaking Down the SILOs

```
Prod. manager → Devs → DBA → QA → Sys. adm. → Net. adm. → SAN Adm.
```

**Build dev teams as micro-services too!**

* Each team is small, self-contained, consists of devs, prod. manager, QA, DBA.
* System administration team becomes a platform team.

### Run what you wrote

* Pagerduty to detect problems before deployment.
* Use site availability metrics to measure the reliability.

**Disruptor: continuous delivery of containerized microservices**

_READ: [The Phoenix Project](http://www.amazon.com/The-Phoenix-Project-Helping-Business/dp/0988262592)_

**Microservice** — a loosely coupled service oriented architecture with bounded context.

If every service has to be updated at the same time, it's not loosely coupled.

**Coupling concerns:**

* Conway's Law — organizational coupling
* Centralized Database Schemas

  **Bad news:** no normalized databases anymore, application-level transactions — deal with it™.

* Enterprise Service Bus — centralized message queues

  XML _–40x→_ JSON _–10x→_ ProtoBuf _–10x→_ Simple binary encoding

* Inflexible Protocol Versioning

### Non-Desruptive Production Updates

* "Immutable code" service pattern
  - Exsiting services are unchanged, old code remains in service
  - New code deploys as a new service group
  - No impact on other service groups
* ...

### Speeding-up the platform

AWS Lambda — runs a container per each request.

|           | Datacenter snowflakes | Virtualized and cloud | Docker containers | AWS Lambda   |
|-----------|-----------------------|-----------------------|-------------------|--------------|
| Deploy    | months                | minutes               | seconds           | milliseconds |
| Life time | years                 | weeks                 | minutes/hours     | seconds      |

### State of the art in web scale microservice architectures

Microservice concerns

* Tooling
* Configuration (Decider)
* Discovery (Finagle, Zookeeper, go-platform)
* Routing (Finagle, Akka, go-platform)
* Observability
* Datastores (memcached, Cassandra)
* Manual orchestration (Asgard)

_CHECK go-platform, [spigo](https://github.com/adrianco/spigo)_

### Orchestration for applications

Next gen: docker for tooling, eureka/consul for discovery, ribbon/finagle for routing, hystrix for observing

* Tooling — docker
* Configuration — eureka/consul
* Discovery
* Routing — ribbon/finagle
* Observability — hystrix

<a name="3"></a>
Principles of Microservices
---------------------------
_[Sam Newman](https://twitter.com/samnewman)_ — Technologist at ThoughtWorks, author of the book "Building Microservices".


**Microservices** — small **autonomous** services that **work together**.

1. Modelled around business domain
2. Culture of automation
3. Hide implementation details
4. Decentralize all the things
5. Deploy independently
6. Isolate failure
7. Highly observable

### Modelled around business domain

Decouple business domain and build each service representing its own part.

### Culture of automation

The number of services grows exponentially, automation is essential.

* Infrastructure automation
* Automated testing
* Continuous delivery

> If you had to think something twice, third time you write a script.

### Hide implementation details

Shared database is a common pattern these days. What we should do instead is a service talking to another service talking to db.

```
Service
   ↓
 RDBMS      =>   Service → Service → DB
   ↑
Service
```

### Decentralize all the things

Devolution of the power:

* Self-service and shared governace.
* Owner becomes an operator (except, maybe, hosting decisions — security, etc.)
* Internal open source (apply open-source contribution standards internally: code reviews, pull-requests, etc.)
* Dump pipes, smart endpoints (no more message buses, please)

### Deploy independently

The hardest one. How to keep self-service when deploying to production?

* If there is no way to deploy two services separately, one should consider them to be a single service.
* One service per host: several services on a single host are okay for a short period of time.
* Consumer-driven contacts: consumer (shipping) to define expectations, producer (inventory) to meet them.
* Co-exist endpoints: version them, keep several versions at the same time and let the client to decide which one to use.

### Isolate failure

Microservices are less reliable, so there **will** be failures.

```
                 --> Legacy app
                /
Strangler app <----> Legacy app
                \
                 --> Legacy app
```

Having a thread pool in a strangler app can be exausted if the legacy app slows down for some reason.

Bulkhead — downstream connections:

* Split thread pools
* Back up thread pools with circuit breakers

> Buckhead your system to make sure you can isolate the failure quickly.

### Highly observable

Non-prod tests and bechmarks are just bogus.

* Aggregation of logs (logstash/kibana)
* Aggregation of stats
* Correlation IDs
  - Generate an ID for each request and pass it over services, put it to the log to trace later.

_! READ [Building Microservices](http://buildingmicroservices.com)_

<!-- Discount code: AUTHD -->

From Homogeneous Monolith to Radically Heterogeneous Microservices: The Wunderlist 3 Story
------------------------------------------------------------------------------------------
_Chad Fowler_

```
=~======++?7I??+++++++++++++++++==~~=====+I7II7???++++++++++++++========+?I$7$77
=~=~=====+?7I?++++++++++++++++++=~~=~~===+I7III???+++++++++++++++=======+?I77777
=~~~~====+?7I?++++++++==+++=++++=~~~~~~==+I7III???++++++++++++++========++?7777I
=~~~~===++?7I?+++++++=======++++=~~~~~~~==IIIII???++++++=+++++++========++?77777
=~~~~~===+?7I?+++++++========?7I$IZON8~$7=?IIII??+++++++++++++++=========+?7777I
=~~~~~===++7??+++++=?$O8ZO8DIONNDNNND888NDD88$I?+++++++++=++++++=========+?77777
~~~~~~===++7??++++DNDNNNNNNDDDNNNNDNNNNDDDOO8DDNDO++++++===+++++=~~=~~===+?7777I
~~~~~~===++7??++ZDDDDDNNNNNDDNDDNDDDNNNNNNNDDDNNNNN8I+++==++++++=~~~~~===+?7777I
~~~~~~===++7?++ND8OODDNNNNNNDDDDDNNDDNNNNNNNNNNNNNNNDN7+===+++++=~~~~~~==+?77777
~~~~~~===++7?+NDNNNNNNNNNNNNDDDDDDNNNNNNNNNNNNNNNNNNNNNN====++++=~~~~~~~=+?7777I
~~~~~~~==++7?INNNNNNNNNNNNNNNNDNNDNNDNNNNDNNNNNNNNNNNNNN$+==++++=~~~~~~~==+7777I
~~~~~~~===?I?DNNNNNNNNDDDDDDNNNNDDNDDO888888DD8DNNNNNNNNN===++++=~~~~~~~==?I777I
~~~~~~~===+78NNNNNNNDNDDDNNDDDDD88OO$?I7IIIII$$$I$ODNNNOO===+=++=~~~~~~~~=+7I77I
~~~~~~====+ONNNNNDDDDDDNNNNDD8OZ$?+====+++?IIIIII?IIODZZ+===+====~:~:~~~~=+7777I
~~~~~~~~==?NNNNDNDDNNDDNDDDDD8Z$?++++++++??IIII7IIIIII++====+++==~~~:~~~~=+I7III
~~~~~~~===NNNNDDDDDDNDDNDDD88OZ$?++++++????I7777777I77++=========~:~:~~~~=+7III7
~~~~~~~~=+NNNNNDDDDDDDDDDDD88OZZ7I+++??????III777777II?+=========~:::~~~~=+7IIII
~~~~~~~~~=MNNNNDDDDDDDDDDDD88OOZZ7I?++????III7$7$$$$$$7==========~:::::~~=+IIIII
~~~~~~~~==MNNNNNNNNNNDDDDDD8888OO$I???????II7$ZZZZ$ZO8DD=========~:::::~~=+I7III
~~~~~~~~==ONDNDNDNNDDDDDDDD888887I?II?III88DDDD88ZZODD8D=========~:::::~~=+IIIII
~~~~~~~~~=INNNNNNNNDNDDDDD88888I???III$Z88OOO8DDO$$DDND8O8M======~::::~~~=+IIIII
~~~~~~~~~=+NNNNNNDDDDDDDDDDDDDZI?I7$O8DDDDNNDDDZ$ONNND8+7ZN======~:::::~~==IIIII
~~~~~~~~~=+DNNNNNDD$??$NNNNNNNNNNNNNNNNDDNNDDDO$$DD?D8I=+=8======~::::~~~==?IIII
~~~~~~~~~=+ODNNNDD7I88888ODD8O7??IODDDNDD8N888ZOZ8I??O==7O~======~::::~~~=+?IIII
~~~~~~~~~==$NNNNNN7OI?IOOZO8DZII?????IIZ8DNDD8DODI?+II$$==~=====~~:::::~~~=?IIII
~~~~:~~~~==IINNNNDZ$?ZO7$?IOZ7II???????IIIZ8DZ$87?+++?I7========~~:::::~~~=?IIII
~:~:~~~~~=+I?DDNNNDI?7I77??$$$7IIII??IIIIII777ZZ$I?$$7O$I=~~====~~:::::~~==?IIII
~::::~~~~~=I?+NNNDNII??++77ZZZZ$77IIIIIII?II7$ZZODDDDD8Z$=~~===~~~:::::~~~=?IIII
~:::::~~~~=I?+ONNNNN$?++++IOOO8OZZZ$7IIIIII77$Z$7$ODNNN8O7=~=~~~~~::::~~~~=?IIII
~::::::~~~=I?++7NNNND$?+??I88OOOOOOOZ$$7$$$$ZZZZONDDNNNNOO~~~~~~~~::::~~~~=?I?II
~:::::::~~=I++++7NNNDZ$$$Z788888O888OOOZZOOZOOODDDDNDNNDDO~~~~~~~~::::~~~~=?I??I
::::::::~~=I+++=+NDN8Z$$ZZ$88888888DD88OO8OOO8DDDD8OOODNN+~~~~~~~~:::::~~~=+I??I
::::::::~~=I+++==+DD8$I77$8D8888D8DDD8888D888DND888DDDDDO~~~~~~~~~:~:::~~~=+I?I?
::::::::~~=7?++===+8O7I??78888888DDDDD88DDDDDDD8$$ZO8DDD$=~~~~~~~~::~~~~~~=+I???
::::::::~~=I?++====+ZI??I7OOO88888DD8DDDNNNDDDDOZ$$Z8DDDZ=~~~~~~~~::::~~~~=+I???
:::::::::~=I?++=====7II?I7888888D88D8DDDNNNNDNNDOOOO8DDDD+~~~~~~~~::::~~~~=+I??I
:::::::::~=?+++====?????II$Z888888888DNDNNNNNNNDD8D8DNND8?~~~~~~~~:::::~~~=+I???
::::::::~~=I?++====?+????I7$ZO8888888DDDNNNNNNNNDDDDDDDDO+~~~~~~~~::::~~~~=+I???
:::::::::~=I?+==~~=I+????I77$ZZO888888DDDNNNNNNNNDDDDDNDZ=~~~~~~~~:::::~~~=+7I??
:::::::::~~??+=::=?ZZ????II77$ZOOO8888DDDNNNNNNNNDDDDDD8?=~~~~~~~~:::::~~~=+II??
:::::::::~~+,,::~~7IOO????III7$ZZOO8O8DDDNNNNNNNNNND888O$=~~~~~~~~:::::~~~==II??
:::::::::,,,,:~~==O7IOZ???III77$ZZZOOO88DDDNDNNNNNDDDDOO==~~~~~~~~:::::~~~=+II??
:::,,,,,,,,,::~===+O7IOZ????I7$$ZZOOOO888DDDD7NDNDDND8O$+=~~~~~~~~:::::~~~=+I???
:,,,,,,,,::::~~~==+?Z7?Z7??II77$ZZOO888888888$$88DDD8ZZ$+=~~~~~~~~::::::~~=+????
,,,,,,:~~~~~~~~~====IO7IZ$??II77$$OO8DDD88OO8$$$7IZO8$7I==~~~~~~~~::::::~~=+?I??
,,,,,:~~~~~~~~~====+=+OI?OZ????II7$$ZZZ$ZZ$$$Z7I7II?+?~~~~~~~~~~~~::::::~~=+????
::::::~===~========++++Z$IZZ??????II7$ZZOOOZ8OO?+?+++~~:::,:~~~~~~::::::~~=+????
~~===~~=======+===+++++=Z7IZZ????????????I77OOO??+I+=+~~~=~~::::~:::::::~~=+????
~=====~~+=====++++=++++=+OI?Z$?++++?+?+?????Z8O$???I??????I+?+~,,,::::::~~==?I??
==+++===++++++++++++++++++Z77Z$+++++++++++++IOO8I??7I??I?IIII?++=:,,:::::~=+?I??
==+++=~~+++=++++==+++++++++Z7ZZ7++==+=+=====+OZO???7I???IIIIIII==++::::::~==????
```

```
................................................................................
.............,..................................................................
................................................................................
......................................,.........................................
............,................,.......,,.........................................
..........,,,,.,,,.,.,........,.,..,...,,.......................................
......,,,,,,,,,,,,.,...,,,.,...,.,,:~,,,::......................................
,,,,.,,,,,,,,,,,,,,,,,,,,,,.....,.~:,:=~+~==,..,................................
.,,,,,,,,,,,,,,,,,,,,,,,,,,..,.,..::,:,:::~:==:::,,.............................
,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,~,,,.,,,,=:~~~+:::,............................
,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,:~~::,::~~::::,==~:,,,:...........................
,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,~???IIII?+=:,,,:,,,.,.,.........................
,,,,,,,,,,,,,,,,,,,,,,,,,,,.,,:IIIIIIIIIIIIII?:,,,,,,:..........................
,,,,,,,,,,,,,,,,,,,,,,,,,,,,,~IIIIIIIIIIIIIII??+,,.,,,,,........................
,,,,,,,,,,,,,,,,,,,,,,,,,,::,IIIIIIIIIIIIIIII???+~,,,,,.,.......................
,,,,,,,,,,,,,,,,,,,,,,,,,,,=?IIIIIIIII?II?IIII??+=~::,:,,.......................
,,,,,,,,,,,,,,,,,,,,,,,,,,:=?=~:~+?????=::~==???+==::,,,........................
,,,,,,,,,,,,,,,,,,,,,,,,,,,:?III=~:+I+,:~??+,,~++~=:,:,,,.......................
,,,,,,,,,,,,,,,,,,,,,,,,,,,++:,:::~=I:~~,.,,~=,..+:=:::,,.......................
,,,,,,,,,,,,,,,,,,,,,,,,,,.?:::~:=+7I=+::::,::.,,..~~::,........................
,,,,,,,,,,,,,,,,,,,,,,,,,,~+++II?+7III=I?+?+~:~~:~,.,::,........................
,,,,,,,,,,,,,,,,,,,,,,,,,,??III7I+~==??II+~~~I?++=+~:,,,........................
,,,,,,,,,,,,,,,,,,,,,,,,,+III7III:,,:,~IIIIII??++++:=+=?........................
,,,,,,,,,,,,,,,,,,,,,,,,,=IIIIII+:,,:~I??III???+=++=:,,,........................
,,,,,,,,,,,,,,,,,,,,,,,,,=??III~~~+I~:~++??+=+++==~~,=~~,,,.....................
,,,,,,,,,,,,,,,,,,,,,,,,,:+??+:,,.,,,,,,:==~~=~~:::~???.........................
,,,,,,,,,,,,,,,,,,,,,,,,,:~=?=:+II??II?=:===::::,::=+?..,.......................
,,,,,,,,,,,,,,,,,,,,,,,,,~~~+~=I?~:~~=+?:=~:,:::,:,=+...........................
,,,,,,,,,,,,,,,,,,,,,,,,,::~:~+I?+I++?I+~::,,::,,,,,,...........................
,,,,,,,,,,,,,,,,,,,,,,,~~~~~::~=~~~:~+=::::,,::,,,,,...,,,......................
,,,,,,,,,,,,,,,,,,,,,,?I7,~~~~:~,,:::~,,,,,,,:,,,,.,,..,.,......................
.,,,,,,,,,,,,,,,,,,,,.,.,,,=~::,,,,,,,.,,,,,::::::.,.,,,.,......................
,,,,,,,,,,,,,,,,,,,,,,,,,,,+==~,,,,,,,,,,,,::::~~,,,,...,.......................
,,,,,,,,,,,,,,,,,,,,,,,,,,?++===~~:::::::::::~~~~..,,...,.......................
..,,,,,,,,,,,,,,,,,,,,,,,:?++===~~~~~~:::::::~~~=..,..,.........................
..,,,,,,,,,,,,,,,,,,,,,,,II?I=~~~~::~~:::::::~~~~+,,............................
...,,,,,,,,,,.,,,.,,,:~II?I=?II:,:::~::::::~~~===+:,,,,.........................
...,.,,,,,,,,,:~?I7II7IIII?=+I=,=:::::::::~====++I?.,.,,,,......................
.,,,,,,,,+7IIIIIII?I?777I?I~++,.?::,::::~~=+=?I~?+?,,.,.,,,.....................
=I7I7IIIII?7III+I??77IIIII=I??,,:=+,::~=:~:,.~~~=++=++.,........................
?7III?I7I?I?7I77I77I77I7I+II+?~,~?I?I=,:,,,,~~:+?+=+=I?I,...,...................
I?I+??=7??7=IIII?I7I=I??I+?III=~?:,,?++,,,,::~~?=??==II+?7=.,...................
?+II+I+?II7I?7?IIII7+?I=7??II+?II=?=7?~II=,+I~~++I?I??I?=I=II~..,.,.............
=?II=I??I7II777II7I7I+7III?IIII==III??===?=I?+?I+??I+II+II+IIIII?~...,..........
++II~I7I?7I7III7IIIII+7III+II?7????I~I?II??=IIII?++I=II=II?II=??+II?:.,.........
+I7I+II7=II?77?7II7?I?I7I7?I?+7I?II=?II+I~I??I?II??I~?I++I???I7I=I~?I+II,,......
=?I?IIII??7I?IIIII7?I7IIIII?7=II=II?=?II7+I7=I?+?I?I=+I?=7+?I~II?I?I?+I?II+,,...
I?++III?IIII+IIIIII+III7??I=I+?I~I~I??7?I?+I~I?~IIII=+I?=I=+I~???I+II=???=?I,...
?I=+IIIIII?I+II?7IIIII7II?I=I??I~I~II?7III+I=II=?I?I??I++7+??+?III?II++?+~I??7,.
~~I?7IIIII+IIIIIII?7I7III+I~?I+I+I+?I?IIII?I=?I++I+II?I=II?II=?I?II?I===I+++I+,.
I=II7?IIII=II+I?II?7III+I~?+?I?II7IIII7?+I~???I++I?II+I=II?I?+?I?I??I?+?I??++=,.
```
