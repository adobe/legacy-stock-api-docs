<!-- omit in toc -->
# Licensing reference

<!-- MarkdownTOC-->

- [Content and Member requests](#content-and-member-requests)
  - [Authentication](#authentication)
  - [Request headers](#request-headers)
  - [URL parameters](#url-parameters)
- [Responses](#responses)
  - [Response JSON summaries](#response-json-summaries)
  - [Response attributes](#response-attributes)
- [Examples](#examples)
  - [Content/Info](#contentinfo)
  - [Content/License](#contentlicense)
  - [Member/Profile](#memberprofile)
- [Error codes](#error-codes)
- [Downloading licensed files](#downloading-licensed-files)
  - [Authentication](#authentication-1)
  - [Request headers](#request-headers-1)
  - [URL parameters](#url-parameters-1)
  - [Response](#response)
  - [Examples](#examples-1)
  - [Download errors](#download-errors)
- [More information](#more-information)

<!-- /MarkdownTOC -->

## Content and Member requests

The Stock API provides the following methods related to licensing and getting more info about licensed assets.  For a guide to usage and additional examples, see [Licensing Stock assets](../getting-started/apps/06-licensing-assets.md).


| Endpoints                                                     | Method                                                 |
| ------------------------------------------------------------- | ------------------------------------------------------ |
| https://stock.adobe.io/Rest/Libraries/1/Content/Info          | GET                                                    |
| https://stock.adobe.io/Rest/Libraries/1/Content/License       | GET, POST (for adding [license references](#examples)) |
| https://stock.adobe.io/Rest/Libraries/1/Member/Profile        | GET                                                    |
| https://stock.adobe.io/Rest/Libraries/1/Member/LicenseHistory | GET                                                    |



*   **Content/Info.** Requests licensing information about a specific asset for a specific user or enterprise organization.
*   **Content/License.** Requests a license for an asset.
*   **Member/Profile.** Asks for the licensing capabilities for a specific user or organization.  Call this before requesting an asset license so that you can display the user's existing quota or direct the user to the Adobe Stock site to purchase a plan or asset.

    This API returns the user's available purchase quota and information that you can use to present licensing options to the user when the user next requests an asset purchase. Depending on the user's quota and plan the following cases can occur:

    *   _User has enough quota to license the next asset._ Display the returned message, which shows the user's current quota before purchasing.
    *   _User doesn't have enough quota but can handle overage_ (they have a saved purchase method on file). Display the returned message, which includes the price and asks the user to license the asset with overage.
    *   _User doesn't have quota and there is no overage plan._ Display the returned message, which indicates that the user will be redirect to the [Adobe Stock site](https://stock.adobe.com/) to review plans.

*   **Member/LicenseHistory.** Requests a list of licenses and download URLs for every previously licensed asset. This is covered in the next section, [License history reference](13-license-history.md).


### Authentication

The Authorization header is required for any of the licensing calls. The API uses the header to determine the user's member number, licensing status, and default locale. See [API authentication](../getting-started/03-api-authentication.md) for more information.


### Request headers

See [Headers for Stock API calls](10-headers-for-api-calls.md) for details about header content. 


*   Required headers: `x-Product`, `x-api-key`, `Authorization`
*   Optional headers: `X-Request-Id`


<a name="url-parameters"></a>
### URL parameters

Pass URL-encoded parameters with the GET request.


<table>
  <tr>
   <td><strong>Parameter</strong>
   </td>
   <td><strong>Description</strong>
   </td>
  </tr>
  <tr>
   <td>content_id
   </td>
   <td>Asset's unique identifier. You can get this from a search response's <code>id </code>attribute. Integer.
   </td>
  </tr>
  <tr>
   <td>license
   </td>
   <td>Use only with Content/Info, Content/License, and Member/Profile. 
The Adobe Stock licensing state for the asset. String. 
<p>Valid values and meaning:
<ul>
<li><code>""</code> <em>(empty string)</em>: No license applies</li>
<li>For standard/core photos, illustrations, and vectors (non-Premium):
    <ul>
    <li><code>Standard</code>: License for the full-resolution image
    <li><code>Extended</code>: Extended license for the full-resolution image</li>
    </ul>
<li>For video:
    <ul>
    <li><code>Video_HD</code>: License for the HD-resolution video
    <li><code>Video_4K</code>: License for the 4K-resolution video. <strong>Tip:</strong> Not all videos are available in 4K.</li>
    </ul>

<li>For vector assets: <code>Standard</code> or <code>Extended</code>
<li>For 3D assets:  <code>Standard</code>  
<li>For templates: <code>Standard</code></li>
<li>For Premium:
    <ul>
    <li><code>Standard</code>: License for the full-resolution image
    <li><code>Standard_M</code>: License for a medium-size asset approximately 1600x1200 pixels. <strong>Tip:</strong> Not all Premium assets offer this license.
    <li><code>Extended</code>: Extended license for the full-resolution image</li>
    </ul>
</li>
  <li>Additional license values if Alternative Licenses are available:
    <ul>
      <li><code>Internal_Use</code></li>
      <li><code>Video_HD_Internal_Use</code></li>
      <li><code>Digital_Use</code></li>
      <li><code>Video_HD_Digital_Use</code></li>
      <li><code>Social_Media</code></li>
      <li><code>Video_HD_Social_Media</code></li>
      <li><code>Video_HD_Standard</code></li>
    </ul>
  </li>
</ul>

   </td>
  </tr>
  <tr>
   <td>locale
   </td>
   <td>Use with Member/Profile and Member/LicenseHistory.
<p>
Optional. Location language code for the API to use when returning localized messages. The API can usually get the user's default locale through the Authorization header. This value overrides that or provides a locale if not available through Authorization. String.
<p>
Default is <code>en_US</code>. See the full list of <a href="14-locale-codes.md">Locales</a>.
   </td>
  </tr>
  <tr>
   <td>license_again
   </td>
   <td>Use with Content/License.
<p>
Optional. Used to re-license an asset, deducting licenses/credits as if it were a new transaction. Boolean.
<p>
Default is <code>false</code>, meaning that if Content/License is used on an asset that is already licensed, it will not trigger a new license. Using <code>true</code> overrides that behavior, and forces a new license event.
<pre><code>  curl "https://stock.adobe.io/Rest/Libraries/1/Content/License?content_id=112670342&license=Standard&license_again=true" \
    -H "x-api-key: YourApiKeyHere" \
    -H "x-product: MySampleApp/1.0" \
    -H "authorization: Bearer AccessTokenHere"</code></pre>
   </td>
  </tr>
</table>


## Responses

Calls return information in JSON structures.

### Response JSON summaries


*   **Content/Info** returns this structure. Some attributes appear only in certain situations.

```javascript
{"contents": {
    "0000": {
      "content_id": 0000,
      "purchase_details": {
        "stock_id": 12345,  /*only if user has not purchased*/
        "date": "yyyy-mm-dd hh:mm:mm", /*only if purchased*/
        "license": "...", /*only if purchased*/
        "state": "..."
      },
      "size": "..."
    }
  },
  "member": { "stock_id": 12345 }
}
```


*   **Content/License** returns this structure. Some attributes appear only in certain situations.

```javascript
{ "available_entitlement": { 
    "quota": n, 
    "license_type_id": x,
    "has_credit_model": true||false,
    "has_agency_model": true||false,
    "is_cce": true||false,    
    "full_entitlement_quota": [] 
  },
  "contents": {
    "0000": {
      "content_id": 0000,
      "purchase_details": {
        "state": "...",
        "license": "...",
        "date": "yyyy-mm-dd hh:mm:mm", /*only if licensed*/
        "url": "...",     /*only if download needed*/
        "content_type": "...",
        "width": 000,
        "height": 000,
        /*additional props depending on asset type...*/
      },
      "size": "..."
    }
  },
  "member": { "member_id": 12345 }
 }
```

*   **Member/Profile** returns this structure. Some attributes appear only in certain situations.

```javascript
{ "available_entitlement": { 
    "quota": n, 
    "license_type_id": x,
    "has_credit_model": true||false,
    "has_agency_model": true||false,
    "is_cce": true||false,    
    "full_entitlement_quota": [] 
  },
  "purchase_options": {
    "state": "...",
    "requires_checkout": ....,
    "message": "...",
    "url": "..."   /*only if user has no quota or overage*/
  },
  "member": { "member_id": 12345 } /*only if user has quota or overage*/
}
```

* **Member/Profile** can also return the attribute `possible_licenses` if Alternative Licenses are enabled on the account.

```javascript
{
    "available_entitlement": {
        "quota": 9733,
        "license_type_id": 43,
        "has_credit_model": false,
        "has_agency_model": true,
        "is_cce": true,
        "full_entitlement_quota": {
            "universal_credits_quota": 9733
        },
        "quota_ids": {
            "universal_credits_quota": "credits"
        },
        "user_has_ccpro": false,
        "organization_has_ccpro": false
    },
    "member": {
        "stock_id": 14357036
    },
    "purchase_options": {
        "state": "possible",
        "requires_checkout": false,
        "license": "Extended",
        "message": "This will use 2 of your 9,733 credits."
    },
    "possible_licenses": [
        {
            "license": "Social_Media",
            "license_label": "Social Media",
            "price_with_unit": "5 Credits"
        },
        {
            "license": "Extended",
            "license_label": "Extended",
            "price_with_unit": "2 Credits"
        }
    ]
}
```

### Response attributes

<table>
  <tr>
   <td><strong>Name</strong>
   </td>
   <td><strong>Description</strong>
   </td>
  </tr>
  <tr>
   <td>available_entitlement{...}
   </td>
   <td>Information about licenses available for this user. Structure.
   </td>
  </tr>
  <tr>
   <td><em>available_entitlement:</em><br>
   &nbsp;&nbsp;&nbsp;&nbsp;quota
   </td>
   <td>Quantity of licenses/credits remaining available for this user for this asset type. Integer.
   </td>
  </tr>
  <tr>
  <tr>
   <td><em>available_entitlement:</em><br>
   &nbsp;&nbsp;&nbsp;&nbsp;license_type_id
   </td>
   <td>License type of combined asset and license. Integer.
   </td>
  </tr>
  <tr>
  <tr>
   <td><em>available_entitlement:</em><br>
   &nbsp;&nbsp;&nbsp;&nbsp;has_credit_model
   </td>
   <td>(Enterprise only) Type of enterprise contract model. Boolean.
   </td>
  </tr>
  <tr>
  <tr>
   <td><em>available_entitlement:</em><br>
   &nbsp;&nbsp;&nbsp;&nbsp;has_agency_model
   </td>
   <td>(Enterprise only) Type of enterprise contract model. Boolean.
   </td>
  </tr>
  <tr>
  <tr>
   <td><em>available_entitlement:</em><br>
   &nbsp;&nbsp;&nbsp;&nbsp;is_cce
   </td>
   <td>Whether this is an enterprise entitlement or not. Boolean.
   </td>
  </tr>
  <tr>
   <td><em>available_entitlement:</em><br>
   &nbsp;&nbsp;&nbsp;&nbsp;full_entitlement_quota
   </td>
   <td>Licensing quota types available. This information is subject to change as new license types are added and subtracted; best practice is to use the main <code>available_entitlement.quota</code> value instead. Array.
   </td>
  </tr>
  <tr>
   <td>contents{...}
   </td>
   <td>Licensing information for this asset for this user. Structure.
<p>
The number directly inside contents is the same asset ID number that is returned in content_id:

<pre><code>"contents": {
    "<em>nnnnn</em>": {
        "content_id": <em>nnnnn</em>, 
        ...
    }, ...
}
</code></pre> 
   </td>
  </tr>
  <tr>
   <td><em>contents:</em><br>
   &nbsp;&nbsp;&nbsp;&nbsp;content_id
   </td>
   <td>Asset's unique identifier. String.
   </td>
  </tr>
  <tr>
   <td><em>contents:</em><br>
   &nbsp;&nbsp;&nbsp;&nbsp;purchase_details{...}
   </td>
   <td>Information about the user's purchase/license of this asset. Structure.
   </td>
  </tr>
  <tr>
   <td><em>contents:</em><br>
   &nbsp;&nbsp;<em>purchase_details:</em><br>
   &nbsp;&nbsp;&nbsp;&nbsp;state
   </td>
   <td>User's purchase relationship to an asset.
<p>
Possible values and meanings:<ul>

<li><code>not_purchased</code>: User has not at any time in the past purchased the asset.
<li><code>purchased</code> : User has at some time in the past purchased the asset.
<li><code>cancelled</code>: User attempted to buy the asset and for some reason the order did not go through.
<li><code>not_possible</code>: User must go to the Adobe Stock site to buy plan or asset.
<li><code>just_purchased</code>: User bought asset within the current session.
<li><code>overage</code>: Adobe Stock has a payment instrument on file for the user and can bill the user for additional purchases. Does not apply to enterprise as it is always possible to add additional licenses.</li></ul>

   </td>
  </tr>
  <tr>
   <td><em>contents:</em><br>
   &nbsp;&nbsp;<em>purchase_details:</em><br>
   &nbsp;&nbsp;&nbsp;&nbsp;license
   </td>
   <td>Type of license that the user has for this asset. String.
<p>
For possible values, see <a href="#url-parameters">URL parameters</a>, above.
   </td>
  </tr>
  <tr>
   <td><em>contents:</em><br>
   &nbsp;&nbsp;<em>purchase_details:</em><br>
   &nbsp;&nbsp;&nbsp;&nbsp;date
   </td>
   <td>Date when the asset was purchased or licensed. Date format.
   </td>
  </tr>
  <tr>
   <td><em>contents:</em><br>
   &nbsp;&nbsp;<em>purchase_details:</em><br>
   &nbsp;&nbsp;&nbsp;&nbsp;url
   </td>
   <td>The URL from which the licensed asset can be downloaded. String.
   </td>
  </tr>
  <tr>
   <td><em>contents:</em><br>
   &nbsp;&nbsp;<em>purchase_details:</em><br>
   &nbsp;&nbsp;&nbsp;&nbsp;content_type
   </td>
   <td>Mime type of asset. String.
   </td>
  </tr>
  <tr>
   <td><em>contents:</em><br>
   &nbsp;&nbsp;<em>purchase_details:</em><br>
   &nbsp;&nbsp;&nbsp;&nbsp;width/height/...
   </td>
   <td>Dimensions and other physical properties of asset. Mixed types.
   </td>
  </tr>
  <tr>
   <td><em>contents:</em><br>
   &nbsp;&nbsp;&nbsp;&nbsp;size
   </td>
   <td>The size of the asset, indicating whether it is the free complementary size or the original full-sized asset.  String.
<p>
Possible values:<ul>

<li><code>Comp</code>
<li><code>Original</code></li></ul>

   </td>
  </tr>
  <tr>
   <td>purchase_options {...}
   </td>
   <td>Information about the user's purchasing options for this asset. Structure.
   </td>
  </tr>
  <tr>
   <td>member {...}
   </td>
   <td>Information about the user; for Adobe Stock internal use.
   </td>
  </tr>


  <tr>
   <td><em>purchase_options:</em><br>
   &nbsp;&nbsp;&nbsp;&nbsp;requires_checkout
   </td>
   <td>Whether a purchase in process requires going to the Adobe Stock site for completion. Returns true or false. Boolean. 
   </td>
  </tr>
  <tr>
   <td><em>purchase_options:</em><br>
   &nbsp;&nbsp;&nbsp;&nbsp;message
   </td>
   <td><p>Message to display to your user in response to a Licensing API query. String.
<p>
All returned messages are localized and generated for you by the Stock API. This provides uniform messaging for all clients of Stock API.
   </td>
  </tr>
  <tr>
   <td><em>purchase_options:</em><br>
   &nbsp;&nbsp;&nbsp;&nbsp;state
   </td>
   <td>User's purchase relationship to an asset. See <code>contents.purchase_details.state</code> above. String.</td>
  </tr>
  <tr>
   <td><em>purchase_options:</em><br>
   &nbsp;&nbsp;&nbsp;&nbsp;url
   </td>
   <td>Only displayed if user is not able to license asset. URL directs user to Adobe Stock site to get plan. String.</td>
  </tr>

  <tr>
   <td>possible_licenses {...}
   </td>
   <td>This block is returned if Alternative Licenses are enabled for the account. Structure.
   </td>
  </tr>
  <tr>
   <td><em>possible_licenses:</em><br>
   &nbsp;&nbsp;&nbsp;&nbsp;license
   </td>
   <td>License field value. For possible values, see <a href="#url-parameters">URL parameters</a>, above. String.</td>
  </tr>  
  <tr>
   <td><em>possible_licenses:</em><br>
   &nbsp;&nbsp;&nbsp;&nbsp;license_label
   </td>
   <td>Name of license to display to user (text localized by <code>locale</code> command). String.</td>
  </tr>
  <tr>
   <td><em>possible_licenses:</em><br>
   &nbsp;&nbsp;&nbsp;&nbsp;price_with_unit
   </td>
   <td>Cost in credits for user (text localized by <code>locale</code> command). String.</td>
  </tr>
</table>

## Examples

### Content/Info

#### Asset not purchased by this user

```http
GET /Rest/Libraries/1/Content/Info?content_id=59741022 HTTP/1.1
Host: stock.adobe.io
X-Product: MySampleApp/1.0
x-api-key: MyApiKey
Authorization: Bearer MyAccessToken
```

```json
{
  "contents": {
    "59741022": {
      "content_id": 59741022,
      "purchase_details": {
        "member_id": 23456,
        "state": "not_purchased"
      },
      "size": "Comp"
    }
  },
  "member": {
    "member_id": 23456
  }
}
```

#### Asset purchased by this user with Extended license

```http
GET /Rest/Libraries/1/Content/Info?content_id=62047262&license=Extended HTTP/1.1
Host: stock.adobe.io
X-Product: MySampleApp/1.0
x-api-key: MyApiKey
Authorization: Bearer MyAccessToken
```

```json
{
  "contents": {
    "62047262": {
      "content_id": 62047262,
      "purchase_details": {
        "date": "2015-02-10 15:00:00",
        "license": "Extended",
        "state": "purchased"
      },
      "size": "Original"
    }
  },
  "member": {
    "member_id": 23456
  }
}
```


### Content/License

#### Just-licensed asset with existing quota

Download from the `purchase_details.url` and indicate how many purchasing options remain for the user. You can download the asset again without using any quota or making a purchase.


```http
GET /Rest/Libraries/1/Content/License?content_id=62305369&license=Standard HTTP/1.1
Host: stock.adobe.io
X-Product: MySampleApp/1.0
x-api-key: MyApiKey
Authorization: Bearer MyAccessToken
```


```json
{
  "available_entitlement": {
    "quota": 48,
    "license_type_id": 42,
    "has_credit_model": false,
    "has_agency_model": true,
    "is_cce": true,
    "full_entitlement_quota": {
      "image_quota": 48
    }
  },
  "contents": {
    "62305369": {
      "content_id": "62305369",
      "size": "Comp",
      "purchase_details": {
        "state": "just_purchased",
        "license": "Standard",
        "date": "2017-11-01 22:57:48",
        "url": "https://stock.adobe.com/Rest/Libraries/Download/62305369/1",
        "content_type": "image/jpeg",
        "width": 3333,
        "height": 2222
      }
    }
  }
}
```

#### Using license references in license request
If your enterprise account is configured to require or allow optional license references, then the Content/License request must be made a POST, adding the references as JSON in the body of the message. Additionally, the `Content-Type` of the request should be `application/json`.

```http
POST /Rest/Libraries/1/Content/License?content_id=88744241&license=Standard HTTP/1.1
Host: stock.adobe.io
Content-Type: application/json
X-Product: MySampleApp/1.0
x-api-key: MyApiKey
Authorization: Bearer MyAccessToken

{
  "cce_agency": [
    { "id": "3", "value": "AcmePillows" },
    { "id": "4", "value": "123456789" }
  ]
}
```

JSON response is identical to response for a normal GET Content/License request.

### Member/Profile 

#### User has quota

```http
GET /Rest/Libraries/1/Member/Profile?content_id=112670342&license=Standard HTTP/1.1
Host: stock.adobe.io
X-Product: MySampleApp/1.0
x-api-key: MyApiKey
Authorization: Bearer MyAccessToken
```


```json
{
  "available_entitlement": {
    "quota": 48,
    "license_type_id": 1,
    "has_credit_model": false,
    "has_agency_model": false,
    "is_cce": false,
    "full_entitlement_quota": {
      "image_quota": 48
    }
  },
  "member": {
    "stock_id": 1272100
  },
  "purchase_options": {
    "state": "possible",
    "requires_checkout": false,
    "message": "This will use 1 of your 48 licenses."
  }
}
```


#### User has no quota but has an overage plan

```http
GET /Rest/Libraries/1/Member/Profile?content_id=64285595&license=Standard HTTP/1.1
Host: stock.adobe.io
X-Product: MySampleApp/1.0
x-api-key: MyApiKey
Authorization: Bearer MyAccessToken
```

```json
{
  "available_entitlement": {
    "quota": 0,
    "license_type_id": 1,
    "has_credit_model": false,
    "has_agency_model": false,
    "is_cce": false,
    "full_entitlement_quota": {
      "image_quota": 0
    }    
  },
  "purchase_options": {
    "state": "overage",
    "requires_checkout": false,
    "message": "Would you like to license the image for $2.99?"
  },
  "member": {
    "member_id": 34567
  }
}
```

#### User has no quota or overage plan

```http
GET /Rest/Libraries/1/Member/Profile?locale=en_US&content_id=64285595 HTTP/1.1
Host: stock.adobe.io
X-Product: MySampleApp/1.0
x-api-key: MyApiKey
Authorization: Bearer MyAccessToken
```


```json
{
  "available_entitlement": {
    "quota": 0,
    "full_entitlement_quota": {
      "image_quota": 0
    } 
  },
  "purchase_options": {
  "state": "not_possible",
  "requires_checkout": true,
  "message": "Would you like to see purchase options?",
  "url": "http://stock.adobe.io/plans?image_id=64285595"
  }
}
```

## Error codes

Each error generates a JSON array that contains the following keys and values. If your application receives this array and you need assistance, send the array to Adobe.



*   An **`error`** key.  
*   Optionally, a **`code`** key. Specifies an integer designating the category of error. Code values:  
    *   `10`: Invalid access token. The access token that you passed is invalid or expired.
    *   `11`: Invalid API Key. The API key that you passed is not valid or has expired.
    *   `20`: Invalid parameters. The URL parameters that you passed are not supported.
    *   `31`: Invalid Method. The method that you specified does not exist in the method list.
    *   `100`: Invalid data. Data that you specified as arguments are not supported.


## Downloading licensed files

The __Content/License__ method returns a download URL, which uses the __Libraries/Download__ endpoint. This URL can be accessed via a normal GET request over an HTTPS connection.

| Endpoint                                                       | Method |
| -------------------------------------------------------------- | ------ |
| https://stock.adobe.com/Rest/Libraries/Download/{id}/{license} | GET    |

In the URL above, the `{id}` value must be substituted with the Adobe Stock ID attribute, while `{license}` is an integer returned by Adobe Stock in the download URL. Because this integer value can change without notice, it is recommended not to _predict_ this value, but to get it by calling the `Content/License` method to first obtain the download URL.

### Authentication

Unlike other Stock API license methods, Download does not use request headers for authentication; using an authentication header can _break_ the download URL. Instead, the access token needs to be sent via a URL parameter--see below.

### Request headers

__None__ (see above).

### URL parameters

<table>
  <tr>
   <td><strong>Parameter</strong>
   </td>
   <td><strong>Description</strong>
   </td>
  </tr>
  <tr>
      <td>token</td>
      <td><p>Valid access token. String. Required, used in place of the <code>Authorization: Bearer {token}</code> authentication header.</p>
      <p>The token does not need to be the same one used to license the asset as long as it is from the same user or enterprise entitlement.</p></td>
  </tr>
  <tr>
      <td>size</td>
      <td><p>Optional, requested image size. Integer. Only valid for bitmap/image assets and videos only (e.g., does not apply to template, vector, or 3D assets.) </p>
<p>If command is not present, file returned will be full/original size. Valid values:</p>
<ul>
    <li><code>5000</code>: 5000px or original size image</li>
    <li><code>3100</code>: 3100-4999px image</li>
    <li><code>2400</code>: 2400-3099px image</li>
    <li><code>1600</code>: 1600-2399px image</li>
    <li><code>800</code>: 800-1599px image</li>
    <li><code>400</code>: 799px or smaller image</li>
    <li><code>2160</code><strong>*</strong>: Full/original 4K video</li>
    <li><code>1080</code><strong>*</strong>: HD 1080 video</li>
</ul>
<p><em>* Only applies to video.</em></p>
</td>
  </tr>
</table>

### Response

A valid response will trigger a file download, otherwise, an error will be returned. Downloads will typically consist of a 302 redirect which points to a signed URL, but some downloads are served directly without a redirect.

### Examples

In the following examples, "MyAccessToken" would be substituted with a valid access token generated by your application. When using curl, be sure to include the "follow redirects" option (`-L` on the command line.)

Download of original asset

```shell
curl -L "https://stock.adobe.com/Rest/Libraries/Download/62305369/1?token=MyAccessToken" > "AdobeStock_62305369.jpeg"
```

Download of 1600px image

```shell
curl -L "https://stock.adobe.com/Rest/Libraries/Download/62305369/1?token=MyAccessToken&size=1600" > "AdobeStock_62305369-1600.jpeg"
```

Download of 1080p rendition of video

```shell
curl -L "https://stock.adobe.com/Rest/Libraries/Download/99872034/1?token=MyAccessToken&size=1080" > "AdobeStock_99872034-1080.mp4"
```


### Download errors

- "This download cannot be processed, invalid size": Size command must be one of the values listed above, and file must be an image or video asset.
- "This download is expired": Token has expired
- "Cannot find a download for this file and license on this organization": Invalid access token, or generated for wrong user/account, or asset is not actually licensed. Use Content/Info or Member/Profile for more information.

## More information


*   See [Licensing Stock assets](../getting-started/apps/06-licensing-assets.md).
*   For guides to licensing assets for the enterprise or individual users, see the [Workflow guides](../getting-started/07-workflow-guides.md).
