Composer:

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



