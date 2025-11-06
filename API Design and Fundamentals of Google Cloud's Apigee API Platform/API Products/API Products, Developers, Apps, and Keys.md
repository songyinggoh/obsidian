# Onboarding Guide: Integrating Your Application with Apigee-Powered APIs

This guide provides the essential knowledge for integrating your application with our Apigee-powered APIs. Our primary goal is to provide you with a clear and structured path to discovering, registering for, and successfully consuming our APIs.

Throughout this guide, we will cover the core components of Apigee's access model, walk through the step-by-step developer workflow, and detail the technical mechanism used to verify your API requests. Mastering these fundamental concepts will ensure a smooth, secure, and successful integration process, enabling you to leverage the full power of our APIs in your applications.

--------------------------------------------------------------------------------

## 1.0 Understanding the Apigee Access Control Model

To integrate successfully, you must first master the four entities that form Apigee's access control model. This model is not just a security layer; it's the blueprint for how we package, deliver, and manage our API services. Understanding these components is a prerequisite for making your first API call.

1. **1.1 API Products** An **API Product** is a bundle of API operations packaged specifically for developer consumption. Its primary function is to control access to our APIs. By grouping API resources and operations into distinct products, we can implement different business models and service levels tailored to various use cases.
2. Common examples of how API Products are used include:
    - **Access Tiers:** Differentiating between `read-only` and `read-write` access to the same underlying API.
    - **Audience Segmentation:** Providing different capabilities for `internal` versus `public` applications.
    - **Service Plans:** Offering tiered access levels, such as `free`, `standard`, and `premium` plans with varying features or capacity.
3. **1.2 App Developers** An **App Developer** is the individual or entity—you—who builds applications that consume our APIs. Within the Apigee platform, each developer is identified by their `Name`, `Email address`, and `Username`. Crucially, custom attributes can be attached to a developer profile, allowing for user-specific logic within the API proxy.
4. **1.3 Applications (Apps)** An **App** is the specific software you register to consume one or more API Products. Registering an app is the formal process of requesting access to a set of APIs. Upon successful registration, each app is issued a unique set of credentials to identify itself when making API calls. Like developers, apps can also have custom attributes attached, enabling fine-grained, app-specific behavior.
5. **1.4 Credentials & API Keys** The credentials issued to a registered app consist of a **Consumer Key** and a **Consumer Secret**. For the purpose of making authenticated API requests, the **Consumer Key is used as the API Key**. This API Key uniquely identifies your application and must be included in every request to access protected API operations.

These four entities work together in a sequence often called the "Digital Value Chain." The **API Team** bundles APIs into **API Products**. As an **App Developer**, you discover these products and register an **App** to subscribe to them. This registration process grants your App a unique **API Key**, which it uses to make authenticated calls to the APIs.

With this foundational understanding, let's proceed to the practical steps you will take to get started.

--------------------------------------------------------------------------------

## 2.0 The Developer Workflow: From Discovery to Your First API Call

This section provides a practical, step-by-step walkthrough of the workflow every developer follows to gain access to an API. These steps are a direct application of the access control model described previously, translating the conceptual framework into a clear action plan.

1. **Step 1: Discover and Select API Products** Begin by exploring the available APIs on our developer portal. These APIs are presented as **API Products**, each bundling a specific set of operations and designed for a particular use case or service level. Select the API Product(s) that provide the functionality your application requires.
2. **Step 2: Register Your Application** Once you have identified the necessary API Products, register your application. This process formally associates your app with the selected products, granting it the right to access the corresponding APIs and operations.
3. **Step 3: Obtain Your Credentials** Upon successful app registration, the platform will generate a unique set of credentials for your app: a **Consumer Key** and a **Consumer Secret**. These credentials are the keys to accessing the API. It is critical that you store them securely.
4. **Step 4: Make an Authenticated API Request** To consume a protected API, include your credentials with each request. Specifically, the **Consumer Key** must be sent as the **API Key**. Any request made to a protected endpoint without a valid API Key will be automatically rejected by the platform.

Now that you understand the developer workflow, let's explore the technical mechanism that validates your API key with every call.

--------------------------------------------------------------------------------

## 3.0 How Your Access is Verified: The `VerifyAPIKey` Policy

Behind the scenes, the security of our APIs is enforced by a powerful technical mechanism within Apigee. A specialized policy named `VerifyAPIKey` acts as a gatekeeper, inspecting every incoming request to ensure the sender is authorized to access the requested resource.

### 3.1 Policy Function and Placement

The `VerifyAPIKey` policy is responsible for one critical task: validating the incoming API key. To ensure that unauthorized requests are stopped immediately, this policy is almost always attached at the very beginning of the API processing flow, in a stage known as the "proxy endpoint request preflow."

When a request arrives, one of two outcomes occurs:

- If the API key is missing, invalid, expired, or not authorized for the requested API product, the policy **will raise a fault**, immediately halting processing and returning an error to the caller.
- If the API key is valid, processing continues to the next step in the API proxy.

### 3.2 Example Policy Configuration

The behavior of the `VerifyAPIKey` policy is defined in an XML configuration. A typical configuration looks like this:

```xml
<VerifyAPIKey continueOnError="false" enabled="true" name="VK-VerifyKey">
  <APIKey ref="request.header.apikey"/>
</VerifyAPIKey>
```

The key elements in this configuration determine how the policy operates:

|   |   |
|---|---|
|Element|Description|
|`<Name>VK-VerifyKey</Name>`|The unique name of this policy instance. This name is how the policy is referenced within the `<Step>` of the proxy's processing flow.|
|`<APIKey ref="request.header.apikey"/>`|Specifies where the policy should look for the incoming API key. In this example, it's an HTTP header named `apikey`.|

**Note:** While this example uses an HTTP header (`apikey`), the policy can also find the key in a query parameter. In fact, searching in a query parameter named `apikey` is the policy's default behavior if not otherwise configured. Remember that HTTP headers are case-insensitive, while query parameters are case-sensitive.

Once the policy successfully validates your key, it does more than just grant access; it enriches the request context with valuable information.

--------------------------------------------------------------------------------

## 4.0 Leveraging Context: Using Variables After Verification

One of the most powerful features of the Apigee platform is its ability to provide rich contextual data after a successful API key verification. When the `VerifyAPIKey` policy validates a key, it automatically populates a set of variables containing detailed information about the developer, the application, and the API product associated with the request. This enables the API to implement dynamic, intelligent behavior tailored to each specific caller.

### 4.1 The Variable Format

These context variables follow the standard format `verifyapikey.{policy_name}.{var_name}`, where `verifyapikey` is the policy type, `{policy_name}` is the specific instance name you defined (e.g., `VK-VerifyKey`), and `{var_name}` is the specific data point.

### 4.2 Available Contextual Data

After successful verification, a wide range of information becomes available. This data is logically grouped by the entity it describes:

#### Developer Variables

- `verifyapikey.VK-VerifyKey.developer.firstName`
- `verifyapikey.VK-VerifyKey.developer.lastName`
- `verifyapikey.VK-VerifyKey.developer.email`
- `verifyapikey.VK-VerifyKey.developer.{custom_attr_name}`

#### App Variables

- `verifyapikey.VK-VerifyKey.app.name`
- `verifyapikey.VK-VerifyKey.app.apiproducts`
- `verifyapikey.VK-VerifyKey.{custom_app_attr_name}`

#### API Product Variables

- `verifyapikey.VK-VerifyKey.apiproduct.name`
- `verifyapikey.VK-VerifyKey.apiproduct.{custom_attr_name}`
- `verifyapikey.VK-VerifyKey.apiproduct.developer.quota.*`

**Important Note on Multiple API Products:** If an app's credentials are associated with more than one API Product, the `VerifyAPIKey` policy will only populate the variables for _one_ of those products. You should design your integration with the understanding that context variables for a single, specific API Product will be available per request.

### 4.3 The "So What?": The Power of Custom Attributes

The true strategic value of this feature lies in the handling of **custom attributes**. Any custom attributes defined on the API Product, Developer, or App entities are automatically populated as context variables during the request.

For instance, an API Product might have a custom attribute like `service-plan: premium` or an app might have one like `readOnly: yes`. An API proxy can then inspect these variables at runtime to dynamically alter its behavior. It could enable premium features, enforce read-only restrictions, or apply different rate limits—all based on the attributes associated with the validated API key. This powerful mechanism directly connects the business rules defined in the access control model to the real-time functionality of the API. This means that a business decision—such as upgrading a partner to a 'premium' tier via a custom attribute—can be instantly reflected in the API's behavior without requiring a new code deployment.

--------------------------------------------------------------------------------

## 5.0 Summary and Next Steps

This guide has provided a comprehensive overview of the essential concepts for integrating with APIs on the Apigee platform. We have covered the four core entities of the access model—API Products, Developers, Apps, and API Keys—that govern security and access. We have also walked through the standard developer workflow for gaining API access and examined the technical function of the `VerifyAPIKey` policy that enforces these rules.

We encourage you to use this guide as a reference as you begin your integration project. A clear understanding of these principles will empower you to build robust, secure, and effective applications that leverage the full capabilities of our API ecosystem.