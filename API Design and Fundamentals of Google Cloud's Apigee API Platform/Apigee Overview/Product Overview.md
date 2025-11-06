# Challenges and Apigee solutions 
The core purpose of Apigee, Google Cloud's API Management platform, is to **bridge the gap** between connected digital experiences (like apps, connected devices, and partner systems) and complex, rapidly evolving backend systems.
## I. Business Problems Solved by Apigee

The proliferation and diversification of customer-facing applications (web, mobile, connected devices) have created new challenges for businesses. These new challenges, which Apigee addresses, include:

|Business Problem|Underlying Challenge|
|:--|:--|
|**Security and Access Control**|Securing communication and access for new channels and devices. The real boundary of a company's control over policy enforcement and resource access ends at the API layer.|
|**Scalability and Performance**|Increasing scale to handle higher traffic and usage demanded by accelerating digital experiences.|
|**Complexity and Pace of Change**|Managing the diversity of requirements and the increasing speed at which applications evolve (days vs. years in the past). Backend systems struggle to keep up with the demands for speed and customization.|
|**Visibility and Data-Driven Decisions**|Improving visibility of business and technical metrics to allow for data-driven business decisions.|
|**Ecosystem and Reach**|Leveraging ecosystems and platforms to increase business reach. APIs themselves must be managed like products, including marketing them to internal and external audiences.|

## II. Solutions Provided by the Apigee Platform

Apigee solves these problems by inserting itself into the **Digital Value Chain** (Customer $\rightarrow$ App Developer $\rightarrow$ API $\rightarrow$ API Team $\rightarrow$ Backend). By building APIs tailored for connected experiences and implementing them on Apigee, companies create **abstraction layers that help reduce the complexity** required of backend systems.

The solutions correspond directly to Apigee's four core capabilities:

### 1. Solving Security, Access, and Control (Mediation & Developer Ecosystem)

Apigee provides the enforcement layer at the boundary of the company's control, which is the API layer.

- **Security Enforcement:** Apigee leverages features and policies—including `VerifyAPIKey` and `OAuthv2` policies—to explicitly **enforce key checking** in API proxies. Without a credential enforcement policy, any caller can invoke the APIs.
- **Access Control through API Products:** API products are the **central mechanism for access control**. They bundle one or more API operations and are used to define different tiers of access. API keys are provisioned for API products, ensuring that only registered apps associated with a product can access the bundled functionality.
- **Traffic Protection (Quota):** Apigee allows setting **quotas** on API products or specific operations to **protect backend servers from high traffic variances** and prevent them from being overwhelmed.
- **Mediation:** Mediation capabilities allow API teams to perform **enforcement of security** and **API abuse prevention** within the API call flow.

### 2. Solving Scale and Complexity (API Runtime & Mediation)

Apigee's ability to handle runtime traffic and mediate requests shields the backend from complexity and velocity issues.

- **Runtime Scalability:** The **API Runtime** is responsible for handling runtime API traffic and is offered in flexible deployment options (SaaS, Hybrid, Multi-Cloud) to meet high scale and performance needs. The Google Cloud-managed SaaS deployment accelerates time to market for new APIs and allows developers to immediately start building and running APIs **at scale**.
- **Abstraction and Customization:** Apigee provides features like **caching, transformation, and mediation**. These features allow the creation of APIs **tailored to the needs of individual applications** without requiring direct customization or modification of the backend services.

### 3. Solving Visibility and Data-Driven Decisions (API Analytics)

Apigee provides the necessary data to transition from simply tracking website visits to making strategic decisions.

- **Data Generation:** **Every API call** that passes through Apigee generates analytics data.
- **Decision Making:** This analytics data is used by operations teams and business users to gain **insights into technical and business challenges**. Apigee Analytics includes tools for viewing **Operational Metrics, Business Metrics, API Monitoring and Alerting**, and **API Program Metrics**.

### 4. Solving Ecosystem and Reach Challenges (Developer Ecosystem)

Apigee facilitates the treatment of APIs as true business products, enabling expansion of reach through partner and customer ecosystems.

- **Developer Portal:** The Developer Ecosystem capability is crucial for success. APIs bundled into **API products** are deployed to a **developer portal**. This web interface is dedicated to app developers, facilitating the **discovery and consumption of APIs**, and offering access to documentation.
- **Monetization:** The Developer Ecosystem includes **API Monetization** capabilities, allowing providers to define pricing tiers and product lines based on usage (e.g., unlimited vs. 100 requests/day) or access levels (e.g., read/write vs. read-only).
- **API Product Management:** APIs are the digital products presented to app developers, and Apigee provides the tools to **manage them through a lifecycle**, including marketing them to audiences.

# Digital value chain

The Digital Value Chain is a visualization tool used to understand how connected digital experiences are realized.

The **six components** of the Digital Value Chain, starting from the end user and moving towards the company's internal systems, are:

1. **Customer** (or "end users"): In a digitally connected world, companies interact with customers, or "end users," using applications.
2. **App** (Application): These applications facilitate interaction with customers and can include web and mobile apps, large enterprise systems, and smart connected devices. These applications may be built by the company itself or by partners.
3. **Developer** (App Developer): These individuals build applications that leverage the APIs offered by your company. Since some application developers using your APIs might be external to your company, the apps they create are typically not under your direct control.
4. **API (Application Programming Interface):** APIs serve as the **digital products** that you present to application developers. They act as abstraction layers intended to reduce the complexity that backend systems require. The **real boundary of a company's control** over policy enforcement and resource access management ends at the API layer. As products, APIs should follow a life cycle and be managed accordingly.
5. **API Team:** This is a cross-functional team responsible for building and managing the APIs. The APIs built by this team utilize backend resources while simultaneously shielding application developers from unnecessary complexity.
6. **Backend:** This component encompasses the backend resources utilized by the APIs. Historically, this layer consisted of Systems of Record such as CRM, ERP, SOA Database, and ESB/Integration. Today, this layer also frequently incorporates Microservices and Data Lakes.


# I. The Apigee Platform: Core Capabilities

The Apigee API Management Platform is designed to **bridge the gap** between connected digital experiences (like apps and connected devices) and complex backend systems. It provides a rich set of features necessary to secure, analyze, scale, and manage API traffic.

The platform is composed of **four core capabilities**:

1. **API Runtime**
    ◦ This component is the foundation of the platform and is responsible for **handling runtime API traffic**.
    ◦ It leverages multiple deployment models (see Section II).
    
2. **Mediation**
    ◦ Mediation provides the ability to **parse and manipulate** the requests and responses of API calls flowing through Apigee.
    ◦ API teams use mediation features to perform crucial functions such as **orchestration, transformation, caching, enforcement of security**, and handling of faults.

3. **API Analytics**
    ◦ **Every API call** passing through Apigee generates analytics data.
    ◦ This data gives business users and operations teams visibility into technical and business metrics, enabling them to make **data-driven decisions** about their APIs and API program.
    ◦ Analytics include API monitoring and alerting, operational metrics, business metrics, and API program metrics.

4. **Developer Ecosystem**
    ◦ This capability is an important factor in API success.
    ◦ It includes components necessary for externalizing APIs, such as the **API Catalog, API Marketplace, API Products**, and **API Monetization**.
    ◦ APIs are bundled into **API products** and deployed to a **developer portal** to facilitate discovery and consumption.

# II. Deployment Products (API Runtime Options)

Apigee offers flexible platform deployment options for the API runtime:

1. **Google Cloud-managed SaaS (Google-Managed Runtime)**
    ◦ This is the enterprise-level platform running fully managed by Google in Google Cloud.
    ◦ It is described as the **most popular deployment option**.
    ◦ This model simplifies customer adoption, accelerates time to market for new APIs, and allows customers to focus on business needs while Google manages the operational overhead.

2. **Hybrid Deployment (Hybrid Runtime)**
    ◦ This model is intended for customers who need to provide API access in **multiple clouds or on-premises**.
    ◦ The **customer manages and deploys** containerized versions of the API runtime on Kubernetes, while Google manages the management plane operations.

3. **Multi-Cloud Runtime / On-Premises Runtime**

    ◦ In these configurations, the runtime deployment (in a customer private cloud or on-premises data center) is **managed by the customer**, but the management services are managed by Google and run in Google Cloud.

4. **Apigee Adapter for Envoy**

    ◦ This is a **lightweight API gateway** that offers limited API management functionality.

    ◦ It can be deployed close to backend services.

III. API Products (The Digital Offering)

From a business perspective, APIs are considered the **digital products** presented to app developers. Apigee uses the specific entity called an "API product" as the **central mechanism for access control** and packaging.

Definition and Function

• **What it is:** An API product is created by the API provider to **bundle APIs** and make them available to app developers for consumption; it represents the provider's product line.

• **Bundling:** An API product bundles one or more **operations**. An operation specifies an API proxy, the resource paths, and can restrict access by HTTP methods and quotas.

• **API Keys:** API keys are provisioned for **API products** (the bundle of operations), not for the APIs themselves. When a developer app is registered, it must be associated with at least one API product, resulting in the assignment of a unique consumer key.

• **Enforcement:** Setting up an API product does not automatically enforce security. The API publisher must explicitly enforce key checking in API proxies using policies like `VerifyAPIKey` or `OAuthv2`.

Usage and Differentiation

API products are used to define different tiers and access levels:

• **Pricing/Quota Differentiation:** You can use API products to define pricing levels based on the **number of requests** (e.g., Free: 100 requests/day; Premium: Unlimited requests/day). Quotas can also be specified at the API product level or the operation level, with the operation quota taking precedence.

• **Access Level Differentiation:** Pricing can also be based on the **level of access** (e.g., Free: Read-only; Premium: Read, write, update, and delete).

• **Access Levels (Visibility):**

    ◦ **Public:** API products available to all developers, often listed on integrated or Drupal-based developer portals.

    ◦ **Private or Internal only:** API products designed for internal use or a select audience.