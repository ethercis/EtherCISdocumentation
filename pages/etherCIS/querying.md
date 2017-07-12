---
title: Querying in EtherCIS
keywords: SQL, AQL
sidebar: EtherCIS_sidebar
permalink: querying.html
folder: etherCIS
filename: querying.md
---

## Querying within EtherCIS

EtherCIS supports 3 query models:

- SQL to perform direct queries to the DB. This is a very fast querying mode, but strongly coupled to PostgreSQL. It is essentially non portable across multiple openEHR implementations. This should be reserved to perform specialized reporting, KPIs building etc.
- AQL is the preferred mode to perform interoperable queries. AQL queries can be used seamlessly to query arbitrary openEHR implementation (hence the vendor neutral characteristic)
- GraphQL is experimental at this stage (more details on this soon)

[TO BE CONTINUED]