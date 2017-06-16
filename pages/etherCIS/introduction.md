---
title: Introducing EtherCIS
keywords: EtherCIS
sidebar: EtherCIS_sidebar
permalink: introduction.html
folder: etherCIS
---

## Welcome to EtherCIS

>How can we explain the fact that, of the thousand products of our unconscious activity, some are invited to cross the threshold, while others remain outside? Is it mere chance that gives them this privilege? Evidently not...

--- <cite>Henri Poincaré, La science et l'hypothèse, 1902</cite>

### EtherCIS Mission Statement

#### Why openEHR?

Whenever dealing the first time with Clinical Data, it is rather easy to underestimate the complexity, richness and depth of clinical concepts. A candid browsing exercise in [SNOMED-CT](http://www.snomed.org/snomed-ct) or, better, [UMLS](https://www.nlm.nih.gov/research/umls/) is however a mind boggling exercise, helping if needed to understand that most traditional approaches are poised to fail. Unfortunately, IT professionals learn this reality through significantly costly failures.

Back in 2010, during an initial attempt to implement a tele-medicine application, we evaluated various candidates both commercial and open source. All of the platforms were using rather rigid information models from Form Architecture, Document stores to plain SQL associated with binary objects. No data can be exploited in a structured manner (a critical requirement since we the system aimed at assessing severity of problems, detects adverse reaction, calculate various treatment indicators and present the data in a meaningful, time based way etc.)

The solution was indeed to look at a way to dynamically insert a structured data model into the backend store ideally from a schema. Schema based data representation is nothing new and has been implemented in large systems such as [CODASYL](https://en.wikipedia.org/wiki/CODASYL) compliant [IDS/2](https://en.wikipedia.org/wiki/Integrated_Data_Store). IDS/2 was based on a schema data description language (similar to our current SQL DDL). When compiled, the schema resulted in a hierarchical data model that could be queried with exceptional performance considering the hardware available at that time (mid 70's).

Another significant aspect of this system was that the schema syntax was very close to COBOL. One of the benefits of this - verbose - language is to be easily understood by average users (accounting/finance professionals).   

Back in 2010, the question was to figure out whether a similar approach could be envisioned to implement clinical data, that is: a schema based information model easily understood by its target users (healthcare professional)? We turned on our research to HL7 CDA and openEHR. Having been exposed to HL7v2 we knew already that entering this consortium arena was potentially beyond our reach. Further, HL7 mission is more on data interchange and therefore not fitting our requirements. OpenEHR on another hand did seem to fit nicely in our objectives

- Structured information model: archetype based
- Interoperable (semantically and on a messaging level)
- Easily understood by the medical community (medical concepts are indeed expressed in medical terms)
- Open knowledge: provide reviewed conceptual building bricks

We decided to give it a try. This is discussed below.          

#### Why SQL?

EtherCIS is the result of multiple attempts of implementing openEHR persistence backends using different technologies (OODB, NoSQL, Multi-Dimensional DB, XML). 

Further to achieve the objectives described above, we also expected the clinical data repository (CDR) to

- Support easy usage and administration by in-house DBA without requiring specific tools and/or knowledge
- Insert into existing operational environment (Data Center)
	- backup and disaster recovery strategies
	- application monitoring
	- capacity planning
	- incident management
- Be easily extended with third party data provider (for example demographics)
- Limit roundtrips during queries
- Preserve data integrity 
- Provide a way to quickly generate KPIs and other management reports

Clearly when looking at an implementation candidate based on the above business requirement, SQL seemed to be a valid alternative. 

Whatever today's hype, SQL since its inception back in 1974 has nicely evolved to cope with today's requirements that can be summarized as follows:

- Relational/SQL
- ACID transactions
- Horizontal/Vertical scalability
- Performance on big volume
- Schema-less
- Wealth of new statements and operators (interested reader should check [JOOQ](https://www.jooq.org/),  [Markus Winand](http://use-the-index-luke.com/blog/2015-02/modern-sql) blogs on this)

At this stage, PostgreSQL recent release supports these objectives:

- In memory database is supported and can be tuned depending on the available hardware. PostgreSQL engine performs in memory caching efficiently. It can be deployed to deal with large data volume (Hadoop), integrates with external data sink (FDW).

- The most important feature, on an EtherCIS standpoint, is schema-less support with the JSONB datatype. JSONB is used extensively to project archetyped structures into the DB. The benefit is that relational and schema-less data can be queried using standard modern SQL. Queries can be optimized in various ways and indexing is also supported at schema-less level. JSON objects support is now part of [ISO/IEC 9075:2016](https://share.ansi.org/Shared%20Documents/News%20and%20Publications/Links%20Within%20Stories/SQL%20standard%20published_POST.pdf) (SQL:2016).       

