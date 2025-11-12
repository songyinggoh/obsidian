# Understanding JSON Web Tokens (JWTs): A Beginner's Guide

### Introduction: What Problem Do JWTs Solve?

Imagine you have a ticket to a concert. This single piece of paper contains all the information needed to grant you access: the event name, your seat number, and a unique barcode. The ticket taker doesn't need to look you up in a central database; they simply scan the barcode to verify that the ticket is authentic and valid. This ticket is a self-contained, verifiable piece of information.

A JSON Web Token (JWT) works in a very similar way. Its core purpose is to provide a compact and self-contained method for securely transmitting information between parties as a JSON object. This information can be trusted because it is digitally signed, just like the concert ticket's barcode proves its authenticity. This guide will break down the fundamental characteristics of a JWT, its structure, and how it's used in modern applications.

## 1. Core Concepts: The "What" and "Why" of JWTs

A JSON Web Token, commonly pronounced "jot," is a JSON-based access token that is used to assert a set of "claims." A claim is simply a statement, like a user's name, their email address, or their permissions. JWTs have a few critical characteristics that make them incredibly useful for developers.

- **Digitally Signed**: Every JWT contains a cryptographic signature. This allows a service that receives the token to verify its authenticity and integrity locally, without needing to make a network call back to a central token server that issued it. By avoiding this network hop, verification is extremely fast and efficient, which is a major benefit in modern microservices architectures where minimizing network latency is a primary concern.
- **Stateless**: Because a JWT is self-contained and can be verified on its own, servers don't need to store session information in a database. The token itself contains all the necessary information about the user's session, which simplifies server architecture and improves scalability.
- **Trade-offs (Non-Revocable)**: This stateless, self-contained nature comes with a significant security consideration. Because JWTs are verified locally, they generally cannot be revoked before they expire. If an _opaque token_—which is just a random string of characters that must be checked against a central server—is compromised, it can be immediately invalidated on that server. But with a JWT, since verification is local and there's no call back to an issuer, a compromised token remains valid until it expires. The critical best practice to mitigate this risk is to ensure **JWTs have a short Time-To-Live (TTL)** or expiration time (`exp` claim) to limit the window of potential misuse.

These characteristics are made possible by the token's specific three-part structure, which cleverly separates metadata, data, and proof of integrity. Let's break it down piece by piece.

## 2. The Anatomy of a Signed JWT

A signed JWT consists of three distinct parts, separated by dots (`.`): `Header.Payload.Signature`.

Each of these parts is **Base64URL encoded**. It's crucial to understand that this is an _encoding_ for safe URL transmission, not _encryption_ for secrecy. Anyone who intercepts a JWT can easily decode the Header and Payload to read their contents.

1. **Header (Metadata)**
    - **Purpose**: The Header contains metadata about the token itself, primarily the signing algorithm used (`alg`) and the type of the token (`typ`).
    - **Example (Unencoded)**:
2. **Payload (The Claims)**
    - **Purpose**: The Payload contains the claims, which are statements about an entity (typically the user) and any other required data. There are two main types of claims.

|   |   |   |
|---|---|---|
|Claim Type|Description|Example(s)|
|**Registered Claims**|Predefined, standard claims recommended for use. These are not mandatory but provide a common baseline.|`iss` (issuer), `sub` (subject), `aud` (audience), `exp` (expiration time), `iat` (issued at)|
|**Custom Claims**|User-defined claims created to share information specific to the application.|`name`, `locale`, `email`|

```
*   **Example (Unencoded)**:
    ```json
    {
      "sub": "18373426",
      "iss": "example.com",
      "aud": "idsvc",
      "iat": 1570819380,
      "exp": 1570819680,
      "name": "Joe Apigeek",
      "locale": "en",
      "email": "apigeek@example.com"
    }
    ```
```

1. **Signature (The Verification)**
    - **Purpose**: The Signature is the most critical part for security. It guarantees the integrity of the token, verifying that the sender is who they claim to be and that the Header and Payload have not been tampered with since the token was created.
    - **Creation**: It is created by taking the Base64URL-encoded Header and Payload, concatenating them with a dot, and then cryptographically signing the result using the algorithm specified in the header (e.g., `HMACSHA256`) along with a secret or private key.

This three-part signed structure brilliantly guarantees a token's integrity, but it's vital to remember what it _doesn't_ do: provide confidentiality. This brings us to a critical distinction every developer must understand: the difference between a signed token and an encrypted one.

## 3. Signed vs. Encrypted Tokens: A Critical Distinction

While the previous section focused on _signed_ JWTs (which provide integrity and authenticity), it's also possible to create _encrypted_ JWTs (which provide confidentiality).

|   |   |   |
|---|---|---|
|Feature|Signed JWT (JWS Format)|Encrypted JWT (JWE Format)|
|**Primary Goal**|**Integrity & Authenticity:** Prove the data hasn't been changed and came from a trusted source.|**Confidentiality:** Keep the payload's contents secret and unreadable to unauthorized parties.|
|**Payload Visibility**|Base64URL encoded, but **readable** by anyone.|**Unreadable** without the correct decryption key.|
|**Structure**|Three parts: `Header.Payload.Signature`|Five parts: `Header.Key.InitializationVector.Payload.AuthTag`|

The takeaway is simple: use a **signed JWT** when the information inside can be public but must be trusted. Use an **encrypted JWT** when the information itself is sensitive and must be kept secret. It's also possible to combine these security guarantees by first signing a JWT and then encrypting the entire signed token.

This distinction between signed and encrypted tokens is critical, and it's worth knowing that the signed format (JWS) is actually a more flexible standard that exists beyond JWTs. Understanding this relationship clarifies why these technologies are structured the way they are.

## 4. A Quick Look at JWS (JSON Web Signature)

The relationship between JWT and JWS is straightforward: a signed JWT is a specific implementation of the JWS standard. JWS is a more flexible and general-purpose standard for signing content.

Here's how JWS is more flexible than a standard signed JWT:

- **Payload Format**: A JWS can carry _any_ type of payload, such as a JSON object, a byte stream, or an octet stream. In contrast, a JWT's payload _must_ always be a JSON object containing claims.
- **Detached Payload**: JWS supports a "detached" form where the payload is sent separately from the `Header..Signature` structure. This is particularly useful for signing very large files or data streams without having to include them directly in the token.

In summary, JWS is the standard for signing things, and a signed JWT is what you get when the "thing" you are signing is a specific JSON structure of claims. With this foundational knowledge, we can now look at how these powerful tokens are put to work in the most common real-world authentication and authorization systems.

## 5. How JWTs Are Used in the Wild

JWTs are a cornerstone of modern authentication and authorization protocols, particularly for securing APIs.

1. **Access Tokens in OAuth 2.0**: This is a very common use case. A client application (like a mobile app) authenticates with an authorization server and receives a JWT as an _access token_. The app then includes this JWT in its requests to a resource server (an API) to access protected data or perform actions on behalf of the user.
2. **ID Tokens in OpenID Connect (OIDC)**: OIDC is a thin identity layer built on top of the OAuth 2.0 authorization framework. It introduces a specific type of JWT called an **ID Token**. While an OAuth 2.0 access token's job is to grant _access_ to resources (authorization), an OIDC ID Token's job is to prove a user's _identity_ (authentication). It contains claims like the user's name and email, providing verifiable proof that the user has successfully logged in.

In a typical API call, the client sends the JWT in an HTTP header like this:

`Authorization: Bearer <token>`

Tools like API Gateways can be configured to automatically intercept and validate these incoming JWTs. The gateway checks the token's signature, expiration date, and other claims before forwarding the request to the backend service, which simplifies security and removes the validation burden from individual microservices.

## Conclusion: Key Takeaways for Beginners

As you continue your learning journey, keep these three essential concepts about JWTs in mind:

1. **Self-Contained & Verifiable**: JWTs bundle claims (information) and a signature into a single, compact token. The signature allows any party with the correct public key or secret to verify the token's integrity without contacting the issuer.
2. **Signed is Not Encrypted**: By default, a JWT's payload is only encoded, not encrypted, making it readable by anyone. Always use signing to ensure data integrity and encryption to ensure data confidentiality.
3. **Short Lifespans are Crucial**: Because JWTs are typically stateless and cannot be easily revoked, they must be given short expiration times. This is the most important security practice to limit the risk if a token is ever compromised.