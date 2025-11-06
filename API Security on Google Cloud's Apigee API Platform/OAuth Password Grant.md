# A Practical Guide to Implementing the OAuth 2.0 Password Grant in Apigee

### Introduction: Understanding the OAuth 2.0 Password Grant

The OAuth 2.0 Resource Owner Password Credentials grant type, commonly known as the Password Grant, is a specific authorization flow designed for scenarios where an end-user has a high degree of trust in the client application. Its primary strategic purpose is to provide a streamlined and low-friction login experience for users of highly trusted, first-party applications that need to access protected user data. In this flow, the user directly provides their username and password to the client application, which then exchanges these credentials for an access token.

This convenience comes with a critical security trade-off. By requiring the user to share their credentials with the client application, the Password Grant is inherently less secure than other OAuth 2.0 flows, such as the Authorization Code grant, which never exposes user credentials to the client. For this reason, many organizations opt for the more secure Authorization Code grant even for their own applications. The Password Grant should be implemented with caution and reserved only for use cases where the application is fully trusted by the end-user.

--------------------------------------------------------------------------------

### 1. The High-Level Implementation Flow

Before beginning the technical implementation, it is crucial to understand the end-to-end flow and the role each component plays. The Password Grant process can be broken down into a two-phase operation: first, the client authenticates the user and generates access and refresh tokens; second, it uses those tokens to access protected backend resources on behalf of the user.

The following table identifies each participant in the Password Grant flow and defines their specific role.

|   |   |
|---|---|
|Component|Role in the Flow|
|**Client App**|A confidential and trusted first-party application that consumes the API. It handles user credentials temporarily and is responsible for protecting its own client secret.|
|**Apigee Proxy**|Acts as the API Gateway and authorization server intermediary. It validates the client app, delegates user authentication, mints and manages tokens, and validates tokens for resource access.|
|**User**|The resource owner who provides their username and password to the client app to grant it permission to access their protected resources.|
|**Authorization Server**|A backend service responsible for validating the user's credentials (username and password) and returning user details upon successful authentication.|
|**Backend Service**|The resource server that contains the actual business logic and protected data the client app needs to access.|

The flow can be summarized in two primary phases:

1. **Token Generation (Authentication):** The trusted client app sends a request to Apigee containing its own credentials (client ID and secret) along with the user's username and password. The Apigee proxy first validates the client app, then coordinates with an external Authorization Server to validate the user's credentials. Upon successful validation, Apigee mints an access token and a refresh token and returns them to the client app.
2. **Resource Access (Authorization):** The client app includes the newly acquired access token in the `Authorization` header of its requests to protected backend services. Apigee, acting as the gatekeeper, intercepts each request, validates the token's authenticity and expiration, and only allows valid requests to pass through to the backend service.

With this conceptual framework in mind, we can now proceed to the practical steps required for implementation.

### 2. Step-by-Step Guide: Token Generation and Issuance

This section provides the core instructions for configuring the token generation endpoint in an Apigee API proxy. This endpoint is responsible for securely exchanging a user's username and password for an access token that can be used to call protected APIs.

#### 2.1. The Client's Token Request

The process is initiated when the trusted client application makes a `POST` request to the Apigee `/token` endpoint. This request must be carefully constructed with the correct headers and payload.

- **HTTP Method and Endpoint:** `POST https://api.example.org/oauth/token`
- **Headers:** The request must include a `Content-Type: application/x-www-form-urlencoded` header. The client application's own credentials (its ID and secret) must be Base64-encoded and sent in the `Authorization: Basic <credentials>` header.
- **Payload:** The body of the request must contain the following form parameters: `grant_type=password`, `username`, and `password`.

The following cURL command provides a complete example of the token request. It demonstrates how to pass client credentials via the `-H "Authorization: ..."` flag and user credentials in the body via the `-d` flag.

```bash
curl -X POST "https://api.example.org/oauth/token" \
-H "Authorization: Basic YmFkOnBhc3N3b3Jk..." \
-H "Content-Type: application/x-www-form-urlencoded" \
-d "grant_type=password&username=bob&password=opensesame"
```

#### 2.2. Apigee Proxy Logic: Validating Credentials

Within the Apigee proxy, a two-part validation process must occur before a token can be issued.

First, Apigee validates the client application's credentials (`client_id` and `client_secret`) sent in the `Authorization` header. This ensures that the token request is coming from a known, registered, and trusted application.

Second, the proxy must validate the end-user's credentials. It is a critical implementation detail that the Apigee `OAuthV2` policy **does not** validate the end-user's username and password. The policy only checks that these values are present in the request. Therefore, the proxy developer is responsible for implementing the user authentication logic. This is typically achieved by using a `Service Callout` policy to make a request to an external identity provider or Authorization Server. If this external validation fails, the proxy must be configured to stop processing and return an error to the client app.

#### 2.3. Apigee Policy Configuration: The `GenerateAccessToken` Operation

After the client app and the end-user have been successfully authenticated, an `OAuthV2` policy configured with the `GenerateAccessToken` operation is used to mint the tokens.

```xml
<OAuthV2 continueOnError="false" enabled="true" name="O2-GenerateToken">
  <Operation>GenerateAccessToken</Operation>
  <ExpiresIn>1800000</ExpiresIn>
  <RefreshTokenExpiresIn>28800000</RefreshTokenExpiresIn>
  <SupportedGrantTypes>
    <GrantType>password</GrantType>
  </SupportedGrantTypes>
  <GrantType>request.formparam.grant_type</GrantType>
  <UserName>request.formparam.username</UserName>
  <PassWord>request.formparam.password</PassWord>
  <GenerateResponse enabled="true"/>
  <AppEndUser>backend.endUser</AppEndUser>
  <Attributes>
    <Attribute name="backend.data" ref="receivedData" display="false">NONE</Attribute>
  </Attributes>
</OAuthV2>
```

The key elements in this policy configuration are:

- `<SupportedGrantTypes>`: This element explicitly enables the `password` grant type for this policy.
- `<UserName>` and `<PassWord>`: These elements tell the policy where to find the user's credentials in the incoming request. The values shown (`request.formparam.username` and `request.formparam.password`) are the standard locations defined by the OAuth 2.0 specification.
- `<RefreshTokenExpiresIn>`: This sets the expiration time for the refresh token in milliseconds. A refresh token is issued alongside the access token in the Password Grant flow.
- `<AppEndUser>`: This element specifies a variable that contains the unique ID of the end-user, such as their username or email address. This value is associated with the token and returned in the token response.
- `<Attributes>`: This tag allows you to attach custom data to the token. In this example, it takes data from a flow variable named `receivedData` and attaches it as an attribute named `backend.data`. This `receivedData` flow variable is populated by the response from the external Authorization Server, which was called using a policy like `Service Callout` in the credential validation step described previously. This data will become available later when the token is verified.

#### 2.4. Analyzing the Token Response

Upon successful validation and token generation, Apigee returns a JSON payload to the client application.

```json
{
    "issued_at": "1565729213000",
    "scope": "READ",
    "application_name": "030fdcea-cf97-12084aea513c",
    "app_enduser": "bob",
    "refresh_token_issued_at": "1565729213000",
    "status": "approved",
    "refresh_token_status": "approved",
    "api_product_list": "[gold]",
    "expires_in": "119",
    "developer.email": "bob@example.com",
    "organization_id": "0",
    "token_type": "Bearer",
    "refresh_token": "IFl7jlijYuexu6XVSSjLMJq8SVXGOAAq",
    "client_id": "RqBca4HGxdyaDM6AAPIHfQ53kLLIGFMf",
    "access_token": "4WCAchNNtVyK8JsACl1HP7mlWW1X",
    "organization_name": "apigee",
    "refresh_token_expires_in": "28799",
    "refresh_count": "0"
}
```

Key fields in this response include:

- `**access_token**`: The token the client will use to make authorized API calls.
- `**refresh_token**`: A long-lived token that can be used to obtain a new access token after the original one expires. The `refresh_token_expires_in` field indicates its lifetime.
- `**app_enduser**`: The identifier for the end-user, corresponding to the value specified in the `<AppEndUser>` policy element.
- `**expires_in**`: The lifetime of the access token in seconds.

With these tokens, the client application is now ready to access protected resources.

### 3. Step-by-Step Guide: Accessing Protected Resources

Once a client application has a valid access token, it is authorized to access protected backend resources proxied by Apigee. This section details how to make an authorized API call and how Apigee validates the token to enforce security.

#### 3.1. The Client's Authorized API Call

The process for making a request to a protected API is identical for all OAuth 2.0 grant types. The client must include the access token as a Bearer token in the `Authorization` header of every request to a protected resource.

The following cURL command demonstrates a `GET` request to a protected `/orders` endpoint.

```bash
curl -X GET "https://api.example.org/orders/v1/items/14" \
-H "Authorization: Bearer 4WCAchNNtVyK8JsACl1HP7"
```

#### 3.2. Apigee Policy Configuration: The `VerifyAccessToken` Operation

Every Apigee proxy that protects a backend resource must include an `OAuthV2` policy configured to validate incoming tokens. This policy acts as a security checkpoint for every API call.

```xml
<OAuthV2 name="O2-VerifyAccessToken">
  <Operation>VerifyAccessToken</Operation>
</OAuthV2>
```

This simple policy configuration instructs Apigee to perform several critical security checks automatically:

1. It inspects the `Authorization` header for a Bearer token.
2. It verifies that the token is authentic, unexpired, and has not been revoked.
3. It rejects the request with an error if the token is missing or invalid.

If the token is valid, the request is allowed to proceed. Furthermore, any custom attributes that were attached to the token during its generation (using the `<Attributes>` tag) are extracted and made available as flow variables within the proxy. These variables, such as user details, can then be used by other policies or passed to the backend service.

With this validation in place, the API is secured, and access is restricted to authenticated and authorized clients.

### 4. Security Considerations and Best Practices

Implementing the Password Grant requires careful consideration of its security implications. While it offers a convenient user experience, it also introduces risks that must be actively managed.

1. **Use For Trusted Applications Only:** This grant type should be strictly limited to confidential, first-party applications where the user has a high degree of trust. Because it requires users to share their direct credentials with the application, it is inappropriate for any third-party or untrusted client.
2. **Implement as a Server-Side Flow:** The client secret is a sensitive credential that must be protected. Under no circumstances should this flow be executed in client-side code, such as a browser-based Single-Page Application (SPA) or a mobile app, where the client secret could be extracted and compromised. It is designed exclusively for server-side applications that can securely store their secrets.
3. **Prefer the Authorization Code Grant for Higher Security:** The Authorization Code grant is significantly more secure because it does not expose user credentials directly to the client application. It should be considered the default and preferred choice for user authorization, even for first-party applications, as it provides a stronger security posture.

--------------------------------------------------------------------------------

### 5. Conclusion

Implementing the OAuth 2.0 Password Grant in Apigee involves two primary stages of configuration. The first is setting up the token endpoint, which uses an `OAuthV2` policy with the `GenerateAccessToken` operation after a custom-built step to authenticate the end-user against an external identity provider. The second is securing the resource endpoint with another `OAuthV2` policy using the `VerifyAccessToken` operation to validate the token on every incoming request.

The Password Grant serves as a valuable tool for specific, trusted use cases where prioritizing user convenience is a key business driver. However, its implementation carries significant security responsibilities. By adhering to the best practices outlined in this guide, architects and developers can leverage its benefits while mitigating the associated risks.