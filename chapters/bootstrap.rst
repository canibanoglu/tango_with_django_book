Bootstrapping Rango
===================
http://getbootstrap.com/2.3.2/index.html

* Get a feel for the bootstrap toolkit
* Identify a style that suits our needs - http://getbootstrap.com/2.3.2/examples/fluid.html
* Adopt the main layout and set up the Base Template
* Customise each page



Setting up  the Base Template
-----------------------------

Download Bootstrap 
..................

Download JQuery
...............

Including CSS/JS in Base Template
.................................

Structuring the Base Template
.............................


Adding in the menu bar
......................


Hero Templates
..............

Add a hero unit div around the content of each page.

For example, the body_blocks for about should look like:

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


With all pages fitted with hero unit div's you app you should be looking pretty good. However, when you look through the pages some of the forms look pretty ugly and the index page isn't looking that great either.

You'll also notice we have a sidebar that is empty - we'll keep it that way for now, but in the following chapters we'll add in some navigation elements.


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








