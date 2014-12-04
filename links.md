Helpful links 
===================================

Helpful links on a variety of topics:

* [General Web & Development](#general)
* [Laravel 4](#laravel)
* [Composer Packages](#composer)
* [Testing](#testing)
* [Misc](#misc)
* [Important Project File Reference](#project-files)
* [User Interface / Front End](#ui)
    * [Front-End Frameworks](#ui-frontend)
    * [General Layout](#ui-layout)
    * [Tables](#ui-tables)
    * [UI / View Controls](#ui-controls)
    * [Canvas](#ui-canvas)
    * [Validators / Notifiers](#ui-validators)
    * [Forms / Data-Entry Controls](#ui-forms)
    * [Forms / Date/Time](#ui-datetime)
    * [Forms / Select Boxes](#ui-select)
    * [Forms / Textbox Editing](#ui-textbox)
    * [File Managers](#ui-filemanager)
    * [Graphics/Media](#ui-graphics)
    * [Misc](#ui-misc)
* [Backbone](#backbone)
* [Component Management](#components)
* [Web Components](#webcomponents)
* [Websockets (Persistent Connections / Real-time updates)](#websockets)
* [Interesting Apps to Model / Watch / Assist?](#apps)
* [Interesting Blogs](#blogs)
* [Forums / Ideas ](#forum)
* [APIs](#api)



General Web & Development <a name="general">
---------------------------------------------

HTTP Response Codes
http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html

Composer Package Directory
http://packagist.org

REST API
http://net.tutsplus.com/tutorials/php/laravel-4-a-start-at-a-restful-api/

Error handling in REST
http://satishgopal.wordpress.com/tag/rest-error-handling/

Style guide for web authors
http://med.stanford.edu/modelsite/styles/

Supported Timezones:
http://php.net/manual/en/timezones.php

GPG Cheatsheet:
http://irtfweb.ifa.hawaii.edu/~lockhart/gpg/


Laravel 4 <a name="laravel">
-------------------------------------

Installing Laravel
http://niallobrien.me/2013/03/installing-and-updating-laravel-4/

Testing Laravel Models
http://net.tutsplus.com/tutorials/php/testing-like-a-boss-in-laravel-models/

Laravel 4 IoC and Facades
http://www.thenerdary.net/post/30859565484/laravel-4

Dependency Injection and IoC in Laravel 4 Controllers
http://www.nathandavison.com/posts/view/16/using-dependency-injection-and-ioc-in-laravel-4-controllers

Excellent tutorial on a user management system:
https://gist.github.com/anchetaWern/4223764

Laravel starter site
https://github.com/andrew13/Laravel-4-Bootstrap-Starter-Site

Routing / Cross-Origin-Resource-Sharing 
http://acairns.co.uk/2013/01/routing-and-cors-with-laravel-4/

Doctrine documentation:
http://docs.doctrine-project.org/projects/doctrine-dbal/en/latest/index.html



Composer Packages <a name="composer">
-------------------------------------------------------
Laravel package registry:
http://registry.autopergamene.eu/

Way Generators
https://github.com/JeffreyWay/Laravel-4-Generators
"way/generators": "dev-master"

Prevent artisan from running some commands (eg, migrate:refresh on a production server):
https://github.com/Itrulia/ArtisanBlock

DocBlock parser:
"phpdocumentor/reflection-docblock": "2.0.*@dev"
https://github.com/phpDocumentor/ReflectionDocBlock

Presenter:
https://github.com/robclancy/presenter
"robclancy/presenter": "dev-master"

Image handling and manipulation:
http://creolab.hr/2013/07/image-manipulation-in-laravel-4-with-imagine/
http://intervention.olivervogel.net/image
"intervention/image": "dev-master"

More image handling and manipulation:
https://github.com/Zweer/php-images

Laravel Administrator (seems to be a nice front-end for accessing tables, etc.):
http://administrator.frozennode.com/
https://github.com/FrozenNode/Laravel-Administrator
"frozennode/administrator": "dev-master"

Laravel 4 package unit testing helper  (this has a lot of dependencies...):
https://github.com/orchestral/testbench
"orchestra/testbench": "2.0.*"

Classes to help write tests for laravel:
https://github.com/Zizaco/testcases-laravel

Role-based permissions for Laravel 4:
https://github.com/Zizaco/entrust
"zizaco/entrust": "dev-master"

Authentication (by same person who built entrust):
https://github.com/Zizaco/confide
"zizaco/confide": "dev-master"  (needs PHP 5.4)

Authentication & Authorization:
https://github.com/cartalyst/sentry
"cartalyst/sentry": "2.0.*" 

Authentication/ACL admin panel:
https://github.com/intrip/laravel-authentication-acl

Another auth package:
https://github.com/Toddish/Verify-L4
"toddish/verify": "dev-master"

OAuth (login via facebook, github, google, etc.)
https://github.com/madewithlove/laravel-oauth2

Server-side handling of datatables:
https://github.com/bllim/laravel4-datatables-package
"bllim/datatables": "dev-master"
also https://github.com/Chumper/Datatable

I think this lets you interact with Artisan via a web form (useful if no shell access):
https://github.com/JN-Jones/web-artisan

Laravel IDE Helper - enables auto-completion for Laravel in Sublime/PHPStorm:
https://github.com/barryvdh/laravel-ide-helper
"barryvdh/laravel-ide-helper": "v1.5.2"

Extra functionality for models (including auto-validation, similar to ardent):
https://github.com/betawax/role-model

Ardent Self-validating, secure and smart models:
http://laravelbook.github.io/ardent/
https://github.com/laravelbook/ardent
"laravelbook/ardent": "dev-master"

Markdown compiler for Laravel 4:
https://github.com/vtalbot/markdown

Markdown:
https://github.com/michelf/php-markdown/

User-available configuration settings (config loader via database):
https://bitbucket.org/hailwoodnz/database-config-loader
"hailwood/database-config-loader": "dev-master"

Simple menu builder:
https://bitbucket.org/purposemedia/menu

Alternate formatting for rules 
(eg, 'username'  => Rule::required()->alphaDash()->between(3, 100))
https://github.com/bigelephant/laravel-rules

File attachments:
https://github.com/CodeSleeve/stapler

File uploads:
https://github.com/andrew13/cabinet

Theme and asset management:
https://github.com/teepluss/laravel4-theme
"teepluss/theme": "dev-master"

Themes:
http://docs.cartalyst.com/themes-2/overview

Find the classes behind the facades:
https://github.com/experience/laravel-4-map

Multi-gateway payment processing library:
https://github.com/adrianmacneil/omnipay
"omnipay/omnipay": "1.*"

Breadcrumbs:
https://github.com/davejamesmiller/laravel-breadcrumbs

History tracking:
https://github.com/VentureCraft/revisionable

Form Builder:
https://github.com/Anahkiasen/former

Reposed (an extended model class):
https://github.com/kapv89/reposed

Shopping Cart:
https://github.com/moltin/laravel-cart

Laravel Nested Set:
https://github.com/lazychaser/laravel4-nestedset

Create/work with PDF files:
https://github.com/barryvdh/laravel-dompdf
"barryvdh/laravel-dompdf": "dev-master"

http://www.tcpdf.org/index.php
"tecnick.com/tcpdf": "dev-master"

HTML -> PDF Converter (with some Laravel4 wrappers):
https://github.com/dompdf/dompdf
"barryvdh/laravel-dompdf": "dev-master"
"thujohn/pdf": "dev-master"

FPDF (possibly included in distro as php-fpdf):
http://www.fpdf.org/
"itbz/fpdf": "dev-master"

FPDI (FPDF Document Importer; import/annotate pdf files):
(also works with templates)
http://www.setasign.com/products/fpdi/downloads/

Online payments
http://payum.forma-dev.com/

E-Commerce:
http://sylius.org/



Testing <a name="testing">
---------------------------------------

PhpUnit
https://jtreminio.com/2013/03/unit-testing-tutorial-introduction-to-phpunit/

Integration Testing the DOM with a web crawler 
Information from http://symfony.com/doc/2.0/book/testing.html
http://symfony.com/doc/2.0/components/dom_crawler.html

Flexible mock objects with Mockery
http://jontai.me/blog/2012/04/flexible-mock-objects-with-mockery/

Mockery
https://github.com/padraic/mockery





Misc <a name="misc">
--------------------------------------------

Easy fixtures for models
https://github.com/summerstreet/woodling

There's an interesting package called Expressive Date, at:
https://github.com/jasonlewis/expressive-date

Test cases (inherit classes from these):
https://github.com/Zizaco/laravel4-test-cases

Descriptions, ratings, and demos of various software products:
http://demo.softaculous.com/enduser/index.php?

Themosis Framework for WordPress developers:
http://framework.themosis.com/


Important Project File Reference <a name="project-files">
-------------------------------------------------------------

HTTP Response Codes:
vendor\symfony\http-foundation\Symfony\Component\HttpFoundation\Response.php

See a full list of autoloaded classes for the project (refresh via composer dump-autoload):
<project dir>/vendor/composer/autoload_classmap.php





User Interface / Front-End <a name="ui">
---------------------------------------------

Lots of stuff on the UI. Here are some things:

* [Front-End Frameworks](#ui-frontend)
* [General Layout](#ui-layout)
* [Tables](#ui-tables)
* [UI / View Controls](#ui-controls)
* [Canvas](#ui-canvas)
* [Validators / Notifiers](#ui-validators)
* [Forms / Data-Entry Controls](#ui-forms)
* [Forms / Date/Time](#ui-datetime)
* [Forms / Select Boxes](#ui-select)
* [Forms / Textbox Editing](#ui-textbox)
* [File Managers](#ui-filemanager)
* [Graphics/Media](#ui-graphics)
* [Misc](#ui-misc)



### Front-End Frameworks <a name="ui-frontend">

HTML Kickstart (similar to bootstrap)
http://www.99lime.com/elements/

Semantic web components (similar to bootstrap):
http://semantic-ui.com/

Template rendering engine:
http://tempojs.com/

http://foundation.zurb.com/



### General Layout <a name="ui-layout">

Flexbox:
https://www.adobe.com/devnet/html5/articles/working-with-flexbox-the-new-spec.html

"Holy Grail" layout -- 3 equal-length full-height columns with fixed sides and fluid center:
http://alistapart.com/article/holygrail
http://philipwalton.github.io/solved-by-flexbox/demos/holy-grail/

Packery (dynamically rearrange things on pages):
http://packery.metafizzy.co/faq.html


### Tables <a name="ui-tables">

Data tables:
http://datatables.net/
http://www.codeproject.com/Articles/194916/Enhancing-HTML-tables-using-a-JQuery-DataTables-pl
https://github.com/DataTables/DataTables.git
(includes info/examples on server-side processing for thousands of records)
(also see: Server-side handling of datatables)

Another data grid (Flexigrid; not updated often):
http://flexigrid.info/

A data grid based on backbone:
http://loicfrering.github.io/backbone.datagrid/

Another data grid (looks very nice):
https://github.com/mleibman/SlickGrid/wiki/Examples

Keep static (floating) table headers:
http://mkoryak.github.io/floatThead

Bootstrap Table (fixed headers, show/hide columns, sortable, card view, etc. Awesome!)
http://wenzhixin.net.cn/p/bootstrap-table/docs/index.html
https://github.com/wenzhixin/bootstrap-table


### UI / View Controls <a name="ui-controls">

Carousel:
https://kenwheeler.github.io/slick/

Slideshow:
http://jquery.malsup.com/cycle2/

Syntax highlighting:
http://prismjs.com/
http://highlightjs.org/

Animation:
https://daneden.me/animate/
http://coding.smashingmagazine.com/2011/09/14/the-guide-to-css-animation-principles-and-examples/

Three-dimensional menu (move mouse to left side to open)
http://lab.hakim.se/meny/

Presentations (eg, powerpoint in HTML)
http://lab.hakim.se/reveal-js


### Canvas <a name="ui-canvas">

Canvas resize:
https://gokercebeci.com/dev/canvasresize

Canvas functions:
http://iwhitcomb.github.io/dynamocanvas/


### Validators / Notifiers <a name="ui-validators">

Credit card number validator:
http://paweldecowski.github.io/jQuery-CreditCardValidator/

JQuery modification highlighter:
http://dougestep.com/dme/jquery-modification-highlighter-widget

Front-end data validation:
http://reactiveraven.github.io/jqBootstrapValidation/

"Are You Sure?" message when leaving a page with changed data:
https://github.com/codedance/jquery.AreYouSure

"Is Loading" message
https://github.com/hekigan/is-loading

"growler" notification boxes:
https://github.com/ifightcrime/bootstrap-growl

Pop-up dialog boxes:
http://docs.toddish.co.uk/popup/

Simple modal dialog box:
http://www.ericmmartin.com/projects/simplemodal/


### Forms / Data-Entry Controls <a name="ui-forms">

Bootstrap-themed jQuery UI Widgets
http://addyosmani.github.io/jquery-ui-bootstrap/

Front-end widgets:
http://alloyui.com/

Bootstrapper for Laravel:
http://bootstrapper.aws.af.cm/
(this is an old Laravel bundle)

Additional controls for bootstrap (including a wizard):
http://exacttarget.github.io/fuelux
    Checkbox    Combobox    Datagrid    Datepicker    Pillbox    Preloader    Radio
    Scheduler   Search      Select      Spinner       Tree       Wizard

Plugins for extending bootstrap:
http://tutorialzine.com/2013/07/50-must-have-plugins-for-extending-twitter-bootstrap/

Twitter Bootstrap Wizard
http://vadimg.com/twitter-bootstrap-wizard-example/#
https://github.com/VinceG/twitter-bootstrap-wizard

Another Bootstrap Wizard
http://www.panopta.com/2013/02/06/bootstrap-application-wizard-2/

Money masks:
http://plentz.github.io/jquery-maskmoney/

Type ahead:
http://twitter.github.io/typeahead.js/

Tag autocomplete (like twitter hashes, etc.):
http://sandglaz.github.io/bootstrap-tagautocomplete/

Autocomplete:
http://hillelcoren.com/flex-autocomplete/

In-place editing for jquery (works with ajax):
http://www.appelsiini.net/projects/jeditable
http://vitalets.github.io/x-editable/


### Forms / Date/Time <a name="ui-datetime">

A nice JQuery UI Date/Time picker:
https://github.com/trentrichardson/jQuery-Timepicker-Addon
http://trentrichardson.com/examples/timepicker/

Bootstrap-compatible date picker:
http://eternicode.github.io/bootstrap-datepicker/

Calendar:
https://github.com/arshaw/fullcalendar
https://github.com/jarnokurlin/fullcalendar    (includes resource views)


### Forms / Select Boxes <a name="ui-select">

Prettier select boxes:
http://harvesthq.github.io/chosen/
http://brianreavis.github.io/selectize.js/

Pretty select box; searching, remote data sets, infinite scrolling:
(awesome, but the select box grows as items are added)
http://ivaynberg.github.io/select2/

Skins for select2 to work with Bootstrap3:
https://github.com/t0m/select2-bootstrap-css/tree/bootstrap3
http://fk.github.io/select2-bootstrap-css/

Pretty select box; groupable, stays on one line
http://wenzhixin.net.cn/p/multiple-select/docs/
https://github.com/wenzhixin/multiple-select

Multiselection widget:
(old/abandoned)
http://www.erichynds.com/blog/jquery-ui-multiselect-widget

Tree-style multiselect:
http://travistidwell.com/jquery.treeselect.js/
https://github.com/travist/jquery.treeselect.js

Multiselect with Inactive/Active lists (two boxes)
http://loudev.com/
https://github.com/lou/multi-select

Type ahead
http://twitter.github.io/typeahead.js/examples/



### Forms / Textbox Editing <a name="ui-textbox">

Auto-size text fields (autogrow textarea input type):
http://www.jacklmoore.com/autosize/

Javascript editor -- CKEditor:
http://ckeditor.com
http://docs.ckeditor.com/#!/guide/dev_configuration

Another nice Javascript editor -- TinyMCE:
http://www.tinymce.com/index.php


#### File Managers <a name="ui-filemanager">

Editors can upload files with a file manager. Some interesting ones:

http://www.moxiemanager.com/   (paid, but very nice)

https://github.com/barryvdh/laravel-elfinder
https://github.com/Studio-42/elFinder
http://elfinder.org/

(I like the interface, but it's slow...):
https://github.com/simogeo/Filemanager

Interface for File Uploads:
http://blueimp.github.io/jQuery-File-Upload/index.html

Other jquery-based file managers:
http://www.jquery4u.com/plugins/10-jquery-file-manager-plugins/


### Graphics/Media <a name="ui-graphics">

Font Awesome (various icons):
http://fortawesome.github.io/Font-Awesome/

"State Face" icons:
http://propublica.github.io/stateface/

Image cropping with jquery:
http://deepliquid.com/content/Jcrop.html

Before/After image slider:
https://github.com/NUKnightLab/juxtapose


### Misc  <a name="ui-misc">

Huge list of frontend resources:
https://github.com/dypsilon/frontend-dev-bookmarks



Backbone <a name="backbone">
---------------------------------

cool demo of backbone.js:
http://www.sinbadsoft.com/blog/backbone-js-by-example-part-1/

Another cool demo at:
http://coenraets.org/directory/#
http://coenraets.org/blog/2012/02/sample-app-with-backbone-js-and-twitter-bootstrap/

"Hello World" of increasing complexity in backbone at:
http://arturadib.com/hello-backbonejs/


AngularJS <a name="angularjs">
----------------------------------
Example of using list/detail for an RSS reader:
http://vojtajina.github.io/WebApp-CodeLab/FinalProject/index.html
https://github.com/GoogleChrome/wReader-app

AngularJS directives for Bootstrap:
http://mgcrea.github.io/angular-strap/




Component Management <a name="components">
--------------------------------------------

Installs packages and dependencies (eg, jquery, etc.)
https://github.com/bower/bower

Another method:
https://github.com/component/component


Web Components <a name="webcomponents">

Introduction to Web Components
http://slides.com/jasonmayes/introduction-to-web-components

http://webcomponents.org


Websockets (Persistent Connections / Real-time updates) <a name="websockets">
----------------------------------------------------------------------------

Latchet - Laravel-specific wrapper for Ratchet
https://github.com/sidneywidmer/Latchet

Ratchet - Awesome php websockets library
Ratchet: http://socketo.me/



Search
-------------
(client and server)
http://sphinxsearch.com/docs/2.1.2/
https://github.com/scalia/sphinxsearch
http://www.andrehonsberg.com/article/install-sphinxsearch-205-in-ubuntu-1204-server

https://lucene.apache.org/solr/



Code Review
--------------------
http://css.dzone.com/articles/automated-code-reviews-php


Interesting Apps to Model / Watch / Assist? <a name="apps">
-------------------------------------------------------------------------
Laravel 4 online reservation system, YARS (Yet Another Reservation System (looks dead...)):
https://github.com/alariva/yars

Missing pet flyer
https://github.com/msurguy/missingpetflyer

Awesome Invoicing System:
https://www.invoiceninja.com/
https://github.com/hillelcoren/invoice-ninja

Laravel Tricks Site:
https://github.com/CodepadME/laravel-tricks


Interesting Blogs <a name="blogs">
---------------------------------------------
http://driesvints.com/
http://culttt.com/
http://antjanus.com/blog/
http://fideloper.com/
http://maxoffsky.com/category/code-blog/
http://keltdockins.com/tags/laravel/
https://medium.com/laravel-4/


Forums / Ideas <a name="forum">
---------------------------------------------

Best practice for responding to AJAX request?
http://forums.laravel.io/viewtopic.php?id=2508

How do you take your project from development to production?
http://forums.laravel.io/viewtopic.php?pid=32807#p32807

Uploading files?
http://forums.laravel.io/viewtopic.php?id=2022

Validating an input array and normal inputs.
(eg, 'items' => 'required|min:1|integerOrArray')
http://forums.laravel.io/viewtopic.php?id=13146
http://stackoverflow.com/questions/18161785/validation-of-array-form-fields-in-laravel-4-error/18163546#18163546

Validation services:
http://jasonlewis.me/article/laravel-advanced-validation

Events:
http://jasonlewis.me/article/laravel-events

Controller/Layout testing:
http://stackoverflow.com/questions/16607310/laravel-controller-layout-testing

View Composers:
http://heera.it/laravel-4-view-composer-master-layout#.UmTaM_l00RU

Multi-select filter search:
http://stackoverflow.com/questions/18127040/multi-select-filter-search-in-laravel-4

Beanstalkd (a work queue, which can run queued jobs) with Laravel:
http://fideloper.com/ubuntu-beanstalkd-and-laravel4

Ruby / Hexagonal Architecture
http://www.youtube.com/watch?v=CGN4RFkhH2M

Design Patterns in PHP
http://github.com/domnikl/DesignPatternsPHP

Wizard with Bootstrap and AngularJS:
http://onehungrymind.com/ng-animate-first-look-with-angularjs-wizard/




APIs <a name="api">
--------------------
http://help.infusionsoft.com/api-docs
http://api.namastelight.com/
