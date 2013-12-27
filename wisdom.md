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



Best Practices  (Clean Code)
-------------------------------

* Always start with acceptance/functional test
* Go from outside, and work in.

* Do not start with model. Delay for as long as possible.
  This makes system more accepting of change.
* Start with view, degenerate scenario (outside-in)
  * simplest; lets you get system structure working
  * basic structure, routes, etc.
  * hard-code some html
  * route, controller, method, view (hard-code expected values)
* Scenario 2 is for variations
  * For early tests, create a struct/array with data...
* Introduce dynamism -- programming by wishful thinking
  (if this worked, it would be wonderful)
  * Modify the view for how you would like the data to be called
  * Build a dummy object in the controller (an array of them...)
* Move code from test -> view -> controller

* When testing, use a Given, When, Then structure
    Given: the initial state            (setup)
    When:  what happens to change it?   (method call, etc.)
    Then:  what should the result be?   (assertion(s))

* Complexity should be extracted from the test objects and into helper functions.

* Switch statements and if statements can cause problems

### Function Structure

* As few arguments to functions as possible, at most 3
* (if more than 3 variables are so cohesive they're needed together, why aren't they an object?)
* No boolean arguments ever. Write two functions, instead
  (one for the true case, one for the false case)
* Don't use output arguments at all

* Public functions at the top; private/protected below
* Important functions at the top, less important at the bottom

* Organize functions in the order they are called, and their heirarchy:
  * Main Public
  * Another Public
    * Main Public Sub1
      * Sub1 sub1 
        * Sub1 sub1 sub1
      * Sub1 sub2
        * Sub1 sub2 sub1
    * Main Public Sub2

* No backward references: No private/protected function below calls one above

* Switch statements are bad -- a missed opportunity to use polymorphism

* Command/Query Separation - used to manage side-effects
  * Command: Change state of system; returns nothing
  * Query:   Returns state of system or value of computation

  * Functions that return values should not change state, and vice versa
  * If a command fails, throw an exception

* Tell, don't ask
  * Do not ask an object for it's state, then make decisions about what it should do

* Write error handling code first

* Entity / Interactors don't know anything about outside environment.
* Entity: Business Rules or business objects
* Interactor: Talk to entities (Use Cases / eg, "AddEmployeeTransaction")
* Interactor would know about database... also about security/permissions?

* Single Responsibility: Family of functions that serve a particular actor
* Responsibilities are assigned to actors (a role that a user changes)
* Unit tests generally align with actors
