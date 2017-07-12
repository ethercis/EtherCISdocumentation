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
- AQL is the preferred mode to perform interoperable queries. AQL queries can be used seamlessly to query arbitrary openEHR implementation (hence the vendor neutral characteristic). See [AQL specification](http://www.openehr.org/releases/QUERY/latest/AQL.html) for syntax.
- GraphQL is experimental at this stage (more details on this soon)

### Example

#### SQL

	POST host:port/rest/v1/query 

Using the REST API, querying using SQL requires to specify the sql nature of the query in the POST body:

	{"sql":
	"SELECT ROW_NUMBER() OVER (ORDER BY sys_transaction DESC ) AS version_id, 
		id, 
		sys_transaction 
	FROM 
		ehr.entry_history 
	WHERE composition_id = '755bdd6f-3d73-4207-bb0c-16713f5a71b0'"
	}


#### AQL


	POST host:port/rest/v1/query

Likewise, to specify an AQL query, the expression must be tagged as an aql expression:

	{"aql":
	"
		select distinct e/ehr_id/value as ehrId 
		from EHR e 
		contains COMPOSITION a 
		contains EVALUATION a_a[openEHR-EHR-EVALUATION.adverse_reaction_risk.v1] 
		where a/name/value='Adverse reaction list' and a_a/data[at0001]/items[at0002]/value/value ilike '%peanut%'
	"
	}

#### AQL to SQL

To make it more intuitive, the SQL query corresponding to an AQL expression can be found using the `explain=true` query parameter:


	POST host:port/rest/v1/query?explain=true

With the above AQL query, the corresponding expression is returned:

	{
	    "explain": [
	        [
	            "(\n  select DISTINCT \"ehr_join\".\"id\" as \"ehrId\"\n  from \"ehr\".\"entry\"\n    join \"ehr\".\"composition\" as \"composition_join\"\n    on \"composition_join\".\"id\" = \"ehr\".\"entry\".\"composition_id\"\n    join \"ehr\".\"ehr\" as \"ehr_join\"\n    on \"ehr_join\".\"id\" = \"composition_join\".\"ehr_id\"\n  where (\n    \"ehr\".\"entry\".\"template_id\" = ?\n    and ((SELECT trim(LEADING '''' FROM (trim(TRAILING ''']' FROM\n (regexp_split_to_array((select root_json_key from jsonb_object_keys(\"ehr\".\"entry\".\"entry\") root_json_key where root_json_key like '/composition%'), 'and name/value=')) [2]))))='Adverse reaction list')\n    and (\"ehr\".\"entry\".\"entry\" #>> '{/composition[openEHR-EHR-COMPOSITION.adverse_reaction_list.v1 and name/value=''Adverse reaction list''],/content[openEHR-EHR-SECTION.allergies_adverse_reactions_rcp.v1],0,/items[openEHR-EHR-EVALUATION.adverse_reaction_risk.v1],0,/data[at0001],/items[at0002],0,/value,value}'ilike'%peanut%')\n  )\n)\nunion (\n  select DISTINCT \"ehr_join\".\"id\" as \"ehrId\"\n  from \"ehr\".\"entry\"\n    join \"ehr\".\"composition\" as \"composition_join\"\n    on \"composition_join\".\"id\" = \"ehr\".\"entry\".\"composition_id\"\n    join \"ehr\".\"ehr\" as \"ehr_join\"\n    on \"ehr_join\".\"id\" = \"composition_join\".\"ehr_id\"\n  where (\n    \"ehr\".\"entry\".\"template_id\" = ?\n    and ((SELECT trim(LEADING '''' FROM (trim(TRAILING ''']' FROM\n (regexp_split_to_array((select root_json_key from jsonb_object_keys(\"ehr\".\"entry\".\"entry\") root_json_key where root_json_key like '/composition%'), 'and name/value=')) [2]))))='Adverse reaction list')\n    and (\"ehr\".\"entry\".\"entry\" #>> '{/composition[openEHR-EHR-COMPOSITION.adverse_reaction_list.v1 and name/value=''Adverse reaction list''],/content[openEHR-EHR-EVALUATION.adverse_reaction_risk.v1],0,/data[at0001],/items[at0002],0,/value,value}'ilike'%peanut%')\n  )\n)",
	            "IDCR - Adverse Reaction List.v1",
	            "Allergies"
	        ]
	    ],
	    "executedAQL": "\nselect distinct e/ehr_id/value as ehrId from EHR e contains COMPOSITION a contains EVALUATION a_a[openEHR-EHR-EVALUATION.adverse_reaction_risk.v1] where a/name/value='Adverse reaction list' and a_a/data[at0001]/items[at0002]/value/value ilike '%peanut%'"
	}  

Please note the SQL expression is returned as an array. If, due to containment, several sql expression are returned they should be combined with SQL UNION. The array representation provides more flexibility to assemble an SQL query depending on the context.

[TO BE CONTINUED]