---
title: EtherCIS Architecture
keywords: EtherCIS
sidebar: EtherCIS_sidebar
permalink: architecture.html
folder: etherCIS
---

## Architecture
> **Numerobis**: And if we fail, we go to crocodiles
> **Obelix**: Are crocodile tasty?
> -- <citation>Asterix & Cleopatra</citation>

To implement EtherCIS we have been following openEHR's [Service Model](http://www.openehr.org/releases/BASE/latest/docs/architecture_overview/architecture_overview.html#_service_model_sm), hence the architectural approach is Service Oriented Architecture (SOA). Currently, EtherCIS is an application server but can be easily adapted to a micro-service environment according to site requirements.

Each specialized service, whether service or business oriented, is contained in its own library. A service registers itself a startup into a local service registry. The service registry is used by services to resolve dependencies. For example, Composition Service (the service responsible to perform persistence operations on  openEHR RM Composition objects) uses the Resource Access Service (the service responsible to access the DB backend and the Knowledge Service) by resolving it from the service registry.

Functionally, EtherCIS consists in a HTTP backend hosting a collection of services. Each service exposed method is invoked from a REST API dispatcher (a service itself). This is at this level that required authentication and authorization are performed. Transactions audit logging is also done at gate level. Each service depending on its specialization is associated with local services as well as external servers, for example a database server. Client queries are encoded depending on the requested format:

- XML
- JSON
- GraphQL

The following diagram depicts this

![Logical View](http://docs.ethercis.org/images/functional_view.jpg)

### Application Server

EtherCIS as an application server can be illustrated as follows:

![Application Server](http://docs.ethercis.org/images/application_server.png)

NB. The above diagram gives a simplified representation of the actual implementation. Interested reader should get EtherCIS source code to understand each service implementation and dependencies.

