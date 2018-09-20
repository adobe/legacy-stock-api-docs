# Getting started with the Adobe Stock API

> _**Tl;dr version:** (1) register for an Adobe Stock API key at [console.adobe.io](https://console.adobe.io/), (2) use [curl](https://curl.haxx.se/) to make your first search query, (3) return here to learn more!_

```
curl https://stock.adobe.io/Rest/Media/1/Search/Files?locale=en_US%26search_parameters%5Bwords%5D=kittens 
  -H 'x-api-key:myAPIKey' 
  -H 'x-product:myTestApp1.0'
```

## Contents
<!-- MarkdownTOC -->

- [Overview](#overview)
- [Design phase](#design-phase)
- [Next steps](#next-steps)
- [More topics](#more-topics)

<!-- /MarkdownTOC -->

> Not a developer, but are interested in partnering? Please [contact us](mailto:Grp-AdobeStockPartnerships@adobe.com?subject=%5BAdobe%20I%2FO%5D%20Stock%20partnership%20inquiry).

The Adobe Stock API provides programmatic access to [Adobe Stock](https://stock.adobe.com/) content. You can integrate this API into your organization's applications and processes. You can use the API in scripts or programs to search for and retrieve Adobe Stock assets such as photos, videos, and vector files, and to license assets for your users.

You interact with the Adobe Stock API via HTTP requests, rather than through a user interface. You can create applications that perform these actions:



*   **Search for and retrieve Adobe Stock assets.** Your applications can search by keywords, models, contributor, series, and categories, and can also request assets that are similar to other assets or images. You can sort, filter, and page through any results from your searches. Search results contain URLs for thumbnails of the found assets, which you can use to display the thumbnails to your users. Results also can contain metadata about the assets, including information that your applications can use to retrieve the original asset.
*   **License assets that your search retrieves.** If you have an Authentication token, you can use the API to determine your user's purchasing status, obtain a license for the user for a specific asset, and download the original asset. 
*   **Get a license history of past purchases.** In addition to allowing users to license and download content, your application can retrieve the licensing history for your users or organization, allowing download of previously purchased assets.

 


<a name="overview"></a>
<a id="overview"></a>
## Overview

![API workflow](images/app-process-3-steps.png)

At its simplest, all Stock API integrations include these steps:



1.  Register your application on Adobe I/O.
2.  Determine the authentication method from your use case and build appropriate headers.
3.  Build and deploy your application using Stock APIs.


<a name="design-phase"></a>
<a id="design-phase"></a>
## Design phase

This step is implied by the list above and is outside the scope of this documentation, however everything you learn in the Getting Started section should give you sufficient information to design and architect your application.

As you read the documentation, keep some questions in mind. Some of these will be answered by your [use case](getting-started/02-register-app.md), but some may require a separate business discussion.

*   Is this an internal integration for members of my company, or for external users?
    *   If internal, does my company have Adobe Stock Enterprise entitlements, or just Team and Individual entitlements?
    *   If external, do I assume that users will have their own Adobe Stock accounts, or will my company use its own entitlements to purchase on their behalf?
*   Do I need to expose search options to the end user? 
    *   If yes, what kind of search experience do I want to provide: basic search on keywords only, or allow searching on similar images? The power and flexibility of similarity search is a key differentiator for Adobe Stock.
*   Will my application need to handle licensing of Stock assets, or will users be directed to the Adobe Stock website to purchase?
*   What technology/platform will I be using to create my application?
*   What size and how many image thumbnails do I want to display? 
*   Do I require localization?


<a name="next-steps"></a>
<a id="next-steps"></a>
## Next steps



1.  Learn how to [register](getting-started/02-register-app.md) your application on the Adobe I/O Console. You can add as many integrations as you need.
2.  Test how your application will [authenticate](getting-started/03-api-authentication.md) to the Adobe Stock API.
3.  Finally, start [building](getting-started/04-creating-apps.md) your app, test and deploy to your users!


<a name="more-topics"></a>
<a id="more-topics"></a>
## More topics

If you have already performed most of the tasks above or are in the middle of an implementation and need detailed help, check out these resources as well:



*   Review the detailed [workflow guides](getting-started/07-workflow-guides.md), which gives information on everything you need for a particular use case.
*   Get [sample code and download SDKs](getting-started/08-sample-code-sdks.md) from GitHub.
*   Read complete reference details for all API calls, call headers, and locales in the [Adobe Stock API Reference](#).

