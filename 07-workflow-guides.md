# Stock workflow guides

These guides are meant to supplement the API documentation with detailed instructions for specific workflows.

## Authentication

For a complete description and business case for these types of integrations, refer to [Register your application](02-register-app.md).

- __[Enterprise service account](Service-Account-API-workflow.pdf).__ This is for customers with Adobe Stock enterprise entitlements only who want to create an integration between their application and their Stock account, without requiring users to sign in.
- __[OAuth (auth code) integration](Stock-Authorization-Code-Workflow.pdf).__ This can be used for authenticating any type of user who has an Adobe Stock account so they can access protected resources such as the License API. This method can be used potentially for accessing _any_ type of Adobe service, not only Stock.
- __[Affiliate workflow (search only)](Affiliate-API-workflow.pdf).__ This guide covers how to integrate with the Search API and redirect users to the Adobe Stock website to complete the transaction.

