Laravel Error Handling
========================


Validation Errors
-------------------

Validation is done like this (in a controller):

    $input = Input::all();
    $validation = Validator::make($input, Model::$rules);

    if ($validation->fails())
    {
        return Redirect::route('items.create')
            ->withInput()
            ->withErrors($validation)
            ->with('flash', 'There were validation errors.');
    }


In the view:

    @if (Session::has('flash'))
        <div class="flash alert">
            <p>{{ Session::get('flash') }}</p>
        </div>
    @endif

    @if ($errors->any())
        <ul>
            {{ implode('', $errors->all('<li class="error">:message</li>')) }}
        </ul>
    @endif


