---
title: Employee Management Overview
---
# Employee Management Overview

Employee Management in this application consists of Mule flows that handle operations related to employee data. These flows enable the insertion of new employee records into the database and the retrieval of existing employee information by employee ID.

# Core Functionality

The insertion flow processes incoming payloads containing employee details, transforms them into SQL insert statements, and executes these statements in bulk to efficiently add multiple employee records at once. Key employee attributes such as employee ID, national ID number, name, address, phone number, gender, and birth date are mapped from the payload to the corresponding database columns.

# Retrieving Employee Data

To fetch employee information, the retrieval flow constructs a SQL select query using the provided employee ID, executes the query against the database, and transforms the resulting data into a JSON response. This allows clients to obtain detailed employee records in a structured format.

# Logging and Monitoring

Both insertion and retrieval flows include logging mechanisms that provide informational messages. These logs indicate the successful completion of data insertion and the retrieval of employee data, aiding in monitoring and troubleshooting.

# Implementation Details

The Employee Management flows are implemented in the Mule configuration file located at <SwmPath>[src/…/mule/employee.xml](src/main/mule/employee.xml)</SwmPath>. Specifically, the flows named <SwmToken path="src/main/mule/employee.xml" pos="10:7:7" line-data="	&lt;flow name=&quot;employeeInsert&quot; doc:id=&quot;5265fa9b-6f54-4f7c-8cdd-87642ee588b0&quot; &gt;">`employeeInsert`</SwmToken> and <SwmToken path="src/main/mule/employee.xml" pos="41:7:7" line-data="	&lt;flow name=&quot;employeeGet&quot; doc:id=&quot;8380dba3-0eb4-4aa9-bfb4-182e8cc8a689&quot; &gt;">`employeeGet`</SwmToken> encapsulate the insertion and retrieval logic respectively.

<SwmSnippet path="/src/main/mule/employee.xml" line="10">

---

For example, the <SwmToken path="src/main/mule/employee.xml" pos="10:7:7" line-data="	&lt;flow name=&quot;employeeInsert&quot; doc:id=&quot;5265fa9b-6f54-4f7c-8cdd-87642ee588b0&quot; &gt;">`employeeInsert`</SwmToken> flow accepts a list of employee JSON objects, transforms them into a bulk SQL insert statement, and executes it to add multiple employees in one operation. Similarly, the <SwmToken path="src/main/mule/employee.xml" pos="41:7:7" line-data="	&lt;flow name=&quot;employeeGet&quot; doc:id=&quot;8380dba3-0eb4-4aa9-bfb4-182e8cc8a689&quot; &gt;">`employeeGet`</SwmToken> flow uses the employee ID variable to build a SQL select query, executes it, and returns the first matching employee record as a JSON object.

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
	<flow name="employeeGet" doc:id="8380dba3-0eb4-4aa9-bfb4-182e8cc8a689" >
		<ee:transform doc:name="Transform Message" doc:id="61716d39-89f2-41b8-9b18-17f8945a2b76" >
			<ee:message >
				<ee:set-payload ><![CDATA[%dw 2.0
output application/java
---
"select * from employee where empId = '" ++ vars.empId ++ "'"]]></ee:set-payload>
			</ee:message>
		</ee:transform>
		<db:select doc:name="Select" doc:id="9ef1e38a-e8d5-4740-9798-05e6634a852f" config-ref="Database_Config">
			<db:sql ><![CDATA[#[payload]]]></db:sql>
		</db:select>
		<ee:transform doc:name="Transform Message" doc:id="9d48be5a-7a19-4adc-b4b6-e53e65ce7d21" >
			<ee:message >
				<ee:set-payload ><![CDATA[%dw 2.0
output application/json
---
payload[0] default {}]]></ee:set-payload>
			</ee:message>
		</ee:transform>
		<logger level="INFO" doc:name="Logger" doc:id="935042a6-83b8-4ba6-9723-e61bd61ce515" message='#["Got Data for " ++ (payload.eName default "None")]'/>
	</flow>
```

---

</SwmSnippet>

# Summary

In summary, Employee Management provides essential database operations for maintaining employee records through Mule flows that handle bulk insertion and targeted retrieval, supported by logging for operational transparency.

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBbXVsZS1kZW1vLWRhdGFiYXNlLWFwcCUzQSUzQXVtYWxpbmdhc3dhbWk=" repo-name="mule-demo-database-app"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
