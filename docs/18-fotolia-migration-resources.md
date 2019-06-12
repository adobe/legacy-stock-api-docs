# Fotolia API migration resources

This guide is intended for developers migrating from Fotolia API to the Adobe Stock API. 

<!-- MarkdownTOC -->

- [First steps](#first-steps)
- [Feature mapping](#feature-mapping)
    - [Endpoints](#endpoints)
    - [Search filters and parameters](#search-filters-and-parameters)
- [Deprecated API methods](#deprecated-api-methods)
- [Licensing and authentication](#licensing-and-authentication)
    - [Licensing changes](#licensing-changes)
    - [Authentication](#authentication)
- [Next steps](#next-steps)
    - [Get registered](#get-registered)
    - [Get a Stock account](#get-a-stock-account)
    - [Sign a contract](#sign-a-contract)
    - [Build an app](#build-an-app)

<!-- /MarkdownTOC -->


<a id="first-steps"></a>
## First steps
If you are new to using Adobe APIs, we recommend you begin by reading [Getting Started with the Adobe Stock API](https://www.adobe.io/apis/creativecloud/stock/docs.html), and browse the [Adobe Stock website](https://stock.adobe.com/) to become familar with navigation, search, and the new content types available.

Overall, the transition from the Fotolia API to the Adobe Stock API should not require much work, since most of the commands and parameters are the same, and both return standard JSON responses. One of the main advantages of switching to Adobe Stock is the robust platform offered by Adobe I/O, which hosts all of Adobe's APIs. Unlike Fotolia, Adobe Stock does not currently enforce API limits. For marketers, creative agencies and production houses, Stock also has a greater amount of quality content. In addition to all the photo, vector and video content on Fotolia, Adobe Stock has exclusive asset types such as 3D, Templates (including Motion Graphics Templates), Editorial assets, and a deep collection of Premium content by award-winning artists and agencies.  

The most significant difference between the APIs is the authentication method. Fotolia supported only basic authentication based on user name and password to receive a token, while Adobe requires a more secure method. Adobe offers two methods: one based on user login (OAuth), and a fully automated method for enterprise service accounts. See [Authentication](#authentication) for more details.

Because the licensing workflow is different, we recommend you also review [our guide on licensing](https://www.adobe.io/apis/creativecloud/stock/docs.html#!adobe/stock-api-docs/master/docs/getting-started/apps/06-licensing-assets.md). Furthermore, unlike Fotolia, when licensing a Standard image from Stock (non-Premium and non-Editorial), you will always receive a license for the _original size_. Therefore, there is no reason for "L", "XL" and "XXL" licenses. See [Licensing changes](#licensing-changes) for specific details on the differences.

<a id="feature-mapping"></a>
## Feature mapping

<a id="endpoints"></a>
### Endpoints
Here is a list of existing Fotolia API endpoints and their new Stock API equivalents. Please note that some replacement methods are still being developed (marked with "TBA", To Be Announced). Further, not all Fotolia methods are being carried over to Stock. See [Deprecated API methods](#deprecated-api-methods) for more information.

| Fotolia          | Adobe Stock          | Description                                    | Notes                                                                                                                                        | 
|------------------|----------------------|------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------| 
| __Search__           |                      |                                                |                                                                                                                                              | 
| [getSearchResults](https://us.fotolia.com/Services/API/Rest/Method/getSearchResults) | Search/Files         | Full search capabilities                       | See [Search filters and parameters](#search-filters-and-parameters) for details on search filters                                                                                                        | 
| [getCategories1](https://us.fotolia.com/Services/API/Rest/Method/getCategories1)<br>[getCategories2](https://us.fotolia.com/Services/API/Rest/Method/getCategories2) | Search/CategoryTree  | Return categories               | Categories are considered a legacy feature. While they are still available on Adobe Stock, they are less reliable than searching by keywords | 
| __Media__            |                      |                                                |                                                                                                                                              | 
| [getMediaData](https://us.fotolia.com/Services/API/Rest/Method/getMediaData)     | [Media/1/Files](api/19-bulk-metadata-files-reference.md)                  | Return all information about a media           | This API can be used to get metadata for files one at time, or in bulk | 
| [getBulkMediaData](https://us.fotolia.com/Services/API/Rest/Method/getBulkMediaData) | Media/1/Files](api/19-bulk-metadata-files-reference.md)                  | Return all information about a media           | Same as above | 
| [getMedia](https://us.fotolia.com/Services/API/Rest/Method/getMedia)         | Content/License      | Return download link (media purchase)          |                                                                                                                                              | 
| [getMediaComp](https://us.fotolia.com/Services/API/Rest/Method/getMediaComp)     | Search/Files         | Return url of the comp image                   |                                                                                                                                              | 
| __User__             |                      |                                                |                                                                                                                                              | 
| [loginUser](https://us.fotolia.com/Services/API/Rest/Method/loginUser)        | /ims/authorize (IMS) | Login a user (needed for authentification)       | See [Authentication](#authentication) | 
| [refreshToken](https://us.fotolia.com/Services/API/Rest/Method/refreshToken)     | /ims/token (IMS)     | Renew authentication token                     | See [Authentication](#authentication) | 
| [getUserData](https://us.fotolia.com/Services/API/Rest/Method/getUserData) | Member/Profile | Return information about the logged user and get available credits | To get license quota, use Member/Profile. Stock does not store information about the user, only about entitlements available | 


If you have a business requirement to access Stock Contributor APIs, please please [contact us](mailto:Grp-AdobeStockPartnerships@adobe.com?subject=%5BFotolia%5D%20Stock%20API%20question).

<a id="search-filters-and-parameters"></a>
### Search filters and parameters

Nearly all the Fotolia search parameters and filters are also available in Adobe Stock. Note that there are more filters available to Adobe Stock, especially for new content types (Templates, 3D, Premium, Editorial, etc.), as well as more controls for searching by similar images. Refer the [Stock Search API Reference](https://www.adobe.io/apis/creativecloud/stock/docs.html#!adobe/stock-api-docs/master/docs/api/11-search-reference.md) for full details.

| Fotolia                                               | Adobe Stock                             | Notes                                                                                                                                                       | 
|-------------------------------------------------------|-----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------| 
| search_parameters[language_id]                        | __locale__                                  | See [Locale codes reference](api/14-locale-codes.md).                                                                                                                                 | 
| search_parameters[words]                              | Same                                    | Keyword search                                                                                                                                              | 
| search_parameters[creator_id]                         | Same                                    | Search by creator                                                                                                                                           | 
| search_parameters[cat1_id]<br>search_parameters[cat2_id]                           | __search_parameters[category]__             | Search by  category                                                                                                                                         | 
| search_parameters[gallery_id]                         | Same                                    | Search by Fotolia gallery ID                                                                                                                                           | 
| search_parameters[model_id]                           | Same                                    | Search by same model                                                                                                                                        | 
| search_parameters[serie_id]                           | Same                                    | Search by same photographer collection                                                                                                                                       | 
| search_parameters[similar]                            | Same                                    | Search by similar media ID                                                                                                                           | 
| search_parameters[filters][content_type:photo]        | Same                                    | Search for photos                                                                                                                                           | 
| search_parameters[filters][content_type:illustration] | Same                                    | Search for illustrations                                                                                                                                    | 
| search_parameters[filters][content_type:vector]       | Same                                    | Search for vectors                                                                                                                                          | 
| search_parameters[filters][content_type:video]        | Same                                    | Search for videos                                                                                                                                           | 
| search_parameters[filters][offensive:2]               | Same                                    | Explicit/nudity/violence included                                                                                                                           | 
| search_parameters[filters][isolated:on]               | Same                                    | Isolated contents (on white background)                                                                                                                     | 
| search_parameters[filters][panoramic:on]              | Same                                    | Panoramic images                                                                                                                                            | 
| search_parameters[filters][license_L:on] <br>search_parameters[filters][license_XL:on] <br>search_parameters[filters][license_XXL:on] <br>search_parameters[filters][license_XXL>25MP:on]      | search_parameters[filters][area_m_pixels]<br>search_parameters[filters][image_width]<br>search_parameters[filters][image_height]<br>~~search_parameters[filters][area_pixels]~~ | Filter by size in megapixels (width * height). In Stock, standard images are always licensed at full resolution. Note that `[area_pixels]` has been deprecated and the other filters should be used instead | 
| search_parameters[filters][orientation]               | Same                                    | Search assets of the specified orientation                                                                                                                                       | 
| search_parameters[filters][video_duration]            | Same                                    | Filter by videos whose length is no longer than the specified duration in seconds                                                                                                                                              | 
| search_parameters[filters][colors]                    | Same                                    | comma separated listed of hexadecimal colors (without any # prefix)                                                                                         | 
| search_parameters[filters][has_releases]              | Same                                    | Filters if the asset has model or property releases                                                                                                         | 
| search_parameters[order]                              | Same                                    | Sort order when returning assets                                                                                                                                                   | 
| search_parameters[limit]                              | Same                                    | Maximum number of assets returned per request                                                                                                                            | 
| search_parameters[offset]                             | Same                                    | Start position in query                                                                                                                                     | 
| search_parameters[thumbnail_size]                     | Same                                    | Sets the desired thumbnail size in pixels                                                                                                                                              | 
| result_columns                                        | result_columns[]                        | Refer to the [Stock Search API Reference](https://www.adobe.io/apis/creativecloud/stock/docs.html#!adobe/stock-api-docs/master/docs/api/11-search-reference.md) for a full list of fields that can be returned. Default fields returned are mostly the same, but have slight differences.                     | 


<a id="deprecated-api-methods"></a>
## Deprecated API methods
The following API endpoints and methods will be deprecated in Adobe Stock API as they are either not currently being used, no longer supported, or no longer necessary. These APIs either receive no traffic, or have such low usage that they are candidates for removal.

If you have a business requirement for any of the APIs below, please [contact us](mailto:Grp-AdobeStockPartnerships@adobe.com?subject=%5BFotolia%5D%20Stock%20API%20question).

| Fotolia                              | Adobe Stock | Description                                                               | 
|--------------------------------------|-------------|---------------------------------------------------------------------------| 
| getTags                              | N/A         | Return tag cloud                                                          | 
| getGalleries                         | N/A         | Return public galleries                                                   | 
| getSeasonalGalleries                 | N/A         | Return seasonal galleries                                                 | 
| getCountries                         | N/A         | Return countries list                                                     | 
| getMediaGalleries                    | N/A         | Return galleries attached to a media                                      | 
| getHomePageImages                    | N/A         | Return images features on the homepage                                    | 
| shoppingcart.getList                 | N/A         | Return shopping cart content                                              | 
| shoppingcart.add                     | N/A         | Add a media to the user's shopping cart                                   | 
| shoppingcart.update                  | N/A         | Update a media into the user's shopping cart                              | 
| shoppingcart.remove                  | N/A         | Remove a media from the user's shopping cart                              | 
| shoppingcart.transferToLightbox      | N/A         | Remove a media from the user's shopping cart and add to to his lightbox   | 
| shoppingcart.clear                   | N/A         | Clear the user's shopping cart                                            | 
| getData                              | N/A         | Retun general information about Fotolia                                   | 
| test                                 | N/A         | Return "success"                                                          | 
| user.subaccount.getIds               | N/A         | Return an array of all available subaccount IDs                           | 
| user.subaccount.create               | N/A         | Create a reseller subaccount                                              | 
| user.subaccount.delete               | N/A         | Delete a reseller subaccount                                              | 
| user.subaccount.edit                 | N/A         | Update data of a reseller subaccount                                      | 
| user.subaccount.get                  | N/A         | Get reseller subaccount details                                           | 
| user.subaccount.getPurchasedContents | N/A         | Get the list of purchases made by a subaccount                            | 
| media.getLicense                     | N/A         | Get the licence between the reseller and his customer for a specific sale | 

<a id="licensing-and-authentication"></a>
## Licensing and authentication

<a id="licensing-changes"></a>
### Licensing changes

Adobe Stock has a flexible licensing system that allow individual and team customers to purchase image licensing subscriptions as well as "universal" credit packs which can be used to license any asset. Enterprise customers typically have a quota of licenses that is not fixed; they can exceed their quota and simply pay the same negotiated rate for additional credits.

For more information on consumer (non-Enterprise) plans, see [Adobe Stock pricing and membership plans](https://stock.adobe.com/plans). For information on Stock for Enterprise, see [this page](https://landing.adobe.com/en/na/products/creative-cloud/ctir-4625-stock-for-enterprise/index.html) for details.

Unlike Fotolia, all Standard assets require one license or credit to purchase, regardless of size. "Standard" assets include non-Premium photos, illustrations and vectors. Each Standard license gives access to the full resolution of the original asset. Other asset types may require multiple credits to purchase, and may come in different sizes. Extended licenses are also available for assets; see [License information and Terms of Use](https://stock.adobe.com/license-terms).

> __Note for Print on Demand (POD) customers:__ If you license assets for use with printed goods that you sell, there are special rules you must follow. For example, you must use universal credits instead of subscription plans when licensing. Also, you will need to license the asset again in most cases, especially if used for different customer orders or for multiple products. And lastly, you will need to accept special terms and conditions before you can begin selling Stock images on your printed goods.
>  
>  Note that if you purchase Extended licenses, these precautions are not necessary, but the cost per asset is much higher. To discuss your use case and expected volume, please [contact us](mailto:Grp-AdobeStockPartnerships@adobe.com?subject=%5BFotolia%5D%20Stock%20API%20question) for additional terms and instructions.

In terms of technical workflow, continue reading for an overview. Please refer to [Licensing assets and stuff](https://www.adobe.io/apis/creativecloud/stock/docs.html#!adobe/stock-api-docs/master/docs/getting-started/apps/06-licensing-assets.md) for full details.

To license an asset, your application will want to follow these steps:

1. Get a token: See [Authentication](#authentication), below.
2. Get a Stock asset ID by using the Search API, or by entering it from data.
3. Check if the asset is already licensed. If you are a Print on Demand retailer (see above), continue the process. But if you are using the image for internal use such as on a marketing campaign or website, go to step #6.
4. Check if licensing is possible. Because there are multiple types of assets and credits, it is possible that your plan does not include the asset you are trying to license. This step will let you know and give you the available remaining credits.
5. License the asset. This uses credits from the account and results in a download link, but does not start the download. As discussed above, if you are a POD retailer, the API provides a command to license an asset again. See the [FAQ](https://www.adobe.io/apis/creativecloud/stock/docs.html#!adobe/stock-api-docs/master/docs/15-faq.md).
6. Download the file from the link. This link is also available from a standard search. Note that unlike Fotolia, downloading the asset does not trigger a license action. Users can download the asset as often as needed with a valid token.

Another use of the License API is to retrieve a history of licensed assets. For more details, see the [License History reference](https://www.adobe.io/apis/creativecloud/stock/docs.html#!adobe/stock-api-docs/master/docs/api/13-license-history.md).

<a id="authentication"></a>
### Authentication

The Fotolia API supported basic authentication by posting a user name and plain-text password, and receiving back a session token. This has been replaced at Adobe with a much more secure authentication workflow which is common to all Adobe cloud-based services.

Here is an overview of authentication types supported by Adobe Stock, but for full details, see the [Adobe I/O Authentication guide](https://www.adobe.io/authentication.html).

* JWT service account: Allows for fully automated scripting without requiring a user login. In this scheme, a developer shares a public key certificate with Adobe, and uses scope and credentials to create a signed JWT. The JWT is exchanged with Adobe to receive an access token.
    - This authentication type is available to _Adobe Enterprise customers only_.
* OAuth: Allows your application to sign in a user to access their Adobe services, including Stock. This is based on the OAuth 2.0 model, which is used by other services such as Facebook, Google, Dropbox, etc. Once the user signs in, your application will receive an access token and a refresh token. The refresh token is used to renew the login for a set amount of time.

For technical details on either workflow, see the [Stock workflow guides](https://www.adobe.io/apis/creativecloud/stock/docs.html#!adobe/stock-api-docs/master/docs/getting-started/07-workflow-guides.md).

Both workflows result in creating an access token, which is used to identify that user (or Enterprise organization), and can be used to license assets, get license history, and authorize downloads. The token must be treated like a secret, and not be exposed to the public.

<a id="next-steps"></a>
## Next steps

Now that you have an overview, check out the links below.

<a id="get-registered"></a>
### Get registered
You may start testing the Search APIs at any time; all that is required is a free Adobe ID account to receive an API key. Look at [Register your application](https://www.adobe.io/apis/creativecloud/stock/docs.html#!adobe/stock-api-docs/master/docs/getting-started/02-register-app.md) for details.

<a id="get-a-stock-account"></a>
### Get a Stock account
If you are an individual customer, go to the [Plans and Pricing](https://stock.adobe.com/plans) page to sign up for an account. Note that if you plan to sell images for print use (e.g., you have a Print on Demand business), you will need to [contact us](mailto:Grp-AdobeStockPartnerships@adobe.com?subject=%5BFotolia%5D%20Stock%20API%20question) for additional terms and instructions, and to discuss your volume and use case. If you plan to use assets only for internal use (such as for general marketing and design), then you can choose your plan accordingly.

If you do not need your own account but need to sign in others using the OAuth model, refer to the [Authorization code workflow](https://github.com/adobe/stock-api-docs/raw/master/supplemental/Stock-Authorization-Code-Workflow.pdf) guide. If you have a business need, you may [contact us](mailto:Grp-AdobeStockPartnerships@adobe.com?subject=%5BFotolia%5D%20Stock%20API%20question) if you need access to a demo account with dummy credits to test licensing.

<a id="sign-a-contract"></a>
### Sign a contract
As discussed earlier, if you plan to use Adobe Stock for commercial purposes--especially for selling printed goods--you will need to [contact us](mailto:Grp-AdobeStockPartnerships@adobe.com?subject=%5BFotolia%5D%20Stock%20API%20question). In many cases, you may need only accept our terms and conditions, and will be ready to begin.

<a id="build-an-app"></a>
### Build an app
You should begin with the [Getting started guide](https://www.adobe.io/apis/creativecloud/stock/docs.html), and then look at [Creating Adobe Stock applications](https://www.adobe.io/apis/creativecloud/stock/docs.html#!adobe/stock-api-docs/master/docs/getting-started/04-creating-apps.md). If you have integration questions, send a message to stockapis@adobe.com. 



