---
title: 'Fullstack Fest 2016. Day 2'
---

Fullstack Fest 2016
===================

_September 6, 2016, Barcelona, Spain_

1. [How to write a search engine in 15 lines of code](#how-to-write-a-search-engine-in-15-lines-of-code)
2. [Whirlwind tour through the HTTP2 spec](#whirlwind-tour-through-the-http2-spec)
3. [Microservice Pipeline Architecture](#microservice-pipeline-architecture)
4. [The State Of Serverless](#the-state-of-serverless)
5. [Low-res NLP for your everyday life](#low-res-nlp-for-your-everyday-life)
6. [Distributed Apps with IPFS](#distributed-apps-with-ipfs)
7. [From REST to GraphQL](#from-rest-to-graphql)
8. [Building a Recommendation Engine with Machine Learning Techniques](#building-a-recommendation-engine-with-machine-learning-techniques)
BY BRIAN SAM-BODDEN

<a name="how-to-write-a-search-engine-in-15-lines-of-code"></a>
How to write a search engine in 15 lines of code
------------------------------------------------
_[Paul Chiusano]()_

[Unison](http://unisonweb.org/) programming language.

* First-class functions
* Types
* Persistent key-value storage

<a name="whirlwind-tour-through-the-http2-spec"></a>
Whirlwind tour through the HTTP2 spec
-------------------------------------
_[Ole Michaelis](https://twitter.com/CodeStars)_

### FAQ

- Is it done? Yes.
- Is SSL mandatory? No.
- Is it made by Google? No.
- Is it plain-text? No.
- Are headers compressed? Yes.
- Will HTTP/1.1 still work? Yes.

### Upgrading from HTTP/1.1

_Assuming we already have HTTPS._

* TLS-ALPN (RFC 7301)
* h2 (encrypted) vs. h3c (clear-text)

### Basics

* Frames
  - Connection contains multiple streams
  - Data withing a stream is transferred in frames
* Server Pushes
  - Server returns a promise
* Header Compression
  - Exchange is in binary format, hence saving bandwidth

### Support

* Browser
  - Almost all browsers have HTTP/2 support in their latest versions (except Opera Mini).
* Servers
  - Apache via `mod_http2`
    
    ```
    LoadModule http2_module mod_http2.so
    ```
    
  - nginx via `http2` option in `listen` directive
    
    ```
    listen 443 http2 ssl default_server;
    ```
 
  - IIS
  - Amazon ELB

### Future

* Cache digests
* QUIC (a.k.a. TCP/2 which is actually UDP)

<a name="microservice-pipeline-architecture"></a>
Microservice Pipeline Architecture
----------------------------------
_[Tim Perry](https://twitter.com/pimterry)_

[Slides](https://speakerdeck.com/pimterry/microservice-pipeline-architecture)

### Sync vs. async:

Sync:
```
Content delivery service -> ... -> content services
```

Async:
```
Render + serve -> DB <- load data <- content services
```

Renderers are data loaders, they may be parallelized.

`Render + serve` part may be broken down to a composer + broker. Web requires responses from broker being
combined into a single (or few) responses.

Ruby implementation: [Alephant](https://github.com/bbc-news/alephant).

<a name="the-state-of-serverless"></a>
The State Of Serverless
-----------------------
_[Austen Collins](https://twitter.com/austencollins)_

AWS Lambda:
- microservices
- event-driven
- zero-administraction
- easy access to AWS services
- pay-per-execution

<a name="low-res-nlp-for-your-everyday-life"></a>
Low-res NLP for your everyday life
----------------------------------
_[Duretti Hirpa](https://twitter.com/duretti)_

Humans are exceptionally good at contexts. Computers are not, but they can learn recognizing contexts
if provided with enough data.

* Tokenizing words is hard, especially for such languages as Chinese or Korean.
* Filter out stop words (do, have, and, etc.)
* DB lookup using the meaningful words combination

**Packages**

* [superscriptjs](http://superscriptjs.com/)/[normalizer](https://www.npmjs.com/package/node-normalizer) - NodeJS package for text normalizetion
* [Stanford CoreNLP](http://stanfordnlp.github.io/CoreNLP/) - tokenizer
* [howdy.ai](https://howdy.ai/) - a library to do NLP in Slack

<a name="distributed-apps-with-ipfs"></a>
Distributed Apps with IPFS
--------------------------
_[Juan Benet](https://twitter.com/juanbenet)_

[IPFS](https://ipfs.io/) - InterPlanetary File System, a peer-to-peer hypermedia protocol.

* Data is distributed across nodes
* Data is addressable by its hash

[IPFS on GitHub](https://github.com/ipfs/ipfs).

Also I am a cyber wizard in 2016.

<a name="from-rest-to-graphql"></a>
From REST to GraphQL
--------------------
_[Marc-Andre Giroux](https://twitter.com/__xuorig__)_

* REST
  - Many round-trips to render a page
* Custom endpoints
  - Tends to turn into a mess when adding parameters to a route

### GraphQL

* Lexed
* Parsed
* Validated
* Executed

An example query
```
{
    myShop {
        name
    }
}
```

* GraphQL is typed and can be described as a contract.
* `_schema` in a query body to describe types.
* Query `mutations`

### Drawbacks

* N+1 queries
  Batching + caching
  + Loader throttles the requests to the API and fires them all in one batch.
  + HTTP caching doesn't work because of POST requests. Client-side caching to the rescue.
    - https://github.com/facebook/relay
    - http://appolostack.com
* Security
  + Timeouts
  + Query Depth
  + Query Complexity (weigh the field extraction complexity)

### Future

* Technology-agnostic `subscriptions` (web-sockets, server push, etc.)
* Deferred queries - split up the query and send as soon as the part is complete

<a name="building-a-recommendation-engine-with-machine-learning-techniques"></a>
Building a Recommendation Engine with Machine Learning Techniques
-----------------------------------------------------------------
_[Brian Sam-Bodden](https://twitter.com/bsbodden)_

**Recommendation engine** predicts the level of user interest that a user might have on a product or item.

* Why?
  - To combat the paradox of choice
  - Deal with exmplosion of information coupled with growing user expectations

An **item** is anything that can be represented.

### Approaches

* Collaborative filtering (actions of many)
* Content-based (characteristics)
* Hybrid

### Learning ML

* Supervised
  - Classification
  - Regression
* Unsupervised
  - Grouping
  - Anomaly Detection

* Get the big picture first
* Play with small code samples
* Focus on the [meaningful] data first (and algorithms too!)

[Rapidminer](https://rapidminer.com/) - predictive analytics platform.

### Collaborative filtering

* Collecting feedback
  - Explicitly - likes, rating, etc.
  - Implicitly - purchase, search, browse

#### User-based collabortive filtering

* Relies on finding similar users
* Similarity based on items in common
* Use similar user ratings to predict rating

**k-NN algorithm** - prediction is the average of the neighbors.

##### Drawbacks

* Performs poorly if not enough ratings
* Expensive to compute similarity between all users
* Needs to be recalculated if users' tastes change

#### Item-based filtering

* Recommendations as a ML problem
* Classify items according to their content
* Recommend items that match users' interests

##### Advantages

* Items do not tend to change
* Smaller data set
* Faster to compute, cheaper to store

### Using world knowledge

Use Wikipedia as a knowledge repository! Lots of items are already factorized (categories, tags, etc.).
