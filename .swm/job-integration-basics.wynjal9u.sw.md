---
title: Job Integration Basics
---
# Overview of Job Integration

Job Integration in Job Management is the process that handles job-related data operations within the application. It primarily focuses on managing the insertion of new job records and the retrieval of existing job records from the database, ensuring smooth data flow between the application and the database.

# Purpose of Job Integration

The main purpose of Job Integration is to facilitate efficient communication between the application and the database for job data management. This includes supporting bulk insertion of multiple job entries to optimize performance and enabling precise retrieval of job details based on specific criteria.

# Implementation in the Codebase

Job Integration is implemented through two main flows located in the <SwmPath>[src/…/mule/job.xml](src/main/mule/job.xml)</SwmPath> file: <SwmToken path="src/main/mule/job.xml" pos="11:7:7" line-data="	&lt;flow name=&quot;jobInsert&quot; doc:id=&quot;e136c434-10be-4b46-8043-57344343ccaa&quot; &gt;">`jobInsert`</SwmToken> and <SwmToken path="src/main/mule/job.xml" pos="40:7:7" line-data="	&lt;flow name=&quot;jobGet&quot; doc:id=&quot;ff4dc4f5-8532-4493-a569-cbfd448e8da4&quot; &gt;">`jobGet`</SwmToken>. These flows encapsulate the logic for inserting and fetching job data respectively.

<SwmSnippet path="/src/main/mule/job.xml" line="11">

---

The <SwmToken path="src/main/mule/job.xml" pos="11:7:7" line-data="	&lt;flow name=&quot;jobInsert&quot; doc:id=&quot;e136c434-10be-4b46-8043-57344343ccaa&quot; &gt;">`jobInsert`</SwmToken> flow receives incoming job data and transforms it into a structured JSON payload. This payload contains an SQL insert statement along with a mapped list of job entries prepared for bulk insertion. The flow then executes a <SwmToken path="src/main/mule/job.xml" pos="33:4:6" line-data="		&lt;db:bulk-insert doc:name=&quot;Bulk insert&quot; doc:id=&quot;8f2a4472-997e-45c9-8e21-1d6f39a4c4f4&quot; config-ref=&quot;Database_Config&quot;&gt;">`bulk-insert`</SwmToken> database operation to add multiple job records efficiently. After the insertion completes, a logger confirms the successful operation.

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
```

---

</SwmSnippet>

# The <SwmToken path="src/main/mule/job.xml" pos="40:7:7" line-data="	&lt;flow name=&quot;jobGet&quot; doc:id=&quot;ff4dc4f5-8532-4493-a569-cbfd448e8da4&quot; &gt;">`jobGet`</SwmToken> Flow

The <SwmToken path="src/main/mule/job.xml" pos="40:7:7" line-data="	&lt;flow name=&quot;jobGet&quot; doc:id=&quot;ff4dc4f5-8532-4493-a569-cbfd448e8da4&quot; &gt;">`jobGet`</SwmToken> flow is responsible for retrieving job data. It constructs a select query using a job code variable to fetch a specific job record from the database. After executing the query, the flow transforms the result into JSON format, making it ready for further processing or response.

# Data Flow in Job Integration

When inserting jobs, the application sends job data to the <SwmToken path="src/main/mule/job.xml" pos="11:7:7" line-data="	&lt;flow name=&quot;jobInsert&quot; doc:id=&quot;e136c434-10be-4b46-8043-57344343ccaa&quot; &gt;">`jobInsert`</SwmToken> flow, which prepares and executes a bulk insert operation. For retrieval, the application invokes the <SwmToken path="src/main/mule/job.xml" pos="40:7:7" line-data="	&lt;flow name=&quot;jobGet&quot; doc:id=&quot;ff4dc4f5-8532-4493-a569-cbfd448e8da4&quot; &gt;">`jobGet`</SwmToken> flow with a job code, which queries the database and returns the job details in JSON format. This design ensures efficient and reliable job data management.

# Summary

In summary, Job Integration in this application is centered around two Mule flows that manage job data insertion and retrieval. The <SwmToken path="src/main/mule/job.xml" pos="11:7:7" line-data="	&lt;flow name=&quot;jobInsert&quot; doc:id=&quot;e136c434-10be-4b46-8043-57344343ccaa&quot; &gt;">`jobInsert`</SwmToken> flow handles bulk insertion of job records, while the <SwmToken path="src/main/mule/job.xml" pos="40:7:7" line-data="	&lt;flow name=&quot;jobGet&quot; doc:id=&quot;ff4dc4f5-8532-4493-a569-cbfd448e8da4&quot; &gt;">`jobGet`</SwmToken> flow fetches job details based on a job code. Together, they provide seamless integration between the application and the database for job management.

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBbXVsZS1kZW1vLWRhdGFiYXNlLWFwcCUzQSUzQXVtYWxpbmdhc3dhbWk=" repo-name="mule-demo-database-app"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
