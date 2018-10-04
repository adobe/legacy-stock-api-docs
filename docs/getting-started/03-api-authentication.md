# API authentication headers

> _**Tl;dr version:** All Stock API requests require two basic headers, and authenticated requests require you to add an access token. Here you can learn the format for these headers and test them._

#### Contents
<!-- MarkdownTOC -->

- [Overview](#overview)
- [Store your credentials](#store-your-credentials)
- [Create your basic header](#create-your-basic-header)
    - [Test your API key header](#test-your-api-key-header)
- [Generate an access token](#generate-an-access-token)
    - [Important: Use the correct scope in your JWT](#important-use-the-correct-scope-in-your-jwt)
- [Add token to header](#add-token-to-header)
    - [Test your access token](#test-your-access-token)

<!-- /MarkdownTOC -->

## Overview

In the previous step, you registered your application on the [Adobe I/O Console](https://console.adobe.io). Now you will take the credentials created for you, and use them to build proper headers.

Note that these topics are explored in detail in each of these workflow guides, so only the summary of the process will be discussed here:



*   [Service account workflow](07-workflow-guides.md)
*   [OAuth workflow](07-workflow-guides.md)
*   [Affiliate (API key) workflow](07-workflow-guides.md)

## Store your credentials

The first thing you will need is the information given to you when you registered your application. Depending on which integration method you chose, you will only need particular fields (see a [screenshot](../images/io_all-integration-details.png) of each integration type).

Other than the API key, the information here--especially the client secret--should be safeguarded like any sensitive credentials, similar to your application's private key. At a minimum, this data should be stored on your server in an file inaccessible to the public. At no time should it be exposed in front-end JavaScript, for example.


| **Integration type** | **Fields required** |
|----|----|
| API Key | API key (aka, Client ID) |
| OAuth | API key, client secret, redirect URI |
| Service Account | API key, technical account ID, organization ID, client secret |

## Create your basic header

The Adobe Stock APIs is a REST-based service. Subsequently, all requests to the Stock APIs require these HTTP headers:



*   **x-api-key**: Your API key
*   **X-Product**: Your application's name

Example:


```http
    X-Product: MySampleApp/1.0
    X-API-Key: abc123c9f1194eac8a63cc25c1b9b9df
```


The format for X-Product can be any string, however a common convention is to include the version number separated by a slash.

### Test your API key header

For unauthenticated requests such as normal search queries, this is all you need, and you can start building your application. But before you do, run a quick test to make sure it is working. Simply replace "YourApiKeyHere" with the key you generated, and when you run this command, you should get back JSON results of cats wearing costumes, proving that it works.

Example search query using [curl](https://curl.haxx.se/). Note that line breaks have been added for clarity.


```bash
curl "https://stock.adobe.io/Rest/Media/1/Search/Files?locale=en_US
&search_parameters%5Bwords%5D=cats%20in%20costume" 
-H "x-api-key: YourApiKeyHere" -H "x-product: MySampleApp/1.0"
```

## Generate an access token

All License API requests to Adobe Stock must be authenticated and authorized using an access token string which follows the JSON Web Token (JWT) standard. The token will contain claims authorizing the bearer of the token access to protected resources (such as licensed images), and is digitally signed by the issuer. All access tokens you will use in your workflow must be issued by the Adobe Identity Management Services (IMS). In the Service Account workflow, this token will be generated as a result of exchanging tokens between your application and Adobe IMS, and for the OAuth workflow, Adobe IMS will issue the token as the result of a successful login.

The creation of tokens is beyond the scope of this article, however it is covered in depth in the Service Account workflow and OAuth workflow guides.

For complete information about creating access tokens, see the Adobe I/O[ API Authentication Guide](https://www.adobe.io/content/udp/en/apis/cloudplatform/console/authentication). Here you will also find examples of creating JWTs using Java and Node.js.

<a id="important-use-the-correct-scope-in-your-jwt"></a>
### Important: Use the correct scope in your JWT
Please note that if you are following the Service Account workflow, the Adobe I/O API Authentication Guide (above) uses a different scope in its sample JWT payload than the one required by the Stock API. The correct scope for your application is listed in the __JWT__ tab of the Adobe I/O Console, and it will include your proper payload.

![JWT tab of Service Account integration](../images/io_jwt-tab.png)

## Add token to header

Once your token is generated, add it to your authenticated request using this syntax:


```
    Authorization:Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```


The sample token above comes from [jwt.io](https://jwt.io/), an excellent resource for learning about and troubleshooting tokens.


### Test your access token

Like we tested our API key header, now we can test our access token by combining it with the earlier headers (which are still required), and calling an API that requires authentication. The Member/Profile request is perfect for this test, as it will tell you what Adobe Stock entitlements you have (if any).

Replace the text "PutYourAccessToken" with the token you generated, and replace "YourApiKeyHere" with your key, and run from a terminal prompt.


```bash
    curl "https://stock.adobe.io/Rest/Libraries/1/Member/Profile?locale=en_US" -H "authorization: Bearer PutYourAccessToken" -H "x-api-key: YourApiKeyHere" -H "x-product: MySampleApp/1.0"
```


If you correctly generated an access token, you should see something like this (JSON has been formatted for display). 


```json
    {
        "available_entitlement": {
            "quota": 49,
            "license_type_id": 1,
            "has_credit_model": false,
            "has_agency_model": false,
            "is_cce": false,
            "full_entitlement_quota": {
                "image_quota": 49
            }
        },
        "member": {
            "stock_id": 1272100
        },
        "purchase_options": {
            "state": "possible",
            "requires_checkout": false,
            "message": "This will use 1 of your 49 licenses."
        }
    }
```


Even if your image quota is 0, congratulations for getting this far! Generating the token is the most difficult part of the Stock API process.

__>>> NEXT:__ Learn the Stock API command structure and  [start building your app](./04-creating-apps.md)!
