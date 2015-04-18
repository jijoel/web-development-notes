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

* Controller methods should be set up like this:

    Guarding (eg, basic security/validation)
    try {
        run method
        return result
    }
    except {
        return error
    }

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

Use case factory: make method (with use case name)
Request Builder:  build method (with request name and constructor arguments)

System design using SOLID principles

* First, figure out who the actors are (groups of people who might request changes)
* Single responsibility principle - one module per actor
* Goal of component structure is independent deployability
* Components should be large enough to justify cost of managing releases
* (they will be independently versioned, etc... think: package)
* When one class polymorphically uses another, the two classes should be in different components

* Each use case can be called by many different controllers

* Tests: Red, Green, Refactor 
* Arrange / Act / Assert / Annihilate (cleanup);  Given / When / Then
  * Arrange: Set up context / state / test fixture
  * Act:     Call function / perform action we want to test
  * Assert:  Check output of action; One logical assertion
  * Annihilate:  Undo everything done in Arrange; restore to base state
* As tests get more specific, code gets more generic
  * Make incremental generalizations to the code:
      constant --> variable --> array/loop
      if($i == 2)  -->  if($i == $n)  --> while($i==$n)
  * Always start with the degenerate/edge test cases (errors/boundaries/etc)
    * Null input (return valid output -- empty array, empty string, etc)

  * Then, go for the guts of the algorithm
  * Gradually increase complexity (you get stuck if you bite off too much at once) 
    None of the code we write to pass early tests is wasted code; it’s just code that’s incomplete, not properly placed, or not general enough

* Name tests after When/Then:
  * eg, testWhenPayrollIsRunPaymasterHoldsCheckForHourlyRateTimesHoursWorked
  * See CleanCode-E21-Test-Design.mp4
  * Setup functions named after Given (eg, givenStandardHourlyRate)
* Tests should be named like well written specifications
* Tests express behavior, not API.
* Try to test algorithms, not results (spot-check results)

* Test Doubles:
    * Dummy     Implements interface; all methods return nothing  (Null Object)
                Neither test nor implementation use values from object
    * Stub      Dummy, but all methods return special fixed values
                (can be used to drive code through tested pathway (ifs, etc.))
    * Spy       Stub, but remembers how it was called (reports to test)
                (which functions called, how many times, what arguments, etc.)
    * Mock      Spy, but knows what should happen

    * Fake      Simulated class; respond differently to different inputs
                (can look like actual class; can be very complex)
                If you use fakes, make sure to test the fakes, also
                (avoid fakes, if can)

* Stubs and spies are sufficient for unit tests
* Sometimes fakes are useful for integration tests

* Test-specific subclass:
  * Make a subclass of the class to be tested
  * Replace protected functions needed by the test
  * Modify / eliminate behavior in protected functions
* Self shunt
  * The test becomes a mock (implement an interface, injects itself into class)
  * You won't need a separate mock class...
* Humble objects
  * Isolate testable code from stuff that is hard to test
  * Move interesting algorithm to a class that can be tested
  * The Humble Object pattern can be used to test GUIs

* When testing GUIs
  * Test GUIs with your eyes, coupled with a fake back-end process
  * GUI code (eg, view) shows UI ONLY
    * It gets anything that might change from a connector
    * Also, any static information (strings, etc.)
  * The connector does not have any logic; it calls functions from an interface
  * All logic happens in classes that implement the interface

* App should consist of:
  * Actions - single method use-case objects
  * Things / Entities - simple objects, few methods


From Build APIs You Won't Hate:

Use UUID/GUID instead of AUTOINCREMENT  (can generate with uniqueid())

{
  "data": [
    { 
      "id": "uuid", 
      "name": "Object Name", 
      "links": [
        { "rel": "link.name", "url": "/path/to/link" }
      ],
      ...
    }
  ],
  "pagination": [
    { "total": total, ... }
  ],
  "errors": [
    {"code": "error_code", "title": "short summary", "detail": "human-readable explanation"}
  ]
}

Send good status codes:

  2xx  Success
  200  Everything is OK
  201  Object created OK
  202  Accepted but being processed async

  4xx  Request / client errors (fix and re-send)
  400  Bad Request / Invalid syntax
  401  Unauthorized  (no current user)
  403  Forbidden  (current user can not access requested data)
  404  Invalid route / not found
  405  Method not allowed
  410  Data has been deleted

  5xx  Server errors
  500  Something has gone wrong on server
  503  API not available right now

Transform data to good json before returning to the user. See fractal.thephpleague.com.


12 Factor App (eg, "best practice" design recommendations):
http://12factor.net/
http://slashnode.com/the-12-factor-php-app-part-1/

 1. Codebase - One codebase tracked in revision control, many deploys (eg, git)
 2. Dependencies - Explicitly declare and isolate dependencies (eg, composer)
 3. Config - Store config in the environment (.env files)
 4. Backing Services - Treat backing services as attached resources
    (eg, flysystem; connect to local/remote files/databases/etc the same way)
 5. Build, release, run - Strictly separate build and run stages  (dev vs prod)
 6. Processes - Execute the app as one or more stateless processes
    (not monolithic; handle requests by different, unrelated, non-communicating processes)
 7. Port binding - Export services via port binding  (eg, ReactPhp)
 8. Concurrency - Scale out via the process model (web / worker processes)
 9. Disposability - Maximize robustness with fast startup and graceful shutdown
10. Dev/prod parity - Keep development, staging, and production as similar as possible
11. Logs - Treat logs as event streams
12. Admin processes - Run admin/management tasks as one-off processes


The Pragmatic Programmer Checklist
----------------------------------
Architectural Questions

  * Are responsibilities well defined?
  * Are the collaborations well defined?
  * Is coupling minimized?
  * Can you identify potential duplication?
  * Are interface definitions and constraints acceptable?
  * Can modules access needed data—when needed?

The debugging checklists
 
  * Is the problem being reported a direct result of the underlying bug, or merely a symptom?
  * Is the bug really in the compiler? Is it in the OS? Or is it in your code?
  * If you explained this problem in detail to a coworker, what would you say?
  * If the suspect code passes its unit tests, are the tests complete enough? What happens if you run the unit test with this data?
  * Do the conditions that caused this bug exist anywhere else in the system?

How to Program Deliberately

  * Stay aware of what you're doing.
  * Don't code blindfolded.
  * Proceed from a plan.
  * Rely only on reliable things.
  * Document your assumptions.
  * Test assumptions as well as code.
  * Prioritize your effort.
  * Don't be a slave to history.

Cutting the Gordian Knot
When solving impossible problems, ask yourself:

  * Is there an easier way?
  * Am I solving the right problem?
  * Why is this a problem?
  * What makes it hard?
  * Do I have to do it this way?
  * Does it have to be done at all?

Law of Demeter for Functions

  * An object's method should call only methods belonging to: Itself
  * Any parameters passed in
  * Objects it creates
  * Component objects

How to maintain orthogonality

  * Design independent, well defined components
  * Keep your code decoupled
  * Avoid global data
  * Refactor similar functions

Things to prototype

  * Architecture
  * New functionality in an existing system
  * Structure or contents of external data
  * Third-party tools or components
  * Performance issues
  * User interface design

Aspects of Testing

  * Unit testing
  * Integration testing
  * Validation and verification
  * Resource exhaustion, errors, and recovery
  * Performance testing
  * Usability testing
  * Testing the tests themselves

When to Refactor ?

  * You discover a violation of the DRY principle.
  * You find things that could be more orthogonal.
  * Your knowledge improves.
  * The requirements evolve.
  * You need to improve performance.

The Wisdom Acrostic ( Customer specific)

  * Why do you want to learn them?
  * What is their interest in what you have got to say?
  * How sophisticated are they?
  * How much details do they want?
  * Whom do you want to own the information?
  * How can you motivate them to listen to you?



Get Better
------------
http://www.mdswanson.com/blog/2011/10/24/get-better.html

> ... the sooner you care, the better you'll make. The better you'll do. And the better you'll live.
> Merlin Mann

Take 5 minutes to:

  * Read the most interesting blog post in your RSS reader
  * Look through the code you wrote today and find a place to improve
  * Write down any problems you encountered today
  * Learn a new keyboard shortcut for your IDE, source control tool, or shell
  * Ask a co-worker if they've read anything interesting lately

Take 15 minutes to:

  * Refactor a piece of code you wrote this week
  * Find code that's missing tests and add one
  * Update your team/company wiki
  * Read a few of the top stories on news.yc or /r/programming
  * Read a chapter in a technical book
  * Write a thoughtful comment on a blog post

Take 30 minutes to:

  * Watch a talk from a conference that interests you
  * Write a blog post about a bug you encountered and how you fixed it
  * Write a blog post about something you've been working on or learning about
  * Listen to a podcast - here are some I like: Podcast Roundup
  * Do a kata or a problem on Project Euler
  * Attend a brownbag or lunch-and-learn

Take an hour a week to:

  * Help an open source library that you use - patchs, documentation, bugs
  * Work on a side-project on your own
  * Watch a screencast about something new
  * Try to answer some questions on StackOverflow
  * Do prep work to host a brownbag or meetup talk
  * Take an online class: Stanford courses on iPhone, Machine Learning, AI

Take 2 hours a month to:

  * Go to a local dev meetup
  * Take someone you look up to out to lunch and pick their brain
  * Do self reflection and update your position on The Long Road
  * Plan out how to Get Better over the next month;

Take a weekend a year to:

  * Go to a conference
  * Attend a Startup Weekend or Hackathon
  * Use your craft to help others
  
Take one minute a day - just 60 seconds - to stop and ask yourself this question:

Did I Get Better today?

If you aren't happy with your answer, do something about it.

