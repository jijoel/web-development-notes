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

Turn on the global event log:

    SET GLOBAL general_log_file = '/var/log/mysql/mysql.log';
    set GLOBAL general_log='ON'

