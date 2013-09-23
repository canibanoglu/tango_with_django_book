Working with Templates
======================
With the Rango tutorial thus far, we've created several Django HTML templates for different pages in the application. You've probably already noticed that there's an awful lot of redundancy in the HTML templates - and if you haven't, go have a look at your templates now. Do you notice any similarities between the templates?

In most applications, there is often a lot of repetition in HTML markup. This isn't a particularly bad thing. Indeed, a well-designed website includes plenty of repetition, providing users of the site with a familiarity - as well as potentially making it easier for users to find what they are looking for. Most websites have repetitive page headers, navigation bars, sidebars and footers. In terms of code, there are often identical scripts which are added to each page. Repetitive navigation bars may require the same JavaScript and styling properties!

With repetition a key ingredient to developing a good-looking website, Django's template system provides us with a really nifty tool to help us achieve this. It's called *template inheritance*, and is pretty straightforward to use. Think of it like class inheritance in other programming languages like Java - start with a base class, and extend it in different ways to allow for different functionality.

We'll be showing you template inheritance in this section to demonstrate its power, and how you can simplify Rango's templates with such an approach. We'll be following this basic approach.

#. We'll identify reoccurring parts of each template that are repeated across the Rango application.
#. We'll take the reoccurring stuff and place it in a new *base template*, defined by so-called *blocks*.
#. With each template in the application, we'll then *inherit* from the base HTML template, and add page-specific HTML and other content into the blocks we define.

Reoccurring HTML and the Base Template
--------------------------------------
Identifying reoccurring HTML can be quite tricky at first - especially if you have crazy-complex templates. It can take several attempts - you may miss aspects first time around, not realising that they may or may not be reoccurring. This is normal: patience is a virtue!

.. note:: You should always aim to extract as much reoccurring content for your base templates. While it may be a bit more of a challenge for you to do initially, the time you will save in maintenance of your templates in the future far outweighs the initial overhead. Think about it: would you rather maintain one copy of your markup than five copies or more?

While we won't be repeating all of the templates we have created thus far, we have identified the following HTML for you that reoccurs in every page.

.. code-block:: html
	
	{% load static %}

	<!DOCTYPE html>
	<html>
	    <head>
	        <title>Rango</title>
	    </head>
	
	    <body>
	        <!-- Page specific content goes here -->
	    </body>
	</html>

In essence, the identified HTML is the barebones for creating a blank HTML5 page. We identify the page as HTML5 with the ``<!DOCTYPE html>`` document type declaration - and set the title of the page to ``Rango`` like in all previous templates. Differences occur within the ``<body>`` of each page - so we have included only a HTML comment which we will replace in the next section. The ``{% load static %}`` Django template command includes the ``static`` library, which will allow us to include any static media in our base template (or inheriting templates) with minimum hassle.

Take this base template and save it as ``base.html`` in Rango's ``templates`` directory (e.g. ``templates/rango/base.html``). Our new base template is now called ``base.html``. Keep that in mind as we progress through the tutorial.

Template Blocks
---------------
Now that we've identified our base template, we can prepare it for our inheriting templates. We identified in the previous section that each inheriting template's content should be placed between the base template's ``<body>`` tags - which is identified by the HTML comment we placed.

We'll be replacing the comment with a Django template block - a portion of the template which can be `overridden by an inheriting template <https://docs.djangoproject.com/en/1.5/topics/templates/#id1>`_. We can replace the HTML comment we previously placed with a Django block. Let's call it ``body_block`` so we can clearly identify what it should contain. Our template should now look something like the code sample below.

.. code-block:: html
	
	{% load static %}

	<!DOCTYPE html>
	<html>
	    <head>
	        <title>Rango</title>
	    </head>
	
	    <body>
	        {% block body_block %}{% endblock %}
	    </body>
	</html>

Note the syntax that is used - it's a standard Django template command, so commands for starting and ending blocks are contained within ``{%`` and ``%}`` tags. To start a block, the command is ``block <NAME>``, where ``<NAME>`` is the name of the block you wish to create. You must also ensure that you close the block with the ``endblock`` command, again enclosed within Django template tags.

You can also specify 'default content' for your blocks, if you so desire. Our ``body_block`` defined above presently has no default content associated with it. This means that if no inheriting template were to employ the use of ``body_block``, nothing would be rendered - as shown in the code snippet below.

.. code-block:: html
	
	{% load static %}

	<!DOCTYPE html>
	<html>
	    <head>
	        <title>Rango</title>
	    </head>
	
	    <body>
	        
	    </body>
	</html>

However, we can overcome this by placing default content within the block definition, like so.

.. code-block:: html
	
	{% load static %}

	<!DOCTYPE html>
	<html>
	    <head>
	        <title>Rango</title>
	    </head>
	
	    <body>
	        {% block body_block %}This is body_block's default content.{% endblock %}
	    </body>
	</html>

If a template were to inherit our base template without employing the use of ``body_block``, the rendered outcome would now look something like the markup shown below.

.. code-block:: html
	
	{% load static %}

	<!DOCTYPE html>
	<html>
	    <head>
	        <title>Rango</title>
	    </head>
	
	    <body>
	        This is body_block's default content.
	    </body>
	</html>

Hopefully this all makes sense - and for now, we'll be leaving ``body_block`` blank by default. All of our inheriting templates will be making use of ``body_block`` to get their content rendered. You can place as many blocks in your templates as you so desire. For example, you could create a block for the page title, meaning you can alter the title of each page while still inheriting from the same base template.

Blocks are a really powerful feature of Django's template system - and it's highly recommended that you have a look at `Django's documentation <https://docs.djangoproject.com/en/1.5/topics/templates/#id1>`_ to see more of the awesome things you can do with blocks.

Inheriting
----------
Now that we've created a block, how do we create a template to inherit from the base template? Here, we'll show you how. For this example, we'll be taking Rango's ``category.html`` template and transforming it into a template which inherits from ``base.html``.

With the ``category.html`` template open in your text editor, we first tell Django's template system that we wish to inherit from a particular template. To do this, we place a Django template command on the first line of the template, called ``extends``:

.. code-block:: html
	
	{% extends 'rango/base.html' %}

As you can see from the example above, we provide one parameter to the ``extends`` command. This is a string containing the name of the template we wish to inherit from - in this case, the ``base.html`` template we created previously.

.. note:: The parameter you supply to the ``extends`` command should be relative from your project's ``templates`` directory. For example, all templates we use for Rango should extend from ``rango/base.html``, not ``base.html``.

With this in place, take a new line or two underneath the ``extends`` command, and add the following so that your template now looks something like the following.

.. code-block:: html
	
	{% extends 'rango/base.html' %}
	
	{% block body_block %}
	
	{% endblock}
	
	<!DOCTYPE html>
	<html>
	    <head>
	        <title>Rango</title>
	    </head>

	    <body>
	        <h1>{{ category_name }}</h1>

	        {% if pages %}
	            <ul>
	                {% for page in pages %}
	                <li><a href="{{ page.url }}">{{ page.title }}</a></li>
	                {% endfor %}
	            </ul>
	        {% else %}
	            <strong>No pages currently in category.</strong>
	        {% endif %}
	    </body>
	</html>

You can see that we are now introducing ``body_block`` to the template - which overrides the previous definition in our base template. Presently, it overrides with a blank line - but we can easily fix this by moving all the content from within ``category.html``'s ``<body>`` tags to within the ``body_block``, like so:

.. code-block:: html
	
	{% extends 'rango/base.html' %}
	
	{% block body_block %}
	<h1>{{ category_name }}</h1>
	
	{% if pages %}
	    <ul>
	        {% for page in pages %}
	        <li><a href="{{ page.url }}">{{ page.title }}</a></li>
	        {% endfor %}
	    </ul>
	{% else %}
	    <strong>No pages currently in category.</strong>
	{% endif %}
	{% endblock}

After the block ends, remove the remaining HTML content. All that exists within the ``category.html`` template should be the ``extends`` command, and ``body_block`` block. You don't need a well-formatted HTML document, because ``base.html`` provides all the groundwork for you. All you're doing is plugging in additional content to that base to create the complete HTML document which is sent to the client's browser.

Exercises
---------
Update all the other existing templates within Rango's repertoire to extends from our new ``base.html`` template. Follow the exact same process as demonstrated above. Once completed, your templates should all inherit from ``base.html``, as demonstrated in Figure :num:`fig-rango-template-inheritance`.

.. _fig-rango-template-inheritance:

.. figure:: ../images/rango-template-inheritance.pdf
	:figclass: align-center

	A UML class diagram demonstrating how your templates should inherit from ``base.html``.

Templates are very powerful, and you can create your own template tags... see Template Tags Django docs.
They are useful when you have repeated content on each page - so instead of the view passing the data directly to the template, the template can request the additional data it needs.
