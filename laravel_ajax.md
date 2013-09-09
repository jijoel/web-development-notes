Using Laravel with Javascript and ajax
==========================================

We can link to routes (in a view) using resources like this:

``` php
    Route::resource('todos', 'TodosController', 
        array('only'=>array('index','store','update', 'destroy')));
```

The routes defined by that look like this:

    GET /                          home                   TodosController@index
    GET /todos                     todos.index            TodosController@index
    POST /todos                    todos.store            TodosController@store
    PUT /todos/{todos}             todos.update           TodosController@update
    PATCH /todos/{todos}                                  TodosController@update
    DELETE /todos/{todos}          todos.destroy          TodosController@destroy

In the view, we can call them like this:

``` html
    <a class="editEntryAnchor" 
        href="{{ URL::route('todos.update', $item->id) }}">Edit</a>
    <a class="deleteEntryAnchor" 
        href="{{ URL::route('todos.destroy', $item->id) }}">Delete</a>
```

We can't generally call PUT and DELETE via the browser, but they can be called in Javascript via ajax in JQuery:

```php
    $('a.deleteEntryAnchor').click(function() {
        var thisparam = $(this);
        thisparam.parent().parent().find('p').text('Please Wait...');
        $.ajax({
            type: 'DELETE',
            url: thisparam.attr('href'),
            success: function() {
                thisparam.parent().parent().fadeOut('slow');
            }
        });
        return false;
    });

    $('.editEntryAnchor').click(function() {
        var $this = $(this);
        var oldText = $this.parent().parent().find('p').text();
        var id = $this.parent().parent().find("#id").val();
        $this.parent().parent().find('p').empty()
            .append('<textarea class="newDescription" cols="33">' + oldText + '</textarea>');
        
        $('.newDescription').blur(function() {
            var newText = $(this).val();
            $.ajax({
                type: 'PUT',
                url: $this.attr('href'),
                data: 'description=' + newText,
                success: function() {
                    $this.parent().parent().find('p').empty().append(newText);
                }
            })
        });

        return false;
    });
```

In the controller, we can see if we're being sent an AJAX request:

```php
    if (Request::ajax()) {
        // do something
    }
```

We can also respond to an AJAX call with php data:

``` php
    $category = new Category;
    return Response::json($category->search(Request::all())
        ->get(array('id','name as label')));
```

This can be read directly by javascript (for instance, in a select list):

``` js
    function loadAjaxForSelectBox(url, selectBox) 
    {
        $.ajax({
            url: url,
        }).done(function(data) {
            var selectableItems = '';
            data.forEach(function(entry) {
                var extra = '';
                if(typeof entry.extra != 'undefined') {
                    extra = entry.extra;
                }

                var newItem = '<option value=' + entry.id + extra + '>' + entry.label + '</option>' ;
                selectableItems += newItem;
            });
            $(selectBox).html(selectableItems);
        });
    }
```


Integrating validation errors with Ajax
------------------------------------------------------

Consider this form:

```php
    <?= Form::open(array('url' => 'login/verify', 'method' => 'POST', 'id' => 'login')) ?>

    <h3><?= Form::label('email', 'Your e-mail') ?></h3>
    <?= Form::text('email', Input::old('email')) ?>

    <h3><?= Form::label('password', 'Your password') ?></h3>
    <?= Form::password('password') ?>

    <br />
    <?= Form::submit('Go', array('class' => 'btn')) ?>
    <?= Form::close() ?>
    <?= HTML::script('js/jquery.validate-login.js') ?>
```

Now the verify method from LoginController:

```php
    $rules = array( 
        'email'  => 'required',
        'password' => 'required'
    );

    $v = Validator::make(Input::all(), $rules);

    if ( ! $v->passes())
    {
        if(Request::ajax())
        {                    
            $response_values = array(
                'validation_failed' => 1,
                'errors' =>  $v->errors()->toArray());              
            return Response::json($response_values);
        }
        else
        {
        return Redirect::to('login')
            ->with('validation_failed', 1)
            ->withErrors($v);
        }       
    }
```

And finally the js:

``` js
    $(document).ready(function()
    {
        $('form#login).submit(function()
        {
            
            $.ajax({
                url: "http://localhost/laravel/public/login/verify",
                type: "post",
                data: $('form#login').serialize(),
                datatype: "json",
                beforeSend: function()
                {
                    $('#ajax-loading').show();
                    $(".validation-error-inline").hide();
                }
                })
                .done(function(data)
                {
                    if (data.validation_failed == 1)
                    {
                        var arr = data.errors;
                        $.each(arr, function(index, value)
                        {
                            if (value.length != 0)
                            {
                                $("#" + index).after('<span class="text-error validation-error-inline">' + value + '</span>');
                            }
                        });
                        $('#ajax-loading').hide();
                    }
                })
                .fail(function(jqXHR, ajaxOptions, thrownError)
                {
                      alert('No response from server');
                });
                return false;
        });
    });
```

The .done() method will render the content from inside .after() right next to the field with a validation error. So it will go from this:

    <input name="email" type="text" id="email">

To this:

    <input name="email" type="text" id="email">
    <span class="text-error validation-error-inline">The your e-mail field is required</span>

When the form is submitted again, the beforeSend option from the ajax request will wipe out any tag containing validation-error-inline in its class property.

With all those nice, fancy and pretty validation rules from laravel's form validation, I considered sacrificing client-side form validation and have a middle term. And with little additiions, validation will work the same with or without javascript-enabled, in this case will be the same even aesthetically.

