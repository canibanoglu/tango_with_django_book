Bootstrapping Rango
===================
In this chapter we will be styling Rango using the Bootstrap 2.3.2 toolkit - we wont be going into the details about how it works and we will be assuming you have some familiarity with CSS. If you don't, check out the CSS chapter so that you understand the basics and then check out some Bootstrap tutorials. However, you should be able to go through this section and piece things together.

To get started take a look at the `Bootstrap 2.3.2 website <http://getbootstrap.com/2.3.2/index.html>`_ - it provides you with sample code and examples of the different components and how to style them by added in the appropriate style tags, etc.

On the Bootstrap website they provide a number of `example layouts <http://getbootstrap.com/2.3.2/getting-started.html#examples>`_ which we can base our design on, see: http://getbootstrap.com/2.3.2/getting-started.html#examples 

To style rango we have identified that the `fluid style <http://getbootstrap.com/2.3.2/examples/fluid.html>`_ more or less meets our needs in terms of the layout of Rango, i.e. it has a menu bar at the top, a side bar (which we will use to show categories) and a main content pane, see: http://getbootstrap.com/2.3.2/examples/fluid.html

Setting up  the Base Template
-----------------------------
Before we can set up the base template to use this style we need to download Bootstrap, JQuery and the Fluid Template.

Download Bootstrap 
..................
Go to the `Bootstrap 2.3.2 website <http://getbootstrap.com/2.3.2/index.html>`_ and download the toolkit. When you unzip the files you will see that in the directory you have a ``imgs``, ``js`` and ``css`` directory plus associated files. Copy these subdirectories in your static folder, so that you will be able to reference them via ``/static/css/bootstrap.css`` for example.

Download JQuery
...............
Now go to the `JQuery website <http://jquery.com>'_ and download the latest version of JQuery. Put the ``js`` file in to the ``static/js/`` directory.

Including CSS/JS in Base Template
.................................
If you download or look at the source for http://getbootstrap.com/2.3.2/examples/fluid.html, you'll notice that in the <head> section there is some additional <style> code. Copy the CSS inside these style tags and create a new CSS file in ``static/css/`` called ``boostrap-fluid-adj .css`` i.e:

.. code-block:: css
	
	
	body {
  		padding-top: 60px;
  		padding-bottom: 40px;
		}
	.sidebar-nav {
  		padding: 9px 0;
	}

	@media (max-width: 980px) {
  	/* Enable use of floated navbar text */
  	.navbar-text.pull-right {
    	float: none;
    	padding-left: 5px;
    	padding-right: 5px;
  		}
		}

By adding it to a file we can minimize the code in our template. Now update the <head> section of ``base.html`` as follows:

.. code-block:: html
	
	<head>
    	<meta name="viewport" content="width=device-width, initial-scale=1.0">
    	<!-- Bootstrap -->
    	<link href="{% static 'css/bootstrap-fluid-adj.css'%} " rel="stylesheet">
    	<link href="{% static 'css/bootstrap.min.css'%} " rel="stylesheet" media="screen">
    	<link href="{% static 'css/bootstrap-responsive.css' %}" rel="stylesheet">
		
    	<title>Rango - {% block title %}How to Tango with Django!{% endblock %}</title>
		</head>
 

Note how we are including all these files through by externally linking them in. We will also need to include the js files. Instead of adding them to the <head> section, we will added them in at the bottom of ``base.html`` just before we close the <body> tag:

.. code-block:: html
	
	<script src="{% static 'js/jquery-2.0.3.min.js' %}"></script>
	<script src="{% static 'js/bootstrap.min.js' %}"></script>
	</body>
	</html>

The reason to add them here is so that the page can load up faster.

Structuring the Base Template
.............................
If you take a close look at the fluid html source, you'll notice it has a lot of structure in it created by a series of <div>s. Essentially the is broken into three parts - the top navigation bar, the main pane (houses the side bar and the main content pane), and a footer. 

In the body of base put in the navigation bar code:

.. code-block:: html
	
	<div class="navbar navbar-inverse navbar-fixed-top">
    <div class="navbar-inner">
        <div class="container">
            <button type="button" class="btn btn-navbar" data-toggle="collapse" data-target=".nav-collapse">
                <span class="icon-bar"></span>
                <span class="icon-bar"></span>
                <span class="icon-bar"></span>
            </button>
            <a class="brand" href="/rango/">Rango</a>

            <div class="nav-collapse collapse">
                <ul class="nav pull-right">
                    {% if user.is_authenticated %}
                    	<li class="navbar-text">Welcome, {{ user.username }}!</li>
                    	<li><a href="/rango/logout/">Logout</a></li>
                    {% else %}
						<li><a href="/rango/register/">Register</a></li>
                    	<li><a href="/rango/login/">Login</a></li>
                    {% endif %}
                </ul>
				<ul class="nav">
                    {% if user.is_authenticated %}
                    	<li><a href="/rango/restricted/">Restricted</a></li>
                    	<li><a href="/rango/add_category/">Add Category</a></li>
                    {% endif %}
                    <li><a href="/rango/about/">About</a></li>
                </ul>
            </div>
            <!--/.nav-collapse -->
        </div>
    </div>
	</div>


After this, you can add in the next <div> which will house the side bar navigation and the main content pane:

.. code-block:: html

	<div class="container-fluid">
    <div class="row-fluid">
        <div class="span3">
            <div class="well sidebar-nav">
             	<!--- Empty for the timebeing -->
			</div>
            <!--/.well -->
        </div>
        <!--/span-->
        <div class="span9">
            {% block body_block %}
            {% endblock %}
        </div>
        <!--/span-->
    </div>
    <!--/row-->
	</div>
	<!--/.fluid-container-->

	<hr>

You can see that we have included the ``body_block`` in here. And now finally, below this add in a footer:

.. code-block:: html

	<footer>
    	<div class="container">
        	<p>&copy; Rango: How to Tango with Django 2013</p>
    	</div>
	</footer>


Quick Style Change
------------------
Now that we have the ``base.html`` all set up and ready to go, we can do a really quick face light to Rango by adding ``<div class="hero-unit">`` around the contents within each ``body_block`` on each page.  For example, convert the body_block of the ``about.html`` template to be:

.. code-block:: html

	{% block body_block %}
    	<div class="hero-unit">
		<h1>About Rango</h1>
		This is <strong>Rango's about page</strong>.<br />
	
		You've visited the site on <strong>{{ visit_count }} occasion(s).</strong><br />
	
		Here's a picture of Rango!<br />
		<img src="{% static "rango.jpg" %}" alt="Picture of Rango" />
			</div>
	{% endblock %}



.. _fig-about-page-before:

.. figure:: ../images/ch4-about.png
	:figclass: align-center

	A screenshot of the About page without style.


.. _fig-about-page-after:

.. figure:: ../images/ch11-bootstrap-about.png
	:figclass: align-center

	A screenshot of the About page with Bootstrap Styling applied.


With all pages fitted with hero unit ``<div>``s Rango you should be looking pretty good. However, you will notice that some of the page still look pretty ugly, especially the pages with forms.


.. _fig-about-page-after:

.. figure:: ../images/ch11-bootstrap-register-initial.png
	:figclass: align-center

	A screenshot of the Registration page with Bootstrap Styling applied but not customised.


Also you'll probably have noticed the sidebar is empty. In the next chapter we will sort that out with some handy navigation links.

Index Page
..........

AF94717Bd19GPOryktdcDKDbiviTZUOfKnY6G1vLyvE

Login Page
..........

Customize the form

http://getbootstrap.com/2.3.2/examples/signin.html


.. code-block:: html

	{% block body_block %}
    <div class="hero-unit">
	<h1>Login to Rango</h1>

    <div class="container">
	<form class="form-signin span4" id="login_form" method="post" action="/rango/login/">
        <h2 class="form-signin-heading">Please sign in</h2>
		{% csrf_token %}

        {% if bad_details %}
			<p><strong>Your username and/or password were incorrect!</strong></p>
		{% elif disabled_account %}
			<p><strong>Your Rango account is currently disabled; we can't log you in!</strong></p>
		{% endif %}
		
		Username: <input type="text" class="input-block-level" placeholder="Username" name="username" value="" size="50" />
		<br />
		Password: <input type="password" class="input-block-level" placeholder="Password" name="password" value="" size="50" />
		<br />
		<button class="btn btn-primary" type="submit">Sign in</button>
	</form>


    </div> <!-- /container -->

	</div>



Other Form based Templates
...........................
To add_category.html and add_page.html apply the following updates:

* Contain the form 
* Add btn and btn-primary class to input
* add span6 class to form
* add breaks before form, after the field text tag, and before the input element to space out the elements.


Register Template
.................








