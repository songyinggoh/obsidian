# The Journey of an API Request in Apigee: From Client to Proxy

Welcome! As a Solutions Architect, one of the most common questions I hear is, "When a client sends a request to my API, how does Apigee know exactly where to send it?" It's a fantastic question, and the answer reveals the elegant and powerful routing system at the heart of Apigee. This document will demystify that process, showing you step-by-step how an API request finds its correct destination.

To make this journey clear, let's use a central analogy that we'll refer to throughout our explanation.

**The Postal Service Analogy**

Think of Apigee's routing like a postal service. A request is a letter with an address. The **hostname** (`api.example.org`) is like the City and ZIP Code, getting the letter to the right regional sorting center. The **base path** (`/orders/v1`) is the specific street address, ensuring it gets to the correct mailbox.

Before we can understand the address on the letter, we first need to understand the postal service's overall infrastructure.

## 1. The Big Picture: Where Does the Request Go?

Apigee on Google Cloud has two primary components that work together:

- **Apigee services:** This is the management layer. It provides the APIs and tools you use to create, manage, and deploy your API proxies.
- **Apigee runtime:** This is the Google-managed, containerized engine where the magic happens. All of your API traffic, regardless of its origin or destination, passes through and is processed by the Apigee runtime.

The most important takeaway here is that every single API request is first sent to the Apigee runtime.

Now that we know all requests arrive at the runtime, how does Apigee sort through them to find the correct API proxy?

## 2. The First Stop: The Hostname & Environment Group

The first piece of the address that Apigee inspects is the **Hostname**. This is the domain name used in the request, such as `api.example.org`.

Apigee uses a mechanism called **Environment Groups** to connect these hostnames to one or more **Environments** (logical groupings for your proxies, often used for different lifecycle stages like `test` or `production`).

Revisiting our postal service analogy, the hostname acts like the City and ZIP code. It directs the request to the correct "regional sorting center," which is the Environment Group. This immediately narrows down the possible destinations for the request to only those proxies deployed within that group's environments.

The relationship between hostnames, groups, and environments follows a clear set of rules.

|   |   |
|---|---|
|Rule|Description|
|**Hostname to Group**|A specific hostname (e.g., `api.example.org`) can only be assigned to a single environment group.|
|**Group to Hostnames**|An environment group can have multiple hostnames assigned to it.|
|**Group to Environments**|An environment group contains one or more environments.|
|**Environment in Groups**|A single environment can be a member of more than one environment group.|

This system allows you to cleanly separate traffic. For example, you might assign `api.example.org` to your `production` environment group and `test-api.example.org` to your `test` environment group, ensuring that no test traffic ever accidentally reaches your production APIs.

Furthermore, this model enables sophisticated, fine-grained access control. Imagine you want to give specific partners access to your production order system but not your inventory system. You could create a `partnerprod` environment group with its own unique hostnames (e.g., `ace-api.example.org`, `zap-api.example.org`) and assign only the `prod-ordersystem` environment to it. This isolates partner traffic and ensures they can only access the APIs you've explicitly granted them.

Once the request has reached the correct group of environments, Apigee needs a more specific address to find the final destination.

## 3. The Final Destination: The Base Path & Proxy Endpoint

After using the hostname to identify the right environment group, Apigee looks at the next part of the URL: the **Base Path**. This is the _first part_ of the URL that immediately follows the hostname, such as `/orders/v1`.

The Base Path is defined within an API proxy's configuration, specifically in a component called the **Proxy Endpoint**. It acts as the unique entry point for that proxy.

In our postal service analogy, the base path is the "street address." It pinpoints the exact mailbox (the API Proxy) within the city (the Environment Group).

When deploying proxies, you must consider the following rules regarding base paths:

- **Uniqueness:** A base path must be unique for all proxies deployed to the _same environment_. You cannot have two different proxies in the `prod-ordersystem` environment with the base path `/orders/v1`.
- **Longest Match Wins:** If multiple base paths could potentially match an incoming request (e.g., you have one proxy with `/orders` and another with `/orders/v1`), Apigee will route the request to the proxy with the longer, more specific base path.
- **Routing Conflicts:** You will be warned if you deploy a proxy with a base path that already exists in another environment within the _same environment group_. This isn't just a conflict to avoid; savvy architects use this behavior for strategic purposes. It allows you to move a deployed proxy from one environment to another within the same group with zero downtime, as the second proxy will not receive traffic until the first is undeployed.

Now, let's combine these two concepts—hostname and base path—to trace a request from start to finish.

## 4. Tracing a Request: A Complete Step-by-Step Example

Let's imagine a client application needs to get information about an order and sends the following API request:

`https://api.example.org/orders/v1/items/123`

Here is the step-by-step journey that request takes through Apigee:

1. **Request Sent:** A client sends the `GET` request to `https://api.example.org/orders/v1/items/123`.
2. **Hostname Match:** The Apigee runtime receives the request. It first inspects the hostname, `api.example.org`. It looks up this hostname and finds it is assigned to the `production` environment group. The search is now limited to proxies deployed in the environments within this group (e.g., `prod-ordersystem`, `prod-inventory`).
3. **Base Path Match:** Next, Apigee inspects the path `/orders/v1/items/123`. It searches for proxies in the `production` group's environments with a matching base path. Even if a proxy with a shorter base path like `/orders` existed, Apigee selects the proxy with the base path `/orders/v1` because it adheres to the "longest match wins" rule, ensuring the most specific endpoint is chosen.
4. **Successful Routing:** The request is successfully routed to the correct API proxy for processing. The rest of the path, `/items/123`, is then handled by the logic inside that specific proxy.

## 5. A Note on Security: HTTP vs. HTTPS

One final, important technical detail relates to the type of connection used. While APIs can be designed to use unencrypted HTTP or encrypted HTTPS, the Apigee runtime has a strict rule: the runtime instance only accepts **HTTPS** traffic.

This means a customer-managed load balancer plays a critical role:

- For **HTTPS** requests, the secure TLS connection from the client is terminated at the load balancer, which then forwards the request to the Apigee runtime.
- For **HTTP** requests, the load balancer must accept the unencrypted traffic and then forward it as an encrypted HTTPS request to the runtime.

Because of this architecture, and for general security best practices, it is strongly recommended that your APIs only accept HTTPS requests.

## 6. Conclusion: The Two Keys to Apigee Routing

As we've seen, Apigee's routing is a logical, two-step process that efficiently directs any incoming API call to its precise destination from among potentially thousands of deployed proxies.

Just like a letter needs both a ZIP code and a street address to be delivered, an API request in Apigee needs a **Hostname** to find the right environment group and a **Base Path** to find the specific API proxy. By understanding these two keys, you've unlocked the core of how Apigee manages and directs API traffic.


