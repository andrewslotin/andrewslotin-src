---
title: 'Fullstack Fest 2016 Notes'
---

Fullstack Fest 2016
===================

_September 5, 2016, Barcelona, Spain_

1. [Computing: the First 100 Years](#computing-the-first-100-years)
2. [Shopify in Multiple Datacenters](#shopify-in-multilple-datacenters)
3. [Architectural Patterns of Resilient Distributed Systems](#architectural-patterns-of-resilient-distributed-systems)

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
