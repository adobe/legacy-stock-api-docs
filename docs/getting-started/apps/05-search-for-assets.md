# Search for assets

_**Tl;dr version:** Convert your app requirements into commands and build your search query using the Search API reference._

<!-- MarkdownTOC -->

- [Practical search example](#practical-search-example)
- [Tips and techniques](#tips-and-techniques)
  - [Paginating results](#paginating-results)
    - [Pagination example](#pagination-example)
  - [Change the order of your results](#change-the-order-of-your-results)
  - [Similarity \(visual\) search](#similarity-visual-search)
    - [Image similarity POST example](#image-similarity-post-example)
    - [Combining visual search with other filters](#combining-visual-search-with-other-filters)
  - [Jump to a search results page on Adobe Stock](#jump-to-a-search-results-page-on-adobe-stock)
    - [Search by keyword](#search-by-keyword)
    - [Filter by photos, vectors, video, etc.](#filter-by-photos-vectors-video-etc)
    - [Search on standard or Premium content](#search-on-standard-or-premium-content)
    - [Important: Get tracking credit](#important-get-tracking-credit)

<!-- /MarkdownTOC -->

To perform a search, you will call to the Search/Files endpoint and supply one or more search_parameter commands, which form the heart of your search query. Your search URL can perform three tasks:

*   **Specify a search type.** Choose whether to search on keywords, image similarity, or search on a particular asset ID, creator ID, etc.
*   **Filter values.** By default, the query will return all types of assets that can be found on the [Adobe Stock](https://stock.adobe.com/) website, e.g., images, videos, templates, 3D, etc. Using filters, you can choose to search only on certain asset types (e.g., images) and subtypes (e.g., vector images), or only assets that have certain characteristics (e.g., images that are horizontal and greater than 20 megapixels).
*   **Set response fields.** The Search API will only return a default set of fields in the JSON response unless you override this behavior and tell it which fields you want. There are many more fields available than what is returned by default, for performance reasons.

## Practical search example

You have been asked to create a simple web page that lets a user enter a word and show them the most-downloaded photo result along with a title. Furthermore, you want the image to be displayed at 800 pixels size, and have a clickable link that takes the user to a page on the Adobe Stock website where the image can be licensed.

This example searches for the keyword "dogs" and limits the results to 1. 

```
https://stock.adobe.io/Rest/Media/1/Search/Files?locale=en_US&search_parameters[words]=dogs&search_parameters[limit]=1
```

This URL won't work as is, because it lacks the headers we created earlier. When complete, it would look like this in HTTP format:


```http
GET /Rest/Media/1/Search/Files?locale=en_US
&search_parameters[words]=dogs&search_parameters[limit]=1 HTTP/1.1
Host: stock.adobe.io
X-Product: MySampleApp/1.0
X-API-Key: YourApiKeyHere
```

And like this using the [curl](https://curl.haxx.se/) format. Curls are convenient because they can be run from a terminal/command line program. Just be sure to remove the extra line breaks and substitute "YourAPIKeyHere" placeholder text with your own values.


```shell
curl "https://stock.adobe.io/Rest/Media/1/Search/Files?locale=en_US
    &search_parameters%5Bwords%5D=dogs&search_parameters%5Blimit%5D=1" \
  -H "x-api-key: YourApiKeyHere" \
  -H "x-product: MySampleApp/1.0"
```


Note that the square brackets **[ ]** have been [URL-encoded](https://en.wikipedia.org/wiki/Url_encoding) as **%5B** and **%5D**, respectively. The curl application has issues processing these characters (and they should be not be sent in plain text over HTTP), so it is recommended to encode them to web-safe entities.

If you supplied a valid API key, you should see something like this. The first line (`nb_results`) tells you that it found 930,400 results, and results are contained in a `files[]` array.


```json
{
    "nb_results": 930400,
    "files": [
        {
            "id": 86760419,
            "title": "German Shepherd Dog Sticking Head Out Driving Car Window",
            "width": 3454,
            "height": 2303,
            "creator_name": "Christin Lola",
            "creator_id": 204004289,
            "thumbnail_url": "https://as1.ftcdn.net/jpg/00/86/76/04/500_F_86760419_NEhOeuriYu82RwfgDqjTeIL9yx7ih5iv.jpg",
            "thumbnail_html_tag": "<img src=\"https://as1.ftcdn.net/jpg/00/86/76/04/500_F_86760419_NEhOeuriYu82RwfgDqjTeIL9yx7ih5iv.jpg\" alt=\"German Shepherd Dog Sticking Head Out Driving Car Window\" title=\"Photo: German Shepherd Dog Sticking Head Out Driving Car Window\" zoom_ratio=\"1.4692685255\" zoom_depth_max=\"2\" />",
            "thumbnail_width": 500,
            "thumbnail_height": 334,
            "media_type_id": 1,
            "vector_type": null,
            "content_type": "image/jpeg",
            "category": {
                "id": 47,
                "name": "Dogs"
            },
            "stock_id": "NEhOeuriYu82RwfgDqjTeIL9yx7ih5iv",
            "premium_level_id": 0
        }
    ]
}
```


All valid responses from the Stock API are returned as JSON, as seen above. Note the JSON has been formatted for easier reading--if you are working from the command line, it's recommended you use a service like the [Online JavaScript Beautifier](http://jsbeautifier.org/) to clean up the results, or better yet, use a dedicated API testing app like [Postman](https://www.getpostman.com/), which makes the work simple.

So far, we have used two of the three abilities of the Search API:


*   We chose a search type (words), and set it to search on a keyword ("dogs").
*   We told it to filter (limit) the results to one asset.

For our simple application, we don't need all that extra metadata--we only need a few fields. Furthermore, we want some data that is not present, and also limit our search to photos, in order of popularity. 

After consulting the Search API reference, let's break down these extra requirements into additional API commands:


*   Only show photos

```
    search_parameters[filters][content_type:photo]=1
```


*   Sort by most-downloaded

```
    search_parameters[order]=nb_downloads
```


And we'll use the third ability of the Search API to choose only the result fields (`result_columns`) we want for our app. If you look at the documentation for result_columns, you'll see that only one of the fields is returned by default (`title`). You can add as many result_columns to your URL as you need:


*   Get the title

```
    result_columns[]=title
```


*   Get a link that goes to the Adobe Stock page for that image

```
    result_columns[]=details_url
```


*   Because the default image result is 500, we need a larger size for our 800px requirement. So we'll get the 1000px thumbnail instead:

```
    result_columns[]=thumbnail_1000_url
```


Here is the final URL before encoding and adding headers:

```http
    https://stock.adobe.io/Rest/Media/1/Search/Files?locale=en_US&search_parameters[words]=dogs&search_parameters[limit]=1&search_parameters[filters][content_type:photo]=1&search_parameters[order]=nb_downloads&result_columns[]=title&result_columns[]=details_url&result_columns[]=thumbnail_1000_url
```

Let's test it out with a new curl (remember to plug in your API key):


```shell
curl "https://stock.adobe.io/Rest/Media/1/Search/Files?locale=en_US
&search_parameters%5Bwords%5D=dogs&search_parameters%5Blimit%5D=1&search_parameters%5Bfilters%5D%5Bcontent_type%3Aphoto%5D=1&search_parameters%5Border%5D=nb_downloads&result_columns%5B%5D=title&result_columns%5B%5D=details_url&result_columns%5B%5D=thumbnail_1000_url" \
  -H "x-api-key: YourApiKeyHere" \
  -H "x-product: MySampleApp/1.0"
```


And get back our filtered results:


```json
{
    "files": [
        {
            "title": "Young puppy listening to music on a head set.",
            "details_url": "https://stock.adobe.com/5684305?as_channel=affiliate&as_source=api&as_content=cfc3d3bd68784b8cbeec1ad707c2aecb",
            "thumbnail_1000_url": "https://as2.ftcdn.net/jpg/00/05/68/43/1000_F_5684305_J1w2x70BL9gKf9EI5X9FRE2DPyXQDUPM.jpg"
        }
    ]
}
```


Since the Adobe Stock collection is constantly evolving, the result you get may be very different, but you will get back something. Now let's create a simple HTML snippet for our "application" and we're done. See the awesome results <a href="https://cfsdemos.worldsecuresystems.com/stock/api/simple_app_result.html" target="_blank">here</a>! 


```html
<h1>Young puppy listening to music on a head set.</h1>
<a href="https://stock.adobe.com/5684305?as_channel=affiliate&amp;as_source=api&amp;as_content=cfc3d3bd68784b8cbeec1ad707c2aecb">
  <img style="width:800px; height:auto" src="https://as2.ftcdn.net/jpg/00/05/68/43/1000_F_5684305_J1w2x70BL9gKf9EI5X9FRE2DPyXQDUPM.jpg">
</a>
```

__>>> NEXT:__ Review the [tips and techniques](#tips-and-techniques) below, or continue ahead to learn how to use the [Licensing API](./06-licensing-assets.md)!

## Tips and techniques

### Paginating results

Although a search request might find thousands of matching assets, search results include only a maximum of 64 for each call (and 32 results by default). If your results page layout displays a certain quantity of assets, you can limit each call to returning that quantity, and then call Search again to get the next set. 

To do this:


1.  On your search call, use the `limit` parameter to control the number of results to return with each call.
    *   If this is the initial call to Search, set the `offset` parameter to zero (0). 
1.  Display your first page of results by parsing the JSON response. 
1.  If your user requests another page of results, add the `limit` value to the `offset` attribute and repeat the two preceding steps.

#### Pagination example

Request first 16 results

```
https://stock.adobe.io/Rest/Media/1/Search/Files?locale=en_US&search_parameters[words]=dogs&search_parameters[limit]=16&search_parameters[offset]=0
```

Get next 16 results

```
https://stock.adobe.io/Rest/Media/1/Search/Files?locale=en_US&search_parameters[words]=dogs&search_parameters[limit]=16&search_parameters[offset]=16
```

### Change the order of your results

By default, Search returns assets sorted in descending order by how closely they match your search and filtering requirements.  You can change that order with `search_parameters[order]`.

Valid orders and their meanings:

*   `relevance`: How closely it matches your search request, closest matches first (default). 
*   `creation`: Creation date in descending order (newest first).
*   `featured`: Attempts to display the highest quality content first, as scored by Adobe Sensei's machine learning algorithms. In practice, it performs best on lifestyle imagery.
*   `nb_downloads`: In descending order by the number of downloads by all users since the asset was added to Adobe Stock.
*   `undiscovered`: Starting with assets that have not commonly been viewed or downloaded.

Example:

```
https://stock.adobe.io/Rest/Media/1/Search/Files?locale=en_US&search_parameters[words]=dogs&search_parameters[order]=undiscovered
```

### Similarity (visual) search

One of the most useful and powerful capabilities of the Search API is to return results that are visually similar to a base image, and then filter further by adding additional attributes. This functionality is powered by the machine learning AI of [Adobe Sensei](http://www.adobe.com/sensei.html).

The Search API supports three types of visual search:

*   Similar to the image being uploaded.

```
    search_parameters[similar_image]=1&similar_image=<FILE>
```


*   Similar to an image URL.

```
    search_parameters[similar_url]=<URL>
```


*   Similar to an existing Stock ID.

```
    search_parameters[similar]=<ID>
```


#### Image similarity POST example

Searching on a similar URL or Stock ID works exactly like the previous search examples that use an HTTP GET, while comparing to an uploaded image requires a multipart form POST, and accepts JPG, PNG, or GIF files.


```
POST /Rest/Media/1/Search/Files?locale=en_US&search_parameters[similar_image]=1&search_parameters[filters][content_type:photo]=1 HTTP/1.1
Host: stock.adobe.io
X-Product: MySampleApp/1.0
X-API-Key: YourApiKeyHere
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW

------WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="similar_image"; filename="myImage.jpg"
Content-Type: image/jpeg

------WebKitFormBoundary7MA4YWxkTrZu0gW--
```


Stock API will return JSON results exactly as if you had searched on text or some other type.


#### Combining visual search with other filters

In addition to searching for results similar to the image you specify, you can narrow the results by adding keywords, colors and other qualifiers. This example searches for a public-domain image of a pumpkin using a `similar_url` search type, but adds a `colors` filter to find images that also have a dark shade of blue. You could add more colors to the query (the command accepts a comma-separated list of RGB hex values), but that might limit the search results too much. Note that the image URL in this example may expire, but you can use any valid image URL for your search.

```shell
curl "https://stock.adobe.io/Rest/Media/1/Search/Files?
search_parameters%5Bsimilar_url%5D=http%3A%2F%2Ftinyurl.com%2Fyc5kag79&search_parameters%5Bfilters%5D%5Bcontent_type=photo%5D%3A1&search_parameters%5Bfilters%5D%5Bcolors%5D=004358" \
  -H "x-api-key: YourApiKeyHere" \
  -H "x-product: MySampleApp/1.0"
```

### Jump to a search results page on Adobe Stock

The `details_url` will direct an end user to a page on the Adobe Stock website where they can license and see more information about _one_ asset, but what if you wanted to redirect the user to a page where they can view search results for _multiple_ assets? For example, if your website only shows the first few results from Adobe Stock, and then encourages users to "click for more" results. When the user is redirected to Adobe Stock from your link, they don't want to lose the context in which they were searching and have to start over--that would make for a frustrating experience.

The Adobe Stock Search API allows a client (desktop, web, or mobile) to launch the Adobe Stock website with specific search term(s). The website has robust functionality that you may not want to replicate on your own site using the Search API. There is a documented API reference for this functionality, but here are some quick examples to get started.

#### Search by keyword

Use the `k` parameter. Example:

```
https://stock.adobe.com/search?k=kittens
```

#### Filter by photos, vectors, video, etc.

Use these parameters:

*   Photos. 
    `filters[content_type:photo]=1`

*   Illustrations (bitmap).
    `filters[content_type:illustration]=1`

*   Vector images (Illustrator AI, EPS files).
    `filters[content_type:zip_vector]=1`

*   Videos.
    `filters[content_type:video]=1`

Example: Search for vector artwork of cats.

```
https://stock.adobe.com/search?k=cats&filters[content_type:zip_vector]=1
```

#### Search on standard or Premium content

Use the `price[_$_]` parameter:


*   Standard/core assets
    `price[$]=1`
*   Premium tier 1
    `price[$$]=1`
*   Premium tier 2
    `price[$$$]=1`

Example: Find Premium assets about stars

```
https://stock.adobe.com/search?k=stars&price[$$]=1&price[$$$]=1
```

#### Important: Get tracking credit

If you want your application to get referral credit for the search, meaning that you have joined the Adobe Partner/Affiliate program and receive a "bounty" for referral traffic, then make sure you add add the same parameters that are present on your details_url. See the discussion of using this data in the [practical search example](#practical-search-example), above. 

Example:

```
https://stock.adobe.com/search?k=kittens&as_channel=affiliate&as_source=api
&as_content=cfc3d3bd68784b8cbeec1ad707c2aecb
```
