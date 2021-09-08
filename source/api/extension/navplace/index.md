---
title: navPlace Extension
layout: spec
tags: [extension, navPlace]
cssversion: 2
---

## Status of this Document
{:.no_toc}

This document is not subject to [semantic versioning][notes-versioning].
Changes will be tracked within the document.

{% include copyright.md %}

## Table of Contents
{:.no_toc}

* Table of Discontent (will be replaced by macro)
{:toc}

## 1. Introduction

The concept of place is foundational to human communication and understanding. Locations are quantitative descriptions of the position of a place, independent of time and other human or physical characteristics of such place. The concept of location is a first class descriptor for resources, and so needs a unique property. In IIIF Presentation API 3 there is no property designed for a geographic location.


### 1.1 Objectives and Scope

This IIIF Presentation API 3 extension describes a new property, `navPlace`, which is defined by earthbound geographic coordinates in the form of [GeoJSON-LD](https://geojson.org/geojson-ld/). Clients may use this property to leverage the functionality of web-based map platforms such as Google Earth, Leaflet, OpenLayers, etc., providing a means to enrich data presentation through map-based interfaces.

Spatial coordinates for resources on other celestial bodies or contrived worlds can be expressed using the semantic pattern of GeoJSON. However, `navPlace` adopts the [existing GeoJSON specification](https://datatracker.ietf.org/doc/html/rfc7946) to promote interoperability with industry standard mapping libraries and methods using [WGS84](http://www.w3.org/2003/01/geo/wgs84_pos) as the coordinate reference system for projections of the surface of Earth. As such, expressing the location of extraterrestrial entities is not supported by the `navPlace` property. This extension does not preclude the development of future extensions to address this use case.


### 1.2 Motivating Use Cases

The reasons for associating geographic coordinates with web resources vary greatly. This extension does not intend to meet the data requirements for all geospatial practices. Use cases within the scope include


*   Link a IIIF resource to a single or multiple geographic areas
*   Supply a single geographic bounding box for an image of a map
*   Represent locations that appear in digital objects, such as itineraries or travel journals

Situations which are not in scope include


*   Georeferencing and map warping
*   3D spatial representation
*   [Application of geographic information to resource fragments](https://iiif.io/api/cookbook/recipe/0139-geolocate-canvas-fragment/)


### 1.3 Terminology

This extension uses the following terms:

* __embedded__: When a resource (A) is embedded within an embedding resource (B), the complete JSON representation of resource A is present within the JSON representation of resource B, and dereferencing the URI of resource A will not result in additional information. Example: Canvas A is embedded in Manifest B.
* __referenced__: When a resource (A) is referenced from a referencing resource (B), an incomplete JSON representation of resource A is present within the JSON representation of resource B, and dereferencing the URI of resource A will result in additional information. Example: Manifest A is referenced from Collection B.
* __HTTP(S)__: The HTTP or HTTPS URI scheme and internet protocol.
* __location__: the quantitative description of the position of a place, in this case, using coordinates.
* __GeoJSON__: "The GeoJSON Format" specification published at [https://datatracker.ietf.org/doc/html/rfc7946](https://datatracker.ietf.org/doc/html/rfc7946).
* __GeoJSON-LD__: The Linked Data implementation of the GeoJSON specification published at [https://geojson.org/geojson-ld/](https://geojson.org/geojson-ld/).
* __linked data context__: A reference to a [JSON-LD context file](https://www.w3.org/TR/json-ld11/#the-context) as described by JSON-LD 1.1

The terms _array_, _JSON object_, _number_, _string_, and _boolean_ in this document are to be interpreted as defined by the [Javascript Object Notation (JSON)][org-rfc-8259] specification.

The key words _MUST_, _MUST NOT_, _REQUIRED_, _SHALL_, _SHALL NOT_, _SHOULD_, _SHOULD NOT_, _RECOMMENDED_, _MAY_, and _OPTIONAL_ in this document are to be interpreted as described in [RFC 2119][org-rfc-2119].


## 2. The `navPlace` Property and GeoJSON-LD


### 2.1 `navPlace` Property

The `navPlace` property identifies a single or multiple geographic areas pertinent to a resource using a GeoJSON Feature Collection containing one or more Features. These areas _SHOULD_ be bounded discrete areas of the map akin to extents. These areas do not imply any level of accuracy, temporality, or state of existence. 


```json-doc
{
   "navPlace":{
      "id": "http://example.com/feature-collection/1",
      "type": "FeatureCollection",
      "features":[
         {
            "id": "http://example.com/feature/1",
            "type": "Feature",
            "properties":{},
            "geometry":{
               "type": "Point",
               "coordinates":[
                  9.938,
                  51.533
               ]
            }
         }
      ]
   }
}
```


The `navPlace` property can be used with the [IIIF Presentation 3 API Defined Types](https://iiif.io/api/presentation/3.0/#21-defined-types).



*   A Collection _MAY_ have the `navPlace` property.<br/>
   Clients _MAY_ render `navPlace` on a Collection.
*   A Manifest _MAY_ have the `navPlace` property.<br/>
   Clients _MAY_ render `navPlace` on a Manifest.
*   A Range _MAY_ have the `navPlace` property.<br/>
   Clients _MAY_ render `navPlace` on a Range.
*   A Canvas _MAY_ have the `navPlace` property.<br/>
   Clients _MAY_ render `navPlace` on a Canvas.
*   Other types of resource _MUST NOT_ have the `navPlace` property.<br/>
   Clients _SHOULD_ ignore `navPlace` on other types of resource.

The `navPlace` property's value follows a specific pattern.



*   The value _SHOULD_ be an embedded Feature Collection. However, the value _MAY_ be a referenced GeoJSON Feature Collection. 
*   Feature Collections referenced in the `navPlace` property _MUST_ have the `id` and `type` properties. The reference object _MUST NOT_ have the `features` property, such that clients are able to recognize that it should be retrieved in order to be processed. <br/>
```json-doc
{"navPlace":{"id": "https://example.org/iiif/1/feature-collection", "type": "FeatureCollection"}}
```


### 2.2 GeoJSON

This extension utilizes the [GeoJSON specification](https://tools.ietf.org/html/rfc7946). Values for the `navPlace` property follow the conventions for [GeoJSON Feature Collections](https://datatracker.ietf.org/doc/html/rfc7946#section-3.3).


#### 2.2.1 GeoJSON as Linked Data

[GeoJSON-LD](https://geojson.org/geojson-ld/) is a publicly available vocabulary and linked data context for the GeoJSON specification. The `navPlace` extension context file contains this context and reference to the vocabulary which ensures the geographic data values are well described. The example below shows how to set the `@context` property for IIIF resources with the `navPlace` property. [Section 3](#3-linked-data) has more detail on linked data compatibility.



```json-doc
{
    "@context":[ 
       "http://iiif.io/api/extension/navplace/context.json",
       "http://iiif.io/api/presentation/3/context.json"
    ]
}
```


#### 2.2.2 Feature

A Feature represents a spatial bounding. Every Feature is a GeoJSON object. [Go to the GeoJSON specification for a visual example](https://datatracker.ietf.org/doc/html/rfc7946#section-1.5). Note that the IIIF Presentation API 3 value requirements for the `id` property are in effect over the GeoJSON specification `id` property value recommendations. In this document, the term "Feature" relates to "Feature Object" from the GeoJSON specification.


*   A Feature _MUST NOT_ be NULL.
*   A Feature has a `type` property with the value "Feature".
*   A Feature has a property with the name `geometry`. The value of the `geometry` property _SHALL_ be either a Geometry object as defined in the [Table of Geometric Shapes](#225-table-of-geometric-shapes) or, in the case that the Feature is unlocated, a JSON null value.
*   A Feature has a property with the name `properties`. The value of the `properties` property is an object (any JSON object or a JSON null value). For information on using this property to provide information associated with the geographic coordinates, see [Section 3.2](#32-context-considerations-for-geojson-ld-properties).
*   A Feature _MAY_ have an `id` property. The value of this property _MUST_ be a JSON string or number. If the Feature has a [commonly used HTTP(S) URI identifier](https://iiif.io/api/presentation/3.0/#61-uri-recommendations), it _SHOULD_ be provided as the value of the `id` property. The `id` _MAY_ be the URI of a Feature Collection that contains the Feature with a unique fragment on the end. The Feature _MAY_ be accessible by the URI. 


#### 2.2.3 Feature Collection

A Feature Collection represents an aggregation of spatial boundings. [Go to the GeoJSON specification for a visual example](https://datatracker.ietf.org/doc/html/rfc7946#section-1.5). Note that the IIIF Presentation API 3 value requirements for the `id` property are in effect over the GeoJSON specification `id` property value recommendations. In this document, the term "Feature Collection" relates to "Feature Collection Object" from the GeoJSON specification.


*   A Feature Collection _MUST NOT_ be NULL.
*   A Feature Collection has a `type` property with the value "FeatureCollection".
*   A Feature Collection has a property with the name `features`. The value of `features` is a JSON array. Each element of the array is a Feature as defined above. It is possible for this array to be empty, but when used in the context of this extension it _SHOULD NOT_ be empty.
*   A Feature Collection _MAY_ have an `id` property. The value of this property _MUST_ be a JSON string or number. If the Feature Collection has a [commonly used HTTP(S) URI identifier](https://iiif.io/api/presentation/3.0/#61-uri-recommendations), it _SHOULD_ be provided as the value of the `id` property. The Feature Collection _MAY_ be accessible by the URI.

#### 2.2.4 Position

A position is the fundamental geometry construct and contains a `coordinates` property. A position is an array of numbers. There _MUST_ be two or more elements. The first two elements are longitude and latitude, or easting and northing, precisely in that order and using decimal numbers. Altitude or elevation _MAY_ be included as an optional third element.


#### 2.2.5 Table of Geometric Shapes

| Geometry Object  |    Description| 
|----|----|
| Point  |  The "coordinates" property is a single position| 
| MultiPoint |  The "coordinates" property is an array of positions| 
| LineString |  The "coordinates" property is an array of two or more positions| 
| MultiLineString   |   The "coordinates" property is an array of LineString coordinate arrays| 
| Polygon   |   The "coordinates" property _MUST_ be an array of linear ring coordinate arrays. For Polygons with more than one of these rings, the first _MUST_ be the exterior ring, and any others _MUST_ be interior rings. The exterior ring bounds the surface, and the interior rings (if present) bound holes within the surface.| 
| MultiPolygon  |   The "coordinates" property is an array of Polygon coordinate arrays| 
| GeometryCollection  | Has a property with the name "geometries". The value of "geometries" is an array. Each element of this array is a GeoJSON Geometry object. It is possible for this array to be empty. | 
{: .api-table}

For examples of these shapes, see the “Examples” section of the GeoJSON specification at [https://datatracker.ietf.org/doc/html/rfc7946#appendix-A](https://datatracker.ietf.org/doc/html/rfc7946#appendix-A)


## 3. Linked Data


### 3.1 Linked Data Context


*   The URI of the `navPlace` linked data context is [`http://iiif.io/api/extension/navplace/context.json`](http://iiif.io/api/extension/navplace/context.json)
*   The URI of the IIIF Presentation API 3 linked data context is [`http://iiif.io/api/presentation/3/context.json`](http://iiif.io/api/presentation/3/context.json)

The navPlace extension linked data context _MUST_ be included before the IIIF Presentation API 3 linked data context on the top-level object. The navPlace extension linked data context file includes the [GeoJSON-LD context](https://geojson.org/geojson-ld/geojson-context.jsonld) through [context scoping](https://www.w3.org/TR/json-ld11/#scoped-contexts). This means the GeoJSON-LD context URI does not have to be explicitly included on the top level object. It is important to note that since the IIIF Presentation API 3 linked data context has the JSON-LD `@version` set to 1.1, all linked data contexts are processed as JSON-LD 1.1.  

Consult the [Linked Data Context and Extensions section of IIIF Presentation API 3](https://iiif.io/api/presentation/3.0/#46-linked-data-context-and-extensions) for further guidance on use of the `@context` property.

### 3.2 Context Considerations for GeoJSON-LD `properties`

The value of `properties` can be any JSON object and is used to supply additional information associated with the geographic coordinates. Terms used in `properties` _SHOULD_ be described either by [registered IIIF API extensions](https://iiif.io/api/extension/#abstract) or [local linked data contexts](https://www.w3.org/TR/json-ld11/#dfn-local-context). If a client discovers properties that it does not understand, then it _MUST_ ignore them.

## 4. Full Manifest Example

Here you can see an example of a IIIF Manifest with the `navPlace` property. It is made JSON-LD 1.1 compatible by including multiple linked data contexts to cover all the terms used within the web resource. Review the Manifest below in the [JSON-LD playground](https://json-ld.org/playground/) for an example of Linked Data processing. 


```json-doc
{
   "@context":[
      "http://iiif.io/api/extension/navplace/context.json",
      "http://iiif.io/api/presentation/3/context.json"
   ],
   "id":"https://example.org/iiif/manifest/1",
   "type":"Manifest",
   "label":{
      "en":[
         "navPlace Extension Example Manifest"
      ]
   },
   "items":[
      {
         "id":"https://example.org/iiif/canvas/p1",
         "type":"Canvas",
         "label":{
            "en":[
               "Picture of Göttingen taken during the 2019 IIIF Conference"
            ]
         },
         "type":"Canvas",
         "height":3024,
         "width":4032,
         "items":[
            {
               "id":"https://example.org/iiif/page/p1/1",
               "type":"AnnotationPage",
               "items":[
                  {
                     "id":"https://example.org/iiif/annotation/p0001-image",
                     "type":"Annotation",
                     "motivation":"painting",
                     "label":{
                        "en":[
                           "Picture of Göttingen"
                        ]
                     },
                     "body":{
              "id":"https://iiif.io/api/image/3.0/example/reference/918ecd18c2592080851777620de9bcb5-gottingen/full/max/0/default.jpg",
                        "type":"Image",
                        "format":"image/jpeg",
                        "height":3024,
                        "width":4032,
                        "service":[
                           {
                              "id":"https://iiif.io/api/image/3.0/example/reference/918ecd18c2592080851777620de9bcb5-gottingen",
                              "profile":"level1",
                              "type":"ImageService3"
                           }
                        ]
                     },
                     "target":"https://example.org/iiif/canvas/p1"
                  }
               ]
            }
         ]
      }
   ],
   "navPlace":{
      "id" : "https://example.org/iiif/feature-collection/2",
      "type":"FeatureCollection",
      "features":[
         {
            "id":"https://example.org/iiif/feature/2",
            "type":"Feature",
            "properties":{
               "label":{
                  "en":[
                     "navPlace Extension Example Manifest"
                  ]
               }
            },
            "geometry":{
               "type":"Point",
               "coordinates":[
                  9.938,
                  51.533
               ]
            }
         }
      ]
   }
}
```

## 5. Implementation Notes


### 5.1 GeoJSON-LD on the Web

The choice to use GeoJSON-LD is not arbitrary. It is the primary geographic coordinate data format supported by web-based map platforms. This extension drives implementers to have geographic coordinate data saved as GeoJSON-LD when it is intended for use on the web. This practice removes barriers for interoperable geographic coordinate data as well as promotes the robust functionality of prior, current, and upcoming web-based map platform developments.


### 5.2 IIIF Cookbook

The [IIIF Cookbook](https://iiif.io/api/cookbook/) exemplifies implementations of digital objects under IIIF Presentation API 3. IIIF Cookbook recipes exemplifying use of the `navPlace` property are under development. This specification will update to include links to those recipes as they are published.


## Appendices

### A. Acknowledgements

An enormous amount of gratitude is owed to the IIIF community for their continuous engagement, innovative ideas, and feedback. Thanks to [Sean Gilles](https://github.com/sgillies) and MapBox for the GeoJSON linked data context and its promotion of standardized geographic web data. We would also like to recognize the [IETF](https://www.ietf.org/) for the semantics produced through the GeoJSON specification. An extra special thank you goes out to the [IIIF Maps](https://iiif.io/community/groups/maps/) and [IIIF Cookbook](https://iiif.io/api/cookbook/) communities as implementers of this developing technology. The initial version of this document was the work of the [IIIF Maps Technical Specification Group](https://iiif.io/community/groups/maps-tsg/).
{: #acknowledgements}


### B. Change Log
{: #change-log}

| Date       | Description           |
| ---------- | --------------------- |
| 2021-06-01 | Initial commit        |
| 2021-06-28 | Updated copy over from Google Doc draft|
{: .api-table #table-changelog}

{% include acronyms.md %}

{% include links.md %}
