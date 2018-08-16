# Stock workflow guides

These guides are meant to supplement the API documentation with detailed instructions for specific workflows.

## Authentication

For a complete description and business case for these types of integrations, refer to [Register your application](02-register-app.md).

- __[Enterprise service account](https://www.adobe.io/content/dam/udp/assets/StockAPI/Service-Account-API-workflow.pdf).__ This is for customers with Adobe Stock enterprise entitlements only who want to create an integration between their application and their Stock account, without requiring users to sign in.
- __[OAuth (auth code) integration](https://www.adobe.io/authentication/auth-methods.html#!adobeio/adobeio-documentation/master/auth/OAuth2.0Endpoints/web-oauth2.0-guide.md).__ This can be used for authenticating any type of user who has an Adobe Stock account so they can access protected resources such as the License API.
    + Note that if your application is being created for an Adobe Enterprise customer (including most Print on Demand customers), OAuth is _not_ the correct workflow for you. Instead, choose Service Account (above.) OAuth integrations always require an interactive user login--it cannot be fully automated.
- __[Affiliate workflow (search only)](https://www.adobe.io/content/dam/udp/assets/StockAPI/Affiliate-API-workflow.pdf).__ This guide covers how to integrate with the Search API and redirect users to the Adobe Stock website to complete the transaction.

