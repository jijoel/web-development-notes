Laravel Setup
=================
I do several things when setting up a new Laravel project:

    composer create-project laravel/laravel <projectName>
    composer install
    composer update

(but more often, just copy from a local installation template that I keep updated)

    cp -r _tools/laravel4 newProjectName
    cd newProjectName

Edit .gitignore:

    # Laravel
    /bootstrap/compiled.php
    /vendor

    # Composer
    composer.phar
    composer.lock

    # OS
    .DS_Store

    # Configuration
    /app/config/staging
    /app/config/production

    # Codeception
    _*

Setup permissions:

    chmod -R go+w app/storage/*

Set tabs to spaces:

    cd app
    find . ! -type d ! -name _tmp_ -exec sh -c 'expand -t 4 {} > _tmp_ && mv _tmp_ {}' \;

Initialize git:

    git init
    git commit -m 'initial commit'
    git checkout -b dev

If it's going on github, create the github project, then:

    git remote add origin https://github.com/jijoel/repo.git
    git push -u origin master
    git push -u origin dev


Namespaces
------------
If working with a namespaced app, set up an application folder (and mirrored testing folder):

    mkdir -p app/Kalani/Project/{Controllers,Interfaces,Models,Repositories,ServiceProviders,Utilities} 
    mkdir -p app/tests/Kalani/Tests/Project/{Controllers,Interfaces,Models,Repositories,ServiceProviders,Utilities} 

Add the namespaces to composer.json:

    "autoload": {
        "psr-0": {
            "Kalani\\Tests": "app/tests",
            "Kalani": "app/"
        },
        "classmap": [

Update git:

    git add .
    git commit -m 'setup namespaces'


Codeception
---------------
Setup codeception:

    codecept bootstrap
    mv tests app/tests/codeception

Edit codeception.yml:

    paths:
        tests: app/tests/codeception
        log: app/tests/codeception/_log
        data: app/tests/codeception/_data
        helpers: app/tests/codeception/_helpers
    settings:
        bootstrap: _bootstrap.php
        suite_class: \PHPUnit_Framework_TestSuite
        colors: true
        memory_limit: 1024M
        log: true
    modules:
        config:
            Db:
                dsn: 'sqlite:app/tests/codeception/_data/db.sql'
                dump: app/tests/codeception/_data/dump.sql
                user: ''
                password: ''

Edit app/tests/codeception/acceptance.suite.yml:

    class_name: WebGuy
    modules:
        enabled:
            - Selenium2
            - WebHelper
            - Db
        config:
            Selenium2:
                url: 'http://lkata/'
                browser: 'phantomjs'

Edit app/tests/codeception/functional.suite.yml:

    class_name: TestGuy
    modules:
        enabled: 
            - Filesystem
            - TestHelper
            - Laravel4
            - Db

Set up database configuration for testing. Create app/config/testing/database.php:

    <? php

    return array( 
        'default' => 'codeception',
        'connections' => array(
            'codeception' => array(
                'driver'   => 'sqlite',
                'database' => __DIR__.'/../../tests/codeception/_data/db.sqlite',
                'prefix'   => ''
            ),
        )
    );

Copy the configuration to test-foo:

    cp -r app/config/testing app/config/test-foo

Modify the app/config/test-foo/database.php as follows:

                'database' => ':memory:'

Setup folder for local projects, staging, and production:

    mkdir app/config/{local,staging,production}
    mv app/config/database.php app/config/local

Include a codeception (testing) option in app/config/local/database.php: 

    'connections' => array(

        'codeception' => array(
            'driver'   => 'sqlite',
            'database' => __DIR__.'/../tests/codeception/_data/db.sqlite',
            'prefix'   => ''
        ),

Create a bash file to load the codeception test database with migrated/seeded data. bin/prepTestDB:

    #!/bin/bash

    echo > app/tests/codeception/_data/db.sqlite

    php artisan migrate --seed --database=codeception
    sqlite3 app/tests/codeception/_data/db.sqlite .dump > app/tests/codeception/_data/dump.sql

    ls app/tests/codeception/_data

Setup permissions:

    chmod u+x bin/prepTestDB
    touch app/tests/codeception/_data/db.sqlite
    sudo chown www-data app/tests/codeception/_data
    sudo chown www-data app/tests/codeception/_data/db.sqlite

Tell Laravel to use the codeception testing environment whenever it gets a local phantomjs request. Add this to bootstrap/start.php:

    if ($env==='local'
        && isset($_SERVER['HTTP_USER_AGENT'])
        && strpos($_SERVER['HTTP_USER_AGENT'], 'PhantomJS')) 
    {
        $env = 'testing';
    }

Tell Laravel to use the test-foo environment for regular phpunit testing. Add this to app/tests/TestCase.php:

        $testEnvironment = 'test-foo';

Setup a router so that we can use both the test-foo and testing environments for testing. If in a namespaced project, it can go in the ServiceProviders folder. Otherwise, put it in a library folder (and load the folder in composer.json). Using ServiceProviders:

app/Kalani/Project/ServiceProviders/RoutingServiceProvider.php: 

    < ?php namespace Kalani\Project\ServiceProviders;

    use Illuminate\Routing\Router;
    use Illuminate\Routing\RoutingServiceProvider as LaravelRoutingServiceProvider;

    class RoutingServiceProvider extends LaravelRoutingServiceProvider 
    {
        protected function registerRouter()
        {
            $this->app['router'] = $this->app->share(function($app)
            {
                $router = new Router($app);

                // If the current application environment is "testing", we will disable the
                // routing filters, since they can be tested independently of the routes
                // and just get in the way of our typical controller testing concerns.
                if ($app['env'] == 'testing' || $app['env'] == 'test-foo')
                {
                    $router->disableFilters();
                }

                return $router;
            });
        }
    }

Add the routing service provider to the providers array in app/config/app.php:

                'Kalani\Project\ServiceProviders\RoutingServiceProvider',

Update git:

    git add .
    git rm app/config/database.php
    git commit -m 'setup testing environment'    


Development
--------------
Generate initial codeception tests:

    codecept build
    codecept generate:cest acceptance WebHome
    codecept generate:cest functional Home

Edit the acceptance test with things like this:

    public function testViewHomePage(WebGuy $I) 
    {
        $I->am('a user');
        $I->wantTo('see the home page');

        $I->amOnPage('/');
        $I->dontSee('The requested URL');
        $I->dontSee('Exception');
        $I->see('{something}', 'h1');  // Show the page is being served
        $I->see('{something}', 'li');  // Show data is being delivered
    }

    // This is an example. Contrast the commands with the Functional test, below:
    public function testAddItem(WebGuy $I)
    {
        $I->am('a user');
        $I->wantTo('add a todo');

        $I->amOnPage('/');
        $I->dontSee('new todo');
        $I->fillField('new', 'new todo');
        $I->click('New');
        $I->see('new todo');
    }

Edit the functional test to include things like this:

    public function testAddItem(TestGuy $I) 
    {
        $I->am('a tester');
        $I->wantTo('add a todo');

        $I->amOnPage('/');
        $I->dontSeeInDatabase('todos', array('name'=>'new todo'));
        $I->submitForm('#new-todo-form', array('new'=>'new todo'));
        $I->seeInDatabase('todos', array('name'=>'new todo'));    
    }

Create a test for general database and migrations. You should be using the test-foo environment, so this test should work (It will, of course, fail, until you create the migration). app/tests/DbTest.php:

    < ?php

    /**
     * @group functional
     */
    class DbTest extends TestCase
    {
        public function testDbConnectionWorks()
        {
            $this->assertNotNull(DB::connection(), 
                'connection should not be null');
        }

        /**
         * @dataProvider getTableConstructors
         */
        public function testCreateTables($table, $constructor)
        {
            $constructor->up();
            $this->assertTrue(Schema::hasTable($table));
        }

        /**
         * @dataProvider getTableConstructors
         */
        public function testDropTables($table, $constructor)
        {
            $constructor->down();
            $this->assertFalse(Schema::hasTable($table));
        }

        public function getTableConstructors()
        {
            return array(
                array( 'todos', new CreateTodosTable ),
            );
        }

    }

Create a migration:

    php artisan migrate:make create_some_table

Edit the migration:

    < ?php

    use Illuminate\Database\Migrations\Migration;
    use Illuminate\Database\Schema\Blueprint;

    class Create{Name}Table extends Migration 
    {

        /**
         * Run the migrations.
         *
         * @return void
         */
        public function up()
        {
            Schema::create('{name}', function(Blueprint $table) {
                $table->increments('id');
                $table->string('name',40);
                $table->timestamps();
            });
        }

        /**
         * Reverse the migrations.
         *
         * @return void
         */
        public function down()
        {
            Schema::dropIfExists('{name}');
        }
    }

Create a test for routes (app/test/routes.php). This should be a functional test that ultimately verifies that every exposed route in the app is reachable (eg, the method exists):

    <?php

    /**
     * @group functional
     */
    class RoutesTest extends TestCase 
    {
        public function setUp()
        {
            parent::setUp();

            Artisan::call('migrate');
            Artisan::call('db:seed');
        }

        /**
         * @dataProvider getRoutes
         */
        public function testGetRoutes($route, $params)
        {
                $this->call('GET', route($route));
                $this->assertResponseOk();

                if ($params) {
                    $this->assertViewHas($params);
                }
        }

        public function getRoutes()
        {
            return array(
                array('home', 'items'),
            );
        }
    }

Add routes to the database. In routes.php:

    Route::get('/', array('as'=>'home', 'uses'=>'TodosController@index'));
    Route::resource('todos', 'TodosController');
    Route::resource('todos', 'TodosController', array(
        'only'=>array('index', 'store', 'destroy')));

