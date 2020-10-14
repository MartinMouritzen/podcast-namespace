# The "podcast" Namespace

A wholistic rss namespace for podcasting that is meant to synthesize the fragmented world of podcast namespaces.  The broad goal is to create one namespace to rule them all, that is easily extensible, community controlled/authored and addresses the needs of the independent podcast industry now and in the future.

The large podcast platforms have shown virtually no interest in extending their namespaces for new functionality in many years.  Our hope is that this namespace will become the framework that the independent podcast community needs to deliver new functionality to apps and aggregators.


## Goal #1 - Eliminate Redundancy

There is significant overlap amongst the many existing podcast namespaces.  Each platform and publisher has created their own namespace to give their respective system and audience the metadata they need in the way they want it delivered.


## Goal #2 - Minimize Attributes and Sub-elements

Attributes in xml elements should be used only where absolutely needed.  The preference is to create a new element type, rather than reuse the same element with different attributes.

For example, instead of using `<podcast:image type="Large">`, we would use `<podcast:imageLarge>`.  This makes the corresponding aggregator code easier and more linear.  Sub-elements should be avoided if at all possible.


## Goal #3 - Keep "required" tags minimal

The only required tags should be those that solve an overwhelming need in the industry.  Requiring tags is a roadblock to adoption and should be avoided.  Attributes should also only be required when they are key to the functionality of the tag.


## Goal #4 - Keep Exisiting Conventions

Reinventing the wheel helps nobody.  When at all possible, existing conventions should be maintained.  For example, it would make sense to turn **\<podcast:explicit>** into a unary element, where it's existence is taken as a "yes" and it's absence as a "no".  But, that has never been the standard.  And, given as how this namespace will probably sit alongside at least one other namespace, it makes sense to keep existing conventions in place.


## Goal #5 - Be General... to a point

There is no way to address every possible metadata point that each platform would want.  That is not the aim.  Instead we focus on defining the elements that would be useful to the broadest set of apps, publishers, platforms and aggregators.  Individual parties can keep their respective supplemental namespaces small and targeted as an adjunct to this larger namespace.  But, we don't want to be so general that the spec becomes overly complicated.  A beautiful, "perfect" spec is not important.  Solving real problems is.


# The Guiding Principles

Our guiding principles for development of this namespace is "[Rules for Standards Makers](http://scripting.com/2017/05/09/rulesForStandardsmakers.html)" by Dave Winer.  Please read it before contributing if you aren't familiar with it.


## Element List

### Phase 1 (Open)

> `<podcast:locked owner="[podcast owner email address]">[yes or no]</podcast:locked>`
  Channel
  (required | single)

   This tells other podcast platforms whether they are allowed to import this feed.  A value of "yes" means that any attempt to import    this feed into a new platform should be rejected.  It is expected that podcast hosting providers will enable a toggle in their GUI to allow their users to turn feed transfer lock on or off.

   The owner attribute is an email address that can be used to verify ownership of this feed during move and import operations.  This could be a public email or a virtual email address at the hosting provider that redirects to the owner's true email address.  This is a critical element, and it's expected that podcast hosting providers (if not providing virtual addresses) will allow setting this element's value in their GUI with an emphasis to their users of how important it is to have this be a valid, working email address.


> `<podcast:transcript url="[url to a file or website]" type="[mime type]" rel="captions" language="[language code]" />`
   Item
   (optional | multiple)

   Links to an external file containing a transcript or closed captions file, which may actually be the same file.  The mime type of the file should be given - such
   as `text/plain`, `text/html`, `application/srt`, `application/json`.  If there is no language attribute given, the linked file is assumed to be the same language that is
   specified by the RSS \<language\> element.  If the rel="captions" attribute is present, the linked file is considered to be a closed captions file, regardless of what the
   mime type is.  In that scenario, time codes are assumed to be present in the file in some capacity.


> `<podcast:chapterstart="[(int or float)]" title="[(string)]" href="[uri to content]" type="[mime type]" />`
>   Item
>   (optional | multiple)

   This element specifies a point in time during the podcast that can be linked to directly and optionally supplemented with additional content.
   `start` is required to identify the starting point of the chapter within the podcast. Start time is expressed as seconds since the start time of the podcast.
   `title` is optional and is used as a user facing identifier for this chapter.
   `href` is optional and points to additional user facing content.
   `type` is optional but it is strongly encouraged if `href` is used so that clients can handle the chapter content appropriately.


>`<podcast:location latlon="[latitude,longitude]" (osmid="[OSM type][OSM id]")>[CountryCode(|Locality)]</podcast:location>`
   Channel (required | single)
   Item (optional | multiple)

   This element is required at the channel level.  And, it MUST contain, at minimum, a latlon point and a country code.  Although, an OSM specification is highly recommended.
   
 - latlon: (required) A latitude/longitude point reflecting the location associated with this show or episode. This could be where it is made, or alternatively a location which features in the podcast.
    - osmid: (recommended) From the OpenStreetMap API. If a value is given for osmid it must contain both 'type' and 'id'.
        - osm type: A one-character description of the type of OSM point. Valid is "N" (node); "W" (way); "R" (relation).
        - osm id: The ID of the OpenStreetMap feature that is described. This may be a city or a building. While OSM IDs are not considered permanent, cities rarely disappear.
    - CountryCode: (required) The ISO 3166-1 alpha-2 country code, eg 'US'. (Note that the United Kingdom is 'GB', not 'UK'.)
    - Locality: (recommended) With a pipe separator from the countrycode, this is a humanly-readable place name as preferred by the podcast publisher.


> `<podcast:person name="[(string)]" role="[(string)]" img="[(uri of content)]" href="[(uri to website/wiki/blog)]" />`
   Channel (optional | multiple)
   Item (optional | multiple)

   This element specifies a *person of interest* to the podcast
   `name` is required and is the full name or alias of the person.
   `role` is optional and is used to identify what role the person has for the show or episode. If missing, on the channel element, "host" is assumed, on the Item element "guest" is assumed.
   `img` is optional and is meant for a photo or graphical representation of the person
   `href` is optional and is a link to a relevant resource of information about the person. (eg. website, blog or wiki entry).


> `<podcast:contact type="[feedback or advertising or abuse]" method="[email or link]">[email address or url]</podcast:contact>`
   Channel
   (optional | multiple)

   This element allows for listing different contact methods for the podcast owner.  This could be for general feedback, advertising inquiries, abuse reports, etc.

   All attributes are required.


> `<podcast:alternateEnclosure type="[mime type]" length="[(int)]" bitrate="[(float)]" title="[(string)]" stream>[uri of media asset]</podcast:alternateEnclosure>`
   Channel (optional | single)
   Item (optional | multiple)

   This element is meant to provide alternate versions of an enclosure, such as low or high bitrate, or alternate formats or alternate uri schemes, like IPFS or live streaming.  There may be multiple alternateEnclosure elements in an item, but there must be no more than one in a channel.
   
   The presence of this element at the    channel level would be useful for adding a video/audio trailer or intro media that introduces the listener to the podcast.  For instance, in a podcast of an audiobook, this could be the book's    introduction or preface.  The alternateEnclosure element always refers to an "alternate" media version.  The standard RSS enclosure element is always the default media to be    played.

   All attributes are required except for `"stream"`, which is a boolean attribute that indicates the uri points to a streaming media that is not downloadable.


> `<podcast:id platform="[service slug]">[the id string]</podcast:id>`
   Channel
   (optional | multiple)

   See "ID's" in this document for an explanation.

   All attributes are required.


> `<podcast:imageLarge size="[pixel width]">[url to a large image file]</podcast:imageLarge>`
   Channel or Item
   (optional | single)

   This is assumed to point to an image that is 1000px or larger in size.  The image must be square (1:1 ratio).  The image content may differ from other images specified in
   the feed where appropriate.

   All attributes are required.


> `<podcast:imageMedium size="[pixel width]">[url to a medium image file]</podcast:imageMedium>`
   Channel or Item
   (optional | single)

   This is assumed to point to an image that is 300px to 999px in size.
   The image must be square (1:1 ratio).  The image content may differ from other images specified in the feed where appropriate.

   All attributes are required.


> `<podcast:imageSmall size="[pixel width]">[url to a small image file]</podcast:imageSmall>`
   Channel or Item
   (optional | single)

   This is assumed to point to an image that is 299px or less in size.
   The image must be square (1:1 ratio).  The image content may differ from other images specified in the feed where appropriate.

   All attributes are required.


> `<podcast:funding platform="[service slug]" title="[user provided note]">[url for the show at the platform]</podcast:funding>`
   Channel or Item
   (optional | multiple)

   This element lists multiple possible donation/funding links for the podcast.



### Phase 2 (Open)

> `<podcast:social platform="[service slug]" url="[link to social media account]">[social media handle]</podcast:social>`
   Channel or Item
   (optional | multiple)

   This element lists social media accounts for this podcast.  The service slugs should be community written into the accompanying serviceslugs.txt file.


> `<podcast:category>[category Name]</podcast:category>`
   Channel
   (optional | multiple)

   See "Categories" in this document for an explanation.  There can be up to a total of 9 categories defined.

   There can be a maximum of 9 category elements defined in a feed.  Any number greater than that should be discarded.

   Category names are defined in the accompanying "categories.json" file in this repository.  They should be referenced in the element by their textual name.
   The characters can be in any case.  This list of categories aims to replicate the current standard but also eliminate as much as possible compound, heirarchical
   naming and the use of ampersands.  Thus, "Health & Fitness" becomes "Health" and "Fitness" as two distinct categories.  And, "Religion & Spirituality" becomes
   two separate categories.  Again, they are different things that don't always go together.  Splitting them allows for more flexible combinations.  And, avoiding
   ampersands makes xml encoding errors less likely.


> `<podcast:contentRating>[rating letter]</podcast:contentRating>`
  Channel or Item
  (optional | single)

  Specifies the generally accepted rating letter of G, PG, PG-13, R or X.  Or, perhaps an age rating system like all, 14, 19, adult.  Needs discussion.


> `<podcast:previousUrl>[url this feed was imported from]</podcast:previousUrl>`
   Channel
   (optional | multiple)

   Lists the previous url of this feed before it was imported.  Any time a feed is moved, an additional **\<podcast:previousUrl>** element
   should be added to the channel, to create a paper trail of all the previous urls this feed has lived at.  This way, aggregators can easily deduplicate their feed lists.


> `<podcast:newFeedUrl>[the url the feed now lives at]</podcast:newFeedUrl>`
   Channel
   (optional | single)

   If the feed moved, or was imported to a different hosting platform, this element may exist and specify the new location.  It may refer
   to itself as a confirmation to aggregators that they now have the most current url.



## Verification for Importing and Transferring

If the `"locked"` element is present and set to `"yes"`, podcasting hosts should not allow importing of this feed until the email listed in the element's `owner=""` attribute is contacted and subsequently changes the value of the element to `"no"`.



## ID's

Their can be multiple `<podcast:id>` elements to indicate a listing on multiple platforms, directories, hosts, apps and services.  The "platform" attribute shall be a slug
representing the platform, directory, host, app or service. The slugs will look like this:

- blubrry
- captivate
- podcastindex
- fireside
- transistor
- libsyn
- buzzsprout
- apple
- google
- spotify
- anchor
- overcast

More should be added by the community as needed.  This is just a starter list.


## Example feed

There is an example feed (example.xml) in this repository showing the podcastindex namespace side by side with the Apple itunes namespace.
