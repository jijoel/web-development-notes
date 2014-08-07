CSS
========

Cascading style sheets

Include on a page like so:

    <link rel="stylesheet" href="/assets/css/styles.css" media="screen">

Padding does different things, based on the number of entries you write:

    padding: 1;         // sets 1 unit all around (write as 1px, or 1pt, or 1em, etc.)
    padding: 1 2;       // top/bottom are 1, left/right are 2
    padding: 1 2 3;     // top is 1, right/left are 2, bottom is 3
    padding: 1 2 3 4;   // top is 1, right is 2, bottom is 3, left is 4

.class1.class2  - does something unique when an element has BOTH classes


### Floating child elements inside a div

Although elements like <div>s normally grow to fit their contents, using the float property can cause a problem: if floated elements have non-floated parent elements, the parent will collapse. To work around this issue, set overflow to auto in the parent (hackish, but works):

    .console {
        overflow: auto;
    }

    .console p {
        float: left;
    }


### Keep a block of text together when printing:

    .class {
        page-break-inside: avoid;
    }


### Box with rounded corners and a shadow:

    img {
        margin: auto;               // center in parent div
        max-width: 100%;            // resize as needed; maintain aspect ratio up to 100% of parent div
        max-height: 100%;           // resize as needed; maintain aspect ratio up to 100% of parent div
        border-radius: 5px;         // small rounded corners
        border: 1px solid black;            // color/style 
        box-shadow: 5px 5px 10px #999;             // very light gray shadow 
        -webkit-box-shadow: 5px 5px 10px #999;     // needed for webkit browsers
    }
