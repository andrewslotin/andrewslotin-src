---
title: 'microXchg 2015 Notes. Day 1'
---

[![microXchg 2015](http://microxchg.io/microxchg_logo.png)](/microxchg2015)
=================================================================================
_Berlin, Feb 12-13th, 2015_

<a name="1"></a>
Domain Service Aggregators: a Structured Approach to Microservices Composition
------------------------------------------------------------------------------
*[Caolite O'Connor](https://twitter.com/caolite_oconnor) — developer at ITV (British television company)*

### Split into a set of bounded contexts


```
     ----> Domain (e.g. catalogue) -    +-------+
    /                               \   |       |
App -----> Domain --------------------> | Cache | -> User
    \                               /   |       |
     ----> Domain ------------------    +-------+
```

1. Organisationla boundaries work best.
2. High cohesion, loose coupling.
3. Think resource oriented.

Each domain is represented by a REST service:

* Extract domain into a microservice and put it in front of the legacy system.
* Varnish cache to "protect" the microservice.

_! LATER: The Netflix API gateway pattern._

**Pros:**

* Bounded contexts
* Resource-oriented
* ...

**Cons:**

* Ignored underlying data sources.
* API Gateway can become a monolith.

### Simplifying the API Gateway

* Batch queries are hard to cache.
* Use of HTTP queries to simulate database inner joins.

**Introduce aggregation service:**

* Client specific integration separated.
* New VOD (Video-on-demand) service gets datastore.
* Less HTTP calls
* ...
* ...

**Domain service**

* Like a single DB table.
* Consistent.
* Modified by Human Commands.
* Long lived datastore.

**Aggregation service**

* Like a DB projected view.
* Eventually consistent.
* Modified by Domain Update Events.
* Ephemeral datastore.

**Pros:**

* Client integration separated from Service aggregation
* Inner Join" HTTP calls eliminated
* Query logic to specialist datastore

**Cons:**

* High coupling between domains and aggregation service

### The Right Way™

**HATEOAS** — [Hypermedia as the Engine of Application State](http://en.wikipedia.org/wiki/HATEOAS).

Add an availability service (presenter).

**Pros:**

* Identified a new and highly cohensive domain.
* Decoupled domain services from domain aggregator.

**Cons:**

* Some duplicated logic back into API Gateways.
* A challenge for Consumer Contract Testing.

### Versioning

**Pitfalls:**

* `/catalogue/v1/episodes/123456` — no permanent location.
* Any breaking change in the API needs to be reflected in URLs.
* Domains coupling is back: this time with hardcoded URLs.

**Solutions:**

* Don't version your APIs
* Version the host
* Require custom version header
* Require a version request parameter
* Require a version in the accept header media type

  ```
  HTTP GET:

  https://api.example.com/some/resource
  Accept: application/vnd.my+awesome+type+hal+json;version=1
  ```

#### Content negotiation

1. Web browser makes a standard request.

  ```
  HTTP GET:

  https://api.example.com/some/resource
  Accept: text/html
  ```
2. Respond with the content of type, provided in `Accept` header.

  ```
  Context-Type: text/html;encoding=utf-8

  <html>
    <body>
      <h1>Resource</h1>
    </body>
  </html>
  ```

<a name="2"></a>
Measuring Microservices
-----------------------
_[Richard Rodger](https://twitter.com/rjrodger)_ — CTO of nearForm.com

### Message flow rate

* Easy to measure.
* Tells you a lot:
  - Perfomance degradation because of changes in services
* Independent of services.

#### What to measure?

**Services patterns:**

* Actor — a pool of services share message load evenly.
* Subscriber — many services, all listen for the same set of message types.
* Chain — an initial message causes a chain of serial message steps.
* Tree — an initial message causes a flowering of child mesages.

Dynamic measurement — measure health of the system. Exposes unknown unknowns.

_! LATER: [**TLA+** (Leslie Lamport)](http://research.microsoft.com/en-us/um/people/lamport/tla/tla.html)_

Define and measure invariants — non-changing metrics.

> Example:
>  E-commerce shopping cart:
>  add-to msg == sales tax msg

Example invariants for different patterns:

* Actor: n actors means each actor sees 1/n of the messages
* Subscriber: n subscribes n messages
* Chain: n over k means n*k
* Tree: n msg over k leaves n*k leaf messages

Invariant has changed after deployment → rollback.

* Business rules are invariants too! Express them as message relationships.
* Combine individual indicators to get a deeper measure of risk.

<a name="3"></a>
Building Event-Driven Microservices with Docker and Spring Boot
---------------------------------------------------------------
_[Chris Richardson](https://twitter.com/crichardson) — Author of POJOs in Action, Founder of the original Cloud Foundry PaaS, Java Champion, JavaOne Rock Star_

**[Slides](http://www.slideshare.net/chris.e.richardson/building-and-deploying-microservices-with-event-sourcing-cqrs-and-docker-melbourne-microservices-meetup-2015)**


### Why build event-driven microservices?

Traditional application architecture: browser/client-presenter+services-RDBMS, packed and deployed all at once.

* **Simple** to develop, test and deploy.
* **Hard** to do continuous delivery, scale and maintain.

Scaling cube:

* X — horizontal duplication
* Y — functional composition
* Z — data partitioning

Single RDBMS system limitations:

* Scalability
* Distribution
* Schema updates
* O/R impedance mismatch
* Handling semi-structured data

Solutions?

1. Sharding
2. Use NoSQL

Multiple RDBMS — how to keep them in sync?

### Event-based architecture approach

* Components (services) publish event when state changes.
* Components subscribe to services:
  - Maintaing eventual consistency across several aggregates.
  - Synchronize replicated data.

To mainpain consistency service must **atomically** publish an event **whenever** the domain object changes.

* Use 2PC
* Use datastore as a message queue
  - new event: insert event into database
  - consume event: mark as processed

### Event sourcing

* Identify domain events
* Define event classes

Persist the sequence of events that lead to the state, not the state itself,
and replay them later. No need of of atomic update+publish.

### Business benefits of event-driven approach

* Built-in reliadble audit log
* Enables temporal queries
* Publishes event needed by big data/prefdictive analysis etc.
* Preserved history

### Technical benefits and drawbacks

* Solves data consistency
  - Atomic save and publish events
  - Event subscribers update other aggregates ensuring eventual consis
  - Event subscribers update materialized views in SQL and NoSQL databases
* Eliminates O/R mapping problem

* Weird and unfamiliar
* Events = a historical record of your bad design decisions
* Handling duplicates can be tricky
* Application must handle eventually consistent data

### Implementing queries in an event sourced application

**CQRS** pattern — **C**ommand **Q**uery **R**esponsibility **S**eparation.

**Pros:**

* NEcessary in an event-sourcet architecture
* Separation of concerns
* Supports multiple denormalized views
* Improved scalability and perfomance

**Cons:**

* Complexity
* Potential code duplication
* Replication lag / eventually consistent views

### Builing and deploying microservices

* Node.JS for API
* Scala / Spring Boot for heavy lifting
* S3 as a storage
* AWS Cloud for indexing

#### Jenkins-based deployment pipeline

1. Build & test
2. Build & test docker image
  - deploy container
  - check e.g. /health
3. Deploy docker image to registry

<a name="4"></a>
Microservices, Micro Operations? - Challenges of Microservice Models at the Operations Level
--------------------------------------------------------------------------------------------
_[Dustin Huptas](https://twitter.com/dhpts) & [Andreas Schmidt](https://twitter.com/aschmidt75)_


Evolution of infrastructure over last years:

```
Physical servers → virtual servers → containers → dynamic workloads
```

**Dev:**

- Local development/test environment
- Single host
- Mocked services

**Prod:**

- Scaling of instances
- Services instances scattered
- Networking, security, i.e. firewalling
- Logging and monitoring on a larger scale
- Stuff needs to be persisted

### Increasing number of services

The number of services is tend to increase over time.

Problem: maintaining a bag of services. How to call? How to scale? Deployments? Logging?

Solution: detect patterns and apply them across all services.

### Discovering of services

Few services — easy, just write down IP addresses.

Scalability & failover:

1. In-app configuration
2. Balancing layer within the service
3. Wiring built into proxy/load balancer.

Versioning (different versions of the same service exists simultaneously) and reachability (different network segments, etc.) are problematic.

Proxy/LB with a built-in wiring tends to grow and uglify the configuration management layer.

**KV cluster:** — to keep mappings

* coreos/etcd
* consul
* …


**Template engine** — for configuration templates

* confd
* consul-template
* …

**Proxying** — you guess

* haproxy
* …

```
discovery = proxying(template(values))
```

### Dependencies

- Build: internal dependencies
- Runtime: infrastructure dependencies
Problem: startup order.

Resilience → **circuit breaker pattern**

~~Upfront design~~ → Changing architecture in-between

* Full automation is the key
* Configuration management tools for servers
* No more static wiring
