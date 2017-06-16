---
title: EtherCIS- Enterprise Data Repositiry
keywords: etherCIS
tags: [openEHR]
sidebar: EtherCIS_sidebar
permalink: index.html
summary: this is the start page for EtherCIS
---

## Overview of EtherCIS
> We want some things to be boringly simple so we can do exciting things with them
-- <citation>John D. Cook</citation>

### Welcome to the EtherCIS Enterprise Grade Data Repository

EtherCIS (Ethereal Clinical Information System) is an Open Source platform compatible with the [openEHR](http://www.openehr.org/) clinical information representation standard. 

EtherCIS supports

- The [core openEHR information model](http://www.openehr.org/releases/RM/latest/docs/index)
- The openEHR specialized query language [AQL](http://www.openehr.org/releases/QUERY/latest/docs/AQL/AQL.html)
- Direct DB querying using SQL
- GraphQL queries (experimental)
- A RESTful API (converging to the proposed [openEHR EHR REST API](http://www.openehr.org/releases/ITS/latest/ehr_restapi.html))
- A Service Architecture aligned with [openEHR services](http://www.openehr.org/releases/BASE/latest/docs/architecture_overview/architecture_overview.html#_service_model_sm)  recommendation

EtherCIS uses extensively the PostgreSQL database not only for persistence but also to implement openEHR essential capabilities including

- Archetype Model ([AOM](http://www.openehr.org/releases/AM/latest/docs/AOM1.4/AOM1.4.html))
- [Template](http://www.openehr.org/releases/BASE/latest/docs/architecture_overview/architecture_overview.html#_archetypes_and_templates) Model
- Locatable Objects ([openEHR Path Syntax](http://www.openehr.org/releases/BASE/latest/docs/architecture_overview/architecture_overview.html#_paths_and_locators))
- Versioned Objects ([openEHR versioning](http://www.openehr.org/releases/BASE/latest/docs/architecture_overview/architecture_overview.html#_versioning_2))    

Other EtherCIS features include

- Validation of data based on openEHR constraint model
- Remote monitoring of the platform using
	- [Webmin](http://www.webmin.com/)
	- [JMX](http://www.oracle.com/technetwork/articles/java/javamanagement-140525.html) (NB. since Oracle strategy on JMX is not quite clear, a JMX compatible alternative is planned)
- Pre-packaged Docker container

At the time of this writing, EtherCIS security and confidentiality has been left open since it is strongly site dependent. Nevertheless, authentication and authorization is implemented to support various schemes.

The same remark applies to demographics.

EtherCIS is available under [Apache 2.0 license](https://www.apache.org/licenses/LICENSE-2.0).
