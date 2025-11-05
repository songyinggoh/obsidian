
--------------------------------------------------------------------------------
## 1.0 Introduction and Objectives

Welcome to this training module on generating Apigee API proxies. In modern API development, an OpenAPI Specification serves as a foundational "contract" that precisely defines an API's capabilities. Using this specification as the source for creating an API proxy is a strategic best practice. This approach accelerates development, ensures consistency across your API landscape, and fosters clear alignment between the teams building the API (producers) and the developers who will use it (consumers).

### Learning Objectives

Upon successful completion of this training, you will be able to:

- **Analyze** the structure of a sample OpenAPI specification to understand its endpoints and operations.
- **Execute** the creation of a new API proxy directly from an OpenAPI specification using the Apigee platform.
- **Verify** the functionality of the newly created proxy using Apigee's built-in exploration and tracing tools.

This manual will now guide you through the initial setup required to access the lab environment.

## 2.0 Prerequisites and Lab Environment Setup

A correctly configured environment is mandatory for this lab. Execute the following steps precisely and in order to prevent errors and ensure access to all required Google Cloud resources.

### Step-by-Step Setup Instructions

1. **Open an Incognito Browser Window:** To prevent conflicts with any existing Google accounts, begin by opening a new incognito or private browsing window.
2. **Sign In:** Navigate to the lab platform and sign in.
3. **Note the Time Limit:** Before starting, observe the lab's access time. This is a hard limit, and there is no pause feature. If you run out of time, you can restart the lab, but you will have to begin from the first step. Ensure you have adequate time to complete all tasks.
4. **Start the Lab:** When you are ready to begin, click **Start lab**.
5. **Record Your Credentials:** The lab will provide you with a temporary **Username** and **Password**. Note these credentials, as you will need them to sign into the Google Cloud Console.
6. **Open the Google Cloud Console:** Click the **Open Google Console** button.
7. **Authenticate Carefully:** On the Google sign-in page, select **Use another account**. Copy and paste the **Username** and **Password** you recorded from the lab platform into the appropriate fields. Using any other account will result in errors or potential charges.
8. **Finalize Login:** Accept the terms of service and, when prompted, skip the option to add recovery resources.

With your lab environment now active and accessible, you are ready to begin the core technical tasks.

## 3.0 Core Procedure: From Specification to Live Proxy

This section contains the primary workflow of the lab. The following tasks represent the end-to-end lifecycle of generating and verifying a new API proxy, a fundamental skill for any API developer or administrator working within the Apigee ecosystem.

### 3.1 Task 1: Examine the OpenAPI Specification

Before implementing any code or configuration, a thorough examination of the OpenAPI specification is the critical first step. This document is the authoritative source of truth that defines the API's contract, including its resources, available methods, and the structure of the data it expects and returns. Understanding this contract ensures the resulting proxy will accurately reflect the intended API design.

During your examination, you should focus on identifying the following key components:

- **Base Path and Endpoints:** Identify the primary URL path for the API and the specific resource paths available (e.g., `/users`, `/products/{product_id}`).
- **HTTP Methods:** For each endpoint, understand the allowed operations, such as `GET` for retrieving data, `POST` for creating data, or `DELETE` for removing it.
- **Request/Response Schemas:** Review the expected data structures for both API requests and the corresponding responses to ensure you understand the data model.

Once you have a clear understanding of the API contract defined in the specification, you can proceed to generate the proxy.

### 3.2 Task 2: Create an API Proxy Using the OpenAPI Specification

This task is the central action of the lab. Here, you will leverage the power of the Apigee platform to automate the creation of the proxy scaffolding directly from the OpenAPI specification. This specification-driven approach saves significant development time, minimizes the risk of manual configuration errors, and guarantees that the proxy's interface perfectly matches the API contract.

The conceptual workflow for this process within the Apigee UI involves the following key stages:

1. Navigate to the API Proxy creation wizard within the Apigee platform.
2. Select the option to use an existing OpenAPI Specification as the source for the new proxy.
3. Provide the specification to the wizard and configure essential proxy details, such as its name and base path.
4. Initiate the generation and deployment of the proxy to the designated lab environment.

With the proxy successfully created and deployed, the final and most crucial step is to verify its operation.

### 3.3 Task 3: Explore and Trace the API Proxy

Creating a proxy is only half the process; you must ensure it functions exactly as designed. Verification is paramount to confirming that the proxy correctly routes requests, applies policies, and returns the expected responses. Apigee provides powerful, integrated tools for debugging, validation, and understanding the complete request/response lifecycle.

**Exploring the Proxy**

The first step in verification is to make live test calls to the new proxy's endpoints. The "Explore" functionality allows you to send sample requests directly from the Apigee UI. This is the quickest way to confirm basic connectivity, validate that the proxy is listening for requests, and check that it returns successful responses from the backend service.

**Tracing a Request**

For more detailed analysis, the "Trace" tool provides an in-depth, step-by-step view of a transaction as it flows through the API proxy. A trace session captures the entire request and response flow, allowing you to inspect policy execution, view the state of flow variables, and pinpoint the exact source of any errors or unexpected behavior. This tool is indispensable for debugging complex logic and understanding the proxy's internal workings.

By completing these steps, you have successfully navigated the entire lifecycle: from analyzing a static design contract (the OpenAPI specification) to generating a live proxy and, finally, to verifying its real-world functionality using professional-grade tools.

## 4.0 Conclusion and Lab Completion

Congratulations on completing the core tasks of this lab. You have successfully navigated the fundamental workflow of creating a robust, specification-driven API proxy using Apigee. This process is a cornerstone of modern API management and a critical skill for building scalable and maintainable API products.

### Summary of Skills Acquired

Through this lab, you have demonstrated the ability to:

- Analyze a foundational OpenAPI specification.
- Execute the creation of an API proxy using the Apigee platform.
- Verify live proxy functionality with integrated exploration and tracing tools.

### Concluding the Lab Session

To release all allocated cloud resources, you must formally end the session. Click **End your lab** in the lab platform interface.