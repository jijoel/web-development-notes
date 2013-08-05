php commands
==============

    file_put_contents($file, $contents)   write $contents to $file
    file_get_contents($file)              read $file into a variable
    Insert end-of-line code:              echo PHP_EOL;

    uniqid              Generate a unique id string based on timestamp
    serialize           Generate a (storable) string from an object/array
    unserialize         Load (stored) string data into an object/array
    end(array)          Return value of last element in array
    compact('items')    shorthand for array('items' => $items);
    strip_tags($str)    strips all html tags from $str
    get_class           Returns the name of a class from an object
    
    var_export()        Like var_dump, but writes data in a way it can be captured. For instance:

        \Log::debug(var_export(DB::getQueryLog(), true));  // writes debug info to the log file



Static vs Non-static
----------------------

    Non-static:  $this->function(x);
    Static:      self::function(x);

In a static function, you can only call other static functions, unless you create an instance, like so: 

        $instance = new static;
        $instance->function(x);


Magic functions
-----------------
Magic functions run at (given) times. For instance, __call runs whenever a function can not be found, so you can do something else:

    public static function __call($name, $args)
    {
        $fn = strtolower($name);

        if (substr($fn, 0, 8) == 'gettable'){
            $table = $this->getTable();
            return $table[substr($fn, 8)];
        } elseif (substr($fn, 0, 8) == 'getfield') {
            $fields = $this->getFields();
            return $fields[substr($fn, 8)];
        }
        return strtolower($name);
    }

    
Namespaces
------------

Based off the folder structure.

    My project: package/src/Vendor/Package.php
        github: jijoel/Package/src/Vendor/Package.php

    namespace Vendor\Package

