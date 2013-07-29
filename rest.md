REST  ----------------------------------------------------------------------

REST is a web service design model meant to make applications usable, well-designed, 
and easy to integrate. It does things like standardizing the naming conventions of 
components. Its goals include things like:

* Scalability of component interactions
* Generality of interfaces
* Independent deployment of components
* Intermediary components to reduce latency, enforce security and encapsulate legacy systems
* Self-similarity, and easy to learn

Per object, we only need 2 base URLs:

    plural (collection)   /dogs
    singular              /dogs/bo  (or /dogs/1)

    Resource    GET         PUT          POST               DELETE
    /dogs       list dogs   <error>      create a new dog   delete all dogs
    /dogs/bo    show bo     update bo    <error>            delete this dog (bo)

Verbs in URL are bad
Nouns are good; use plural nouns for url names
WHERE statements would be behind the question mark:

    /dogs?color=red&state=running&loc=park
    (as opposed to getRedDogsRunningAtPark)

So, where a legacy system might have functions like this:

    getUsers()
    getNewUsersSince(date SinceDate)
    savePurchaseOrder(string CustomerID, string PurchaseOrderID)

A RESTful system might have:

    /users
    /users?date>=sinceDate
    /purchaseOrder  (with POST values for the data)

Pagination:

    /users?start=x&count=y  (or offset/limit, or page/rpp)

Specific fields only:

    /users?:(id,name,etc)

or

    /users?fields=id,name,color,picture

Orders/commands/events:

    PUT /dogs/bo?command=bark
    GET /dogs/bo?:(state)
    return  {"state": "barking"}

Types:

    /users.json  (returns it as json)
    /users.xml   (returns it as xml)
    GET /dogs/bo.json?:(state)

It's better to use the Accept and Content-Type headers on the client.

    A mobile request wanting JSON back would look like:

        Accept: text/json GET /eshop/sales/topItems/monday

    A web browser request which wants pdf would send

        Accept: application/pdf GET /eshop/sales/topItems/monday
    
Inheritance:

    GET /dogs/bo
    GET /animals/bo
    (return the same result)

Laravel function mapping to REST:

    Function    REST
    index       GET - list all items
    create      GET - show form for creating new item
    store       POST - write a new item
    show        GET - Show form to display one item (eg site/photos/1)
    edit        GET - Show form for editing one item (eg site/photos/1/edit)
    update      PUT - write changes to existing item
    destroy     DESTROY - delete an item

Useful response codes for RESTful apps:

    200 OK                 - This should be used only for success and nothing else. Period.
    201 Created            - This should be used as a response to a POST request when a 
                             resource is created. The location header should contain 
                             the URI to the newly created resource
    202 Accepted           - This can be used for async processing, 
                             with a link to the status/next step being sent by the server.
    400 Bad Request        - This can be used for input validation errors.
    401 Unauthorized       - User with given credentials not allowed to do requested task
    403 Forbidden          - Return this in case of an authentication error 
                             instead of returning a 200
    404 Not Found          - Resource was not found
    405 Method Not Allowed - If there is a read only resource and a client makes 
                             a non GET request, this should be the status code 
                             returned by the server with the valid methods included 
                             as part of the Allow header
    412 Precondition       - This can be used to indicate that a logical precondition 
                             check has failed
    500 Internal Server Error

Others:

    304 Not Modified
    409 Conflict
    410 Gone


Here's an idea about best-practice error handling, from
http://www.stormpath.com/blog/spring-mvc-rest-exception-handling-best-practices-part-1

    {
        "status": 404,
        "code": 40483,
        "message": "Oops! It looks like that file does not exist.",
        "developerMessage": "File resource for path /uploads/foobar.txt does not exist. 
          Please wait 10 minutes until the upload batch completes before checking again.",
        "moreInfo": "<link rel="moreinfo" href='http://www.mycompany.com/errors/40483' />"
    }

Note, on the moreinfo entry, instead of trying to construct a uri, the client can look at the respose; if the tag is there, it can use the reference directly.

    <link rel="moreInfo" href="/estore/catalog/books/id/12345"/>
    <link rel="reviews" href="http:/reviewStore/estore/reviews/id/9912345&customer=888"/>
    <link rel="rating" href="/estore/books/id/12345/rating?customer=888"/>

http://satishgopal.wordpress.com/2012/03/26/building-restful-services-part-2/

In an order, we might get this:

    <order>
        <cart>
            <items>
                <item code="abc" qty="1">
                <item code="def" qty="1">
            </items>
            <link rel="update" href="/estore/order/178567/cart"/>
        </cart>
        <link rel="saveForLater" href="/estore/customer/1356/archive/order/178567"/>
        <link rel="cancel" href="/estore/order/178567"/>
        <link rel="address" href="/estore/order/178567/address"
            type="application/vnd.estore_address+xml"/>
    </order>

The important point here is that the client doesn’t have to figure out from an external document as to what are the possible next states for the order. The possible next states are all captured with hyperlinks. Again, this makes for really loose coupling and provides the service a lot of flexibility in evolving business rules around order processing without breaking existing clients.

The set of possible next steps are provided by the service at any given point using hyperlinks. This is HATEOAS (Hypermedia As The Engine Of Application State) 

---
Using laravel resources, we get these routes:

    | GET /todos              | todos.index   | TodosController@index   |
    | GET /todos/create       | todos.create  | TodosController@create  |
    | POST /todos             | todos.store   | TodosController@store   |
    | GET /todos/{todos}      | todos.show    | TodosController@show    |
    | GET /todos/{todos}/edit | todos.edit    | TodosController@edit    |
    | PUT /todos/{todos}      | todos.update  | TodosController@update  |
    | PATCH /todos/{todos}    |               | TodosController@update  |
    | DELETE /todos/{todos}   | todos.destroy | TodosController@destroy |

