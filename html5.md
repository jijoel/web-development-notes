HTML 5
===============

* [Custom Attributes](#custom)
* [Canvas](#canvas)
* [Flexbox](#flexbox)





Custom Attributes <a name="custom">
---------------------------------------

You can include any number of custom attributes in any page element, simply precede the name with data-. eg:

    data-pk:        primary key
    data-name:      name
    data-whatever:  whatever ;-)

Access them in jquery with $('.selector').data('pk');


Canvas <a name="canvas">
---------------------------
The canvas is a drawing surface for various shapes. Here's an example on how to use it:

``` js
    var canvas = document.getElementById('myCanvas');
    var context = canvas.getContext('2d');

    function writeTextBlock(left, top, width, height, text) 
    {
        context.save();                 // Saves the visible region, so we can un-clip, later
        context.beginPath();
        context.rect(left, top, width, height);
        context.clip();                 // Output can only take place in the given viewport
        
        context.beginPath();
        context.rect(left, top, width, height);
        context.fillStyle = "yellow";
        context.fill();
        context.lineWidth = 2;
        context.strokeStyle = 'black';
        context.stroke();
        
        context.fillStyle = 'black';
        context.font = '18pt Calibri';
        context.fillText(text, left+4, top+20);
        context.restore();              // Restore to standard (saved) viewport
    }

    writeTextBlock(1,1,100,50, 'This is a test');
    writeTextBlock(1,50,130,20, 'Something else');

    context.beginPath();
    context.rect(120, 50, 200, 100);
    context.clip();

    context.beginPath();
    context.rect(120, 50, 200, 100);
    context.fillStyle = 'yellow';
    context.fill();
    context.lineWidth = 7;
    context.strokeStyle = 'black';
    context.stroke();

    context.fillStyle = 'blue';
    context.font = 'italic 40pt Calibri';
    context.fillText('Hello World!', 150, 100);
```

Here's an example, using KineticJS:

```js
    var stage = new Kinetic.Stage({
        container: 'container',
        width: 578,
        height: 300
    });

    var layer = new Kinetic.Layer();

    var group = new Kinetic.Group({
        clip: [100, 40, 200, 100],
        draggable: true
    });

    var yellowCircle = new Kinetic.Circle({
        x: 0,
        y: 0,
        radius: 50,
        fill: 'yellow',
        stroke: 'black',
        draggable: true,
    });

    var blueBlob = new Kinetic.Blob({
        points: [73, 140, 340, 23, 500, 109, 300, 170],
        stroke: 'blue',
        strokeWidth: 10,
        fill: '#aaf',
        tension: 0.8,
    });

    var redBlob = new Kinetic.Blob({
        points: [73, 140, 340, 23, 500, 109],
        stroke: 'red',
        strokeWidth: 10,
        fill: '#faa',
        tension: 1.2,
        scale: 0.5,
        x: 100,
        y: 50,
        draggable: true,
    });

    var someText = new Kinetic.Text({
        x: 105,
        y: 45,
        text: 'Some Text',
        fill: 'black',
        fontSize: 20,
        fontFamily: 'Calibri',
        draggable: true,
    });

    layer.add(yellowCircle);
    group.add(blueBlob);
    group.add(redBlob);
    group.add(someText);
    layer.add(group);
    stage.add(layer);
```



Flexbox
-----------------
https://www.adobe.com/devnet/html5/articles/working-with-flexbox-the-new-spec.html

Flexbox is new to HTML5; many browsers can't handle it yet. It's very powerful for laying things out. It's still in development, and frequently needs a vendor prefix, eg:

    display: -webkit-box;
    display: -webkit-flex;
    display: -moz-box;
    display: -ms-flexbox;
    display:  flex;

There are two main objects with a flexbox:

    Flex Container: Parent element in which flex item are located
    Flex item:      The child of a flex container (the parent)


### Flex Container

    display: flex           // sets up this object as a container

    flex-flow:              // The main axis for this container
        ROW                 // 1 row, items in columns
        column              // 1 column, items in rows
        wrap                // ???????

    flex-wrap:              //
        wrap                //
        nowrap              //
        wrap-reverse        //

    flex-direction:         //
        ROW                 //
        row-reverse         //
        column              //
        column-reverse      //

    justify-content:        // Align elements along the main axis of the container
        flex-start          // Packed toward start
        flex-end            // Packed toward end
        center              // Packed toward center
        space-between       // Evenly distributed in line
        space-around        // Evenly distributed in line with half-size spaces at ends

    align-items:            // Align items along the cross axis of the container

    baseline                // Align the baseline of items to the cross line
    stretch                 // Stretch items a little to align Flex items and baseline


### Flex Items

    order           // changes the order of an item
    flex            // shorthand for: none | [ <flex-grow> <flex-shrink> || <flex-basis> ]
    flex-grow       // How much an item can grow relative to others (integer)
    flex-shrink     // How much an item can shrink relative to others (integer)
    flex-basis      // Initial width of item before free space is distributed (can be auto)
    align-self      // overrides align-items for this item


### Examples

This is a picture, with a name, date, and comment:

```css
    *,*::before,*::after {
        box-sizing:border-box
    }
    html,body {
        margin: 0;
        padding: 0;
        height: 100%;
    }
    .comment {
        display: -webkit-flex;
        flex-flow: column;
    }
    .comment header {
        display: -webkit-flex;
        flex-flow: row;
    }
    .comment header img {
        height: 50px;
        width: 50px;
        min-width: 50px;
        border: 4px solid black;
        margin-right: 10px;
    }
    .comment header .text {
         margin-top: auto; /* pin to bottom */
         margin-bottom: auto; /* pin to top */
    }
```

It can work with html that looks like this:

```html
    <div class="comment">
        <header>
            <img src="foo">
            <div class="text">
                Header text (including name and date)
            </div>
        </div> <!--  header -->
        <div class="body">
            Body text
        </div>
    </div> <!--  .comment -->
```


Custom Scrollbars
----------------------------
You can create custom scroll bars, for any object or type of object.

http://css-tricks.com/custom-scrollbars-in-webkit/

    ::-webkit-scrollbar              { }
    ::-webkit-scrollbar-button       { }
    ::-webkit-scrollbar-track        { }
    ::-webkit-scrollbar-track-piece  { }
    ::-webkit-scrollbar-thumb        { }
    ::-webkit-scrollbar-corner       { }
    ::-webkit-resizer                { }

