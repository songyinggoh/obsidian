> The top-level entity for Apigee

![[Screenshot 2025-10-25 162012.png]]
# Apigee Developer Onboarding Guide: Core Concepts and Architecture

## 1.0 Introduction to the Apigee Ecosystem

Welcome to the Apigee platform. This guide provides the foundational knowledge required to understand Apigee's architecture, its key components, and the standard developer workflow. This guide establishes the mental model required to operate effectively within our Apigee ecosystem. Mastering these core concepts is the first and most critical step toward becoming a productive and effective member of the API development team.

The primary structural components of Apigee are organized in a clear hierarchy. Understanding this structure is essential for navigating the platform.

1. **Organization:** The top-level container for all Apigee assets, including users, APIs, and configuration.
2. **Environment:** The runtime execution context where your API proxies are deployed and accessed by consumers.
3. **API Proxy:** The implementation of your API within the Apigee platform, acting as the interface for all inbound requests.

We will begin our exploration with a detailed look at the top-level entity that contains all others: the Apigee Organization.

## 2.0 The Apigee Organization: Your Top-Level Container

The Apigee Organization serves as the strategic hub for your entire API program. It is the central container where all API assets, user accounts, roles, and platform-wide configurations are managed. Every API proxy, product, and developer application you create will exist within the scope of a specific organization. Each Apigee Organization is directly associated with a single Google Cloud project, which provides the underlying cloud infrastructure and identity management framework.

An Apigee Organization is formally defined as the top-level entity within Apigee. While it is associated with one Google Cloud project and shares its name, it is important to note that an Apigee Organization is a distinct entity from a Google Cloud organization. All your work in the Apigee console will occur within the context of your assigned organization.

### Key Components within an Organization

The organization houses several critical components that you will interact with daily. These entities are defined at the organization level and provide the building blocks for API development, security, and consumption.

- **Users and Roles:** Access to the organization and its assets is governed by associating users with specific roles. This is managed through Google Cloud's Identity and Access Management (IAM). The `Apigee Organization Admin` role grants superuser access, while other built-in roles provide permissions tailored for operations, business, and API development team members.
- **API Proxies and Shared Flows:** These are the core assets that developers build. API Proxies are the concrete implementations of your APIs, and Shared Flows are reusable sequences of logic. While they are defined and stored at the organization level, they must be deployed to an environment to become active and handle live traffic.
- **API Products:** An API Product is the primary mechanism for grouping and "productizing" your APIs before they are published for consumption. It acts as a bundle of one or more API proxies and defines the terms of access, such as rate limits and authorization rules.
- **App Developers and Apps:** App Developers are the consumers of your APIs. They register themselves within your organization and create Apps to consume one or more API Products. Each registered App receives a set of credentials (like an API key) that it uses to authenticate requests.
- **Organization-Scoped Configuration:** Several configuration elements are managed at the organization level to ensure consistency. These include `Environment Groups` for mapping hostnames to environments, `Data Collectors` for capturing custom analytics data, and `Key Value Maps (KVMs)` for storing global configuration data.

These organization-level assets provide the foundation for your work, but they are only brought to life within the environments where they are deployed and executed.

## 3.0 Environments: The API Runtime Context

Environments play a critical role in the API lifecycle. They are the runtime execution contexts—the sandboxes and production stages—where your API proxies are deployed, tested, and ultimately run. Environments provide the necessary isolation between different stages of development, forming the backbone of a structured and reliable software development process.

An environment is a runtime execution context for API proxies; an API request can only be handled by a proxy that has been deployed to a specific environment. This separation allows you to manage traffic, configuration, and security independently for different stages of your workflow.

**The API Development Lifecycle: Development -> Test -> Production** Architecturally, we leverage environments to model and enforce a standard software development lifecycle (SDLC). A typical setup includes three environments: development, test, and production. An API developer works on a new proxy revision in the `development` environment. Once it functions as intended, that revision is deployed to the `test` environment for more formal quality assurance. After passing all tests, the same immutable revision is promoted to the `production` environment to handle live traffic.

Environments also provide granular control over permissions and contain components that are specific to that runtime context.

|   |   |
|---|---|
|Concept|Strategic Importance for a Developer|
|**User Permissions**|User permissions can differ between environments, ensuring separation of duties. For example, a developer may have full access in the `development` environment but no write access in `production`. Conversely, the support team might have read-only access in development but be permitted to trace proxies in `production`.|
|**Deployments**|API proxy and shared flow revisions are deployed to an environment to become active. A deployed revision is **immutable**; any edits or bug fixes require creating and deploying a new revision. This immutability is a core principle that guarantees stability and predictability, as the exact same artifact is promoted from testing to production without modification.|
|**Connectivity**|`Target Servers` are used to decouple backend service URLs from your proxy code. This is a critical architectural best practice for configuration management, as it allows you to point to different backends in dev, test, and prod without error-prone code changes. `Keystores` and `Truststores` are used to manage TLS certificates for securing connections.|
|**Runtime Data & Config**|`Caches` improve performance and reduce backend load. `Environment-scoped KVMs` store configuration like credentials that change between environments. `Flow Hooks` automatically attach a shared flow to every proxy in an environment. `Resource files` allow sharing of code libraries (e.g., common JavaScript or XSLT files) across proxies within an environment.|

Now that we understand where APIs run, we will shift our focus to the core assets that developers build and deploy: API proxies, policies, and shared flows.

## 4.0 Building APIs: Proxies, Policies, and Shared Flows

API Proxies are the fundamental building blocks for implementing your APIs in Apigee. They act as the facade for your backend services, processing all incoming requests and outgoing responses. To add functionality, developers use Policies and Shared Flows—powerful tools that enable rich features and promote reusability without the need for extensive custom code.

1. **API Proxies**
    - An API proxy is the component that implements your API on Apigee. It serves as the primary interface for processing API requests, decoupling the consumer-facing endpoint from your backend services.
2. **Policies**
    - Policies are pre-built, configurable modules that add specific functionality within the request and response flow of a proxy. They are the primary tools for implementing features like security (e.g., API key verification, OAuth2), traffic management (e.g., rate-limiting, spike arrests), message transformation (e.g., JSON to XML), or mediation. The key advantage of policies is that they allow you to add rich, complex features to your APIs without writing lots of code.
3. **Shared Flows**
    - Shared flows are reusable sequences of policies that can be invoked from multiple API proxies to implement common patterns. This promotes consistency and reduces duplication of effort for logic like centralized logging, security enforcement, or standard fault handling.
    - As detailed in the Environments section, these can be enforced platform-wide using `Flow Hooks` to automatically attach a specific shared flow to every API proxy deployed within an environment.

With a clear understanding of how to construct APIs, the next logical step in the lifecycle is to publish them for consumption.

## 5.0 Publishing and Consuming APIs: Products and Apps

Publishing an API is the process of making it available to consumers in a controlled and managed way. This is not as simple as deploying a proxy; it involves packaging APIs, defining access rules, and onboarding developers. In Apigee, API Products and Developer Apps are the key entities that govern this entire process of access and authorization.

The workflow for publishing an API and enabling consumption follows a clear, structured path:

1. **Productize the API:** Before an API can be consumed, one or more API proxies are grouped into an **API Product**. The API product is the primary vehicle for access control and authorization. It defines which APIs a developer can access, the associated quota or rate limits, and the environments where access is permitted.
2. **Register Consumers:** **App Developers** discover your available API Products, typically through a developer portal. To gain access, they must register their client **Apps**.
3. **Grant Access Credentials:** Upon successful registration, each App receives credentials, such as **API keys and OAuth tokens**. The client application must present these credentials with every API request.
4. **Control Access:** When a request arrives, Apigee uses the credential to identify the App and, by extension, the **API Product** it is subscribed to. This allows the API proxy to enforce the rules defined in the product. While API keys and tokens are stored at the organization level, they are generally associated with a single environment. The API Product also specifies which environment(s) the App is authorized to access, providing an additional layer of control.

After developing, deploying, and publishing your APIs, the final step is to leverage the platform's supporting services for ongoing management and visibility.

## 6.0 Key Supporting Services: Analytics and Configuration

A robust API platform relies on essential supporting services for monitoring, dynamic configuration, and performance optimization. Apigee provides a suite of tools that are critical for managing your APIs effectively, ensuring they are reliable, performant, and secure.

- **Analytics and Reporting** Apigee automatically captures a wide range of operational and business metrics for all API traffic, providing deep visibility into performance, error rates, and usage patterns. The platform includes a suite of built-in reports, and you can create custom reports to visualize specific business metrics captured using `Data Collectors`.
- **Configuration with KVMs** Key Value Maps (KVMs) are used to store and retrieve configuration data at runtime. They are available in two distinct scopes: organization-scoped KVMs are ideal for global, non-changing data, while environment-scoped KVMs are used for environment-specific data, such as backend credentials or other secrets that differ between your development, test, and production stages. KVMs can be encrypted, making them a secure place to store sensitive information.
- **Performance with Caching** Caches are used to store the responses of backend services, which can dramatically improve latency, reduce costs, and enhance scalability by eliminating unnecessary backend traffic. It is important to remember that caches are scoped only at the **environment level** to prevent data from leaking between different lifecycle stages.
- **Troubleshooting with Debug** The `Debug` tool (also known as Trace) is an indispensable resource for developers. It allows you to start a debug session to trace API traffic, capturing a detailed step-by-step view of the request and response flow as it is processed by the proxy's policies. This provides a clear view of policy execution, variable changes, and potential errors, making it a critical tool for troubleshooting.

These services complete the picture, providing the tools needed to manage and operate your APIs effectively throughout their lifecycle.

## 7.0 Summary: The Apigee Developer Workflow

This guide has outlined the core architectural concepts and the typical lifecycle of an API managed by Apigee. As a developer, your workflow will involve creating API proxies and shared flows composed of policies, deploying these assets through a series of environments (dev, test, prod), and bundling them into API Products for consumption. App developers will then register their applications to consume these products, and you will use Apigee's analytics and configuration services to monitor, manage, and optimize your APIs.

### Key Concepts Recap

- **Organizations** are the top-level containers that hold all your API assets.
- **Environments** are the runtime contexts for deployment and typically correspond to SDLC stages like development, test, and production.
- **API Proxies**, built with declarative **Policies**, are the core implementation of your APIs.
- **Shared Flows** promote the reusability of common logic, such as security and logging, across multiple proxies.
- **API Products** bundle one or more API proxies for publishing and provide the primary mechanism for access control.
- **Developer Apps** are created by API consumers to subscribe to API Products and receive credentials like API keys for access.


# Understanding Apigee's Structure: Organizations and Environments

### Introduction: Your Digital Filing Cabinet for APIs

Welcome! Imagine your company's entire API portfolio is managed out of a large, central headquarters. This headquarters needs a clear structure to keep everything organized—from the initial blueprints of an API to the final products that are shipped to customers. Apigee provides this structure through a clear hierarchy of components.

This document will explain the hierarchy of Apigee's core components for someone completely new to the platform. We will start at the highest level—the **Organization**—and work our way down to the individual API proxies and products that developers use every day.

--------------------------------------------------------------------------------

### 1. The Top Level: The Apigee Organization

The **Organization** is the highest-level container in Apigee, acting as the root for all your API resources. Think of it as the company headquarters that houses all departments, projects, and employees. Everything you do in Apigee happens within the context of an Organization.

Here are the most important things to know about an Apigee Organization:

- **Top-Level Entity:** This is the main container for all of your API assets, including the APIs themselves, the developers who use them, and the applications they build.
- **Connected to a Google Cloud Project:** Each Apigee organization is associated with exactly one Google Cloud _project_. It's important to note that this is _not_ the same thing as a Google Cloud _organization_.
- **Container for Users and Roles:** This is where you manage who has access to your Apigee instance. By assigning **Users** to specific **Roles**, you control who can create, deploy, and manage your APIs.

The Organization provides the overall structure, but the actual work of running APIs happens one level down, inside Environments.

--------------------------------------------------------------------------------

### 2. The Runtime Workspaces: Environments

An **Environment** is a "runtime execution context" where your API proxies are actually deployed and made active. They are isolated software environments within your Organization.

Following our company headquarters analogy, you can think of Environments as different floors or departments, each with a specific purpose, such as 'Development', 'Testing', and 'Production'. This separation is the primary way Apigee models and enforces an API development lifecycle, allowing you to build and test an API in one environment before promoting it to another, which highlights the critical distinction between where assets are _defined_ (the Organization) versus where they are _run_ (the Environment).

This separation is also crucial for managing permissions. Users can be given different access levels in each environment. For example, a developer might need full creative access in the 'development' environment but should have no write access in 'production', which is reserved for automated deployment processes.

|   |   |
|---|---|
|Scope|Purpose & Key Components|
|**Organization**|Defines the "blueprints" and overarching rules. Contains **API Proxies**, **Shared Flows**, and **API Products**.|
|**Environment**|Acts as the "factory floor" where blueprints are built and run. Contains **Deployments**, **Caches**, **Key Value Maps (KVMs)**, and **Target Servers**.|

This separation allows for environment-specific configurations, such as using `Target Servers` to point to a mock backend in 'test' and the live backend in 'production'.

With our workspaces defined, let's look at how the actual APIs are built and deployed within them.

--------------------------------------------------------------------------------

### 3. From Blueprint to Reality: API Proxies and Deployments

#### 3.1. API Proxies: The Blueprint for Your API

An **API Proxy** is the actual implementation of your API within Apigee. Returning to our headquarters analogy, if the Organization is the entire company, the API Proxy is the detailed architectural _blueprint_ for a specific service. This blueprint is built using **policies**, which are pre-built modules that add functionality like security checks, rate-limiting, and message transformation without requiring you to write a lot of code.

#### 3.2. Deployments: Making the Blueprint Live

A blueprint isn't useful until it's active on the factory floor. An API proxy does not receive any traffic until it is **deployed** to an Environment. The deployment is what makes the API available to receive traffic in a specific context (like `test` or `production`). Once a specific version (or 'revision') of a proxy is deployed, it is immutable, meaning any edits require creating and deploying an entirely new revision.

Now that the API proxy is live and receiving traffic, the next step is to package it in a way that developers can easily discover and consume it.

--------------------------------------------------------------------------------

### 4. From API to Product: Packaging for Developers

Simply deploying an API isn't enough; developers need a structured way to find, understand, and use it. This is where you "productize" your APIs.

#### 4.1. API Products: Your Public Catalog

An **API Product** is a bundle of one or more API proxies that you offer to developers. This is like the marketing department packaging different blueprints into a finished offering in the company's public _product catalog_. API Products are the primary way you control access and authorization for a group of APIs, allowing you to package different features for different developer communities.

#### 4.2. App Developers & Apps: Your Consumers

The consumers of your APIs are **App Developers**. To use your APIs, a developer must register their client **App** with your organization and subscribe to one or more of your API Products. This registration process is how the developer's app receives credentials, such as an **API key** or an **OAuth token**, which are required to make authorized calls to your APIs.

With all the individual components defined, let's now look at the complete hierarchy to see how they all fit together.

--------------------------------------------------------------------------------

### 5. The Complete Hierarchy: A Summary View

Let's tie everything together with a top-to-bottom summary of the relationships between these components.

1. A top-level **Organization** contains everything.
2. Within the Organization, you define one or more **Environments** (e.g., `test`, `prod`).
3. You build an **API Proxy** (the blueprint) at the Organization level.
4. You **deploy** that API Proxy to an **Environment** to make it live.
5. You bundle one or more API Proxies into an **API Product** to package them for consumption.
6. An **App Developer** registers their **App** to subscribe to your API Product.
7. The developer's **App** receives an API Key, which it uses to make calls to the API Proxy running in its specific **Environment**.

--------------------------------------------------------------------------------

### 6. Key Takeaways for Beginners

If you remember just three things from this guide, make it these:

- **Organization vs. Environment:** The **Organization** holds the _definitions_ (the blueprints), while **Environments** are where those definitions are _run_.
- **Proxies vs. Products:** An **API Proxy** is the _technical implementation_ of your API, while an **API Product** is the _business packaging_ you offer to developers.
- **Apps Provide Access:** Developers don't call APIs directly; their registered **Apps** consume **API Products** to get the keys needed for access.