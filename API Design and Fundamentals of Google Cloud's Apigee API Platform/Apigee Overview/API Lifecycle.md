## digital value chain
customer, app, developer, API, API team, backend


# 1. Design

![[Screenshot 2025-10-25 003413.png]]
OpenAPI specifications are used to design REST APIs. OpenAPI specs may be used to define the interface and capabilities of the APIs without focusing on the implementation 

OpenAPI spec may be used to generate API proxy stub, which provides a template for building APIs to the defined specification
# 2. Develop 

![[Screenshot 2025-10-25 003833.png]]
API proxies may be built using Apigee policies (no-code pre-built functions)
# 3. Secure
> Multi-layer security for APIs

![[Screenshot 2025-10-25 004132.png]]
> Threat protection against content-based attacks that detects malicious request payloads and rejecting the requests before they reach the backend

Apigee allows masked sensitive data so operations teams are unable to see user information when tracing live API traffic

Cloud Armor WAF is a webapp firewall that protects against DDOS attacks and allows IP address blocking, as well as other firewall rules to protect against other common attacks

Identity Platform is a customer IAM platform that assists organisations add IAM functionality to APIs and applications. It supports multi-factor authentication SAML, OpenID Connect, email & password and even custom implementations

Security policies include: OAuth SAML, JSON webtokens, HMAC authentication, and other API authorizations 
# 4. Deploy
> When API has been built, API proxies are deployed to production.

Deployment process may be built into a deployment pipeline, where changes to proxies are automatically tested before being deployed
Apigee provides management API that creates deployment pipelines
# 5. Publish
Apigee developer portal helps app developers discover APIs and register apps to use them

OpenAPI specifications may be used to create live documentations

Apigee has 2 developer portals 
Drupal-based portal offering a full-featured customizable content management system
Hosted integrated portal, requiring much less effort with less customization and features

# 6. Monitor
> APIs require to be monitored to ensure performance and availability

Alerts can be used to inform of spikes and latencies, which may be analyzed in the console

Diagnose issues before app developers and users notice them

# 7. Analyze
> Captures business metrics by collecting data within API proxies

Reports may be generated for insights

Apigee analytics may be integrated into own systems using metrics API/by extracting data into Google Cloud Storage or BigQuery

# 8. Monetize
