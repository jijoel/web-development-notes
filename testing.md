Testing
==========

This document contains notes related to testing, using these tools:

* [Test Driven Development (TDD)](#tdd)
* [phpunit](#phpunit)
    * [Testing for exceptions](#phpunit-exceptions)
    * [Data Providers](#phpunit-data-providers)
* [Mockery](#mocks)
* [Laravel Techniques](#laravel)
    * [Call/Response](#laravel-call)
    * [Web Crawler](#laravel-web-crawler)
        * [Browsing](#laravel-client-browsing)
        * [Accessing Internal Objects](#laravel-client-internals)
        * [Redirecting](#laravel-client-redirection)
        * [Crawler](#laravel-crawler)
    * [Laravel 4 IoC and Facades](#laravel-ioc)
    * [Mock Input](#laravel-mock-input)
    * [Mocking a Facade](#laravel-mock-facade)
    * [In-memory database and test environment](#laravel-memory-db)
    * [Testing with an Array Repository](#laravel-array-repo)
    * [Testing Without a Repository Pattern](#laravel-no-repo)
    * [Testing Filters](#laravel-filters)
    * [Additional stuff for testing](#laravel-extra)
* [Selenium](#selenium)
* [BDD and Acceptance Testing with Codeception](#codeception)




Test Driven Development (TDD) <a name="tdd">
--------------------------------------------------

This is a development technique that helps create more reliable code over time.

* Begin by writing a test that will fail
* Test it (make sure it fails)
* Write the code to make the test pass
* Test it (make sure it passes)
* Refactor (make sure all tests still pass)



phpunit <a name="phpunit">
----------------------------
I'm using phpunit for testing. It will read configuration information from phpunit.xml, in the root directory of the project.

#### Incomplete tests:
Skip them by putting one of these at the beginning of the test:

```php
    $this->markTestIncomplete();

    $this->markTestSkipped(
      'The MySQLi extension is not available.'
    );
```

(these can be entered in the setUp routine for all tests, to skip all. The parser will still go through everything to find class definitions, etc., though)

#### phpUnit test groups
We can only test some groups at a time:

```php
    /**
     * @group database
     * @group remoteTasks
     */
    public function testSomething()
    {
    }
```

testSomething() is now in two groups, and if either is added on the command line (or in the config.xml) --exclude-group parameter. it won't be run.

This won't run integration tests tagged with @group integration:

    phpunit --exclude-group integration         

This will run only tests tagged with @group integration:

    phpunit --group integration                 

Tags for phpunit can be put on either classes or individual functions. This means we can make a tag for the current test, and move it to new tests as we work on them. Then, run phpunit for the current test only, bypassing all other tests. (it's a good idea to run all tests from time to time, like just before I take a short break).

    phpunit --group now

We can also include or exclude specific test groups in the phpunit.xml file:

``` xml
    <?xml version="1.0" encoding="UTF-8"?>
    <phpunit backupGlobals="false"
            backupStaticAttributes="false"
            bootstrap="bootstrap/autoload.php"
            colors="true"
            convertErrorsToExceptions="true"
            convertNoticesToExceptions="true"
            convertWarningsToExceptions="true"
            processIsolation="false"
            stopOnFailure="false"
            syntaxCheck="false"
    >
        <testsuites>
            <testsuite name="Application Test Suite">
                <directory>./app/tests/</directory>
            </testsuite>
        </testsuites>

        <groups>
          <exclude>
            <group>integration</group>
            <group>failing</group>
          </exclude>
        </groups>

    </phpunit>
```

(this will automatically exclude the integration and failing test groups, that is anything tagged with @group integration  or  @group failing)

To use a different configuration file, use:

    phpunit -c other_config_file.xml

To generate documentation, use:

    phpunit --testdox

To show code coverage, use:

    phpunit --coverage-html <folder>

You can then to go the specified folder for marked-up coverage. It shows lines of code that have been run in green, other lines in red. It will also show everything in the framework that has been run. 

    
#### phpUnit Test Dependencies

Make a test dependent on success of a previous test (eg, the previous test is required to pass, or we won't run this one):

``` php 
    public function testEmpty()
    {
        $stack = array();
        $this->assertTrue(empty($stack));
        return $stack;   // also sends this variable to any following tests - if this worked
    }

    /**
     * only runs if testEmpty() passed
     *
     * @depends testEmpty
     */
    public function testPush(array $stack)
    {
    }
```


#### Testing for exceptions <a name="phpunit-exceptions">

We can make sure that an exception is thrown by using a docblock:

    /*
     * @expectedException ExceptionName
     */
    public function testThrowsExceptionOnError() {
        // do something that should throw an exception
    }

    
#### Data Providers<a name="phpunit-data-providers">

If you have a lot of tests that are basically similar, these can be set in by a data provider.
For instance, if you have this:

```php
    public function testGetFieldData() {
        $fields = $this->schema->getFields();
        $this->assertEquals('id', $fields['id']['name']);
        $this->assertEquals('Text1', $fields['text1']['display']);
        $this->assertEquals('Object Identifier', $fields['oid']['display']);
    }
```

... you're basically doing the same thing over and over again. For more dry code, you can do something like this:

```php
    public function inputFieldData() {
        return array(
            array('id', 'id', 'name'),
            array('Text1', 'text1', 'name'),
            array('Object Identifier', 'oid', 'display'),
        );
    }
    
    /*
     * @dataProvider inputFieldData
     */
    public function testGetFieldData($expected, $field, $attr) {
        $fields = $this->schema->getFields();
        $this->assertEquals($expected, $field, $attr);
    }
```

For a small set of tests, this may be overkill, but you can repeat the data in other tests, and easily add new data.






Laravel Techniques <a name="laravel">
==========================================

* [Call/Response](#laravel-call)
* [Web Crawler](#laravel-web-crawler)
    * [Browsing](#laravel-client-browsing)
    * [Accessing Internal Objects](#laravel-client-internals)
    * [Redirecting](#laravel-client-redirection)
    * [Crawler](#laravel-crawler)
* [Laravel 4 IoC and Facades](#laravel-ioc)
* [Mocking a Facade](#laravel-mock-facade)
* [In-memory database and test environment](#laravel-memory-db)
* [Testing with an Array Repository](#laravel-array-repo)
* [Additional stuff for testing](#laravel-extra)


Call/Response <a name="laravel-call">
----------------------------------------
When unit testing, we can call functions and get the responses. We can also do this with mocks. The basic format is like this:

```php
    $response = $this->action('GET', 'ItemsController@show', array('1'));
    $response = $this->call('GET', '/items/1');
    $this->assertTrue($response->isOk());
    $this->assertNotEmpty($response->getContent());
```

The response class has several useful functions, including:

    getStatusCode()
    getContent()                    // returns string with final html page 
    getOriginalContent()            // returns View that creates final html page
    headers->get('header-name')

Response Helper Functions:

    Function Name           Status Codes
    isInvalid()             <100 and >=600
    isInformational()       >=100 and <200
    isSuccessful()          >=200 and <300
    isRedirection()         >=300 and <400
    isClientError()         >=400 and <500
    isServerError()         >=500 and <600
    isOk()                  200
    isForbidden()           403
    isNotFound()            404
    isRedirect()            201,301,302,303,307,308
    isEmpty()               201,204,304
    isNotModified()
 
use like:

    if ($response->isOK)  doSomething;

The view class that is returned also has valuable information:

    $result->original->getData();               // data sent to the view
    $result->getOriginalContent()->getData();   // data sent to the view (same)
    $result->original->getEngine();             // templating engine (eg, blade)
    $result->original->getName();               // name of the view (eg todo/index)



Web Crawler <a name="laravel-web-crawler">
---------------------------------------------
The Symphony web crawler component will go through the DOM of the page to handle very specific test cases. It can be used during integration tests to simulate a browser (much faster than Selenium).

We can see if there is a div with id 'item' like this:

```php
    public function testSampleItemIsInAnItemDiv()
    {
        $crawler = $this->client->request('GET', '/');
        $this->assertGreaterThan(0, $crawler->filter('div.item')->count());
    }
```

We can get information for children in the DOM chain, with:

```php
    $children = $crawler->filter('div.item')->first()->children();
    echo $children->filter('h4')->count();
    foreach($children as $child) {
        echo($child->tagName);
        echo($child->getAttribute('class'));
        echo($child->nodeValue);
    }
```

Other stuff:

```php
    // Click a link:

        $crawler = $this->client->request('GET', '/user/login');
        $link = $crawler->filter('a:contains("Greet")')->eq(1)->link();
        $crawler = $client->click($link);
     
    // Find the submit button on a form:

        $form = $crawler->selectButton('submit')->form();
     
    // set some values

        $form['name'] = 'Lucas';
        $form['form_name[subject]'] = 'Hey there!';
     
    // submit the form

        $crawler = $client->submit($form);
      
    // Assert that the response matches a given CSS selector.

        $this->assertGreaterThan(0, $crawler->filter('h1')->count());
```

Test against the Response content directly if you just want to assert that the content contains some text, or if the Response is not an XML/HTML document:

```php
    $this->assertRegExp(
        '/Hello Fabien/',
        $client->getResponse()->getContent()
    );
```

Force each request to be executed in its own PHP process to avoid any side-effects when working with several clients in the same script:

    $client->insulate();
 
```php
// Search through all content on page for a string

    $crawler = $this->client->request('GET', '/user/login');
    $this->assertTrue($this->client->getResponse()->isOk(), 'should have an OK response');
    $this->assertContains('Please Log In', 
        $this->client->getResponse()->getContent(), 'should contain "Please Log In" ');
 
// Find a specific string in a specific location

    $crawler = $this->client->request('GET', '/user/login');
    $this->assertCount(1, $crawler->filter('h1:contains("Please Log In")'), 
        'should contain "Please Log In" in h1 tag (only once)');
```

Useful Assertions:

```php
    // Assert that there is at least one h2 tag with the class "subtitle"
    $this->assertGreaterThan( 0, $crawler->filter('h2.subtitle')->count());
 
    // Assert that there are exactly 4 h2 tags on the page
    $this->assertCount(4, $crawler->filter('h2'));
 
    // Assert that the "Content-Type" header is "application/json"
    $this->assertTrue(
        $client->getResponse()->headers->contains(
            'Content-Type',
            'application/json'
        )
    );
 
    // Assert that the response content matches a regexp.
    $this->assertRegExp('/foo/', $client->getResponse()->getContent());
 
    // Assert that the response status code is 2xx
    $this->assertTrue($client->getResponse()->isSuccessful());
 
    // Assert that the response status code is 404
    $this->assertTrue($client->getResponse()->isNotFound());
 
    // Assert a specific 200 status code
    $this->assertEquals(200,$client->getResponse()->getStatusCode());
 
    // Assert that the response is a redirect to /demo/contact
    $this->assertTrue($client->getResponse()->isRedirect('/demo/contact'));
 
    // or simply check that the response is a redirect to any URL
    $this->assertTrue($client->getResponse()->isRedirect());
```
 
Browsing <a name="laravel-client-browsing">
----------------------------------------------
The Client supports many operations that can be done in a real browser:

```php
    $client->back();
    $client->forward();
    $client->reload();
 
    // Clears all cookies and the history
    $client->restart();
```
 
Accessing Internal Objects <a name="laravel-client-internals">
---------------------------------------------------------------
If you use the client to test your application, you might want to access the client's internal objects:

    $history   = $client->getHistory();
    $cookieJar = $client->getCookieJar();
 
You can also get the objects related to the latest request:

    $request  = $client->getRequest();
    $response = $client->getResponse();
    $crawler  = $client->getCrawler();
 
If your requests are not insulated, you can also access the Container and the Kernel:

    $container = $client->getContainer();
    $kernel    = $client->getKernel();
 

Redirecting <a name="laravel-client-redirection">
-----------------------------------------------------
When a request returns a redirect response, the client does not follow it automatically. You can examine the response and force a redirection afterwards with the followRedirect() method:

    $crawler = $client->followRedirect();
 
If you want the client to automatically follow all redirects, you can force him with the followRedirects() method:

    $client->followRedirects();
 

The Crawler <a name="laravel-crawler">
-----------------------------------------
A Crawler instance is returned each time you make a request with the Client. It allows you to traverse HTML documents, select nodes, find links and forms.
 
#### Traversing:

Like jQuery, the Crawler has methods to traverse the DOM of an HTML/XML document. For example, the following finds all input[type=submit] elements, selects the last one on the page, and then selects its immediate parent element:

```php
    $newCrawler = $crawler->filter('input[type=submit]')
        ->last()
        ->parents()
        ->first();
```

Many other methods are also available:

    Method                  Description
    filter('h1.title')      Nodes that match the CSS selector
    filterXpath('h1')       Nodes that match the XPath expression
    eq(1)                   Node for the specified index
    first()                 First node
    last()                  Last node
    siblings()              Siblings
    nextAll()               All following siblings
    previousAll()           All preceding siblings
    parents()               Returns the parent nodes
    children()              Returns children nodes
    reduce($lambda)         Nodes for which the callable does not return false
 
#### Extracting Information:

```php
    // Returns the attribute value for the first node
    $crawler->attr('class');
 
    // Returns the node value for the first node
    $crawler->text();
 
    // Extracts an array of attributes for all nodes (_text returns the node value)
    // returns an array for each element in crawler, each with the value and href
    $info = $crawler->extract(array('_text', 'href'));
 
    // Executes a lambda for each node and return an array of results
    $data = $crawler->each(function ($node, $i)
    {
        return $node->attr('href');
    });
```

We can chain commands like this (which will assert the last input field in the form has a type of 'submit):

```php
    this->assertEquals('submit', $crawler
        ->filter('form')
        ->filter('input')
        ->last()
        ->attr('type'), 'should have submit button');
```

#### Links:

To select links, you can use the traversing methods above or the convenient selectLink() shortcut:

```php
    $crawler->selectLink('Click here');
 
    $link = $crawler->selectLink('Click here')->link();
    $client->click($link);
``` 

#### Forms:

    $buttonCrawlerNode = $crawler->selectButton('submit'); 



Laravel 4 IoC and Facades <a name="laravel-ioc">
---------------------------------------------------------
http://www.thenerdary.net/post/30859565484/laravel-4

When using a laravel class, we generally use a facade:

    $var = Session::get('foo');
 
Under the hood, it does this:

    $app->resolve('session')->get('foo');
 
So, we can swap out parts of the framework, like so:

    $app['session'] = function()
    {
        return new MyCustomSessionLayer;
    }
 
For instance, maybe you want to make the whole Redirect layer for a test. In your test you could just do:

    $app['redirect'] = $mock;

For any classes that use facades (most of Laravel's classes), get the original class name with:

    echo get_class(App::getFacadeRoot());       (App, or any other class)


https://news.ycombinator.com/item?id=5044336

However, things like the Input, URL, File, etc. classes still being static could lead to some testability problems. I've broken encapsulation on the Input class just to make it a little more testable. You can set the Input just by saying "Input::$input = array()".

You can swap out entire components with your own. For instance, if you had a class that inherited from the root Response object (Illuminate\Http\Response), you could use it (instead of the standard response class) for all response handling. Just edit app/config/app.php:

    //'Response'        => 'Illuminate\Support\Facades\Response',
    'Response'        => 'Api\Facades\Response',    (your own response facade)



Mocks <a name="mocks">
----------------------
A mock is a replacement for an object that we can use for testing.
Rather than hitting an actual class (eg, to write to the database), go to a mock
Integration testing should hit actual classes

Mockery is a project that makes creation and handling of mocks easier:
in composer.json, require  "mockery/mockery": "dev-master"

### Using Mockery

```php
    $mock = \Mockery::mock('Ticket');       // create a mock object
    $mock->closed_at = $date;               // set a variable on the mock object
    $mock->shouldReceive('function')        // function that should be called
        ->once()                            // require it to be called once
        ->times(4)                          // require it to be called 4 times
        ->andReturn('foo')                  // return the given value to the calling function
```

For instance:

```php
    $errors = array('foo'=>'bar');

    Validator::shouldReceive('make')->once()
        ->andReturn(\Mockery::mock(array(
            'passes'=>$bool, 
            'errors'=>new \Illuminate\Support\MessageBag($errors)
        )));
```

You can also assign an attribute to a mock, like this:

```php
    $mock = \Mockery::mock('MockObject');
    $mock->foo = 'bar';
```


### Using Mocks:
(instructions from https://tutsplus.com/tutorial/better-testing-in-laravel/)

* My controller is ItemsController
* My model is Item

I'll set up several different classes:

* ItemsController
* Item
* ItemRepositoryInterface
* EloquentItemRepository

In ItemsController, use this:

``` php
    public function __construct(ItemRepositoryInterface $items)
    {
        $this->items = $items;
    }

    Interface ItemRepositoryInterface
    {
        public function all();
        public function find($id);
        ...
        any other functions you want the controller to be able to access
    }

    class EloquentItemRepository implements ItemRepositoryInterface
    {
        public function all(){     return Item::all(); }
        public function find($id){ return Item::find($id); }
        ... 
        other functions, just call the corresponding item function
    }
```

The application also needs to know the default repository to use to implement the interface:

``` php
    App::bind('ItemRepositoryInterface', 'EloquentItemRepository');
```

When testing, using Mockery,

``` php
    public function testMockShow()
    {
        $mock = \Mockery::mock('ItemRepositoryInterface');
        $mock->shouldReceive('find')->once()->andReturn('{"name":"works"}');
        App::instance('ItemRepositoryInterface', $mock);

        $response = $this->call('GET', 'items/1');
        $this->assertTrue($response->isOk());
        $this->assertNotEmpty($response->getContent());
        $json = json_decode($response->getContent());
        $this->assertEquals('works', $json->name);
    }
```

This should bypass the database completely.
In some cases, we need to return an intermediate object (eg, items/1/vendors)

``` php
    public function testShowItemVendors()
    {
        $mockVendor = $this->mock('Vendor');
        $mockVendor->shouldReceive('get')->once()->andReturn('{"name":"vendor works"}');

        $mockItem = $this->mock('Item');
        $mockItem->shouldReceive('find')->once()->andReturn($mockItem);
        $mockItem->shouldReceive('vendors')->once()->andReturn($mockVendor); 

        $response = $this->call('GET', 'items/1/vendors');
        $this->assertTrue($response->isOk());
        $this->assertNotEmpty($response->getContent());
        $json = json_decode($response->getContent());
        $this->assertEquals('vendor works', $json->name);
    }

    public function mock($class)
    {
        $repo = $class . 'RepositoryInterface';
        $mock = \Mockery::mock($repo);
        App::instance($repo, $mock);       
        return $mock;
    }
```

Mock statements can be either separate or chained. If they are chained, make sure to use `getMock()` at the end to actually get the mock object. So, this:

```php
    $mockItem = $this->mock('Item');
    $mockItem->shouldReceive('find')->once()->andReturn($mockItem);
    $mockItem->shouldReceive('vendors')->once()->andReturn($mockVendor); 
```

is equivalent to this:

```php
    $mockItem = $this->mock('Item')
        ->shouldReceive('find')->once()->andReturn($mockItem)
        ->shouldReceive('vendors')->once()->andReturn($mockVendor)
        ->getMock(); 
```

Missing Functions <a name="laravel-mock-missing">
------------------------------------------------------
If you're mocking something, you can either ignore or defer functions that are missing. For instance, this:

```php
    $this->objectMock = \Mockery::mock('Client\Core\Object\UniversalObject');
    $this->objectMock
        ->shouldReceive('unguard')->once()
        ->shouldReceive('fill')->once()
        ->shouldReceive('reguard')->once()
        ->shouldReceive('save')->once();
```

Can also be done this way:

```php
    $this->objectMock = \Mockery::mock('Client\Core\Object\UniversalObject')->shouldDeferMissing();
    $this->objectMock
        ->shouldReceive('fill')->once()
        ->shouldReceive('save')->once();
```

Mocking Input <a name="laravel-mock-input">
----------------------------------------------
The input facade is designed to be able to handle mock input easily. To mock input, just do this:

```php
    Input::replace($input = array('ticket-type' => 'open'));
```

We can then test this in a couple of different ways:

```php
    Input::replace(array('username' => 'foo', 'password'=>'bar'));
    $this->call('POST', '/user/login', Input::all());
```

or:

```php
    Input::replace(array('username' => 'foo', 'password'=>'bar'));
    $test = new UserController;
    $test->postLogin();
```



Mocking a Facade <a name="laravel-mock-facade">
---------------------------------------------------
This is how to mock a facade:

```php
    use \Mockery as m;

    public function setUp()
    {
        parent::setUp();

        $app = m::mock('AppMock');
        $app->shouldReceive('instance')->once()->andReturn($app);

        Illuminate\Support\Facades\Facade::setFacadeApplication($app);
        Illuminate\Support\Facades\Config::swap($config = m::mock('ConfigMock'));

        $config->shouldReceive('get')->once()
            ->with('logviewer::log_dirs')
            ->andReturn(array('app' => 'app/storage/logs'));

        $this->logviewer = new Logviewer('app', 'cgi-fcgi', '2013-06-01');
    }
```

I think that ...\Config::swap  statement actually swaps out the class the facade is looking for.


Mocking Demeter Chains
-----------------------------
Mockery will let us mock demeter chains, eg `$object->foo()->bar()->zebra()->alpha()->selfDestruct();`. It will return the value of the LAST entry of the entire chain. We can mock all of that like this:

```php
    $mock = \Mockery::mock('SomeMock');
    $mock->shouldReceive('foo->bar->zebra->alpha->selfDestruct')
        ->andReturn('Ten!');
```

It will also let us do partial mocks, where we mock some of the public functions of the class under test. (They MUST be public functions; we can't mock private or protected functions).

In my class, if I have a function `getAlias` that I want to mock, this is how I do it: 

```php
    $test = m::mock('\Kalani\FacadeRoot\FacadeRoot[getAlias]', array(Null, Null));
        // the array, above, is for passing parameters to the constructor

    $test->shouldReceive('getAlias')->andReturn('Foo');
        // The `getAlias` function will be called, and return 'Foo'

    $this->assertEquals('(alias) Foo', $test->getRoot(Null));
        // getRoot calls getAlias (and takes a parameter)
```

If we need to mock multiple statements, we do it like this:

// TODO: Mock multiple statements
    

In-memory database and test environment <a name="laravel-memory-db">
----------------------------------------------------------------------
An in-memory database is much faster than writing data to your actual database (it doesn't require any disk reads, indexes, etc.)

From: http://net.tutsplus.com/tutorials/php/testing-like-a-boss-in-laravel-models/
Within the app/config/testing directory, create a new file, named database.php, and fill it with the following content:

    // app/config/testing/database.php
    <?php     
    return array( 
        'default' => 'sqlite',
        'connections' => array(
            'sqlite' => array(
                'driver'   => 'sqlite',
                'database' => ':memory:',
                'prefix'   => ''
            ),
        )
    );
 
#### Before running tests:
Since the in-memory database is always empty when a connection is made, it’s important to migrate the database before every test. To do this, open app/tests/TestCase.php and add the following method to the end of the class:

```php
    /**
     * Migrates the database.
     * This will cause the tests to run quickly.
     *
     */
    private function prepareForTests()
    {
        Artisan::call('migrate');   // sets up all tables
        $this->seed;                // seed test database values
        Mail::pretend(true);        // if using mail
    }

    // When using Mockery, it's important to close it at the end of the test
    public function tearDown()
    {
        \Mockery::close();
    }
```

Testing with an Array Repository <a name="laravel-array-repo">
-----------------------------------------------------------------

We can get fast, solid testing with an array repository, which basically stubs the functionality we need. Here's the critical pieces:

```php
    interface TodoRepositoryInterface
    {
        public function all();
    }

    class EloquentTodoRepository implements TodoRepositoryInterface
    {
        public function all()     { return Todo::all(); }
    }

    class ArrayTodoRepository implements TodoRepositoryInterface
    {

        protected $guarded = array();
        public static $rules = array();

        public function all()     
        {
            return array(
                array(
                    'id'            => 999,
                    'title'         => 'foo',
                    'description'   => 'bar',
                ),
            );
        }
    }

    class TodosTableSeeder extends Seeder {

        public function run()
        {
            DB::table('todos')->delete();

            $todos = array(
                array(
                    'id'            => 1,
                    'title'         => 'Learn Laravel',
                    'description'   => 'It\'s really important',
                    'created_at'    => new DateTime,
                    'updated_at'    => new DateTime,
            ...
        }
```

The global binding is set to this:

    App::bind('TodoRepositoryInterface', 'EloquentTodoRepository');

My controller looks like this:

```php
    class TodoController extends BaseController 
    {
        protected $todo;

        public function __construct(TodoRepositoryInterface $todo)
        {
            $this->todo = $todo;
        }

        public function index() 
        {
            return View::make('todo/index')
                ->with('items', $this->todo->all());
        }
    }
```

My tester:

```php
    class TodoControllerTest extends TestCase
    {
        public function testTodoControllerExistsAndReturnsView()
        {
            $result = $this->action('GET', 'TodoController@index');
            $this->assertContains('Illuminate\View', get_class($result->getOriginalContent()));
            $this->assertViewHas('items');
        }

        public function testTodoControllerCanLoadDataFromDB()
        {
            $result = $this->action('GET', 'TodoController@index');
            $data = $result->getOriginalContent()->getData();
            $this->assertEquals(1, $data['items'][0]['id']);        
        }

        public function testTodoControllerCanLoadDataFromArray()
        {
            App::bind('TodoRepositoryInterface', 'ArrayTodoRepository');
            $result = $this->action('GET', 'TodoController@index');
            $data = $result->getOriginalContent()->getData();
            $this->assertEquals(999, $data['items'][0]['id']);        
        }
    }
```

To test specific actions, we can use this:

```php
    $data = $this->action('PUT',                // request type
        'TodoController@update',                // controller/method to use
        array('999'),                           // id sent to update 
        array('title'=>'something new'));       // new data via Input

    $data = $this->action('DELETE', 
        'TodoController@destroy', 
        array(42));

    $result = $this->action('POST', 
        'TodoController@store', 
        array('title'=>'test'));
```


Testing Without a Repository Pattern <a name="laravel-no-repo">
------------------------------------------------------------------
(from http://lutro.priv.no/posts/testable-simple-l4-code-without-repository-patterns)
For simple tests/projects, the repository pattern is overkill. We can just swap out our models:

```php
    class MyController extends Controller
    {
        protected $model;

        public function __construct(MyModel $model)
        {
            $this->model = $model;
        }

        public function index()
        {
            return View::make('my.index')
                ->with('models', $this->model->all());
        }

        public function show($modelId)
        {
            return View::make('my.show')
                ->with('models', $this->model->find($modelId));
        }
    }
```

A test would look like this:

```php
    class MyControllerTest extends TestCase
    {
        public function setUp()
        {
            parent::setUp();
            $this->mockModel = Mockery::mock('MyModel');
            $this->app->instance('MyModel', $this->mockModel);
        }

        public function testIndex()
        {
            // return an empty array because our index view has a foreach
            // loop that would error if we returned something non-iterable
            $this->mockModel->shouldReceive('all')->once()
                ->andReturn(array());

            $this->call('get', '/my-route');

            $this->assertResponseOk();
            $this->assertViewHas('models');
        }

        public function testShow()
        {
            // this is the best way to mock a real model to pass to a
            // view without having to add ->shouldReceive for every
            // single function and defining every single variable on it.
            $mock = Mockery::mock(new MyModel);

            $this->mockModel->shouldReceive('find')->once()->with(1)
                ->andReturn($mock);

            $this->call('get', '/my-route/1');

            $this->assertResponseOk();
            $this->assertViewHas('model');
        }
    }



Testing Filters <a name="laravel-filters">
------------------------------------------------------------------

Filters are generally ignored during the unit tests. This is a good thing, because it lets us test without worrying about logging in, and such, first. To test filters, Test the action itself, then the filter, and then test that the action has that filter. Putting filters into a separate class will allow you to test them properly. 
https://github.com/laravel/framework/issues/766

You can also enable filters for a specific test like so:

```php
    public function testRedirectionWorks()
    {
        Route::enableFilters();
        $response = $this->call('GET', '/');
        $this->assertEquals(302, $response->getStatusCode());

        $this->client->followRedirect();
        $response = $this->client->getResponse();
        $this->assertEquals(200, $response->getStatusCode());
        $this->assertContains('login', $response->getContent());
        Route::disableFilters();
    }
```


    
    
Additional stuff for testing<a name="laravel-extra">
----------------------------------------------------------

To dump a query:

    var_dump(DB::getQueryLog());

This will produce output like this (where you can see the actual query string):

    array (size=1)
      0 => 
        array (size=3)
          'query' => string 'select * from `questions` where `qid` = ? limit 1' (length=49)
          'bindings' => 
            array (size=1)
              0 => int 4
          'time' => string '0.77' (length=4)

You can also export the data to a log, like so:

    \Log::debug(var_export(DB::getQueryLog(), true));
         
          
          
          
Selenium <a name="selenium">
====================================================================

We can test the actual user experience with selenium. To set up a selenium test, use this:

```php
    class SeleniumTest extends PHPUnit_Extensions_Selenium2TestCase
    {
        public function __construct()
        {
            parent::__construct();
            $this->setBrowserUrl('http://lkata');
            $this->setBrowser('chrome');
            $this->setHost('localhost');
            $this->setPort(4444);
        }

        public function testHomePageDoesNotIncludeDebugError()
        {
            $this->assertEquals(0, 
                preg_match('/xdebug-error/i', $this->source()), 
                'should not return an xdebug error');
        }
    }
```
          
          
          
BDD and Acceptance Testing with Codeception <a name="codeception">
====================================================================

Codeception is a testing tool based on phpunit, which can do BDD (behavior driven development) and acceptance testing. It can also possibly do unit testing. It has three separate "tester" objects:

    WebGuy    (Acceptance tests) Standard user; emulate web browser; see web output
    TestGuy   (Functional tests) Advanced user; emulate web requests; see app internal values
    CodeGuy   (Unit tests) Coder

Download from:

    http://codeception.com/quickstart
    wget http://codeception.com/codecept.phar

To get started, in the web root:

    php codecept.phar bootstrap [path]      // sets up the testing environment

Configure the database (codeception.yml):

    dsn:
    
Configure acceptance tests (in tests/acceptance.suite.yml)

    class_name: WebGuy 
    modules: 
        enabled: [PhpBrowser, WebHelper]
        config: 
            PhpBrowser:
                url: '{YOUR APP'S URL}'
    
Create an acceptance test:

    php codecept.phar generate:cept acceptance Welcome    // create first acceptance test

Write the test (eg, in tests/acceptance/WelcomeCept.php. One test will go into each file).

Run the test:

    php codecept.phar run
    php codecept.phar run --steps                 // also show steps
    php codecept.phar run <suitename> <testname>  // run just one suite/test

Note: Codeception can also use test groups. So, you can enter this at the beginning of a file:

```php
/**
 * @group now
 */
```

and run it from the command line like this:

    php codecept.phar run --group now

Codeception can provide much simpler syntax than using a web crawler (as described above). For instance, this is a codeception test:

```php
    $I->amOnPage('/user/login');
    $I->fillField('Username', 'joel');
    $I->fillField('Password', 'test');
    $I->click('Log in');
```

or...

```php
    $I->amOnPage('/user/login');
    $I->submitForm('#login', array('username'=>'joel', 'password'=>'test'));
```

This would be the equivalent test using php (with a web crawler):

```php
    $this->call('GET', '/user/login');

    $crawler = $this->client->getCrawler();
    $form = $crawler->selectButton('Log in')->form();

    $form['username'] = 'joel';
    $form['password'] = 'test';

    $crawler = $this->client->submit($form);
```


### Using codeception with a sqlite database

This will describe how to set up a separate database for codeception acceptance tests. We'll use sqlite, because it's dramatically faster than mysql, even though an in-memory database does not work at this time. To do this, we'll use two files: a sqlite database, and a data dump. The data dump will repopulate the database for each test.

In acceptance.suite.yml, add this:

    modules:
        enabled:
            - Db

After you've added it, run `codecept build` again to rebuild the WebGuy class.

In codeception.yml, add this:

    modules:
        config:
            Db:
                dsn: 'sqlite:app/tests/codeception/_data/db.sqlite'
                user: ''
                password: ''
                dump: app/tests/codeception/_data/dump.sql

Next, build the files. First, we'll need a database connection to the sqlite file. In `app/config/database.php`, enter this:

```php
    'codeception'  => array(
        'driver'   => 'sqlite',
        'database' => __DIR__.'/../tests/codeception/_data/db.sqlite',
        'prefix'   => '',
    ),
```

Populate the database:

    php artisan migrate --seed --database=codeception

Next, we'll create a dump file to be loaded before every codeception test. To do this, we first need to be able to run sqlite3. Install the package, if needed (`sudo apt-get install sqlite3`). The command to create a backup is simple:

    sqlite3 app/tests/codeception/_data/db.sqlite .dump > app/tests/codeception/_data/dump.sql




### Sample Codeception Tests

Here are some sample tests:

```php
    // This is standard BDD labelling. Who am I? What do I want to do? Why? Then do it.
    $I = new WebGuy($scenario);
    $I->am('Account Holder'); 
    $I->wantTo('withdraw cash from an ATM');
    $I->lookForwardTo('get money when the bank is closed');

    $I = new WebGuy($scenario);
    $I->wantTo('see home page');
    $I->amOnPage('/');
    $I->seeResponseCodeIs(200);
    $I->see('Hello');

    $I = new WebGuy($scenario);
    $I->wantTo('log in');
    $I->amOnPage('/login');
    $I->seeResponseCodeIs(200);
    $I->see('notFoundHttpException');
    $I->click('login');

    $I->fillField('Name', 'Miles');
    // we can use input name, or id
    $I->fillField('user[email]','miles@davis.com');
    $I->selectOption('Gender','Male');
    $I->click('Update');
    
    $I->seeInCurrentUrl('/user/miles');
    $I->seeCheckboxIsChecked('#agree');
    $I->seeInField('user[name]','Miles');
    $I->seeLink('Login');

    $v=$I->grabTextFrom('body');
    var_dump($v);
```

