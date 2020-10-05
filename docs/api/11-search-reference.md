# Search API reference



<!-- MarkdownTOC -->

- [Search requests](#search-requests)
    - [About search and filter criteria](#about-search-and-filter-criteria)
    - [Authentication](#authentication)
    - [Request headers](#request-headers)
    - [URL parameters](#url-parameters)
- [Responses](#responses)
- [Example returned comps values](#example-returned-comps-values)
- [Example queries and responses](#example-queries-and-responses)
    - [Common search queries](#common-search-queries)
- [Error codes](#error-codes)
- [More information](#more-information)

<!-- /MarkdownTOC -->

You can query Adobe Stock for assets that meet your specified search criteria. You can filter the results, specify the sort order in which the results are returned, and choose how many assets to return for each page of results.

<a id="search-requests"></a>
## Search requests 

A request using the Stock Search API retrieves a list of assets from Adobe Stock that matches a set of search and filter values. A maximum of 64 assets can be returned from one request. This is a paginated interface that you can call multiple times to retrieve the full list.

For a guide to usage and additional examples, see [Creating Adobe Stock applications](../getting-started/04-creating-apps.md).

| Endpoint | Methods |
| ------------ | ------------- |
| https://stock.adobe.io/Rest/Media/1/Search/Files | GET <p>POST (only when using the similar_image parameter) |


<a id="about-search-and-filter-criteria"></a>
### About search and filter criteria 

Search commands have three formats:

1. __Search parameters.__ In general, search parameters identify asset information for values that cannot be predetermined, such as ID numbers or keywords. Parameters for searches look like this:

    `search_parameters[*search_item*]=*value*`

    For example:

    `search_parameters[words]=dog big happy`

    Search parameters are treated as AND. For example, you could combine `[words]` and `[creator_id]` to search for assets created by a specific creator that have the specified keywords.

    `search_parameters[words]=dog big happy&search_parameters[creator_id]=12345`

    __Tip:__ You must specify at least one `search_parameters` value for each Search.


2. __Filtering values.__ These optional qualifiers specify which of the found assets to return. In general, filters identify asset information that has a known set of values, such as true/false or file type. Parameters for filtering look like this:

    `search_parameters[filters][*filter_item*]=*value*`

    For example:

    `search_parameters[filters][orientation]=horizontal`

    You can specify multiple filtering values for content_type, template_type_id, and template_category_id; search returns assets that match any of those values.  The remaining filters are treated as AND.

3. __Response control.__ In addition to the filter and search mechanisms above, search queries by default return a fixed number of fields. To increase or decrease the scope of the response data, add one or more `result_columns[]` to the query. For example, this command will return _only_ content IDs.

    `result_columns[]=id`

```json
{
    "id": 108289885
},
{
    "id": 173919253
},
```

Chain together multiple `result_columns[]` commands to get exactly the results you want.

    result_columns[]=id&result_columns[]=title&result_columns[]=thumbnail_url

```json
{
    "id": 108289885,
    "title": "Vector illustration of colorful horse, unicorn, or pony.",
    "thumbnail_url": "https://as1.ftcdn.net/jpg/01/08/28/98/500_F_108289885_zxdW0u0ds2oI69ZiLaON3kfhM2OLxdin.jpg"
},
{
    "id": 173919253,
    "title": "Isolated cute watercolor unicorn clipart. Nursery unicorns illustration. Princess rainbow unicorns poster. Trendy pink cartoon horse.",
    "thumbnail_url": "https://as1.ftcdn.net/jpg/01/73/91/92/500_F_173919253_ivNTXG10bJKxSPRkxiAeaZZOjaWr5SBe.jpg"
},
```

See [Responses](#responses), below.

<a id="authentication"></a>
### Authentication 

An `Authorization` header is not required. If you do not pass a valid bearer token in the Authorization header, you can search within Adobe Stock and access preview versions of assets, but the API will not return licensing requirements or give you the licensed status for the assets. Requests made in this way are essentially anonymous, with no notion of the user making the request.

If you do pass a valid token, then the Adobe Stock service returns the license state and licensed URL for each asset. See [API authentication](../getting-started/03-api-authentication.md).  


<a id="request-headers"></a>
### Request headers 

See [API authentication](../getting-started/03-api-authentication.md) and [Headers for Stock API calls](10-headers-for-api-calls.md) for details about header content. 


*   Required headers: `x-Product`, `x-api-key`
*   Optional headers: `Authorization` (required to view license state), `X-Request-Id`


<a id="url-parameters"></a>
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
   <td>Location language code. String. Default is en_US.  See <a href="14-locale-codes.md">Locale codes reference</a>.
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
<strong>Tip:</strong> The number of images returned in each call can vary, but never exceeds 64 entries. </p>
<p>See the note below for <code>search_parameters[filters][premium]</code> and refer to the FAQ question, <a href="../15-faq.md?#why-are-there-more-search-results-returned-than-the-limit-value">Why are there more search results returned than the 'limit' value?</a></p>
   </td>
  </tr>
  <tr>
   <td>search_parameters[offset]
   </td>
   <td><p>Start position in query. Valid values are 0 (the first found asset) or higher integers. With each successive call for your search, increment this by the [limit] value to get the next page of assets.</p>
<p>
For example, by default your first call uses a 0 offset and limit of 32 to return the first 32 found assets.  Call this API again with an offset of 32 to retrieve the next page. Integer. Default is 0.</p>

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
<li><code>featured</code></li>
Attempts to display the highest quality content first, as scored by Adobe Sensei's machine learning algorithms. In practice, it performs best on lifestyle imagery. 
<li><code>nb_downloads</code></li>
In descending order by the number of downloads by all users since the asset was added to Adobe Stock.
<li><code>undiscovered</code></li>
Starting with assets that have not commonly been viewed or downloaded.</li>
<li><code><s>popularity</s></code></li>
<s>In descending order by the number of views by all users.</s> This filter option is no longer maintained, and may be removed in the future.
</ul>

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
<code>0 | 1</code> (if using image data). 
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
   <td><p>Search for assets with a specific category ID. Integer.
For example, to search for assets in the category "travel":</p>
  <p><code>search_parameters[category]=1043</code></p>
  <p>For more information see the <a href="17-categorytree.md">CategoryTree API reference</a>.</p>

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
<li><code>220</code>: Medium-Large (220 px)
<li><code>240</code>: Large (240 px)
<li><code>500</code>: Extra large (XL) (500 px). Returned with watermark. (default)
<li><code>1000</code>: Extra-extra large (XXL) (1000 px). Returned with watermark.</li>
</ul>
   </td>
  </tr>
  <tr>
    <td><s>search_parameters[filters][area_pixels]</s></td>
    <td>This filter will be deprecated in an upcoming release. Please use the filters <code>[area_m_pixels]</code>, <code>[image_width]</code>, and <code>[image_height]</code> in its place.</td>
  </tr>
  <tr>
   <td>search_parameters[filters][area_m_pixels]
   </td>
   <td>
    <p>Image sizes in megapixels (millions of pixels) to return, specified as a range in the format <code>min-max</code>. <code>min</code> and <code>max</code> are both optional and default to open ranges. Values must be (whole) integers.</p>
    <p>Examples:</p>
    <p>
      <em>Search for an image that has a minimum pixel area of 4000x2500 (10Mpix) and maximum area of 5000x5000 (25Mpix):</em><br>
      <code>search_parameters[filters][area_m_pixels]:10-25</code>
    </p>
    <p>
      <em>Search for an image that has a minimum area size of 4000x5000 pixels (20Mpix).</em><br>
      <code>search_parameters[filters][area_m_pixels]:20-</code>
    </p>
   </td>
  </tr>
  <tr>
    <td>search_parameters[filters][image_width]</td>
    <td>
        <p>Asset width specified as a range of pixels in the format <code>min-max</code>. <code>min</code> and <code>max</code> are both optional and default to open ranges.</p>
        <p>Example:</p>
        <p>
            <em>Only include images with a width of at least 5000 pixels</em><br>
            <code>search_parameters[filters][image_width]=5000-</code><br>
            OR <code>search_parameters[filters][image_width]=5000</code>
        </p>
    </td>
  </tr>
  <tr>
    <td>search_parameters[filters][image_height]</td>
    <td>
        <p>Asset height specified as a range of pixels in the format <code>min-max</code>. <code>min</code> and <code>max</code> are both optional and default to open ranges.</p>
        <p>Examples:</p>
        <p>
            <em>Only include images with a height between 2000-4000 pixels</em><br>
            <code>search_parameters[filters][image_height]=2000-4000</code>
        </p>
        <p>
            <em>Only include images with a max height of 3000 pixels</em><br>
            <code>search_parameters[filters][image_height]=-3000</code>
        </p>
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
   <p>Strongly recommend <strong>always</strong> setting this parameter to one of its three values, as it works around an issue where more assets can be returned than set in the <code>search_parameters[limit]</code> parameter, which can throw off pagination. See the FAQ, <a href="../15-faq.md?#why-are-there-more-search-results-returned-than-the-limit-value">Why are there more search results returned than the 'limit' value?</a></p>
   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][3d_type_id][]
   </td>
   <td>A multiple-value array specifying which 3D types to return. Valid values and meanings:<ul>



<li><code>1</code>: Models
<li><code>2</code>: Lights
<li><code>3</code>: Materials</li></ul>

   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][template_type_id][]
   </td>
   <td>Return found templates (starter files) for PSD or  AI only if they are of the specified type. A multiple-value array specifying which template types to return.  
<p>
Valid values and meanings:
<ul>
<li><code>1</code>: Photoshop PSDT
<li><code>2</code>: Illustrator AIT</li>
<li><code>3</code>: Indesign INDT</li>
<li><code>4</code>: Premiere Pro Motion Graphics Template</li>
<li><code>5</code>: After Effects Motion Graphics Template</li>
</ul>
<p>
For example:
<p>
<code>search_parameters[filters][template_type_id][]=2&amp;search_parameters[filters][template_type_id][]=3</code>

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
<code>0 | 1</code>. 
   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][content_type:illustration]
   </td>
   <td>Include found assets that are illustrations. Integer.
<p>
<code>0 | 1</code>. 
   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][content_type:vector]
   </td>
   <td>Include found assets that are vectors. Integer.
<p>
<code>0 | 1</code>.
   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][content_type:video]
   </td>
   <td>Include found assets that are videos. Integer.
<p>
<code>0 | 1</code>.
   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][content_type:template]
   </td>
   <td>Include found assets that are templates. Integer.
<p>
<code>0 | 1</code>.
   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][content_type:3d]
   </td>
   <td>Include found assets that are 3D items. Integer.
<p>
<code>0 | 1</code>.
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
   <td>Return found assets only if they are flagged as including Explicit/Nudity/Violence. Integer.<ul>

<li><code>0</code>: Default. Omit assets in this group.
<li><code>1</code>: Return assets only if they are in this group.</li></ul>

   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][isolated:on]
   </td>
   <td>Return found assets only if the subject is isolated from the background by being on a uniformly colored background. Integer.<ul>

<li><code>0</code>: Default. Omit assets that are isolated.
<li><code>1</code>: Return assets only if they are isolated.</li></ul>

   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][panoramic:on]
   </td>
   <td>Return found assets only if they are panoramic (can be combined with [orientation]). Integer.<ul>

<li><code>0</code>: Default. Omit panoramic assets.
<li><code>1</code>: Return assets only if they are panoramic.</li></ul>

   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][orientation]
   </td>
   <td>Return found assets of the specified orientation. String. 
   <p>
Valid values and meanings:<ul>

<li><code>horizontal</code>: Only horizontal images.
<li><code>vertical</code>: Only vertical images.
<li><code>square</code>: Only square images.
<li><code>all</code>: Default. All image orientations.
</li></ul>

   </td>
  </tr>
  <tr>
   <td><s>search_parameters[filters][age]</s>
   </td>
   <td>
    This filter will be deprecated in a future release. Instead, please use <code>search_parameters[order]=creation</code> to sort by newest assets.</p>
<!--
<p>Return found assets of the specified age (weeks, months, years). String. Valid values and meanings:</p>
<ul>

<li><code>1w</code>: Only assets up to 1 week old.
<li><code>1m</code>: Only assets up to 1 month old.
<li><code>6m</code>: Only assets up to 6 months old.
<li><code>1y</code>: Only assets up to 1 year old.
<li><code>2y</code>: Only assets up to 2 years old.
<li><code>all</code>: Default. Assets of any age.</li></ul>
-->
   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][video_duration]
   </td>
   <td>Return found videos whose duration is no longer than the specified duration in seconds. String.
<p>
Valid values and meanings:<ul>

<li><code>10</code>: Only videos up to 10 seconds.
<li><code>20</code>: Only videos up to 20 seconds.
<li><code>30</code>:  Only videos up to 30 seconds.
<li><code>30-</code>: Only videos longer than 30 seconds.
<li><code>all</code>: Default. Videos of all durations.</li></ul>

   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][colors]
   </td>
   <td><p>Comma-separated list of hexadecimal colors (without any # prefix). Return only found assets that contain the specified colors. String.
   <p>Example:<br>
   <code>search_parameters[filters][colors]=ff0000,00ff00,0000ff</code>
   </td>
  </tr>
  <tr>
   <td>search_parameters[gallery_id]
   </td>
   <td>Returns members of the specified Fotolia gallery, which must be public. Note this requires access to a Fotolia website user account. String.
   </td>
  </tr>
  <tr>
   <td>search_parameters[filters][copy_space]
   </td>
   <td><p>Image copy space. Value <code>all</code> returns all the images (equivalent to not having the filter in the query); value <code>1</code> filters for images that have copy space. String.</p>
   <code>all | 1</code>
   </td>
  </tr>
  <tr>
   <td>result_columns[]
   </td>
   <td><p>Specific fields you wish to include in the search result, excluding all other fields. Array[]. For a detailed description of each field, see <a href="#responses">Responses</a>, below.
<p>
<strong>Tip:</strong> To combine result columns, use this syntax: 
<code>result_columns[]=is_licensed&amp;result_columns[]=creation_date</code>
<p>
<strong>Note 1:</strong> Fields marked with <strong>*</strong> are returned by default, but if the <code>result_columns[]</code> command is present, the default fields will not be returned unless explicitly included.
<br>
<strong>Note 2:</strong> <code>is_licensed</code> requires an authentication header.

<p><code>*nb_results</code>   <code>*id</code>   <code>*title</code>   <code>*creator_name</code>   <code>*creator_id</code>   <code>country_name</code>   <code>*width</code>   <code>*height</code>   <code>*thumbnail_url</code>   <code>*thumbnail_html_tag</code>   <code>*thumbnail_width</code>   <code>*thumbnail_height</code>   <code>thumbnail_110_url</code>   <code>thumbnail_110_width</code>   <code>thumbnail_110_height</code>   <code>thumbnail_160_url</code>   <code>thumbnail_160_width</code>   <code>thumbnail_160_height</code>   <code>thumbnail_220_url</code>   <code>thumbnail_220_width</code>   <code>thumbnail_220_height</code>   <code>thumbnail_240_url</code>   <code>thumbnail_240_width</code>   <code>thumbnail_240_height</code>   <code>thumbnail_500_url</code>   <code>thumbnail_500_width</code>   <code>thumbnail_500_height</code>   <code>thumbnail_1000_url</code>   <code>thumbnail_1000_width</code>   <code>thumbnail_1000_height</code>   <code>*media_type_id</code>   <code>*category</code>   <code>*category_hierarchy</code>   <code>nb_views</code>   <code>nb_downloads</code>   <code>creation_date</code>   <code>keywords</code>   <code>has_releases</code>   <code>comp_url</code>   <code>comp_width</code>   <code>comp_height</code>   <code>is_licensed</code>   <code>*vector_type</code>   <code>*content_type</code>   <code>framerate</code>   <code>duration</code>   <code>comps</code>   <code>details_url</code>   <code>template_type_id</code>   <code>template_category_ids</code>   <code>marketing_text</code>   <code>description</code>   <code>size_bytes</code>   <code>*premium_level_id</code>   <code>is_premium</code>   <code>licenses</code>   <code>video_preview_url</code>   <code>video_preview_width</code>   <code>video_preview_height</code>   <code>video_preview_content_length</code>   <code>video_preview_content_type</code>   <code>video_small_preview_url</code>   <code>video_small_preview_width</code>   <code>video_small_preview_height</code>   <code>video_small_preview_content_length</code>   <code>video_small_preview_content_type</code>

   </td>
  </tr>
</table>

<a id="responses"></a>
## Responses 

The Adobe Stock service returns information about all found assets that also match the filtering criteria.

**Tip:** Assets on Adobe Stock can be added, changed, or removed by other parties between your API calls. Therefore, the total number of matching assets and the selection of assets can change between successive calls to Search.

All responses are in a JSON array with this general structure:

```js
{"nb_results" : value
    "files": [
     { details_for_first_file ...
          field_name: value,       
          ...
     },
     { details_for_second_file   ...
          field_name: value,       
          ...      
     }
  ]
}
```

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
   <td>URL for the default-sized asset thumbnail. You can use this to display the thumbnail on your page using your preferred display method. Alternatively, use <code>thumbnail_html_tag</code>. String.
   </td>
  </tr>
  <tr>
   <td>thumbnail_html_tag
   </td>
   <td>HTML &lt;img&gt; tag that you can use to display the default asset thumbnail. This is a convenience for displaying the thumbnail and references the <code>thumbnail_url</code>.  String.
<p>Example:
<pre><code>"thumbnail_html_tag": "&lt;img src='https://as1.ftcdn.net/jpg/00/86/76/04/110_F_86760419_NEhOeuriYu82RwfgDqjTeIL9yx7ih5iv.jpg' 
    alt='German Shepherd Dog Sticking Head Out Driving Car Window'
    title='Photo: German Shepherd Dog Sticking Head Out Driving Car Window' /&gt;",</code></pre>
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
   <td><p>Width for the thumbnail of the requested size, where * is the thumbnail size in pixels.  Float. 
   <p>For example:<br>
<code>"thumbnail_160_width": 200</code>
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
   <td>URL for the requested thumbnail size, where * is the thumbnail size in pixels. You can use this to display the thumbnail on your page using your preferred display method. Alternatively, use <code>thumbnail_*_html_tag</code>.  String.
   </td>
  </tr>
  <tr>
   <td>thumbnail_*_html_tag
   </td>
   <td>HTML &lt;img&gt; tag that you can use to display the thumbnail of the requested size, where  where * is the thumbnail size in pixels. This is a convenience for displaying the thumbnail and references the <code>thumbnail_*_url</code>.  String.
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

<li><code>Standard</code>: License for the full-resolution asset
<li><code>Extended</code>: Extended license
<li><code>Video_HD</code>: Video HD license
<li><code>Video_4K</code>: Video 4K license
<li><code>Standard_M</code>: License for a medium-size asset approximately 1600x1200 pixels
<li><code>""</code> <em>(empty string)</em>: No license applies</li></ul>

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
<pre><code>"category": {
    "id": 0000,
    "name":"..." }</code></pre>

<p>
For example: 
<br>
<code>"category": {  "id": 47,  "name": "Dogs"}</code>
   </td>
  </tr>
  <tr>
   <td>category{<em>id</em>}
   </td>
   <td>Identifier for the category assigned to the asset. Integer. 
   </td>
  </tr>
  <tr>
   <td>category{<em>name</em>}
   </td>
   <td>Localized name of the asset's category. String.
   </td>
  </tr>
  <tr>
   <td>keywords
   </td>
   <td>List of localized keywords for the asset. Array.
   </td>
  </tr>
  <tr>
   <td>media_type_id
   </td>
   <td>Type of the asset. Integer.

<ul>
<li><code>1</code>: Photos
<li><code>2</code>: Illustrations
<li><code>3</code>: Vectors
<li><code>4</code>: Videos
<li><code>6</code>: 3D
<li><code>7</code>: Templates</li>
</ul>

   </td>
  </tr>
  <tr>
   <td>vector_type
   </td>
   <td>If the asset is a vector, this indicates whether it is an SVG or an AI/EPS asset. String. 
   <p>Values and meanings:<ul>

<li><code>svg</code>: SVG file
<li><code>zip</code>: AI/EPS file</li></ul>

   </td>
  </tr>
  <tr>
   <td>content-type
   </td>
   <td>Mime type of the asset's content. String. For example:
<p>
<code>"content_type": "image/jpeg"</code>
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
   <td>JSON object that contains one or more of the following properties for complementary assets if applicable: Standard, Video_HD, or Video_4K. The properties contain width, height, comp URL.  See<a href="#example-returned-comps-values"> Example returned comps values</a>.
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

<li><code>1</code>: PSDT</li>
<li><code>2</code>: AIT</li>
<li><code>3</code>: INDT</li>
<li><code>4</code>: PPRO Motion Graphics Template</li>
<li><code>5</code>: AE Motion Graphics Template</li>
</ul>

   </td>
  </tr>
  <tr>
   <td>marketing_text
   </td>
   <td>Marketing text for the template in <a href="http://daringfireball.net/projects/markdown/">Markdown format</a>, if the found asset is a template.  String.
   </td>
  </tr>
  <tr>
   <td>description
   </td>
   <td>Description text for the template in <a href="http://daringfireball.net/projects/markdown/">Markdown format</a>,  if the found asset is a template. String.
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
<ul>
<li><code>0</code>: Core/standard</li>
<li><code>1</code>: Free</li>
<li><code>2</code>: Premium level 1</li>
<li><code>3</code>: Premium level 2</li>
<li><code>4</code>: Premium level 3</li>
</ul>
   </td>
  </tr>
</table>

<a id="example-returned-comps-values"></a>
## Example returned comps values 

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

<a id="example-queries-and-responses"></a>
## Example queries and responses 

This example searches for assets that have the keyword "dog" and returns no more than the first two matches.

```http
GET /Rest/Media/1/Search/Files?locale=en_US&search_parameters[words]=dogs&search_parameters[limit]=2 HTTP/1.1
Host: stock.adobe.io
X-Product: MySampleApp/1.0
x-api-key: MyApiKey
```

The preceding request returns two asset descriptions. `nb_results` shows that 399,884 assets currently match the keyword "dog".

```json
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
        "<img src='https://as1.ftcdn.net/jpg/00/86/76/04/110_F_86760419_NEhOeuriYu82RwfgDqjTeIL9yx7ih5iv.jpg' 
        alt='German Shepherd Dog Sticking Head Out Driving Car Window' 
        title='Photo: German Shepherd Dog Sticking Head Out Driving Car Window' />",
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
     "thumbnail_url": "https://as1.ftcdn.net/jpg/00/84/97/72/110_F_84977202_JplQMoMQ5QiZCgVeWLwKhFHCrr4HG99Q.jpg",
     "thumbnail_html_tag":    
        "<img src='https://as1.ftcdn.net/jpg/00/84/97/72/110_F_84977202_JplQMoMQ5QiZCgVeWLwKhFHCrr4HG99Q.jpg'
         alt='Happy dog playing outside and carrying the American flag' 
         title='Photo: Happy dog playing outside and carrying the American flag' />",
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
}
```

<a id="common-search-queries"></a>
### Common search queries 

Here are simple examples of common searches.


*   Keyword search; assets matching "purple" and "clouds":

    `https://stock.adobe.io/Rest/Media/1/Search/Files?search_parameters[words]=purple clouds`


*   Using pagination, get the 3rd page of results (rows 64-95) for the word "dogs":

    `https://stock.adobe.io/Rest/Media/1/Search/Files?search_parameters[words]=dogs&search_parameters[limit]=32&search_parameters[offset]=64`


*   Search for assets similar in appearance to the specified asset ID:

    `https://stock.adobe.io/Rest/Media/1/Search/Files?search_parameters[similar]=121353611`


*   Search for assets similar in appearance to the specified URL:

    `https://stock.adobe.io/Rest/Media/1/Search/Files?search_parameters[similar_url]=https://i.kinja-img.com/gawker-media/image/upload/xqkbwkexcl7udc5va7pn.jpg`


*   Similar asset by URL and keyword:

    `https://stock.adobe.io/Rest/Media/1/Search/Files?search_parameters[similar_url]=https://i.kinja-img.com/gawker-media/image/upload/xqkbwkexcl7udc5va7pn.jpg&search_parameters[words]=cats`


*   Search for assets depicting the specified model:

    `https://stock.adobe.io/Rest/Media/1/Search/Files?search_parameters[model_id]=58344279`

<a id="error-codes"></a>
## Error codes 

Each error generates a JSON array that contains the following keys and values. If your application receives this array and you need assistance, send the array to Adobe.


*   An **`error`** key.  
*   Optionally a **`code`** key. Specifies an integer designating the category of error. Code values:  
    *   `10`: Invalid access token. The access token that you passed is invalid or expired.
    *   `11`: Invalid API Key. The API key that you passed is not valid or has expired.
    *   `20`: Invalid parameters. The URL parameters that you passed are not supported.
    *   `31`: Invalid Method. The method that you specified does not exist in the method list.
    *   `100`: Invalid data. Data that you specified as arguments are not supported.


<a id="more-information"></a>
## More information 

* See the practical search example in [Search for assets](../getting-started/apps/05-search-for-assets.md).
* Refer to the [Affiliate API Workflow](../getting-started/07-workflow-guides.md) guide for a complete guide to partnering with Adobe Stock and using the Search API.
