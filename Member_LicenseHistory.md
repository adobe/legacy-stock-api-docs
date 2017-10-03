# License History Reference


The License History API is used to retrieve past license history. For information on licensing Adobe Stock content, see [Licensing Stock Assets](https://www.adobe.io/apis/creativecloud/stock/docs/licensing.html).


## License History Calls

The License History API returns a list of assets licensed for a given user or organization.

Endpoints | Method
------------ | -------------
https://stock.adobe.io/Rest/Libraries/1/*Member/LicenseHistory* | GET


### Authentication

The Authorization header is required for any of the licensing calls. The API uses the header to determine the user's member number, licensing status, and default locale. See[ Authentication for API Access](https://www.adobe.io/apis/creativecloud/stock/docs/authentication.html).


### Request Headers

See [Headers for Stock API Calls](https://www.adobe.io/apis/creativecloud/stock/docs/api/headers.html) for details about header content. 

*   Required headers: x-Product, x-api-key, Authorization
*   Optional headers: X-Product-Location, X-Request-Id


### URL parameters

Pass the following URL parameters with the GET request.


| **Parameter** | **Description** |
|---------------|-----------------|
| locale | Location language code. String. Default is en-US.  See [Locales](https://www.adobe.io/apis/creativecloud/stock/docs/api/locales.html). |
| search_parameters[limit] | Optional, for pagination. Maximum number of assets to return in the call. Call repeatedly with different [offset] values to page through the found assets. Valid values are 1 through 100. Integer. Default is 100. |
| search_parameters[limit] | Optional, start position in query. Valid values are 0 (the first found asset) or higher integers. Integer. Default is 0. <br><br> With each successive call for your search, increment this by the [limit] value to get the next page of assets. For example, by default your first call uses a 0 offset and limit of 100 to return the first 100 found assets. Call this API again with an offset of 100 to retrieve the next page. |
| search_parameters[thumbnail_size] | Optional, thumbnail size in pixels. Specify the size of thumbnail to return for each found asset. Integer. <br><br>Valid values and meanings:<br> 110: Medium (110px)<br> 160: Big (160px)<br> 500: Extra large (500px). Returned with watermark.<br> 1000 Extra-extra large (1000px). Returned with watermark. |
| result_columns[] | Fields to include in the search result. For more information, see [Search Reference](https://www.adobe.io/apis/creativecloud/stock/docs/api/search.html).<br><br> thumbnail_110_url<br>thumbnail_110_height<br>thumbnail_110_width<br>thumbnail_160_url<br>thumbnail_160_height<br>thumbnail_160_width<br>thumbnail_220_url<br>thumbnail_220_height<br>thumbnail_220_width<br>thumbnail_240_url<br>thumbnail_240_height<br>thumbnail_240_width<br>thumbnail_500_url<br>thumbnail_500_height<br>thumbnail_500_width<br>thumbnail_1000_url<br>thumbnail_1000_height<br>thumbnail_1000_width |



## **Responses**

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


| **Name** | **Description** |
|---------------|-----------------|
| *nb_results| Total number of found assets in the search result. Integer. |
| *license| License type (Standard, Extended, Video_HD, Video_4K). String. |
| *license_date| Date asset was licensed. String. |
| *download_url| URL to download the licensed asset (requires authentication). String. |
| *id| Item ID. Integer. |
| *title| Item title. String. |
| *creator_name| Creator Name. String. |
| *creator_id| Creator unique ID. String. |
| *content_url| |
| *media_type_id| Type of media (1 - photo, 2 - illustration, 3 - vector, 4 - video). Integer. |
| *vector_type| If the file is a vector indicates if its a svg or a ai/eps (reported as zip). String or Null. |
| *content_type| Content type of the file (e.g. image/jpeg). String. |
| *height| Item height. Integer. |
| *width| Item width. Integer. |
| *details_url| URL to Adobe Stock with content details. String. |
| thumbnail_110_url| URL for 110px thumbnail. String. |
| thumbnail_110_height| Height for 110px thumbnail. Integer. |
| thumbnail_110_width| Width for 110px thumbnail. Integer. |
| thumbnail_160_url | *Same as above, respective to thumbnail size.* |
| thumbnail_160_height | *Same as above, respective to thumbnail size.* |
| thumbnail_160_width | *Same as above, respective to thumbnail size.* |
| thumbnail_220_url| |
| thumbnail_220_height| |
| thumbnail_220_width| |
| thumbnail_240_url| |
| thumbnail_240_height| |
| thumbnail_240_width| |
| thumbnail_500_url| |
| thumbnail_500_height| |
| thumbnail_500_width| |
| thumbnail_1000_url| |
| thumbnail_1000_height| |
| thumbnail_1000_width| |



## **Example Request and Response**

**Request**

```http
GET https://stock.adobe.io/Rest/Libraries/1/Member/LicenseHistory HTTP/1.1
------------- headers -------------
X-Product: MyApp/1.0
x-api-key: myAPIkey
Authorization: Bearer *token*
```


**Response**

```json
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
        }, ... 
    ]
}
```

