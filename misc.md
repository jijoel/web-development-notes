Miscellaneous Topics
=======================



### Bash commands

Convert tabs to spaces for all files in a given directory tree. DO NOT run this on your project root folder. It will destroy your git index. Run in the app directory, it cleans things up nicely:

    find . ! -type d ! -name _tmp_ -exec sh -c 'expand -t 4 {} > _tmp_ && mv _tmp_ {}' \;

Find critical information about every namespace, class, function, and interface in a directory tree:

    for i in `find app/Kalani -type f`; do cat $i | grep -E 'namespace|class|function|interface' | grep -vE 'class=|\$class|this->app'; done




### MySQL Commands

Get a list of all tables and the number of rows in a database:

    select table_name, table_rows from information_schema.tables where table_schema = '<db>';

Get the name of the currently active database (from eg, USE db):

    select database();

Get information about the version of mysql:

    show variables like "%version%"

Turn on the global event log:

    SET GLOBAL general_log_file = '/var/log/mysql/mysql.log';
    set GLOBAL general_log='ON'

Show defined users:

    SELECT user, host FROM mysql.user;


Users and Permissions

    Permissions are granted to a combination of User and Host.

    CREATE USER 'username'@'host' IDENTIFIED BY 'password';
    GRANT [type of permission] ON [database name].[table name] TO '[username]'@'host';
    SHOW GRANTS FOR 'username'@'host';
    FLUSH PRIVILEGES;
    
    DROP USER 'username'@'host';
    REVOKE [type of permission] ON [database name].[table name] FROM '[username]'@'host';

    Grant permission types:
        ALL             all access to a designated database (or if no database is selected, across the system)
        CREATE          allows them to create new tables or databases
        DROP            allows them to them to delete tables or databases
        DELETE          allows them to delete rows from tables
        INSERT          allows them to insert rows into tables
        SELECT          allows them to use the Select command to read through databases
        UPDATE          allow them to update table rows
        GRANT OPTION    allows them to grant or remove other users' privileges

    'host' can be a domain name, ip address, etc.:

        'host.name'     name of machine
        '192.168.0.1'   ip address
        '192.168.0.%'   range of ip addresses
        '%'             global

