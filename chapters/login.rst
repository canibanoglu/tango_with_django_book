User Authentication
===================
The aim of this next part of the tutorial is to get you familiar with the user authentication mechanisms provided by Django. We'll by using the ``auth`` application provided as part of a standard Django installation in package ``django.contrib.auth``. According to `Django's official documentation <https://docs.djangoproject.com/en/1.5/topics/auth/>`_, the application consists of the following aspects.

- Users.
- Permissions: a series of binary flags (e.g. yes/no) determining what a user may or may not do.
- Groups: a method of applying permissions to more than one user.
- A configurable password hashing system: a must for ensuring data security.
- Forms and view tools for logging in users, or restricting content.
- A pluggable backend system.

There's lots that Django can do for you in the area of user authentication. We'll be covering the basics, but you can of course check out the `Django website <https://docs.djangoproject.com/en/1.5/topics/auth/>`_ for more advanced examples - and elsewhere online. Remember, your search engine is your friend!

Setting up Authentication
-------------------------
Before we can begin to play around with Django's authentication offering, we'll need to make sure that the relevant settings are present in your Rango project's ``settings.py`` file. Open the file - remember, this is located in your Django project's configuration directory (e.g. ``<workspace>/tango_with_django_project/tango_with_django_project``).

Within the ``settings.py`` file, you must first find the ``INSTALLED_APPS`` tuple and check that ``django.contrib.auth`` and ``django.contrib.contenttypes`` are listed within the tuple. As an example, ``INSTALLED_APPS`` should look something like the following example.

.. code-block:: python
	
	INSTALLED_APPS = (
	    'django.contrib.auth', # THIS LINE SHOULD BE PRESENT AND UNCOMMENTED
	    'django.contrib.contenttypes', # THIS LINE SHOULD BE PRESENT AND UNCOMMENTED
	    'django.contrib.sessions',
	    'django.contrib.sites',
	    'django.contrib.messages',
	    'django.contrib.staticfiles',
	    # Uncomment the next line to enable the admin:
	    'django.contrib.admin',
	    # Uncomment the next line to enable admin documentation:
	    # 'django.contrib.admindocs',
	    'rango',
	)

While ``django.contrib.auth`` provides Django with access to the authentication system, ``django.contrib.contenttypes`` is used by the authentication application to track models installed in your database. You can read more about the application on `Django's official documentation <https://docs.djangoproject.com/en/1.5/ref/contrib/contenttypes/>`_ if you're interested.

.. warning:: Remember, if you had to add either one of the ``auth`` or ``contenttypes`` applications to your ``INSTALLED_APPS`` tuple, you'll need to resynchronise your database with the ``$ python manage.py syncdb`` command.

Passwords are stored by default in Django using the `PBKDF2 algorithm <http://en.wikipedia.org/wiki/PBKDF2>`_, providing a good level of security for your user's data. You can read more about this as part of the `official Django documentation <https://docs.djangoproject.com/en/1.5/topics/auth/passwords/#how-django-stores-passwords>`_. The documentation also provides an explanation of how to use different password hashers if you require a greater level of security. With these new settings saved, you should now be ready to move on and begin experimenting with Django's authentication system.

.. note:: If you're interested in further detail about how Django's password hashing system works, check out `this awesome explanation <http://www.levigross.com/post/18880148948/a-review-of-djangos-new-password-authentication>`_.

The User Model: Understanding and Expanding
-------------------------------------------
The core of Django's authentication system is the ``User`` object, located at ``django.contrib.auth.models.User``. A ``User`` object represents each of the people interacting with a Django application. The `Django documentation <https://docs.djangoproject.com/en/1.5/topics/auth/default/#user-objects>`_ states that they are used to allow aspects of the authentication system like access restriction, registration of new user profiles and the association of creators with site content.

The ``User`` model comes complete with five primary attributes. They are:

- the username for the user account;
- the account's password;
- the user's email address;
- the user's first name; and
- the user's surname.

The model also comes with other attributes such as ``is_active`` (which determines whether a particular account is active or not). Check the `official Django documentation <https://docs.djangoproject.com/en/1.5/ref/contrib/auth/#django.contrib.auth.models.User>`_ for a full list of attributes provided by the base ``User`` model.

However, what if all the provided attributes that the ``User`` model provides isn't enough? For our Rango application, we want to include two more additional attributes for each user account. Specifically, we wish to include:

- a ``URLField``, allowing a user of Rango to specify their own website; and
- a ``ImageField``, which allows users to specify a picture for their user profile.

Fortunately, this is a relatively easy task to accomplish. This is achieved through the creation of an additional model in Rango's ``models.py`` file. Let's add the new model - add the following code.

.. code-block:: python
	
	class UserProfile(models.Model):
	    # This line is required. Links UserProfile to a User model instance.
	    user = models.OneToOneField(User)
	    
	    # The additional attributes we wish to include.
	    website = models.URLField(blank=True)
	    picture = models.ImageField(upload_to='profile_images', blank=True)
	    
	    # Override the __unicode__() method to return out something meaningful!
	    def __unicode__(self):
	        return self.user.username

As we also reference the ``User`` model, we'll need to include the model into the ``models.py`` namespace. Add it with the following import statement at the top of the Python file.

.. code-block:: python
	
	from django.contrib.auth.models import User

So, how do we accomplish our goal of adding additional user profile attributes? This isn't actually achieved with class inheritance as some may think. Our ``UserProfile`` actually inherits from Django's bog-standard ``Model`` class. Instead, we `link to the base <http://stackoverflow.com/a/3221933>`_ ``User`` class through a one-to-one relationship via attribute ``user``. We also add our two additional attributes to complete our user profile, and provide a ``__unicode__()`` method to return a meaningful value when a unicode representation of a ``UserProfile`` model instance is requested.

For our two additional attributes, note how we set ``blank=True`` for both. This allows each of the fields to be blank if necessary, meaning that users need not supply values for the attributes if they do not wish to. The ``ImageField`` field also has a ``upload_to`` attribute. The value of this attribute is conjoined with the project's ``MEDIA_ROOT`` setting to provide a path with which uploaded profile images will be stored. For example, a ``MEDIA_ROOT`` of ``<workspace>/tango_with_django_project/media/`` and ``upload_to`` attribute of ``profile_images`` will result in all profile images being stored in ``<workspace>/tango_with_django_project/media/profile_images/``.

With our ``UserProfile`` model defined, we now edit Rango's ``admin.py`` file to include the new ``UserProfile`` model in the Django administration web interface. In the ``admin.py`` file, add the following line.

.. code-block:: python
	
	admin.site.register(UserProfile)

You also need to import the ``UserProfile`` model by adding one of the following lines at the top of the ``admin.py`` file.

.. code-block:: python
	
	# Import the UserProfile model individually
	from rango.models import UserProfile
	
	# Import the UserProfile model with Category and Page
	from rango.models import Category, Page, UserProfile

.. warning:: Remember that with the creation of a new model, you much synchronise your database. Run the ``$ python manage.py syncb`` command to synchronise the new ``UserProfile`` model. Not doing so will result in errors explaining that the required database tables cannot be found.

Creating a User Registration View and Template
----------------------------------------------
With our authentication infrastructure laid out, we can now begin to build onto it by providing users of our application with the opportunity to create new user accounts. We will achieve this via the creation of a new view and template combination.

.. note:: We feel it's important to note that there are several off-the-shelf user registration packages available for you to download and use in your Django projects. Examples include the `Django Registration application <https://bitbucket.org/ubernostrum/django-registration/>`_, and you can also check out the table on `this webpage <https://www.djangopackages.com/grids/g/registration/>`_ which lists other registration packages. While these exist, we'll be showing you how to set up everything from scratch. This is at odds from the DRY principle, but we feel this is important as doing everything from scratch will allow you to familiarise yourself with the handling of user registrations.

To set everything up, we'll be making use of Django's form functionality once more. Specifically, we'll be looking at how to go about the following five steps.

#. The first step is to create a ``UserForm`` and ``UserProfileForm``.
#. We'll then add a view to handle the creation of a new user.
#. A template will then be created that displays the ``UserForm`` and ``UserProfileForm``.
#. A URL mapping will be added to Rango's ``urls.py`` file.
#. Finally, we'll pop a link into our ``index.html`` template to allow users to register.

Now we're all set to get started! Let's create our ``ModelForms`` first. In Rango's ``models.py`` file, add the following classes.

.. code-block:: python
	
	class UserForm(forms.ModelForm):
	    password = forms.CharField(widget=forms.PasswordInput())
	    
	    class Meta:
	        model = User
	        fields = ['username', 'email', 'password']

	class UserProfileForm(forms.ModelForm):
	    class Meta:
	        model = UserProfile
	        fields = ['website', 'picture']

We add **two** classes: one representing an input form for a ``User`` model instance, the other for a ``UserProfile`` model instance. Recall how additional fields were combined with the base ``User`` model - not with inheritance, but by linking the two models together with a one-to-one relationship, hence the need for two form representations.

You should be able to recall from the previous chapter what each class does that inherits from ``ModelForm`` - including the nested ``Meta`` class. ``Meta`` attribute ``model`` associated the ``ModelForm`` to a Django model, and ``fields`` dictates what fields users should be able to provide values for in the rendered form. Within ``UserForm``, we also adjust the widget that is provided as the means for input of the ``password`` attribute. This is changed to a ``PasswordInput`` widget, which hides a user's input when he or she types into the field.

With our ``ModelView`` representations adds, we can then move on to creating the view handling registration of a new user. Like our ``add_category()`` view defined in the previous chapter, our new view - ``register()`` - will handle both the rendering of the form, and the processing of form input data. Within Rango's ``views.py`` file, add the following function. Check out the inline commentary for an explanation of what each line does.

.. code-block:: python
	
	def register(request):
	    # Like before, get the request's context.
	    context = RequestContext(request)
	    
	    # A boolean value for telling the template whether the registration was successful.
	    # Set to False initially. Code changes value to True when registration succeeds.
	    registered = False
	    
	    # If it's a HTTP POST, we're interested in processing form data.
	    if request.method == 'POST':
	        # Attempt to grab information from the raw form information.
	        # Note that we make use of both UserForm and UserProfileForm.
	        user_form = UserForm(data=request.POST)
	        profile_form = UserProfileForm(data=request.POST)
	        
	        # If the two forms are valid...
	        if user_form.is_valid() and profile_form.is_valid():
	            # Save the user's form data to the database.
	            user = user_form.save()
	            
	            # Now we hash the password with the set_password method.
	            # Once hashed, we can update the user object.
	            user.set_password(user.password)
	            user.save()
	            
	            # Now sort out the UserProfile instance.
	            # Since we need to set the user attribute ourselves, we set commit=False.
	            # This delays saving the model until we're ready to avoid integrity problems.
	            profile = profile_form.save(commit=False)
	            profile.user = user
	            
	            # Did the user provide a profile picture?
	            # If so, we need to get it from the input form and put it in the UserProfile model.
	            if 'picture' in request.FILE:
	                profile.picture = request.FILES['picture']
	            
	            # Now we save the UserProfile model instance.
	            profile.save()
	            
	            # Update our variable to tell the template registration was successful.
	            registered = True
	        
	        # Invalid form or forms - mistakes or something else?
	        # Print problems to the terminal.
	        # They'll also be shown to the user.
	        else:
	            print user_form.errors, profile_form.errors
	    
	    # Not a HTTP POST, so we render our form using two ModelForm instances.
	    # These forms will be blank, ready for user input.
	    else:
	        user_form = UserForm()
	        profile_form = UserProfileForm()
	    
	    # Render the template depending on the context.
	    return render_to_response(
	            'rango/register.html',
	            {'user_form': user_form, 'profile_form': profile_form, 'registered': registered},
	            context)

As we reference our new ``ModelForm`` classes, we'll also need to import them at the top of ``views.py``. Add the following at the top of the file:

.. code-block:: python
	
	from rango.models import UserForm, UserProfileForm

Is the view a lot more complex? Not really. The only added complexity from our previous ``add_category()`` view is the need to handle two distinct ``ModelForm`` instances - one for the ``User`` model, and one for the ``UserProfile`` model. We also need to handle a user's profile image, if he or she chooses to upload one. We must also establish a link between the two model instances that we create. After creating a new ``User`` model instance, we reference it in the ``UserProfile`` instance with the line ``profile.user = user``.

Now create a new template file. You can work out its name from the ``register()`` view code sample provided above. In your new template, add the following markup.

.. code-block:: html
	
	<!DOCTYPE html>
	<html>
	    <head>
	        <title>Rango</title>
	    </head>

	    <body>
	        <h1>Register with Rango</h1>

	        {% if registered %}
	        <strong><a href="/rango/">Rango</a> says:</strong> thank you for registering!
	        Click <a href="/rango/" >here</a> to go to the homepage.<br />
	        {% else %}
	        <strong><a href="/rango/">Rango</a> says:</strong> register here!<br />

	        <form id="user_form" method="post" action="/rango/register/"
	                enctype="multipart/form-data">

	            {% csrf_token %}
	            
	            <!-- Display each form. The as.p method wraps each element in a paragraph
	                 (<p>) element. This ensures each element appears on a new line,
	                 making everything look neater. -->
	            {{ user_form.as_p }}
	            {{ profile_form.as_p }}

	            <input type="submit" name="submit" value="Register">
	        </form>
	        {% endif %}

	    </body>
	</html>

Our HTML template makes use of the ``register`` variable we used in our view indicating whether registration was successful or not. Note that ``registered`` must be ``False`` in order for the template to display the registration form - otherwise, apart from the title, only a success message is displayed.

.. warning:: When using forms, don't forget to include your ``{% csrf_token %}``. You'll save yourself a world of issues if you remember to include it. You should also be aware of the ``enctype`` attribute for the ``<form>`` element. When you wish to provide functionality for users to upload files, you **need** to set ``enctype`` to ``multipart/form-data``. This is due to the need for your browser needing to encode data in a particular way. A more detailed explanation can be seen `here <http://stackoverflow.com/a/4526286>`_.

Now we can add a URL mapping to our new view. In Rango's ``urls.py`` file, modify the ``urlpatterns`` tuple to look like the sample below. Pay attention to the ordering in which the patterns appear.

.. code-block:: python
	
	urlpatterns = patterns('',
	    url(r'^$', views.index, name='index'),
	    url(r'^add_category/$', views.add_category, name='add_category'),
	    url(r'^register/$', views.register, name='register'), # NEW PATTERN HERE
	    url(r'^(?P<category_name_url>\w+)', views.category, name='category'),)

Our new pattern now points the URL ``/rango/register/`` to our new ``register()`` view. Now that we know this, we can add a link pointing to that URL in our homepage ``index.html`` template. Underneath the link to the category addition page, add the following hyperlink.

.. code-block:: html
	
	<a href="/rango/register/">Register Here</a>

Easy! Now you'll have a new hyperlink with the text ``Register Here`` that'll take you to the registration page. Try it out now - you're ready to go! Just start your Django development server, go to the Rango homepage, and try and register a new user account. Upload a profile image if you wish. Your registration form should look like the one illustrated in Figure :num:`fig-rango-register-form`.

.. _fig-rango-register-form:

.. figure:: ../images/rango-register-form.png
	:figclass: align-center

	A screenshot illustrating the basic registration form you create as part of this tutorial.

Upon seeing the message indicating your details were successfully registered, the database should have two new entries in its tables corresponding to the ``User`` and ``UserProfile`` models. Your question now may be how to use that data. Read on to see!

Adding Login Functionality
--------------------------
With the ability to register accounts completed, the next logical step would be to provide functionality to allows users to enter their credentails, allowing them to login to the application. Here, we'll have a look at how to do use Django's built-in machinery to achieve this. We'll be creating a new view, matching template and url mapping just like before. The new view will contain the logic behind logging in.

To start, open Rango's ``views.py`` file and create a new function, ``user_login()``. Check out the code sample below. Take note of the inline commentary to get an understanding of what's going on.

.. code-block:: python
	
	def user_login(request):
	    # Like before, obtain the context for the user's request.
	    context = RequestContext(request)
	    
	    # If the request is a HTTP POST, try to pull out the relevant information.
	    if request.method == 'POST':
	        # Gather the username and password provided by the user.
	        # This information is obtained from the login form.
	        username = request.POST['username']
	        password = request.POST['password']
	        
	        # Use Django's machinery to attempt to see if the username/password
	        # combination is valid - a User object is returned if it is.
	        user = authenticate(username=username, password=password)
	        
	        # If we have a User object, the details are correct.
	        # If None (Python's way of representing the absence of a value), no user
	        # with matching credentials was found.
	        if user is not None:
	            # Is the account active? It could have been disabled.
	            if user.is_active:
	                # If the account is valid and active, we can log the user in.
	                # We'll send the user back to the homepage.
	                login(request, user)
	                return HttpResponseRedirect('/rango/')
	            else:
	                # An inactive account was used - no logging in!
	                return HttpResponse("Your Rango account is disabled.")
	        else:
	            # Bad login details were provided. So we can't log the user in.
	            print "Invalid login details: {0}, {1}".format(username, password)
	            return HttpResponse("Invalid login details supplied.")
	    
	    # The request is not a HTTP POST, so display the login form.
	    # This scenario would most likely be a HTTP GET.
	    else:
	        # No context variables to pass to the template system, hence the
	        # blank dictionary object...
	        return render_to_response('rango/login.html', {}, context)

Note that, like previous examples before, the ``user_login()`` view again handles form rendering and processing. If a valid form is sent, the username and password are extracted from the form. These details are then used to attempt to authenticate the user (with Django's ``authenticate()`` function). ``authenticate()`` then returns a ``User`` object if the username/password combination exists within the database - or ``None`` if no match was found. If we retrieve a ``User`` object, we can then check if the account is active or inactive - and return the appropriate response to the client's browser.

Of particular interest in the code sample above is the use of the built-in Django machinery to help with the authentication process. Note the use of the ``authenticate()`` function to check whether the username and password provided match to a valid user account, and the ``login()`` function to actually log the user in. You'll also notice that we make use of a new class, ``HttpResponseRedirect``. As the name may suggest to you, the response generated by an instance of the ``HttpResponseRedirect`` class tells the client's browser to redirect to the URL you provide as the only argument. As you can read in the `official Django documentation <https://docs.djangoproject.com/en/1.5/ref/request-response/#django.http.HttpResponseRedirect>`_, the returned HTTP status code is `302 <http://en.wikipedia.org/wiki/HTTP_302>`_ - the de facto way to tell a browser to redirect.

All of these functions and classes are provided by Django, and as such you'll need to import them. Don't forget to include the following import statements at the top of Rango's ``views.py`` file.

.. code-block:: python
	
	from django.contrib.auth import authenticate, login
	from django.http import HttpResponseRedirect

With our new view created, we'll need to create a new template allowing users to login. While we know that the template will live in the ``templates/rango/`` directory, we'll leave you to figure out the name. Look at the code example above to work out the name. In your new template file, add the following markup.

.. code-block:: html
	
	<!DOCTYPE html>
	<html>
	    <head>
	        <title>Rango</title>
	    </head>

	    <body>
	        <h1>Login to Rango</h1>

	        <form id="login_form" method="post" action="/rango/login/">
	            {% csrf_token %}
	            Username: <input type="text" name="username" value="" size="50" />
	            <br />
	            Password: <input type="password" name="password" value="" size="50" />
	            <br />

	            <input type="submit" value="submit" />
	        </form>

	    </body>
	</html>

Ensure that you match up the input ``name`` attributes to those that you specified in the ``user_login()`` view - i.e. ``username`` for the username, and ``password`` for password. Don't forget the ``{% csrf_token %}``, either!

With your login template created, we can now match up the ``user_login()`` view to a URL. Modify Rango's ``urls.py`` file so that its ``urlpatterns`` tuple now looks like the example below. Remember, take care with the order in which patterns appear - you want the category viewing pattern to appear last. In the example below, our new login pattern is placed in the tuple's penultimate position.

.. code-block:: python
	
	urlpatterns = patterns('',
	    url(r'^$', views.index, name='index'),
	    url(r'^add_category/$', views.add_category, name='add_category'),
	    url(r'^register/$', views.register, name='register'),
	    url(r'^login/$', views.user_login, name='login'), # THIS IS OUR NEW ENTRY
	    url(r'^(?P<category_name_url>\w+)', views.category, name='category'),)

Our final step is to provide users of Rango with a handy link to access the login page. To do this, we'll edit the ``index.html`` template inside of the ``templates/rango/`` directory. Find the previously created category addition and registration links, and add the following hyperlink underneath.

.. code-block:: python
	
	<a href="/rango/login/">Login</a>

If you like, you can also modify the header of the index page to provide a personalised message if a user is logged in, and a more generic message if the user isn't. Within the ``index.html`` template, find the header, as shown in the code snippet below.

.. code-block:: python
	
	<h1>Rango says..hello world!</h1>

Replace this header with the following markup and Django template code. Note that we make use of the ``user`` object, which is available to Django's template system via the context. We can tell from this object if the user is logged in (authenticated). If he or she is logged in, we can also obtain details about him or her.

.. code-block:: python
	
	{% if user.is_authenticated %}
	<h1>Rango says..hello {{ user.username }}!</h1>
	{% else %}
	<h1>Rango says..hello world!</h1>
	{% endif %}

From the example above, we present the header ``Rango says..hello jon!`` if the user is logged in, and the username is ``jon``. Otherwise, the generic ``Rango says..hello world!`` header is displayed if the user is not logged in. Simple, but a nice touch. Check out Figure :num:`fig-rango-login-message` for screenshots of what everything should look like.

.. _fig-rango-login-message:

.. figure:: ../images/rango-login-message.png
	:figclass: align-center

	Screenshots illustrating the header users receive when not logged in, and logged in with username ``somebody``.

With this completed, user logins should now be completed! To test everything out, try starting Django's development server and attempt to register a new account. After successful registration, you should then be able to login with the details you just provided, which will take you back to the login page.

Restricting Access
..................
Now that users can login to Rango, a next logical step would be to figure out how to create views which only logged in users can access. With Django, there are two ways in which we can achieve this goal:

* directly, by examining the ``request`` object; or
* using a convenience *decorator* function.

The direct approach checks to see whether a user is logged in, via the ``user.is_authenticated()`` method. The ``user`` object is available via the ``request`` object passed into a view. The following example view demonstrates the approach in action, which is pretty neat and easy to understand. You can clearly see how to generate different responses depending on the user's context.

.. code-block:: python
	
	def some_view(request):
	    if not request.user.is_authenticated():
	        return HttpResponse("You are logged in.")
	    else:
	        return HttpResponse("You are not logged in.")

The second approach uses `Python decorators <http://wiki.python.org/moin/PythonDecorators>`_. Decorators are named after a `software design pattern by the same name <http://en.wikipedia.org/wiki/Decorator_pattern>`_. They can dynamically alter the functionality of a function, method or class without having to directly edit the source code of the given function, method or class.

Django comes with several decorators predefined for us - and luckily, one of them concerns whether a user is logged in or not. It's called ``login_required()``, and it's a cinch to put into your application's views. If a view is decorated with ``login_required()``, the view is only executed if a user has been previously logged in. If this is not the case, the user is presented with a message stating they do not have permission to access the given view. To check out this approach, create a new view in Rango's ``views.py`` file, called ``restricted()``. Check out the code example below to see how it all fits together.

.. code-block:: python
	
	@login_required
	def restricted(request):
	    return HttpResponse("Since you're logged in, you can see this text!")

Note that to use a decorator, you place it *directly above* the function signature, and put a ``@`` before naming the decorator. That's all! Python will execute the decorator before executing the code of your function/method. Oh, and you'll also need to import the ``login_required()`` decorator using a standard import statement, as shown below.

.. code-block:: python
	
	from django.contrib.auth.decorators import login_required

We'll also add in another pattern to Rango's ``urlpatterns`` tuple in the ``urls.py`` file. Our tuple should then look something like the following example. Note the inclusion of mapping of the ``views.restricted`` view - this is the mapping you need to add.

.. code-block:: python
	
	urlpatterns = patterns('',
	    url(r'^$', views.index, name='index'),
	    url(r'^add_category/$', views.add_category, name='add_category'),
	    url(r'^register/$', views.register, name='register'),
	    url(r'^login/$', views.user_login, name='login'),
	    url(r'^restricted/', views.restricted, name='restricted'),
	    url(r'^(?P<category_name_url>\w+)', views.category, name='category'),)

We'll also need to handle the scenario where a user attempts to access the ``restricted()`` view, but is not logged in. Where do we take the user? Django allows us to specify this in our project's ``settings.py`` file, located in the project configuration directory. In ``settings.py``, define the variable ``LOGIN_URL`` with the URL you'd like to redirect users to that aren't logged in. In our scenario, it would make sense to take them to the login page, located at ``/rango/login/`` - hence the following code.

.. code-block:: python
	
	LOGIN_URL = '/rango/login/'

This ensures that the ``login_required()`` decorator will redirect any user not logged in to the URL ``/rango/login/``.

From here, you can take the basic approaches discussed and extend them to consider ever more complex situations. For example, you may want a view that which only administrators of a system can access. Instead of simply determining if a user is logged in or not, you would also want to check if they are part of the administrators group before you grant him or her access. The possibilities are endless!

Logging Out
-----------
Once a user logs in, it should be expected that he or she can logout of the application too. Django comes with a handy ``logout()`` function that can take care of that for us. To demonstrate the function in action, we're going to create a final view allowing a user to logout - and also make use of decorators that we discussed in the previous section.

To start, create a new view in Rango's ``views.py`` file, and call it ``user_logout()``. Check out the code sample below. There's also inline commentary explaining each line.

.. code-block:: python
	
	# Use the login_required() decorator to ensure only those logged in can access the view.
	@login_required
	def user_logout(request):
	    # Like before, obtain the request's context.
	    context = RequestContext(request)
	    
	    # Since we know the user is logged in, we can now just log them out.
	    logout(request)
	    
	    # Take the user back to the homepage.
	    return HttpResponseRedirect('/rango/')


As the ``logout()`` function is part of Django's machinery, we'll need to include it - you can find it in the same module as the ``login()`` function we included earlier.

.. code-block:: python
	
	from django.contrib.auth import logout

With our view created, we now map the ``/rango/logout/`` URL to the ``user_logout()`` view by modifying the ``urlpatterns`` tuple in Rango's ``urls.py`` module. Change it to look like the following example. Again, be wary of the order in which each pattern appears!

.. code-block:: python
	
	urlpatterns = patterns('',
	    url(r'^$', views.index, name='index'),
	    url(r'^add_category/$', views.add_category, name='add_category'),
	    url(r'^register/$', views.register, name='register'),
	    url(r'^login/$', views.user_login, name='login'),
	    url(r'^logout/$', views.user_logout, name='logout'), # OUR NEW MAPPING
	    url(r'^restricted/', views.restricted, name='restricted'),
	    url(r'^(?P<category_name_url>\w+)', views.category, name='category'),)

Now that all the machinery for logging a user out has been completed, it'd be handy to provide a link from the homepage to allow users to simply click a link to logout. However, let's be smart about this: is there any point providing the logout link to a user who isn't logged in? Perhaps not - it may be more beneficial for a user who isn't logged in to be given the chance to register, for example.

Like in the previous section, we'll be modifying Rango's ``index.html`` template, and making use of the ``user`` object in the template's context to determine what links we want to show. Find your growing list of links at the bottom of the page, and put this markup in.

.. code-block:: html
	
	{% if user.is_authenticated %}
	<a href="/rango/restricted/">Restricted Page</a><br />
	<a href="/rango/logout/">Logout</a>
	{% else %}
	<a href="/rango/register/">Register</a><br />
	<a href="/rango/login/">Login</a>
	{% endif %}

Simple - when a user is logged in/authenticated, he or she is presented with links to the restricted page and the logout view. If he or she isn't, they are presented with links to either register or login. You will want to alter your previous list of links, removing the login and register links you had. This will help to prevent any confusion.

In this part of the tutorial, we've only begin to scratch the surface of some the user authentication features that Django has to offer. We've looked at registering new users, how to log them in and out, and provide restricted access content. It's definitely a good idea to read into user authentication in more detail. Check out `Django's official documentation <https://docs.djangoproject.com/en/1.5/topics/auth/>`_ for many more helpful functions and helper templates/views to handle common aspects of user authentication and registration.

Basic Workflow
--------------
This chapter has covered several important aspects of managing user authentication within Django. We've covered the basics of installing Django's ``django.contrib.auth`` application into our project. Additionally, we have also shown how to implement a user profile model that can provide additional fields to the base ``django.contrib.auth.models.User`` model. In most scenarios, you will more than likely be required to do this.

We have also shown how to setup functionality for allowing user registrations, logins, logouts and support for checking if a user can access a particular view. Using Django's form functionality, user registration can be provided in the following steps.

#. First, a form should be created which maps to our ``User`` model - and if we create a secondary ``User`` model with additional fields, a form should be created to correspond to that, too.
#. We then create a view which corresponds to our one or two ``User`` form classes.
#. A template can be provided which helps describe how the forms should be displayed to the user.
#. A URL is then mapped to the view.

User logins also make use of forms - though the number details required is usually very small (only username and password). As such, we can create a simple HTML form within a login page template, without the need to use Django's form functionality.

#. Create a login view, ensuring that the view can process both HTTP ``GET`` and ``POST`` requests. For ``GET`` requests, the login form should be displayed to the user. For ``POST`` requests, the view should attempt to process login information provided by the user via the form.
#. Produce a template which displays a login form, containing a username and password input box. The form should also contain a login button to submit the form to the *same URL*, but using a HTTP ``POST`` request.
#. Map the login view to a URL.

Exercises (LEIF TODO?)
----------------------

	* Customize the application so that only registered users can add/edit, while non-registered can only view/use the categories/pages
	* Handle lost passwords by adding a form that emails the password to the user
	
	* How can you make the error messages prettier?
	
	* Note the limitation on using the basic Forms framework - there's no password check field like in nearly all websites. Can you figure our a way to include one?
	
	* Another way of handling user registration is use the ``django-registration`` package. Check out ``https://django-registration.readthedocs.org/en/latest/``
