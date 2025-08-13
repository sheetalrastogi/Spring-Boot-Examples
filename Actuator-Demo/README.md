# Related Tutorials

* [Spring Boot Actuator ‘/Info’ Endpoint](https://howtodoinjava.com/spring-boot/info-endpoint-custom-info/)

* Concept 1:
===============


"SSL Termination" refers to the process where SSL encryption is removed (terminated) at a load balancer or proxy, and the traffic from that point onward is sent in plain HTTP to backend servers. In API testing with RestAssured, this situation typically means:

-> Your API endpoint is accessible via HTTP (not HTTPS) because SSL is handled upstream.
-> Or, you might still be testing against HTTPS endpoints, but need to handle SSL certificates (including self-signed certs).

ways to handle "SSL Termination"

1. configure custom trust stores 
RestAssured.config = RestAssured.config()
    .sslConfig(sslConfig()
        .trustStore("/path/to/truststore.jks", "password")
    );

2. Testing HTTPS Endpoints with Self-Signed Certificates
import io.restassured.RestAssured;

RestAssured.useRelaxedHTTPSValidation();

given()
    .get("https://your-api-endpoint.com/resource")
    .then()
    .statusCode(200);

3. Testing HTTP Endpoints (SSL Terminated Upstream)
RestAssured.baseURI = "http://your-api-endpoint.com";



Concept 2:
================

Service Mesh. A service mesh is a dedicated infrastructure layer for handling service-to-service communication in a microservices architecture



Concept 3:
================
12 Factor Methodology

The **12 Factor Methodology** is a set of best practices for building modern, scalable, and maintainable cloud-native applications, particularly suited for microservices and SaaS apps. It was originally defined by Heroku engineers to help developers build applications that are easy to deploy, scale, and manage.

Here are the **12 factors**:

1. **Codebase**: One codebase tracked in revision control, many deploys.  
   *Each microservice should have its own repository.*

2. **Dependencies**: Explicitly declare and isolate dependencies.  
   *Use dependency management tools; don’t rely on system packages.*

3. **Config**: Store configuration in the environment.  
   *Config (credentials, endpoints, etc.) should be in environment variables, not in code.*

4. **Backing Services**: Treat backing services as attached resources.  
   *Databases, caches, and other services should be easily replaceable and configurable.*

5. **Build, Release, Run**: Strictly separate build and run stages.  
   *The build stage produces artifacts; the release stage combines artifacts with config; run executes the app.*

6. **Processes**: Execute the app as one or more stateless processes.  
   *State should be stored externally (e.g., in a database).*

7. **Port Binding**: Export services via port binding.  
   *The app should be self-contained and expose an HTTP port for communication.*

8. **Concurrency**: Scale out via the process model.  
   *Increase capacity by running more instances, not by making a single instance bigger.*

9. **Disposability**: Maximize robustness with fast startup and graceful shutdown.  
   *Processes should start and stop quickly and handle termination signals.*

10. **Dev/Prod Parity**: Keep development, staging, and production as similar as possible.  
    *Avoid environment-specific differences; use the same tools and workflows.*

11. **Logs**: Treat logs as event streams.  
    *Apps should write logs to stdout/stderr; log aggregation is handled externally.*

12. **Admin Processes**: Run admin/management tasks as one-off processes.  
    *Database migrations, maintenance scripts, etc. should be run as ad-hoc processes.*

---

**Summary:**  
The 12 Factor methodology guides the design of microservices to be portable, resilient, and cloud-friendly, making it easier to deploy, scale, and maintain microservice-based architectures.



Concept 4:
==============

For object-oriented design we follow the SOLID principles. 
For microservice design we propose developers follow the “IDEALS”: 
	interface segregation, 
	deployability (is on you), 
	event-driven, 
	availability over consistency, 
	loose-coupling, and 
	single responsibility.


Concept 5:
============

Is a rest API a microservice?

REST APIs are a communication mechanism, whereas Microservices represent an architectural style. 

REST APIs are commonly used within Microservices architectures.


Concept 6:
===============

REST APIs
REST is a software architectural style that imposes six conditions on how an API should work. These are the six principles REST APIs follow:

1. Client-server architecture. The sender and receiver are independent of each other regarding technology, platforming, programming language, and so on.
2. Layered. The server can have several intermediaries that work together to complete client requests, but they are invisible to the client.
3. Uniform interface. The API returns data in a standard format that is complete and fully useable.
4. Stateless. The API completes every new request independently of previous requests.
5. Cacheable. All API responses are cacheable.
6. Code on demand. The API response can include a code snippet if required.

You send REST requests using HTTP verbs like GET and POST. Rest API responses are typically in JSON but can also be of a different data format.


Concept 7:
=============

SOAP is a protocol that defines rigid communication rules. It has several associated standards that control every aspect of the data exchange. For example, here are some standards SOAP uses:

 -> Web Services Security (WS-Security) specifies security measures like using unique identifiers called tokens
 -> Web Services Addressing (WS-Addressing) requires including routing information as metadata
 -> WS-ReliableMessaging standardizes error handling in SOAP messaging
 -> Web Services Description Language (WSDL) describes the scope and function of SOAP web services

When you send a request to a SOAP API, you must wrap your HTTP request in a SOAP envelope. This is a data structure that modifies the underlying HTTP content with SOAP request requirements. Due to the envelope, you can also send requests to SOAP web services with other transport protocols, like TCP or Internet Control Message Protocol (ICMP). However, SOAP APIs and SOAP web services always return XML documents in their responses.


Concept 8:
================

When to use SOAP vs REST?
Before choosing between SOAP and REST, consider your scenarios and your API users' requirements. The following criteria are worth considering.

Overall application design
Modern applications like mobile apps and hybrid applications work better with REST APIs. REST gives you the scalability and flexibility to design applications using modern architecture patterns like microservices and containers. However, if you need to integrate or extend legacy systems that already have SOAP APIs, you may be better off continuing with SOAP.

Security
Public APIs have lower security requirements and demand greater flexibility so anyone can interact with them. So, REST is a better choice when you build public APIs. Conversely, some private APIs for internal enterprise requirements (like data reporting for compliance) may benefit from the tighter security measures in WS-Security of SOAP.

ACID compliance
Do your API users require stringent consistency and data integrity across a chain of transactions? For instance, finance transactions require an entire batch of data updates to fail if even one update fails.

SOAP has built-in compliance for atomicity, consistency, isolation, and durability (ACID). And SOAP may be better suited for high data integrity requirements. In this case, REST APIs may require additional software modules to enforce the state at the server or database level.


Concept 9:
================

Summary of differences between SOAP vs REST
 
 	
SOAP

REST

Stands for 

Simple Object Access Protocol

Representational State Transfer

What is it?

SOAP is a protocol for communication between applications

REST is an architecture style for designing communication interfaces.

Design

SOAP API exposes the operation.

REST API exposes the data.

Transport Protocol

SOAP is independent and can work with any transport protocol.

REST works only with HTTPS.

Data format

SOAP supports only XML data exchange.

REST supports XML, JSON, plain text, HTML.

Performance

SOAP messages are larger, which makes communication slower.

REST has faster performance due to smaller messages and caching support.

Scalability

SOAP is difficult to scale. The server maintains state by storing all previous messages exchanged with a client.

REST is easy to scale. It’s stateless, so every message is processed independently of previous messages.

Security

SOAP supports encryption with additional overheads.

REST supports encryption without affecting performance.

Use case

SOAP is useful in legacy applications and private APIs.

REST is useful in modern applications and public APIs.


Concept 10:
===============

Conway’s Law, coined by computer scientist Melvin Edward Conway, “Any organization that designs a system (defined broadly) will produce a design whose structure is a copy of the organization’s communication structure.” 

Concept 11 (source: https://dev.to/ajmal_hasan/mastering-system-design-24-key-concepts-for-beginners-198b)
============================================
Vertical Scaling

Vertical scaling means adding more resources (like RAM, CPU) to a single server to handle more load.


Horizontal Scaling

Horizontal scaling involves adding more servers to share the load. This approach allows systems to grow without relying on a single server’s capacity.


Sharding

Sharding involves splitting a large database into smaller pieces (called shards), each of which is stored on a separate server. It helps distribute the load across multiple machines.


Vertical Partitioning

Vertical partitioning splits a database table into smaller parts based on columns. This optimizes performance for queries that require specific columns.

CAP Theorem

The CAP theorem states that in a distributed system, you can achieve only two of the following three: Consistency, Availability, and Partition Tolerance.



Concept 12:
====================

