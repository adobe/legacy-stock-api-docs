# Bulk metadata Files API reference

The Files API is used to retrieve metadata from Adobe Stock, either one asset at a time, or in bulk.

<!-- MarkdownTOC -->

- [Files requests](#files-requests)
    - [Authentication](#authentication)
    - [Request headers](#request-headers)
    - [URL parameters](#url-parameters)
- [Responses](#responses)
- [Example requests and responses](#example-requests-and-responses)
- [Error handling](#error-handling)
    - [Example invalid requests and error responses](#example-invalid-requests-and-error-responses)

<!-- /MarkdownTOC -->

<a id="files-requests"></a>
## Files requests

The Files API returns a JSON-formatted list of metadata attributes for a given list of Stock asset IDs.

| Endpoints | Methods |
|-----------|---------|
| https://stock.adobe.io/Rest/Media/1/Files | GET | 

<a id="authentication"></a>
### Authentication

Optional. The Authorization header is only required for retrieving the `is_licensed` field.

<a id="request-headers"></a>
### Request headers

See [Headers for Stock API Calls](https://www.adobe.io/apis/creativecloud/stock/docs.html#!adobe/stock-api-docs/master/docs/api/10-headers-for-api-calls.md) for details about header content.

● Required headers: `x-Product`, `x-api-key`, `Authorization`

● Optional headers: `X-Product-Location`, `X-Request-Id`

<a id="url-parameters"></a>
### URL parameters

| Parameter | Description | 
| ------------ | ------------- |
| ids | Comma-separated list of file IDs, e.g. "`ids=100,101`". Maximum number of IDs is 110. Exceeding this limit triggers an error. String. | 
| locale | Optional. Location language code for the API to use when returning localized messages. The API can usually get the user's default locale through the Authorization header. This value overrides that or provides a locale if not available through Authorization. String.<br><br>Default is en-US. See the full list of[  Locales](https://www.adobe.io/apis/creativecloud/stock/docs.html#!adobe/stock-api-docs/master/docs/api/14-locale-codes.md). | 
| result_columns[] | Optional. Fields to include in response. Note that some fields (`is_licensed`) require an authorization token. Array[].<br><br>__Tip:__ To combine results, use this syntax: `result_columns[]=is_licensed&result_columns[]=creation_date`<br><br>By default, only `id` is returned. For the full list, see the [Search API reference](https://www.adobe.io/apis/creativecloud/stock/docs.html#!adobe/stock-api-docs/master/docs/api/11-search-reference.md). | 

<a id="responses"></a>
## Responses

The response for any given request is dependent on the value of the `result_columns` argument. As mentioned, the only field returned by default is `id` (Stock asset ID, integer).

For a complete list of return fields, see the [Search API reference](https://www.adobe.io/apis/creativecloud/stock/docs.html#!adobe/stock-api-docs/master/docs/api/11-search-reference.md).

<a id="example-requests-and-responses"></a>
## Example requests and responses

The basic use of the API (without setting result columns) only returns the list of IDs without additional metadata.

```http
GET /Rest/Media/1/Files?ids=12345678,57185897,94682947,180905406,175119903,113187776,120451263,117684952,84666330,70910021,89866754,97126353 HTTP/1.1
Host: stock.adobe.io
X-Product: MySampleApp/1.0
x-api-key: MyApiKey
```

Note that in the response, ID "12345678" does not exist because it is not returned by the API. This is expected behavior. Rather than throw an error for missing assets, it excludes them from the results. This way, the entire request does not fail.

```javascript
{
    "files": [
        {
            "id": 57185897
        },
        {
            "id": 94682947
        },
        {
            "id": 180905406
        },
        {
            "id": 175119903
        },
        {
            "id": 113187776
        },
        {
            "id": 120451263
        }
    ]
}
```

This example requests the number of results (`nb_results`) as well as additional fields.

```http
GET /Rest/Media/1/Files/?ids=77625150,117356939&result_columns[]=nb_results&result_columns[]=id&result_columns[]=thumbnail_url HTTP/1.1
Host: stock.adobe.io
X-Product: MySampleApp/1.0
x-api-key: MyApiKey
```

```javascript
{
  "nb_results": 2,
  "files": [
    {
      "id": 77625150,
      "thumbnail_url": "https://as2.ftcdn.net/jpg/00/77/62/51/500_F_77625150_JCiDBzNWtLXnyuXROMgpquQ9NS64OTbY.jpg"
    },
    {
      "id": 117356939,
      "thumbnail_url": "https://as2.ftcdn.net/jpg/01/17/35/69/500_F_117356939_aJF1d2FMSQdrZ2M5m1aY1gjOcXbItMvJ.jpg"
    }
  ]
}
```

<a id="error-handling"></a>
## Error handling

If any error is encountered, the response will be a JSON object containing the key `error` with a textual description of the error and the key `code` with a numeric identifier for the error.

| HTTP Status | JSON Code | JSON Message |
|------------|------------|-------------|
| 400 | 600 | The required X-Product header is missing or invalid |
| 400 | 601 | Authorization required when requesting `is_licensed` field |
| 400 | 602 | Invalid locale supplied |
| 400 | 603 | Invalid value in result_columns supplied |
| 400 | 604 | Unrecognized query parameter received. |
| 404 | 605 | Unknown id in supplied `ids` parameter |
| 400 | 606 | More than 110 IDs supplied for `ids` parameter |

<a id="example-invalid-requests-and-error-responses"></a>
### Example invalid requests and error responses

```javascript
curl --header 'X-Api-Key: MyApiKey' 
  https://stock.adobe.io/Rest/Media/1/Files/?ids=100

{"error":"The required X-Product header is missing or invalid","code":"600","case":"ab4a0f466ed85a838853dae159edee6c"}
```

```javascript
curl --header 'X-Api-Key: MyApiKey' 
  --header 'X-Product: MySampleApp/1.0' 
  https://stock.adobe.io/Rest/Media/1/Files/?ids=a, 100

{"error":"Required \"ids\" parameter is missing or invalid","code":"605","case":"01f49fa990a7579aa69b6fd232504c71"}
```
