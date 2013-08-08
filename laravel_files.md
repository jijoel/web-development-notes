Laravel File Handling
=============================

The Illuminate\Filesystem\Filesystem object (File facade) exposes these public functions:

    exists($path)                   Determine if a file exists.
    get($path)                      Get the contents of a file.
    getRemote($path)                Get the contents of a remote file.
    getRequire($path)               Get the returned value of a file.
    requireOnce($file)              Require the given file once.
    put($path, $contents)           Write the contents of a file.
    append($path, $data)            Append to a file.
    delete($path)                   Delete the file at a given path.
    move($path, $target)            Move a file to a new location.
    copy($path, $target)            Copy a file to a new location.
    extension($path)                Extract the file extension from a file path.
    type($path)                     Get the file type of a given file.
    size($path)                     Get the file size of a given file.
    lastModified($path)             Get the file's last modification time.
    isDirectory($directory)         Determine if the given path is a directory.
    isWritable($path)               Determine if the given path is writable.
    isFile($file)                   Determine if the given path is a file.
    glob($pattern, $flags = 0)      Find path names matching a given pattern.
    files($directory)               Get an array of all files in a directory.
    allFiles($directory)            Get all of the files from the given directory (recursive).
    directories($directory)         Get all of the directories within a given directory.
    cleanDirectory($directory)      Empty the specified directory of all files and folders.

    makeDirectory($path, $mode = 0777, $recursive = false)      Create a directory.
    copyDirectory($directory, $destination, $options = null)    Copy a directory from one location to another.
    deleteDirectory($directory, $preserve = false)              Recursively delete a directory.


File uploads
--------------

To upload a file to Laravel, this should go in the controller accepting the POST route: 

    $file = Input::file('file'); // your file upload input field in the form should be named 'file'

    $destinationPath = 'uploads/'.str_random(8);
    $filename = $file->getClientOriginalName();
    //$extension =$file->getClientOriginalExtension(); //if you need extension of the file
    $uploadSuccess = Input::file('file')->move($destinationPath, $filename);
     
    if( $uploadSuccess ) {
       return Response::json('success', 200); // or do a redirect with some message that file was uploaded
    } else {
       return Response::json('error', 400);
    }

This should be in the view:

    {{ Form::open(array('action' => 'UploadController@postImage','files'=>true)) }}

(the files=>true in the opener allows the uploading of forms to happen)

