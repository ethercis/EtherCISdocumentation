---
title: EtherCIS Architecture
keywords: EtherCIS
sidebar: EtherCIS_sidebar
permalink: architecture.html
folder: etherCIS
---

## Architecture

To implement EtherCIS we have been following openEHR's [Service Model](http://www.openehr.org/releases/BASE/latest/docs/architecture_overview/architecture_overview.html#_service_model_sm), hence the architectural approach is Service Oriented Architecture (SOA). Currently, EtherCIS is an application server but can be easily adapted to a micro-service environment according to site requirements.

Each specialized service, whether service or business oriented, is contained in its own library. A service registers itself a startup into a local service registry. The service registry is used by services to resolve dependencies. For example, Composition Service (the service responsible to perform persistence operations on  openEHR RM Composition objects) uses the Resource Access Service (the service responsible to access the DB backend and the Knowledge Service) by resolving it from the service registry.

 

See this diagram here;
![dddd](https://github.com/ethercis/EtherCISdocumentation/tree/gh-pages/images/helpapi-01.png)

<img src="../../images/helpapi-01.png" alt="hi" class="inline"/>


See this document here;
[Document]

