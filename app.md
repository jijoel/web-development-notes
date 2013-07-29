Application Variables
========================

These are all of the variables listed in the application object, as found using:

      var_dump( App::getFacadeRoot() );

      Class: Illuminate\Foundation\Application

      protected $booted
      $bootingCallbacks
      protected $bootedCallbacks 
      protected $shutdownCallbacks


protected $serviceProviders
------------------------------

      class Illuminate\Exception\ExceptionServiceProvider
      class Illuminate\Routing\RoutingServiceProvider
      class Illuminate\Events\EventServiceProvider
      class Illuminate\Foundation\Providers\ArtisanServiceProvider
      class Illuminate\Auth\AuthServiceProvider
      class Illuminate\Cache\CacheServiceProvider
      class Illuminate\Foundation\Providers\CommandCreatorServiceProvider
      class Illuminate\Session\CommandsServiceProvider
      class Illuminate\Foundation\Providers\ComposerServiceProvider
      class Illuminate\Routing\ControllerServiceProvider
      class Illuminate\Cookie\CookieServiceProvider
      class Illuminate\Database\DatabaseServiceProvider
      class Illuminate\Encryption\EncryptionServiceProvider
      class Illuminate\Filesystem\FilesystemServiceProvider
      class Illuminate\Hashing\HashServiceProvider
      class Illuminate\Html\HtmlServiceProvider
      class Illuminate\Foundation\Providers\KeyGeneratorServiceProvider
      class Illuminate\Log\LogServiceProvider
      class Illuminate\Mail\MailServiceProvider
      class Illuminate\Database\MigrationServiceProvider
      class Illuminate\Pagination\PaginationServiceProvider
      class Illuminate\Foundation\Providers\PublisherServiceProvider
      class Illuminate\Queue\QueueServiceProvider
      class Illuminate\Redis\RedisServiceProvider
      class Illuminate\Auth\Reminders\ReminderServiceProvider
      class Illuminate\Database\SeedServiceProvider
      class Illuminate\Foundation\Providers\ServerServiceProvider
      class Illuminate\Session\SessionServiceProvider
      class Illuminate\Foundation\Providers\TinkerServiceProvider
      class Illuminate\Translation\TranslationServiceProvider
      class Illuminate\Validation\ValidationServiceProvider
      class Illuminate\View\ViewServiceProvider
      class Illuminate\Workbench\WorkbenchServiceProvider


protected $loadedProviders
------------------------------

      'Illuminate\Exception\ExceptionServiceProvider'
      'Illuminate\Routing\RoutingServiceProvider'
      'Illuminate\Events\EventServiceProvider'
      'Illuminate\Foundation\Providers\ArtisanServiceProvider'
      'Illuminate\Auth\AuthServiceProvider'
      'Illuminate\Cache\CacheServiceProvider'
      'Illuminate\Foundation\Providers\CommandCreatorServiceProvider'
      'Illuminate\Session\CommandsServiceProvider'
      'Illuminate\Foundation\Providers\ComposerServiceProvider'
      'Illuminate\Routing\ControllerServiceProvider'
      'Illuminate\Cookie\CookieServiceProvider'
      'Illuminate\Database\DatabaseServiceProvider'
      'Illuminate\Encryption\EncryptionServiceProvider'
      'Illuminate\Filesystem\FilesystemServiceProvider'
      'Illuminate\Hashing\HashServiceProvider'
      'Illuminate\Html\HtmlServiceProvider'
      'Illuminate\Foundation\Providers\KeyGeneratorServiceProvider'
      'Illuminate\Log\LogServiceProvider'
      'Illuminate\Mail\MailServiceProvider'
      'Illuminate\Database\MigrationServiceProvider'
      'Illuminate\Pagination\PaginationServiceProvider'
      'Illuminate\Foundation\Providers\PublisherServiceProvider'
      'Illuminate\Queue\QueueServiceProvider'
      'Illuminate\Redis\RedisServiceProvider'
      'Illuminate\Auth\Reminders\ReminderServiceProvider'
      'Illuminate\Database\SeedServiceProvider'
      'Illuminate\Foundation\Providers\ServerServiceProvider'
      'Illuminate\Session\SessionServiceProvider'
      'Illuminate\Foundation\Providers\TinkerServiceProvider'
      'Illuminate\Translation\TranslationServiceProvider'
      'Illuminate\Validation\ValidationServiceProvider'
      'Illuminate\View\ViewServiceProvider'
      'Illuminate\Workbench\WorkbenchServiceProvider'


protected $deferredServices
-----------------------------

      'artisan' => "Illuminate\\Foundation\\Providers\\ArtisanServiceProvider"
      'auth' => "Illuminate\\Auth\\AuthServiceProvider"
      'cache' => "Illuminate\\Cache\\CacheServiceProvider"
      'memcached.connector' => "Illuminate\\Cache\\CacheServiceProvider"
      'command.command.make' => "Illuminate\\Foundation\\Providers\\CommandCreatorServiceProvider"
      'command.session.database' => "Illuminate\\Session\\CommandsServiceProvider"
      'composer' => "Illuminate\\Foundation\\Providers\\ComposerServiceProvider"
      'filter.parser' => "Illuminate\\Routing\\ControllerServiceProvider"
      'command.controller.make' => "Illuminate\\Routing\\ControllerServiceProvider"
      'hash' => "Illuminate\\Hashing\\HashServiceProvider"
      'form' => "Illuminate\\Html\\HtmlServiceProvider"
      'command.key.generate' => "Illuminate\\Foundation\\Providers\\KeyGeneratorServiceProvider"
      'log' => "Illuminate\\Log\\LogServiceProvider"
      'mailer' => "Illuminate\\Mail\\MailServiceProvider"
      'swift.mailer' => "Illuminate\\Mail\\MailServiceProvider"
      'swift.transport' => "Illuminate\\Mail\\MailServiceProvider"
      'migrator' => "Illuminate\\Database\\MigrationServiceProvider"
      'migration.repository' => "Illuminate\\Database\\MigrationServiceProvider"
      'command.migrate' => "Illuminate\\Database\\MigrationServiceProvider"
      'command.migrate.rollback' => "Illuminate\\Database\\MigrationServiceProvider"
      'command.migrate.reset' => "Illuminate\\Database\\MigrationServiceProvider"
      'command.migrate.refresh' => "Illuminate\\Database\\MigrationServiceProvider"
      'command.migrate.install' => "Illuminate\\Database\\MigrationServiceProvider"
      'migration.creator' => "Illuminate\\Database\\MigrationServiceProvider"
      'command.migrate.make' => "Illuminate\\Database\\MigrationServiceProvider"
      'paginator' => "Illuminate\\Pagination\\PaginationServiceProvider"
      'asset.publisher' => "Illuminate\\Foundation\\Providers\\PublisherServiceProvider"
      'command.asset.publish' => "Illuminate\\Foundation\\Providers\\PublisherServiceProvider"
      'config.publisher' => "Illuminate\\Foundation\\Providers\\PublisherServiceProvider"
      'command.config.publish' => "Illuminate\\Foundation\\Providers\\PublisherServiceProvider"
      'queue' => "Illuminate\\Queue\\QueueServiceProvider"
      'queue.worker' => "Illuminate\\Queue\\QueueServiceProvider"
      'queue.listener' => "Illuminate\\Queue\\QueueServiceProvider"
      'command.queue.work' => "Illuminate\\Queue\\QueueServiceProvider"
      'command.queue.listen' => "Illuminate\\Queue\\QueueServiceProvider"
      'redis' => "Illuminate\\Redis\\RedisServiceProvider"
      'auth.reminder' => "Illuminate\\Auth\\Reminders\\ReminderServiceProvider"
      'auth.reminder.repository' => "Illuminate\\Auth\\Reminders\\ReminderServiceProvider"
      'command.auth.reminders' => "Illuminate\\Auth\\Reminders\\ReminderServiceProvider"
      'seeder' => "Illuminate\\Database\\SeedServiceProvider"
      'command.seed' => "Illuminate\\Database\\SeedServiceProvider"
      'command.serve' => "Illuminate\\Foundation\\Providers\\ServerServiceProvider"
      'command.tinker' => "Illuminate\\Foundation\\Providers\\TinkerServiceProvider"
      'translator' => "Illuminate\\Translation\\TranslationServiceProvider"
      'translation.loader' => "Illuminate\\Translation\\TranslationServiceProvider"
      'validator' => "Illuminate\\Validation\\ValidationServiceProvider"
      'validation.presence' => "Illuminate\\Validation\\ValidationServiceProvider"


$bindings 
-------------

      request
      kernel.error
      kernel.exception
      exception
      exception.function
      router
      url
      redirect
      events
      env
      app
      config.loader
      artisan
      auth
      cache
      memcached.connector
      command.command.make
      command.session.database
      composer
      filter.parser
      command.controller.make
      cookie
      db.factory
      db
      encrypter
      files
      hash
      form
      command.key.generate
      swift.transport
      swift.mailer
      mailer
      migration.repository
      migrator
      command.migrate
      command.migrate.rollback
      command.migrate.reset
      command.migrate.refresh
      command.migrate.install
      migration.creator
      command.migrate.make
      paginator
      command.asset.publish
      asset.publisher
      command.config.publish
      config.publisher
      queue
      command.queue.work
      queue.worker
      command.queue.listen
      queue.listener
      redis
      auth.reminder
      auth.reminder.repository
      command.auth.reminders
      command.seed
      seeder
      command.serve
      session
      command.tinker
      translation.loader
      translator
      validation.presence
      validator
      view.engine.resolver
      view.finder
      view
      package.creator
      command.workbench
      ItemRepositoryInterface
      VendorRepositoryInterface


protected $instances
-----------------------

      'path' => "C:\\wamp\\www\\shopping\\api\\bootstrap/../app"
      'path.base' => "C:\\wamp\\www\\shopping\\api\\bootstrap/.."
      'path.public' => "C:\\wamp\\www\\shopping\\api\\bootstrap/../public"
      'config.loader' => Illuminate\Config\FileLoader {
            protected $files => class Illuminate\Filesystem\Filesystem
            protected $defaultPath => "C:\\wamp\\www\\shopping\\api\\bootstrap/../app/config"
            protected $hints => {}
            protected $exists => {}
      'config' => class Illuminate\Config\Repository {
            protected $loader => class Illuminate\Config\FileLoader 
            protected $environment => "production"
            protected $items => array(5) 
            protected $packages => array(0) 
            protected $afterLoad => array(0) 
            protected $parsed => array(9) 
      'log' => class Illuminate\Log\Writer
            protected $monolog => class Monolog\Logger
            protected $levels => array(8)
            protected $dispatcher => class Illuminate\Events\Dispatcher
      'artisan' => class Illuminate\Console\Application
            protected $exceptionHandler => class Illuminate\Exception\Handler
            protected $laravel =>
            private $commands => array(23)
            private $wantHelps => bool(false)
            private $runningCommand => class TestCommand
            private $name => "Laravel Framework"
            private $version => "4.0.0"
            private $catchExceptions => bool(true)
            private $autoExit => bool(false)
            private $definition => class Symfony\Component\Console\Input\InputDefinition
            private $helperSet => class Symfony\Component\Console\Helper\HelperSet

      protected $aliases => array(0)
      protected $resolvingCallbacks => array(0)

