Workarounds and solutions for some issues I've seen
======================================================

* [Trying to get property of non-object for ajax query](#ajax-non-object)
* [phpUnit - no tests found in class "TestCase"](#phpunit-testcase)


Trying to get property of non-object for ajax query <a name="ajax-non-object">
-----------------------------------------------------------------------------------
I have a complex relationship, but it works beautifully when sent directly from the view. After an ajax query, though, it's sending bad data. My tables / relationships:

    Person (id, oid, :name, :comments)
    Comment (id, oid, for_oid)
    Object (oid)
    History (id, for_oid, timestamp, creator_oid)

    To get the original creator of a comment, we'll use:
    $this->object->history->first()->event_creator_id;

On a person record, we create a comment (via an ajax call). This cascades to create related Object and History records. When we return data to the browser, it does not find the correct Object for the comment. It will on a page refresh, though.

It's getting confused about $this. It's only returning oid and for_oid; the problem is that it's not returning the REAL oid. Instead, it's sending the $id data as the $oid.

    Comment::__set_state(array( 'table' => 'object_comments', 'guarded' => array ( ), 'primaryKey' => 'oid', 'timestamps' => false, 'connection' => NULL, 'perPage' => 15, 'incrementing' => true, 'attributes' => array ( 'oid' => 16, 'for_oid' => '724', 'comment' => 'foo', ), 'original' => array ( 'oid' => 16, 'for_oid' => '724', 'comment' => 'foo', ),

So, we do this:

    $found = $this;
    
    if ( ! $found->id ) {
        $found = Comment::findId($found->oid);
    }

    $creatorId = $found->object->history->first()->event_creator_id;



phpUnit - No tests found in class "TestCase" <a name="phpunit-testcase">
---------------------------------------------------------------------------

I can run phpunit, and get this as part of the result:

> There were 2 failures:
>
> 1) Warning
> No tests found in class "Illuminate\Foundation\Testing\TestCase".
>
> 2) Warning
> No tests found in class "TestCase".

I found it's because I had a case-difference in one of the test class names. (The file was DBTest.php; the class name was DbTest.php)

