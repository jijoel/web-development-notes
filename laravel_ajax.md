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

