MVC 
======

MVC is a software pattern that separates the representation of information
from the user's interactions with it.

    Model       - Classes which contain all of the business logic of the application
    View        - Classes which format and present data to the user
    Controller  - Classes which coordinate user actions and routing


http://www.bennadel.com/blog/2379-A-Better-Understanding-Of-MVC-Model-View-Controller-Thanks-To-Steven-Neiland.htm

### Controller 

The "C" in "MVC"

The Controller's job is to translate incoming requests into outgoing responses. In order to do this, the controller must take request data and pass it into the Service layer. The service layer then returns data that the Controller injects into a View for rendering. This view might be HTML for a standard web request; or, it might be something like JSON (JavaScript Object Notation) for a RESTful API request.

Red Flags: My Controller architecture might be going bad if:

The Controller makes too many requests to the Service layer.
The Controller makes a number of requests to the Service layer that don't return data.
The Controller makes requests to the Service layer without passing in arguments.

### View 

The "V" in "MVC"

The View's job is to translate data into a visual rendering for response to the Client (ie. web browser or other consumer). The data will be supplied primarily by the Controller; however, the View may also have a helper that can retrieve data that is associated with the rendering and not necessarily with the current request (ex. aside data, footer data).

Red Flags: My View architecture might be going bad if:

The View contains business logic.
The View contains session logic.

### Model 

The "M" in "MVC"

The Model's job is to represent the problem domain, maintain state, and provide methods for accessing and mutating the state of the application. The Model layer is typically broken down into several different layers:

Service layer - this layer provides cohesive, high-level logic for related parts of an application. This layer is invoked directly by the Controller and View helpers.

Data Access layer - (ex. Data Gateway, Data Access Object) this layer provides access to the persistence layer. This layer is only ever invoked by Service objects. Objects in the data access layer do not know about each other.

Value Objects layer - this layer provides simple, data-oriented representations of "leaf" nodes in your model hierarchy.

Red Flags: My Model architecture might be going bad if:

The Model contains session logic.
The Value Objects retain (ie. store) references to Service objects or Gateway objects.
