---
title: CoRAL API Discovery
abbrev: CoRAL API Disco
docname: draft-fhj-core-coral-api-disco-latest
category: std

ipr: trust200902
area: ART
workgroup: CoRE Working Group
keyword: CoAP, CoRAL, API, Discovery

stand_alone: yes
pi: [toc, sortrefs, symrefs]

author:
 -
    ins: "T. Fossati"
    name: "Thomas Fossati"
    organization: "arm"
    email: thomas.fossati@arm.com
 -
    ins: "J. Jiménez"
    name: "Jaime Jiménez"
    organization: "Ericsson"
    email: jaime@iki.fi

 -
    ins: "K. Hartke"
    name: "Klaus Hartke"
    organization: "Ericsson"
    email: klaus.hartke@ericsson.com

--- abstract

A common problem when exchanging CoRAL {{!I-D.ietf-core-coral}} documents is
aligning producing and consuming endpoints with regards to the vocabulary and
the associated compression dictionary used.  This memo proposes a set of
mechanisms to allow advertising, discovery and negotiation of such
translation artifacts.

--- middle

# Introduction

A common problem when exchanging CoRAL {{!I-D.ietf-core-coral}} documents is
aligning the producing and consuming endpoints with regards to the vocabulary
and the associated compression dictionary used both in abstract and on the
wire.

For example, entities that consume CoRE problem details
{{?I-D.ietf-core-problem-details}} will typically need to understand the
semantic associated with the compressed elements (in particular, the problem
type code) in order to make sense of the received document.

This memo proposes data formats and protocol interactions that allow:

* Producing endpoints to advertise a vocabulary used when
  interacting with a certain resource ({{sec-advert}});
* Consuming endpoints to discover the above ({{sec-disco}});
* Producers and consumers to negotiate its use in the context of a certain
  interaction ({{sec-nego}}).

## Requirements Language

{::boilerplate bcp14}

# The Full Picture

~~~
Client                          Server          Server/Dict
                                                https://googley.eye/api-dict
 |                               |                    |
 | -(1) GET /.well-known/core -> |                    |
 |                               |                    |
 | <-- 2.05 /config + api-ep --- |                    |
 |                                                    |
 | --------------(2) GET /api-dict -----------------> |
 |                                                    |
 | <------------ gets back cbor-mappings ------------ |
 |                                                    |
 | --(3) POST /config ---------> |                    |
 |                               |                    |
 | <-- 2.04 Changed ------------ |                    |
                               [...]
 | --(n) GET /config ----------> |                    |
 |                               |                    |
 | <-- 2.05 Content ------------ |                    |
~~~
{: #fig-full-picture title="Full Picture"}

# Breakdown

## Adverstising
{: #sec-advert}

~~~
   Link rel=hosts rt=temperature href=/temp1 {
      Link rel=vocab href=/api-dict
~~~
{: #fig-advert title="Advertising"}


## Discovery and Retrieval
{: #sec-disco}

~~~
GET /.well-known/core

   2.05 Content

   Link rel=hosts rt=temperature href=/temp1 {
      Link rel=vocab href=/api-dict
   }
   Link rel=hosts rt=temperature href=/temp2 {
      Link rel=vocab href=/api-dict
   }
   Link rel=hosts rt=humidity href=/hum {
      Link rel=vocab href=/api-dict
   }
   Link rel=hosts rt=configuration href=/config {
      Link rel=vocab href=https://googley.eye/api-dict
   }
~~~
{: #fig-disco-vocabs title="Vocabulary Discovery"}

~~~
GET /api-dict

2.05 Content
ETag: 12345

| Count | Namespace                  |
|:-----:|:-------------------------- |
|  100  | "ACE group management"     |
~~~
{: #fig-disco-vocab title="Vocabulary Retrieval"}

## Negotiation
{: #sec-nego}

~~~
POST /config
Link rel=vocab href=/api-dict etag=12345

Setting key=@72 value=false
Setting key=@73 value=15
Setting key=@74 value=@75

2.04 Changed
~~~
{: #fig-nego title="Negotiation"}


# Security Considerations

TODO elaborate on the following:

* https://owasp.org/www-community/vulnerabilities/XML_External_Entity_(XXE)_Processing

* https://cwe.mitre.org/data/definitions/827.html


# IANA Considerations

--- back

# Acknowledgments
{: numbered="no"}

TODO
