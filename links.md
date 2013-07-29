Helpful links 
===================================

General Web & Development 
--------------------------

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



Laravel 4
----------------------

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



Composer Packages
----------------------------------------
Laravel package registry:
http://registry.autopergamene.eu/

Prevent artisan from running some commands (eg, migrate:refresh on a production server):
https://github.com/Itrulia/ArtisanBlock

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
"zizaco/confide": "dev-master"

Authentication & Authorization:
https://github.com/cartalyst/sentry
"cartalyst/sentry": "2.0.*" 

Another auth package:
https://github.com/AndreasHeiberg/Verify-L4
"andheiberg/verify": "2.0.*"

Server-side handling of datatables:
https://github.com/bllim/laravel4-datatables-package
"bllim/datatables": "dev-master"

I think this lets you interact with Artisan via a web form (useful if no shell access):
https://github.com/JN-Jones/web-artisan

Laravel IDE Helper - improves auto-completion:
https://github.com/barryvdh/laravel-ide-helper
"barryvdh/laravel-ide-helper": "v1.5.2"

Extra functionality for models (including auto-validation, similar to ardent):
https://github.com/betawax/role-model

Ardent Self-validating, secure and smart models:
https://github.com/laravelbook/ardent
"laravelbook/ardent": "dev-master"

Markdown compiler for Laravel 4:
https://github.com/vtalbot/markdown

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

Find the classes behind the facades:
https://github.com/experience/laravel-4-map



Testing
------------------------

PhpUnit
https://jtreminio.com/2013/03/unit-testing-tutorial-introduction-to-phpunit/

Integration Testing the DOM with a web crawler 
Information from http://symfony.com/doc/2.0/book/testing.html
http://symfony.com/doc/2.0/components/dom_crawler.html

Flexible mock objects with Mockery
http://jontai.me/blog/2012/04/flexible-mock-objects-with-mockery/

Mockery
https://github.com/padraic/mockery





Misc
-----------------------------

Easy fixtures for models
https://github.com/summerstreet/woodling

There's an interesting package called Expressive Date, at:
http://jasonlewis.me/code/expressive-date/docs

Test cases (inherit classes from these):
https://github.com/Zizaco/laravel4-test-cases




Important Project File Reference 
===================================

HTTP Response Codes:
vendor\symfony\http-foundation\Symfony\Component\HttpFoundation\Response.php

See a full list of autoloaded classes for the project (refresh via composer dump-autoload):
<project dir>/vendor/composer/autoload_classmap.php





User Interface / Front-End
===================================

Huge list of frontend resources:
https://github.com/dypsilon/frontend-dev-bookmarks

"Holy Grail" layout -- 3 equal-length full-height columns with fixed sides and fluid center:
http://alistapart.com/article/holygrail

Bootstrap-themed jQuery UI Widgets
http://addyosmani.github.io/jquery-ui-bootstrap/

Type ahead:
http://twitter.github.io/typeahead.js/

Slideshow:
http://jquery.malsup.com/cycle2/

Auto-size text fields:
http://www.jacklmoore.com/autosize/

Money masks:
http://plentz.github.io/jquery-maskmoney/

"Are You Sure?" message when leaving a page with changed data:
https://github.com/codedance/jquery.AreYouSure

JQuery modification highlighter:
http://dougestep.com/dme/jquery-modification-highlighter-widget

Canvas resize:
https://gokercebeci.com/dev/canvasresize

"Is Loading" message
https://github.com/hekigan/is-loading

Canvas functions:
http://iwhitcomb.github.io/dynamocanvas/

Data tables:
http://datatables.net/
http://www.codeproject.com/Articles/194916/Enhancing-HTML-tables-using-a-JQuery-DataTables-pl
(includes info/examples on server-side processing for thousands of records)
(also see: Server-side handling of datatables)

CKEditor:
http://ckeditor.com
http://docs.ckeditor.com/#!/guide/dev_configuration

A nice JQuery UI Date/Time picker:
https://github.com/trentrichardson/jQuery-Timepicker-Addon
http://trentrichardson.com/examples/timepicker/

"State Face" icons:
http://propublica.github.io/stateface/

Image cropping with jquery:
http://deepliquid.com/content/Jcrop.html



Backbone
------------------

cool demo of backbone.js:
http://www.sinbadsoft.com/blog/backbone-js-by-example-part-1/

Another cool demo at:
http://coenraets.org/directory/#
http://coenraets.org/blog/2012/02/sample-app-with-backbone-js-and-twitter-bootstrap/

"Hello World" of increasing complexity in backbone at:
http://arturadib.com/hello-backbonejs/



Component Management
-----------------------

Installs packages and dependencies (eg, jquery, etc.)
https://github.com/bower/bower

Another method:
https://github.com/component/component



Websockets (Persistent Connections / Real-time updates)
=========================================================

Latchet - Laravel-specific wrapper for Ratchet
https://github.com/sidneywidmer/Latchet

Ratchet - Awesome php websockets library
Ratchet: http://socketo.me/



Interesting Apps to Model / Watch / Assist?
==============================================
Laravel 4 online reservation system, YARS (Yet Another Reservation System):
https://github.com/alariva/yars

Missing pet flyer
https://github.com/msurguy/missingpetflyer



Interesting Blogs
============================
http://driesvints.com/
http://culttt.com/
http://antjanus.com/blog/
http://fideloper.com/



Forums / Ideas 
================================

Best practice for responding to AJAX request?
http://forums.laravel.io/viewtopic.php?id=2508

How do you take your project from development to production?
http://forums.laravel.io/viewtopic.php?pid=32807#p32807
