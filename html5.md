HTML 5
===============

You can include any number of custom attributes in any page element, simply precede the name with data-. eg:

    data-pk:        primary key
    data-name:      name
    data-whatever:  whatever ;-)


Canvas
-----------
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

