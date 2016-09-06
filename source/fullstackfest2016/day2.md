---
title: 'Fullstack Fest 2016. Day 2'
---

Fullstack Fest 2016
===================

_September 6, 2016, Barcelona, Spain_

1. [How to write a search engine in 15 lines of code](#how-to-write-a-search-engine-in-15-lines-of-code)
2. [Whirlwind tour through the HTTP2 spec](#whirlwind-tour-through-the-http2-spec)

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
