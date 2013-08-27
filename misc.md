Miscellaneous Topics
=======================



### Bash commands

Convert tabs to spaces for all files in a given directory tree. DO NOT run this on your project root folder. It will destroy your git index. Run in the app directory, it cleans things up nicely:

    find . ! -type d ! -name _tmp_ -exec sh -c 'expand -t 4 {} > _tmp_ && mv _tmp_ {}' \;

