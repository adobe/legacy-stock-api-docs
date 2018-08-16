# Adobe Stock Ingestion API

The Adobe Stock Ingestion API provides a mechanism for partners to create,
update, and delete assets in the Adobe Stock system. Jobs are submitted in
[JSON files](https://tools.ietf.org/html/rfc8259) dropped to an Adobe-provided
Amazon S3 location, which then automatically triggers processing.

Each job file runs a single job for a single file. Multiple assets can be
processed by submitting multiple job files. **Note that the processing order of
job files is not guaranteed.**

A job file contains:

* The task to complete (create, update, or delete)
* Asset properties, such as asset location, dimension, and type
* Metadata properties, such as headline, description, and keywords

Job filenames are restricted to the [supported character list](#user-content-supported-filename-characters) and 
must have the extension .json.

The API currently supports video and 3D assets with commercial licenses.

## Creating an asset

An asset is created in Adobe Stock with the `create` action. When creating an
asset the following properties are required: `assetType`, `partnerAssetId` and
`uri`.

If the assetType is video then the following additional properties must be
provided: `height` and `width`.

In addition to the file properties, the following metadata properties are
required when creating a new asset: `headline`, `keywords`, and `licenseType`.

If the licenseType is editorial then the following additional metadata property
must be provided: `category` and `eventDate`.

See the [asset properties reference](#user-content-asset-properties) and
[metadata properties reference](#user-content-metadata-properties) for a
complete list of supported properties, their descriptions, default values, and
valid values.

Here is a complete sample for creating an editorial video asset:

```JSON
{
  "action": "create",
  "assets": [
    {
      "assetType": "video",
      "height": 3840,
      "metadata": {
        "category": 1156,
        "creatorNames": [
          "Neil Enns"
        ],
        "description": "Paige Pierce putts out to win her fourth straight Vibram Open at the Maple Hill disc golf course in Leicester, MA on September 3, 2017. Pierce played a 1050-rated last round to secure the victory.",
        "embargoDate": "2017-09-10T18:20:50.00Z",
        "eventDate": "2017-09-10T18:20:50.00Z",
        "graphicContent": false,
        "headline": "Paige Pierce putts out to win her fourth straight Vibram Open.",
        "keywords": [
            "disc golf",
			"Vibram Open",
			"Paige Pierce",
			"Frisbee golf",
			"PDGA Vibram Open"
        ],
        "licenseType": "editorial",
        "premiumLevel": 0
      },
      "partnerAssetId": "1A2C3B",
      "uri": "https://jd2-7ff9409ace8a9f2a397bfde47684147c-us.s3.amazonaws.com/assets/file.mp4",
      "width": 5760
    }
  ],
  "version": "1.0.0"
}
```

### Updating an asset

The metadata on an asset is updated in Adobe Stock with the `update` action.
When updating an asset the following asset properties are required:
_partnerAssetId_. This indicates which asset the metadata updates apply to.

When updating the asset on a metadata at least one of the following properties
must be specified: `category`, `creatorNames`, `description`, `eventDate`,
`graphicContent`, `headline`, `keywords`, `premiumLevel` and `rating`.

Adobe Stock does not support updating the licenseType or embargoDate on an
asset. If you need to change these properties delete the old asset and upload
a new asset with the other licenseType or embargoDate specified.

Changing the asset itself, by modifying the uri, is also not supported. If you
need to change the asset itself, delete the old asset and upload a new asset.
**Never change the physical asset file located at the uri**. If you need to
change the asset itself send a delete job for the old asset and submit a
create job for new one at a different uri location.

When updating the ``keywords`` property the supplied keywords replace all
existing keywords that were associated with the asset.

Here is a complete sample:

```JSON
{
  "action": "update",
  "assets": [
    {
      "metadata": {
        "headline": "Paige Pierce putts out to win her fifth straight Vibram Open.",
        "keywords": [
            "disc golf",
			"Vibram Open",
			"Paige Pierce",
			"Frisbee golf",
			"PDGA Vibram Open"
        ]
      },
      "partnerAssetId": "1A2C3B"
    }
  ],
  "version": "1.0.0"
}
```

### Deleting an asset

Assets are deleted from Adobe Stock with the `delete` action. When deleting an
asset the following asset properties are required: `partnerAssetId`. This
indicates which asset is deleted. No metadata properties are supported on asset
deletion.

When an asset is deleted from Adobe Stock it is removed from sale, however
customers who have previously purchased the asset maintain rights to it and are
still able to download it for use.

Here is a complete sample:

```JSON
{
  "action": "delete",
  "assets": [
    {
      "partnerAssetId": "1A2C3B"
    }
  ],
  "version": "1.0.0"  
}
```

## Job properties

The job is the top-level object in the JSON and provides information about
the action to take and the version of the schema being used. All string properties must be between one and 5000 characters.

|Property|Data Type|Description|Example|Default Value|Supported Values|
|---|---|---|---|---|---|
|action|string|The action to take on the asset|"create"||"create", "update", "delete"|
|assets|[asset](#user-content-asset-properties) array|The asset being created, updated, or deleted||Only one asset per job is supported|
|version|string|The version of the schema being used|"1.0.0"||"1.0.0"|

## Asset properties

Asset properties provide information about the file containing the asset. All string properties must be between one and 5000 characters.

|Property|Data Type|Description|Example|Default Value|Supported Values|
|---|---|---|---|---|---|
|assetType|string|The type of the asset.|&quot;image&quot;||image, video, 3D|
|height|integer|The height of the asset in pixels.|2160|||
|metadata|[metadata](#user-content-metadata-properties)|The metadata for the asset|||
|partnerAssetId|string|Unique identifier for the asset in the partner's asset management system. This property cannot be changed after the asset is created.|&quot;1A2B3C&quot;|||
|uri|string (uri)|The path to the asset. For assets stored in Amazon S3 use the full path to the asset in the S3 bucket.|&quot;<https://jd2-7ff9409ace8a9f2a397bfde47684147c-us.s3.amazonaws.com/assets/file.jpg>&quot;|The path and filename portion of the uri is restricted to the [supported character list](#user-content-supported-filename-characters).|uri should be min 1, max 1050 (1024 + 26).|
|width|integer|The width of the asset in pixels.|3840|||

## Metadata properties

Metadata properties provide information about the content of the asset such as the keywords and description.

|Property|Data Type|Description|Example|Default Value|Supported Values|
|---|---|---|---|---|---|
|category|integer|Adobe Stock category. Describes the nature, intellectual, artistic or journalistic characteristic of an asset type. Adobe Stock supports a list of over 900 categories for commercial images and a limited list for editorial images.|1156||Editorial and Commercial assets: [See the category reference](#user-content-adobe-stock-categories).|
|creatorNames|string array|IPTC Creator. The name of the person who created the asset, but in cases where the photographer should not be identified the name of a company or organisation may be appropriate.|["Neil Enns"]||Only one name is supported.|
|description|string|IPTC Description. Often referred to as a "caption" this is used to describe the who, what (and possibly where and when) and why of what is happening in the photograph. It can include people's names, their role in the action, the location. Geographic location details should also be entered in the Location fields. The amount of detail included will depend on the image and whether the image is documentary or conceptual. Typically, editorial images come with complete caption text, while advertising images may not.|"Paige Pierce putts out to win her fourth straight Vibram Open at the Maple Hill disc golf course in Leicester, MA on September 3, 2017. Pierce played a 1050-rated last round to secure the victory."|||
|embargoDate|string (date-time)|Adobe Stock embargo date. The asset will not be made available on the Adobe Stock platform for purchase until after the specified date and time. No guarantee is made that the asset will be published on this specific date and time, just that the asset will not be available until after the date and time has passed. If omitted the asset will be made available immediately after processing completes. Must be specified in UTC, formatted as defined by RFC 3339, section 5.6. This property cannot be changed after the asset is created.|"2017-09-10T18:20:50.00Z"|||
|eventDate|string (date-time)|Designates the date and time of the event shown in the asset. Must be specified in UTC, formatted as defined by RFC 3339, section 5.6. Only supported on editorial content, not on commercial content.|"2017-09-10T18:20:50.00Z"|||
|graphicContent|boolean|Adobe Stock graphic content flag. If true, the image contains content considered graphical per the Adobe Stock content guidelines.|false|false|true, false|
|headline|string|IPTC Headline. A headline is a brief synopsis or summary of the contents of the photograph. Like a news story, the Headline should grab attention, and telegraph the content of the image to the audience. Headlines need to be succinct. Leave the supporting narrative for the Description field.|"Paige Pierce putts out to win her fourth straight Vibram Open."|||
|keywords|string array|IPTC Keywords. Keywords are descriptive words added to an image to enable search and retrieval. They describe what is visible in the image and concepts associated with the image. Keywords can be single or compound terms.|["disc golf", "Vibram Open", "Paige Pierce", "Frisbee golf", "PDGA Vibram Open"]|At least five unique keywords must be provided.|Keywords should not be more than 1000 items.|
|licenseType|string|Adobe Stock license type. Determines whether the asset is sold under commercial or editorial license terms. This property cannot be changed after the asset is created. Only image and video assets can be marked as editorial.|"editorial"||editorial, commercial|
|premiumLevel|integer|Adobe Stock premium level. This level is used in conjunction with the asset type and customer-specified download resolution and license type to calculate the price charged to the customer for the asset. [Learn more about Adobe Stock premium levels](#user-content-adobe-stock-premium-levels).|2|2|0, 1, 2, 3, 4|

## Supported Filename Characters

The following characters are supported in job file names and asset uris:

* Alphanumeric characters: [0-9a-zA-Z]
* Special characters: !, -, _, ., *, ', (, +, and )

## Adobe Stock Premium Levels

The premium level of an asset, in conjunction with the asset type, license
rights and download size, determines the price charged for the asset when
purchased. Partners specify the premium level and asset type, and the end user
selects the license rights and download size at time of purchase. The following
table outlines how these four values combine to determine the final price for
the asset.

|Asset Type|Premium Level|License|Asset Size|Price|
|---|---|---|---|---|
|Commercial image|0|Standard|Full resolution|$9.99|
|Commercial image|0|Extended|Full resolution|$79.99|
|Commercial image|2|Enhanced|Full resolution|$99.99|
|Commercial image|2|Enhanced|Half resolution|$49.99|
|Commercial image|3|Enhanced|Full resolution|$249.99|
|Commercial image|3|Enhanced|Half resolution|$119.99|
|Commercial image|4|Enhanced|Full resolution|$499.99|
|Commercial image|4|Enhanced|Half resolution|$249.99|
|Editorial image|0|Standard|Full resolution|$9.99|
|Editorial image|1|Standard|Full resolution|$0.00|
|Editorial image|3|Standard|Half resolution|$49.99|
|Editorial image|3|Enhanced|Full resolution|$199.99|
|Editorial image|4|Enhanced|Full resolution|$499.99|
|Editorial image|4|Enhanced|Half resolution|$249.99|
|Commercial video|0|Enhanced|4K|$199.99|
|Commercial video|0|Enhanced|HD|$79.99|
|Commercial video|3|Enhanced|4K|$249.99|
|Commercial video|3|Enhanced|HD|$119.99|
|Editorial video|0|Standard|HD|$79.99|
|Editorial video|0|Enhanced|HD|$249.99|
|Editorial video|3|Enhanced|HD|$499.99|

## Adobe Stock Categories

Adobe Stock supports different categories for assets, with different valid
categories depending on whether the asset is for editorial or commercial use.

### Editorial Assets

|Category Code|Description|
|---|---|
|1156|Sport News|
|1155|Entertainment|
|1157|News|

### Commercial Assets

|Category Code|Description|
|---|---|
|1|Animals|
|2|Accessories|
|3|Birds|
|4|Dove|
|5|Ducks|
|6|Eagles|
|7|Flamingo|
|8|Geese|
|9|Ostrich|
|10|Other|
|11|Owls|
|12|Parrots|
|13|Penguins|
|14|Pigeons|
|15|Roosters and Chickens|
|16|Seagulls|
|17|Swans|
|18|Turkeys|
|19|Bugs, Insects and Arachnids|
|20|Bees|
|21|Butterflies|
|22|Cockroaches|
|23|Dragonflies|
|24|Flies|
|25|Ladybugs|
|26|Other|
|27|Scorpions|
|28|Snails|
|29|Spiders|
|30|Worms|
|33|Imaginary Animals|
|32|Other|
|34|Centaur|
|35|Dragons|
|36|Mermaid|
|37|Pegasus|
|38|Unicorn|
|39|Mammals|
|40|Bats|
|41|Bears|
|42|Bulls and cows|
|43|Camels and Dromadaires|
|44|Cats|
|45|Cheetahs|
|46|Deer|
|47|Dogs|
|48|Donkeys|
|49|Elephants|
|50|Giraffes|
|51|Goats|
|52|Hippos|
|53|Horses|
|54|Kangaroos|
|55|Koalas|
|56|Leopards and Panthers|
|57|Lions|
|58|Monkeys|
|59|Mouse|
|60|Other|
|61|Other Pets|
|62|Pandas|
|63|Pigs|
|64|Polar bears|
|65|Puma|
|66|Rabbits|
|67|Rhinoceros|
|68|Sheeps|
|69|Tigers|
|70|Wolves|
|71|Microscopic world|
|72|Reptiles and Amphibians|
|73|Chameleons|
|74|Crocodiles|
|75|Frogs|
|76|Lizards|
|77|Other|
|78|Snakes|
|79|Turtles|
|80|Under the Sea|
|81|Coral|
|82|Crabs|
|83|Deep Water fish|
|84|Dolphins|
|85|Fish|
|86|Lobster|
|87|Mussels|
|88|Other|
|89|Other Shellfish|
|91|Sea Snails|
|92|Sharks|
|95|Whales|
|96|Buildings and Architecture|
|97|Industrial and Commercial Buildings|
|98|Docks and Warehouses|
|99|Factories|
|100|Other|
|101|Shopping Malls|
|102|Skycrappers and Towers|
|103|Infrastructure|
|104|Airports|
|105|Bridges and Tunnels|
|106|Canals and Reservoirs|
|107|Dams|
|108|Lighthouses and Mills|
|109|Other|
|110|Parking Lots|
|111|Ports|
|112|Powerplants|
|113|Streets, Roads and Highways|
|114|Train Stations and the Underground|
|115|Monuments|
|116|Artistic Monuments|
|117|Castles|
|118|Forts|
|119|Fountains|
|120|Memorial Monuments|
|121|Other|
|122|Pyramids|
|123|Religious Monuments|
|124|Ruins|
|125|Statues|
|126|Walls|
|127|Other|
|128|Private Buildings|
|129|Bathrooms|
|130|Bedrooms|
|131|Cabins and Lodges|
|132|Doors|
|133|Houses and Flats|
|134|Kitchens|
|135|Living rooms|
|136|Mirrors|
|137|Other|
|138|Palaces|
|139|Private Gardens|
|140|Staircases|
|141|Swimming-pools|
|142|Windows|
|143|Public Buildings|
|144|Cemeteries|
|145|City Halls and Public Services|
|146|Hospitals, Labs and Clinics|
|147|Jail|
|148|Libraries|
|149|Museums|
|150|Other|
|151|Parks|
|152|Places of Worship|
|153|Stadiums|
|154|Theatres and Opera Houses|
|155|Shops|
|156|Bakery|
|158|Cottage Industry and Family Business|
|159|Department Stores|
|160|Florists|
|161|Grocery Stores|
|162|Hairdressing|
|163|Hotels and Restaurants|
|164|Markets|
|165|Other Retail|
|166|Supermarkets|
|167|Business|
|168|Business Concepts|
|169|Business Vision|
|170|Challenge|
|171|Corruption|
|172|Creativity|
|173|Globalization|
|174|Harassment|
|175|Innovation|
|176|Leadership|
|177|Motivation|
|178|Strike|
|179|Success in Business|
|180|Teamwork|
|181|Unemployment|
|182|Business Situations|
|183|Business Meetings and Partnerships|
|184|Business Presentations|
|185|Business Travel|
|186|Coffee Break|
|187|Getting Fired|
|188|Job Interviews|
|189|Job Search|
|190|Long Working Hours|
|191|Office Life|
|192|Promotions, Salary, Incentives and Rewards|
|193|Signing Contract|
|194|Finance|
|195|Banking|
|196|Bankruptcy|
|197|Investment Risk|
|198|Money, Currency and Credit Cards|
|199|Stock Exchange|
|200|Tax and Accounting|
|201|Office supplies|
|202|Envelopes|
|203|Notepads and Notebooks|
|204|Other|
|205|Paper|
|206|Pens and Pencils|
|207|Staplers|
|208|Tape|
|209|Sales|
|210|Consumer Service|
|211|Consumers|
|212|Discount|
|213|Marketing, Advertising and PR|
|214|Drinks|
|215|Accessories|
|216|Barrel|
|217|Cups and Mugs|
|218|Flute|
|219|Glass|
|220|Other|
|221|Alcohol|
|222|Aperitif and Digestive|
|223|Beer and Cider|
|224|Champagne|
|225|Cocktail|
|226|Other Strong Alcohol|
|227|Sake|
|228|Wine|
|229|Hot Drinks|
|230|Coffee|
|231|Hot Chocolate|
|232|Other|
|233|Tea and Infusion|
|234|Juice|
|235|Milk|
|236|Milk Shakes and Smoothies|
|237|Other|
|238|Sodas|
|239|Water|
|240|Environment|
|241|Ecology|
|242|Climate Change|
|243|Environment Destruction|
|244|Pollution|
|245|Recycling|
|246|Renewable Energies and Sustainable Development|
|247|Natural Disasters|
|248|Cyclones, Tornados and Twisters|
|249|Drought|
|250|Earthquakes|
|251|Fire|
|252|Floods|
|253|Storms and Thunderstorms|
|254|Volcanic Eruptions|
|255|Wonders of Nature|
|256|Aurora borealis|
|257|Comets and Shooting Stars|
|258|Eclipses|
|259|Other|
|261|Feelings Emotions and States of Mind|
|262|Anger|
|263|Aggresion|
|264|Getting Offended|
|265|Getting Upset|
|266|Rage|
|267|Happiness|
|268|Joy|
|269|Love and Romance|
|270|Pleasure|
|271|Serenity|
|272|Sharing|
|273|Smiling and Laughing|
|274|Other Feelings|
|275|Disgust|
|276|Fear|
|277|Imagination|
|278|Misunderstandings|
|279|Other|
|280|Shock|
|281|Stress|
|282|Surprise|
|283|Thoughts and Concentration|
|284|Sadness|
|285|Crying|
|286|Depression|
|287|Loneliness|
|288|Pain|
|289|Food|
|290|Accessories|
|291|Cutlery|
|292|Freezer and Fridge|
|293|Microwaves Ovens|
|294|Other|
|295|Ovens|
|296|Pans|
|297|Plates|
|299|Toaster|
|300|Bread|
|301|Cheese and Dairy|
|302|Camembert|
|303|Cheddar|
|304|Emmental and Gruyere|
|305|Feta|
|307|Other|
|309|Mozzarella|
|310|Parmesan|
|311|Cooking|
|312|Eggs|
|313|Caviar|
|314|Fish Eggs|
|315|Fried Eggs|
|316|Hen Eggs|
|317|Other|
|318|Fruit|
|319|Apples|
|320|Apricots|
|321|Bananas|
|322|Blackberries|
|323|Cherry|
|324|Clementines, Mandarines and Tangerines|
|325|Coconuts|
|326|Grapefruit|
|327|Grapes|
|328|Kiwi|
|329|Lemons|
|330|Limes|
|331|Lychees|
|332|Mangoes|
|333|Melons|
|334|Oranges|
|335|Other|
|336|Other Berries|
|337|Peaches|
|338|Pears|
|339|Pineapples|
|340|Plums|
|341|Raspberries|
|342|Strawberries|
|343|Watermelon|
|344|Grains and Cereals|
|345|Corn|
|346|Grains|
|347|Other|
|348|Sesame|
|349|Wheat|
|350|Meals|
|351|Appetizers|
|352|Bar and Buffet|
|353|Barbecue and Grills|
|354|Breakfast|
|355|Chinese Food|
|356|Creative and Gourmet Food|
|357|Fast Food and Snacks|
|358|Food Trays and Assortments|
|359|Hamburgers|
|360|Indian Food|
|361|Italian food|
|362|Japanese food|
|363|Kebabs|
|364|Korean food|
|365|Mexican Food|
|366|Mid-Eastern food|
|367|Organic Foods|
|368|Other|
|369|Other American Food|
|370|Other Arabic Food|
|371|Other Asian Food|
|372|Other European Food|
|373|Other South American Food|
|374|Picnics|
|375|Pizzas|
|376|Salads|
|377|Sandwiches|
|378|Soups|
|379|Stew|
|380|Sushis|
|381|TV Dinner|
|382|Tapas|
|383|Thai food|
|384|Vegetarian Food|
|385|Paella|
|386|Risotto|
|387|Meat|
|388|Beef|
|389|Chicken|
|390|Ham|
|391|Lamb|
|392|Other|
|393|Pork|
|394|Sausages and Salamis|
|395|Pasta|
|396|Rice|
|397|Seafood|
|398|Fish|
|399|Other|
|400|Shellfish|
|401|Spices, Herbs and Condiments|
|402|Coriander|
|403|Garlic|
|405|Mint Leaves|
|407|Olive Oil|
|408|Other|
|409|Other Herbs|
|410|Parsley|
|411|Rosemary|
|413|Shallots|
|414|Soy Sauce|
|415|Spice|
|416|Thyme|
|417|Vinegar|
|418|Sweets and Desserts|
|419|Cakes and Pastries|
|420|Candy|
|421|Chocolate|
|422|Cookies and Biscuits|
|423|Donuts|
|424|Other|
|425|Pancake|
|426|Vegetables|
|427|Avocados|
|428|Broccoli|
|429|Cabbage|
|430|Carrots|
|431|Celery|
|432|Cucumbers|
|433|Eggplants|
|434|Fennel|
|435|Lettuce|
|436|Onions|
|437|Other|
|438|Peas|
|439|Potatoes|
|440|Radish|
|441|Spinach|
|442|Tomatoes|
|443|Zucchini|
|444|Graphic ressources|
|445|Abstract|
|446|Blur|
|447|Fractals|
|448|Light and Shade|
|449|Loops and Spirals|
|450|Motion|
|451|Other|
|452|Reflections|
|453|Shapes|
|454|Backgrounds|
|455|Calendars|
|456|Greeting cards|
|457|Christmas|
|458|New Year's|
|459|Other|
|460|Signs and Symbols|
|461|3D Images|
|462|Arrows|
|463|Boards and Menus|
|464|Bubbles|
|465|Business|
|466|Buttons|
|467|Colors|
|468|Flags|
|469|Frames|
|470|Hearts|
|471|Icons|
|472|Internet|
|473|Letters and Numbers|
|474|Media Technology|
|475|Money|
|476|Other|
|477|Silhouettes|
|478|Stamps|
|479|Tattoo|
|480|Traffic Signs|
|481|Textures|
|482|Fabrics and Canvas Textures|
|483|Fire and Flame Textures|
|484|Glass Textures|
|485|Jean Textures|
|486|Leather Textures|
|487|Metal Textures|
|488|Organic and Plant Textures|
|489|Other Textures|
|490|Paper Textures|
|491|Plastic Textures|
|492|Sand Textures|
|493|Smoke Textures|
|494|Stone and Rock Textures|
|495|Vintage Textures|
|496|Water Textures|
|497|Wood Textures|
|498|Hobbies and Leisure|
|499|Art and creation|
|500|Creative Objects|
|501|Dance|
|502|Graffiti|
|503|Historial Art|
|504|Movies and Cinema|
|505|Other|
|506|Painting|
|507|Photography|
|508|Sculptures|
|509|Writing|
|510|Entertainment|
|511|Bowling|
|512|Carnival|
|513|Casinos|
|514|Cinema|
|515|Circus and Performing|
|516|Collections|
|517|Concerts|
|518|Disco and Clubs|
|519|Festivals and Parties|
|520|Knitting|
|521|Opera and Theater|
|522|Other|
|523|Pool and Billiards|
|524|Sewing|
|525|Show Business|
|526|Theme Parks|
|527|Games|
|528|Board games|
|529|Indoors|
|530|Models and Figurines|
|531|Money Games|
|532|Outdoors|
|533|Toys|
|534|Video Games|
|535|Holidays|
|536|Around the World|
|537|Beach Holidays|
|538|Camping|
|539|Chilling Out|
|540|Tourism|
|541|Winter Holidays|
|542|Home and Garden|
|543|Beds, Chairs and Furnitures|
|544|Carpets and Bedding|
|545|DIY Tools|
|546|Decorations|
|547|Gardening|
|548|Home Appliances|
|549|Housework|
|550|Keys|
|551|Other|
|552|Relaxing|
|553|Music|
|554|Electronic Devices|
|555|Listening to Music|
|556|Music Notes|
|557|Musical Accessories|
|558|Other|
|559|Other Musical Instruments|
|560|Percussion Instruments|
|561|Recording|
|562|Singing|
|563|String Instruments|
|564|Wind Instruments|
|565|Reading|
|566|Books|
|567|Magazines and Newspapers|
|568|Industry|
|569|Agriculture|
|570|Farming|
|571|Farming tools|
|572|Fields|
|573|Harvest|
|574|Other|
|575|Other Farming Vehicles|
|576|Tractors|
|577|Heavy Industry|
|578|Construction|
|579|Manufacturing|
|580|Mining|
|581|Shipping|
|582|Industrial Tools|
|583|Containers|
|584|Other|
|585|Pipes and Pipelines|
|586|Spare Parts|
|587|Machinery|
|588|Raw Materials|
|589|Coal and Ore|
|590|Copper and Zinc|
|591|Fuel and Oil|
|592|Gold|
|593|Marble|
|594|Other|
|595|Silver|
|596|Landscapes|
|597|Aerial view|
|598|Countryside|
|599|Deserts|
|600|Ice Deserts and Tundra|
|601|Other|
|602|Sand Deserts|
|603|Stone Deserts|
|604|Forests|
|605|Islands|
|606|Mountains|
|607|Canyons|
|608|Hills|
|609|Other|
|610|Peaks|
|611|Volcanoes|
|612|Nature and Wilderness|
|613|Other|
|614|Seasons|
|615|Fall|
|616|Spring|
|617|Summer|
|618|Winter|
|619|Sky|
|620|Clouds|
|621|Day|
|622|Night|
|623|Other|
|624|Sunrise and Sunset|
|625|Underwater|
|626|Urban|
|627|City Views and Skylines|
|628|Other|
|629|Pier|
|630|Public Gardens and Squares|
|631|Villages|
|632|Water|
|633|Beaches|
|634|Coastlines|
|635|Falls and Geysers|
|636|Glaciers|
|637|Lakes and Ponds|
|638|Other|
|639|Rain|
|640|Rivers|
|641|Seas and Oceans|
|642|Springs|
|643|Lifestyles|
|644|Beauty and Body Care|
|645|Aesthetic Treatments|
|646|Diet and Weight|
|647|Make Up|
|648|Men's Skincare|
|649|Nudity|
|650|Other|
|651|Skincare|
|652|Spa and Massages|
|653|Sun Tanning|
|654|Celebrations|
|655|Birthday and Anniversary|
|656|Honeymoon|
|657|Other|
|658|Wedding|
|659|Education|
|660|Exams|
|661|Graduation|
|662|High School|
|663|School|
|664|University|
|665|Fashion|
|666|Accessories|
|667|Clothes|
|668|Gems and Jewlery|
|669|Glasses|
|670|Hair Styles|
|671|Other|
|672|Pearls|
|673|Shoes|
|674|Shopping|
|675|Underwear|
|676|Watches and Clocks|
|677|Life|
|678|Birth|
|679|Death|
|680|Friendship|
|681|Risk and Danger|
|682|Safety and Security|
|683|Sleeping|
|684|Success and Achievement|
|685|Competition|
|686|Glory|
|687|Power|
|688|Pride|
|689|Respect|
|690|Wealth|
|691|Time|
|692|24/7||693|Future|
|694|Memory|
|695|People|
|696|Babies|
|703|Body parts|
|704|Arms|
|705|Back and Bum|
|706|Chest and Stomach|
|707|Faces|
|708|Feet|
|709|Hair|
|710|Hands|
|711|Legs|
|712|Other|
|713|Children|
|720|Couples|
|721|Adultery|
|722|Dating|
|723|Domestic Violence|
|724|Love and Tenderness|
|725|Other|
|726|Family Life|
|727|Divorce and Conflict|
|728|Family Value|
|729|Grand Parenting|
|730|Other|
|731|Parenting|
|732|Pregnancy|
|733|Groups and Crowds|
|734|Men|
|741|People at work|
|742|Business men and business women|
|743|Chefs|
|744|Doctors|
|745|Electricians|
|746|Firemen|
|747|Health Occupations|
|748|Hotel Business|
|749|Judicial Occupations|
|750|Mechanics|
|751|Nurses|
|752|Other|
|753|Pilots and Astronauts|
|754|Plumbers|
|755|Police, Military and Security|
|756|Scientists|
|757|Service Industry Occupations|
|758|Waiter and Waitress|
|759|Workers|
|760|Seniors|
|768|Teenagers|
|775|Women|
|782|Plants and Flowers|
|783|Flowers|
|784|Daffodils and Narcissus|
|785|Dahlias|
|786|Daisies|
|787|Dandelions|
|789|Geraniums|
|790|Iris|
|791|Lavender|
|792|Lilies|
|793|Orchids|
|794|Other|
|795|Peonies|
|796|Pooppies|
|797|Roses|
|798|Sunflowers|
|799|Tulips|
|800|Mushrooms|
|801|Ceps and Boleti|
|803|Other Edible Mushroom|
|804|Other Poisenous Mushrooms|
|805|Truffles|
|806|Plants|
|807|Bamboo|
|808|Clovers|
|809|Fern|
|810|Other|
|811|Raspberry Bush|
|812|Seeds|
|813|Trees|
|814|Apple Trees|
|815|Beech Trees|
|816|Birch Trees|
|817|Bonsais|
|818|Cherry Trees|
|819|Chestnut Trees|
|821|Maple Tree|
|822|Oak Trees|
|823|Olive Tree|
|824|Other Fruit Trees|
|825|Other Trees|
|826|Palms Trees|
|827|Pear Trees|
|828|Pine Trees|
|830|Plum Trees|
|832|Religion and Culture|
|833|International Celebrations|
|834|Christmas|
|835|Easter|
|836|Halloween|
|837|Mother's Day|
|838|New Year's Eve|
|839|Valentines day|
|840|National Events|
|841|National Days|
|842|Other Cultural Manifestations|
|843|Thanksgiving|
|844|Religion|
|845|Buddhism|
|846|Christianism|
|847|Hinduism|
|848|Islam|
|849|Judaism|
|850|Prayers, Meditation and Relaxation|
|851|Science|
|852|Applied and Fundamental Sciences|
|853|Chemistry|
|854|Engineering|
|855|Mathematics|
|856|Physics|
|857|Esoteric|
|858|Astrology and Fortune Telling|
|859|Myth and Mystery|
|860|Health and Medicine|
|861|Aging|
|862|Alternative Medicine|
|863|Dental Medicine|
|864|Disability|
|865|Disease|
|866|Drugs and Pills|
|867|Eye Care|
|868|Health Care|
|869|Hospital/ER|
|870|Hygiene|
|871|Medical Equipements|
|872|Mental Health|
|873|Pain and Symptoms|
|874|Patients|
|875|Surgery|
|876|Tests and Analysis|
|877|Veterinary Care|
|878|Outer space|
|879|Earth|
|880|Galaxies|
|881|Nebulas|
|882|Other|
|883|Planets|
|884|Stars|
|885|The Moon|
|886|The Sun|
|887|The Universe|
|888|Social Issue|
|889|Addiction|
|890|Alcohol|
|891|Drugs|
|892|Gambling|
|893|Other|
|894|Smoking|
|895|Crimes and Violence|
|896|Other|
|897|Terrorism|
|898|War|
|899|Weapons and Gun Control|
|900|Ethics|
|901|Biotechnology|
|902|Child Labour|
|903|Diversity|
|904|Freedom of Expression|
|905|Racism|
|906|Gender Issues|
|907|Contraception|
|908|Discrimination|
|909|Feminism and Women's Rights|
|910|Gay and Lesbian Rights|
|911|Prostitution|
|912|Homelessness|
|913|Illegal Immigration|
|914|Justice|
|915|Breaking the Law and Illegal Activity|
|916|Death Penalty|
|917|Equality|
|918|Social Justice and Justice Symbols|
|919|Liberty|
|920|Peace|
|921|Poverty|
|922|Sports|
|923|Extreme Sports|
|924|Bull Fighting|
|925|Climbing and Mountaineering|
|926|Martial Arts and Self Defense|
|927|Motorsports|
|928|Other|
|929|Parachuting, Hang Gliding and Paragliding|
|930|Individual Sports|
|931|Athletics|
|932|Cycling|
|933|Equestrian and Horse Riding|
|934|Exercise, Working Out and Fitness|
|935|Golf|
|936|Gymnastics|
|937|Other|
|938|Running and Jumping|
|939|Skating and Rollersports|
|940|Tennis|
|941|Matches and Competitions|
|942|Fans and Support|
|943|Goals and Victory|
|944|Loosing|
|945|Outdoor Sports|
|946|Adventure and Expedition|
|947|Fishing|
|948|Hiking|
|949|Hunting|
|950|Other|
|951|Sports Items|
|952|Balls|
|953|Other|
|954|Playing Fields|
|955|Rackets|
|956|Sports Shoes|
|957|Team Sports|
|958|Baseball|
|959|Basketball|
|960|Football|
|961|Handball|
|962|Hockey|
|963|Other|
|964|Rugby|
|965|Soccer|
|966|Volley Ball|
|967|Water Sports|
|968|Kayaking and Rafting|
|969|Kitesurfing|
|970|Other|
|971|Sailing|
|972|Scubadiving, Snorkeling and Diving|
|973|Surfing|
|974|Swimming|
|975|Windsurfing|
|976|Winter Sports|
|977|Other|
|978|Skiing|
|979|Sledding and Bobsleigh|
|980|Snowboarding|
|981|Technology|
|982|Computers|
|983|Laptops|
|984|Office Computers|
|985|Electricity|
|986|Batteries|
|987|LightBulbs and Lamps|
|988|Other|
|989|Sockets and Outlets|
|990|Electronic|
|991|Chips|
|992|Measuring Instruments|
|993|Other|
|994|Peripheral Hardware|
|995|Storage|
|996|Internet and Networks|
|997|Dialing and Conecting|
|998|E-Business|
|999|E-learning|
|1000|E-mail|
|1001|Wireless|
|1002|Wires and cables|
|1003|p2p and piracy|
|1004|Phones|
|1005|Landline phones|
|1006|Smartphones and cell phones|
|1007|Tablets and E-Books|
|1008|Videos|
|1009|DVD and DVD Readers|
|1010|Other|
|1011|TV|
|1013|Transports|
|1014|Air|
|1015|Airplanes and Aircrafts|
|1016|Balloons and Airships|
|1017|Gliders|
|1018|Helicopters|
|1019|Other|
|1020|Space Shuttles|
|1021|Boats|
|1022|Fishing boats|
|1023|Motor Boats|
|1024|Other|
|1025|Passenger Ships|
|1026|Tankers|
|1027|Yachts|
|1028|On the Road|
|1029|Bicycles|
|1030|Buses|
|1031|Cars|
|1032|Coaches|
|1033|Motorcycles and Scooters|
|1034|Other|
|1035|Rv and Small Trucks|
|1036|Traffic Jams and Road Blocks|
|1037|Trucks|
|1038|Railway|
|1039|Other|
|1040|Subways|
|1041|Trains|
|1042|Tramways and Monorails|
|1043|Travel|
|1044|Accessories and Objects|
|1045|Luggage and Suitcase|
|1046|Map|
|1047|Other|
|1048|Africa|
|1049|Algeria|
|1050|Egypt|
|1051|Kenya|
|1052|Morocco|
|1053|Other|
|1054|South Africa|
|1055|Tunisia|
|1056|America|
|1058|Argentina|
|1059|Bolivia|
|1060|Brazil|
|1061|Canada|
|1062|Caribbean|
|1063|Chile|
|1064|Great Lakes|
|1065|Mexico|
|1066|Natural Parks|
|1067|Other Central America Countries|
|1068|Other South America Countries|
|1069|United States|
|1075|Other|
|1070|American cities|
|1071|Brasilia|
|1072|Las Vegas|
|1073|Los Angeles|
|1074|New York|
|1076|Rio de Janeiro|
|1077|San Francisco|
|1078|Sao Paulo|
|1079|Washington|
|1080|Asia|
|1081|Burma|
|1082|Cambodia|
|1083|China|
|1084|India|
|1085|Indonesia|
|1086|Japan|
|1087|Korea|
|1088|Laos|
|1089|Mongolia|
|1090|Nepal|
|1091|Other|
|1092|Russia|
|1093|Sri Lanka|
|1094|Thailand|
|1095|Vietnam|
|1136|Afghanistan|
|1143|Pakistan|
|1096|Asian cities|
|1097|Beijing|
|1098|Delhi|
|1099|Hong Kong|
|1100|Kathmandu|
|1101|Moscow|
|1102|Seoul|
|1103|Shanghai|
|1104|Singapore|
|1105|Tokyo|
|1106|Xian|
|1107|Europe|
|1108|France|
|1109|Germany|
|1110|Italy|
|1111|Netherlands|
|1112|Other|
|1113|Poland|
|1114|Portugal|
|1115|Scandinavia|
|1116|Spain|
|1117|Sweden|
|1118|United Kingdom|
|1119|European Cities|
|1120|Amsterdam|
|1121|Athens|
|1122|Barcelona|
|1123|Berlin|
|1124|Brussels|
|1125|Lisbon|
|1126|London|
|1127|Madrid|
|1128|Oslo|
|1129|Paris|
|1130|Prague|
|1131|Rome|
|1132|Venice|
|1133|Vienna|
|1134|Warsaw|
|1135|Middle East|
|1137|Dubai|
|1138|Iran|
|1139|Iraq|
|1140|Israel|
|1141|Jordan|
|1142|Other|
|1144|Syria|
|1145|Turkey|
|1146|North Pole and South Pole|
|1147|Antarctic|
|1148|Arctic|
|1149|Oceania|
|1150|Australia|
|1151|New Zealand|
|1152|Other|
|1153|Other|
