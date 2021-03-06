---
title: 'Fullstack Fest 2016. Day 1'
---

Fullstack Fest 2016
===================

_September 5, 2016, Barcelona, Spain_

1. [Computing: the First 100 Years](#computing-the-first-100-years)
2. [Shopify in Multiple Datacenters](#shopify-in-multilple-datacenters)
3. [Architectural Patterns of Resilient Distributed Systems](#architectural-patterns-of-resilient-distributed-systems)
4. [Unikernels and why they're useful (or not)](#unikernels-and-why-they-are-useful-or-not)
5. [What did AlphaGo do to beat the strongest human Go player?](#what-did-alphago-do-to-beat-the-strongest-human-go-player)
6. [How secure are Docker containers?](#how-secure-are-docker-containers)

<a name="computing-the-first-100-years"></a>
Computing: the First 100 Years
------------------------------
_[Joe Armstrong]()_

A nice talk on the history of computers.

### Computers

* 1975: Cray 1 - the most advanced supercomputer back in 1975
* 1975: Vax 11/780 = 0.00625 x Cray 1
* 2014: iPhone 6 = 256 x Cray 1
* 2015: Raspberry Pi 3 = 15 x Cray 1
* 2016: Nvidia Tesla P100 = 66250 x Cray 1
* ca. 5 million years ago: Human Brain = 2.85E8 x Cray 1

### Hardware from 1948-2016

* TFlops CPU
* TBytes+ memory
* Connectivity is limited by laws of physics (LiFi)
* Consists of billions of small communicating deivces

### Dangers

1. Saving history - perisisting data for decades and centuries is a problem
  - Storage
  - Naming

  + [IPFS](https://ipfs.io/)
  + [Kademlia](https://en.wikipedia.org/wiki/Kademlia)
  + [Self-certifying File System](https://en.wikipedia.org/wiki/Self-certifying_File_System)
2. Computational power
  - A computer built into a solar panel.

<a name="shopify-in-multilple-datacenters"></a>
Shopify in Multiple Datacenters
-------------------------------
_[Simon Eskildsen](https://twitter.com/sirupsen)_

[Slides](https://speakerdeck.com/sirupsen/full-stack-fest-2016-shopify-in-multiple-datacenters)

The Flash Sale Problem -- 3-4x req/s peaks.

### Single Tenant vs Multi Tenant solutions

Single Tenant:
+ Isolation
+ Scalability

Multi Tenant:
+ Capacity
+ Utilization
+ Good for flash sales
+ Cheaper

### The Golden Middle?

* First steps
  - Vertical scaling
  - Performance optimizations
  - Caching
  - Throttling
* Database sharding
* Resilience

    > errors ~ surface area
    >
    > platform surface area = time * scale

    **Resilience Maturity Pyramid**

    ![Resilience pyramid](/images/resilience-pyramid.jpg)

* Multiple DCs
  - Zero-downtime migrations.
* Pods

  **Pod** -- an isolated group of shops (DB only):
  - Workers
  - KV store
  - Cache
  - Shard

  Goal is to run pods in multiple DCs.

  * Pods have all benefits of multi tenant approach while being isolated (within a single pod) and scalable.
  * Flash sales: leveraging platform size to steal capacity from other pods when required.
    _"Borrowing" workers_

  API routing and load-balancing via [openresty](https://openresty.org/en/):
  > openresty = nginx + lua + ❤️

  **Rules**
  1. Any request must be annotated with a pod or shop.
  2. Any request can only touch one pod.

#### Shitlist-driven development

Instead of deprecating certain API method whitelist valid ones.

### Q&A

Q: Sharded transactions?
A: They are isolated, so no cross-shard transactions.

Q: Microservices?
A: Seems to be an overcompensation.

Q: How to handle pod deployments?
A: Deploying all at once works for now, but would like to make them independent in future.

Q: Kubernetes?
A: No.

<a name="architectural-patterns-of-resilient-distributed-systems"></a>
Architectural Patterns of Resilient Distributed Systems
-------------------------------------------------------
_[Ines Sombra](https://twitter.com/randommood)_

[Slides](http://www.slideshare.net/InesSombra/architectural-patterns-of-resilient-distributed-systems) same talk but from 2015.
[GitHub repo](https://github.com/Randommood/FullStackFest2016) of the talk.

**Resilience** is the ability of a sustem to adapt or keep working when challenges occur.

### How can we construct more resilient systems?

* Yield -- how much information I can get back from my system.

  Focus on yield rather than uptime.
  
* Harvest -- fraction of the complete result.

  > harvest = data available / total data

#### Probabilistic Availability

* Graceful harvest degradation
* Randomness to make the worst-case & average-case the same
* Replication of high-priority data for greater harvest control
* ...

#### Decomposition & Orthogonality

Decompose into subsystems intolerant to harvest degradation
Only provide strong consistency when required

> If your system favors yield or harvest is an outcome of its design.

#### Cook's model

* Mentoring
* Responding
* Adapting
* Learning

System safety is about what can happen, where the operating point is and what's the error margin.

#### Engineering system resilience

* Continuous maintenance
* Reveal control to opeators
* Know it's going to be used in the ways you never intend

#### Borrill's Model

> Failre areas need != strategies

* Classical engineering
* Reactive operations
* Unknown-unknown

### Resilience in industry

* Chubby lock by Google
  - centralized services are hard to contruct
  - engineers don't understand distributed systems
* ChaosMonkey by Netflix
* Fastly
  - [Powderhorn](https://www.fastly.com/blog/building-fast-and-reliable-purging-system) by fastly
  - ImageOpto
    + Design error types & graceful handling of them
    + Failure detection & system operability are ongoing concerns
    + Mixed-mode & versioning of data structures
    + Validation & system adaptability

### Wrapping up

* Redundancies are the key
* Gossip/epidemic protocols
* Capacity planning matters

> Optimizations can make your systems less resilient!

Operations matter

* We're always guessing
* Complex ops leads to less resilient system
* You design operability too

Not all complexity is bad

* Increases safety
* Adding resilience may come at the cost of other desired goals (perfomance, simplicity, etc)

Leverage engineering best practice

* Test!
* Version from the begininng!
* Mixed-mode ops need to be common (upgrades & evolvability are still tricky)

<a name="unikernels-and-why-they-are-useful-or-not"></a>
Unikernels and Why They're Useful (or not)
------------------------------------------
_[Amir Chaudhry](https://twitter.com/amirmc)_

Code you **care** about / code the OS **insists** you need = 1/9.

* Disentangle apps from ths OS
* Break up OS libs into modular components
* Link only system functionality
* Target alternative platforms

> // Unikernels wiki quote

Two approaches

1. Consider legacy
   - ...
2. Clean slate approach (from the ground)
   - Mirage OS
   - IncludeOS

##### MirageOS

* Written in OCaml
* Everything is a library
* Swapping system libraries allows to target different platforms

Familiar development cycle, while being flexible with the choice of target platform.

### Unikernels recap

* Highly specialized
* Continuum with containers
* Robust deployments
* Everything is a library!

### When should you use unikernels?

Microservices:

* Single service
* Distributed system
* Independent deployment
* Diversity of test choices

### Pathway

1. Detect boundary lines in your monolith
2. Break it up into microservices
3. Turn each into a unikernel

### Are they production ready?

Yes, but be ready to get your hands dirty.

<a name="#what-did-alphago-do-to-beat-the-strongest-human-go-player"></a>
What did AlphaGo do to beat the strongest human Go player?
----------------------------------------------------------
_[Tobias Pfeiffer]()_

[Slides](https://speakerdeck.com/pragtob/what-did-alphago-do-to-beat-the-strongest-human-go-player-1)

Machine learning and Go 101 (I missed a large part of it to be honest).

<a name="how-secure-are-docker-containers"></a>
How secure are Docker containers?
---------------------------------
_[Ben Hall](https://twitter.com/Ben_Hall)_

What happens when you give anonymous unrestricted access to a hosted Docker daemon?

Dockerfile:
```
RUN adduser <new user>
USER <new user>
```

Run container:
```
$ docker run -u <new user>
```

Also this gives an access to host's network, as well as D-Bus, and thus insecure:
```
$ docker run -it --net=host ubuntu bash
```

## Limit a container to a share of the resource
```
--cpu-shares
--cpuset-cpus
--memory-resetvation
--kernel-memory
...
```

### Elevation of privileges

An existing `setuid(0) && setgid(0)` exploit:
```
$ docker run --uid=$(id -u) --security-opt=no-new-priveleges
```

### Read-only containers

```
$ docker run --read-only \                          # Make container FS read-only
             --security-opt="no-new-priveleges \    # Disallow elevation of priveleges
             --security-opt="apparmor:es-profile" \ # Use elasticsearch security profile
             -v /data:/data
             elasticsearch
```

So Is Docker Secure? Yes, it is but only as secure as your practices.

[A security benchmark and analyzer for Docker](http://DockerBench.com)

### How secure are containers?

* Docker has strong defaults
* Nothing is really secure
* Potential exploits via Linux Kernel
* Run DockerBench.com
