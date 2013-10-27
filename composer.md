Composer
===============

* [Usage](#usage)
* [Loading a package from a VCS repository (eg, github)](#github)
* [Composer Package Notes](#packages)
    * [Carbon](#carbon)
    * [Presenter](#presenter)




Usage <a name="usage">
------------------------

Use composer to install additional components for the app.

Get composer at http://getcomposer.org/
It looks for a file called composer.json in your project root directory:

```json
    {
        "require": {
                "laravel/framework": "4.0.*",
                "mockery/mockery": "dev-master"
        },
        "autoload": {
                "classmap": [
                        "app/commands",
                        "app/controllers",
                        "app/models",
                        "app/database/migrations",
                        "app/database/seeds",
                        "app/tests"
                ]
        },
        "minimum-stability": "dev"
    }
```

Basic functions:

    composer install                  // installs packages
    composer update                   // updates packages
    composer update --dev             // updates packages for the require-dev section
    composer dump-autoload            // dumps, and refreshes the autoload list

If it fails (due to a script), you can turn the scripts off:

    composer update --no-scripts

Composer will automatically load files from the autoloaded directories.
You can see what classes it found by checking:

    vendor/composer/autoload_classmap.php

If you want to get a git repository:

    composer install --prefer-source

Installing (or updating) one package only:

    composer update vendor/package

You can combine these, to update one package for source. Do an initial installation, then delete the vendor/package folder, and then:

    composer update --prefer-source vendor/package

Note: When using composer, we should upload the file composer.lock to our VCS repository. Having it in the repository assures you that each developer is using the same versions of all packages, and that the version in test is the same as the version in dev. On a production server, run composer install (never composer update).




Loading a package from a VCS repository (eg, github) <a name="github">
-----------------------------------------------------------------------

We don't have to upload packages to packagist.org. They can be downloaded from a git repository, as follows:

http://getcomposer.org/doc/05-repositories.md#repository

```json
    {
        "require": {
            "vendor/my-private-repo": "dev-master"
        },
        "repositories": [
            {
                "type": "vcs",
                "url":  "git@bitbucket.org:vendor/my-private-repo.git"  // for a private repo on bitbucket
            },
            {
                "type": "vcs",
                "url":  "https://github.com/igorw/monolog"              // for a repo on github
            }
        ]
    }
```



Composer Package Notes <a name="packages">
==========================================

There are a lot of packages I find useful. Many of these are listed in [a relative link](links.md). This section has specific notes about specific packages:

* [Carbon](#carbon)     Date/Time management
* [Presenter](#presenter)    Presenter (a Decorator)
* [DataTables](#datatables)     DataTables (server side; works with [datatables client](client.md#datatables))




Carbon <a name="carbon">
--------------------------

Carbon is used for date/time management.
https://github.com/briannesbitt/Carbon
Composer: "nesbot/Carbon": "dev-master"

Create a Carbon object from a mysql datetime value:

```php
    $c = Carbon::createFromTimestamp(strtotime($model->date_time_field));
```


We can add to date values:

```php
    $date = new \Carbon\Carbon;
    $prev = $date->addDay();            // Note this changes the underlying Carbon object
```

Output a formatted date string (Mon d, YYYY):

```php
    $c->toFormattedDateString();        //  Aug 7, 2013
    $c->toDayDateTimeString();          //  Wed, Aug 7, 2013 10:09 PM
    $c->format($format);                //  Any format from http://www.php.net/manual/en/function.date.php
```

Some sample date formats include:

    n/j/Y h:i a             8/18/2013 03:09 pm
    n/j/Y g:i a             8/18/2013 3:09 pm 
    n/j/Y g:i a e           8/18/2013 3:09 pm Pacific/Honolulu
    n/j/Y g:i a T           8/18/2013 3:09 pm HST
    D, M j, Y               Sun, Aug 18, 2013
    l, F j, Y               Sunday, August 18, 2013
    jS \d\a\y\ \o\f F, Y    18th day of August, 2013

By default, Eloquent will convert the created_at, updated_at, and deleted_at columns to instances of Carbon. You may customize which fields are automatically mutated, and even completely disable this mutation, by overriding the getDates method of the model:

```php
public function getDates()
{
    return array('created_at', 'my_date_field');
}
```



Presenter <a name="presenter">
--------------------------------
Presenter is a decorator class that overloads methods and variables so that you can add extra logic to objects and arrays. Add view logic to the presenter, rather than to models, controllers, or views.

https://github.com/robclancy/presenter
Composer: "robclancy/presenter": "1.1.*"

```php
class TicketPresenter extends \Robbo\Presenter\Presenter
{
    public function presentUpdatedAt()
    {
        // use $this->object to get fields from the underlying object
        return $this->getDateString($this->object->updated_at);
    }

    public function getDateString($carbonDate)
    {
        if (is_null($carbonDate) || $carbonDate->timestamp <= 0) {
            return 'n/a';
        }

        $minDate = Carbon::now()->subDays(4);
        $maxDate = Carbon::now()->addDays(4);

        if ($carbonDate->lte($maxDate) && $carbonDate->gte($minDate)) {
            return $carbonDate->diffForHumans();
        }

        return $carbonDate->toDayDateTimeString();
    }
}

In the model, refer to the presenter like this:

```php
class Ticket extends Eloquent implements PresentableInterface
{
    protected $presenter;

    public function __construct(array $attributes = array(), $presenter=Null)
    {
        parent::__construct($attributes);

        $this->presenter = $presenter ?: 'Kalani\TicketTracker\Tickets\TicketPresenter';
    }

    public function getPresenter()
    {
        return new $this->presenter($this);
    }
}
```

In the view, we can use the presented information like this:

```php
    <td>{{ $ticket->updated_at }}</td>
```

(yes, there is no logic in the view. Yay!)

Note: If you are editing records, this will confuse a call to Form::model, because it would be getting the presenter, rather than the underlying model. Use the getObject method:

```php
    {{Form::model($ticket->getObject(), array('route'=>array('tickets.update', $ticket->id), 'method'=>'PATCH'))}}
```


DataTables  <a name='datatables'>
----------------------------------
I really like the [Chumper datatable library](https://github.com/Chumper/Datatable) for server-side datatable processing. It has a lot of potential. The readme is a pretty good howto... It's really flexible (passes data to a view, which I can modify myself). 

It works in a few different parts:

* Server sends view containing datatable to the client
* (this includes a rendered datatable view)
* Client requests table data from the server via ajax
* Server returns table data to the client 


Server sends initial view:

```php
    public function table() 
    {
        $items = $this->repo->datatable();

        return \DataTable::collection($items)
            ->showColumns('slug', 'title')
            ->addColumn('author', function($model) {
                return $model->getPresenter()->author; })
            ->addColumn('updated', function($model) {
                return $model->getPresenter()->updated; })
            ->addColumn('created_at',function($model){
                return $model->created_at->toDateTimeString();})
            ->make();
    }
```

Server sends table data to the client:

```php
    public function datatable()
    {
        $fields = array('id', 'title', 'author_id', 'updated_at', 'created_at');
        return $this->model->get($fields);
    }
```

It is critical that the table data sent to the client has everything the presenter needs to generate presented fields. If it doesn't, the presenter will return Null, and things get a bit confusing.

(I have not yet figured out a good way to sort/filter/etc on custom values. This works, as a kludge):

```php
    public function datatable()
    {
        $fields = array('id', 'title', 'author_id', 'updated_at');

        if ($id = \Input::get('topic')) {
            $repo = \App::make('KBase\Interfaces\TagsRepositoryInterface');
            return $repo->find($id)->pages()->get();
        }
        if ($id = \Input::get('author')) {
            $repo = \App::make('KBase\Interfaces\UsersRepositoryInterface');
            return $repo->find($id)->pages()->get();
        }

        return $this->model->get($fields);
    }
```

Initial View:

```php
<script type="text/javascript" src="/assets/datatables/js/jquery.dataTables.js"></script>
<link rel="stylesheet" type="text/css" href="/assets/datatables/css/jquery.dataTables.css">

{{ DataTable::table()
        ->addColumn('slug', 'title', 'author', 'updated') // column headers
        ->setUrl(route('admin.pages.table'))              // GET table data
        ->setCustomValues('table-url', '/admin/pages')    // for custom rendered datatable
        ->setCallbacks(                                   // for custom callbacks
            'fnServerParams', 'function ( aoData ) {
                aoData.push( { "name": "topic", "value": '.$item->id.' } ); 
            }')
        ->render('common.datatable')                     // or just render() if not custom
}}
```

Example rendered datatable view: 

```html
    <table class="table table-bordered {{ $class = str_random(8) }}">
        <colgroup>
            @for ($i = 0; $i < count($columns); $i++)
            <col class="con{{ $i }}" />
            @endfor
        </colgroup>
        <thead>
        <tr>
            @foreach($columns as $i => $c)
            <th class="head{{ $i }}">{{ $c }}</th>
            @endforeach
        </tr>
        </thead>
        <tbody>
        @foreach($data as $d)
        <tr>
            @foreach($d as $dd)
            <td valign="middle">{{ $dd }}</td>
            @endforeach
        </tr>
        @endforeach
        </tbody>
    </table>
    <script type="text/javascript">
        jQuery(document).ready(function(){
            // dynamic table
            jQuery('.{{ $class }}').dataTable({
                "sPaginationType": "full_numbers",
                "bProcessing": false,
                @foreach ($options as $k => $o)
                {{ json_encode($k) }}: {{ json_encode($o) }},
                @endforeach
                @foreach ($callbacks as $k => $o)
                {{ json_encode($k) }}: {{ $o }},
                @endforeach

            });
     
            @if (isset($values['table-url']))
                $('.{{$class}}').hover(function() {
                    $(this).css('cursor', 'pointer');
                });
                // This will go to value set in customValues (above) when 
                // any row is clicked 
                $('.{{$class}}').on('click', 'tbody tr', function(e) {
                    $id = e.currentTarget.children[0].innerHTML;
                    $url = e.currentTarget.baseURI;
                    document.location.href = "{{ $values['table-url'] }}/" + $id;
                });
            @endif
        });
    </script>

```

