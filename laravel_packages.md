Laravel Packages
===================

Instructions at:  http://culttt.com/2013/06/24/creating-a-laravel-4-package/

Install a new instance of laravel:

    composer create-project laravel/laravel foo
    chmod -R go+w app/storage/*

Edit app/config/workbench.php to insert critical information, then run:

    php artisan workbench Vendor/PackageFoo

I get a new folder structure that looks like this:

    <project>/workbench/vendor/package-foo/src/Vendor/PackageFoo

Enter this in the providers array of app/config/app.php:

    'Vendor\PackageFoo\PackageFooServiceProvider', 

Set up files as follows:

Modify workbench/vendor/package-foo/src/Vendor/PackageFoo/PackageFooServiceProvider:

    public function register()
    {
        $this->app['foo'] = $this->app->share(function($app){
          return new Foo;
        });

        $this->app->booting(function()
        {
          $loader = \Illuminate\Foundation\AliasLoader::getInstance();
          $loader->alias('Foo', 'Vendor\PackageFoo\Facades\Foo');
        });     
    }

    public function provides()
    {
        return array('Foo');
    }

Create workbench/vendor/package-foo/src/Vendor/PackageFoo/Facades/Foo:

    <?php namespace Vendor\PackageFoo\Facades;
     
    use Illuminate\Support\Facades\Facade;
     
    class Foo extends Facade 
    {
        protected static function getFacadeAccessor() { return 'foo'; }
    }

Create workbench/vendor/package-foo/src/Vendor/PackageFoo/Foo.php:

    <?php namespace Vendor\PackageFoo;

    class Foo
    {
        public function foo()
        {
            return 'bar';
        }
    }

Create workbench/vendor/package-foo/tests:

    <?php

    class FooTest extends PHPUnit_Framework_TestCase
    {
        public function testSetup()
        {
            $foo = new \Vendor\PackageFoo\Foo;
            $this->assertEquals('\\Vendor\\PackageFoo\\Foo', get_class($foo));
            $this->assertEquals('bar', $foo->foo());
        }
    }

