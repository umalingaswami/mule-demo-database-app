---
title: Basic Concepts of Employee Integration Flows
---
# Overview of Employee Integration Flows

Employee Integration Flows in this repository are MuleSoft flows designed to manage database operations specifically related to employee data. These flows automate key tasks such as inserting multiple employee records and retrieving individual employee details, thereby streamlining interactions with the underlying database.

# Purpose and Benefits

The primary purpose of these integration flows is to efficiently handle employee data operations. Bulk insert operations allow multiple employee records to be inserted in a single database transaction, which reduces overhead and improves performance. Additionally, the flows support precise retrieval of employee information by using unique employee IDs.

<SwmSnippet path="/src/main/mule/employee.xml" line="10">

---

The <SwmToken path="src/main/mule/employee.xml" pos="10:7:7" line-data="	&lt;flow name=&quot;employeeInsert&quot; doc:id=&quot;5265fa9b-6f54-4f7c-8cdd-87642ee588b0&quot; &gt;">`employeeInsert`</SwmToken> flow accepts a payload containing an array of employee objects. This payload is transformed using DataWeave into a structured JSON object that includes an SQL insert query and a mapped list of employee data parameters. This transformed payload is then passed to the database bulk insert component, which executes the insert operation for all employee records in one batch. After the operation completes, a logger confirms the successful insertion.

```xml
	<flow name="employeeInsert" doc:id="5265fa9b-6f54-4f7c-8cdd-87642ee588b0" >
		<ee:transform doc:name="Transform Message" doc:id="d40b196b-fa52-4bc6-81b3-cbd838baf0ff" >
			<ee:message >
				<ee:set-payload ><![CDATA[%dw 2.0
output application/json
---
{
	query: "insert into employee (empId,nationalIDNumber,eName,addressLn,addressCity,addressZIP,addressState,addressCountry,phone,gender,birthDate) 
			values (:empId,:nationalIDNumber,:eName,:addressLn,:addressCity,:addressZIP,:addressState,:addressCountry,:phone,:gender,:birthDate)",
	body: payload map ( payload01 , indexOfPayload01 ) -> {
			empId: payload01.empId,
			nationalIDNumber: payload01.nationalIDNumber,
			eName: payload01.eName,
			addressLn: payload01.addressLn,
			addressCity: payload01.addressCity,
			addressZIP: payload01.addressZIP,
			addressState: payload01.addressState,
			addressCountry: payload01.addressCountry,
			phone: payload01.phone,
			gender: payload01.gender,
			birthDate: payload01.birthDate
	}
}]]></ee:set-payload>
			</ee:message>
		</ee:transform>
		<db:bulk-insert doc:name="Bulk insert" doc:id="0736fde4-aff5-405d-8d34-bd40f644baec" config-ref="Database_Config">
			<db:bulk-input-parameters ><![CDATA[#[payload.body]]]></db:bulk-input-parameters>
			<db:sql ><![CDATA[#[payload.query]]]></db:sql>
		</db:bulk-insert>
		<logger level="INFO" doc:name="Logger" doc:id="c30da949-db08-471a-bc57-65d9b5b7dcf2" message="Insert of Data Complete"/>
	</flow>
```

---

</SwmSnippet>

The snippet from <SwmPath>[src/…/mule/employee.xml](src/main/mule/employee.xml)</SwmPath> shows the DataWeave transformation and the bulk insert database operation within the <SwmToken path="src/main/mule/employee.xml" pos="10:7:7" line-data="	&lt;flow name=&quot;employeeInsert&quot; doc:id=&quot;5265fa9b-6f54-4f7c-8cdd-87642ee588b0&quot; &gt;">`employeeInsert`</SwmToken> flow.

# Retrieval Flow Functionality

The <SwmToken path="src/main/mule/employee.xml" pos="41:7:7" line-data="	&lt;flow name=&quot;employeeGet&quot; doc:id=&quot;8380dba3-0eb4-4aa9-bfb4-182e8cc8a689&quot; &gt;">`employeeGet`</SwmToken> flow is responsible for retrieving employee data by employee ID. The ID is passed as a variable to the flow, which constructs a select SQL query using this ID. The database component executes the query, and the retrieved data is transformed into a single employee record in JSON format. Logging is included to confirm the successful retrieval of the data.

# Usage Instructions

To insert employees, send a payload with an array of employee objects to the <SwmToken path="src/main/mule/employee.xml" pos="10:7:7" line-data="	&lt;flow name=&quot;employeeInsert&quot; doc:id=&quot;5265fa9b-6f54-4f7c-8cdd-87642ee588b0&quot; &gt;">`employeeInsert`</SwmToken> flow. For retrieving an employee, provide the employee ID as a variable to the <SwmToken path="src/main/mule/employee.xml" pos="41:7:7" line-data="	&lt;flow name=&quot;employeeGet&quot; doc:id=&quot;8380dba3-0eb4-4aa9-bfb4-182e8cc8a689&quot; &gt;">`employeeGet`</SwmToken> flow. These flows handle the necessary transformations and database interactions automatically, simplifying the integration process.

# Summary

In summary, the Employee Integration Flows provide efficient mechanisms for bulk inserting employee records and retrieving individual employee details by ID. They leverage DataWeave transformations and database components to perform these operations with logging for traceability.

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBbXVsZS1kZW1vLWRhdGFiYXNlLWFwcCUzQSUzQXVtYWxpbmdhc3dhbWk=" repo-name="mule-demo-database-app"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
