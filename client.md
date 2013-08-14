Client
====================

Here are notes about client-side tools and techniques. Including

* [DataTables](#datatables)


DataTables <a name="datatables">
--------------------------------------

DataTables gives me a nice scrollable, sortable table on the client.

Initializing:

    $('#tableName').dataTable();

With parameters:

    $('#tableName').dataTable({        
        "sDom": 'trip',             // shows only the (t)able, p(r)ocessing, (i)nformation, (p)agination 
        "bProcessing": true,        // shows a processing message when working
        "sAjaxSource": '/ajax/tickets/data-table',            // sets the ajax source to this location
        "fnServerParams": function(aoData) {
            aoData.push({'foo':'bar'});
            // data.push({sourceData});
        },
    });

##### sDom parameter info:

Available settings are:

    l - Length changing
    f - Filtering input
    t - The table!
    i - Information
    p - Pagination
    r - pRocessing
    < and > - div elements
    <"class" and > - div with a class

> '<' gives '<div>'
> '<"class"' gives '<div class="class">'
> '>' gives '</div>'
>
> So this: "sDom": '<"top"i>rt<"bottom"flp><"clear">'
>
> turns into this:
>
>    <div class="top">
>        i
>    </div>
>    rt
>    <div class="bottom">
>        flp
>    </div>
>    <div class="clear"></div>




Highlighting the current row:

    #example tbody tr.even:hover, #example tbody tr.even td.highlighted {
    background-color: #ECFFB3;}

    #example tbody tr.odd:hover, #example tbody tr.odd td.highlighted {
        background-color: #E6FF99;
    }

    #example tr.even:hover {
        background-color: #ECFFB3;
    }

    #example tr.even:hover td.sorting_1 {
        background-color: #DDFF75;
    }

    #example tr.even:hover td.sorting_2 {
        background-color: #E7FF9E;
    }

    #example tr.even:hover td.sorting_3 {
        background-color: #E2FF89;
    }

    #example tr.odd:hover {
        background-color: #E6FF99;
    }

    #example tr.odd:hover td.sorting_1 {
        background-color: #D6FF5C;
    }

    #example tr.odd:hover td.sorting_2 {
        background-color: #E0FF84;
    }

    #example tr.odd:hover td.sorting_3 {
        background-color: #DBFF70;
    }

