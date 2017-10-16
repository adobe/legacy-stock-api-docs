# Stock Search Reference



*   [About Search and Filter Criteria](https://www.adobe.io/apis/creativecloud/stock/docs/api/api-summary.html#criteria)
*   [Stock Search Calls](https://www.adobe.io/apis/creativecloud/stock/docs/api/api-summary.html#calls)
    *   [Authentication](https://www.adobe.io/apis/creativecloud/stock/docs/api/api-summary.html#auth)
    *   [Request Headers](https://www.adobe.io/apis/creativecloud/stock/docs/api/api-summary.html#headers)
    *   [URL Parameters](https://www.adobe.io/apis/creativecloud/stock/docs/api/api-summary.html#params)
*   [Responses](https://www.adobe.io/apis/creativecloud/stock/docs/api/api-summary.html#responses)
*   [Example Return Comps Values](https://www.adobe.io/apis/creativecloud/stock/docs/api/api-summary.html#ex_comps)
*   [Example Queries and Responses](https://www.adobe.io/apis/creativecloud/stock/docs/api/api-summary.html#ex_query)
*   [Common Search Queries](https://www.adobe.io/apis/creativecloud/stock/docs/api/api-summary.html#ex_common)
*   [Error Codes](https://www.adobe.io/apis/creativecloud/stock/docs/api/api-summary.html#errors)
*   [More Information](https://www.adobe.io/apis/creativecloud/stock/docs/api/api-summary.html#info)

You can query Adobe Stock for assets that meet your specified search criteria. You can filter the results, specify the sort order in which the results are returned, and choose how many assets to return for each page of results.


## About Search and Filter Criteria

Search parameters have two formats:


### Search values

In general, search parameters identify asset information for values that cannot be predetermined, such as ID numbers or keywords. Parameters for searches look like this:

`search_parameters[*search_item*]=*value*`

For example:

`search_parameters[words]=dog big happy`

Search parameters are treated as AND. For example, you could combine [words] and [creator_id] to search for assets created by a specific creator that have the specified keywords.

__Tip:__ You must specify at least one [search_parameters] value for each Search.


### Filtering values

These optional qualifiers specify which of the found assets to return. In general, filters identify asset information that has a known set of values, such as true/false or file type. Parameters for filtering look like this:

`search_parameters[filters][*filter_item*]=*value*`

For example:

`search_parameters[filters][orientation]=horizontal`

You can specify multiple filtering values for content_type, template_type_id, and template_category_id; search returns assets that match any of those values.  The remaining filters are treated as AND.


## Stock Search Calls

A request using the Stock search API retrieves a list of assets from Adobe Stock that matches a set of search and filter values. A maximum of 64 assets can be returned from one request. This is a paginated interface that you can call multiple times to retrieve the full list.

For a guide to usage and additional examples, see [Creating Adobe Stock Applications](https://www.adobe.io/apis/creativecloud/stock/docs/createapps.html).


<table>
  <tr>
   <td>Endpoint
   </td>
   <td>https:/stock.adobe.io/Rest/Media/1/Search/Files
   </td>
  </tr>
  <tr>
   <td>Methods
   </td>
   <td>GET
<p>
POST (only when using the similar_image parameter)
   </td>
  </tr>
</table>



### Authentication

Not required. If you do not pass a valid bearer token in the Authorization header, you can search within Adobe Stock and access complementary versions of assets, but the API will not return licensing requirements for the assets. 

If you do pass a valid token, then the Adobe Stock service returns the license state and licensed URL for each asset. See [ Setting Up Access](https://www.adobe.io/apis/creativecloud/stock/docs/setup.html).  


### Request Headers

See[ Headers for Stock API Calls](https://www.adobe.io/apis/creativecloud/stock/docs/api/headers.html) for details about header content. 



*   Required headers: x-Product, x-api-key
*   Optional headers: Authorization (required to view license state), X-Product-Location, X-Request-Id


### URL parameters

Pass the following URL parameters with the GET request.

**Tip:** The only required parameter is at least one search_parameters[]. 


<table>
  <tr>
   <td><strong>Parameter</strong>
   </td>
   <td><strong>Description</strong>
   </td>
  </tr>
  <tr>
   <td>locale
   </td>
   <td>Location language code. String. Default is en-US.  See<a href="https://www.adobe.io/apis/creativecloud/stock/docs/api/locales.html"> Locales</a>.
   </td>
  </tr>
  <tr>
   <td>search_parameters[words]
   </td>
   <td><p>Keyword search. Space-separated list of keywords. String.
<p>
Words can also be individual media identifiers (media_id), for example:
<p>
<code>search_parameters[words]=71182279
   </td>
  </tr>
  <tr>
   <td>search_parameters[limit]
   </td>
   <td><p>Maximum number of assets to return in the call.  Valid values are 1 through 64. Default is 32. String.
<p>
Call repeatedly with different [offset] values to page through the found assets.
<p>
<strong>Tip:</strong> The number of images returned in each call can vary, but never exceeds 64 entries. 
   </td>
  </tr>
  <tr>
   <td>search_parameters[offset]
   </td>
   <td><p>Start position in query. Valid values are 0 (the first found asset) or higher integers. With each successive call for your search, increment this by the [limit] value to get the next page of assets.
<p>
For example, by default your first call uses a 0 offset and limit of 32 to return the first 32 found assets.  Call this API again with an offset of 32 to retrieve the next page. Integer. Default is 0.
   </td>
  </tr>
  <tr>      
   <td>search_parameters[order]
   </td>
   <td>Sort order in which to return found assets. Default is "relevance". String. 
<p>
Valid strings and their meanings:<ul>

<li><code>relevance</code></li>
How closely it matches your search request, closest matches first. 
<li><code>creation</code></li>
Creation date in descending order (newest first).
<li><code>popularity</code></li>
In descending order by the number of views by all users.
<li><code>nb_downloads</code></li>
In descending order by the number of downloads by all users since the asset was added to Adobe Stock.
<li><code>undiscovered</code></li>
Starting with assets that have not commonly been viewed or downloaded.</li></ul>

   </td>
  </tr>
  <tr>
   <td>search_parameters[creator_id]
   </td>
   <td>Search by a specific asset creator's ID. Integer.
   </td>
  </tr>
  <tr>
   <td>search_parameters[media_id]
   </td>
   <td>Search for one specific asset by its unique identifier (media_id). Integer.
   </td>
  </tr>
  <tr>
   <td>search_parameters[model_id]
   </td>
   <td>Search for assets that portray a specific person (model) using the model's ID. Integer.
   </td>
  </tr>
  <tr>
   <td>search_parameters[serie_id]
   </td>
   <td>Search for assets in the specified series using the series ID. Returns all assets that the creator has grouped into this single series. Integer.
   </td>
  </tr>
  <tr>
   <td>search_parameters[similar]
   </td>
   <td><p>Search for assets that are similar in appearance to an asset with a specific media ID. Integer. For example:
<p>
<code>search_parameters[similar]=99338
   </td>
  </tr>
  <tr>
   <td>search_parameters[similar_url]
   </td>
   <td><p>Search for assets that are similar in appearance to an image at a specific URL. String. For example:
<p>
<code>search_parameters[similar_url]=http://my.site.com/images/cutedog.jpg
   </td>
  </tr>
  <tr>
   <td>search_parameters[similar_image]
   </td>
   <td>Whether to use similar_image data for visual similarity search. Integer.
<p>
0 or 1 (if using image data).
   </td>
  </tr>
  <tr>
   <td>similar_image
   </td>
   <td><p>Image data to use when searching for visually similar assets. Must also specify:
<p>
<code>search_parameters[similar_image]=1</code>
<p>
Supported in POST only. Valid image data is for JPG, PNG, or GIF files. Use multipart/form-data. 
<p>
Ignored if <code>search_parameters[similar_url]</code> is specified.  
   </td>
  </tr>
  <tr>
   <td>search_parameters[category]
   </td>
   <td>Search for assets with a specific category ID. Integer.
<p>
For example, to search for assets in the category "travel":
<p>
<code>search_parameters[category]=1043</code>
   </td>
  </tr>
  <tr>
   <td>search_parameters[thumbnail_size]
<p>
 
   </td>
   <td><p>Thumbnail size in pixels. Specify the size of thumbnail to return for each found asset. Integer.
<p>
Valid values and meanings:

<ul>
<li><code>110</code>: Small (110 px)
<li><code>160</code>: Medium (160 px)
<li><code>220</code>: Large (220 px)
<li><code>240</code>: Large (240 px)
<li><code>500</code>: Extra large (XL) (500 px). Returned with watermark. (default)
<li><code>1000</code>: Extra-extra large (XXL) (1000 px). Returned with watermark.</li>
</ul>

   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][area_pixels]
   </td>
   <td>Image sizes in pixels to return, specified as a range in the format "<em>min-max</em>". <em>min</em> and <em>max</em> are both optional and default to open ranges.
<p>
Examples:
<p>
<code>search_parameters[filters][area_pixels]:10000000-25010000</code>
<p>
<code>search_parameters[filters][area_pixels]:-2000000</code>
   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][premium]
   </td>
   <td><p>Whether to return Premium assets or not. Possible values:
   <ul>
   <li><code>false</code>: only return assets with a premium level of either 0 (core) or 1 (free). 
   <li><code>true</code>: only return assets with a premium level > 1. 
   <li><code>all</code>: Return everything. String.
   </ul>
   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][3d_type_id][]
   </td>
   <td>A multiple-value array specifying which 3D types to return. Valid values and meanings:<ul>

<li>1 - Models
<li>2 - Lights
<li>3 - Materials</li></ul>

   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][template_type_id][]
   </td>
   <td>Return found templates (starter files) for PSD or  AI only if they are of the specified type. A multiple-value array specifying which template types to return.  
<p>
Valid values and meanings:<ul>

<li>1 - PSDT
<li>2  - AIT</li></ul>

<p>
For example:
<p>
<code>search_parameters[filters][template_type_id][]=1

   </td>
  </tr>
<!--  
  <tr>
   <td>search_parameters[filters][template_category_id][]
   </td>
   <td>Return found templates (starter files) for PSD or AI only if they are of the specified template category. Multiple-value array specifying which template categories to return.
<p>
Valid values and meanings:<ul>

<li>1 - mobile
<li>2 - web
<li>3 - print
<li>4 - photo
<li>5 - film
<li>6 - art</li></ul>

   </td>
  </tr>
 -->
  <tr>
   <td>search_parameters[filters][has_releases]
   </td>
   <td>Return found assets only if the  asset has model or property releases. String.
<p>
Valid values and meanings:<ul>

<li><code>true</code>: Return only assets with releases.
<li><code>false</code>: Return only assets without releases. 
<li><code>all</code>: (Default.) Return assets regardless of release status.</li></ul>

   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][content_type:photo]
   </td>
   <td>Include found assets that are photos. Integer.
<p>
0 or 1. 
   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][content_type:illustration]
   </td>
   <td>Include found assets that are illustrations. Integer.
<p>
0 or 1. 
   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][content_type:vector]
   </td>
   <td>Include found assets that are vectors. Integer.
<p>
0 or 1.
   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][content_type:video]
   </td>
   <td>Include found assets that are videos. Integer.
<p>
0 or 1.
   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][content_type:template]
   </td>
   <td>Include found assets that are templates. Integer.
<p>
0 or 1.
   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][content_type:3d]
   </td>
   <td>Include found assets that are 3D items. Integer.
<p>
0 or 1.
   </td>
  </tr>
<!-- 
  <tr>
   <td>search_parameters[filters][content_type:all]
   </td>
   <td>Include found assets of all content_types.  Integer.<ul>

<li>0  - Return only content types specifically set to 1.
<li>1  - Default. Return all content types except those that are specifically set to 0. </li></ul>

   </td>
  </tr>
 -->
  <tr>
   <td>search_parameters[filters][offensive:2]
   </td>
   <td>Return found assets only if they are flagged as including Explicit/Nudity/Violence. Integer.
<p>
Valid values and meanings:<ul>

<li>0 - Default. Omit assets in this group.
<li>1 - Return assets only if they are in this group.</li></ul>

   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][isolated:on]
   </td>
   <td>Return found assets only if the subject is isolated from the background by being on a uniformly colored background. Integer.<ul>

<li>0 - Default. Omit assets that are isolated.
<li>1 - Return assets only if they are isolated.</li></ul>

   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][panoramic:on]
   </td>
   <td>Return found assets only if they are panoramic (can be combined with [orientation]). Integer.<ul>

<li>0 - Default. Omit panoramic assets.
<li>1 -Return assets only if they are panoramic.</li></ul>

   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][orientation]
   </td>
   <td>Return found assets of the specified orientation. String. Valid values and meanings:<ul>

<!-- START HERE: format like above examples -->

<li>horizontal - Only horizontal images
<li>vertical - Only vertical images
<li>square - Only square images
<li>all - Default. All image orientations </li></ul>

   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][age]
   </td>
   <td>Return found assets of the specified age (weeks, months, years). String.
<p>
Valid values and meanings:<ul>

<li>1w: Only assets up to 1 week old
<li>1m: Only assets up to 1 month old
<li>6m: Only assets up to 6 months old
<li>1y: Only assets up to 1 year old
<li>2y: Only assets up to 2 years old
<li>all: Default. Assets of any age</li></ul>

   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][video_duration]
   </td>
   <td>Return found videos whose duration is no longer than the specified duration in seconds. String.
<p>
Valid values and meanings:<ul>

<li>10: Only videos up to 10 seconds
<li>20: Only videos up to 20 seconds
<li>30:  Only videos up to 30 seconds
<li>30-: Only videos longer than 30 seconds
<li>all: Videos of all durations (default)</li></ul>

   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][colors]
   </td>
   <td>Comma-separated list of hexadecimal colors (without any # prefix). Return only found assets that contain the specified colors. String.
   </td>
  </tr>
  <tr>
   <td>search_parameters[gallery_id]
   </td>
   <td>Returns members of the specified collection or lightbox. String.
   </td>
  </tr>
  <tr>
   <td>result_columns[]
   </td>
   <td>Fields to include in the search result. (is_licensed requires authentication). Array[].
<p>
<strong>Tip:</strong> To pass results_columns, use this syntax: &result_columns[]=is_licensed&
<p>
result_columns[]=creation_date
<p>
<strong>Note: </strong>Fields marked with * are returned by default.
<p>
*nb_results
<p>
*id
<p>
*title
<p>
*creator_name
<p>
*creator_id
<p>
country_name
<p>
*width
<p>
*height
<p>
*thumbnail_url
<p>
*thumbnail_html_tag
<p>
*thumbnail_width
<p>
*thumbnail_height
<p>
thumbnail_110_url
<p>
thumbnail_110_width
<p>
thumbnail_110_height
<p>
thumbnail_160_url
<p>
thumbnail_160_width
<p>
thumbnail_160_height
<p>
thumbnail_220_url
<p>
thumbnail_220_width
<p>
thumbnail_220_height
<p>
thumbnail_240_url
<p>
thumbnail_240_width
<p>
thumbnail_240_height
<p>
thumbnail_500_url
<p>
thumbnail_500_width
<p>
thumbnail_500_height
<p>
thumbnail_1000_url
<p>
thumbnail_1000_width
<p>
thumbnail_1000_height
<p>
*media_type_id
<p>
*category
<p>
*category_hierarchy
<p>
nb_views
<p>
nb_downloads
<p>
creation_date
<p>
keywords
<p>
has_releases
<p>
comp_url
<p>
comp_width
<p>
comp_height
<p>
is_licensed
<p>
*vector_type
<p>
*content_type
<p>
framerate
<p>
duration
<p>
comps
<p>
details_url
<p>
template_type_id
<p>
template_category_ids
<p>
marketing_text
<p>
description
<p>
size_bytes
<p>
*premium_level_id
<p>
is_premium
<p>
licenses
<p>
video_preview_url
<p>
video_preview_width
<p>
video_preview_height
<p>
video_preview_content_length
<p>
video_preview_content_type
<p>
video_small_preview_url
<p>
video_small_preview_width
<p>
video_small_preview_height
<p>
video_small_preview_content_length
<p>
video_small_preview_content_type
   </td>
  </tr>
</table>



## Responses

The Adobe Stock service returns information about all found assets that also match the filtering criteria.

**Tip: **Assets on Adobe Stock can be added, changed, or removed by other parties between your API calls. Therefore, the total number of matching assets and the selection of assets can change between successive calls to Search.

**Note: **The response is in a JSON array with this general structure:

{"nb_results" : value
    "files": [
     { details_for_first_file ...
          "category": {       
               ...   }   
     },
     { details_for_second_file   ...
          "category": {       
               ...   }       
     }
  ]
}


<table>
  <tr>
   <td><strong>Name</strong>
   </td>
   <td><strong>Description</strong>
   </td>
  </tr>
  <tr>
   <td>nb_results
   </td>
   <td>Total number of found assets in the search result. Integer.
   </td>
  </tr>
  <tr>
   <td>id
   </td>
   <td>Asset's unique identifier. Integer.
   </td>
  </tr>
  <tr>
   <td>title
   </td>
   <td>Asset's title. String.
   </td>
  </tr>
  <tr>
   <td>creator_id
   </td>
   <td>Unique identifier for the asset's creator. Integer.
   </td>
  </tr>
  <tr>
   <td>creator_name
   </td>
   <td>Asset creator's name. String.
   </td>
  </tr>
  <tr>
   <td>creation_date
   </td>
   <td>Date on which the asset was created. Date.
   </td>
  </tr>
  <tr>
   <td>country_name
   </td>
   <td>Country in which the asset's creator lives. String.
   </td>
  </tr>
  <tr>
   <td>thumbnail_url
   </td>
   <td>URL for the default-sized asset thumbnail. You can use this to display the thumbnail on your page using your preferred display method. Alternatively, use thumbnail_html_tag. String.
   </td>
  </tr>
  <tr>
   <td>thumbnail_html_tag
   </td>
   <td>HTML <img> tag that you can use to display the default asset thumbnail. This is a convenience for displaying the thumbnail and references the thumbnail_url.  String.
<p>
For example:
<p>
"thumbnail_html_tag": "<img src="https://as1.ftcdn.net/jpg/00/86/76/04/
<p>
110_F_86760419_NEhOeuriYu82RwfgDqjTeIL9yx7ih5iv.jpg" alt="German Shepherd Dog Sticking Head Out Driving Car Window"
<p>
title="Photo: German Shepherd Dog Sticking
<p>
Head Out Driving Car Window" />",
   </td>
  </tr>
  <tr>
   <td>thumbnail_width
   </td>
   <td>Thumbnail's width in pixels. Integer.
   </td>
  </tr>
  <tr>
   <td>thumbnail_height
   </td>
   <td>Thumbnail's height in pixels. Integer.
   </td>
  </tr>
  <tr>
   <td>thumbnail_*_width
   </td>
   <td>Width for the thumbnail of the requested size, where * is the thumbnail size in pixels.  Float. For example:
<p>
"thumbnail_150_width":200
   </td>
  </tr>
  <tr>
   <td>thumbnail_*_height
   </td>
   <td>Height for the thumbnail of the requested size, where * is the thumbnail size in pixels. Integer.
   </td>
  </tr>
  <tr>
   <td>thumbnail_*_url
   </td>
   <td>URL for the requested of the requested size, where * is the thumbnail size in pixels. You can use this to display the thumbnail on your page using your preferred display method. Alternatively, use thumbnail_*_html_tag.  String.
   </td>
  </tr>
  <tr>
   <td>thumbnail_*_html_tag
   </td>
   <td>HTML  <img> tag that you can use to display the thumbnail of the requested size, where  where * is the thumbnail size in pixels. This is a convenience for displaying the thumbnail and references the thumbnail_*_url.  String.
   </td>
  </tr>
  <tr>
   <td>width
   </td>
   <td>Width in pixels of the full-sized original asset. Integer.
   </td>
  </tr>
  <tr>
   <td>height
   </td>
   <td>Height in pixels of the full-sized original asset. Integer.
   </td>
  </tr>
  <tr>
   <td>is_licensed
   </td>
   <td>The Adobe Stock licensing state for the asset. String. Values and meaning:<ul>

<li>Standard – License for the full-resolution asset
<li>Extended – Extended license
<li>Video_HD – Video HD license
<li>Video_4K – Video 4K license
<li>Standard_M – License for a medium-size asset approximately 1600x1200 pixels
<li><em>empty string</em> - No license applies</li></ul>

   </td>
  </tr>
  <tr>
   <td>comp_url
   </td>
   <td>URL to the watermarked version of the asset. String.
   </td>
  </tr>
  <tr>
   <td>comp_width
   </td>
   <td>Width in pixels of the asset's complementary (unlicensed) image. Integer.
   </td>
  </tr>
  <tr>
   <td>comp_height
   </td>
   <td>Height in pixels of the asset's complementary (unlicensed) image. Integer.
   </td>
  </tr>
  <tr>
   <td>nb_views
   </td>
   <td>Total views of the asset by all users. Integer.
   </td>
  </tr>
  <tr>
   <td>nb_downloads
   </td>
   <td>Total downloads of the asset by all users. Integer.
   </td>
  </tr>
  <tr>
   <td>category
   </td>
   <td>JSON structure with information about the category assigned to the asset.
<p>
"category": {
<p>
      "id": <em>nnnnn</em>,
<p>
      "name":"..." }
<p>
For example: 
<p>
"category": {  "id": 47,  "name": "Dogs"}
   </td>
  </tr>
  <tr>
   <td>category { ... id ...}
   </td>
   <td>Identifier for the category assigned to the asset. Integer. 
<p>
You can pass the category identifier to the Category or CategoryTree methods to get localized category information. 
   </td>
  </tr>
  <tr>
   <td>category{ ... name ...}
   </td>
   <td>Localised name of the asset's category. String.
   </td>
  </tr>
  <tr>
   <td>keywords
   </td>
   <td>List of localised keywords for the asset. Array.
   </td>
  </tr>
  <tr>
   <td>media_type_id
   </td>
   <td>Type of the asset. Integer.<ul>

<li>1 - photos
<li>2 - illustrations
<li>3 - vectors
<li>4 - videos</li></ul>

   </td>
  </tr>
  <tr>
   <td>vector_type
   </td>
   <td>If the asset is a vector, this indicates whether it is an SVG or an AI/EPS asset. String. Values and meanings:<ul>

<li>svg - SVG file
<li>zip - AI/EPS file</li></ul>

   </td>
  </tr>
  <tr>
   <td>content-type
   </td>
   <td>Mime type of the asset's content. String. For example:
<p>
"content_type": "image/jpeg"
   </td>
  </tr>
  <tr>
   <td>framerate
   </td>
   <td>Frame rate for the asset if it is a video. Float.
   </td>
  </tr>
  <tr>
   <td>duration
   </td>
   <td>Duration in milliseconds of the asset if it is a video. Integer.
   </td>
  </tr>
  <tr>
   <td>comps
   </td>
   <td>JSON object that contains one or more of the following properties for complementary assets if applicable: Standard, Video_HD, or Video_4K. The properties contain width, height, comp URL.  See<a href="https://www.adobe.io/apis/creativecloud/stock/docs/api/api-summary.html#ex_comps"> Example Returned Comps Values</a>.
   </td>
  </tr>
  <tr>
   <td>details_url
   </td>
   <td>URL to the Adobe Stock details page for the asset. If you pass the Authorization header with the call, Adobe Stock generates an SSO jump URL. String. 
   </td>
  </tr>
  <tr>
   <td>3d_type_id
   </td>
   <td>The ID of the 3D type, if the return asset is 3D. Values and meanings:<ul>

<li>1 - Models
<li>2 - Lights
<li>3 - Materials</li></ul>

   </td>
  </tr>
  <tr>
   <td>template_type_id
   </td>
   <td>The ID of the template type, if the returned asset is a template. Integer. Values and meanings:<ul>

<li>1 - PSDT
<li>2 - AIT</li></ul>

   </td>
  </tr>
  <tr>
   <td>template_category_ids
   </td>
   <td>An array of template category identifiers for the asset if the asset is a template (1 = mobile, 2 = web, 3 = print, 4 = photo, 5 = film, 6 = art).
   </td>
  </tr>
  <tr>
   <td>marketing_text
   </td>
   <td>Marketing text for the template in<a href="http://daringfireball.net/projects/markdown/"> Markdown format</a>, if the found asset is a template.  String.
   </td>
  </tr>
  <tr>
   <td>description
   </td>
   <td>Description text for the template in<a href="http://daringfireball.net/projects/markdown/"> Markdown format</a>,  if the found asset is a template. String.
   </td>
  </tr>
  <tr>
   <td>size_bytes
   </td>
   <td>Size of the template file in bytes, if the found asset is a template. Integer.
   </td>
  </tr>
  <tr>
   <td>premium_level_id
   </td>
   <td>Asset's premium (pricing) level. Integer.
<p>
0 - core
<p>
1 - free
<p>
2 - premium 1
<p>
3 – premium 2
<p>
4 – premium 3
   </td>
  </tr>
</table>



## Example Returned Comps Values

Image:

     "comps": {
        "Standard": {
          "url": "https://stock.adobe.io/Rest/Libraries/Watermarked/Download/76203302/1",
          "width": 1000,
          "height": 248
        }
      }

Vector:

    "comps": {
        "Standard": {
          "url": "https://stock.adobe.io/Rest/Libraries/Watermarked/Download/47788193/1",
          "width": 770,
          "height": 1000
        }
      }

Video that is available in 4K or HD:

     "comps": {
        "Video_HD": {
          "url": "https://stock.adobe.io/Rest/Libraries/Watermarked/Download/106945684/3",
          "width": 1920,
          "height": 1080
        },
        "Video_4K": {
          "url": "https://stock.adobe.io/Rest/Libraries/Watermarked/Download/106945684/4",
          "width": 3840,
          "height": 2160
        }
      }

Video that is in HD only:

     "comps": {
        "Video_HD": {
          "url": "https://stock.adobe.io/Rest/Libraries/Watermarked/Download/109410569/3",
          "width": 1920,
          "height": 1080
        }
      }


## Example Queries and Responses

This example searches for assets that have the keyword "dog" and returns no more than the first two matches.

GET https://stock.adobe.io/Rest/Media/1/Search/Files?locale=en-US&search_parameters%5Bwords%5D=dogs&search_parameters%5Blimit%5D=2
------------------------- headers --------------------------
X-Product: Photoshop/15.2.0
X-Product-Location: Libraries/1.0.0
x-api-key: myAPIkey

The preceding request returns two asset descriptions. nb_results shows that 399,884 assets currently match the keyword "dog".

{  "nb_results": 399884,
 "files": [
   {
     "id": 86760419,
     "title": "German Shepherd Dog Sticking Head Out Driving Car Window",
     "width": 3454,
     "height": 2303,
     "creator_name": "Christin Lola",
     "creator_id": 204004289,
     "thumbnail_url": 
                       "https://as1.ftcdn.net/jpg/00/86/76/04/110_F_86760419_NEhOeuriYu82RwfgDqjTeIL9yx7ih5iv.jpg",  
     "thumbnail_html_tag": 
             "<img src="https://as1.ftcdn.net/jpg/00/86/76/04/110_F_86760419_NEhOeuriYu82RwfgDqjTeIL9yx7ih5iv.jpg" 
              alt="German Shepherd Dog Sticking Head Out Driving Car Window" 
              title="Photo: German Shepherd Dog Sticking Head Out Driving Car Window" />",
     "thumbnail_width": 110,
     "thumbnail_height": 73,
     "media_type_id": 1,
     "vector_type": null,
     "category": {
       "id": 47,
       "name": "Dogs"
     }
   },
   {
     "id": 84977202,
     "title": "Happy dog playing outside and carrying the American flag",
     "width": 5616,
     "height": 3744,
     "creator_name": "SSilver",
     "creator_id": 200407313,
     "thumbnail_url":    "https://as1.ftcdn.net/jpg/00/84/97/72/110_F_84977202_JplQMoMQ5QiZCgVeWLwKhFHCrr4HG99Q.jpg",
     "thumbnail_html_tag":    
            "<img src="https://as1.ftcdn.net/jpg/00/84/97/72/110_F_84977202_JplQMoMQ5QiZCgVeWLwKhFHCrr4HG99Q.jpg"    
             alt="Happy dog playing outside and carrying the American flag" 
             title="Photo: Happy dog playing outside and carrying the American flag" />",
     "thumbnail_width": 110,
     "thumbnail_height": 73,
     "media_type_id": 1,
     "vector_type": null,
     "category": {
       "id": 47,
       "name": "Dogs"
     }
   }
 ]




### Common Search Queries

Here are simple examples of common searches.



*   Keyword search; assets matching "purple" and "clouds":

GET https://stock.adobe.io/Rest/Media/1/Search/Files?search_parameters[words]=purple clouds



*   Category search; assets in category 1043:

GET https://stock.adobe.io/Rest/Media/1/Search/Files?search_parameters[category]=1043



*   Search for assets similar in appearance to the specified asset ID:

GET https://stock.adobe.io/Rest/Media/1/Search/Files?search_parameters[similar]=121353611



*   Search for assets similar in appearance to the specified URL:

GET https://stock.adobe.io/Rest/Media/1/Search/Files?search_parameters[similar_url]=https://i.kinja-img.com/gawker-media/image/upload/xqkbwkexcl7udc5va7pn.jpg



*   Similar asset by URL and keyword:

GET https://stock.adobe.io/Rest/Media/1/Search/Files?search_parameters[similar_url]=https://i.kinja-img.com/gawker-media/image/upload/xqkbwkexcl7udc5va7pn.jpg&search_parameters[words]=cats



*   Search for assets depicting the specified model:

GET https://stock.adobe.io/Rest/Media/1/Search/Files?search_parameters[model_id]=58344279


## Error codes

Each error generates a JSON array that contains the following keys and values. If your application receives this array and you need assistance, send the array to Adobe.



*   An **error** key.  
*   Optionally a **code** key. Specifies an integer designating the category of error. Code values:  
    *   10: Invalid access token 
    *   The access token that you passed is invalid or expired.
    *   11: Invalid API Key
    *   The API key that you passed is not valid or has expired.
    *   20: Invalid parameters
    *   The URL parameters that you passed are not supported.
    *   31: Invalid Method
    *   The method that you specified does not exist in the method list.
    *   100: Invalid data
    *   Data that you specified as arguments are not supported.


## More Information

See the Search discussion in[ Creating Adobe Stock Applications.](https://www.adobe.io/apis/creativecloud/stock/docs/createapps.html#search)
