---
title: API Layer Configuration Basics
---
# Overview of API Layer Configuration

The API Layer Configuration defines how the API handles incoming HTTP requests, manages routing, and processes responses including error handling. It establishes the foundational behavior of the API by specifying how requests are received, routed, and responded to.

# HTTP Listener Setup

At the core of the API Layer is the HTTP listener configuration. This listener is responsible for accepting HTTP requests on designated paths, such as '<SwmPath>[src/…/resources/api/](src/main/resources/api/)</SwmPath>\*'. It manages both successful responses and error responses, ensuring that the API can communicate effectively with clients.

# APIKit Router Integration

The APIKit router is referenced within the configuration to route incoming requests to the appropriate processing flows. It uses the API specification to determine which flow should handle each request, enabling modular and organized request processing.

# Error Handling Configuration

Error handling is an integral part of the API Layer Configuration. It is set up to propagate specific HTTP status codes and JSON messages for common API errors such as bad requests (400), not found (404), method not allowed (405), and unsupported media types (415). This ensures clients receive meaningful feedback when errors occur.

# Endpoint-Specific Flows

Separate flows are configured for different API endpoints. Each flow is linked to specific processing logic or sub-flows that implement the business logic for that endpoint. For example, flows like 'get:/employee/{empId}:<SwmToken path="src/main/mule/api.xml" pos="13:11:13" line-data="        &lt;apikit:router config-ref=&quot;api-config&quot; /&gt;">`api-config`</SwmToken>' and 'post:/employee:<SwmToken path="src/main/mule/api.xml" pos="19:2:4" line-data="output application/json">`application/json`</SwmToken>:<SwmToken path="src/main/mule/api.xml" pos="13:11:13" line-data="        &lt;apikit:router config-ref=&quot;api-config&quot; /&gt;">`api-config`</SwmToken>' handle requests related to employee data retrieval and creation respectively.

# Application of Configuration in Flows

The API Layer Configuration is primarily applied in the <SwmToken path="src/main/mule/api.xml" pos="3:7:9" line-data="    &lt;flow name=&quot;api-main&quot;&gt;">`api-main`</SwmToken> flow, where the HTTP listener and APIKit router are set up. Additionally, it is used in the <SwmToken path="src/main/mule/api.xml" pos="95:7:9" line-data="    &lt;flow name=&quot;api-console&quot;&gt;">`api-console`</SwmToken> flow, which provides the API console interface, and in individual endpoint flows that contain the business logic for handling specific API requests.

<SwmSnippet path="/src/main/mule/api.xml" line="3">

---

In the <SwmToken path="src/main/mule/api.xml" pos="3:7:9" line-data="    &lt;flow name=&quot;api-main&quot;&gt;">`api-main`</SwmToken> flow, the HTTP listener uses the <SwmToken path="src/main/mule/api.xml" pos="4:11:13" line-data="        &lt;http:listener config-ref=&quot;api-httpListenerConfig&quot; path=&quot;/api/*&quot;&gt;">`api-httpListenerConfig`</SwmToken> to accept requests on the '<SwmPath>[src/…/resources/api/](src/main/resources/api/)</SwmPath>\*' path. The APIKit router references <SwmToken path="src/main/mule/api.xml" pos="13:11:13" line-data="        &lt;apikit:router config-ref=&quot;api-config&quot; /&gt;">`api-config`</SwmToken> to route requests to the appropriate flows. Error handling is configured here to respond with specific HTTP status codes and JSON messages for errors such as bad requests, resource not found, and unsupported media types.

```xml
    <flow name="api-main">
        <http:listener config-ref="api-httpListenerConfig" path="/api/*">
            <http:response statusCode="#[vars.httpStatus default 200]">
                <http:headers>#[vars.outboundHeaders default {}]</http:headers>
            </http:response>
            <http:error-response statusCode="#[vars.httpStatus default 500]">
                <http:body>#[payload]</http:body>
                <http:headers>#[vars.outboundHeaders default {}]</http:headers>
            </http:error-response>
        </http:listener>
        <apikit:router config-ref="api-config" />
        <error-handler>
            <on-error-propagate type="APIKIT:BAD_REQUEST">
                <ee:transform xmlns:ee="http://www.mulesoft.org/schema/mule/ee/core" xsi:schemaLocation="http://www.mulesoft.org/schema/mule/ee/core http://www.mulesoft.org/schema/mule/ee/core/current/mule-ee.xsd">
                    <ee:message>
                        <ee:set-payload><![CDATA[%dw 2.0
output application/json
---
{message: "Bad request"}]]></ee:set-payload>
                    </ee:message>
                    <ee:variables>
                        <ee:set-variable variableName="httpStatus">400</ee:set-variable>
                    </ee:variables>
                </ee:transform>
            </on-error-propagate>
            <on-error-propagate type="APIKIT:NOT_FOUND">
                <ee:transform xmlns:ee="http://www.mulesoft.org/schema/mule/ee/core" xsi:schemaLocation="http://www.mulesoft.org/schema/mule/ee/core http://www.mulesoft.org/schema/mule/ee/core/current/mule-ee.xsd">
                    <ee:message>
                        <ee:set-payload><![CDATA[%dw 2.0
output application/json
---
{message: "Resource not found"}]]></ee:set-payload>
                    </ee:message>
                    <ee:variables>
                        <ee:set-variable variableName="httpStatus">404</ee:set-variable>
                    </ee:variables>
                </ee:transform>
            </on-error-propagate>
            <on-error-propagate type="APIKIT:METHOD_NOT_ALLOWED">
                <ee:transform xmlns:ee="http://www.mulesoft.org/schema/mule/ee/core" xsi:schemaLocation="http://www.mulesoft.org/schema/mule/ee/core http://www.mulesoft.org/schema/mule/ee/core/current/mule-ee.xsd">
                    <ee:message>
                        <ee:set-payload><![CDATA[%dw 2.0
output application/json
---
{message: "Method not allowed"}]]></ee:set-payload>
                    </ee:message>
                    <ee:variables>
                        <ee:set-variable variableName="httpStatus">405</ee:set-variable>
                    </ee:variables>
                </ee:transform>
            </on-error-propagate>
            <on-error-propagate type="APIKIT:NOT_ACCEPTABLE">
                <ee:transform xmlns:ee="http://www.mulesoft.org/schema/mule/ee/core" xsi:schemaLocation="http://www.mulesoft.org/schema/mule/ee/core http://www.mulesoft.org/schema/mule/ee/core/current/mule-ee.xsd">
                    <ee:message>
                        <ee:set-payload><![CDATA[%dw 2.0
output application/json
---
{message: "Not acceptable"}]]></ee:set-payload>
                    </ee:message>
                    <ee:variables>
                        <ee:set-variable variableName="httpStatus">406</ee:set-variable>
                    </ee:variables>
                </ee:transform>
            </on-error-propagate>
            <on-error-propagate type="APIKIT:UNSUPPORTED_MEDIA_TYPE">
                <ee:transform xmlns:ee="http://www.mulesoft.org/schema/mule/ee/core" xsi:schemaLocation="http://www.mulesoft.org/schema/mule/ee/core http://www.mulesoft.org/schema/mule/ee/core/current/mule-ee.xsd">
                    <ee:message>
                        <ee:set-payload><![CDATA[%dw 2.0
output application/json
---
{message: "Unsupported media type"}]]></ee:set-payload>
                    </ee:message>
                    <ee:variables>
                        <ee:set-variable variableName="httpStatus">415</ee:set-variable>
                    </ee:variables>
                </ee:transform>
            </on-error-propagate>
            <on-error-propagate type="APIKIT:NOT_IMPLEMENTED">
                <ee:transform xmlns:ee="http://www.mulesoft.org/schema/mule/ee/core" xsi:schemaLocation="http://www.mulesoft.org/schema/mule/ee/core http://www.mulesoft.org/schema/mule/ee/core/current/mule-ee.xsd">
                    <ee:message>
                        <ee:set-payload><![CDATA[%dw 2.0
output application/json
---
{message: "Not Implemented"}]]></ee:set-payload>
                    </ee:message>
                    <ee:variables>
                        <ee:set-variable variableName="httpStatus">501</ee:set-variable>
                    </ee:variables>
                </ee:transform>
            </on-error-propagate>
        </error-handler>
    </flow>
```

---

</SwmSnippet>

# Summary of API Layer Configuration

Overall, the API Layer Configuration orchestrates how the API listens for requests, routes them according to the API specification, and manages responses including error handling. This modular setup allows for clear separation of concerns and maintainable API design.

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBbXVsZS1kZW1vLWRhdGFiYXNlLWFwcCUzQSUzQXVtYWxpbmdhc3dhbWk=" repo-name="mule-demo-database-app"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
