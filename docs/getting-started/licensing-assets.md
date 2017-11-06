
# Licensing assets and stuff*

*Not a very catchy title, but there is more to this topic than how to license an asset!

> _**Tl;dr version:** Use the Member/Profile API to check the status of an asset you want to purchase, use Content/License to get a license, and download your asset._

In the previous section, we covered how to hook up the Search API into your application. In this article, we'll cover the Adobe Stock **Content** and **Member** methods of the License API. These can get information about a user's licensing (entitlement) status, determine whether the user has an existing license for an asset, request a new license for an asset for that user, get a signed download URL for the asset, and get a history of past licensed assets. 


## Licensing workflow

Before you can purchase an asset using the API, there are a few tasks you will want to perform as part of your application flow, and this may trigger multiple decision points depending on your [use case](#). Like a children's board game, the process is not difficult as long as you understand the rules.



![Licensing Flow board game](api-licensing-flow-game.png)



<table>
  <tr>
   <td><strong>1</strong>
   </td>
   <td>

<img src="images/Licensing-assets1.png" width="" alt="Create access token">

   </td>
   <td>To do any activity involving licensing, you will first need to generate an access token. This is covered in detail in <a href="#">Stock API Authentication</a> and in the individual OAuth and Service Account workflow guides.
   </td>
  </tr>
  <tr>
   <td><strong>2</strong>
   </td>
   <td>

<img src="images/Licensing-assets2.png" alt="Find asset">

   </td>
   <td>The user would start their journey by getting a file's Stock ID (also called "media ID" and "content ID") typically from a search. This was covered in the previous section, and is a key part of any Stock workflow.<ul>

<li>Stock IDs are not permanent/static. If you provide a checkout cart for your user, and the user saves the asset to license later, you should not assume that this asset is still available under that lD--you should verify. (See step #4, below.)
<li>Templates and 3D content are more likely to change IDs, because they are updated more often.

<!-- START HERE -->

<p>
For example, your user wants to license an image of a cute, fluffy kitten (and who wouldn't?) This is an extract of the JSON response, showing the ID we will need for our next set of operations.</li></ul>

   </td>
  </tr>
  <tr>
   <td>
   </td>
   <td>

<img src="./kitten-112670342.jpg" alt="Cute kitten">

   </td>
   <td>
   <code>{
<code>    ...</code>
<code>    "id": 112670342,</code>
<code>    "title": "Kitten on white blanket",</code>
<code>    ...</code>
<code>}</code>
   </td>
  </tr>
  <tr>
   <td><strong>3</strong>
   </td>
   <td>

<img src="images/Licensing-assets4.png" width="" alt="alt_text" title="image_tooltip">

   </td>
   <td>It's best practice to check if the item is licensed already. There are two methods for this:<ul>

<li><strong>As part of the search.</strong>  When you are performing the search in step #2, a convenience method is to add the <strong><code>is_licensed</code></strong> field, which will be set to the license name if that user or organization has licensed this asset.

The main difference between this search request and the ones discussed in the previous section is that this one must be authenticated with an access token. Note the headers used in this request are the same as the ones used for search, with the addition of the <strong><code>Authorization</code></strong> header. You will use these same headers for all remaining licensing requests.</ul>

   </td>
  </tr>
  <tr>
   <td>
   </td>
   <td>
   </td>
   <td><code>curl "https://stock.adobe.io/Rest/Media/1/Search/Files?locale=en_US&search_parameters%5Bmedia_id%5D=62305369&result_columns%5B%5D=id&result_columns%5B%5D=is_licensed" -H "x-api-key: YourApiKeyHere" -H "x-product: MySampleApp/1.0" -H "authorization: Bearer AccessTokenHere"</code>
<p>
<code>{</code>
<p>
<code>    "files": [</code>
<p>
<code>        {</code>
<p>
<code>            "id": 62305369,</code>
<p>
<code>            "is_licensed": "Standard"</code>
<p>
<code>        }</code>
<p>
<code>    ]</code>
<p>
<code>}</code>
   </td>
  </tr>
  <tr>
   <td>
   </td>
   <td>
   </td>
   <td><ul>

<li><strong>Using Content/Info. </strong>If you prefer an explicit method, you can call your first Licensing API request using Content/Info, which has the sole purpose of returning the current license state of an asset for that user (or for that org, if the user belongs to an enterprise).
Using this method, the API returns a <strong><code>contents </code></strong>object which contains the ID of the asset, and a sub-object <strong><code>purchase_details </code></strong>which lists the purchase state, license type and date.</ul>

   </td>
  </tr>
  <tr>
   <td>
   </td>
   <td>
   </td>
   <td><code>curl "https://stock.adobe.io/Rest/Libraries/1/Content/Info?content_id=62305369&license=Standard" -H "x-api-key: YourApiKeyHere" -H "x-product: MySampleApp/1.0" -H "authorization: Bearer AccessTokenHere"</code>
<p>
<code>{</code>
<p>
<code>    "contents": {</code>
<p>
<code>        "62305369": {</code>
<p>
<code>            "content_id": "62305369",</code>
<p>
<code>            "size": "Comp",</code>
<p>
<code>            "purchase_details": {</code>
<p>
<code>                "state": "purchased",</code>
<p>
<code>                "license": "Standard",</code>
<p>
<code>                "date": "2017-11-01 22:57:48"</code>
<p>
<code>            }</code>
<p>
<code>        }</code>
<p>
<code>    }</code>
<p>
<code>}</code>
   </td>
  </tr>
  <tr>
   <td>
   </td>
   <td>
   </td>
   <td>In both cases, if the asset <em>is</em> licensed, then there is nothing else to do but get the URL and download the asset. <em>Proceed to <a href="#">step #6</a></em>.
   </td>
  </tr>
  <tr>
   <td>4
   </td>
   <td>

<p id="gdcalert6" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/Licensing-assets5.png). Store image on your image server and adjust path/filename if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert7">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


<img src="images/Licensing-assets5.png" width="" alt="alt_text" title="image_tooltip">

   </td>
   <td>Assuming the asset is not already licensed, then you will check to see if it's possible to license this asset. This is to prevent a bad user experience and error if you tried to license the item and it cannot be licensed.
<p>
For this step, you will use the <strong>Member/Profile</strong> API. In addition to checking if the asset can be licensed, this will return the user's quota and also return a localized message about how many credits the license will cost. Getting the quota is useful as you can show that the user has "X" number of credits in your application's UI.
<p>
For the query string, you will need to provide the Stock ID with the <strong><code>content_id</code></strong> parameter. Ideally, also provide the license type if it is known (because some assets can have multiple license types); for images the normal default is "Standard." Refer to the <a href="#">Licensing API reference</a> for more details. Also, if you want a localized message, also provide the language code.
   </td>
  </tr>
  <tr>
   <td>
   </td>
   <td>
   </td>
   <td><code>curl "https://stock.adobe.io/Rest/Libraries/1/Member/Profile?content_id=112670342&license=Standard&locale=en_US" -H "x-api-key: YourApiKeyHere" -H "x-product: MySampleApp/1.0" -H "authorization: Bearer AccessTokenHere"</code>
<p>
<code>{</code>
<p>
<code>    "available_entitlement": {</code>
<p>
<code>        "quota": 1,</code>
<p>
<code>        "license_type_id": 42,</code>
<p>
<code>        "has_credit_model": false,</code>
<p>
<code>        "has_agency_model": true,</code>
<p>
<code>        "is_cce": true,</code>
<p>
<code>        "full_entitlement_quota": {</code>
<p>
<code>            "standard_credits_quota": 1</code>
<p>
<code>        }</code>
<p>
<code>    },</code>
<p>
<code>    "purchase_options": {</code>
<p>
<code>        "state": "possible",</code>
<p>
<code>        "requires_checkout": false,</code>
<p>
<code>        "message": "This will use your last standard credit."</code>
<p>
<code>    }</code>
<p>
<code>}</code>
   </td>
  </tr>
  <tr>
   <td>
   </td>
   <td>
   </td>
   <td>In the example, you can see that the <strong><code>purchase_options.state</code></strong> is "possible," which tells you that licensing can continue. If it was <strong>not</strong> possible to license, go to <a href="#">Troubleshooting</a>, below. Otherwise, continue to the next step.
   </td>
  </tr>
  <tr>
   <td>5
   </td>
   <td>

<p id="gdcalert7" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/Licensing-assets6.png). Store image on your image server and adjust path/filename if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert8">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


<img src="images/Licensing-assets6.png" width="" alt="alt_text" title="image_tooltip">

   </td>
   <td>Now you will perform the license purchase request using the <strong>Content/License</strong> API, which will deduct credits from the user or organizational account.
<p>
Use the same parameters and headers that you used in the Member/Profile request.
   </td>
  </tr>
  <tr>
   <td>
   </td>
   <td>
   </td>
   <td>curl "https://stock.adobe.io/Rest/Libraries/1/Content/License?content_id=112670342&license=Standard" -H "x-api-key: YourApiKeyHere" -H "x-product: MySampleApp/1.0" -H "authorization: Bearer AccessTokenHere"
<p>
{
<p>
    "available_entitlement": {
<p>
        "quota": 0,
<p>
        "license_type_id": 42,
<p>
        "has_credit_model": false,
<p>
        "has_agency_model": true,
<p>
        "is_cce": true,
<p>
        "full_entitlement_quota": {
<p>
            "standard_credits_quota": 1
<p>
        }
<p>
    },
<p>
    "contents": {
<p>
        "112670342": {
<p>
            "content_id": "112670342",
<p>
            "size": "Comp",
<p>
            "purchase_details": {
<p>
                "state": "just_purchased",
<p>
                "license": "Standard",
<p>
                "date": "2017-11-06 02:54:18",
<p>
                "url": "https://stock.adobe.com/Rest/Libraries/Download/112670342/1",
<p>
                "content_type": "image/jpeg",
<p>
                "width": 4256,
<p>
                "height": 2832
<p>
            }, ...
<p>
        }
<p>
    }
<p>
}
   </td>
  </tr>
  <tr>
   <td>
   </td>
   <td>
   </td>
   <td>The response returns several fields, but the most important for your user is in the <strong><code>contents.XXX.purchase_details</code></strong> object (where "XXX" is the Stock ID). Here you will find the URL you will need to download the asset. In addition, if you want to update your UI, you can get the updated quota value from the response.
   </td>
  </tr>
  <tr>
   <td>6
   </td>
   <td>

<p id="gdcalert8" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/Licensing-assets7.png). Store image on your image server and adjust path/filename if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert9">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


<img src="images/Licensing-assets7.png" width="" alt="alt_text" title="image_tooltip">

   </td>
   <td>Finally, you can download the full asset. Instead of calling to an API endpoint, you will call directly to the URL of the licensed asset that you obtained in the previous step.
<p>
Note that unlike the other requests, you do <em>not</em> include the Authorization header, because that can cause the request to fail. Instead, you pass the access token using the <strong><code>token</code></strong> parameter. The download also requires that your application has the ability to follow a redirect--make sure this option is enabled on your download client.
   </td>
  </tr>
  <tr>
   <td>
   </td>
   <td>
   </td>
   <td><code>https://stock.adobe.com/Rest/Libraries/Download/112670342/1?token=AccessTokenHere</code>
<p>
<code>HTTP/1.1 200 OK</code>
<p>
<code>Accept-Ranges: bytes</code>
<p>
<code>Content-Disposition: attachment; filename="AdobeStock_112670342.jpeg";</code>
<p>
<code>Content-Length: 176164</code>
<p>
<code>Content-Type: image/jpeg</code>
<p>
<code>Date: Mon, 06 Nov 2017 03:18:50 GMT</code>
   </td>
  </tr>
  <tr>
   <td>
   </td>
   <td>
   </td>
   <td>This URL will trigger a download of the full asset. If you need a different size of the asset (for images only), you can use the <strong><code>size=</code></strong> parameter. See <a href="#">Q&A,</a> below.
Note that the token parameter does not include the word "Bearer" as it does when used as a header.
   </td>
  </tr>
</table>


And you are done! 

...assuming nothing went wrong. If it did, then continue reading. Also, check out common questions and answers about licensing assets. 

You have completed the Adobe Stock API getting started guide. 



*   If you want more detail on anything you have read thus far, explore the [API reference documentation](#).
*   If you are ready to get started in earnest, browse our [sample code and SDKs](#).


## Troubleshooting licensing requests

In most cases, everything should happen as outlined above. But if it doesn't, you need to know how to diagnose the issue and correct it.


### Member/Profile issues

In step #4 above, we used Member/Profile to check if licensing was possible. But what if it was not possible?

In that case, we would have seen a JSON response message like this:


```
        "purchase_options": {
            "state": "not_possible",
```


If this happens, you can build some checks into your application. 


<table>
  <tr>
   <td>

<p id="gdcalert9" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/Licensing-assets8.png). Store image on your image server and adjust path/filename if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert10">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


<img src="images/Licensing-assets8.png" width="" alt="alt_text" title="image_tooltip">

   </td>
   <td>
<h4>Problem: Asset type can't be licensed</h4>

First, check the type of asset you are licensing and compare that to what you are allowed to license. One of the most common situations is that you are trying to license a Premium or Video asset, both of which cannot be licensed under a standard image subscription.
<p>
For a better understanding of asset types and how to license them, see the <a href="https://stock.adobe.com/plans#get-credits">Adobe Stock plans</a> page.
   </td>
  </tr>
  <tr>
   <td>
   </td>
   <td>You will see this message if the asset is Premium or Video, and your plan does not include this content:
<p>
<code>{</code>
<p>
<code>    "available_entitlement": null,</code>
<p>
<code>    "member": {</code>
<p>
<code>        "stock_id": 43289915</code>
<p>
<code>    },</code>
<p>
<code>    "purchase_options": {</code>
<p>
<code>        "state": "not_possible",</code>
<p>
<code>        "message": "You cannot license this type of asset with your standard credits quota. Please contact your administrator to upgrade your current plan."</code>
<p>
<code>    }</code>
<p>
<code>}</code>
<p>
While the message above does not tell you what type of asset this is, you can get this from the search result you performed in step #2, above, if you request the content_type and premium_level_id response fields.
<p>
If the premium level is not 0 (standard), then it's a good chance your plan does not cover it.
<p>
<code>{</code>
<p>
<code>    "nb_results": 1,</code>
<p>
<code>    "files": [</code>
<p>
<code>        {</code>
<p>
<code>            "id": 124287454,</code>
<p>
<code>            "title": "Kitten Peeking Through Barn Doors; Steinbach, Manitoba, Canada",</code>
<p>
<code>            "content_type": "image/jpeg",</code>
<p>
<code>            "premium_level_id": 3</code>
<p>
<code>        }</code>
<p>
<code>    ]</code>
<p>
<code>}</code>
<p>
Video assets could have a premium_level of 0, but simply because the content type is video, realize that you must have credits to license it.
<p>
<code>{</code>
<p>
<code>    "nb_results": 1,</code>
<p>
<code>    "files": [</code>
<p>
<code>        {</code>
<p>
<code>            "id": 121222021,</code>
<p>
<code>            "title": "Christmas kitten meows",</code>
<p>
<code>            "content_type": "video/quicktime",</code>
<p>
<code>            "premium_level_id": 0</code>
<p>
<code>        }</code>
<p>
<code>    ]</code>
<p>
<code>}</code>
   </td>
  </tr>
  <tr>
   <td>

<p id="gdcalert10" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/Licensing-assets9.png). Store image on your image server and adjust path/filename if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert11">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


<img src="images/Licensing-assets9.png" width="" alt="alt_text" title="image_tooltip">

   </td>
   <td>
<h4>Solution: Purchase universal credits</h4>

If you do not belong to an enterprise entitlement, the solution is easy: go to <a href="https://stock.adobe.com/plans">Adobe Stock plans</a> and purchase a universal credit pack. You can still use your image credits for standard images, templates and 3D, and reserve your new credits for special assets.
<p>
If you are an enterprise organization, you will need to contact your Adobe sales team for options.
   </td>
  </tr>
  <tr>
   <td>

<p id="gdcalert11" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/Licensing-assets10.png). Store image on your image server and adjust path/filename if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert12">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


<img src="images/Licensing-assets10.png" width="" alt="alt_text" title="image_tooltip">

   </td>
   <td>
<h4>Problem: No credits or not enough credits</h4>

The next most common scenario is that you don't have enough credits in your account. Situations where this may occur:<ul>

<li><em>All credits have been used up. </em>This is indicated by a <strong><code>available_entitlement.quota</code></strong> property value of 0.
<li><em>The asset you want to license requires more credits than you have.</em> As mentioned above, Premium and Video assets cannot be licensed using standard image credits--they require universal credits. Furthermore, the price per asset is greater than one credit. As seen on the <a href="https://stock.adobe.com/plans">Adobe Stock plans</a> page, these assets could range from 2 credits up to 50 credits each.

<p>
In both cases, if <strong><code>purchase_options.requires_checkout</code></strong> is <strong><code>true</code></strong> but licensing is not possible, then you will need to take actions on your account to add more credits.</ul>

   </td>
  </tr>
  <tr>
   <td>
   </td>
   <td>{
<p>
    "available_entitlement": {
<p>
        "quota": 0,
<p>
        "full_entitlement_quota": {
<p>
            "standard_credits_quota": 0
<p>
        }
<p>
    },
<p>
    "purchase_options": {
<p>
        "state": "not_possible",
<p>
        "requires_checkout": true,
<p>
        "message": "You don't have enough standard credits to license this asset. Please contact your administrator to add more standard credits to your account."
<p>
    }
<p>
}
   </td>
  </tr>
  <tr>
   <td>

<p id="gdcalert12" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/Licensing-assets11.png). Store image on your image server and adjust path/filename if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert13">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


<img src="images/Licensing-assets11.png" width="" alt="alt_text" title="image_tooltip">

<p>


<p id="gdcalert13" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/Licensing-assets12.png). Store image on your image server and adjust path/filename if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert14">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


<img src="images/Licensing-assets12.png" width="" alt="alt_text" title="image_tooltip">

   </td>
   <td>
<h4>Solution (enterprise): Add credits to your PLC</h4>

If you belong to an enterprise organization and you need more credits than you have available, you can simply add additional quota in your product license configuration (PLC). This is done by your account administrator, as described in the <a href="#">Service Account workflow guide</a>, or <a href="https://helpx.adobe.com/enterprise/help/adobe-stock-enterprise.html#Updateaproductconfigurationsdownloadlimit">documented here</a>. Enterprise customers can add more quota than they have available, and those extra credits will be charged back at a later date.
<p>
One reason enterprises may want to intentionally limit their PLC is if each department is given a separate quota. A quota limit allows the administrator to force the department to request more quota if it goes over its limit.
<p>
Note that admins can only add more credits for the type of assets they have agreed to license, per their contract. For example, if a contract only has standard image credits, the administrator cannot add universal credits for Premium or Video assets. In this case, the Adobe sales team must be contacted.
   </td>
  </tr>
  <tr>
   <td>

<p id="gdcalert14" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/Licensing-assets13.png). Store image on your image server and adjust path/filename if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert15">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


<img src="images/Licensing-assets13.png" width="" alt="alt_text" title="image_tooltip">

   </td>
   <td>
<h4>Solution (non-enterprise): Buy more credits or extend your subscription</h4>

You need credits to purchase licenses, whether those are standard image credits or universal credits. If you do not belong to an enterprise org, you can get a subscription or credit pack from <a href="https://stock.adobe.com/plans">Adobe Stock plans</a>.
<p>
Note that some assets (such as some Creative Cloud templates) are free, and do not require a subscription or credits as long as you are a non-enterprise user.
   </td>
  </tr>
  <tr>
   <td>

<p id="gdcalert15" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/Licensing-assets14.png). Store image on your image server and adjust path/filename if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert16">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


<img src="images/Licensing-assets14.png" width="" alt="alt_text" title="image_tooltip">

   </td>
   <td>
<h4>Solution: Look further or get help</h4>

In the unlikely case where you do have credits <em>and</em> licensing is possible, then something else may be happening. Use the <a href="https://www.getpostman.com/">Postman </a>or <a href="https://curl.haxx.se/">curl </a>tools to verify that your application is not getting bad or stale data (for example, it is caching previous responses), and the Adobe Stock API is not returning a 400 error (see below). 
<p>
If none of those things are happening, then <a href="#">contact Support</a>.
   </td>
  </tr>
</table>



### Other problems 

In the previous cases, Member/Profile and the other APIs will return a valid 200 response. But in the cases below, you will get a 400 error. Here are a couple common reasons, but see more in the [Stock API reference](#) section.


#### Wrong license type

You can get this error if you try to license an asset with a mismatched license type. For example, Images, 3D and Templates allow a "Standard" license, but Videos have valid licenses of "Video_HD" and "Video_4K." If you do not specify one of those values for a video, you will get this error.

{

    "error": "The license \"Standard\" does not match type of content #121222021",

    "code": "020",

    "case": "pkx2JXqL7ePdNCXAewS2R7U0PvF27KND"

}


#### Expired or invalid token

As a best practice, your user should have to sign in for each session, or if you are using an enterprise application, it should request a new token. Access tokens have a 24-hour expiration, so unless you use a refresh token or unless your user told the Adobe sign-in screen to "remember me," it is expected that the token will expire after a fixed amount of time.


```
    {
        "error_code": "401013",
        "message": "Oauth token is not valid"
    }
```



## Q&A


### Can I find out how many credits are remaining?

Yes, from the **Member/Profile** API. You can either call a specific ID and license type, or leave off those parameters.

Note that if you do include the content_id in your request and you have more than one type of credit (e.g., standard image credits and universal credits), the **<code>available_entitlement.quota</code></strong> value will reflect the credits available <em>for that type of asset</em>. For example, if you are querying about a video, then you will get back the number of credits available for Video and Premium content.


```
    curl "https://stock.adobe.io/Rest/Libraries/1/Member/Profile?locale=en_US" -H "x-api-key: YourApiKeyHere" -H "x-product: MySampleApp/1.0" -H "authorization: Bearer AccessTokenHere"

    {
        "available_entitlement": {
            "quota": 3,
            "license_type_id": 16,
            "has_credit_model": true,
            "has_agency_model": false,
            "is_cce": true,
            "full_entitlement_quota": {
                "credits_quota": 3
            }
        },
        "member": {
            "stock_id": 41003529
        },
        "purchase_options": {
            "state": "possible",
            "requires_checkout": false,
            "message": "This will use 1 of your 3 credits."
        }
    }
```



### How do you know if the user has an enterprise entitlement?

In general, if you are a _member_ of an Adobe Enterprise customer you will realize this, however, if you are a partner using an OAuth integration, you may not know in advance what kind of user is signing in. For example, you may want to identify and target enterprise users differently than regular users. 

This can also be done using the **Member/Profile** API. In the previous example, the property `available_entitlement.is_cce` will be __`true`__ if the user has an enterprise Stock entitlement.

One thing to note is that Adobe Stock does not have enterprise "accounts" but rather enterprise "entitlements." The point is that a user may be signed in with a free Creative Cloud account, but still be assigned to an account which has enterprise Stock entitlements. That same user may have a personal subscription to Creative Cloud applications. It is best to use the term "entitlement" rather than "account" when dealing with enterprise at Adobe.


### Is it possible to get an image in different size?

When you license an image, you license the full/original size of the asset. However, there are times when you need a smaller size, for example to include in a social media post or email. 

The Stock API has a command for this situation. Simply add a `size=` parameter to the download URL, and Adobe Stock will return the _next larger_ size. The actual size returned depends on the original size of the asset; see the [Licensing API reference](#) for more details.

Example: You want a 350px image for your page.

```
    https://stock.adobe.io/Rest/Libraries/Download/112670342/1?size=350&token=AccessTokenHere
```

### How can I test licensing assets if I do not have an Adobe Stock plan or contract?

If you are an independent developer or student, you can follow the [Affiliate workflow](#) and sign up for an API key. If you want to also test licensing an asset, you can test it on a free asset like one of our great [Creative Cloud Stock templates](https://stock.adobe.com/templates) (not all templates are free, but many are).

However, if you are interested in partnering with Adobe Stock and have a legitimate reason for a demo account, [contact us](#).

