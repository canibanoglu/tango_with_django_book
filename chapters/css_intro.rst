.. _css-course-label:

A CSS Crash Course
==================
In web development, we use *Cascading Style Sheets (CSS)* to describe the presentation of a HTML document (i.e. its look and feel).

Each element within a HTML document can be *styled*. The CSS for a given HTML element describes how it is to be rendered on screen. This is done by ascribing *values* to the different *properties* associated with an element. For example, the ``font-size`` property could be set to ``24pt`` to make any text contained within the specified HTML element to appear at 24pt. We could also set the ``text-align`` property to a value of ``right`` to make text appear within the HTML element on the right-hand side.

.. note:: There are many, many different CSS properties that you can use in your stylesheets. Each provides a different functionality. Check out the `W3C website <http://www.w3.org/TR/CSS2/propidx.html>`_ and `HTML Dog <http://www.htmldog.com/reference/cssproperties/>`_ for lists of available properties. `pageresource.com <http://www.pageresource.com/dhtml/cssprops.htm>`_ also has a neat list of properties, with descriptions of what each one does.

CSS works by following a *select and apply pattern* - for a specified element, a set of styling properties are applied. Take a look at the following example in Figure :num:`fig-css-render` where we have some HTML containing ``<h1>`` tags. In the CSS code example, we specify that all ``h1`` are styled.  We'll come back to `selectors <http://www.w3schools.com/cssref/css_selectors.asp>`_ in Section :ref:`css-course-basic-selectors-label`. For now though, you can assume the CSS style defined will be applied to our ``<h1>`` tags. The style contains four properties:

- the first property (``font-size``) sets the size of the font to 16pt;
- the second property (``font-style``) italicises the contents of all ``<h1>`` tags within the document;
- the third property (``text-align``) centres the text of the ``<h1>`` tags; and
- the final property (``color``) sets the colour of the text to red via `hexadecimal code <http://html-color-codes.com/>`_ ``#FF0000``.

With all of these properties applied, the resultant page render can be seen in the browser in Figure :num:`fig-css-render`.

.. _fig-css-render:

.. figure:: ../images/css-render.pdf
	:figclass: align-center

	Illustration demonstrating the rendered output of the sample HTML markup and CSS stylesheet shown. Pay particular attention to the CSS example - the colours are used to demonstrate the syntax used to define styles and the property/value pairings associated with them.

.. note:: Due to the nature of web development, *what you see isn't necessarily what you'll get*. This is because different browsers have their own way of interpreting `web standards <http://en.wikipedia.org/wiki/Web_standards>`_ and so the pages may be rendered differently. This quirk can unfortunately lead to plenty of frustration.

Including Stylesheets
---------------------
Including stylesheets in your webpages is a relatively straightforward process, and involves including a ``<link>`` tag within your HTML's ``<head>``. Check out the minimal HTML markup sample below for the attributes required within a ``<link>`` tag.

.. code-block:: html
	
	<!DOCTYPE html>
	<html>
	    <head>
	        <link rel="stylesheet" type="text/css" href="URL/TO/stylesheet.css" />
	        <title>Sample Title</title>
	    </head>
	    
	    <body>
	        <h1>Hello world!</h1>
	    </body>
	</html>

As can be seen from above, there are at minimum three attributes which you must supply to the ``<link>`` tag:

- ``rel``, which allows you to specify the relationship between the HTML document and the resource you're linking to (i.e., a stylesheet);
- ``type``, in which you should specify the `MIME type <http://en.wikipedia.org/wiki/Internet_media_type>`_ for CSS; and
- ``href``, the attribute which you should point to the URL of the stylesheet you wish to include.

With this tag added, your stylesheet should in included with your HTML page, and the styles within the stylesheet applied. It should be noted that CSS stylesheets are considered by Django as static media, meaning you should place them within your project's ``static`` directory.

.. note:: You can also add CSS to your HTML documents *inline*, meaning that the CSS is included as part of your HTML page. However, this isn't generally advised because it removes the nice abstraction between presentational semantics (CSS) and content (HTML). 

.. _css-course-basic-selectors-label:

Basic CSS Selectors
-------------------
CSS selectors are used to map particular styles to particular HTML elements. In essence, a CSS selector is a *pattern*. Here, we cover three basic forms of CSS selector: *element selectors*, *id selectors* and *class selectors*. In Section :ref:`css-course-links-label`, we also touch on what are known as *pseudo-selectors*.

Element Selectors
-----------------
Taking the CSS example from Figure :num:`fig-css-render`, we can see that the selector ``h1`` matches to any ``<h1>`` tag. Any selector referencing a tag like this can be called an *element selector*. We can apply element selectors to any HTML element such as ``<body>``, ``<h1>``, ``<h2>``, ``<h3>``, ``<p>`` and ``<div>``. These can be all styled in a similar manner. However, using element selectors is pretty crude - styles are applied to *all* instances of a particular tag. We usually want a more fine-grained approach to selecting what elements we style, and this is where *id selectors* and *class selectors* come into play.

ID Selectors
............
The *id selector* is used to map to a unique element on your webpage. Each element on your webpage can be assigned a unique id via the ``id`` attribute, and it is this identifier that CSS uses to latch styles onto your element. This type of selector begins with a hash symbol (``#``), followed directly by the identifier of the element you wish to match to. Check out Figure :num:`fig-css-id` for an example.

.. _fig-css-id:

.. figure:: ../images/css-id.pdf
	:figclass: align-center

	An illustration demonstrating the use of an *id selector* in CSS. Note the blue header has an identifier which matches the CSS attribute ``#blue_header``.

Class Selectors
...............
The alternative option is to use *class selectors*. This approach is similar to that of *id selectors*, with the difference that you can legitimately target multiple elements with the same class. If you have a group of HTML elements that you wish to apply the same style to, use a class-based approach. The selector for using this method is to precede the name of your class with a period (``.``) before opening up the style with curly braces (``{ }``). Check out Figure :num:`fig-css-class` for an example.

.. _fig-css-class:

.. figure:: ../images/css-class.pdf
	:figclass: align-center

	An illustration demonstrating the use of a *class selector* in CSS. The blue headers employ the use of the ``.blue`` CSS style to override the red text of the ``h1`` style.

.. warning:: Try to use id selectors sparingly. `Ask yourself: <http://net.tutsplus.com/tutorials/html-css-techniques/the-30-css-selectors-you-must-memorize/>`_ *do I absolutely need to apply an identifier to this element in order to target it?* If you need to apply it to more than one element, the answer will always be **no**. In cases like this, you should use a class or element selector.

Fonts
-----
Due to the huge number available, using fonts has historically been a pitfall when it comes to web development. Picture this scenario: a web developer has installed and uses a particular font on his or her webpage. The font is pretty arcane - so the probability of the font being present on other computers is relatively small. A user who visits the developer's webpage subsequently sees the page rendered incorrectly as the font is not present on their system. CSS tackles this particular issue with the ``font-family`` property.

The value you specify for ``font-family`` can be a *list* of possible fonts - and the first one your computer or other device has installed is the font that is used to render the webpage. Text within the specified HTML element subsequently has the selected font applied. The example CSS shown below applies *Arial* if the font exists. If it doesn't, it looks for *Helvetica*. If that font doesn't exist, any available `sans-serif font <http://en.wikipedia.org/wiki/Sans-serif>`_ is applied.

.. code-block:: css
	
	h1 {
	    font-family: 'Arial', 'Helvetica', sans-serif;
	}

In 1996, Microsoft started the `Core fonts for the Web <http://en.wikipedia.org/wiki/Core_fonts_for_the_Web>`_ initiative with the aim of guaranteeing a particular set of fonts to be present on all computers. Today however, you can use pretty much any font you like - check out `Google Fonts <http://www.google.com/fonts>`_ for examples of the typesets you can use and `this Web Designer Depot article <http://www.webdesignerdepot.com/2013/01/how-to-use-any-font-you-like-with-css3/>`_ on how to use such fonts.

Colours and Backgrounds
-----------------------
Colours are important in defining the look and feel of your website. You can change the colour of any element within your webpage, ranging from background colours to borders and text. In this book, we make use of *hexadecimal colour codes* to choose the colours we want. As you can see from the list of basic colours in Figure :num:`fig-css-colours`, you can supply either a *hexadecimal* or *RGB (red-green-blue)* value for the colour you want to use.

.. warning:: You must take great care when picking colours to use on your webpages. Don't select colours that don't contrast well - people simply won't be able to read them! There are many websites available that can help you pick out a good colour scheme - try `colorcombos.com <http://www.colorcombos.com/>`_ for starters.

.. _fig-css-colours:

.. figure:: ../images/css-colours.pdf
	:figclass: align-center
	
	Illustration of some basic colours with their corresponding hexadecimal and RGB values. Illustration adapted from `W3Schools <http://www.w3schools.com/cssref/css_colors.asp>`_.

There are many different websites which you can use to aid you in picking the right hexadecimal codes to enter into your stylesheets. You aren't simply limited to the nine examples above! Try out `html-color-codes.com <http://html-color-codes.com/>`_ for a simple grid of colours and their associated six character hexadecimal code. You can also try sites such as `color-hex.com <http://www.color-hex.com/color-wheel/>`_ which gives you fine-grain control over the colours you can choose.

.. note:: For more information on how colours are coded with hexadecimal, check out `this thorough tutorial <http://www.quackit.com/css/css_color_codes.cfm>`_.

.. warning:: As you may have noticed, CSS uses American/International English to spell words. As such, there are a few words which are spelt slightly differently compared to their British counterparts, like ``color`` and ``center``. If you have grown up in Great Britain, double check your spelling and be prepared to spell it the *wrong way!* Hah!

Applying colours to your elements is a straightforward process. The property that you use depends on the aspect of the element you wish to change! The following subsections explain the relevant properties and how to apply them.

Text Colours
............
To change the colour of text within an element, you must apply the ``color`` property to the element containing the text you wish to change.
The following CSS for example changes all the text within the associated webpage to bright red - hardly a sensible design choice!

.. code-block:: css
	
	body {
	    color: #FF0000;
	}

If you wish to change the colour of a small portion of text, wrap the text in a ``<span>`` tag and assign a class or unique identifier to the element. From there, you can simply reference the ``<span>`` tag in your stylesheet and apply a ``color``.

Borders
.......
You can change the colour of an element's *borders*, too. We'll discuss what borders are in Section **?????????** - but for now, we'll show you how to apply colours to them to make everything look pretty.

Border colours can be specified with the ``border-color`` property. You can supply one colour for all four sides of your border, or specify a different colour for each side. To achieve this, you'll need to supply different colours, each separated by a space.

.. code-block:: css
	
	.some-element {
	    border-color: #000000 #FF0000 #00FF00
	}

In the example above, we use multiple colours to specify a different colour for three sides. Starting at the top, we rotate clockwise. Thus, the order of colours for each side would be ``top right bottom left``.

Our example applies any element with class ``some-element`` with a black top border, a red right border and a green bottom border. No left border value is supplied, meaning that the left-hand border is left transparent. To specify a color for only one side of an element's border, consider using the ``border-top-color``, ``border-right-color``, ``border-bottom-color`` and ``border-left-color`` properties where appropriate.

Backgrounds and Images
......................
You can also change the colour of an element's background through use of the CSS ``background-color`` property. Like the ``color`` property described above, the ``background-color`` property can be easily applied by specifying a single colour as its value. Check out the example below which applies a bright green background to the entire webpage. Yuck!

.. code-block:: css
	
	body {
	    background-color: #00FF00;
	}

Of course, a colour isn't the only way to change your backgrounds. You can also apply background images to your elements, too. We can achieve this through the ``background-image`` property.


.. code-block:: css
	
	#some-unique-element {
	    background-image: url('../images/filename.png');
	    background-color: #000000;
	}

The example above makes use of ``filename.png`` as the background image for the element with identifier ``some-unique-element``. The path to your image is specified *relative to the path of your CSS stylesheet*. Our example above uses the `double dot notation to specify the relative path <http://programmers.stackexchange.com/a/186719>`_ to the image. *Don't provide an absolute path here; it won't work as you expect!* We also apply a black background colour to fill the gaps left by our background image - it may not fill the entire size of the element.

.. note:: By default, background images default to the top-left corner of the relevant element and are repeated on both the horizontal and vertical axes. You can customise this functionality by altering `how the image is repeated <http://www.w3schools.com/cssref/pr_background-repeat.asp>`_ with the ``background-image`` property. You can also specify `where the image is placed <http://www.w3schools.com/cssref/pr_background-position.asp>`_ by default with the ``background-position`` property.

The Cascade
-----------
It's worth pointing out where the *Cascading* in *Cascading Style Sheets* comes into play. You may have noticed in the example rendered output in Figure :num:`fig-css-render` that the red text is **bold**, yet no such property is defined in our ``h1`` style. This is a perfect example of what we mean by *cascading styles*. Most HTML elements have associated with them a *default style* which web browsers apply. For ``<h1>`` elements, the `W3C website provides a typical style that is applied <http://www.w3.org/TR/html-markup/h1.html#h1-display>`_. If you check the typical style, you'll notice that it contains a ``font-weight: bold;`` property and value pairing, explaining where the **bold** text comes from. As we define a further style for ``<h1>`` elements, typical property/value pairings *cascade* down into our style. If we define a new value for an existing property/value pairing (such as we do for ``font-size``), we *override* the existing value. This process can be repeated many times - and the property/value pairings at the end of the process are applied to the relevant element. Check out :num:`fig-css-cascading` for a graphical representation of the cascading process.

.. _fig-css-cascading:

.. figure:: ../images/css-cascading.pdf
	:figclass: align-center

	Illustration demonstrating the *cascading* in *Cascading Style Sheets* at work. Take note of the ``font-size`` property in our ``h1`` style - it is overridden from the default value. The cascading styles produce the resultant style, shown on the right of the illustration.

.. _css-course-positioning:

Inline and Block-Level Elements
-------------------------------
Throughout the crash course thus far, we've introduced you to the ``<span>`` element.





In the markup snippet above, we introduce two new HTML tags - ``<div>`` and ``<span>``. Essentially, these tags themselves are meaningless, and are only present to provide you with a way to contain and separate your page's content. ``<div>`` tags can be considered a *block-level element* used to contain other content. A block-level element will by default display a line break after it. Conversely, ``<span>`` tags can be considered as *inline elements*, and can be used as a container for text. The difference between block-level elements and inline elements are key - and explain why a ``<div>`` can contain ``<span>`` elements, but not vice versa. For an illustration of the difference between the two, check out Figure :num:`fig-css-nesting-blocks`. In the diagram provided, you see ``<div>`` and ``<span>`` elements represented as boxes. The diagram also hints at how you can nest blocks.

Basic Positioning
-----------------
An important concept that we have not yet covered in this CSS crash course regards the positioning of elements within your webpage. Most of the time, you'll be satisfied with inline elements appearing alongside each other, and block-level elements appearing on newlines. However, there will be scenarios where you require a little bit more control on where everything goes. In this section, we'll briefly cover four important techniques for positioning elements within your webpage.

CSS *floats* are one of the most straightforward techniques for positioning elements within your webpage. Indeed, we've already made use of floats - have a look at the CSS styles that correspond to Rango's navigation bar! Using floats allows us to position elements to the left or right of a particular container - or the page.

Imagine that we have a ``<div>`` element that contains a series of nested ``<span>`` elements, as shown in Figure :num:`fig-css-positioning-float1`. Now, imagine that we wish to position the blue ``<span>`` elements to the right of our container, and the yellow ``<span>`` elements to their current position - at left of our container.

.. _fig-css-positioning-float1:

.. figure:: ../images/css-positioning-float1.pdf
	:figclass: align-center
	
	Our fictional ``<div>`` container, with four ``<span>`` child elements. Yellow ``<span>`` elements are to remain at the left, while blue ``<span>`` elements should be moved to the right.



Relative Positioning
....................

Absolute Positioning
....................

Styling Lists
-------------

.. _css-course-links-label:

Styling Links
-------------

Additional Reading
------------------
What we've discussed in this section is by no means a definitive guide to CSS. There are `300-page books <http://www.amazon.co.uk/Professional-CSS-Cascading-Sheets-Design/dp/047017708X>`_ devoted to CSS alone! What we have provided you with here is a very brief introduction showing you the very basics of what CSS is and how you can use it.

As you develop your web applications, you'll undoubtedly run into issues and frustrating problems with styling web content. This is part of the learning experience, and you still have a bit to learn. We strongly recommend that you invest some time trying out several online tutorials about CSS - there isn't really any need to buy a book (unless you want to).

- The *W3C* `provides a neat tutorial on CSS <http://www.w3.org/Style/Examples/011/firstcss.en.html>`_, taking you by the hand and guiding you through the different stages required. They also introduce you to several new HTML elements along the way, and show you how to style them accordingly.

- `W3Schools also provides some cool CSS tutorials <http://www.w3schools.com/css/css_examples.asp>`_. Instead of guiding you through the process of creating a webpage with CSS, *W3Schools* has a series of mini-tutorials and code examples to show you to to achieve a particular feature, such as setting a background image. We highly recommend that you have a look here.

- `html.net has a series of lessons on CSS <http://html.net/tutorials/css/>`_ which you can work through. Like W3Schools, the tutorials on *html.net* are split into different parts, allowing you to jump into a particular part you may be stuck with.

- It's also worth having a look at `CSSeasy.com <http://csseasy.com/>`_'s collection of tutorials, providing you with the basics on how to develop different kinds of page layouts.

This list is by no means exhaustive, and a quick web search will indeed yield much more about CSS for you to chew on. Just remember: CSS can be tricky to learn, and there may be times where you feel you want to throw your computer through the window. We say this is pretty normal - but take a break if you get to that stage. We'll be tackling some more advanced CSS stuff as we progress through the tutorial in the next few sections.

.. note:: With an increasing array of devices equipped with more and more powerful processors, we can make our web-based content do more. To keep up, `CSS has constantly evolved <http://www.w3schools.com/css3/css3_intro.asp>`_ to provide new and intuitive ways to express the presentational semantics of our SGML-based markup. To this end, support `for relatively new CSS properties <http://www.quackit.com/css/css3/properties/>`_ may be limited on several browsers, which can be a source of frustration. The only way to reliably ensure that your website works across a wide range of different browsers and platforms is to `test, test and test some more! <http://browsershots.org/>`_


