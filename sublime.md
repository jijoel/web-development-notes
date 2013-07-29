SublimeText2 Shortcuts
------------------------

    Ctrl+Shift+Backspace  Delete to beginning of line
    Ctrl+Backspace        Delete current word 
    Ctrl+L                Select current line (multiple times for multiple lines)
    Ctrl+D                Select current word/next instances (sets up multiple cursors)
    Alt+F3                Select all similar instances in document (multiple cursors)
    Ctrl+Shift+P          Command Palette
    Ctrl+P                Go to anything  (just type in the identifier); Files/folders
    Ctrl+P, @             Go to method (function)
    Ctrl+R                (does exactly the same thing as Ctrl+P,@)
    Ctrl+P, :<#>          Go to line number in file
    Ctrl+Alt+N            New file/folder (with AdvancedNewFile)
    Ctrl+K, Ctrl+B        Show/Hide sidebar
    Ctrl+Shift+D          Duplicate (selected text or line)
    Ctrl+F                Find
    Ctrl+I                Find next occurrence of a given word/string
    Ctrl+F, Alt+R         toggle regular expression searching 
    Ctrl+J                join lines
    Ctrl+E                Expand (Emmet string)
    Ctrl+Tab              switch back and forth between last used windows

Multiple panes
--------------------

    Alt+Shift+1           show all windows in 1 pane
    Alt+Shift+2           2 panes
    Alt+Shift+5           grid (2 above, 2 below)
    Ctrl+Shift+2          move current file to pane 2
    Ctrl+1                move cursor to pane 1
    

SublimeText2 Installed Packages
---------------------------------

    AdvancedNewFile             Type in new path/file to create and open instantly
    DocBlockr                   Create template for doc block from function/class definition 
    Emmet                       Quickly write html code, based on css descriptors. Love it!
    FindKeyConflicts            Find conflicts for quick keys
    Package Control             Easily find and install packages
    SideBarEnhancements         Full commands when right-clicking on sidebar
    SublimeLinter               Highlight lines with errors in the code after saving;
    SublimeTODO                 Create an index for all of your TODO, FIXME, and similar comments


Global Key Map
--------------

I like these key bindings:

    [
        { "keys": ["ctrl+shift+;"], "command": "run_macro_file", "args": {
                "file": "Packages/User/EOL_Semicolon.sublime-macro"
            } 
        },
        { "keys": ["ctrl+alt+n"], "command": "advanced_new_file"},
        { "keys": ["ctrl+shift+a"], "command": "select_all" },
        { "keys": ["ctrl+a"], "command": "move_to", "args": {"to": "bol", "extend": false} },
        { "keys": ["ctrl+e"], "command": "move_to", "args": {"to": "eol", "extend": false} }
    ]


Global Settings
------------------

    {
        "color_scheme": "Packages/Color Scheme - Default/Monokai.tmTheme",
        "font_size": 16,
        "ignored_packages":
        [
            "Vintage"
        ],
        "tab_size": 4,
        "todo":
        {
            "folder_exclude_patterns":
            [
                "vendor",
                "bootstrap"
            ]
        },
        "translate_tabs_to_spaces": true
    }


Custom Macros
---------------------

I like these macros:

    Packages/User/EOL_Semicolon.sublime-macro
    [ { "args": { "to": "eol" },        "command": "move_to" },
      { "args": { "characters": ";" },  "command": "insert"  }]



Emmet Configuration
----------------------

See 

    http://docs.emmet.io/customization/preferences/
    http://docs.emmet.io/filters/

In the user configuration for Emmet, enter this:

    {
        "preferences": {
            "filter.commentAfter": " <!-- <%= attr(\"id\", \"#\") %> <%= attr(\"class\", \".\") %> -->"
        }
    }

This will enable you to enter comments after tags, like so:

    div#myID|c

The |c tells it to write a comment for the given element, and the configuration sets the format of the comment.

