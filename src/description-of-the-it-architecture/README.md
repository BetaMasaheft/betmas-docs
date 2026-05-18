# About the BetMas web application

The [https://github.com/BetaMasaheft/BetMas](https://github.com/BetaMasaheft/BetMas) repository contains only the  `eXist-db` application, without data, namely only the code used by the web application displaying the data of the project. All the javasscript dependencies that are typically stored in `resources` collection are not copied in this repository.

At the moment, this is not the master repo of the application. It is a copy for transparency and version tracking purposes, which reproduces what is currently online. Once the new version is ready and online, the code is updated here and the release note going with it explains what has changed.

The Beta Masaheft web application works, at the moment, with two of the application building models availble in [eXist-db](https://exist-db.org), with most functions that have been taken from examples already available. If authors feel they are not adequately akwnowledged, please tell us.


# About the BetMas data

The BetMas data is stored in Git repositories in [this group](https://github.com/BetaMasaheft), and is pushed to the live web application via webhooks in each repository which point to the `gitsyncREPONAME.xql` files in the `modules` collection. 

The BetMas data is stored in the following Git repositories:

* [Manuscripts](https://github.com/BetaMasaheft/Manuscripts), containing the manuscript XML files in `draft` status.

* [Works](https://github.com/BetaMasaheft/Works), containing Ethiopian Literature edited in TEI (XML) format.

* [Persons](https://github.com/BetaMasaheft/Persons), containing authority files for each person.

* [Places](https://github.com/BetaMasaheft/Places), containing any place mentioned in the catalogue.

* [Institutions](https://github.com/BetaMasaheft/Institutions), containing the institutions where manuscripts are preserved.

* [Studies](https://github.com/BetaMasaheft/Studies), containing works of interest to Ethiopian and Eritrean studies.

* [Authority Files](https://github.com/BetaMasaheft/Authority-Files), containing places, people and taxonomies for manuscripts and works.

* [Narrative](https://github.com/BetaMasaheft/Narrative), containing the narrative units.


# Description of the BetMas web application


## Model / view / controller 

The parts of the app using this technology use functions in modules which need to be imported by `view.xql`, the XQuery script which calls the templates module and matches the `data-template` attributes in the html with the XQuery functions.


### Templates

- [search.html](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/search.html) is used by the advanced search, namely [as.html](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/as.html);

- templates/searchR.html is used by search.html??

- templates/list.html?? is used by index like resources 

- templates/page.html?? is used by the index page and documentation pages


### Advanced search form

The initial form in [as.html](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/as.html) uses the controller template model with the template [search.html](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/search.html), which calls a the Javascript script [filters.js](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/resources/js/filters.js), which, on click, loads with AJAX the selected form*.html file. Each of these contains a call to a function `app:NAMEofTHEform` which will call `app:formcontrol` which will call `app:selectors`. This allows the form to be flexibly adjusted and load only what is needed.

The number of parameters needed does not allow to do this (to my current knowledge) with RESTXQ, unfortunately.


### The index page
The index page calls some functions to display the data and give an introduction to the project.


### The navigation bar
The navigation bar is called in the templates or from the RESTXQ modules, and is the primary navigation aid. The standard navbar is in [modules/nav.xqm](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/modules/nav.xqm), but some templates needing modification of it have their own.


### Other static pages

The search results are presented in a html page in this way from search.html.

The API documentation is hand produced in [apidoc.html](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/apidoc.html).

The breakdown of the work from the Team is displayed in [team.html](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/team.html).

The basic information, including version about the app is in [appInfo.html](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/appInfo.html).


### Resources / indexes

The indexes under `Resources` use the `model / view / controller` system to load from the called html a single function producing the result. These functions are in [modules/resources.xqm](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/modules/resources.xqm).


### Explicit TEI

The [xslt/post.xsl](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/xslt/post.xsl) transformation is called directly by the controller for any request with the pattern `/tei/resource.xml`. This is different from calling simply `resource.xml` which will return the source file.


## RESTXQ APIs


### Items View

The module [modules/items.xqm](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/modules/items.xqm) is the RESTXQ module which maps the request for a specific resource in the database to a series of views, text, entry, xml (see above), and relations.


### List View

The module [modules/list.xqm](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/modules/list.xqm) produces the list views for resources in the app and some filter options.


### Comparison

The module [modules/compare.xqm](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/modules/compare.xqm) is another RESTXQ module, which calls the function producing the carousel with the manuscripts containing one selected text.


### DTS

The module [modules/dts.xqm](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/modules/dts.xqm) implements the [DTS specification](https://dtsapi.org/specifications/), but still only partially and not according to implementation guidelines, in order to serve text from BM.


### IIIF

The module [modules/iiif.xqm](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/modules/iiif.xqm) takes data from the manuscripts in TEI format and produces dinamically manifests according to the IIIF presentation API. Most uris are dereferencable. Structures and Ranges are used to locate and make available for navigation relevant information in the TEI file.

 
### Gazetteer (Pelagios Interconnection format) and Pelagios annotations

The module [modules/places.xqm](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/modules/places.xqm) produces annotations according to the [Pelagios format](https://github.com/LinkedPasts/linked-places-format/) and the gazetteer of places in the [Pelagios interconnection format](https://github.com/pelagios/pelagios-cookbook/wiki/Pelagios-Gazetteer-Interconnection-Format) (it is to be noted that the former, now called `Linked Places` format, is the current standard).


## Other XQuery modules

- The modules [modules/config.xqm](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/modules/config.xqm) and [modules/app.xqm](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/modules/app.xqm) are used by the app in general.

- The module [modules/coordinates.xqm](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/modules/coordinates.xqm) contains functions which produce coordinates and place informations for other modules.

- The module [modules/error.xqm](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/modules/error.xqm) contains the error messages for RESTXQ functions using it.

- The module [modules/item.xqm](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/modules/item.xqm) contains functions used for the item view and called by the [modules/items.xqm](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/modules/items.xqm) RESTXQ module.

- The module [modules/workmap.xqm](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/modules/workmap.xqm) contains the functions used to produce maps in the app.

- The module [modules/timeline.xqm](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/modules/timeline.xqm) contains the functions used to produce timelines.

- The module [/modules/titles.xqm](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/modules/titles.xqm) contains the functions used to print the titles based on element or id.

- The module [modules/view.xql](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/modules/view.xql) is the core module of the controller model.

- The viewer is the RESTXQ module containing the [resources/js/mirador.js](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/resources/js/mirador.js) for images display (it could have lived in [modules/items.xqm](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/modules/items.xqm)).

- The module [modules/relations.xqm](https://github.com/BetaMasaheft/BetMas/blob/master/db/apps/BetMas/modules/relations.xqm) has a series of functions used to produce nodes and edges for [vis.js](https://visjs.org/).


## Javascript usage

### General

- [jquery](https://jquery.com/): versions used are 1.1, 1.7, 1.11, and 1.12; the latest version is 4.0.

- [Bootstrap](https://getbootstrap.com/): version used is 3.0; the latest version is 5.3.


### Vis.js

This library [vis.js](https://visjs.org/) is used for graphs and timelines.


### Mottie js Keyboard

We use this very nice [virtual keyboard](https://github.com/Mottie/Keyboard), to which we have contributed an Ethiopic keyboard, which can be used both with predefined combination (documented in combo.html??) or holding the selected character. 


### Bootstrap-slider

The library [bootstrap-slider](https://github.com/seiyria/bootstrap-slider) is used for the date and other sliders in search filters. The version used is 9.5; the latest version is 11.1.


### slick

The [slick](http://kenwheeler.github.io/slick/) library is used for the comparison of manuscripts for the carousel. The version used is 1.6; the latest version is 1.8.


### Mirador

[Mirador](https://projectmirador.org/) is used to view images from the IIIF manifests.


### OpenSeadragon

[OpenSeadragon](https://openseadragon.github.io/) is used to view images in the catalogue entry view. The version used is 2.1.0; the latest version is 6.0.2.


### Awdl??

This does the small popups on links to perseus, geonames, wikipedia.
