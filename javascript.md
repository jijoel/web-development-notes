Javascript / JQuery
======================

Run something immediately after the page has been loaded:

    $(function(){
        // run this code
    });  


Selectors:

    $('li:first')             // gets the first item of first list 
    $('li').first()           // gets the first item of first list 
    $('li').eq(x)             // gets the xth item of ANY list (0-based)
    $('li:first-child')       // gets the first item in EACH list    
    $('li:nth-child(2)')      // gets the second item in EACH list
    $('li:nth-last-child(2)') // gets the second item from the bottom in EACH list
    $('s1').children('s2')    // gets immediate 's2' children of 's1'
    $('s1').find('s2')        // gets all 's2' objects under 's1' in tree
    $('s1').parent('s2')      // gets immediate 's2' parent found above 's1'
    $('s1').parents('s2')     // gets all 's2' parents found for 's1' or above
    $('s1').closest('s2')     // gets first 's2' parent found for 's1' or above
    $('s1').next()            // gets next 's1' element
    $('s1').siblings()        // gets all other elements on same level
    $('#id')                  // get everything with id '#id'
    $('.class')               // get everything with class '.class'
    $('s1').filter('f')       // gets everything that matches 'f'
    
Change class, css, attributes, or other data:

    $('selector').addClass('className');
    $('selector').removeClass('className');
    $('selector').css('color', 'green');
    $('selector').attr('attr');         // get attribute 'attr' from 'selector'
    $('selector').attr('attr', 'val');  // set attribute 'attr' to 'val'
    $('selector').removeAttr('attr');   // removes an attribute
    $('selector').data('file')          // gets 'data-file' attribute
    $('selector').text();               // get text
    $('selector').text('new text');     // set new text
    
Chaining:

    $('selector')                       // for the button that was clicked
        .siblings('selector')           // select all siblings of type 'selector'
            .removeAttr('disabled')     //    and remove the 'disabled' attribute
            .end()                      // end the siblings selector (go back to original)
        .setAttr('disabled');           // and set the 'disabled' attribute

        
Events:

    $('s1').on('click', function(){});  // does something when user clicks 's1'
    .click(function(){})                // same as above (shortcut)
    .hover()                            // on hover event
    .change()                           // change text in input, item in drop-down
    .blur
    .focus
    .focusin
    .focusout
    .load
    .resize
    .unload
    .dblclick
    .mousedown
    .mouseup
    .mousemove
    .mouseover
    .mouseout
    .mouseenter
    .mouseleave
    .select
    .submit
    .keydown
    .keypress
    .keyup
    .error
    .contextmenu
        
    $('dt').on('mouseenter', function(){  // when the mouse moves in to a dt object
    $(this).next().slideDown()            // slideDown to show the associated dd
        .siblings('dd').slideUp();        // and hide all other dd objects in the list

Note that this puts an event listener in each 's1' on the page. If you have a lot of elements, you will get better performance if you put the event listener in the parent, instead:

    $('dl').on('mouseenter', 'dt', function() {
    
This means that when the mouse enters a 'dt' child object within the $('dl'), trigger the event. It lets you have one event listener, rather than many (hundreds, in some cases).

    $('h1').bind('click') is an old way of saying  $('h1').on('click')
    $('h1').live('click') is an old way of saying  $('document').on('click', 'h1')
    $('body').delegate('h1','click') old, saying  $('body').on('click', 'h1')

You can also remove event listeners:

    $('h1').on('click', ...       // adds a listener for clicking on h1
    $('h1').off('click');         // removes the listener
    
Set variables:
(put this at the bottom of the script)

    (function(){ 
        // set variables here
    })();

Controlling visibility:

    var $s = $('selector')
    $s.hide()
    $s.show()
    $s.fadeIn()
    $s.fadeOut()
    $s.slideDown()
    $s.slideUp()
    $s.toggle()              // if visible, hide, if hidden, show
    $s.css('display','none')
    
Filters:

    filter(':nth-child(n+4)')   // select every child, starting at the 4th

Creating and appending to the DOM:

    $('s').append('text')     // append text to bottom of selector
    $('s').prepend('text')    // write text to top of selector
    $('s').after('text')      // write text after the selector
    $('s').before('text')     // write text before the selector
    $('h1').appendTo('s')           // moves h1 to bottom of 's'
    $('s').after($('h1'))           // moves h1 to bottom of 's'
    $('<p></p>').appendTo('s')      // append created object to bottom of 's'
    $('<p></p>').prependTo('s')     // write created object to top of 's'
    $('<p></p>').insertAfter('s')   // write created object after 's'
    $('<p></p>').insertBefore('s')  // write created object before 's'
    
We can write html like so:

    $('<p></p>', {
        text: 'writing more to the end of body',
        class: 'myClass', 
    }).appendTo('body');

(this also works for simple things)

    $('body').append('<p>writing more to the end of body</p>');

So, for instance, to write a call-out (defined as <span class="co">) next to the paragraph where it occurs:

    var $co = $('span.co').each(function(){
        var $this = $(this);
        $('<blockquote></blockquote>', {
            class: 'co',
            text:  $this.text(),
        }).prependTo( $this.closest('p') );

Copy (clone) the currently selected element, and append it to the end of the document:

    $('this').clone().appendTo('body');
    

Structuring jquery scripts
----------------------------
Using anonymous functions everywhere can get to be pretty deeply nested and confusing. Instead, we can set up various classes (which are just variables in javascript), and have named functions in them. For instance, this would work:

    (function(){
    var contactForm = {
        init: function(){
            $('<button></button>', {
                text: 'contact me',
            }).insertAfter( $('.article') )
            .on('click', function(){
                $('#contact').slideDown();
            });
        }
    };

    $('html').addClass('js');
    contactForm.init();
    })();

But we can also do this:

    (function(){
    var contactForm = {
        init: function(){
            $('<button></button>', {
                text: 'contact me',
            }).insertAfter($('.article'))
            .on('click', this.show);
        },
        show: function() {
            $('#contact').slideDown();
        }
    }

    $('html').addClass('js');
    contactForm.init();
    })();


These are equivalent:

    contactForm.container
    contactForm['container']

    

Configuration
--------------

You can set configuration parameters in a class with:

    var contactForm = {
        config:  {
            effect: 'slideToggle',
            speed:  500,
        },

To use these parameters, in other functions:

    contactForm.container[contactForm.config.effect](contactForm.config.speed);

To override these parameters, in the init function:

    init: function(config){
        $.extend(this.config, config);

And, when instanciating the class:

    contactForm.init({
        effect: 'fadeToggle',
        speed:  200,
    });

    