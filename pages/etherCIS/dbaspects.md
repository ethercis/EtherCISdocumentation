---
title: Database aspects
keywords: EtherCIS
sidebar: EtherCIS_sidebar
permalink: dbaspects.html
folder: etherCIS
filename: dbaspects.md
---

## Database aspects

Data is maintained under the PostgreSQL database engine. The data are segmented into:

1. Patient Demographics
2. Time bound data (appointments, tasks) supervised by a scheduler
3. Clinical data under a mixed representation relational/schema-less

With the openEHR model we have a strict separation between administrative data and clinical data in two separate databases. 

In real life deployments, patients' administrative data is maintained into a Master Patient Index (MPI) consolidating, under the same identity, dispersed identity sources. 

Clinical data are maintained using a hybrid model SQL and JSON (document). As stated above there is no explicit reference to the administrative data of the patient in these data.

### Hybrid data representation
The used model achieves the integration in the same database engine of both relational data and structured data. This new possibility is provided by PostgreSQL 9.4 with the datatype JSONB. This Jason type allows performing complex queries from the DB engine itself.

The DB has the following structure (simplified diagram) :

![hybrid data](http://docs.ethercis.org/images/db-struct.png)

The column 'entry' in the table 'entry' is of the jsonb data type which is a binary representation of JSON (e.g. it is not a BLOB) support indexing as well as a rich set of operators specific to this type. 

A jsonb entry is for example as follows:

		{
		  "/composition[openEHR-EHR-COMPOSITION.medication_action.v0 and name/value='Medication action']": {
		    "/content[openEHR-EHR-ACTION.medication.v1]": [
		      {
		        "/time": {
		          "/name": "Medication action",
		          "/value": {
		            "value": "2016-09-22T15:15:05.834+07:00",
		            "epoch_offset": 1474532105834
		          },
		          "/$PATH$": "/content[openEHR-EHR-ACTION.medication.v1 and name/value='Medication action']/time",
		          "/$CLASS$": "DvDateTime"
		        },
		        "/ism_transition/careflow_step": {
		          "/name": "Medication action",
		          "/value": {
		            "value": "Administer",
		            "definingCode": {
		              "codeString": "at0006",
		              "terminologyId": {
		                "name": "local",
		                "value": "local"
		              }
		            }
		          },


The benefit is that this entry can be queried directly using PostgreSQL JSONB SQL extension. 

A SQL query is similar to:

		SELECT ehr.entry.composition_id as uid,
			ehr.entry.entry #>> '{/composition[openEHR-EHR-COMPOSITION.care_summary.v0 and name/value=''Procedures list''], /content[openEHR-EHR-SECTION.procedures_rcp.v1],0, /items[openEHR-EHR-ACTION.procedure.v1],0,/description[at0001],/items[at0002 and name/value=''Procedure name''],/value,value}' as procedure_name,
			ehr.entry.entry #>> '{/composition[openEHR-EHR-COMPOSITION.care_summary.v0 and name/value=''Procedures list''], /content[openEHR-EHR-SECTION.procedures_rcp.v1],0, /items[openEHR-EHR-ACTION.procedure.v1],0,/time,/value,value}' as procedure_datetime,
			ehr.event_context.start_time
		FROM ehr.entry
			INNER JOIN ehr.composition ON ehr.composition.id = ehr.entry.composition_id
			INNER JOIN ehr.event_context ON ehr.event_context.composition_id = ehr.entry.composition_id
		WHERE ehr.composition.ehr_id = 'cc005363-e114-41a2-8662-374417fdc5c8' AND ehr.entry.archetype_Id = 'openEHR-EHR-COMPOSITION.care_summary.v0'
		ORDER BY ehr.event_context.start_time DESC;

Having both relational and json data queried within a single SQL query allows to analyze and optimize the query, set indexes etc. 

#### AQL to SQL
 
Since these SQL expressions are rather complex, openEHR AQL can be used instead.

EtherCIS implements an AQL to SQL translator. The benefit is that an AQL query is formatted and delegated to PostgreSQL engine. This avoids multiple I/Os with the DB engine which is a major drawback in terms of performance.

For example, an AQL query:

	select 
		a/uid/value as uid, 
		a/context/start_time/value as date_created, 
		a_a/data[at0001]/events[at0002]/data[at0003]/items[at0005]/value/value as test_name, 
		a_a/data[at0001]/events[at0002]/data[at0003]/items[at0075]/value/value as sample_taken, 
		c/items[at0002]/items[at0001]/value/name as what, 
		c/items[at0002]/items[at0001]/value/value/magnitude as value, 
		c/items[at0002]/items[at0001]/value/value/units as units 
	from EHR e 
		contains COMPOSITION a[openEHR-EHR-COMPOSITION.report-result.v1] 
		contains OBSERVATION a_a[openEHR-EHR-OBSERVATION.laboratory_test.v0] 
		contains CLUSTER c[openEHR-EHR-CLUSTER.laboratory_test_panel.v0]
	where 
		a/name/value='Laboratory test report' 
		AND e/ehr_status/subject/external_ref/id/value = '9999999000'

Is SQL transformed as

	select 
	  "ehr"."comp_expand"."composition_id"||'::'||'test-server'||'::'||(
	  select (count(*) + 1)
	  from "ehr"."composition_history"
	  where "ehr"."composition_history"."id" = '4e607cd7-1f21-4d16-b14b-4239f48b9b04'
	) as "uid", 
	  to_char("ehr"."comp_expand"."start_time",'YYYY-MM-DD"T"HH24:MI:SS')||"ehr"."comp_expand"."start_time_tzid" as "date_created", 
	  "ehr"."comp_expand"."entry"->(select json_object_keys("ehr"."comp_expand"."entry"::json)) #>> '{/content[openEHR-EHR-OBSERVATION.laboratory_test.v0],0,/data[at0001],/events,/events[at0002],0,/data[at0003],/items[at0005],0,/value,value}' as "test_name", 
	  "ehr"."comp_expand"."entry"->(select json_object_keys("ehr"."comp_expand"."entry"::json)) #>> '{/content[openEHR-EHR-OBSERVATION.laboratory_test.v0],0,/data[at0001],/events,/events[at0002],0,/data[at0003],/items[at0075],0,/value,value}' as "sample_taken", 
	  "ehr"."comp_expand"."entry"->(select json_object_keys("ehr"."comp_expand"."entry"::json)) #>> '{/content[openEHR-EHR-OBSERVATION.laboratory_test.v0],0,/data[at0001],/events,/events[at0002],0,/data[at0003],/items[openEHR-EHR-CLUSTER.laboratory_test_panel.v0],0,/items[at0002],0,/items[at0001],0,/value,/name}' as "what", 
	  "ehr"."comp_expand"."entry"->(select json_object_keys("ehr"."comp_expand"."entry"::json)) #>> '{/content[openEHR-EHR-OBSERVATION.laboratory_test.v0],0,/data[at0001],/events,/events[at0002],0,/data[at0003],/items[openEHR-EHR-CLUSTER.laboratory_test_panel.v0],0,/items[at0002],0,/items[at0001],0,/value,/value,magnitude}' as "value", 
	  "ehr"."comp_expand"."entry"->(select json_object_keys("ehr"."comp_expand"."entry"::json)) #>> '{/content[openEHR-EHR-OBSERVATION.laboratory_test.v0],0,/data[at0001],/events,/events[at0002],0,/data[at0003],/items[openEHR-EHR-CLUSTER.laboratory_test_panel.v0],0,/items[at0002],0,/items[at0001],0,/value,/value,units}' as "units"
	from "ehr"."comp_expand"
	where (
	  "ehr"."comp_expand"."composition_id" = '4e607cd7-1f21-4d16-b14b-4239f48b9b04'
	  and ("ehr"."comp_expand"."composition_name"='Laboratory test report')
	  and ("ehr"."comp_expand"."subject_externalref_id_value"='9999999000')
	)

The resulting dataset is similar to
<pre><code>
+--------------------------------------------------+-------------------------+---------------------------------------------+-----------------------------+-------------------------+-----+------+
|uid                                               |date_created             |test_name                                    |sample_taken                 |what                     |value|units |
+--------------------------------------------------+-------------------------+---------------------------------------------+-----------------------------+-------------------------+-----+------+
|55280300-9031-4390-8a90-a4e34feb51f1::test-serv...|2015-05-12T06:17:09+02:00|hepatic function panel                       |2015-05-11T00:13:24.518+02:00|total protein measurement|67.0 |g/l   |
|810272ac-28e8-4928-b61b-79dcef4b4170::test-serv...|2015-03-22T06:11:02+02:00|Urea, electrolytes and creatinine measurement|2015-02-22T00:11:02.518+02:00|Urea                     |7.4  |mmol/l|
|a601a3df-cfea-4cb8-9b82-5737522b52c4::test-serv...|2015-04-15T06:11:02+02:00|Urea, electrolytes and creatinine measurement|2015-04-10T00:11:02.518+02:00|Urea                     |3.6  |mmol/l|
|b97e9fde-d994-4874-b671-8b1cd81b811c::test-serv...|2015-07-23T06:11:02+02:00|Urea, electrolytes and creatinine measurement|2015-07-23T00:11:02.518+02:00|Urea                     |6.7  |mmol/l|
|e54ffbfe-969e-4cae-bc5e-4850b298f5a4::test-serv...|2015-08-19T06:11:02+02:00|complete blood count                         |2015-08-25T00:11:02.518+02:00|white blood cell count   |13.6 |10*9/l|
+--------------------------------------------------+-------------------------+---------------------------------------------+-----------------------------+-------------------------+-----+------+
	</code></pre>

#### Data revision

The history of versions is maintained by a bi-temporal table model (http://en.wikipedia.org/wiki/Bitemporal_data)

#### Unstructured data

Since the actual data representation is SQL based, unstructured data (narrative texts) are easily supported, indexed and queried.

[TO BE CONTINUED] 

## DB administration concepts

EtherCIS DB consists in three types of relational tables

- openEHR RM persistence: where the actual compositions, EHR data are actually stored
- Template-headings: reflect the associations between operational templates and *headings* used to build cache summaries
- openEHR static data: these are tables containing references including: concepts, languages and territories codifications

### Backup and Restore

Make sure you perform a DB backup prior cleaning up the DB.

Backup using postgres is done using `pg_dump` . It is generally located at:

	/usr/bin/pg_dump

The actual options used are the following

	/usr/bin/pg_dump 
		--host 192.168.2.113 
		--port 5432 
		--username "postgres" 
		--no-password  
		--format custom 
		--blobs
		--verbose 
		--file "<destination backup file path with extension>" 
		--schema "ehr" "ethercis"

NB. format **custom** is the recommended format. 

On a remote server, using from a shell

	 pg_dump --host localhost --port 5432 --username "postgres" --no-password --format custom --blobs --verbose --file ./ethercis-170612.custom.bak --schema "ehr" "ethercis"

See pg_dump [documentation](https://www.postgresql.org/docs/current/static/app-pgdump.html) for more details.

Reciprocally, to restore the DB, we use `pg_restore` for instance with the following options:

	/usr/bin/pg_restore 
		--host localhost 
		--port 5434 
		--username "postgres" 
		--dbname "ethercis" 
		--no-password  
		--section data 
		--disable-triggers 
		--no-data-for-failed-tables 
		--verbose 
		"<backup file path with extension>"


The following parameters are useful

- **section data**: direct to restore data only (eg. no schema, functions etc.)
- **disable-triggers**: do not enforce referential integrity during restore
- **no-data-for-failed-tables**: do not complain if data are duplicated for instance (static data tables f.e.)

See pg_restore [documentation](https://www.postgresql.org/docs/current/static/app-pgrestore.html) for more details.

### Cleaning up the DB

Since the clean-up consists in deleting openEHR RM persisted data, simple SQL commands can be used to invoke function purge_db()


#### Example with PostMan

	{"sql":
		"
		SELECT * from ehr.purge_db();
		"
	}

Should return (warning: a bit slow...)

	{
	    "executedSQL": "\r\n\tSELECT * from ehr.purge_db();\r\n\t",
	    "resultSet": [
	        {
	            "purge_db": "Persisted RM data deleted..."
	        }
	    ]
	}

#### From a shell

 	#psql -U postgres -c "select * from ehr.purge_db()" ethercis

#### From Webmin (if applicable)

Access Postgresql server plugin:

`Servers->PostgreSQL Database`

Select database ethercis

Execute SQL:

`SELECT * from ehr.purge_db();`


#### Note on Function purge_db()

The function uses DDL statement TRUNCATE, this implies it request an exclusive lock on each table. The lock may cause significant delays during the execution. 

To check if the function is properly installed, `psql` can be used

	#psql -U postgres ethercis

	#\df ehr.*

	Schema |                  Name                   | Result data type |                                                          Argument data types                                                           |  Type
	--------+-----------------------------------------+------------------+----------------------------------------------------------------------------------------------------------------------------------------+---------
	 ehr    | cache_summary_delete_trigger            | trigger          |                                                                                                                                        | trigger
	 ehr    | cache_summary_update_insert_trigger     | trigger          |                                                                                                                                        | trigger
	 ehr    | create_items_dv_count                   | json             | text, bigint, text                                                                                                                     | normal
	 ehr    | create_items_dv_date_time               | json             | text, timestamp with time zone, text                                                                                                   | normal
	 ehr    | encode_node_name                        | json             | text                                                                                                                                   | normal
	 ehr    | entry_versioning_trigger                | trigger          |                                                                                                                                        | trigger
	 ehr    | event_context_versioning_delete_trigger | trigger          |                                                                                                                                        | trigger
	 ehr    | event_context_versioning_upsert_trigger | trigger          |                                                                                                                                        | trigger
	 ehr    | get_cache_committer                     | uuid             |                                                                                                                                        | normal
	 ehr    | get_cache_system                        | uuid             |                                                                                                                                        | normal
	 ehr    | init_other_context                      | void             |                                                                                                                                        | normal
	 ehr    | is_event_context_summary_cache          | boolean          | uuid                                                                                                                                   | normal
	 ehr    | other_context                           | jsonb            |                                                                                                                                        | normal
	 ehr    | other_context_for_ehr                   | jsonb            | uuid                                                                                                                                   | normal
	 ehr    | other_context_summary_field             | jsonb            | timestamp with time zone, bigint, timestamp with time zone, bigint, timestamp with time zone, bigint, timestamp with time zone, bigint | normal
	 ehr    | purge_db                                | text             |                                                                                                                                        | normal
	 ehr    | set_cache_for_ehr                       | uuid             | uuid                                                                                                                                   | normal
	 ehr    | update_cache_for_ehr                    | uuid             | uuid                                                                                                                                   | normal
	(18 rows)

	(to get the function definition)

	# \df+ ehr.purge_db

	CREATE OR REPLACE FUNCTION ehr.purge_db()
	 RETURNS text
	 LANGUAGE plpgsql
	AS $function$   BEGIN     TRUNCATE     ehr.compo_xref,     ehr.containment,     ehr.ehr,     ehr.entry_history,     ehr.composition_history,     ehr.event_context,     ehr.event_context_history,     ehr.participation_history,     ehr.contribution_history,     ehr.status_history,     ehr.party_identified,     ehr.system,     ehr.entry,     ehr.participation,     ehr.event_context,     ehr.party_identified,     ehr.identifier,     ehr.composition,     ehr.contribution CASCADE;     RETURN 'Persisted RM data deleted...';   END   $function$
	
	(NB. This is into VI, exit by <ESC> q!)
