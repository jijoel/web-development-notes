Client
====================

Here are notes about client-side tools and techniques. Including

* [DataTables](#datatables)
* [JQuery-UI](#jquery-ui)


DataTables <a name="datatables">
--------------------------------------

DataTables gives me a nice scrollable, sortable table on the client.
http://datatables.net/


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

#### sDom parameter info:

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


#### Setting a height, and scrolling for items beyond it

```html
    <div style="height: 150px">
    <table class="grid">
            <thead>
            <tr>
                <th>id</th><th>summary</th><th>description</th><th>priority</th><th>due</th><th>closed</th><th>created</th><th>updated</th>
            </tr>
        </thead>
    </table>
    </div>
```

```js
    <script type="text/javascript">
    $(function () {
        var $table = $("table.grid");

        $table.dataTable({
            ... standard stuff 
            "sDom": 'trip', 
            "bJQueryUI": true,
            "bServerSide": true,
            "sAjaxSource": '/tickets/ajax',
            "bProcessing": true,
            "bDeferRender": true,
            "aaSorting": [[1, "asc"]],
            "sPaginationType": "full_numbers",
            "aoColumns": [
                { "sName": "ID", "sWidth": "2em"},
                { "sName": "Summary", "sWidth": "10em", },
                { "sName": "Description", },
                { "sName": "Priority", "sWidth": "1em" },
                { "sName": "due_at", "sWidth": "14em" },
                { "sName": "closed_at", "sWidth": "14em" },
                { "sName": "created_at", "sWidth": "14em" },
                { "sName": "deleted_at", "sWidth": "14em" },
            ],
            "sScrollY": "0px",

            "fnDrawCallback": function() {
                var $dataTable = $table.dataTable();
                $dataTable.fnAdjustColumnSizing(false);

                // TableTools
                if (typeof(TableTools) != "undefined") {
                    var tableTools = TableTools.fnGetInstance(table);
                    if (tableTools != null && tableTools.fnResizeRequired()) {
                        tableTools.fnResizeButtons();
                    }
                }
                //
                var $dataTableWrapper = $table.closest(".dataTables_wrapper");
                var panelHeight = $dataTableWrapper.parent().height();

                var toolbarHeights = 0;
                $dataTableWrapper.find(".fg-toolbar").each(function(i, obj) {
                    toolbarHeights = toolbarHeights + $(obj).height();
                });

                var scrollHeadHeight = $dataTableWrapper.find(".dataTables_scrollHead").height();
                var height = panelHeight - toolbarHeights - scrollHeadHeight;
                $dataTableWrapper.find(".dataTables_scrollBody").height(height - 24);

                $dataTable._fnScrollDraw();
            },

        });
    });
    </script>
```


#### Highlighting the current row

```css
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
```css


#### Redrawing the table

The table can be redrawn with the fnDraw() method. This happens asynchronously. If you'd like to do something after the table load (eg, getting an id of the first record, etc.), you can use jQuery's 'one' function, like this:

```js
    $('#ticket-type-selector').on( "change", function(){
        $table.fnDraw();
        $('#ticketList').one('draw', function () {
            $id = $("#ticketList tbody td").first().html();
            console.log($id);
        } );
    } );
```

When the ticket-type-selector drop-down is changed, redraw the table, then save the value of the first cell in the body of the table to $id, and write that on the log.




JQuery-UI <a name="jquery-ui">
--------------------------------------

JQuery-UI provides user-interface widgets, controls, and interactions for an app. 
http://jqueryui.com/

