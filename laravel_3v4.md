Differences Between Laravel 3 and 4

Run a server to show Laravel data (need php 5.4.x):

    L3: php -S localhost:8888 -t public     (go to localhost:8888 to get to the page)
    L4: php artisan serve
 
Route declarations:

    L3: Route::get('users/(:num)', function(id){ var_dump( $id ); });
    L4: Route::get('users/{id}', function(id){ var_dump( $id ); })
              ->where('id','\d+');        // regular expression, 1+ digits
 
Templates:

    L3: Route::get('/', 'Home@index');
 
        Home_Controller
          public $layout='master';
          public function action_index() {
              $this->layout->content = View::make('home.index');
              (or return View::make('home.index'), with @layout('master') in index.blade.php)
       
        master.blade.php:
            <div>@yield('content')
        index.blade.php:
            @layout('master')          // if layout not specified in controller
            @section('content')
                Home Page here
            @endsection
 
    L4: Route::get('/', 'HomeController@index');
 
        HomeController:
          protected $layout='master';
          public function index() {
              $this->layout->content = View::make('home.index')
              (or return View::make('home.index'), with @extends('master') in index.blade.php)
     
        master.blade.php:
            <div>@yield('content')
        index.blade.php:
            @extends('master')       // if layout not specified in controller
            @section('content')
                Home Page here
            @stop  (to declare the end of a section)
   
Forms:

    L3:   {{ Form::open('users') }}
            {{ Form::label('firstname', 'First Name') }}
            {{ Form::text('firstname') }}
          {{ Form::close() }}
 
    L4:   <currently unavailable>
 
    L3:  HTML::link
    L4:  HTML::to
  
Migrations:

    L3:
        php artisan migrate:install                         // install the migrations table
        php artisan migrate:make create_posts_table         // then update the table
        artisan migrate:rollback                            // roll back the previous migration
        artisan migrate:reset                               // roll back all migrations

    L4: (all of the L3 commands will work...)
        php artisan migrate:make create_posts_table --table=posts --create
            (does not need artisan migrate:install)
        artisan migrate:refresh                       // roll back everything, and re-run
        artisan migrate:refresh --seed                // do the above, and run all seeds

Seeding tables:

    L3: <does not have that functionality>
    L4: Prepare seed files as described above, then enter:

            artisan db:seed

        or:

            artisan migrate:refresh --seed
 

Laravel 4 includes Route Model Binding (inject model instances into route closures)
It automatically returns all collections from a method or closure as JSON

Routes (without route model binding): 

    L3:   Route::get('users', function() { return User::all(); }});
    L4:   Route::get('users/{id}', function($id) { return User::find($id); }})
            ->where('id', '\d+');

Routes (with route model binding):

    L4:   Route::model('user', 'User');
    or:   Route::bind('user', function($id, $route) { return User::where('id', $id)->first(); }));
          Route::get('users/{user}', function (User $user) { return $user; }));
 
Linking to routes in a view: 

    L3:   HTML::link_to_route('routeName', 'Display')
    L4:   <a href="{{URL::route('routeName')}}>Display</a>
    or:   HTML::to(URL::route('routeName'), 'Display')

Redirection:

    L3:   Redirect::to_route('routeName')
    L4:   Redirect::route('routeName')

Redirection with errors and input:

    L3:   Redirect::with_errors()->with_input()
    L4:   Redirect::withErrors()->withInput()
 
Reporting errors in a view:

    L3:   @if($errors->has())
            <p>The following errors have occurred:</p>
     
            <ul id="form-errors">
                {{ $errors->first('username','<li>:message</li>')}}
                {{ $errors->first('password','<li>:message</li>')}}
            </ul>
          @endif
 
    L4:   The L3 way still works, or:
          @if($errors->has())                    // or @if(count($errors)>0)
            <ul id="form-errors">
                <li>{{ $errors->first('username') }}</li>
                <li>{{ $errors->first('password') }}</li>
           </ul>
          @endif
 
Laravel creates everything for REST. Refer to individual objects like this:

    <a href="{{ route('questions.show', ['questions'=>$question->id]) }}">
 
Updating data: 

    L3:   Questions::update($id, array(
            'question' => Input::get('question'),
            'solved' => Input::get('solved'),
          ));
 
    L4:   Questions::where('id','=',$id)->update(array(
            'question' => Input::get('question'),
            'solved' => Input::get('solved'),
          ));
 
Including stylesheets: 

    L3:   {{ HTML::style('css/main.css') }}
    L4:   <link href="{{route('home')}}/css/main.css" media="all" type="text/css" rel="stylesheet">

