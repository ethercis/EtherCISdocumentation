---
title: Introducing openEHR
keywords: openEHR
sidebar: EtherCIS_sidebar
permalink: openEHR_intro.html
folder: etherCIS
filename: openEHR_intro.md
---

## Intro to openEHR

OpenEHR is a not for profit initiative built on the original GEHR model developed at UCL UK in the early nineties. 

It aims at formalizing the development of clinical concept models (archetypes) that are reused in the information system. To overcome many of the classical EMR approach, OpenEHR provides a simple maintenance path to deal with constant changes of clinical information representation induced by progress in medical research, legal requirements, new or amended protocols and guidelines. OpenEHR keeps up with the representation of the reality thanks using a 2-levels modeling process. 

The modeling process is referred to as “open knowledge engineering”.

2-levels modeling consists of assuming the IT environment as a fixed model of information (invariant) designed to hosts models of content (the knowledge models) using archetypes, templates and user interface definitions. A change occurring in the content models impacts marginally the Information System. This process results in a very short time-to-operation cycle.

![two levels](http://docs.ethercis.org/images/two-levels.png)

### The openEHR Information Model

This information model is based on the modelling of the medical concepts as archetypes. All of these models are maintained in a library of clinical models or CKM (Clinical Knowledge Models).

An archetype is an expression depending on a specific language called ADL, modeling the concept of a delimited set of clinical information, for example the blood pressure. The model specifies how to handle the information following a reference model that defines a number of generic and reusable entities. The entities are then to construct an application model: types and basic structures such as numeric values, dates, time lists, Cardinal integers, coded text, block or cluster of information, structure of document, definition of navigation etc. 
Archetypes are in principle defined by domain experts who provide a complete specification or canonical representation of a concept. Archetypes are published open source, hence supporting the open knowledge engineering principle.

An archetype is a programmatic definition of a concept, in this case in a clinical context, but can be applied to any other field requiring handling of complex information structures with specification rapidly changing. The archetype contains all of the elements relevant to a concept and will be tailored to be used in all scenarios. An archetype definition is submitted in an ergonomic format to a clinician, in the form of structure and mind maps. By construction, a definition gives the structure and specifications of content that is both understandable to a medical specialist and by the information system. On this last point, the archetype is a reusable brick (Lego). An archetype retains the same meaning regardless of its context of use and of the natural language used.

Archetypes are generally built to be stable over time. However, since it is obviously not possible to determine the set of use cases as well as changes in the evolution of clinical knowledge, they are subject to a process of peer reviews and revision control.

Archetypes are used in various combinations or slotted in nested structures in the form of compositions:
The composition is an essential unit in the openEHR architecture.  It can be for example a clinical document, a visit report, an order, a result of laboratory etc.

The compositions are typically organized into sections. A section is similar to a chapter heading in a document. A section has no particular semantic value but is intended to structure the information. 

The sections consist of a set of medical entries (care entry) or administrative or demographic data. 

Examples of sections are:

- Results of medical examinations 
- Allergies list, immunizations
- Vital signs
- Instructions: medications, laboratory analysis
 
An entry can contain several concepts used in full or in parts, as for example:

- Blood pressure
- ECG
- Body mass
- A diagnosis

Medical entries  are defined according to four classes:

- Observation that include information not interpreted as what is directly reported by a patient as the symptoms, events or problems, medical observations, measures and results etc.
- Evaluations that support interpretations, opinions or summaries. They cover for example assessment of severity, diagnosis, set objectives, reactions to substances.
- Instructions and Actions are used to represent events that must take place in the future such as taking medications, or an appointment. The actions describe what is resulting for example to indicate the state of completion of an appointment or compliance with a medication schedule.

Archetypes support the notion of terminology binding :

1. Link with an external base terminology such as LOINC and SNOMED CT 
2. Translation of a term used in the model. This property is used in EtherCIS solutions as models are written in 
English and are translated into French within the model 

Finally, the archetypes are intended to be used in Templates defining a particular use case such as a form, a table of results, a chart, a message, or a report. The templates also contain a set of contextual meta-data allowing the identification of the source of the information, the date of creation etc. Templates define specific guidelines for the use of archetypes that they contain (cardinality in particular).

Structuring Archetypes-Template is shown below:

![archetypes templates](http://docs.ethercis.org/images/archetypes-template.png)

The General provisions regarding security of data according to the openEHR model are:

- Resilience: The data entered in the patient record cannot be deleted. A logical deletion of the information is done by marking it as deleted and transferred it in a historical transactions journal. All data is maintained in the system in the form of contribution revisions.
- Audit Trail: Any change in the patient record is recorded in an audit system 
- Anonymity: The container of medical data is separated from the patient administrative data which could allow identification. This separation ensures that in the event of compromise of the support of medical data it is not possible to retrieve the identity of a patient.

A lot of in-depth materials are on the official openEHR [site](http://www.openehr.org/)
