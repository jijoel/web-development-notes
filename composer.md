Composer
===============

Use composer to install additional components for the app.

Get composer at http://getcomposer.org/
It looks for a file called composer.json in your project root directory:

    {
        "require": {
                "laravel/framework": "4.0.*",
                "mockery/mockery": "dev-master"
        },
        "autoload": {
                "classmap": [
                        "app/commands",
                        "app/controllers",
                        "app/models",
                        "app/database/migrations",
                        "app/database/seeds",
                        "app/tests"
                ]
        },
        "minimum-stability": "dev"
    }


Basic functions:

    composer install                  // installs packages
    composer update                   // updates packages
    composer update --dev             // updates packages for the require-dev section
    composer dump-autoload            // dumps, and refreshes the autoload list

If it fails (due to a script), you can turn the scripts off:

    composer update --no-scripts

Composer will automatically load files from the autoloaded directories.
You can see what classes it found by checking:

    vendor/composer/autoload_classmap.php

If you want to get a git repository:

    composer install --prefer-source

Installing (or updating) one package only:

    composer update vendor/package

You can combine these, to update one package for source. Do an initial installation, then delete the vendor/package folder, and then:

    composer update --prefer-source vendor/package

Note: When using composer, we should upload the file composer.lock to our VCS repository. Having it in the repository assures you that each developer is using the same versions of all packages, and that the version in test is the same as the version in dev. On a production server, run composer install (never composer update).




Loading a package from a VCS repository (eg, github)
-------------------------------------------------------

We don't have to upload packages to packagist.org. They can be downloaded from a git repository, as follows:

http://getcomposer.org/doc/05-repositories.md#repository

    {
        "require": {
            "vendor/my-private-repo": "dev-master"
        },
        "repositories": [
            {
                "type": "vcs",
                "url":  "git@bitbucket.org:vendor/my-private-repo.git"  // for a private repo on bitbucket
            },
            {
                "type": "vcs",
                "url":  "https://github.com/igorw/monolog"              // for a repo on github
            }
        ]
    }




Composer Package Notes
===============================

There are a lot of packages I find useful. Many of these are listed in [a relative link](links.md). This section has specific notes about specific packages:

* [Carbon](#carbon)     Date/Time management


Carbon<a name="carbon">
--------------------------

Create a Carbon object from a mysql datetime value:

    $c = Carbon::createFromTimestamp(strtotime($model->date_time_field));

Output a formatted date string (Mon d, YYYY):

    $c->toFormattedDateString();        //  Aug 7, 2013
    $c->toDayDateTimeString();          //  Wed, Aug 7, 2013 10:09 PM
    $c->format($format);                //  Any format from http://www.php.net/manual/en/function.date.php