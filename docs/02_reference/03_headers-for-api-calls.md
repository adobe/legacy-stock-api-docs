# Headers for Stock API Calls

All Adobe Stock API calls require certain headers and allow the inclusion of some  optional headers.


## Headers


<table>
  <tr>
   <td><strong>Header</strong>
   </td>
   <td><strong>Detail</strong>
   </td>
  </tr>
  <tr>
   <td>X-Product
   </td>
   <td>Required string. The name of your product or website that is using the API. Use the product name followed by a slash and the product version. For example:
<p>
X-Product: Photoshop/15.2.0
   </td>
  </tr>
  <tr>
   <td>X-Product-Location
   </td>
   <td>Optional string. Identifies a location within your application where the API call occurs, for example, if the API is called from a specific page or area on your site. Optionally include slash followed by the version. For example:
<p>
X-Product-Location: Libraries/1.0.0
<p>
 
   </td>
  </tr>
  <tr>
   <td>Authorization
   </td>
   <td>Optional string; required for access to asset licensing. A current access token obtained from a token-exchange request. See<a href="https://www.adobe.io/apis/creativecloud/stock/docs/setup.html"> Setting Up Access</a>.
<p>
The Stock API uses this to access your user's member identifier, licensing status, and default locale for localizing categories and messages that the API returns.
   </td>
  </tr>
  <tr>
   <td>x-api-key
   </td>
   <td>Required string. The API key assigned to your API client account when you signed up through adobe.io. See<a href="https://www.adobe.io/apis/creativecloud/stock/docs/setup.html"> Setting Up Access</a>.
   </td>
  </tr>
  <tr>
   <td>X-Request-Id
   </td>
   <td>Optional string. A unique request identifier that you define. Used to trace the request in logs. 
   </td>
  </tr>
</table>



## Examples


### Headers and parameters for a Search GET

```
GET https://stock.adobe.io/Rest/Media/1/Search/Files?locale=en-US&search_parameters%5Bwords%5D=dogs&search_parameters%5Blimit%5D=2
------------------------- headers --------------------------
X-Product: Photoshop/15.2.0
X-Product-Location: Libraries/1.0.0
x-api-key: myAPIkey
```

### Headers and parameters for a licensing information GET

```
# file 59741022 
$ curl -s 'https://stock.adobe.io/Rest/Libraries/1/Content/Info?content_id=59741022' -H 'X-Product: Photoshop/15.2.0' -H 'X-Product-Location: Libraries/1.0.0' -H 'Authorization: Bearer ...'
```
