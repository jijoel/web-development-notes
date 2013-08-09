MVC 
======

MVC is a software pattern that separates the representation of information
from the user's interactions with it.

* [Model](#model)           - Classes which contain all of the business logic of the application
* [View](#view)             - Classes which format and present data to the user
* [Controller](#controller) - Classes which coordinate user actions and routing

This includes an overview of the MVC pattern, and specific examples of how it's done in Laravel.

* [MVC in Laravel](#laravel) 
    * [Controller](#laravel-controller)
    * [Model](#laravel-model)
        * [Scopes](#laravel-model-scopes)
    * [Views](#laravel-view)
        * [Templates](#laravel-view-template)
        * [Partials](#laravel-view-partials)
        * [Main](#laravel-view-main)
        * [Index](#laravel-view-index)
        * [Create](#laravel-view-create)
        * [Edit](#laravel-view-edit")
        * [Show](#laravel-view-show")
    * [Incorporating Javascript and AJAX](#ajax")



http://www.bennadel.com/blog/2379-A-Better-Understanding-Of-MVC-Model-View-Controller-Thanks-To-Steven-Neiland.htm

### Controller <a name="controller">

The "C" in "MVC"

The Controller's job is to translate incoming requests into outgoing responses. In order to do this, the controller must take request data and pass it into the Service layer. The service layer then returns data that the Controller injects into a View for rendering. This view might be HTML for a standard web request; or, it might be something like JSON (JavaScript Object Notation) for a RESTful API request.

Red Flags: My Controller architecture might be going bad if:

The Controller makes too many requests to the Service layer.
The Controller makes a number of requests to the Service layer that don't return data.
The Controller makes requests to the Service layer without passing in arguments.

### View <a name="view">

The "V" in "MVC"

The View's job is to translate data into a visual rendering for response to the Client (ie. web browser or other consumer). The data will be supplied primarily by the Controller; however, the View may also have a helper that can retrieve data that is associated with the rendering and not necessarily with the current request (ex. aside data, footer data).

Red Flags: My View architecture might be going bad if:

The View contains business logic.
The View contains session logic.

### Model <a name="model">

The "M" in "MVC"

The Model's job is to represent the problem domain, maintain state, and provide methods for accessing and mutating the state of the application. The Model layer is typically broken down into several different layers:

Service layer - this layer provides cohesive, high-level logic for related parts of an application. This layer is invoked directly by the Controller and View helpers.

Data Access layer - (ex. Data Gateway, Data Access Object) this layer provides access to the persistence layer. This layer is only ever invoked by Service objects. Objects in the data access layer do not know about each other.

Value Objects layer - this layer provides simple, data-oriented representations of "leaf" nodes in your model hierarchy.

Red Flags: My Model architecture might be going bad if:

The Model contains session logic.
The Value Objects retain (ie. store) references to Service objects or Gateway objects.



Other Objects
---------------

The MVC pattern is a very good way to split up applications; there are some other objects layers that can split them up even further, ultimately to make it easier to manage:

    Services     - Business logic that can talk to multiple models 
    Presenters   - Logic to be passed to the view



MVC in Laravel <a name="laravel">
===================================

Laravel uses the MVC pattern, with Models, Views, and Controllers. This will show standard code and talk about each of the standard functions we do. 

### Controller <a name="laravel-controller">

``` php
class ModelsController extends Controller
{
    protected $model;

    public function __construct(ModelType $model)
    {
        $this->model = $model;
    }

    /**
    * Display a listing of the resource.
    *
    * @return Response
    */
    public function index()
    {
        $model = $this->model->all();         // Will return all records
        $model = $this->model->paginate();    // Will return n (default 15) records

        return View::make('models.index', compact('model'));
    }

    /**
    * Show the form for creating a new resource.
    *
    * @return Response
    */
    public function create()
    {
        return View::make('models.create');
    }

    /**
    * Store a newly created resource in storage.
    *
    * @return Response
    */
    public function store()
    {
        $input = Input::all();
//        $validation = Validator::make($input, Rules::getRules($this->model));
        $validation = Validator::make($input, ModelType::$rules);     // If we have a rules array

        if ($validation->passes())
        {
            $this->model->create($input);

            return Redirect::route('models.index');
        }

        return Redirect::route('models.create')
            ->withInput()
            ->withErrors($validation)
            ->with('message', 'There were validation errors.');
    }

    /**
    * Display the specified resource.
    *
    * @param  int  $id
    * @return Response
    */
    public function show($id)
    {
        $model = $this->model->findOrFail($id);

        return View::make('models.show', compact('model'));
    }

    /**
    * Show the form for editing the specified resource.
    *
    * @param  int  $id
    * @return Response
    */
    public function edit($id)
    {
        $model = $this->model->find($id);

        if (is_null($model))
        {
            return Redirect::route('models.index');
        }

        return View::make('models.edit', compact('model'));
    }

    /**
    * Update the specified resource in storage.
    *
    * @param  int  $id
    * @return Response
    */
    public function update($id)
    {
        $input = array_except(Input::all(), '_method');
        $validation = Validator::make($input, ModelType::$rules);

        if ($validation->passes())
        {
            $model = $this->model->find($id);
            $model->update($input);

            return Redirect::route('models.show', $id);
        }

        return Redirect::route('models.edit', $id)
            ->withInput()
            ->withErrors($validation)
            ->with('message', 'There were validation errors.');
    }

    /**
    * Remove the specified resource from storage.
    *
    * @param  int  $id
    * @return Response
    */
    public function destroy($id)
    {
        $this->model->find($id)->delete();

        return Redirect::route('models.index');
    }
}
```
    

### Model <a name="laravel-model">

The model will contain validation rules (unless they're auto-generated). The model layer is also where business logic should go. We can also connect to other objects here.

``` php
    class Foo extends Eloquent 
    {
        protected $guarded = array();

        public static $rules = array(
            'foo' => 'required',
            'bar' => 'required'
        );

    // Attributes ---------------------------------------

        
        public function getPriorityTextAttribute()
        {
            if ($this->priority >= 5)
                return 'High';

            if($this->priority>=2)
                return 'Med';

            return 'Low';
        }

        public function getNameAttribute()
        {
            return $this->first_name . ' ' . $this->last_name;
        }

    // Scopes (filters; see [Scopes](#laravel-model-scopes)) 

        public function scopeUnassigned($query)
        {
            $query->where('assignee_id', 0);
        }

        public function scopeOpen($query)
        {
            $query->whereRaw('closed_at=0');
        }

        public function scopeClosed($query)
        {
            $query->whereRaw('closed_at<>0');
        }


    // Relationships ----------------------------------

        public function items()
        {
            return $this->hasMany('Item');
        }

        public function bar()
        {
            return $this->belongsTo('Bar');
        }

        public function roles()
        {
            return $this->belongsToMany('Role');
        }
    }
```

#### Scopes <a name="laravel-model-scopes">

You can also put scopes into the models. These act, basically, like chainable filters. For example:

``` php
    class User extends Eloquent 
    {
        public function scopeApproved($query)
        {
            $query->where('approved', 1);
        }

        public function scopePopular($query, $minimum = 100)
        {
            $query->where('votes', '>', $minimum);
        }
    }
```

You could use it like this:

    $users = User::approved()->popular(50)->get();




### Views <a name="laravel-view">

It's a good practice to have a template, with individual views extending the template. Personally, I also find partial views to make things a lot easier to work with. Here's an example of how it works:

#### template <a name="laravel-view-template">
(following a bootstrap structure; for a 3-column layout)

``` html
<!doctype html>
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Page Title</title>
    @include('partials/styles')
    @yield('css')
</head>

<body>
    <div class="container">
        @include('partials/masthead')
        @include('partials/menu')

        <div class="row">
            @include('partials/sidebar')
            @include('partials/comments')

            {{-- This is where the actual page content will go--}}
            <div class="content">
                @include('partials/message')
                @yield('content')
            </div>
        </div> <!--  .row -->
    </div> <!-- end .container -->

    <div id="footer">
    </div> <!-- end footer -->

    @include('partials/scripts')
    @yield('js')
</body>
</html>
```

### Partials <a name="laravel-view-partials">
Partials are sections that might repeat, or sections that I want to have separated (so I can find and modify them more easily). A partial for the styles (above) might look like this:

``` html
<link rel="stylesheet" href="/components/bootstrap/css/bootstrap.css" media="screen">
<link rel="stylesheet" href="/components/bootstrap/css/bootstrap-responsive.css" media="screen">
<link rel="stylesheet" href="/components/font-awesome/css/font-awesome.css">
<link rel="stylesheet" href="/assets/css/myProject.css" media="screen">
```

A partial for scripts might look like this:

``` html
<script type="text/javascript" src="/components/jquery/jquery.js"></script>
<script type="text/javascript" src="/components/jquery-ui/jquery-ui-built.js"></script>
<script type="text/javascript" src="/components/bootstrap/js/bootstrap.js"></script>
<script type="text/javascript" src="/components/jquery-autosize/jquery.autosize.js"></script>
<script type="text/javascript" src="/assets/js/myProject.js"></script>
```

Note that these scripts will be available on EVERY page. If I want something just on one page, I can include it in the js or css section. 

Messages can be shown via partials/message:

``` php
@if (Session::has('message'))
    <div class="flash alert">
        <p>{{ Session::get('message') }}</p>
    </div>
@endif
```

On forms, where we're entering specific things, we can include error messages with partials/errors:

``` php
@if ($errors->any())
    <ul>
        {{ implode('', $errors->all('<li class="error">:message</li>')) }}
    </ul>
@endif
```


#### Main view <a name="laravel-view-main">
This would be the view called from a controller. It follows this basic structure:

    @extends('template')

    @section('content')
    @stop

    @section('css')
    @stop

    @section('js')
    @stop


#### Index <a name="laravel-view-index">

As an example, this might be an index form entered as a main view:

``` html
@extends('templates/main')

@section('content')
    <h1>People</h1>
    <table id="people-list">
        <thead>
            <tr>
                <th>Name</th>
                <th>Gender</th>
                <th>Birthdate</th>
            </tr>
        </thead>
        <tbody>
            @foreach($model as $item)
                <tr>
                    <td>{{ $item->name }}</td>
                    <td>{{ $item->gender }}</td>
                    <td>{{ $item->birthdate }}</td>
                </tr>
            @endforeach
        </tbody>
    </table>

    <p>{{ $page->create }}</p>
    </div>
@stop
```

In this case, we might have used a view presenter to change this:

    <a href="{{ URL::route('people.show', $item->id)}}" title="Go to this record">
        {{ $item->first_name . ' ' . $item->last_name }}</a></td>
    <a href="{{ URL::route('people.show', $item->id)}}" title="Go to this record">
        {{ $item->gender }}</a>
    <a href="{{ URL::route('people.show', $item->id)}}" title="Go to this record">
        {{ $item->birthdate}}</a>
    <a href="{{URL::route('people.create')}}">Add a new Person rechord</a>

To this:

    {{ $item->name }}
    {{ $item->gender }}
    {{ $item->birthdate }} 
    {{ $route->create }}

The logic takes place in the presenter, so the view can just show data in the simplest possible way. Ideally, the view will have few (if any) logic (if) statements; mostly just displaying and looping. 

There are some helper functions we can use:

    {{ link_to_route('foos.create', 'Add new foo') }}
    {{ link_to_route('foos.edit', 'Edit', array($foo->id), array('class' => 'btn btn-info')) }}

To create a button to delete records, we will need to create a separate form, like this:

    {{ Form::open(array('method' => 'DELETE', 'route' => array('foos.destroy', $foo->id))) }}
        {{ Form::submit('Delete', array('class' => 'btn btn-danger')) }}
    {{ Form::close() }}

The form can be hidden, though, so we can put the button wherever we want it, like this:

    {{ Form::button('<i class="icon-trash icon-large"></i> Delete', 
        array('type' => 'submit', 'form'=>'delItem', 'class' => 'btn btn-danger')) }}

    ...
    {{ Form::open(array('method' => 'DELETE' 'route' => array('foos.destroy', $foo->id),
        'id' => 'delItem', 'hidden' => 'hidden')) }}
    {{ Form::close() }}


#### Create <a name="laravel-view-create">

A creation form might look like this:

``` php
@extends('templates.main')

@section('content')
<h1>Create Foo</h1>

{{ Form::open(array('route' => 'foos.store')) }}
    <ul>
        <li>
            {{ Form::label('foo', 'Foo:') }}
            {{ Form::text('foo') }}
        </li>
        <li>
            {{ Form::submit('Submit', array('class' => 'btn')) }}
        </li>
    </ul>
{{ Form::close() }}

@include('partials/errors')
@stop
```

#### Edit <a name="laravel-view-edit">

The edit form needs to have information about the object being edited. We can do that by using Form::model instead of Form::open. This will bind the form to the model's attributes.

``` php
@extends('layouts.scaffold')

@section('main')

<h1>Edit Foo</h1>
{{ Form::model($foo, array('method' => 'PATCH', 'route' => array('foos.update', $foo->id))) }}
    <ul>
        <li>
            {{ Form::label('foo', 'Foo:') }}
            {{ Form::text('foo') }}
        </li>
        <li>
            {{ Form::submit('Update', array('class' => 'btn btn-info')) }}
            {{ link_to_route('foos.show', 'Cancel', $foo->id, array('class' => 'btn')) }}
        </li>
    </ul>
{{ Form::close() }}

@include('partials/errors')
@stop
```

#### Show <a name="laravel-view-show">

Showing a record just lists out (in a non-editable way) the fields. No forms required, but we could include links to other routes for editing.

    {{ link_to_route('foos.edit', 'Edit', array($foo->id), array('class' => 'btn btn-info')) }}


### Example Fields

    <li>
        {{ Form::label('in', 'Standard Text (as input)') }}
        {{ Form::input('text', 'in', 'default value', array('placeholder' => 'foo')) }}
    </li>

    <li>
        {{ Form::label('text', 'Standard Text') }}
        {{ Form::text('text', 'default', array('placeholder' => 'foo')) }}
    </li>

    <li>
        {{ Form::label('long', 'Long Memo') }}
        {{ Form::textarea('long', 'default', array('id'=>'longMemo', 'style'=>'width: 100px; height: 20px')) }}
    </li>

    <li>
        {{ Form::label('pw', 'Password') }} 
        {{ Form::password('pw', array('placeholder'=>'Password')) }}
    </li>

    <li>
        {{ Form::label('pw2', 'Password with icon') }} 
        <div class="input-prepend">
            <span class="add-on"><i class="icon-lock"></i></span>
            {{ Form::password('pw2', array('placeholder'=>'Password')) }}
        </div>
    </li>

    <li>
        {{ Form::label('email2', 'Email With Icon') }}
        <div class="input-prepend">
            <span class="add-on"><i class="icon-envelope"></i></span> 
           {{ Form::email('email2', 'joel@kalani.com', array('placeholder'=>'email')) }}
        </div>
    </li>

    <li>
        {{ Form::label('file', 'File Uploader') }} 
        {{ Form::file('file') }}
    </li>

    <li>
        {{ Form::label('select', 'Dropdown list with default') }} 
        {{ Form::select('select', array('L' => 'Large', 'S' => 'Small'), 'S') }}
    </li>

    <li>
        {{ Form::label('select', 'Dropdown list with groupings') }} 
        {{ Form::select('animal', array(
                'Cats' => array('leopard' => 'Leopard'),
                'Dogs' => array('spaniel' => 'Spaniel'),
            )); }}
    </li>

    <li>
        <label class="checkbox">
            {{ Form::checkbox('check1', 'value') }} Checkbox
        </label>
    </li>

    <li>
        <label class="checkbox">
            {{ Form::checkbox('check2', 'value') }} Checkbox2
        </label>
    </li>

    <li>
        <label class="radio">
        {{ Form::radio('radio1', 'value') }}  Radio Button 1
        </label>
    </li>
    <li>
        <label class="radio">
        {{ Form::radio('radio1', 'value', True) }}  Radio Button 2
        </label>
    </li>        

    <li>
        {{ Form::label('date', 'Date') }}
        {{ Form::input('date', 'date') }}
    </li>

    <li>
        {{ Form::label('num', 'Number') }}
        {{ Form::input('number', 'num', 10) }}
    </li>

    <li>
        {{ Form::label('calendar', 'Calendar (include jquery-ui') }}
        <div class="input-append">
           {{ Form::text('calendar', '7/5/2013 4:00 pm', array('placeholder'=>'Please enter start time')) }}
            <span class="add-on"><i class="icon-calendar"></i></span> 
        </div>
    </li>

    <li>
        {{ Form::label('inline_calendar', 'Inline Calendar (requires jquery-ui') }}
        <div id="inline_calendar"></div>
    </li>

    <li>
        {{ Form::label('phone', 'Phone Number') }}
        {{ Form::input('telephone', 'phone', '808-965-3897') }}
    </li>

    <li>
        {{ Form::label('url', 'URL') }}
        {{ Form::input('url', 'url', 'http://foo.com') }}
    </li>

    <li>
        {{ Form::label('search', 'Search (a stylistically different text box)') }}
        {{ Form::input('search', 'search', 'foo') }}
    </li>


### Incorporating Javascript and AJAX <a name="ajax">

Some of the above fields require javascript in order to work. Some will need ajax (particularly to load data).

    $('#calendar').datetimepicker({
        dateFormat: "m/d/yy",
        timeFormat: "hh:mm tt",
        hourGrid: 12,
        minuteGrid: 15,
        stepMinute: 5,
        // hour: 12,            // default hour set
        minDate: new Date(2013, 05, 10),
    });

    $('#inline_calendar').datepicker({
        inline: true,
        minDate: new Date(2013, 05, 03),
        maxDate: new Date(2013, 06, 10),
    });

    var cache = {};
    $( "#autocomplete" ).autocomplete({
        minLength: 1,
        source: function( request, response ) {
            var term = request.term;
            if ( term in cache ) {
                response( _.pluck(cache[term], 'state_name') );
                return;
            }
 
            $.getJSON( "resource_to_get", request, function( data, status, xhr ) {
                cache[term] = data;
                response( _.pluck(cache[term], 'list_item') );
            });
        }
    });

In order for the ajax to work, there has to be a controller that can receive the input, and return a result. Something like this:

    public function index()
    {
        if (Request::ajax()) {
            return $this->items->search(Request::all());
        }

In the model, we might have a function that looks like this:

    public function search($searchTerms = array())
    {
        if (empty($searchTerms)) {
            return $this->take($this->perPage)->get();
        }

        $search = $this->where('search_field', 'like', $searchTerms['term'].'%');

        return $search->take($this->perPage)->get();
    }

In many cases, we'll want to search for a value, but return a key. To do this, in the view, we use a hidden field:

    <li>
        {{ Form::label('assignee', 'Assigned To:') }}
        {{ Form::text('assignee', Null, array('id'=>'assignee')) }}
        {{ Form::hidden('assignee_id', Null, array('id'=>'assignee_id')) }}
    </li>

We include this in javascript:

    $( "#assignee" ).autocomplete({
        minLength: 1,
        source: '/users',
        select: function(event, ui) {
            var selectedObj = ui.item;
            $('#assignee').val(selectedObj.label);
            $('#assignee_id').val(selectedObj.id);
            return false;
        }
    });

... and the controller looks like this:

    if (Request::ajax()) {
        return $this->model->search(Request::all())
            ->get(array('id','name as label'));
    }

