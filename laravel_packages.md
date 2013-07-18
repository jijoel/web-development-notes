Laravel Packages
===================

Instructions at:  http://culttt.com/2013/06/24/creating-a-laravel-4-package/

    composer create-project laravel/laravel schema-interface
    chmod -R go+w app/storage/*

Edit some settings in app/config/workbench.php, then set up the workbench:

    php artisan workbench --resources Kalani/SchemaInterface

I get a new folder structure that looks like this:

    ~/projects/web/schema-interface/workbench/kalani/schema-interface/src/Kalani/SchemaInterface

My namespace will be:

    namespace Kalani\SchemaInterface

In the SchemaInterface package, I want to put these objects:

    SchemaController
    SchemaModel
    TableSchema
    ValidationRuleGenerator

First thing to do is create a test in workbench/kalani/schema-interface/tests:

    <?php

    class SchemaControllerTest extends PHPUnit_Framework_TestCase
    {
        public funtion testWorks()
        {
            $this->assertTrue(False);
        }
    }

Running phpunit from /cygdrive/c/wamp/www/active/lkata/workbench/kalani/schema-interface...
It fails. We can begin...

