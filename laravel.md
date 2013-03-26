Laravel -------------------------------------------------------------------------

Using Laravel 4:  http://four.laravel.com


Setup
--------

To set up laravel, copy the basic project from above, and run composer install to get all of the dependencies.

Set the project root directory in apache to the /public folder
(this is a place for css, img, js, etc. files, also)

make storage directory writable:
    chmod -R o+w storage
    (or chown www-data storage/*)

application key:
    app/config/app.php
use command line to generate secure application key:
    php artisan key:generate

Laravel configuration information stored in project/app/config
Each file here returns an array with configuration information (closures are OK)
Access it at any time with Config::get. Use the file name and array key:
    Config::get('app.timezone')

For any classes that use facades (most of Laravel's classes), get the original class name with:
    echo get_class(App::getFacadeRoot());       (App, or any other class)


Laravel’s components are instances that exist within the Laravel 4 $app container object. They can be called in each of these ways:
 
    $app['component']->methodName();
    $app->component->methodName();.
    Component::methodName();


    
IoC Binding
---
To bind classes to the IoC container (for unit testing):
 
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
 
At this point, we can make the class, using
    $app->make('UsersController') ;


    
Migrations and Seeding
-----------------------
Migrations are like version control for your databases.
 
    php artisan migrate:make create_users_table 

creates the basic structure for a new 'users' table. Fill it in further, to get:
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

Use seeds to add sample records:
Create a file within the app/database/seeds folder that has the same name as the table that it corresponds to; in our case, users.php. Add:
    <?php 
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
 
 
Run the migrations and insert sample records:
    // Make sure the auto-loader knows about these new classes
    php composer.phar dump-autoload

To run the migration:
    php artisan migrate                     // run all migrations that have not been run
    php artisan migrate:refresh             // roll back and re-run all migrations
    php artisan migrate:refresh --seed      // refresh, then seed the database
    php artisan db:seed                     // seed the database


    
Artisan Class Generator    
--------------------------
jeffrey way (nettuts) has created an add-in for artisan, which can automatically generate classes (including models, controllers, several views, routing, and error testing) for you.

    require "way/generators": "dev-master"           (composer.json)
    'Way\Generators\GeneratorsServiceProvider'       (add to providers array)

    php artisan generate:resource

    
    
Controllers
----------------

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
in index(), if you return Photo::all(), you'll get json output

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


Models
----------

By default, the table name will be plural, and the model name will be singular. This can be changed, though…
 
Table   Model   Controller
users   User    UsersController
 
To use something else, in the model,
 
    <?php
    class User extends Eloquent {
        public static $table = 'my_user_table';
    }


    
Routing
------------
Here are a few different ways to send variables to the default view:
 
via the routes.php file:
    $data = array(
        'greeting'   => 'hello',
        'something'  => 'world',
        'items'      => array('Item1', 'Item2', 'Item3', 'Item4'),
        'data_items' => array('First','Second')
    );

    Route::get('/', function() {
        return View::make('home.index', $data);
    };

or:

    Route::get('/', function() {
        return View::make('home.index')->with($data);        
    });

or:

    Route::get('/', function() {
        $view = View::make('home.index');
        $view->greeting = 'Hi';
        $view->something = 'Everyone';
        return $view;
    });
 
Via a controller:
 
(in routes.php):
    Route::controller('home');
 
(in controllers/home.php):
    public function action_index() {
        return View::make('home.index', $data);
    }

