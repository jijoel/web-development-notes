Laravel Security
========================


Private Data
-------------

I found a way to make laravel work with private data (eg, images, attachments, etc.). This will be used for documents that should only be seen by authorized users (with potentially different access for each authorized user). Basically, we have to make sure that we get the data through laravel, rather than being served up directly by apache. To do this:

Use this directory structure:

    /project/app/...     (all app code)
            /public/...  (all public assets)
            /private/... (all private assets)
    
We have to look for a file that is not available the file system (if the file exists, Apache will just return it, based on the standard .htaccess rules in Laravel). If the file does not exist, laravel will be called to return something.

In app/routes.php:

    Route::get('/assets/{path}', array('uses' => 'RoutingController@getAsset'))
        ->where('path', '.+');


The RoutingController looks like this:

    class RoutingController extends Controller
    {
        public function getAsset($path)
        {
            // Find out if the user has permission to use this asset;
            // abort with a 403 error code if they do not.
            // For instance, abort if the user is a guest and does not have a p3 element
            // if (Auth::guest() && strlen($p3)==0)
            //     App::abort(403);

            $asset = realpath($_SERVER["DOCUMENT_ROOT"] . '/../private' . $path);

            // Find out if the user has permission to use this asset;
            // abort with a 403 error code if they do not.
    
            $this->returnFile($asset);
        }
    
        private function returnFile($path, array $headers = array())
        {
            if (!is_file($path))
                App::abort(404);

            $finfo = finfo_open(FILEINFO_MIME_TYPE); 
            $mime = finfo_file($finfo, $path);

            // Prepare the headers
            $headers = array_merge(array(
                'Content-Description'       => 'File Transfer',
                'Content-Type'              => $mime,
                'Content-Transfer-Encoding' => 'binary',
                'Expires'                   => 0,
                'Cache-Control'             => 'must-revalidate, post-check=0, pre-check=0',
                'Pragma'                    => 'public',
                'Content-Length'            => filesize($path),
            ), $headers);

            $response = Response::make('', 200, $headers);

            // If there's a session we should save it now
            if (Config::get('session.driver') !== '')
            {
                Session::save();
            }

            // Below is from http://uk1.php.net/manual/en/function.fpassthru.php comments
            session_write_close();
            ob_end_clean();
            $response->sendHeaders();
            if ($file = fopen($path, 'rb')) {
                while(!feof($file) and (connection_status()==0)) {
                    print(fread($file, 1024*8));
                    flush();
                }
                fclose($file);
            }

            // Finish off, like Laravel would
            Event::fire('laravel.done', array($response));
            $response->foundation->finish();

            exit;
        }
    }

So, I'll get these results:

    /                                 (returns homepage)
    /assets/css/style.css             (returns the standard css file)
    /assets/attach/acro.jpg           (returns an error--from RoutingController)
    /assets/attach/2012/acro.jpg      (returns the acro.jpg file--allowed by RoutingController)
    /login                            (returns login page, lets me log in)
    /assets/attach/acro.jpg           (returns the acro.jpg file)
    /assets/attach/2012/acro.jpg      (returns file private/attach/2012/acro.jpg)



Guarded Attributes
----------------------
By default, Laravel will guard all attributes. This will prevent some things from being automatically filled from input. Set a $fillable array to choose attributes that you should be able to fill, or modify the $guarded array to select only some attributes. To write to a guarded attribute:

    public function store()
    {
        $validation = Validator::make(Input::all(), $this->rules->getRules());

        if ($validation->fails()) {
            return Redirect::back()->withInput()->withErrors($validation->errors());
        }

        $items = new $this->items;      // $this->items is the name of the model for this class

        $oid = newObjectId();           // however you get it
        $items->fill(Input::all());     // Fill in all unguarded attributes
        $items->oid = $oid;             // Fill in the (guarded) oid attribute

        if ( ! $items->save() ) {
            return Redirect::back()->withInput()->withErrors( $items->errors() );
        }

        // Create the history entry for this object
        History::store($oid);

        return Redirect::to(Session::get('referrer'));
    }
