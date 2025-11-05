# A Beginner's Guide to HTTP Status Codes

### Introduction: Your API's Communication Signals

Imagine you're at a traffic light. Green means go, red means stop, and yellow means prepare to stop. This simple system communicates a clear status and tells you what to do next. HTTP status codes work in a very similar way for API requests. They are the primary way an API communicates the outcome of a request you've sent—whether it was a success, a failure, or something in between.

This guide will help you understand the most common status codes, what they mean, and why they are crucial for building applications that interact with APIs. To make them easier to learn, we'll start by grouping them into "families."

## 1. The Big Picture: Status Code Families

HTTP status codes are not random numbers; they are organized into five main ranges, or "families," where each range signifies a general category of response. For a beginner, the most important ones to know are the 200s, 400s, and 500s.

|   |   |   |
|---|---|---|
|Code Range|Category|"In Simple Terms, This Means..."|
|**2XX**|Success|Your request was received, understood, and accepted. Everything worked as expected.|
|**4XX**|Client Error|The error is due to your request. You sent something the server couldn't process, and you need to fix it.|
|**5XX**|Server Error|The error is not your fault; you can't do anything about it. The problem is on the server's side.|

Understanding these families is the first step. When you receive a response, looking at the first digit of the status code immediately tells you who is responsible for the outcome. Now, let's look at the specific codes you'll see most often.

## 2. The Everyday Codes: Success & Common Errors

These are the status codes you will encounter constantly when working with APIs. Understanding them is fundamental.

### 2.1. `200 OK`

This is the standard, all-purpose success code. It means the request succeeded, the resource was found, and the requested action was taken. You'll most often see this in response to a `GET` request that successfully retrieves data.

- **When you'll see it:** You send a request like `GET /sales/customers` to fetch a list of customers, and the API successfully returns the list in the response body.
- **What to do next:** Process the data included in the response payload.

### 2.2. `404 Not Found`

Everyone who has used a web browser has seen a 404 error. In the context of an API, it means the specific resource you asked for (e.g., a user with a specific ID) does not exist or is not accessible to you.

- **When you'll see it:** You request `GET /customers/C72`, but there is no customer with the ID `C72`.
- **What to do next:** Double-check that the URL and resource ID are correct. From a security perspective, APIs should also return a `404` if the resource exists but you aren't authorized to see it. This prevents attackers from discovering valid resource IDs.

### 2.3. `400 Bad Request`

This code indicates that the server could not understand or process your request because of a client-side error. The key takeaway is that you, the developer, should be able to repair the request and resubmit it.

- **When you'll see it:** Your request might be missing a required parameter, or the JSON in your request body might be malformed.
- **What to do next:** Read the error message in the response body to understand what needs to be fixed, correct your request, and send it again.

### 2.4. `500 Server Error`

This is a generic "something went wrong" error on the server's side. It's an unexpected condition that prevented the server from fulfilling the request. Crucially, this is not your fault as the client.

- **When you'll see it:** The API server might have an internal bug, a database connection might fail, or another backend dependency might be down.
- **What to do next:** Your first step is often to wait and retry the request after a short interval. If the error persists, it indicates a stable problem on the server, and you should report the issue to the API provider, including details like a correlation ID if one was provided in the error message.

## 3. Expanding Your Vocabulary: More Important Codes

As you work more with APIs, you'll encounter other codes that provide more specific details about the outcome of your request.

### 3.1. Success Variations

- `**201 Created**`: This success code is used specifically when a request (usually a `POST`) results in the creation of a new resource. While `201` is more specific, the source notes that many APIs simply use `200 OK` for this purpose. The most important principle is to be consistent across all the endpoints in your API.
- `**204 No Content**`: This is also a success code, but it indicates that while the server successfully processed the request, there is no data to return in the response body.

### 3.2. Specific Client Errors

- `**401 Unauthorized**`: This code means the request requires authentication, but you haven't provided valid credentials (or any at all). The name is slightly confusing; it's about a lack of _authentication_, not authorization.
- `**403 Forbidden**`: This code is returned when you _have_ successfully authenticated, but you are not _authorized_ to perform the requested action. The server knows who you are, but you don't have the necessary permissions for that specific resource.
- `**429 Too Many Requests**`: This is a rate-limiting response. It means you've sent too many requests in a given amount of time, and the server is asking you to slow down.

## 4. Insight: What Makes a Good Error Message?

A status code tells you _what_ happened, but a good error message tells you _why_ and _how to fix it_. A well-designed API provides helpful information in its error responses without compromising security by leaking internal details.

|   |   |
|---|---|
|Don't Do This (The Anti-Pattern)|Do This Instead (The Best Practice)|
|Be vague and unhelpful.<br>`{"error": "Bad Request"}`|Be specific and provide context.<br>`{"error": "Missing query parameter: q", "message": "See https://example.org/api/getUser"}`|
|Leak internal system details like database names, technologies, and IP addresses.<br>`"Tibco failure querying MySQL database at 10.3.4.33"`|Provide a user-friendly message and a unique correlation ID for support and debugging.<br>`{"error": "User service unavailable...", "correlationId": "C3358-23"}`|
|Reveal the existence of a resource to an unauthorized user.<br><pre><code>GET /users/123 -> 404 Not Found<br>GET /users/456 -> 403 Forbidden<br>(This leaks that user 456 exists)</code></pre>|Protect information by always returning `404 Not Found` if the user cannot access the resource, regardless of whether it exists or not.<br><pre><code>GET /users/123 -> 404 Not Found<br>GET /users/456 -> 404 Not Found<br>(This protects information)</code></pre>|

## 5. Key Takeaways

1. **Codes are Communication:** HTTP status codes are the primary way a REST API signals the success or failure of a request.
2. **Remember the Families:** Grouping codes into families (**2xx** for success, **4xx** for client errors, **5xx** for server errors) is the fastest way to understand a response.
3. **4xx vs. 5xx is Your Responsibility Compass:** A `4xx` error means you (the client) need to fix something in your request. A `5xx` error means the problem is on the server's side, and you likely need to wait or report the issue.
4. **Error Messages Matter:** A good error response is just as important as a success response. It should be helpful and secure, never leaking internal system details.


# Best Practices for Resource-Oriented API Design

## 1.0 Introduction to Resource-Oriented Design

API design is a critical discipline for creating scalable, maintainable, and user-friendly systems. Well-designed APIs are among a company's greatest assets; successful public APIs, in particular, capture and retain customers. In contrast, poorly designed APIs create an unending stream of support issues and hinder development. The key to success lies in adopting a consistent and proven architectural style.

Resource-Oriented Design is an architectural style for networked APIs, built on foundational principles from AIP-121 that ensure clarity and predictability. It borrows many concepts from REST, focusing on a simple, data-centric model for interaction. The core principles are:

- **Resources as Building Blocks:** The API is structured around individually-named _resources_ (nouns). These are the fundamental entities of the system, such as users, products, or orders.
- **Standardized Methods:** A small, well-defined set of standard _methods_ (verbs) are used to perform most common operations on resources. This creates a uniform interface that is easy to learn and use.
- **Stateless Protocol:** Each interaction between the client and server must be independent and self-contained. The server does not maintain any client session state between requests, which enhances scalability and reliability.

This guide provides a prescriptive set of rules and patterns to ensure your API designs are consistent, intuitive, and adhere to these proven principles, leading to higher-quality outcomes for both developers and end-users.

## 2.0 The Core Philosophy: Modeling with Resources

The fundamental step in resource-oriented design is to identify the "nouns" of the system—the core data entities that the API will expose and manipulate. This data-centric model provides superior clarity and stability compared to a function-centric (RPC) model, which focuses on discrete actions. By modeling the API as a hierarchy of resources, we create a clear, navigable, and extensible structure.

### 2.1 Defining Resources and Collections

A _**resource**_ is an object with a type, data, and relationships to other resources. It is the basic building block of the API.

A _**collection**_ is a container for resources of the _same type_. For example, a `publisher` resource can have a collection of `books` that it publishes.

APIs **should** be modeled as a resource hierarchy. In this structure, each node is either a simple resource or a collection, creating a logical and intuitive map of the system's data.

### 2.2 Naming Resources and Collections

Clear and consistent naming is essential for a usable API. URLs identify resources, not actions, and their structure should be predictable.

- **Use Plural Nouns:** Collection names in URLs **must** use plural nouns. This convention clearly indicates that the URI represents a collection of items.
    - **Correct:** `/employees`, `/customers/C72/orders`
    - **Incorrect:** `/employee`, `/customer/C72/order`
- **Prefer Concrete Names:** Resource names **should** be concrete, intuitive, and meaningful to developers consuming the API. Avoid abstractions or internal company terminology that may be confusing.
    - **Correct:** `/employees`
    - **Incorrect:** `/apigeeks`
- **Avoid Verbs in URLs:** URLs identify resources (nouns) and **must not** contain verbs (actions). Actions are represented by HTTP methods (e.g., `GET`, `POST`), not by the URI itself. This is a common anti-pattern that leads to inconsistent and confusing API designs.

### 2.3 Decoupling from Internal Schemas

A resource-oriented API is an interface, not a direct window into a database. Making an API identical to the underlying database schema is an anti-pattern. This practice tightly couples the public interface to the internal system, making it brittle and difficult to evolve. An API has enormous flexibility in how it interprets and represents resources, and this separation allows the backend implementation to change without breaking client applications.

With a stable resource model defined, the next step is to standardize how clients interact with these resources using a universal set of methods.

## 3.0 Standard Methods: The Universal Verbs

A key strategic advantage of resource-oriented design is its reliance on a small, standardized set of methods. Instead of inventing unique verbs for every action, this approach uses the universal verbs of HTTP to create a predictable and consistent interface. This significantly reduces the learning curve for developers, as they can anticipate how to perform basic Create, Read, Update, and Delete (CRUD) operations on any resource in the API.

### 3.1 The Five Standard Methods

The primary CRUD functions map directly to five standard methods, which in turn correspond to standard HTTP verbs. This consistent mapping is the cornerstone of a resource-oriented API.

|   |   |   |   |
|---|---|---|---|
|Standard Method|HTTP Verb|Operation on a Collection (e.g., `/books`)|Operation on a Specific Resource (e.g., `/books/1234`)|
|`List`|`GET`|Retrieves a list of all books.|Not Applicable.|
|`Get`|`GET`|Not Applicable.|Retrieves the book with ID `1234`.|
|`Create`|`POST`|Creates a new book in the collection.|Not Applicable.|
|`Update`|`PUT` / `PATCH`|Bulk updates all books (use with caution).|Updates the book with ID `1234`.|
|`Delete`|`DELETE`|Deletes all books (use with caution).|Deletes the book with ID `1234`.|

### 3.2 Method Requirements and Behavior

To ensure a baseline level of functionality and predictability, certain methods are considered mandatory.

- **Get:** A resource **must** support the `Get` method at a minimum. Clients need to be able to validate the state of a resource after performing a mutation like `Create` or `Update`.
- **List:** A resource **must** also support the `List` method, except for singleton resources where only one instance can exist within its parent.

Methods must also adhere to specific behavioral properties. _Safety_ means the method does not change the resource's state. _Idempotency_ means that making the same request multiple times has the same effect as making it once.

- `GET` **must** be both safe and idempotent.
- `PUT` and `DELETE` **must** be idempotent but are not safe.
- `POST` is neither safe nor idempotent.

### 3.3 Consistent Resource Schema

For consistency, the resource schema for a given resource **must** be the same across all standard methods that return it. Whether a resource is returned from a `Create`, `Get`, `Update`, or `List` operation, its structure and fields should be identical. This ensures that clients can rely on a single, predictable data model for each resource type.

With this foundation of standard methods, we can now explore patterns for handling operations that fall outside the traditional CRUD model.

## 4.0 Advanced Method Design: Beyond Standard CRUD

While the five standard methods should be the default choice for API design, not all functionality maps cleanly to simple CRUD operations. Resource-oriented design provides established patterns for handling more complex or domain-specific actions without abandoning the core principles of consistency and predictability.

### 4.1 Custom Methods

Custom methods **should** be used to define functionality that does not map to any standard method. These are ideal for complex operations that may behave like database transactions, trigger data analysis, or perform other specialized business logic.

It is critical to note that custom methods do not involve creating new, non-standard HTTP verbs. Instead, they use a standard HTTP verb (usually `POST`) and are identified by a colon followed by the custom verb in the URI.

**Example:** `https://googleads.googleapis.com/v22/customers/1234567890:mutate`

In this example, `mutate` is a custom method applied to the specified customer resource.

### 4.2 Distinguishing PUT vs. PATCH for Updates

While both `PUT` and `PATCH` are used for updates, they have distinct semantics that are important to understand.

- **PUT:** An HTTP `PUT` request is used to **completely replace** a resource with the provided payload. If any fields are omitted from the `PUT` request, the server should treat them as if they are being cleared or reset to their default values.
- **PATCH:** An HTTP `PATCH` request is used to apply **partial modifications** to a resource. Only the fields included in the request payload are changed; all other fields on the resource remain unaffected.

Although many existing APIs use `PUT` with `PATCH` semantics (i.e., for partial updates), this is technically a violation of the HTTP standard. For new APIs, you **should** use `PATCH` for partial updates to ensure proper adherence to protocol standards.

### 4.3 Handling Non-CRUD Actions

For simple actions that do not fit the CRUD model but are not complex enough to warrant a full custom method (e.g., activating a user or walking a dog), a simple pattern can be used. This involves making a `POST` request to the resource's URI and specifying the action in a query parameter.

**Example:** To perform a "walk" action on a specific dog resource: `POST /dogs/1234?action=walk`

This approach keeps the URI clean and resource-focused while still providing a clear mechanism for triggering non-standard actions.

These advanced patterns provide flexibility, but their power is maximized when combined with clear, universal naming conventions for all API components.

## 5.0 Universal Naming Conventions

Naming is one of the most important aspects of API design. All names within an API—including interfaces, methods, resources, and messages—should be "straightforward, intuitive, and consistent." This ensures the API is immediately understandable to a broad audience of developers, reducing ambiguity and accelerating adoption.

### 5.1 General Naming Principles

To achieve clarity and consistency, all API components **should** adhere to the following principles:

- Names **should** be in correct American English (e.g., `color` not `colour`).
- Definitions **must** use `UpperCamelCase` (PascalCase), as in `ListBooksRequest`.
- Avoid name overloading. Use different, distinct names for different concepts to prevent ambiguity.
- Avoid overly general and ambiguous names like `Instance`, `info`, or `service`. Names should be specific and descriptive enough to distinguish them from other concepts. For example, instead of `alert`, specify whether it is an `alertRule` or an `alertNotification`.

### 5.2 Component-Specific Naming Rules

In addition to general principles, specific components have their own naming conventions.

#### Interface Names

An interface name defines an API service. It **should** be an intuitive noun that describes the service's purpose, such as `Calendar` or `BlobStore`. If there is a risk of a name conflict with other concepts in the API, a suffix like `Api` or `Service` **should** be used to disambiguate (e.g., `CalendarApi`).

#### Method Names

Method names **must** follow the `VerbNoun` convention in `UpperCamelCase`. The verb describes the action, and the noun is typically the resource type being acted upon.

- **Examples:** `GetBook`, `ListBooks`, `CreateBook`

#### Message Names

Message names (used for request and response payloads) **should** be short and concise. Avoid unnecessary adjectives or prepositions. For example, `ProxySettings` is preferred over `SharedProxySettings` if there is no concept of an _unshared_ proxy setting. Prepositions like "With" or "For" should also be avoided, as this logic can typically be better represented with optional fields on the message itself.

Clear naming, combined with strong architectural rules, creates a reliable and predictable API.

## 6.0 Essential Architectural Constraints

For a resource-oriented API to be robust, scalable, and reliable, it must adhere to several fundamental architectural constraints. These rules govern how the API manages state, ensures data consistency, and structures relationships between its resources.

### 6.1 Stateless Protocol

A resource-oriented API **must** operate over a stateless protocol. This means that every request from a client **must** contain all the information the server needs to process it, independent of any previous requests. The server does not store any client context or application state between calls. This constraint has clear implications for responsibility:

- The **server** is responsible for persisting data (the resource state).
- The **client** is solely responsible for maintaining the application state.

This separation makes the API highly scalable, as any server instance can handle any request without needing shared session information.

### 6.2 Strong Consistency Guarantee

A successfully completed mutation operation (`Create`, `Update`, `Delete`) **must** guarantee that the resource's state has reached a steady state. Clients rely on method completion as a signal that it is safe to proceed with subsequent operations.

This guarantee can be illustrated with the following examples:

- After a successful `Create` call, a subsequent `Get` request for that resource **must** return the created resource.
- After a successful `Update` call, a subsequent `Get` request **must** return the resource with its updated values.
- After a successful `Delete` call, a subsequent `Get` request **must** return a `NOT_FOUND` error.

This strong consistency is crucial for clients that need to orchestrate a sequence of operations, such as creating a resource and then immediately referencing it in a subsequent call.

### 6.3 Acyclic Resource Relationships

Relationships between resources **must** be representable as a directed acyclic graph (DAG). This means that a resource cannot have a dependency path that leads back to itself. For example, resource A can refer to B, but B cannot then refer back to A.

This constraint is critical because cyclic references significantly increase the complexity of resource management. Consider the difficulty of creating two resources that refer to each other:

1. Create resource A without a reference to B.
2. Create resource B with a reference to the newly created A.
3. Update resource A to add the reference to the newly created B.

This multi-step process is cumbersome and error-prone. Deletion becomes even more complex, requiring careful logic to determine which resource must be dereferenced first. By enforcing acyclic relationships, the API ensures that resource creation and deletion remain straightforward operations.

Adhering to these architectural constraints ensures a reliable foundation, upon which we can build a superior developer experience.

## 7.0 Enhancing the Developer Experience (DX)

A technically correct API is not enough to guarantee success. A great API must also be efficient and easy for developers to consume. The following practices are designed to reduce friction, improve performance, and create a superior developer experience (DX) that encourages adoption and correct usage.

### 7.1 Optimizing Payloads to Avoid "Chatty" APIs

"Chatty" APIs, which force clients to make an excessive number of network calls to accomplish a single task, are a common source of poor performance and developer frustration. To create an efficient interface, APIs **should** provide mechanisms for clients to control the data they receive.

- **Pagination:** For collections that can return a large number of items, pagination is essential. It allows data to be returned in manageable chunks, preventing overly large response payloads. This is typically implemented using `offset` and `limit` query parameters.
    - **Example:** `GET /users?offset=21&limit=10`
- **Filtering and Sorting:** Provide query parameters that allow clients to request only the data they need and in the order they need it. This reduces the amount of data transferred and offloads processing from the client.
    - **Example:** `GET /users?sort=lastName`
- **Partial Responses:** To further reduce network traffic, especially for mobile applications, allow clients to request a specific subset of fields in the response. This is often implemented with a `fields` parameter that accepts a comma-separated list of desired field names.
    - **Example:** `GET /employees/5678?fields=id,fullName`

### 7.2 Graceful Error Handling

When a request fails, the API **must** return an appropriate HTTP status code that accurately reflects the nature of the error (e.g., `400 Bad Request`, `404 Not Found`, `500 Internal Server Error`).

Furthermore, the response body for an error **should** contain a clear, actionable error message that helps the developer understand what went wrong. A link to relevant documentation can be invaluable. It is critical to **never** leak internal server details, stack traces, or IP addresses in error responses, as this poses a significant security risk.

### 7.3 API Versioning

APIs inevitably evolve. When making backward-incompatible (breaking) changes, a new version of the API **must** be introduced to avoid breaking existing client integrations. The most common practice is to include the version number directly in the URI path. This ensures that existing clients can continue to function without modification while new clients can adopt the updated version.

- **Example:** `/api/v2/users`

By adhering to these principles of resource-oriented design, from core modeling and naming to architectural constraints and developer experience, you can create high-quality, scalable, and long-lasting APIs that serve as true assets to your organization.