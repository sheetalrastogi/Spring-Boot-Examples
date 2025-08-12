# RestAssured examples

RestAssured Notes:
=======================

#Basic request:
--------------------------

import io.restassured.RestAssured;
import io.restassured.specification.RequestSpecification;

public class Example {
    public static void main(String[] args) {
        // Build a reusable RequestSpecification
        RequestSpecification reqSpec = RestAssured.given()
            .baseUri("https://api.example.com")
            .header("Authorization", "Bearer your_token");

        // --- Query Param Example ---
        // GET https://api.example.com/resource?key=value
        reqSpec.queryParam("key", "value")
               .get("/resource")
               .then().statusCode(200);

        // --- Path Param Example ---
        // GET https://api.example.com/resource/12345
        reqSpec.pathParam("id", "12345")
               .get("/resource/{id}")
               .then().statusCode(200);

        // --- Payload Example (POST with body) ---
        // POST https://api.example.com/resource with JSON payload
        String jsonPayload = "{\"name\":\"value\"}";
        reqSpec.body(jsonPayload)
               .post("/resource")
               .then().statusCode(201);
    }
}


#2. Timeout settings in RestAssured:
--------------------------------------

import io.restassured.RestAssured;
import io.restassured.config.RestAssuredConfig;
import io.restassured.config.HttpClientConfig;

public class TimeoutExample2 {
    public static void main(String[] args) {
        RestAssured.config = RestAssuredConfig.config()
            .httpClient(HttpClientConfig.httpClientConfig()
                .setParam("http.connection.timeout", 7000)
                .setParam("http.socket.timeout", 7000)
            );

        RestAssured.given()
            .baseUri("https://api.example.com")
            .get("/resource")
            .then().statusCode(200);
    }
}


#3. how to use RestAssured to make an HTTPS request using a Java KeyStore (JKS) for client authentication:
--------------------------------------------------------------------------------

import io.restassured.RestAssured;
import io.restassured.response.Response;

public class RestAssuredJKSExample {
    public static void main(String[] args) {
        // Path to your JKS keystore
        String keystorePath = "src/test/resources/client-keystore.jks";
        // Keystore password
        String keystorePassword = "changeit";
        // Keystore type
        String keystoreType = "JKS";

        // Configure RestAssured to use the keystore for SSL
        RestAssured.config = RestAssured.config().sslConfig(
            RestAssured.sslConfig()
                .keyStore(keystorePath, keystorePassword) // Uses JKS by default
                .keystoreType(keystoreType)
        );

        // Example HTTPS request using RestAssured
        Response response = RestAssured
            .given()
            .when()
            .get("https://your-secure-api.com/endpoint");

        // Print response
        System.out.println("Status code: " + response.getStatusCode());
        System.out.println("Body: " + response.getBody().asString());
    }
}


#4. To test for rate limiting in RestAssured, you typically simulate multiple requests in quick succession, then check for HTTP status codes (like 429 Too Many Requests) or specific headers (e.g., Retry-After) that indicate your requests have been throttled.
------------------------------------------------------------------------------

import io.restassured.RestAssured;
import io.restassured.response.Response;
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.*;

public class RateLimitingTest {
    @Test
    public void testRateLimiting() {
        String endpoint = "https://api.example.com/endpoint";
        int requestsToSend = 20; // Adjust as needed to exceed the limit
        int rateLimitStatusCode = 429;
        boolean rateLimited = false;

        for (int i = 0; i < requestsToSend; i++) {
            Response response = RestAssured
                .given()
                .when()
                .get(endpoint);

            if (response.statusCode() == rateLimitStatusCode) {
                rateLimited = true;
                System.out.println("Rate limit hit on request #" + (i + 1));
                break;
            }
        }

        assertTrue(rateLimited, "API did not return 429 Too Many Requests after exceeding rate limit.");
    }
}


#5. Logging and Diagnostics
--------------------------

Enable logging of requests and responses to help diagnose failures:

given()
    .log().all()
    .get("/api/endpoint")
    .then()
    .log().all();

#6. Asynchronous API Call with RestAssured and CompletableFuture
--------------------------------------------------------------

RestAssured does not natively support async APIs, so this technique wraps it in Java's async constructs.

import io.restassured.RestAssured;
import io.restassured.response.Response;

import java.util.concurrent.CompletableFuture;

public class AsyncRestAssuredExample {
    public static void main(String[] args) {
        CompletableFuture<Response> futureResponse = CompletableFuture.supplyAsync(() -> {
            return RestAssured.get("https://jsonplaceholder.typicode.com/posts/1");
        });

        futureResponse.thenAccept(response -> {
            System.out.println("Status Code: " + response.getStatusCode());
            System.out.println("Body: " + response.getBody().asString());
        });

        // Wait for completion (for demo purposes)
        futureResponse.join();
    }
}

Key Points
CompletableFuture.supplyAsync: Runs the RestAssured call in a background thread.
.thenAccept: Processes the response asynchronously when it arrives.
.join(): Waits for completion (in tests, you might use assertions inside .thenAccept).


#7.Request/Response filtering in RestAssured:
-----------------------------------------------

Certainly! RestAssured supports **request** and **response filters** that allow you to intercept and modify HTTP requests/responses, log data, or implement custom logic before/after requests are sent.

Below are simple examples for both **RequestFilter** and **ResponseFilter**.

---

## 1. RequestFilter Example

A `RequestFilter` lets you view or modify the outgoing request:

```java
import io.restassured.RestAssured;
import io.restassured.filter.Filter;
import io.restassured.filter.FilterContext;
import io.restassured.response.Response;
import io.restassured.specification.FilterableRequestSpecification;
import io.restassured.specification.FilterableResponseSpecification;

public class MyRequestFilter implements Filter {
    @Override
    public Response filter(FilterableRequestSpecification requestSpec,
                           FilterableResponseSpecification responseSpec,
                           FilterContext ctx) {
        // Example: Log the request URI
        System.out.println("Request URI: " + requestSpec.getURI());
        // You can modify the requestSpec here if needed

        // Proceed with the request
        return ctx.next(requestSpec, responseSpec);
    }
}

// Usage
RestAssured.given()
    .filter(new MyRequestFilter())
    .baseUri("https://api.example.com")
    .get("/resource");
```

---

## 2. ResponseFilter Example

A `ResponseFilter` lets you inspect or modify the incoming response:

```java
import io.restassured.filter.Filter;
import io.restassured.filter.FilterContext;
import io.restassured.response.Response;
import io.restassured.specification.FilterableRequestSpecification;
import io.restassured.specification.FilterableResponseSpecification;

public class MyResponseFilter implements Filter {
    @Override
    public Response filter(FilterableRequestSpecification requestSpec,
                           FilterableResponseSpecification responseSpec,
                           FilterContext ctx) {
        // Proceed with the request and get the response
        Response response = ctx.next(requestSpec, responseSpec);

        // Example: Log the response status code
        System.out.println("Response Status Code: " + response.getStatusCode());

        // You can inspect or modify the response here if needed

        return response;
    }
}

// Usage
RestAssured.given()
    .filter(new MyResponseFilter())
    .baseUri("https://api.example.com")
    .get("/resource");
```

---

## Notes
- Both filters implement the same interface (`io.restassured.filter.Filter`).
- You can chain multiple filters using `.filters(...)`.
- Filters are useful for logging, debugging, authentication, header injection, or custom reporting.

Let me know if you need more advanced filter examples, such as logging request/response bodies, or combining multiple filters!



#8.  Array/Collection Matchers
----------------------------------
RestAssured.given()
    .baseUri("https://api.example.com")
    .get("/items")
    .then()
    .body("items", hasSize(5))
    .body("items.name", everyItem(startsWith("Item")));

or

RestAssured.given()
    .baseUri("https://api.example.com")
    .get("/product/123")
    .then()
    .body("id", equalTo(123))
    .body("price", lessThan(100.0f))
    .body("tags", hasItems("featured", "sale"));




#9.traversing a JSONArray using the Jackson library
------------------------------------------------------

import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;

public class JacksonJSONArrayTraverse {
    public static void main(String[] args) throws Exception {
        String jsonArray = "[{\"name\": \"Alice\", \"age\": 30}, {\"name\": \"Bob\", \"age\": 25}]";

        ObjectMapper mapper = new ObjectMapper();
        JsonNode arrayNode = mapper.readTree(jsonArray);

        if (arrayNode.isArray()) {
            for (JsonNode objNode : arrayNode) {
                String name = objNode.get("name").asText();
                int age = objNode.get("age").asInt();
                System.out.println("Name: " + name + ", Age: " + age);
            }
        }
    }
}


#10.blacklisting specific log entries (such as headers) in RestAssured using the config method call:
--------------------------------------------------------------------------------

import io.restassured.RestAssured;
import io.restassured.config.LogConfig;
import java.util.HashSet;
import java.util.Set;

public class BlacklistLogExample {
    public static void main(String[] args) {
        // Define headers to blacklist from logs
        Set<String> headers = new HashSet<>();
        headers.add("Authorization");
        headers.add("X-REGION");

        RestAssured.given()
            .baseUri("http://localhost:8080")
            .header("Authorization", "secret_token")
            .header("X-REGION", "NAM")
            .config(RestAssured.config()
                .logConfig(LogConfig.logConfig().blacklistHeaders(headers)))
            .log().all()
            .get("/resource");
    }
}

What this does:

The headers "Authorization" and "X-REGION" will be excluded from the request logs.
You can blacklist any header or log entry that contains sensitive data.



#11. log request and response details only if an assertion fails:
-------------------------------------------------------------------
import io.restassured.RestAssured;

public class LogIfValidationFailsExample {
    public static void main(String[] args) {
        RestAssured.given()
            .baseUri("https://api.example.com")
            .log().ifValidationFails() // Log only if assertion fails
            .get("/resource")
            .then()
            .statusCode(200)           // If this fails, request/response will be logged
            .body("name", equalTo("Alice"));
    }
}




#12. RestAssured/JsonPath code to find all employee IDs between 15 and 300
----------------------------------------------------------------------------

import io.restassured.RestAssured;
import io.restassured.response.Response;
import io.restassured.path.json.JsonPath;
import io.restassured.http.Method;
import java.util.List;

public class EmployeeIdFilter {
    public static void main(String[] args) {
        Response employeesResponse = RestAssured.given()
            .request(Method.GET, "/all");

        JsonPath jsonPathObj = employeesResponse.jsonPath();

        // Get all employee IDs between 15 and 300
        List<Integer> filteredIds = jsonPathObj.getList(
            "company.employee.findAll { it.id >= 15 && it.id <= 300 }.id", Integer.class
        );

        System.out.println("Filtered employee IDs: " + filteredIds);
    }
}



#13. POJO (Plain Old Java Object) to JSON in Java
---------------------------------------------------
--> dependency
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.16.1</version>
</dependency>

-->  Pojo class
public class Employee {
    private int id;
    private String name;

    // Constructors, getters, and setters
    public Employee() {}
    public Employee(int id, String name) {
        this.id = id;
        this.name = name;
    }
    public int getId() { return id; }
    public void setId(int id) { this.id = id; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
}


-->  Conversion of POJO to JSon
import com.fasterxml.jackson.databind.ObjectMapper;

public class PojoToJsonExample {
    public static void main(String[] args) throws Exception {
        Employee emp = new Employee(1, "John Doe");
        ObjectMapper mapper = new ObjectMapper();
        String json = mapper.writeValueAsString(emp);  // Convert POJO to JSON string
        System.out.println(json); // Output: {"id":1,"name":"John Doe"}
    }
}






#14.JSON to POJO in Java 
---------------------------

-->  JSON
{
  "name": "Alice",
  "age": 30
}

--> Pojo
public class Person {
    private String name;
    private int age;

    // Getters and setters (required for Jackson)
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}

--> Convert JSON to Pojo
import com.fasterxml.jackson.databind.ObjectMapper;

public class JsonToPojoExample {
    public static void main(String[] args) throws Exception {
        String json = "{\"name\":\"Alice\",\"age\":30}";

        ObjectMapper mapper = new ObjectMapper();
        Person person = mapper.readValue(json, Person.class);

        System.out.println(person.getName()); // Alice
        System.out.println(person.getAge());  // 30
    }
}

--> another method to convert JSON to POJO
From File or InputStream

Person person = mapper.readValue(new File("person.json"), Person.class);



#15. Query/Path Params
------------------------

RestAssured.given()
    .pathParam("id", 123)
    .queryParam("expand", "details")
    .get("/employee/{id}");

This will make a request to /employee/123?expand=details.


Another example
-- - - - - - - -

Map<String, Object> pathParams = new HashMap<>();
pathParams.put("id", 123);

Map<String, Object> queryParams = new HashMap<>();
queryParams.put("expand", "details");

RestAssured.given()
    .pathParams(pathParams)
    .queryParams(queryParams)
    .get("/employee/{id}");

This will call: /employee/123?expand=details

another example
- - - -  - - - - -
import java.util.Arrays;
import java.util.HashMap;
import java.util.Map;

Map<String, Object> params = new HashMap<>();
params.put("role", Arrays.asList("admin", "user", "manager"));

RestAssured.given()
    .queryParams(params)
    .get("/users");

This also sends: /users?role=admin&role=user&role=manager



#16. Download file with RestAssured
--------------------------------------

import io.restassured.RestAssured;
import io.restassured.response.Response;
import java.io.FileOutputStream;
import java.io.InputStream;

public class DownloadFileExample {
    public static void main(String[] args) throws Exception {
        String fileUrl = "https://example.com/file.pdf"; // Replace with actual file URL

        // Make GET request to download the file
        Response response = RestAssured.given()
            .get(fileUrl);

        // Check if request was successful
        if (response.getStatusCode() == 200) {
            // Get InputStream from response
            InputStream is = response.asInputStream();

            // Write InputStream to file
            try (FileOutputStream fos = new FileOutputStream("downloaded_file.pdf")) {
                byte[] buffer = new byte[8192];
                int bytesRead;
                while ((bytesRead = is.read(buffer)) != -1) {
                    fos.write(buffer, 0, bytesRead);
                }
            }
            System.out.println("File downloaded successfully.");
        } else {
            System.out.println("Failed to download file. Status: " + response.getStatusCode());
        }
    }
}



#17. Uploading a MIME File with RestAssured
--------------------------------------------

import io.restassured.RestAssured;
import java.io.File;

public class UploadMimeFile {
    public static void main(String[] args) {
        File file = new File("path/to/your/file.pdf"); // Use the actual file path

        RestAssured.given()
            .baseUri("https://your.api.endpoint")
            .multiPart("file", file, "application/pdf") // "file" is the parameter name, adjust MIME type as needed
            .post("/upload")                            // Replace with your endpoint
            .then()
            .statusCode(200);                           // Check response as needed
    }
}


Example with Additional Form Fields

RestAssured.given()
    .multiPart("file", file, "application/pdf")
    .multiPart("description", "Quarterly Report")
    .post("/upload");



Common MIME Types Supported by RestAssured

File Type	MIME Type
Text	text/plain
HTML	text/html
XML	application/xml
JSON	application/json
PDF	application/pdf
CSV	text/csv
ZIP	application/zip
Excel (XLS)	application/vnd.ms-excel
Excel (XLSX)	application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
Word (DOC)	application/msword
Word (DOCX)	application/vnd.openxmlformats-officedocument.wordprocessingml.document
Image (JPEG)	image/jpeg
Image (PNG)	image/png
Image (GIF)	image/gif
Audio (MP3)	audio/mpeg
Video (MP4)	video/mp4
Binary	application/octet-stream


#18. JSON Payload
---------------------
String jsonPayload = "{ \"employee\": { \"name\": \"Alice\", \"address\": { \"city\": \"London\", \"zip\": \"E1 6AN\" } } }";

RestAssured.given()
    .contentType("application/json")
    .body(jsonPayload)
    .post("/api/employees");



#19. fetch cookies from a response:
----------------------------------------

import io.restassured.RestAssured;
import io.restassured.response.Response;
import java.util.Map;

public class FetchCookiesExample {
    public static void main(String[] args) {
        Response response = RestAssured.given()
            .get("https://example.com"); // Use your target URL

        // Get a specific cookie by name
        String sessionCookie = response.getCookie("JSESSIONID");
        System.out.println("Session Cookie: " + sessionCookie);

        // Get all cookies as a Map
        Map<String, String> allCookies = response.getCookies();
        System.out.println("All Cookies: " + allCookies);
    }
}


#20. JSON Schema Validation in RestAssured
--------------------------------------------

You can perform JSON Schema Validation in RestAssured using the matchesJsonSchemaInClasspath() or matchesJsonSchema() method from the rest-assured-json-schema-validator dependency.

Step 1: Add Dependency
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>json-schema-validator</artifactId>
    <version>5.4.0</version>
    <scope>test</scope>
</dependency>

Step 2: Create a JSON Schema File
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "properties": {
    "id": { "type": "integer" },
    "name": { "type": "string" },
    "email": { "type": "string" }
  },
  "required": ["id", "name", "email"]
}


Step 3: Validate Response Against Schema
import static io.restassured.module.jsv.JsonSchemaValidator.matchesJsonSchemaInClasspath;
import io.restassured.RestAssured;

RestAssured.given()
    .get("/employee/1")
    .then()
    .assertThat()
    .body(matchesJsonSchemaInClasspath("employee-schema.json"));


Tips
Place your schema file in src/test/resources so matchesJsonSchemaInClasspath can find it.
For inline schema or from a string/file, use matchesJsonSchema(new File("path/to/schema.json")).
The assertion will fail if the response doesn’t match the schema.


#21. disable URL encoding in RestAssured
-------------------------------------------

RestAssured.given()
    .urlEncodingEnabled(false)
    .queryParam("redirectUrl", "https://example.com?foo=bar&baz=qux")
    .get("/test");

What this does:

By default, RestAssured URL-encodes query and path parameters.
Setting .urlEncodingEnabled(false) disables this, so your parameters are sent as-is.



#22. checking if anyof the status response 
--------------------------------------------

import static io.restassured.RestAssured.given;
import static org.hamcrest.Matchers.anyOf;
import static org.hamcrest.Matchers.is;

given()
    .get("/endpoint")
    .then()
    .statusCode(anyOf(is(200), is(201), is(202))); // Accepts 200, 201, or 202


#23. write a code to save the response in a JSON file
-------------------------------------------------------
import io.restassured.RestAssured;
import io.restassured.response.Response;
import java.io.FileWriter;
import java.io.IOException;

public class SaveResponseToJson {
    public static void main(String[] args) throws IOException {
        Response response = RestAssured.given()
            .get("https://jsonplaceholder.typicode.com/todos/1"); // Use your desired URL

        String jsonBody = response.getBody().asString();

        try (FileWriter file = new FileWriter("response.json")) {
            file.write(jsonBody);
        }

        System.out.println("Response saved to response.json");
    }
}

#24. Extract all headers from Response
----------------------------------------------

import io.restassured.RestAssured;
import io.restassured.response.Response;
import io.restassured.http.Headers;

public class ExtractHeadersExample {
    public static void main(String[] args) {
        Response response = RestAssured.given()
            .get("https://example.com"); // Replace with your URL

        // Get all headers
        Headers allHeaders = response.getHeaders();

        // Print all headers
        for (io.restassured.http.Header header : allHeaders) {
            System.out.println(header.getName() + ": " + header.getValue());
        }
    }
}

#25. Concept
-------------------

request.get("https://example.com")
Sends a GET request to the fully specified URL "https://example.com".
example:
	Response response = request.get("https://example.com");


request.request(Method.GET, "/allcustomers")
Sends a GET request using Method.GET to the path "/allcustomers" appended to the base URI (if set).
example:
	Response response = request.request(Method.GET, "/allcustomers");


#26. Logging Request and Response
------------------------------------
import io.restassured.RestAssured;
import io.restassured.filter.log.RequestLoggingFilter;
import io.restassured.filter.log.ResponseLoggingFilter;
import io.restassured.response.Response;

public class LoggingExample {
    public static void main(String[] args) {
        Response response = RestAssured.given()
            .filter(new RequestLoggingFilter())   // Logs the request details
            .filter(new ResponseLoggingFilter())  // Logs the response details
            .get("https://jsonplaceholder.typicode.com/todos/1");
        
        // Use response as needed
        System.out.println("Status code: " + response.getStatusCode());
    }
}

What does this do?

RequestLoggingFilter logs the HTTP request (URI, headers, body, etc.).
ResponseLoggingFilter logs the HTTP response (status, headers, body, etc.).


#27. Response is within specific time unit:
-----------------------------------------------

given()
.when()
.get("/endpoint")
.then()
.time(Matchers.lessThan(1000)); // Response time less than 1000 milliseconds


#28. Concept:
-----------------
given()
    .config(RestAssured.config()
        .sslConfig(new SSLConfig().allowAllHostnames())
    )
.when()
    .get("/secure-endpoint")
.then()
    .statusCode(200);


#29. proxy for your RestAssured requests 
------------------------------------------

import io.restassured.RestAssured;

RestAssured.given()
    .proxy("proxy.example.com", 8080)
    .get("https://example.com");


Proxy with Authentication
- - - - - - - - - - - - - -

RestAssured.given()
    .proxy("proxy.example.com", 8080, "username", "password")
    .get("https://example.com");

#30.JsonConfig.jsonConfig() method in RestAssured 
---------------------------------------------------

It lets you customize how JSON is parsed and serialized.

import io.restassured.RestAssured;
import io.restassured.config.JsonConfig;
import io.restassured.config.RestAssuredConfig;

RestAssured.given()
    .config(RestAssuredConfig.config()
        .jsonConfig(JsonConfig.jsonConfig().numberReturnType(JsonConfig.NumberReturnType.DOUBLE))
    )
    .get("https://jsonplaceholder.typicode.com/todos/1")
    .then()
    .statusCode(200);


In this example, .numberReturnType(JsonConfig.NumberReturnType.DOUBLE) makes RestAssured parse numbers in the response as doubles.


another example
- - - - - - - - 

import io.restassured.RestAssured;
import io.restassured.config.JsonConfig;
import io.restassured.config.RestAssuredConfig;

RestAssured.given()
    .config(RestAssuredConfig.config()
        .jsonConfig(JsonConfig.jsonConfig()
            .numberReturnType(JsonConfig.NumberReturnType.STRING)
        )
    )
    .get("https://jsonplaceholder.typicode.com/todos/1")
    .then()
    .statusCode(200);

.numberReturnType(JsonConfig.NumberReturnType.STRING) tells RestAssured to parse all JSON numbers as Strings in the response.


another example
- - - - - - - - -

import io.restassured.RestAssured;
import io.restassured.config.JsonConfig;
import io.restassured.config.RestAssuredConfig;
import com.jayway.jsonpath.Configuration;
import com.jayway.jsonpath.Option;

RestAssured.given()
    .config(RestAssuredConfig.config()
        .jsonConfig(JsonConfig.jsonConfig()
            .jacksonConfiguration(Configuration.builder()
                .options(Option.ALWAYS_RETURN_LIST, Option.DEFAULT_PATH_LEAF_TO_NULL)
                .build())
        )
    )
    .get("https://your.api/endpoint")
    .then()
    .statusCode(200);

This force all fields (including dates) to be parsed as Strings

another example
- - - - - - - - 

Using Custom ObjectMapper for Dates as Strings

import com.fasterxml.jackson.databind.DeserializationFeature;
import com.fasterxml.jackson.databind.ObjectMapper;
import io.restassured.mapper.factory.Jackson2ObjectMapperFactory;

RestAssured.given()
    .config(RestAssuredConfig.config()
        .jsonConfig(JsonConfig.jsonConfig()
            .jackson2ObjectMapperFactory(new Jackson2ObjectMapperFactory() {
                @Override
                public ObjectMapper create(Class cls, String charset) {
                    ObjectMapper om = new ObjectMapper();
                    om.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);
                    // Add more customizations if needed
                    return om;
                }
            })
        )
    )
    .get("https://your.api/endpoint")
    .then()
    .statusCode(200);


#31. relaxedContentType in RestAssured
--------------------------------------------

import io.restassured.RestAssured;
import io.restassured.http.ContentType;

RestAssured.given()
    .relaxedContentType(ContentType.JSON)
.when()
    .get("https://example.com/nonstandard-json-endpoint")
.then()
    .statusCode(200)
    .body("someField", org.hamcrest.Matchers.notNullValue());

Here, RestAssured will treat the response as JSON even if the response Content-Type is not exactly "application/json" (for example, "text/html" or "application/json; charset=UTF-8").


#32. Using spec() with RequestSpecification
-----------------------------------------------
import io.restassured.RestAssured;
import io.restassured.specification.RequestSpecification;
import io.restassured.builder.RequestSpecBuilder;

public class SpecExample {
    public static void main(String[] args) {
        RequestSpecification reqSpec = new RequestSpecBuilder()
            .setBaseUri("https://jsonplaceholder.typicode.com")
            .setBasePath("/todos")
            .addHeader("Accept", "application/json")
            .build();

        RestAssured.given()
            .spec(reqSpec) // Apply the reusable specification
            .when()
            .get("/1")
            .then()
            .statusCode(200);
    }
}

What does this do?
Creates a reusable specification (reqSpec) for base URI, base path, and headers.
Applies the specification to the request using .spec(reqSpec).
Makes your code cleaner and easier to maintain, especially for multiple requests.


#33. Special characters in Query/Path Params
----------------------------------------------

When dealing with **special characters** in query parameters using RestAssured, you generally don’t need to manually encode them with `URLEncoder`. RestAssured will automatically encode query parameters for you.

---

## **Best Practice: Let RestAssured Encode Automatically**

```java
given()
    .queryParam("param", "special@character")
.when()
    .get("/endpoint")
.then()
    .statusCode(200);
```

**RestAssured will handle the encoding** of `"special@character"` to `"special%40character"` in the actual request.

---

## **Manual Encoding (if needed)**
If you have a special reason to encode manually, your code works, but it’s usually not required:

```java
String encodedValue = URLEncoder.encode("special@character", StandardCharsets.UTF_8.toString());

given()
    .queryParam("param", encodedValue)
.when()
    .get("/endpoint")
.then()
    .statusCode(200);
```

This will **double-encode** if RestAssured encodes it again, resulting in incorrect values.

---

## **Summary**

- **Preferred:** Pass raw value to `.queryParam()`. RestAssured encodes for you.
- **Avoid double-encoding:** Don’t use `URLEncoder.encode()` unless you have a specific need and ensure RestAssured won’t encode again.



#34. SoftAsserts in RestAssured
---------------------------------


import io.restassured.RestAssured;
import io.restassured.response.Response;
import org.testng.asserts.SoftAssert;
import org.testng.annotations.Test;

public class RestAssuredSoftAssertExample {

    @Test
    public void testWithSoftAssert() {
        SoftAssert softAssert = new SoftAssert();

        Response response = RestAssured.given()
            .get("https://jsonplaceholder.typicode.com/todos/1");

        softAssert.assertEquals(response.getStatusCode(), 200, "Status Code");
        softAssert.assertTrue(response.getBody().asString().contains("userId"), "Body should contain userId");
        softAssert.assertTrue(response.getBody().asString().contains("title"), "Body should contain title");
        softAssert.assertNotNull(response.jsonPath().get("completed"), "Completed field should not be null");

        // Report all assertion failures at once
        softAssert.assertAll();
    }
}


# Wiremock example usage
# Short Tutorial: WireMock (Based on Baeldung Guide)

WireMock is a flexible library for mocking HTTP APIs in your tests. It allows you to simulate API endpoints, control responses, and verify interactions.

## 1. Add WireMock Dependency

For Maven:
```xml
<dependency>
    <groupId>com.github.tomakehurst</groupId>
    <artifactId>wiremock-jre8</artifactId>
    <version>2.34.0</version>
    <scope>test</scope>
</dependency>
```

## 2. Start WireMock Server

```java
import com.github.tomakehurst.wiremock.WireMockServer;

WireMockServer wireMockServer = new WireMockServer(8080);
wireMockServer.start();
```

## 3. Stub an Endpoint

Define what the mocked server should return for a given request:
```java
import static com.github.tomakehurst.wiremock.client.WireMock.*;

wireMockServer.stubFor(get(urlEqualTo("/hello"))
    .willReturn(aResponse()
        .withStatus(200)
        .withBody("Hello WireMock!")));
```

## 4. Call the Mocked Endpoint

You can use any HTTP client:
```java
HttpURLConnection connection = (HttpURLConnection) new URL("http://localhost:8080/hello").openConnection();
connection.setRequestMethod("GET");
```

## 5. Stop WireMock Server

```java
wireMockServer.stop();
```

## 6. Example Output

Calling `GET /hello` returns:
```
Hello WireMock!
```

## 7. Additional Features

- **Stub different HTTP methods (POST, PUT, etc.)**
- **Return custom headers and status codes**
- **Verify requests received by WireMock**

---

WireMock helps you test your code’s HTTP interactions without relying on real external services.


wiremock example:
------------------

import com.github.tomakehurst.wiremock.WireMockServer;
import com.github.tomakehurst.wiremock.client.WireMock;
import com.github.tomakehurst.wiremock.core.WireMockConfiguration;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import static com.github.tomakehurst.wiremock.client.WireMock.*;
import static org.junit.jupiter.api.Assertions.*;

public class WireMockJUnitExample {

    private WireMockServer wireMockServer;
    private final int port = 8089;

    @BeforeEach
    public void setup() {
        wireMockServer = new WireMockServer(WireMockConfiguration.options().port(port));
        wireMockServer.start();
        WireMock.configureFor("localhost", port);

        // Stub an endpoint
        stubFor(get(urlEqualTo("/hello"))
            .willReturn(aResponse()
                .withStatus(200)
                .withBody("Hello from WireMock!")));
    }

    @AfterEach
    public void teardown() {
        wireMockServer.stop();
    }

    @Test
    public void testWireMockStub() {
        // Make a request to the WireMock server
        String url = "http://localhost:" + port + "/hello";
        java.net.http.HttpClient client = java.net.http.HttpClient.newHttpClient();
        java.net.http.HttpRequest request = java.net.http.HttpRequest.newBuilder()
            .uri(java.net.URI.create(url))
            .build();

        try {
            java.net.http.HttpResponse<String> response = client.send(request, java.net.http.HttpResponse.BodyHandlers.ofString());
            assertEquals(200, response.statusCode());
            assertEquals("Hello from WireMock!", response.body());
        } catch (Exception e) {
            fail("Test failed with exception: " + e.getMessage());
        }
    }
}


Notes:
@BeforeEach: Starts WireMock and configures a stub.
@AfterEach: Stops WireMock.
The test sends an HTTP GET to the stubbed endpoint and asserts the response.
Uses Java’s built-in HttpClient for the request, but you can use any HTTP client.





