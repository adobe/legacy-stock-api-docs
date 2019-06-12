# License History Reference


<!-- MarkdownTOC -->

- [License History calls](#license-history-calls)
    - [Authentication](#authentication)
    - [Request headers](#request-headers)
    - [URL parameters](#url-parameters)
- [Responses](#responses)
- [Example requests and responses](#example-requests-and-responses)
    - [Simple example](#simple-example)
    - [Example fetching all history and using pagination](#example-fetching-all-history-and-using-pagination)

<!-- /MarkdownTOC -->


The License History API is used to retrieve past license history. For information on licensing Adobe Stock content, see [Licensing Stock assets](../getting-started/apps/06-licensing-assets.md).

<a id="license-history-calls"></a>
## License History calls

The License History API returns a list of assets licensed for a given user or organization.

| Endpoint | Method |
| ------------ | ------------- |
| https://stock.adobe.io/Rest/Libraries/1/Member/LicenseHistory | GET |


<a id="authentication"></a>
### Authentication

The Authorization header is required for any of the licensing calls. The API uses the header to determine the user's member number, licensing status, and default locale. See [API authentication](../getting-started/03-api-authentication.md) for more information.


<a id="request-headers"></a>
### Request headers

See [Headers for Stock API calls](10-headers-for-api-calls.md) for details about header content. 

*   Required headers: `x-Product`, `x-api-key`, `Authorization`
*   Optional headers: `X-Request-Id`


<a id="url-parameters"></a>
### URL parameters

Pass the following URL parameters with the GET request.


| Parameter | Description |
|---------------|-----------------|
| locale | Location language code. String. Default is <code>en_US</code>. See the full list of <a href="14-locale-codes.md">Locales</a>. |
| search_parameters[limit] | Optional, for pagination. Maximum number of assets to return in the call. Call repeatedly with different `[offset]` values to page through the found assets. Valid values are 1 through 100. Integer. Default is 100. |
| search_parameters[offset] | Optional, start position in query. Valid values are 0 (the first found asset) or higher integers. Integer. Default is 0. <br><br> With each successive call for your search, increment this by the `[limit]` value to get the next page of assets. For example, by default your first call uses a 0 offset and limit of 100 to return the first 100 found assets. Call this API again with an offset of 100 to retrieve the next page. |
| search_parameters[thumbnail_size] | Optional, thumbnail size in pixels. Specify the size of thumbnail to return for each found asset. Integer. <br><br>Valid values and meanings:<br> `110`: Small (110px)<br> `160`: Medium (160px)<br> `240`: Large (240px)<br> `500`: Extra large (500px). Returned with watermark.<br> `1000`: Extra-extra large (1000px). Returned with watermark. |
| all | Optional, set to true or false to indicate whether all license history for the organization should be returned, or only the license history for the currently selected user or profile (PLC). Boolean.<br><br>Valid values and meanings:<br>`true`: Show all license history for the entire organization.<br>`false` (or remove parameter): Show only history for the current user or Stock profile. Default. |
| result_columns[] | Allowable fields to include in the history results. For more information, see [Search API reference](11-search-reference.md).<br><br> `thumbnail_110_url` `thumbnail_110_height` `thumbnail_110_width` `thumbnail_160_url` `thumbnail_160_height` `thumbnail_160_width` `thumbnail_220_url` `thumbnail_220_height` `thumbnail_220_width` `thumbnail_240_url` `thumbnail_240_height` `thumbnail_240_width` `thumbnail_500_url` `thumbnail_500_height` `thumbnail_500_width` `thumbnail_1000_url` `thumbnail_1000_height` `thumbnail_1000_width` |


<a id="responses"></a>
## Responses

Files are returned in a JSON array with this structure.

```json
{
  "nb_results": 0,
  "files": [
    {
      "license": "string",
      "license_date": "string",
      "download_url": "string",
      "id": 0,
      "title": "string",
      "width": 0,
      "height": 0,
      "creator_name": "string",
      "creator_id": 0,
      "media_type_id": 1,
      "vector_type": "Unknown Type: string,null",
      "content_type": "string",
      "thumbnail_url": "string",
      "thumbnail_width": 0,
      "thumbnail_height": 0,
      "details_url": "string"
    }
  ]
}
```

In the table below, fields marked with __*__ are returned by default.

| Name | Description |
|---------------|-----------------|
| *nb_results| Total number of found assets in the search result. Integer. |
| *license| License type (`Standard`, `Standard_M`, `Extended`, `Video_HD`, `Video_4K`). String. |
| *license_date| Date asset was licensed. String. |
| *download_url| URL to download the licensed asset (requires authentication). String. |
| *id| Item ID. Integer. |
| *title| Item title. String. |
| *creator_name| Creator Name. String. |
| *creator_id| Creator unique ID. String. |
| *content_url| (Deprecated). String. |
| *media_type_id| Content type of the asset (`1`: Photos, `2`: Illustrations, `3`: Vectors, `4`: Videos, `6`: 3D, `7`: Templates). Integer. |
| *vector_type| If the file is a vector indicates if its a svg or a ai/eps (reported as zip). String or Null. |
| *content_type| Content type of the file (e.g. `image/jpeg`). String. |
| *height| Item height. Integer. |
| *width| Item width. Integer. |
| *details_url | URL to Adobe Stock with content details. String. |
| thumbnail_*_url | URL for the requested thumbnail size, where * is the thumbnail size in pixels. You can use this to display the thumbnail on your page using your preferred display method. String. |
| thumbnail_*_width | Width for the thumbnail of the requested size, where * is the thumbnail size in pixels. Integer. For example: `"thumbnail_160_width": 200` |
| thumbnail_*_height | Height for the thumbnail of the requested size, where * is the thumbnail size in pixels. Integer. |

_**Note:** Allowable values for `thumbnail_*_url`, `thumbnail_*_width` and `thumbnail_*_height` fields: `110`, `160`, `220`, `240`, `500`, `1000`._

<a id="example-requests-and-responses"></a>
## Example requests and responses

<a id="simple-example"></a>
### Simple example
```http
GET /Rest/Libraries/1/Member/LicenseHistory HTTP/1.1
Host: stock.adobe.io
X-Product: MySampleApp/1.0
x-api-key: MyApiKey
Authorization: Bearer MyAccessToken
```

```javascript
{
    "nb_results": 13,
    "files": [
        {
            "license": "Standard",
            "license_date": "9/21/17, 9:00 PM",
            "download_url": "https://stock.adobe.com/Download/DownloadFileDirectly/xM0nanNQXGEFjXOfV3RBTMet3uP2qDwe",
            "id": 121684652,
            "title": "Modern Album Layouts",
            "creator_name": "Creativedash",
            "creator_id": 206267052,
            "content_url": "https://stock.adobe.com/Rest/stock-photo/modern-album-layouts/121684652",
            "media_type_id": 7,
            "vector_type": null,
            "content_type": "image/vnd.adobe.photoshop.template",
            "height": 1424,
            "width": 2048,
            "details_url": "https://stock.adobe.com/121684652?as_channel=affiliate&as_source=api&as_content=73ebcc931b9c454b8cb150816fadb06a"
        }, /*... more files */
    ]
}
```

<a id="example-fetching-all-history-and-using-pagination"></a>
### Example fetching all history and using pagination
```http
GET /Rest/Libraries/1/Member/LicenseHistory?search_parameters[limit]=20&search_parameters[offset]=0&all=true HTTP/1.1
Host: stock.adobe.io
X-Product: MySampleApp/1.0
x-api-key: MyApiKey
Authorization: Bearer MyAccessToken
```
In the example above, the request will return the first 20 of 239 results. Without this command, the API returned only 13 results (see previous example.)

```javascript
{
    "nb_results": 239,
    "files": [
        {
            "license": "Standard",
            "license_date": "9/21/17, 9:00 PM",
            "download_url": "https://stock.adobe.com/Download/DownloadFileDirectly/xM0nanNQXGEFjXOfV3RBTMet3uP2qDwe",
            "id": 121684652,
            "title": "Modern Album Layouts",
            "creator_name": "Creativedash",
            "creator_id": 206267052,
            "content_url": "https://stock.adobe.com/Rest/stock-photo/modern-album-layouts/121684652",
            "media_type_id": 7,
            "vector_type": null,
            "content_type": "image/vnd.adobe.photoshop.template",
            "height": 1424,
            "width": 2048,
            "details_url": "https://stock.adobe.com/121684652?as_channel=affiliate&as_source=api&as_content=73ebcc931b9c454b8cb150816fadb06a"
        }, /*... more files */
    ]
}
```
