---
title: Service Wrappers
keywords: EtherCIS
sidebar: EtherCIS_sidebar
permalink: servicewraps.html
folder: etherCIS
filename: servicewrappers.md
---

## Service Wrappers

The services and framework are located in VirtualEhr

ServiceManager service management framework
VEhrService Query gateway of a running instance
ResourceAccessService a common service to access external resources (DB, knowledge etc.)
PartyIdentifiedService wrapper to interact with OpenEhr PartyIdentified entities
LogonService controls user login/logout and sessions
AuthenticationService wrap a security policy provider
CacheKnowledgeService wrapper of knowledge-cache to allow user queries
EhrService deals with user queries on OpenEhr Ehr and Ehr Status objects
CompositionService deals with user queries on Composition objects

Please refer to the respective component's README for more details on the above
