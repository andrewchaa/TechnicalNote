# The Web As a Platform for Building Distributed Systems

## Architecture of the Web

URIs and resources play a leading role, supported by web caches for scalability

## Thinking in Resources

A resource is anything we expose to the Web, from a document or video clip to a business process or device.

### Resources and Identifiers

To use a resource, we need both to be able to identify it on the network and to have some means of manipulating it.
A URI (Uniform Resource Identifier) uniquely identifies a web resource and at the same time makes it addressable, or capable of being manipulated using an application protocol such as HTTP.

The relationship between URIs and resources is many-to-one. A URI identifies only one resource, but a resource can have more than one URI. 

Google home page

* http://www.google.com
* http://google.com

A resource

* http://restbucks.com/order/1234
* http://restbucks.com/order/1234.jsp
* urn:restbucks.order:1234
* ftp://restbucks.com/order/1234.txt

A URI takes the form <scheme>:<scheme-specific-structure>

#### Terms used on the Web to refer to identifies

* URI: Uniform Resource Identifier
* IRI: International Resource Identifier. TO allow the use of international characters
* URN: Uniform Resource Name. a URI with "urn" as the scheme. ex) urn:isbn:0131401602
* URL: Uniform Resource Locator. Obsolete, though still widely used

URLs and URNs are special forms of URIs. A UrI that identifies the mechanism by which a resource may be accessed is usually referred to as a UrL. http UrIs are examples of UrLs. If the UrI has urn as its scheme and adheres to the requirements of rFC 2141 and RFC 2611, it is a URN. The goal of Urns is to provide globally unique names for resources.

### Resource Representations

Resources must have at least one identifier to be addressable on the Web, and each identifier is associated with one or more representations. A representation is a transformation or a view of a resource's state at an instant in time. This view is encoded in one or more transferable formats, such as XHTML, Atom, XML, JSON, plain text, comma-separated values, MP3, or JPEG.

Access to a resource is always mediated by way of its representations. 

*Resource*


*Menu*
http://restbucks.com/menu

Latte: $5
Espresso $4
Cookie: $1


*XHTML*
<xhtml>
<body>
<p><b>Menu</b></p>
<ui>
<li>Latte: $5</li>
<li>Espresso: $4</li>
<li>Cookie: $1</li>
</ui>
</body>

*Text*
Menu
Latte: $5
Espresso $4
Cookie: $1

### Representation Formats and URIs

A single URI can adress multiple resource representations

menu resource -> http://restbucks.com/menu -> XHTML, TEXT, PDF representations

Using content negotiation, consumers can negotiate for specific representation formats from a service. They do so by populating the HTTP Accept request header with a list of media types they're prepared to process. 

### The Art of Communication

#### HTTP verbs or actions

* GET
* POST
* DELETE
* OPTIONS
* HEAD
* TRACE
* CONNECT
* PATCH

#### HTTP response codes

* 200 OK
* 201 Created
* 404 Not Found
...

Resources, identifiers, and actions are all we need to interact with resources hosted on the Web

*agent*

GET /Resource1 HTTP/1.1
Accept: applicatio/xml
...

--> 

*Resource*

http://example.org/Resource1

HTTP/1.1 200 OK
...
representation of Resource1


## From the Web Architecture to the REST Architectural Style



## The Web As an Application Platform
## Web Friendliness and the Richardson Maturity Model
## GET on Board

Chapter 2. Introducing Restbucks: How to GET a Coffee, Web Style
Section 2.1. Restbucks: A Little Coffee Shop with Global Ambitions
Section 2.2. Toolbox
Section 2.3. Here Comes the Web
Chapter 3. Basic Web Integration
Section 3.1. Lose Weight, Feel Great!
Section 3.2. A Simple Coffee Ordering System
Section 3.3. URI Templates
Section 3.4. URI Tunneling
Section 3.5. POX: Plain Old XML over HTTP
Section 3.6. We Are Just Getting Started
Chapter 4. CRUD Web Services
Section 4.1. Modeling Orders As Resources
Section 4.2. Building CRUD Services
Section 4.3. Aligning Resource State
Section 4.4. Consuming CRUD Services
Section 4.5. Consuming Services Automatically with WADL
Section 4.6. CRUD Is Good, but It’s Not Great
Chapter 5. Hypermedia Services
Section 5.1. The Hypermedia Tenet
Section 5.2. Hypermedia Formats
Section 5.3. Contracts
Section 5.4. Hypermedia Protocols
Section 5.5. Implementing a Hypermedia Service
Section 5.6. Building the Ordering Service in Java
Section 5.7. Building the Ordering Service in .NET
Section 5.8. Ready, Set, Action
Chapter 6. Scaling Out
Section 6.1. GET Back to Basics
Section 6.2. Caching
Section 6.3. Making Content Cacheable
Section 6.4. Implementing Caching in .NET
Section 6.5. Consistency
Section 6.6. Extending Freshness
Section 6.7. Stay Fresh
Chapter 7. The Atom Syndication Format
Section 7.1. The Format
Section 7.2. Common Uses for Atom
Section 7.3. Using Atom for Event-Driven Systems
Section 7.4. Building an Atom Service in Java
Section 7.5. Building an Atom Service in .NET
Section 7.6. Atom Everywhere?
Section 7.7. After the Event
Chapter 8. Atom Publishing Protocol
Section 8.1. Atom Publishing Protocol
Section 8.2. Implementing Order Fulfillment Using AtomPub
Section 8.3. Implementing AtomPub in .NET
Section 8.4. A Versatile Protocol
Chapter 9. Web Security
Section 9.1. HTTP Security Essentials
Section 9.2. Identity and the OpenID Protocol
Section 9.3. The OAuth Protocol
Section 9.4. Service Hacks and Defenses
Section 9.5. Final Thoughts
Chapter 10. Semantics
Section 10.1. Syntax Versus Semantics
Section 10.2. Structure and Representation of Information
Section 10.3. The Semantic Web
Section 10.4. Microformats
Section 10.5. Linked Data and the Web
Section 10.6. Guidance
Chapter 11. The Web and WS-*
Section 11.1. Are Web Services Evil?
Section 11.2. SOAP: The Whole Truth
Section 11.3. WSDL: Just Another Object IDL
Section 11.4. Two Wrongs Don’t Make a Right
Section 11.5. Secure, Reliable, Transacted
Section 11.6. A Requiem for Web Services?
Chapter 12. Building the Case for the Web
Section 12.1. No More Silver Bullets
Section 12.2. Building and Running Web-Based Services
Section 12.3. No Architecture Without Measurement
Section 12.4. Selling the Web
Section 12.5. Go Forth and Build