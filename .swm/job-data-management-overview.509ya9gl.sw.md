---
title: Job Data Management Overview
---
# Overview of Job Data Management

Job Data Management is responsible for handling the storage and retrieval of job-related information within the application. It ensures that job data is consistently structured, stored, and accessible for other components or services that depend on this information.

# Purpose and Scope

The primary purpose of Job Data Management is to manage the lifecycle of job data, including adding new job entries to the database and retrieving existing job records. This functionality supports the application's need to maintain accurate and up-to-date job information.

# Job Data Insertion Process

The insertion process starts by transforming incoming job data into a structured JSON format. This transformation maps the incoming data fields to the corresponding database columns and prepares a SQL insert query along with a list of job data objects. Using this structured data, a bulk insert operation is performed to efficiently add multiple job records to the job table in the database.

# Job Data Retrieval Process

Retrieval of job data is performed by dynamically constructing a SQL select query using a job code variable. This query fetches the job record that matches the provided job code. The retrieved data is then transformed into a JSON payload. If no matching record is found, the system returns an empty JSON object to maintain consistency and ease of consumption by downstream processes.

# Logging in Job Data Management

Logging is integrated into both the insertion and retrieval workflows to provide transparency and aid in monitoring. After a successful insertion, a log message confirms the completion of the data insert operation. Similarly, after retrieval, a log outputs the job title of the fetched record or indicates 'None' if no data was found. These logs help in debugging and tracking the flow of job data.

<SwmSnippet path="/src/main/mule/job.xml" line="11">

---

The flows <SwmToken path="src/main/mule/job.xml" pos="11:7:7" line-data="	&lt;flow name=&quot;jobInsert&quot; doc:id=&quot;e136c434-10be-4b46-8043-57344343ccaa&quot; &gt;">`jobInsert`</SwmToken> and <SwmToken path="src/main/mule/job.xml" pos="40:7:7" line-data="	&lt;flow name=&quot;jobGet&quot; doc:id=&quot;ff4dc4f5-8532-4493-a569-cbfd448e8da4&quot; &gt;">`jobGet`</SwmToken> defined in <SwmPath>[src/…/mule/job.xml](src/main/mule/job.xml)</SwmPath> illustrate the implementation of job data management. The <SwmToken path="src/main/mule/job.xml" pos="11:7:7" line-data="	&lt;flow name=&quot;jobInsert&quot; doc:id=&quot;e136c434-10be-4b46-8043-57344343ccaa&quot; &gt;">`jobInsert`</SwmToken> flow demonstrates how job data is transformed and bulk inserted into the database, followed by a logger message indicating success. The <SwmToken path="src/main/mule/job.xml" pos="40:7:7" line-data="	&lt;flow name=&quot;jobGet&quot; doc:id=&quot;ff4dc4f5-8532-4493-a569-cbfd448e8da4&quot; &gt;">`jobGet`</SwmToken> flow shows how a job record is selected based on a job code and logged after retrieval, reflecting the retrieval process described.

```xml
	<flow name="jobInsert" doc:id="e136c434-10be-4b46-8043-57344343ccaa" >
		<ee:transform doc:name="Transform Message" doc:id="096c0ad4-3f35-4cb9-b180-1bcbe2f73061" >
			<ee:message >
				<ee:set-payload ><![CDATA[%dw 2.0
output application/json
---
{
	query: "insert into job (jobCode,jobTitle,jobDescription,minQualification,empClass,minSalary,maxSalary,flsaStatus) 
			values (:jobCode,:jobTitle,:jobDescription,:minQualification,:empClass,:minSalary,:maxSalary,:flsaStatus)",
	body: payload map ( payload01 , indexOfPayload01 ) -> {
			jobCode: payload01.jobCode,
			jobTitle: payload01.jobTitle,
			jobDescription: payload01.jobDescription,
			minQualification: payload01.minQualification,
			empClass: payload01.empClass,
			minSalary: payload01.minSalary,
			maxSalary: payload01.maxSalary,
			flsaStatus: payload01.flsaStatus
	}
}]]></ee:set-payload>
			</ee:message>
		</ee:transform>
		<db:bulk-insert doc:name="Bulk insert" doc:id="8f2a4472-997e-45c9-8e21-1d6f39a4c4f4" config-ref="Database_Config">
			<db:bulk-input-parameters ><![CDATA[#[payload.body]]]></db:bulk-input-parameters>
			<db:sql ><![CDATA[#[payload.query]]]></db:sql>
		</db:bulk-insert>
		<logger level="INFO" doc:name="Logger" doc:id="dede0e83-c71b-443b-92ed-9d51fc74a1d0" message="Insert of Data Complete"/>
	</flow>
	
	<flow name="jobGet" doc:id="ff4dc4f5-8532-4493-a569-cbfd448e8da4" >
		<ee:transform doc:name="Transform Message" doc:id="b8ee96a1-1fc7-48d2-80bb-bfae7b660b04" >
			<ee:message >
				<ee:set-payload ><![CDATA[%dw 2.0
output application/java
---
"select * from job where jobCode = '" ++ vars.jobCode ++ "'"]]></ee:set-payload>
			</ee:message>
		</ee:transform>
		<db:select doc:name="Select" doc:id="201fdc74-0c7d-43bf-be23-296a49f1cfc5" config-ref="Database_Config">
			<db:sql ><![CDATA[#[payload]]]></db:sql>
		</db:select>
		<ee:transform doc:name="Transform Message" doc:id="2ee13a23-eddd-47af-a57b-c1bac01e005b" >
			<ee:message >
				<ee:set-payload ><![CDATA[%dw 2.0
output application/json
---
payload[0] default {}]]></ee:set-payload>
			</ee:message>
		</ee:transform>
		<logger level="INFO" doc:name="Logger" doc:id="370287ca-2405-470f-b414-e73158160a28" message='#["Got Data for " ++ (payload.jobTitle default "None")]'/>
	</flow>	
```

---

</SwmSnippet>

This snippet from <SwmPath>[src/…/mule/job.xml](src/main/mule/job.xml)</SwmPath> contains the detailed flows for job data insertion and retrieval, including data transformation, database operations, and logging steps.

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBbXVsZS1kZW1vLWRhdGFiYXNlLWFwcCUzQSUzQXVtYWxpbmdhc3dhbWk=" repo-name="mule-demo-database-app"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
