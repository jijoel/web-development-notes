TODO
===============


Short-Term
---------------
Namespace controllers and seeds
Create Common/RestfulController, to handle most functionality currently in ApiController





Long-Term
-----------------
Long term, I would love to have these things available. I might be able to develop some of them; others might be interested/able to develop some.

* [Use Presenter in DataTables](#datatable-presenter)
* [DataTables / Eloquent Queries](#datatable-eloquent)
* [Automatic getDates](#getDates)


DataTables/Presenter <a name="datatable-presenter">
-------------------------------------------------------
Extend the DataTables class so that it can incorporate Presenter data.

DataTables is set up to just read directly from the database. You can add other columns, but it doesn't seem to be able to run Presenter functions...

```php
    public function ajax()
    {
        $tickets = $this->ticket->datatable();

        $d = Datatables::of($tickets)
            ->add_column('priority_text', 'foo', 0)
            ->make();
```

```php
    public function datatable()
    {
        return Ticket::select(array('*'));
    }
```

The Ticket class has an associated Presenter. I can see it, in datatables.php::get_result, with:

    $presenter = $this->query->getModel()->getPresenter();   // JOEL

The table is built with just this:

    $this->result_object = $this->query->get();                 // returns a collection
    $this->result_array = $this->result_object->toArray();      // converts it to an array

toArray is defined like this:

```php
    public function toArray()
    {
        return array_map(function($value)
        {
            return $value instanceof ArrayableInterface ? $value->toArray() : $value;

        }, $this->items);
    }
```

### Work-around

For now, we can use this work-around:

Controller:

```php
    $tickets = Ticket::select(array('id'));

    if($type == 'recent') {
        $tickets = $tickets->recentlyUpdated();
    }

    $stdText = "<"."?php \$p = new Kalani\TicketTracker\Tickets\TicketPresenter("
        . "Kalani\TicketTracker\Tickets\Ticket::find(\$id)); "
        . "echo \$p->%s ?".">";

    return Datatables::of($tickets)
        ->add_column('summary',         sprintf($stdText, 'summary')) 
        ->add_column('description',     sprintf($stdText, 'description'))
        ->add_column('priority',        sprintf($stdText, 'priority'))
        ->add_column('due_at',          sprintf($stdText, 'due_at'))
        ->add_column('closed_at',       sprintf($stdText, 'closed_at'))
        ->add_column('created_at',      sprintf($stdText, 'created_at'))
        ->add_column('updated_at',      sprintf($stdText, 'updated_at'))
        ->make();
```

Model:

```php
    class Ticket extends Eloquent implements PresentableInterface
    {
        public $guarded = array();
        protected $presenter;

        public function __construct(array $attributes = array(), $presenter=Null)
        {
            parent::__construct($attributes);

            $this->presenter = $presenter ?: 'Kalani\TicketTracker\Tickets\TicketPresenter';
        }

        public function scopeRecentlyUpdated($query)
        {
            $query->whereRaw('updated_at>"' . Carbon::now()->subWeek()->toDateTimeString() . '"');
        }

        public function getDates()
        {
            // TODO: Figure out a way to return this array automatically
            return array('created_at','updated_at','deleted_at','closed_at','due_at');
        }

        public function getPresenter()
        {
            return new $this->presenter($this);
        }
    }
```

Presenter:

```php
    use \Carbon\Carbon;

    class TicketPresenter extends \Robbo\Presenter\Presenter
    {
        public function presentDueAt()
        {
            return $this->getDateString($this->object['due_at']);
        }

        public function presentClosedAt()
        {
            return $this->getDateString($this->object['closed_at']);
        }

        public function presentCreatedAt()
        {
            return $this->getDateString($this->object['created_at']);
        }

        public function presentUpdatedAt()
        {
            return $this->getDateString($this->object['updated_at']);
        }

        public function getDateString($carbonDate)
        {
            if (is_null($carbonDate)) {
                return 'n/a';
            }

            if (is_string($carbonDate)) {
                $carbonDate = new Carbon($carbonDate);
            }

            if ($carbonDate->timestamp <= 0) {
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
```

DataTables / Eloquent Queries <a name="datatable-eloquent">
---------------------------------------------------------------

(8/26) DataTables seems to fail when we use eloquent queries. Not sure what's up with that. This does not work:

```php
    public function scopeOpen($query)
    {
        return $query->where('closed_at', 0)
            ->orWhereNull('closed_at');
    }
```

This is equivalent, but works:

```php
    return $query->whereRaw('closed_at=0 or not closed_at is Null');
```



Automatic getDates <a name="getDates">
-------------------------------------------
I suspect that I will always want every date field in every table to be returned as a carbon object. The way to do that, currently, is with the getDates function. Any way to look at the fields, and automatically turn any date into a Carbon object?

