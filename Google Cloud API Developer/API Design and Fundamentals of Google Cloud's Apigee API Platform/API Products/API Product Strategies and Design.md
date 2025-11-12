# API Product Management: An Onboarding Guide for New Product Managers

## 1.0 Introduction: Embracing the "Product, Not Project" Mindset

As you step into the role of an API Product Manager, your success hinges on one foundational shift in thinking: you must manage products, not projects. In the API economy, where APIs are the goods and services that power digital experiences, treating them as long-term, evolving products is the foundational principle for success. This distinction isn't merely semantic; it fundamentally changes how you approach design, development, and a successful go-to-market strategy.

The table below outlines the core differences between these two ways of thinking. Understanding them is your first step toward building a successful API program.


| Mindset             | Core Characteristics                                                                                                                                                                                                                                                                                                                                                                                                          |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Project Mindset** | **Focus:** Delivery-oriented, centered on project deliverables and a fixed delivery date.<br>**Design Basis:** Based on an ordered list of steps to complete a task.<br>**Adaptability:** Seeks to lock down requirements early to avoid "scope creep" and meet a deadline.<br>**Timeline:** Has a defined completion date, after which the team moves on to the next project.                                                |
| **Product Mindset** | **Focus:** Outcome-oriented, centered on fulfilling business requirements and market needs.<br>**Design Basis:** Based on evolving business requirements, often without a predetermined implementation plan.<br>**Adaptability:** Designed to adapt to changing market needs over time, making changes as necessary.<br>**Timeline:** Does not have a completion date; it is a continuous cycle of improvement and evolution. |

Our strong recommendation is to adopt a product mindset for your APIs and API products. Experience shows that this approach leads to better API adoption, higher developer satisfaction, and a more successful overall API program. By focusing on the outcome rather than just the delivery, you create assets that provide sustained value. To do that effectively, we must first clearly define what an API product truly is.

## 2.0 Defining the API Product: More Than Just Code

While APIs are inherently technical, a successful API _product_ must be defined, packaged, and managed with the same strategic rigor as any traditional consumer product. It is a bundle of functionality designed to meet the needs of a specific audience—the application developers who will consume it. To build a successful strategy, it's instructive to recognize the striking parallels between API products and the traditional products you already know.

- **Audience Focus:** Just as a traditional product is built to meet the needs of a target consumer, an API product is designed to meet the needs of a specific audience: the app developers who will use it. If developers don't want or need your product, there is little point in creating it.
- **Documentation:** All products require documentation that explains their features and how to use them. For an API product, this is critical for developer satisfaction. We use specifications like the OpenAPI Specification to allow developers to discover, understand, and try out our APIs, ensuring they can get the most out of the product.
- **Monetization/Pricing:** A product typically has a price. While many APIs generate value indirectly and are offered at no cost, they can be monetized. An API product can have a price based on usage, or it can be part of a revenue-sharing model where developers are paid when they drive sales through their applications. Tools like Apigee API Monetization provide a flexible way to manage these financial models.

Defining an API product is the strategic layer; the next step is to understand the technical specifications that serve as the formal blueprint for that product.

## 3.0 The Foundation of Design: Understanding API Specifications

API specifications are the authoritative "source of truth" for your API. These documents are not just for developers; they are the essential blueprint used throughout the entire API lifecycle, from the initial design phase through development, publishing, and monitoring. Any modification to these specifications has significant downstream impacts on the API proxies, the API products that bundle them, and the documentation presented to developers.

### The OpenAPI Specification

The **OpenAPI Specification** is a standard, vendor-neutral format for describing RESTful APIs. Written in either JSON or YAML, an OpenAPI document is machine-readable while also being easy for humans to understand. It allows you to formally describe key elements of an API, including its base path, available paths and HTTP verbs, parameters, response models, and more.

Note: This is a fragment of a full OpenAPI document, highlighting key structural elements.

```yaml
openapi: 3.0.0
info:
  description: OpenAPI Specification for the Apigee mock target service endpoint.
  version: 1.0.0
  title: Mock Target API
paths:
  /:
    get:
      summary: View personalized greeting
      operationId: View a personalized greeting
      description: View a personalized greeting for the specified or guest user.
      parameters:
        - name: user
          in: query
          description: Your user name.
          required: false
          schema:
            type: string
      responses:
        "200":
          description: Success
  /help:
    get:
      summary: Get help
      operationId: Get help
      description: View help information about available resources in HTML format.
      responses:
        "200":
          description: Success
...
```

For a Product Manager, this specification translates to:

- `info`: Contains metadata like the API's `title`, `description`, and `version`.
- `paths`: Defines the available endpoints. Here, we have two: the root path (`/`) and a `/help` path.
- `parameters`: Describes how a developer can pass information to the API. For the root path, a developer can optionally provide a `user` name as a query parameter (e.g., `/?user=Alex`). As a PM, defining parameters like this is how you give developers control over the data they receive, making your API more flexible and useful.
- `responses`: Details the possible outcomes of a call. A `"200"` response indicates a successful request.

### The GraphQL Schema

A **GraphQL schema** describes the data that is available for a client to query. Its primary benefits include providing a single endpoint for all operations and using a powerful query language that allows clients to request exactly the data they need, which prevents the common problems of over-fetching or under-fetching data.

The following example shows a GraphQL schema for a service managing student data:

```graphql
type Query {
  Greeting: String
  students: [Student]
}
type Mutation {
  createStudent(firstName: String!, lastName: String!): Student!
}
type Subscription {
  newStudent: Student!
}
type Student {
  Id: ID!
  firstName: String!
  lastName: String!
  password: String!
  collegeId: String!
}
```

For a Product Manager, this schema breaks down as follows:

- `Query`: Defines the data that can be read. This is analogous to the `GET` operations in a REST API, allowing clients to fetch data like a `Greeting` or a list of all `students`.
- `Mutation`: Defines the operations that can write or change data. This serves the same purpose as `POST`, `PUT`, `PATCH`, or `DELETE` in a REST API, allowing clients to use `createStudent` to add a new student record.
- `Subscription`: Defines a way for clients to receive real-time data updates. In this case, a client could "subscribe" to be notified whenever a `newStudent` is created.
- `Student`: Defines a data type. It specifies that a `Student` object will have fields like `Id`, `firstName`, and `lastName`.

These formal documents are the bedrock of your API product. As you will see in the following walkthrough, these specifications are the starting point from which we build API proxies and, ultimately, the targeted API products that deliver business value.

## 4.0 Strategic Design in Action: A Tiered API Product Walkthrough

Thoughtful API product design is a strategic exercise. It's about more than just exposing a backend service; it's about packaging functionality to serve specific audiences and achieve distinct business goals. The following walkthrough uses an example of a company with inventory and restocking APIs to illustrate how a single set of backend services can be packaged into multiple, distinct API products over time as business needs evolve.

### 4.1 Stage 1: The Initial Internal Product

The journey begins with an internal business need. An inventory team is responsible for tracking and restocking items in stores and uses its own set of APIs to manage this work: an **Inventory API** to view and update stock, and a **Restocking API** to manage orders.

At this stage, the team is the sole consumer of these APIs. The API Product Manager creates a single, straightforward product to meet this need: the **Inventory Team Product**. This product bundles full access to all operations within both the Inventory and Restocking APIs, exclusively for the inventory team's applications.

### 4.2 Stage 2: Expanding Internally with Controlled Access

Soon, other internal teams recognize the value of the inventory data. They want to view the current inventory and restocking status in stores but should not have the ability to modify data. Giving them full access would risk accidental inventory adjustments or unauthorized restocking orders.

The solution is to create a second, distinct API product: the **Internal RO Product**. This product grants `GET only` (read-only) access to the same Inventory and Restocking APIs. This strategic decision safely expands data access across the company without creating risk, as any attempt to update data (e.g., create a restocking request) is rejected before the request ever reaches the backend.

### 4.3 Stage 3: Launching an External Product

A new business opportunity arises: external app developers want to provide their users with in-stock information for products, which would drive more sales to the company's stores. The API Product Manager decides to create a product for this audience but must do so securely.

The **External Product** is created, offering highly specific and limited functionality: only the `GET in-stock` operation from the Inventory API. This design incorporates two key strategic decisions:

1. A custom attribute (e.g., `access_level: external`) is added to the product. This allows the API proxy to identify calls coming from external apps and return a reduced set of data, ensuring sensitive information like exact stock counts and supplier details are never exposed to external consumers, while still confirming availability.
2. The backend Inventory API did not have a simple `in-stock` call. Instead of waiting for a backend change, the API team created this new functionality directly within the API proxy. The proxy calls the existing inventory API and returns "in stock" if the inventory level is greater than zero, demonstrating agility.

### 4.4 Stage 4: Creating Premium Tiers for Partners

The external product is a success, but key partners now require more detailed information, such as the expected restock dates for out-of-stock items. To meet this need without giving this premium feature to all external developers, the product offering evolves.

The existing "External Product" is rebranded as the **Silver Product**, and a new **Gold Product** is created for key partners. This new premium tier includes access to a `GET stock-date` operation from the Restocking API.

| Product Tier       | Included API Operations                                               | Target Audience             |
| ------------------ | --------------------------------------------------------------------- | --------------------------- |
| **Silver Product** | `Inventory API (GET in-stock)`                                        | General External Developers |
| **Gold Product**   | `Inventory API (GET in-stock)` <br> `Restocking API (GET stock-date)` | Key Partners                |

This walkthrough powerfully demonstrates the core of API product strategy. This entire evolution—from a single internal tool to a tiered, monetizable external offering—is the "product mindset" in action. The API product was not a static project with an end date; it was a living asset that adapted continuously to new business requirements. From the same two backend APIs, we have created four distinct, targeted products that serve different audiences and business objectives. This was achieved with the agility and strategic control provided by an API management platform like Apigee, which forms the bridge between backend services and market-facing products.

## 5.0 The Power of Agility: Managing the API Product Lifecycle

As an API Product Manager, your most powerful strategic lever is the ability to create new value for the business faster than your backend systems can evolve. This is not an incremental improvement; it is a fundamental shift in how you operate, made possible by the principles of modern API management.

The most critical takeaway from the previous walkthrough is that **all of these API products were rolled out without making any changes to the backend APIs.** For an API Product Manager, this capability is transformative. It means you can:

- Move at the speed of business, not at the speed of backend release schedules.
- Experiment with new offerings, pricing tiers, and functionality with minimal risk and investment.
- Align your API products directly with strategic business goals, such as entering new markets or serving new partner segments, quickly and efficiently.

This agility is made possible by creating an API proxy from an OpenAPI document. This process automatically generates the necessary paths, parameters, and conditional flows based on your specification. Upon this foundation, a Product Manager can then layer product-specific features like OAuth security, caching, and rate limiting to define the final product—all without touching the underlying service.

Your role as an API Product Manager is to leverage these tools and concepts to design, package, and evolve API products that drive tangible business value.

## 6.0 Conclusion: Your Strategic Role as an API Product Manager

As a new API Product Manager, your primary responsibility is to act as the strategic bridge between technical assets and business value. By internalizing the concepts in this guide, you can effectively design and manage products that meet the needs of your developer customers and advance the goals of your organization.

Here are the three key takeaways to guide you:

1. **Adopt a Product Mindset:** You are not managing a project with an end date; you are managing a product with a continuous lifecycle. Your focus must be on the business outcome and adapting to market needs over time.
2. **Serve Your Customer:** The application developer is your customer. Every API product you create must be designed to meet their specific needs, supported by clear documentation, and packaged for ease of use.
3. **Master Your Platform to Drive Agility:** Your API management platform is not just infrastructure; it is your primary tool for strategic action. Master its capabilities to create, test, and evolve products at the speed of business, decoupling your product strategy from backend release cycles.