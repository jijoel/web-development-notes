Questions

What is the best practice to handle error processing with JSON?
It sounds like the best practice is to return it in the json string. Something like:
{"errors":["This field is required","something else"]}
Also:
"warnings":[]

  {
      "status": 404,
      "code": 40483,
      "message": "Oops! It looks like that file does not exist.",
      "developerMessage": "File resource for path /uploads/foobar.txt does not exist. 
          Please wait 10 minutes until the upload batch completes before checking again.",
      "moreInfo": "<link rel="moreinfo" href='http://www.mycompany.com/errors/40483' />"
  }
  
How do I actually implement that in laravel?

---
Is there some way to set up something in phpunit that will change the environment that laravel works in? I'd love to have dev and test environments, pulling data from different sources.

phpunit -c dev.xml        // pulls from in-memory database 
phpunit -c test.xml       // pulls from physical database

How can I set that up?


---
When deleting objects, my idea was to just set a 'deleted' flag on the base object. It has problems, though, with unique fields, searching, etc. (we have to keep looking for that deleted flag in every query...). Instead, could we combine the data into a text field, and write it to an archive table?


How can I set up different sets of validation rules, based on the context of a request?
For instance, in User, to set data, we want:
        'username' => 'required|unique:users|alpha_dash|min:4',
        'password' => 'required|between:4,12|confirmed',
        'password_confirmation' => 'required|between:4,12'

But to look at a field to use the data (eg, to log in), we'd want both of:
        'username' => 'required|alpha_dash|min:4',
        'password' => 'required|between:4,12'

And to modify one field at a time (with bootstrap-editable or another such tool), we'd want one of:
        'username' => 'unique:users|alpha_dash|min:4',
        'password' => 'between:4,12'

That's three separate usage cases for one table. Others will be similar.

