Repositories
=======================

This is an design pattern that should make things more flexible and extendable...

The folder structure will be something like this:

    AppName
        Component
            SubComponent
                SubComponentInterface.php   (defines an interface for things this must provide)
                ArraySubComponent.php       (An array version of the component, to test controllers)
                EloquentSubComponent.php    (An eloquent version of the component)
                SubComponent.php            (eloquent model)
        ServiceProviders

For instance:

    app/
        controllers/
            TicketController.php
        Kalani/
            TicketTracker/
                Tickets/
                    TicketRepositoryInterface.php
                    EloquentTicketRepository.php
                    Ticket.php
                (other things used by the ticket tracker)/
            (other components)/
            ServiceProviders/
                TicketTrackerServiceProvider.php
        tests/
            controllers/
                TicketControllerTest.php
            Kalani/
                TicketTracker/
                    Tickets/
                        EloquentTicketRepositoryTest.php



Challenge: you need a flexible (swap in other repository implementations) way to display some kind of resource. Let's say, Posts. What does your folder structure look like?

#### Requirements:

* Repository implements an interface
* Must show at least one implementation of the interface (Eloquent version is fine)
* The interface is injected into your PostsController.
* Show where you register your IoC bindings
* Show folder structure. Where are interfaces/repositories stored?


#### Directory Structure:

Inside of app/lib:

    Appname
        Storage
            Post
                PostInterface.php
                EloquentPost.php
        StorageServiceProvider.php

#### Interface:

```php   
    <?php namespace Appname\Storage\Post;

    interface PostInterface {

        public function getPosts($limit=20, $offset=0);

    }
    ?>
```

#### Eloquent Implementation:

```php
    <?php namespace Appname\Storage\Post;

    class EloquentPost implements PostInterface {

        protected $ormPost;

        public function __construct()
        {
            // Instance of Eloquent Post model
            $this->ormPost = new \Post;
        }

        public function getPosts($limit=20, $offset=0)
        {
            return $this->ormPost->where('status', 'published')
                            ->orderBy('created_at', 'desc')
                            ->take($limit)
                            ->skip($offset)
                            ->get();
        }

    }
    ?>
```

#### IoC Binding:

The service provider will be added to the providers array in app/config/app.php 

```php
    <?php namespace Appname\Storage;

    use Illuminate\Support\ServiceProvider;

    class StorageServiceProvider extends ServiceProvider {

        /**
         * Register the binding
         *
         * @return void
         */
        public function register()
        {
            $this->app->bind('Appname\Storage\Post\PostInterface', 'Appname\Storage\Post\EloquentPost');
        }

    }
    ?>
```

#### Controller:

```php
    # app/controllers/PostController.php
    <?php

    class PostController {

        protected $post;

        public function __construct( Appname\Storage\Post\PostInterface $post )
        {
            $this->post = $post;
        }

        public function index()
        {
            $posts = $this->post->getPosts();

            return View::make('home', [ 'posts' => $posts ]);
        }

    }
    ?>
```

Each method in the repo will contain an eloquent (or query builder) call - what's actually inside that call is totally up to you. There will generally be something like the following for a resource type repo:

```php
    <?php
    class ContactRepository implements ContactRepositoryInterface
    {
        public function all()
        {
            return Contact::with('type')->orderBy('name')->get();
        }

        public function find($id)
        {
            return Contact::findOrFail($id);
        }

        public function store($data = null)
        {
            $data = $data ?: Input::all();
            $contact = new Contact($data);

            return $contact->save();;
        }

        public function update($id, $data = null)
        {
            $data = $data ?: Input::all();
            $contact = $this->find($id);
            $contact->fill($data);

            return $contact->save();
        }
    }
    ?>
```
