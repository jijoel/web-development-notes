Laravel 
=====================

* [Setup](#setup)
* [IOC Binding](#ioc)
* [Migrations and Seeding](#migrations)
* [Way Generators](#way-generators)
* [Routing](#routing)
* [Controllers](#controllers)
* [Models](#models)
    * [Model Sections](#model-sections)
        * [Mutators & Accessors](#model-mutate)
        * [Scopes](#model-scope)
        * [Relationships](#model-relationship)
        * [Utility Functions](#model-utility)
* [Views](#views)
* [Forms](#forms)
* [Mail](#mail)
* [Queues](#queue)
* [Logging](#logging)
* [Sessions](#sessions)
* [Redirections](#redirection)
* [Events](#events)



Setup<a name="setup">
------------------------

Install it via the installer:

    download http://laravel.com/laravel.phar
    php laravel.phar new <projectName> 

Alternately (slower), install it directly via composer:

    composer create-project laravel/laravel <projectName>

Or manually install, via instructions here:

    http://niallobrien.me/2013/03/installing-and-updating-laravel-4/

To set up laravel, copy the basic project from above, then run `composer install` to get all of the dependencies.

Set the project root directory in apache to the /public folder
(this is a place for css, img, js, etc. files, also)

make storage directory writable:

    chmod -R o+w app/storage/*
    (or chown www-data app/storage/*)

A secure application key is automatically created when laravel is installed, but you can change it via Artisan on the command line if you'd like:

    php artisan key:generate

There is currently some inconsistent indentation in the base laravel installation. Sometimes, they'll use tabs; other times, spaces. To convert all tabs to 4 spaces for the entire directory tree, run this from the app directory:

    find . ! -type d ! -name _tmp_ -exec sh -c 'expand -t 4 {} > _tmp_ && mv _tmp_ {}' \;

Do NOT run this from the root directory--it will destroy the .git files.

Permissions are not set up correctly, so change those, also:

    find app/ -type f | xargs chmod -x
    find app/ -type d | xargs chmod o-w
    chmod +x vendor/bin/*

I like setting up a .gitignore file so that some files and folders won't be loaded into a git repository. Here are my settings:

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


Laravel configuration information stored in `project/app/config`.
Each file here returns an array with configuration information (closures are OK).
Access it at any time with `Config::get`. Use the file name and array key:

    Config::get('app.timezone')

For any classes that use facades (most of Laravel's classes), get the original class name with:

    echo get_class(App::getFacadeRoot());       (App, or any other class)


Globals
------------------------
We can use any facade like a global, and can reach almost everything through the App facade (see [IoC Binding](#ioc), below).

There are some helper functions that work like globals:

    app_path()      Path to the 'app' folder
    base_path()     Path to the project's root folder
    public_path()   Path to the 'public' folder
    storage_path()  Path to the 'app/storage' folder



IoC Binding <a name="ioc">
----------------------------

To use classes that are bound to Laravel's IoC container:

Laravel’s components are instances that exist within the Laravel 4 $app container object. They can be called in each of these ways:
 
    Component::methodName();
    App::make('component')->methodName();
    app('component')->methodName();
    $app['component']->methodName();
    $app->component->methodName();

We can also instanciate classes in several ways:

    $profiler = App::make('profiler');
    $profiler = app('profiler');
    $profiler = $app['profiler'];           // If you have access to the instance 
                                               (which you do in app/start/global.php)
    $profiler = $this->app['profiler'];     // If, for example, you're inside the 
                                               boot() method of a service provider



To bind classes to the IoC container:

```php 
$app->bind('UsersController', function($app) {
    $controller = new UsersController(
        new Response,
        $app->make('request'),
        $app->make('validator'),
        $app->make('hash'),
        new User
    );
    return $controller;
});
```

At this point, we can make the class, using

    $app->make('UsersController') ;


    
Migrations and Seeding<a name="#migrations">
----------------------------------------------
Migrations are like version control for your databases.
 
    php artisan migrate:make create_users_table 

creates the basic structure for a new 'users' table. Fill it in further, to get:

```php
public function up()
{
    Schema::create('users', function($table) {
        $table->increments('id')->unsigned();   // auto-increments an unsigned id field
        $table->string('name',50);              // sets to length you entered
        $table->string('email');                // sets to varchar 200
        $table->string('password');
        $table->boolean('activated')->default(0);   // sets a default value
        $table->integer('age')->nullable();         // allows nulls
        $table->timestamps();                       // creates created_at and modified_at fields
    });
}
```

Use seeds to add sample records:

Create a file within the app/database/seeds folder that has the same name as the table that it corresponds to; in our case, users.php. Add:

```php
return array(
    array(
        'username' => 'firstuser',
        'password' => Hash::make('first_password'),
        'created_at' => new DateTime,
        'updated_at' => new DateTime
    ),
    array(
        'username' => 'seconduser',
        'password' => Hash::make('second_password'),
        'created_at' => new DateTime,
        'updated_at' => new DateTime
    )
);
```
 
 
Run the migrations and insert sample records:

    // Make sure the auto-loader knows about these new classes
    php composer.phar dump-autoload

To run the migration:

    php artisan migrate                     // run all migrations that have not been run
    php artisan migrate:refresh             // roll back and re-run all migrations
    php artisan migrate:refresh --seed      // refresh, then seed the database
    php artisan db:seed                     // seed the database


    
Artisan Class Generator<a name="way-generator">    
---------------------------------------------------
jeffrey way (nettuts) has created an add-in for artisan, which can automatically generate classes (including models, controllers, several views, routing, and error testing) for you.

    require "way/generators": "dev-master"           (composer.json)
    'Way\Generators\GeneratorsServiceProvider'       (add to providers array)

This lets me create resources very easily:

    php artisan generate:resource Todos --fields="title:string,description:text"

Even better, we can create resources with a lot of standard boilerplate:

    php artisan generate:scaffold Todos --fields="title:string,description:text"


    
Routing<a name="routing">
----------------------------
Here are a few different ways to send variables to the default view:
 
via the routes.php file:

``` php
    $data = array(
        'greeting'   => 'hello',
        'something'  => 'world',
        'items'      => array('Item1', 'Item2', 'Item3', 'Item4'),
        'data_items' => array('First','Second')
    );

    Route::get('/', function() {
        return View::make('home.index', $data);
    };
```

or:

``` php
    Route::get('/', function() {
        return View::make('home.index')->with($data);        
    });
```

or:

``` php
    Route::get('/', function() {
        $view = View::make('home.index');
        $view->greeting = 'Hi';
        $view->something = 'Everyone';
        return $view;
    });
```

Via a controller:
 
(in routes.php):

    Route::controller('home');
 
(in controllers/home.php):

``` php
    public function action_index() {
        return View::make('home.index', $data);
    }
```

By default, it does not support a trailing slash in a URL, but you can add one, by using the missingMethod function:

``` php
    class BaseController extends Controller 
    { ...
        public function missingMethod($parameters) {

            if (substr($_SERVER['REQUEST_URI'], strlen($_SERVER['REQUEST_URI'])-1)=='/')
                return Redirect::to(rtrim($_SERVER['REQUEST_URI'],'/'));

            throw new Symfony\Component\HttpKernel\Exception\NotFoundHttpException('Page Not Found');
        }
```

(I think this is working now in the final release...)

There are a couple of ways of handling query strings:

    /name/search/ForName:
    Route::get('/name/search/{name}', function($name) { return $name; });

    /name/search?query=ForName
    Route::get('/name/search', function() { return Request::get('query'); });

If you use Route::controller, it will create a new route for each getter in your controller. For instance:

``` php
    Route::controller('pages', 'PagesController');

    class PagesController extends BaseController {
        public function getFoo() {
            return 'bar';
        }
    }
```

This will create a pages route, so you can go to pages/foo. It works on everything that starts with get...  (this works for all GET requests). For POST requests, prefix it with post:

    public function postX

This works for these HTTP verbs:

    GET
    PUT
    POST
    DELETE
    OPTIONS

It does not currently seem to work for these HTTP verbs:

    HEAD       Call to undefined method Illuminate\Routing\Router::head() 
    TRACE
    CONNECT
    PROPFIND
    PROPPATCH
    MKCOL
    COPY
    MOVE
    LOCK
    UNLOCK
    VERSION-CONTROL
    REPORT
    CHECKOUT
    CHECKIN
    UNCHECKOUT
    MKWORKSPACE
    UPDATE
    LABEL
    MERGE
    BASELINE-CONTROL
    MKACTIVITY
    ORDERPATCH
    ACL
    PATCH
    SEARCH


### Nested Routes
You can actually nest resourceful routes, so we can handle a restful interface throughout the system. To do this: 

    Route::resource('pages', 'PagesController');
    Route::resource('pages.tags', 'TagsController');

We will end up with these routes:

    GET /pages                          | pages.index        | PagesController@index
    GET /pages/create                   | pages.create       | PagesController@create
    POST /pages                         | pages.store        | PagesController@store
    GET /pages/{pages}                  | pages.show         | PagesController@show
    GET /pages/{pages}/edit             | pages.edit         | PagesController@edit
    PUT /pages/{pages}                  | pages.update       | PagesController@update
    PATCH /pages/{pages}                |                    | PagesController@update
    DELETE /pages/{pages}               | pages.destroy      | PagesController@destroy
    GET /pages/{pages}/tags             | pages.tags.index   | TagsController@index
    GET /pages/{pages}/tags/create      | pages.tags.create  | TagsController@create
    POST /pages/{pages}/tags            | pages.tags.store   | TagsController@store
    GET /pages/{pages}/tags/{tags}      | pages.tags.show    | TagsController@show
    GET /pages/{pages}/tags/{tags}/edit | pages.tags.edit    | TagsController@edit
    PUT /pages/{pages}/tags/{tags}      | pages.tags.update  | TagsController@update
    PATCH /pages/{pages}/tags/{tags}    |                    | TagsController@update
    DELETE /pages/{pages}/tags/{tags}   | pages.tags.destroy | TagsController@destroy



Controllers<a name="controllers">
-------------------------------------

    php artisan controller:make <Controller name>

creates lots of functions, including:

    index   GET  - collection, eg site/photos
    create  GET  - display form to add new record - site/photos/create
    store   POST - take input from new item, write new record
    edit    GET  - display form to edit record - site/photos/1/edit
    update  PUT  - submitted form, update existing record
    show    GET  - item, eg site/photos/1
    destroy DELETE sent - delete an object
 
    Route::resource('photos','PhotosController');
 
Laravel 4 is restful by default.
in index(), if you return `Photo::all()`, you'll get json output

These are equivalent ways to return data to a user:

    return Photo::all();  

or

    $photos = Photo::all();
    return View::make('photos.index', compact('photos'));

or

    return View::make('photos.index')->with(array('photos' => $photos));
 
In a view, to get a link to the photos.show method (parameter id), use:

    <a href="{{ route('photos.show', ['photos' => $photo->id]) }}"> 

    photos/create -> POST photos -> photos/store
    photos/edit/1 -> PUT photos/1 -> photos/1/update
    GET photos/id/delete
    DELETE photos/id

Our controller can return an error statement, like this:

    return Response::make($view, 404);  // returns a 404 error


Eloquent Models<a name="models">
-------------------------------------

By default, the table name will be plural, and the model name will be singular. This can be changed, though…
 
    Table   Model   Controller
    users   User    UsersController
 
To use something else, in the model,
 
```php
    class User extends Eloquent {
        public static $table = 'my_user_table';
    }
```

We can also use something from an alternate database:

```php
    class User extends Eloquent {
        public static $table = 'other_db.my_user_table';
    }
```

To get additional data from a pivot table (eg, the M2M join table):

``` php
    class Item extends Eloquent
    {
        public function vendors()
        {
            return $this->belongsToMany('Vendor', 'item_vendors')
                ->withPivot(array('confirmed', 'last_known_price'));
        }
    }
```



### Model Sections <a name="model-sections">

There are several types of methods that we want to include in models. These include:

* [Mutators & Accessors](#model-mutate)
* [Scopes](#model-scope)
* [Relationships](#model-relationship)
* [Utility Functions](#model-utility)



#### Mutators & Accessors <a name="model-mutate">

These are used to change data before it is shown to a user, or before it is written to the database. They can be used on existing field names (though it is better to use a Presenter for those), or can define new field names:

```php
    public function setBirthdateAttribute($birthdate)
    {
        $date = new \Carbon\Carbon($birthdate);
        $this->attributes['birthdate'] = $date->format('Y-m-d');
    }

    public function getBirthdateAttribute($value)
    {
        return new \Carbon\Carbon($value);
    }
```

We can tell Laravel arrays to include (or hide) given accessors or attributes. To do that:

    protected $appends = array('name','chartable');                 // includes custom accessors
    protected $hidden  = array('category','value','other');         // hides fields



#### Scopes <a name="model-scope">

These are used to filter data.

```php
    public function scopeOpen($query)
    {
        $query->whereRaw('closed_at=0 or closed_at is Null');
    }

    public function scopeOverdue($query)
    {
        return $query
            ->whereRaw('due_at > 0 and not due_at is Null and due_at < "' 
                . Carbon::now()->toDateTimeString() . '"')
            ->andWhere(function($query){
                $this->scopeOpen($query);
            });
    }
```


#### Relationships <a name="model-relationship">

These are used to get related records from other models.

    hasOne
    hasMany ($otherModel, $otherFieldToMyKey)
    belongsTo ($otherModel, $myFieldToOtherModelKey)
    belongsToMany ($otherModel, $relationTable)


##### Using Relationships

To get data from a related record:

    if ($tag->pages()->where('id', $pageId)->get()->isEmpty()) {


To get all users which have tasks created_at $date:

```
    $users = User::join('user_task', 'user_task.task_id', '=', 'task.id')
        ->where('user_task_created_at', '=', $date)->get();
```

These can also include complex joins:

```
    $slots = Slot::select('slots.*')->with('product')
        ->join('products', 'slots.product_id', '=', 'products.id')
        ->join('categories', 'products.category_id', '=', 'categories.id')
        ->where('categories.name', '=', 'MyCategory')->get();
```

To filter all tasks of a user from $date:

```php
    $user = User::whereId(1)->with(array(tasks' => function($query)
    {
        $query->where('created_at', '=', $date);
    }))->first();
```

```php
    $modules = $dropdowns->filter(function($model) {
        return $model->ref_type=='module';
    });
```

Attach M/M records like this:

```php
    Product::find($id)->categories()->attach( Input::get('category') );
```

Attach M/M records with pivot table data like this:

```php
    $customer->drinks()->attach($drink_id, array(‘customer_got_drink’, 1);
```

We can get an M/M/M (extended) relationship like this:

```php
class Customer extends \Eloquent {    
    public function drinks()
    {
        return $this->belongsToMany('Drink', 'customer_drinks', 'customer_id', 'drink_id')
                    ->withPivot('customer_got_drink', 'chair_id')
                    ->join('chair', 'chair_id', 'chair.id')
                    ->select('drink_id', 'customer_id', 'pivot_customer_got_drink', 'chair.name AS pivot_chair_name'); //this select is optional
    }
}
```

We can also use data from the pivot table in our child records. For instance, if we have tables that look like this:

    Clinic:        id, ...
    Availability:  clinic_id, therapist_id, status
    Therapist:     id
    Session:       id, therapist_id, clinic_id, ...

In our view, we want to call this:

    @foreach($clinics as $clinic)
        @foreach($clinic->therapists as $therapist)
            {{ $therapist->status_message }}
            {{ $therapist->other_fields... }}
            @foreach($therapist->sessions as $session)
                {{ $session->time }}

Clinic can include something like this:

```php
    public function therapists()
    {
        return $this->belongsToMany(
            'Kalani\KSchool\Models\Therapist', 
            'availability',
            'clinic_id'
        )->withPivot('status');
    }
```

In Therapist, we can do something like this:

```php
    public function getStatusMessageAttribute()
    {
        if ($this->pivot) {
            return $this->pivot->status;
        }
    }

    public function getSessionsAttribute()
    {
        if (! $this->pivot) {
            return;
        }

        $clinicId = $this->pivot->clinic_id;

        return $this->appointments()
            ->forClinic($clinicId)->get();
    }
```

In Appointment, 

```php
    public function scopeForClinic($query, $clinic)
    {
        $query->where('clinic_id', $clinic);
    }
```


#### Utilities <a name="model-utility">

We don't have many of these, but they can be used to do things like letting the model know which Presenter to use to display data:

```php
    public function getPresenter()
    {
        return new Kalani\Core\Person\PersonPresenter($this);
    }
```

### Key/Value data (for select boxes, etc.)

```php
    $keyValueArray = User::lists('first_name', 'id');
```



Views<a name="views">
------------------------
Views can contain multiple sections (a part of a view rendered in each section). It can write data (like a report), or have read/write information (as a form). 

To use multiple sections (eg, template / view / sub-view):

template:

    <html>...
    <head>
        @yield('css')
    </head>
    <body>
        @yield('content')
    ...etc

Main view:

    @extends('template')
    @section('content')
       # enter all of the content here
       @include('sub-view')
    @stop
    @section('css')  # or whatever...
        #enter that section's content here
    @stop
    
Sub-view:

    #just include the data for the sub-view here



Although views should not contain much logic, you can do some interesting things with them, such as assigning even and odd classes, for instance:

@foreach($items as $index => $item)
    <tr class="@if($index% 2 == 0) rowclass1 @else rowclass2 @endif">
        ....
    </tr>
@endforeach


When using Fluent/Eloquent, you can use the “lists” method to quickly build an array of data that can easily populate the Form::select() HTML helper.

```php
    $categories = Categories::orderBy('type')->lists('type', 'id');
    Form::select('category_id', $categories );
```

The resulting array will populate the option value as the “id” field from the DB and the “type” will populate what value appears in the drop down. For example: <OPTION value=”id”>type</OPTION> So you can adapt that pattern to whatever you want the resulting array to look like. In this example, the array is indexed by my id column with the value of each “type”.

    
    

Forms<a name="forms">
------------------------

This is the standard structure to use with forms:

    {{ Form::open(array('url'=>URL::route('todos.store'), 'method'=>'POST')) }}
      @if(count($errors)>0)
          <div class="messages">
              <ul class="error">
                  <p>There were errors saving the item:</p>
                  @foreach($errors->all('<li>:message</li>') as $error)
                      {{$error}}
                  @endforeach
              </ul> <!--  .errors -->
          </div> <!--  .messages -->
      @endif
      <p>
          {{ Form::label('title', 'Title') }}
          {{ Form::input('title', 'title') }}
      </p>
      <p>
          {{ Form::label('description', 'Description') }}
          {{ Form::textarea('description', Null, array('rows'=>'8', 'cols'=>'35')) }}
      </p>
      <p>
          {{ Form::submit('Add a new entry') }}
      </p>
    {{ Form::close() }}

You can supply additional parameters like so:

    {{ Form::input('type', 'name', 'value', array('style'=>'width: 30em') ) }}
    {{ Form::textarea('name', 'value', array('rows'=>8)) }}



Mail<a name="mail">
--------------------
Laravel can send email messages; also through gmail. Configuration for that should be set up as follows:

    'driver' => 'smtp',
    'host' => 'smtp.gmail.com',
    'port' => 465,
    'encryption' => 'ssl',
    'username' => 'your-email@gmail.com',
    'password' => 'your-password',

Mail can also be queued for later. Use:

    Mail::queue('view.name', $dataArray, function($message){
        $message
    });



Queues <a name="queue">
------------------------
Queues can speed up the front-end by doing work on the back-end. 



Logging<a name="logging">
--------------------------
Laravel supports logging data to a log file. The logger provides the seven logging levels defined in RFC 5424: 

    debug, info, notice, warning, error, critical, and alert.

To use the logger, write commands like this:

    Log::info('This is some useful information.');
    Log::warning('Something could be going wrong.');
    Log::error('Something is really going wrong.');

Use var_export to covert data to a readable string:

    \Log::debug(var_export(DB::getQueryLog(), true));



Sessions <a name="sessions">
------------------------------
You can use a session to return you to a main page, after making some change in a related (sub) page. Like this:

```php
    public function edit($id)
    {
        if ( ! Session::get('errors')) {
            Session::put('referrer', Request::server('HTTP_REFERER'));
        }

        $item = $this->items->find($id);
        return View::make('api/edit')
            ->with('item', $item);
    }

    public function update($id)
    {
        ... do the update ...

        return Redirect::to(Session::get('referrer'));
    }
```

For this particular function, laravel has a built-in method, `Redirect::intended('default')`


Redirections <a name="redirection">
-------------------------------------
Laravel has several methods for handling redirections. The main facade is Redirect. We can use:

    Redirect::intended('default')
    Redirect::home()                // goes to the route named 'home'
    Redirect::back()                // goes to the calling url
    Redirect::route('named_route')
    Redirect::to('path')
    Redirect::action('Controller@method')

    Redirect::to(route('controller', $record_id) . '#section')


Redirect::back doesn't work so well during testing, because test drivers don't set a refering URL. This can be done instead:

    protected function getRedirectBack($defaultRoute)
    {
        $referer = Request::header('referer');

        return $referer ? Redirect::to($referer) : Redirect::route($defaultRoute);
    }

Alternately, we can create an alternate Redirector class.

```php
    < ?php 
    use Illuminate\Routing\Redirector as LaravelRedirector;


    class Redirector extends LaravelRedirector
    {

       /**
         * Create a new redirect response to the previous location.
         *
         * @param  string  $fallback
         * @param  int     $status
         * @param  array   $headers
         * @return \Illuminate\Http\RedirectResponse;
         */
        public function back($fallback = null, $status = 302, $headers = array())
        {
            $back = $this->generator->getRequest()->headers->get('referer');
      
            if (is_null($back))
            {
              $back = $fallback;
            }
        
            return $this->createRedirect($back, $status, $headers);
        }
    }
```

We tell Laravel to use it with a RoutingServiceProvider, like so:

```php
    < ?php

    use Illuminate\Routing\Router;
    use Illuminate\Routing\RoutingServiceProvider as LaravelRoutingServiceProvider;

    class RoutingServiceProvider extends LaravelRoutingServiceProvider 
    {
        /**
         * Register the Redirector service.
         *
         * @return void
         */
        protected function registerRedirector()
        {
            $this->app['redirect'] = $this->app->share(function($app)
            {
                $redirector = new Redirector($app['url']);

                // If the session is set on the application instance, we'll inject it into
                // the redirector instance. This allows the redirect responses to allow
                // for the quite convenient "with" methods that flash to the session.
                if (isset($app['session.store']))
                {
                    $redirector->setSession($app['session.store']);
                }

                return $redirector;
            });
        }

    }
```

... and, of course, load the redirection service provider in app.php


Events <a name="events">
--------------------------
Events are a very powerful implementation of the Observer pattern. Many of Laravel's objects will send an event as they do work, and you can trap those events, look at them, and pass back a value to let Laravel know if it should cancel the current activity. It generally passes a full object to you, so you can see what is happening. To use events, enter this in a place where it will be executed (eg, start.php, or routes.php, or filters.php, or include a path to something else):

```php
 ```php
         Event::listen('eloquent.creating*', function($model) {
        // will be fired before a record is created in any table

        Event::listen('illuminate.query', function ($sql, $bindings, $times) {
            // will be fired for every SQL query
    ```

    They can also be put directly in your models:

    ```php
        class Post extends eloquent {
            public static function boot()
            {
                parent::boot();

                static::creating(function($post)
                {
                    $post->created_by = Auth::user()->id;
                });
    ```

    Info at:
    http://driesvints.com/blog/using-laravel-4-model-events/
    http://jasonlewis.me/article/laravel-events

    Here are some standard Laravel event types:

       ```


 We tell Laravel to use it with a RoutingServiceProvider, like so:

```php
    < ?php

    use Illuminate\Routing\Router;
    use Illuminate\Routing\RoutingServiceProvider as LaravelRoutingServiceProvider;

    class RoutingServiceProvider extends LaravelRoutingServiceProvider 
    {        Event::fire('laravel.done                [Response $response]');
         /**
         * Register the Redirector service.
         *
         * @return void
         */
        protected function registerRedirector()
        {
            $this->app['redirect'] = $this->app->share(function($app)
            {
                $redirector = new Redirector($app['url']);

                // If the session is set on the application instance, we'll inject it into
                // the redirector instance. This allows the redirect responses to allow
                // for the quite convenient "with" methods that flash to the session.
                if (isset($app['session.store']))
                {
                    $redirector->setSession($app['session.store']);
                }

                return $redirector;
            });
        }

    }
```

... and, of course, load the redirection service provider in app.php
   Event::fire('laravel.log                 [String $type, String $message]');
    Event::fire('laravel.query               [String $sql, Array $bindings, String $time]');
    Event::fire('laravel.resolving           [String $type, Mixed $object]');
    Event::fire('laravel.composing: {view}   [View $view]');
    Event::fire('laravel.started: {bundle}   [String $bundle]');
    Event::first('laravel.controller.factory [String $controller]');
    Event::first('laravel.config.loader      [String $bundle, String $file]');
    Event::first('laravel.language.loader    [String $bundle, String $language, String $file]');
    Event::until('laravel.view.loader        [String $bundle, String $view]');
    Event::until('laravel.view.engine        [View $view]');
    Event::first('laravel.view.filter        [String $content, String $path]');
    Event::fire('eloquent.saving             [Eloquent $model]');
    Event::fire('eloquent.saving: {model}    [Eloquent $model]');
    Event::fire('eloquent.updated            [Eloquent $model]');
    Event::fire('eloquent.updated: {model}   [Eloquent $model]');
    Event::fire('eloquent.created            [Eloquent $model]');
    Event::fire('eloquent.created: {model}   [Eloquent $model]');
    Event::fire('eloquent.saved              [Eloquent $model]');
    Event::fire('eloquent.saved: {model}     [Eloquent $model]');
    Event::fire('eloquent.deleting           [Eloquent $model]');
    Event::fire('eloquent.deleting: {model}  [Eloquent $model]');
    Event::fire('eloquent.deleted            [Eloquent $model]');
    Event::fire('eloquent.deleted: {model}   [Eloquent $model]');
    Event::first('500');
    Event::first('404');

Here is an example event session (after capturing all events...):

    eloquent.saving: Kalani\KWeb\Models\Object
    eloquent.creating: Kalani\KWeb\Models\Object
    eloquent.created: Kalani\KWeb\Models\Object
    eloquent.saved: Kalani\KWeb\Models\Object
    eloquent.saving: Kalani\KWeb\Models\Page
    called creating function...                     // This is something I added to the model
    info                                            // It writes an info log entry
    eloquent.creating: Kalani\KWeb\Models\Page
    eloquent.created: Kalani\KWeb\Models\Page
    eloquent.saved: Kalani\KWeb\Models\Page

    creating: home
    composing: home
    creating: templates/main
    composing: templates/main
    creating: partials/styles
    composing: partials/styles
    creating: partials/branding
    composing: partials/branding

In this case, it's working on these objects (the objects are sent as a parameter):

    Kalani\KWeb\Models\Object
    Kalani\KWeb\Models\Page
    Illuminate\View\View

To capture all events, I did this:

```php
    Event::listen('*', function($arg1, $arg2) {
        $location = __DIR__.'/storage/logs/events.txt';
        foreach(array($arg1, $arg2) as $foo) {
            if (($foo) && is_string($foo)) {
                file_put_contents($location, 's: '.$foo.PHP_EOL, FILE_APPEND);
            } elseif (is_object($foo)) {
                file_put_contents($location, 'o: '.get_class($foo).PHP_EOL, FILE_APPEND);
            } else {
                file_put_contents($location, 'x: '.gettype($foo).PHP_EOL, FILE_APPEND);
            }
        }
    });
```


Uploading Files
--------------------
In the form:

    {{ Form::open(['route'=>'data.store', 'files'=>true]) }}
    {{ Form::file('thumbnail') }}

In the controller:

    $file = Input::get('thumbnail');
    $file->move(public_path.'/images/');



Using Laravel/Blade with AngularJS (and other libraries)
----------------------------------------------------------
Both Laravel and Angular use the double curly brackets.

Use '@{{' at the beginning of a brace to leave the braces (eg, make compatible for angular) 

Other tips available here:
http://scotch.io/bar-talk/quick-tip-using-laravel-blade-with-angularjs

Changing the syntax in Angular:

```js
    var sampleApp = angular.module('sampleApp', [], function($interpolateProvider) {
        $interpolateProvider.startSymbol('<%');
        $interpolateProvider.endSymbol('%>');
    });
```

Changing the syntax in Blade:

```php
    Blade::setContentTags('<%', '%>');      // for variables and all things Blade
    Blade::setEscapedContentTags('<%%', '%%>');     // for escaped data
```

