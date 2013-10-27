Some things I've found along the way
----------------------------------------

* It's a lot easier to work with support objects, like interfaces and service providers, when they're all in the same place. I really don't like having them spread out throughout the system...One standard spot to find out what my contracts look like?

* Multiple bindings can be incorporated in a single service provider. This cuts some overhead.

* I can make stubs for objects, and put them in place of the actual objects. eg,

```php
    $this->app->bind('Kalani\Support\Interfaces\PagesRepositoryInterface', function() {
        return new PagesRepositoryStub();
    });
```

* On a new project, create similar objects in batches (eg, views, schema, seeds, models, repositories, etc). It makes it a lot easier to build them.

* Put similar things together. Makes it lots easier to find them.

* Use actual logic, including actual business rules, even when seeding. Do things like this:

```php
    use Kalani\KWeb\Repositories\SlugRepository;

    class SlugsTableSeeder extends Seeder 
    {
        public function run()
        {
            $repo = new SlugRepository;

            DB::table('slugs')->delete();     // unless you have a deleteAll method in your repo

            $slugs = array(
                array(21, 'about-us'),
                array(52, 'guest-information'),    
            );

            foreach($slugs as $slug) {
                $repo->create(array(
                    'for_oid'   => $slug[0],
                    'slug'      => $slug[1],
                ));
            }
        }
    }
```

* Master techniques on small projects. Changes are expensive on big projects.

* As much as possible, test from the very beginning of the project. Write tests from outside, in (eg, first an acceptance test to show the desired results, then unit tests as we develop the ability to deliver those results).

* Make sure that your tests pass before you check a branch in to source control.

* Tests should encompass several different layers:

    * Acceptance - from an end-user perspective  (use db)
    * Functional - make sure that pieces fit together  (use db)
    * functional (phpunit) - make sure that routes work, and correct data called  (use db)
        * model - verify correct data is returned (use db)
        * routes - make sure routes are available (use db)
    * unit (phpunit) - make sure single unit works well in isolation
        * controllers - verify correct interface methods are called, views returned, etc. (mocks)
        * repositories - verify correct model methods are called  (use mocks)

* Functional tests are great for showing actual data, and learning how a system works

