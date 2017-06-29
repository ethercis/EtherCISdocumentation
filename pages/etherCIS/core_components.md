---
title: Core Components
keywords: EtherCIS
sidebar: EtherCIS_sidebar
permalink: core_components.html
folder: etherCIS
filename: core_components.md
---

## EtherCIS Components
> One can only display complex information in the mind. Like seeing, movement or flow or alteration of view is more important than the static picture, no matter how lovely
> -- <citation>Alan Perlis, Epigrams in Programming</citation>

On a high level point of view, EtherCIS main components are depicted as follows:

![Components](http://docs.ethercis.org/images/components_1.png)

- [JETTY](http://www.eclipse.org/jetty/): is the HTTP server and servlet container.
- [Shiro](https://shiro.apache.org/): provides a security framework for authentication and authorization
- [JOOQ](https://www.jooq.org/): allows to deal with SQL in a *very* elegant manner
- [DBPC2](https://commons.apache.org/proper/commons-dbcp/) is the JDBC connection pooling
- [JDBC](https://en.wikipedia.org/wiki/Java_Database_Connectivity) is a common database connectivity API
- [POSTGRESQL](https://www.postgresql.org/) is the DB engine by EtherCIS since it does blend relational and schema-less objects queryable by SQL
- Think!EHR this library is a temporary fix (non open source). It supports encoding/decoding into the Think!EHR FLAT JSON format

The following are required PostgreSQL extensions

- [jsquery](https://github.com/postgrespro/jsquery) is an extension allowing to perform JSONB object querying with a comprehensive set of operators
- [temporal_tables](https://pgxn.org/dist/temporal_tables/1.0.0/) this extension provides object versioning as required by openEHR
- [ltree](https://www.postgresql.org/docs/9.4/static/ltree.html) is a native postgresql type to represent tree-like hierarchical objects. It is used to resolve the CONTAINS AQL clause.

## EtherCIS components

EtherCIS modules are distributed in three high level build packages:

- CORE: these modules perform all required openEHR persistence and query operations (AQL).
- VEHR: contains services wrapping the core components into a REST API framework
- ECISQL: this is the experimental implementation to query openEHR RM persisted objects using [GraphQL](http://graphql.org/)

### Core components

The core modules are located in the repository ehrservice

- [core](https://github.com/ethercis/ehrservice/tree/remote-github/core): fundamental operations and encoding of OpenEhr entities
- [ehrdao](https://github.com/ethercis/ehrservice/tree/remote-github/ehrdao): persistence of OpenEhr entities using a mixed model (relational/NoSql)
- [knowledge-cache](https://github.com/ethercis/ehrservice/tree/remote-github/knowledge-cache): caching of OpenEhr knowledge models (operational templates in particular)
- [aql-processor](https://github.com/ethercis/ehrservice/tree/remote-github/aql-processor): two passes SQL translation and query execution
- [jooq-pg](https://github.com/ethercis/ehrservice/tree/remote-github/jooq-pg): utility module, binds ethercis table to jOOQ/Postgresql 9.4
- [transform](https://github.com/ethercis/ehrservice/tree/remote-github/transform): utility to handle db JSON encoded data into RAW json. Since the format is not yet final, this module will be enhanced at a later stage
- [validation](https://github.com/ethercis/ehrservice/tree/remote-github/validation): gather and cache constraints from operational templates. Perform data validation (both structurally and elementary)

### VEHR components

The REST API service framework modules are in repository VirtualEhr

- [Authenticate Service](https://github.com/ethercis/VirtualEhr/tree/master/AuthenticateService): logic associated to users credential (authentication). Uses [Shiro](http://shiro.apache.org/) security framework.
- [Composition Service](https://github.com/ethercis/VirtualEhr/tree/master/CompositionService) deals with all CRUD operation for openEHR RM composition
- [Ehr Service](https://github.com/ethercis/VirtualEhr/tree/master/EhrService) deals with EHR and EHR_STATUS openEHR RM objects
- [Knowledge Service](https://github.com/ethercis/VirtualEhr/tree/master/KnowledgeService) performs operations related to the Knowledge Cache (operational templates)
- [Logon Service](https://github.com/ethercis/VirtualEhr/tree/master/LogonService) handles login/logout and session management. It is associated to the SecurityManager Service
- [ServiceSecurityManager](https://github.com/ethercis/VirtualEhr/tree/master/LogonService#servicesecuritymanager) does the link with the security framework
- [Party Identifier Service](https://github.com/ethercis/VirtualEhr/tree/master/PartyIdentifiedService) interfaces CRUD operations with an external identity service
- [Query Service](https://github.com/ethercis/VirtualEhr/tree/master/QueryService) is a simple interface to perform SQL and AQL queries
- [Resource Access Service](https://github.com/ethercis/VirtualEhr/tree/master/ResourceAccessService): resources consists in the actual DB backend and Knowledge Cache. This is a system service used by all CRUD and Query interfaces
- [Service Manager](https://github.com/ethercis/VirtualEhr/tree/master/ServiceManager): this is the service manager. It provides the common attributes and methods to all services including session management etc.
- [System Service](https://github.com/ethercis/VirtualEhr/tree/master/SystemService) is a simple CRUD interface to deal with System objects
- [VEhr Service](https://github.com/ethercis/VirtualEhr/tree/master/VEhrService) is the main REST API gateway.

### GraphQL components

[TBC]
