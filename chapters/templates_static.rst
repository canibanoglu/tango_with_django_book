.. _templates-label:

Templates and Static Media
==========================
In this chapter, we'll be extending your knowledge of Django by introducing you to the template engine as well as how to server and *static media* within your web pages. 

.. _model-setup-templates-label:

Using Templates
---------------
Up to this point, you have plugged a few things together to create a Django-powered webpage that coupled a View with a series of URL mappings. Here we will delve into how to combine Templates into the mix.

Well-designed websites use a lot of repetition in their structure or layout. Whether you see a common header or footer on a website's pages, the `repetition of page layouts <http://www.techrepublic.com/blog/web-designer/effective-design-principles-for-web-designers-repetition/>`_ aids users with navigation, promote organisation of the website and reinforce continuity. Django provides `templates  <https://docs.djangoproject.com/en/1.5/ref/templates/>`_ to make it easier for developers to achieve this design goal, as well as separating application logic from presentation concerns. In this example, you will create a basic template which will be used to create a HTML page, which will be dispatch via the view. Later on in this tutorial, we will use templates in conjunction with models to dispatch dynamically generated data.


Configuring the Templates Directory
...................................
To get templates up and running, you will need to set up a directory in which template files are stored. 

In your Django project's directory (e.g. ``<workspace>/tango_with_django_project/``), create a new directory called ``templates``. 

Within the new templates directory, create another directory called ``rango``. In ``templates/rango/`` we will be storing templates associated with the rango application.

To tell your Django project where the templates will be housed, open the ``settings.py`` file. Find the tuple ``TEMPLATE_DIRS``, and add in the path to your newly created ``templates`` directory, so it looks like:

.. code-block:: python
	
	TEMPLATE_DIRS = (
	    # Put strings here, like "/home/html/django_templates" or "C:/www/django/templates".
	    # Always use forward slashes, even on Windows.
	    # Don't forget to use absolute paths, not relative paths.
	    '<workspace>/tango_with_django_project/templates/',
	)

Note that you are required to use absolute paths to the ``templates`` directory. Now if you are part of a team or working on different systems, this is going to become a problem in the future because you all wont have the same <workspace> directory. Of course, you could add in the template directory for each different setup, but that would be pretty nasty.

.. warning::
	The road to hell is paved with `hard-code <http://en.wikipedia.org/wiki/Hard_coding>` paths. 
 	`Hard-coding <http://en.wikipedia.org/wiki/Hard_coding#Overview>`_ paths is considered to be an `anti-pattern <http://sourcemaking.com/antipatterns>`_, and will make your project less `portable <http://en.wikipedia.org/wiki/Software_portability>`_.


Instead, we can be much smarter and make use of some built-in Python functions to get the absolute path, regardless of where the code is stored. At the expense of a little bit more complexity in setting up our Django project, we can make our lives much easier later on when it comes to setting up and running our project on other machines.

Modify the ``settings.py`` file to include a variable called, ``PROJECT_PATH`` which will stored the absolute path of the project by asking the Operating System. At the top of ``settings.py``, add the following code.

.. code-block:: python
	
	import os
	
	PROJECT_PATH = os.getcwd()

This code asks for the path of the current working directory i.e. where you launched ``python manage.py runserver`` from and assigned this to ``PROJECT_PATH``. 


With the absolute to our project's root directory now stored in ``PROJECT_PATH``, we can then join the path together with a further directory or filename to obtain a complete path to our project's filesystem resources.
Create a templates path and it to settings as follows: 

.. code-block:: python
	
	TEMPLATE_PATH = os.path.join(PROJECT_PATH, 'templates')

Here, we use the ``os.path.join`` command to let Python handle the join, instead of making sure we have the right slashes depending on our OS. Now in the ``TEMPLATES_DIR`` tuple, change the code to:
 	

.. code-block:: python
	
	TEMPLATE_DIRS = (
	    # Put strings here, like "/home/html/django_templates" or "C:/www/django/templates".
	    # Always use forward slashes, even on Windows.
	    # Don't forget to use absolute paths, not relative paths.
	    TEMPLATE_PATH,
	)



Adding a Template
.................

With your template path set up create a file called ``index.html`` and place it in the ``templates/rango/`` directory. Within this new file, add the following HTML code:

.. code-block:: html
	
	<!DOCTYPE html>
	<html>
	
	    <head>
	        <title>Rango</title>
	    </head>
	    
	    <body>
	        <h1>Rango says...</h1>
	        hello world! <strong>{{ boldmessage }}</strong>
	    </body>
	
	</html>

From this HTML code you should it should be clear that a simple HTML page is going to be generated that says Hello world. You might also notice a non-HTML element ``{{ boldmessage }}``, this is a Django Template variable that will we be able to set in our view (more on this in a moment).

To use this template, we need to re-configure the ``index`` view that we created earlier - and get the view to dispatch the template.

In ``rango/views.py`` add the following import statements at the top of the file.

.. code-block:: python
	
	from django.template import RequestContext
	from django.shortcuts import render_to_response

Then update the ``index(request)`` view function as follows:

.. code-block:: python
	
	def index(request):
	    # Request the context of the request.
	    # The context contains information such as the client's machine details, for example.
	    context = RequestContext(request)
	    
	    # Construct a dictionary to pass to the template engine as its context.
	    # Note the key boldmessage is the same as {{ boldmessage }} in the template!
	    context_dict = {'boldmessage': "I am bold font from the context"}
	    
	    # Return a rendered response to send to the client.
	    # We make use of the shortcut function to make our lives easier.
	    # Note that the first parameter is the template we wish to use.
	    return render_to_response('rango/index.html', context_dict, context)

In the view, we use the RequestContext helper function to get access to Django specific values, then we create a dictionary to store any data we want to send through to the template, then finally we call ``render_to_response``  passing through the dictionary and the context to the specified template. The ``render_to_response`` function will take this data and mash it together with the template to produce a HTML page, in this case, and then return it as a HttpResponse.

When a template file is loaded with the Django templating system, a context is created. In simple terms, a template context is essentially a Python dictionary that maps template variable names with Python variables. In our template above, we have a template variable name called ``boldmessage``. In our ``index(request)`` view example, the string ``I am bold font from the context`` is mapped to template variable ``boldmessage``. The string ``I am bold font from the context`` therefore replaces any instance of ``{{ boldmessage }}`` within the template.

Now that you have updated the view to employ the use of your template, run the Django development server and visit http://127.0.0.1:8000/rango/. You should see your template rendered in all its glory, just like the example shown in Figure :num:`fig-rango-hello-world-template`. 

If you don't, read the error message presented to see what the problem is, and then double check all the changes that you have made, and that all the changes required have been made. One of the most common issue people have with templates is that the path is set incorrectly in ``settings.py``. Sometimes it is worth adding a print statement to ``settings.py`` to report the ``PROJECT_PATH`` and ``TEMPLATE_PATH``.

.. _fig-rango-hello-world-template:

.. figure:: ../images/rango-hello-world-template.png
	:figclass: align-center

	A screenshot of Google Chrome rendering the template used with this tutorial.

This example demonstrates how to use templates within your views. However, we have only touched upon some of the functionality provided by Django regarding templates. We will use templates in more sophisticated ways as we progress through this tutorial, but in the meantime you can find out more about `templates from the 
 official Django documentation <https://docs.djangoproject.com/en/1.5/ref/templates/>`_. Next, we will set up our Django Project to be able to serve up static media, such as images, css and javascript.


Serving Static Media
--------------------
Admittedly, the *Rango* website that we've been building thus far isn't looking too fancy. It's plain, there's no styling or anything eye-catching. In the real world, people wouldn't give it a second glance.

One way with which we can spruce up a website is to include other types of media in its webpages such as image content. It would also be beneficial to include `Cascading Style Sheets (CSS) <http://en.wikipedia.org/wiki/Cascading_Style_Sheets>`_ and `JavaScript <https://en.wikipedia.org/wiki/JavaScript>`_ to allow us to style webpages and introduce dynamic behaviour within the website. 

These files are known by Django as *static media*, and are served in a slightly different way from webpages. This section shows you how to set up your Django project to serve static media to the client. We'll also modify our template to include some example static media.



Configuring the Static Media Directory
......................................
To get static media up and running, you will need to set up a directory in which static media files are stored. 

In your Django project's directory (e.g. ``<workspace>/tango_with_django_project/``), create a new directory called ``static``. 

Now, place an image within the ``static`` directory. As shown in Figure :num:`fig-rango-picture`, we chose a picture of the chameleon, `Rango <http://www.imdb.com/title/tt1192628/>`_ - a fitting mascot, if ever there was one.

.. _fig-rango-picture:

.. figure:: ../images/rango-picture.pdf
	:figclass: align-center

	Rango the chameleon within our static media directory.

With our ``static`` directory created, we need to tell Django about it - just like we did with our ``templates`` directory earlier. In ``settings.py`` file, we need to update two variables:  ``STATIC_URL`` and the ``STATICFILES_DIRS`` tuple, but first create a variable to store the path to the static directory (``STATIC_PATH``) as follows:

.. code-block:: python
	
	STATIC_PATH = os.path.join(PROJECT_PATH,'static')

	STATIC_URL = '/static/' # You may find this is already defined as such.
	
	STATICFILES_DIRS = (
	    STATIC_PATH,
	)

So, you've typed in some code, but what does it represent? The first variable ``STATIC_URL`` defines the base URL with which your Django applications will find static media files when the server is running. For example, when running the Django development server with ``STATIC_URL`` set to ``/static/`` like in the code example above, static media will be available at ``http://127.0.0.1:8000/static/``.  The `official documentation on serving up static media <https://docs.djangoproject.com/en/1.5/ref/settings/#std:setting-STATIC_URL>`_ warns that it is vitally  important to make sure that those slashes are there. Not configuring this problem can let to a world of hurt.

While ``STATIC_URL`` defines the URL to access media via the web server, ``STATICFILES_DIRS`` allows you to specify the location of the newly created ``static`` directory on your local disk. Just like the ``TEMPLATE_DIRS`` tuple, ``STATICFILES_DIRS`` requires an absolute path to the ``static`` directory. Here, we re-used the ``PROJECT_PATH`` defined in Section :num:`model-setup-templates-label` to create the ``STATIC_PATH``.


With those two settings updated, run your Django project's development server once more. If we want to view our image of Rango,  visit the URL ``http://127.0.0.1:8000/static/rango.jpg``. If it doesn't appear, you will want to check to see if everything has been correctly spelt and that you saved your ``settings.py`` file, and restart the development server. If it does appear, try putting in additional file types into the ``static`` directory and request them via your browser.

.. caution:: While using the Django development server to serve your static media files is totally fine for a development environment, it's highly unsuitable for a production environment.  The `official Django documentation on Deployment <https://docs.djangoproject.com/en/1.5/howto/static-files/deployment/>`_ provides further information about deploying static files in a production environment.

Static Media Files and Templates
--------------------------------
Now that you have your Django project set up to handle static media, you can now access such media within your templates.

To demonstrate how to include static media, open up ``index.html`` located in the ``templates/rango`` directory. Modify the HTML source code as follows (the two lines that we add are shown with a HTML comment next to them for easy identification):

.. code-block:: html

	<!DOCTYPE html>
	
	{% load static %} <!-- New line -->
	
	<html>
	
	    <head>
	        <title>Rango</title>
	    </head>
	    
	    <body>
	        <h1>Rango says...</h1>
	        hello world! <strong>{{ boldmessage }}</strong><br />
	        <a href="/rango/about/">About</a><br />
	        <img src="{% static "rango.jpg" %}" alt="Picture of Rango" /> <!-- New line -->
	    </body>
	
	</html>

First we need to inform Django's template system that we will be using static media with the ``{% load static %}``. This allows us to call the ``static`` template tag as done in, ``{% static "rango.jpg" %}``. As you see Django Template tag are denoted by the curly brackets ``{ }``. In this example, the ``static`` tag will combine the STATIC_URL with "rango.jpg" so that the rendered HTML looks like:

.. code-block:: html

	<img src="/static/rango.jpg" alt="Picture of Rango" /> <!-- New line -->

If for some reason the image cannot be loaded, it is always nice to specify an alternative text tagline with the ``alt`` attribute to display in place of the missing image.

With this change in place, kick off the Django development server and visit ``http://127.0.0.1:8000/rango``. Hopefully, you will see web page something like the one shown in Figure :num:`fig-rango-site-with-pic`.

.. _fig-rango-site-with-pic:

.. figure:: ../images/rango-site-with-pic.pdf
	:figclass: align-center

	Our first Rango template, complete with a picture of Rango the chameleon.

The ``{% static %}`` function call should be used whenever you wish to reference static media on your page. The code example below demonstrates how you could include JavaScript, CSS and images into your templates.

.. code-block:: html
	<!DOCTYPE html>
	
	{% load static %}
	
	<html>
	
	    <head>
	        <title>Rango</title>
	        <link rel="stylesheet" href="{% static "css/base.css" %}" /> <!-- CSS -->
	        <script src="{% static "js/jquery.js" %}"></script> <!-- JavaScript -->
	    </head>
	    
	    <body>
	        <h1>Including Static Media</h1>
	        <img src="{% static "rango.jpg" %}" alt="Picture of Rango" /> <!-- Images -->
	    </body>
	
	</html>

Obviously, the static files you reference will need to be present within your ``static`` directory. If the file is not there or you have referenced it incorrectly the console output provide by Django's light weight development server will flag up any errors. Go ahead, give it a shot and see what happens.

For further information about including static media you can read through the official `Django documentation on working with static files in templates <https://docs.djangoproject.com/en/1.5/howto/static-files/#staticfiles-in-templates>`_.

.. caution:: Care should be taken in your templates to ensure that any `document type declaration <http://en.wikipedia.org/wiki/Document_Type_Declaration>`_ (e.g. ``<!DOCTYPE html>``) you use in your webpages appears in the rendered output on the *first line*. This is why we put the Django template command ``{% load static %}`` on a line underneath the document type declaration, rather than at the very top. It is a requirement of HTML/XHTML variations that the document type declaration be declared on the very first line. Django commands placed before will obviously be removed in the final rendered output, but they may leave behind residual whitespace which means your output `will fail validation <http://www.w3schools.com/web/web_validate.ASP>`_ on `the W3C markup validation service <http://validator.w3.org/>`_.

The Static Media Server
-----------------------
Now that you can dispatch static files, many projects also want to enable user to upload their own media content - for example to load a profile image. This section shows you how to add a simple, development media server to your Django project. The development media server can be used in conjunction with file uploading forms (which will be touch upon in later chapters, but we'll set up here, now).

So, how do we go about setting up a development media server? The first step is to create another new directory - called ``media`` - within our Django project's root (e.g. ``<workspace>/tango_with_django_project/``). The new ``media`` directory should now be sitting alongside your ``templates`` and ``static`` directories. After you create the directory, you must then modify your Django project's ``urls.py`` file, located in the project configuration directory (e.g. ``<workspace>/tango_with_django_project/tango_with_django_project/``). Add the following code:

.. code-block:: python
	
	# At the top of your urls.py file, add the following line:
	from django.conf import settings
	
	# UNDERNEATH your urlpatterns definition, add the following two lines:
	if settings.DEBUG:
		urlpatterns += patterns(
			'django.views.static',
			(r'media/(?P<path>.*)',
			'serve',
			{'document_root': settings.MEDIA_ROOT}), )

The ``settings`` module from ``django.conf`` allows us access to the variables defined within our project's ``settings.py`` file. The conditional statement then checks if the Django project is being run in `DEBUG <https://docs.djangoproject.com/en/1.5/ref/settings/#debug>`_ mode. If the project's ``DEBUG`` setting is set to ``True``, then an additional URL matching pattern is appended to the ``urlpatterns`` tuple. The pattern states that for any file requested with a URL starting with ``/media/``, the request will be passed to the ``django.views.static`` view. This view handles the dispatching of uploaded media files for you.

With your ``urls.py`` file updated, we now need to modify our ``settings.py`` file, again located within the project configuration directory. We now need to set the values of two variables. In your file, find ``MEDIA_URL`` and ``MEDIA_ROOT``, setting them to the values as shown below.

.. code-block:: python
	
	MEDIA_URL = '/media/'
	MEDIA_ROOT = os.path.join(PROJECT_PATH, '/media/') # Absolute path to the media directory

The first variable, ``MEDIA_URL`` defines the base URL from which all media files will be accessible on your development server. Setting the ``MEDIA_URL`` for example to ``/media/`` will mean that user uploaded files will be available from the URL ``http://127.0.0.1:8000/media/``. ``MEDIA_ROOT`` is used to tell Django where uploaded files should be stored on your local disk. In the example above, we set this variable to the result of joining our ``PROJECT_PATH`` variable defined in Section :num:`model-setup-templates-label` with ``/media/``. This gives an absolute path of ``<workspace>/tango_with_django_project/media/``.

.. caution:: As previously mentioned, the development media server supplied with Django is very useful for debugging purposes. However, it should **not** be used in a production environment. The official `Django documentation on static files <https://docs.djangoproject.com/en/1.5/ref/contrib/staticfiles/#static-file-development-view>`_ warns that such an approach is *"grossly inefficient and insecure"*. If you do come to deploying your Django project, read the documentation to see an alternative solution for file uploading that can handle a high volume of requests in a much more secure manner.

You can test this setup works by placing an image file in your newly created ``media`` directory. Drop the file in, start the Django development server, and request the image in your browser. For example, if you added the file ``rango.jpg`` to ``media``, the URL you should enter would look like ``http://127.0.0.1:8000/media/rango.jpg``. The image should show in your browser. If it doesn't, check your setup.

Basic Workflow
--------------
With the chapter complete, you should now know how to setup and create templates, use templates within your views, setup and use Django's to send static media files, include images within your templates and setup Django's static media server to allow for file uploads. We've actually covered quite a lot!

Creating a template and integrating it within a Django view is a key concept for you to understand. It takes several steps, but becomes second nature to you after a few attempts.

#. First, create the template you wish to use and save it within the ``templates`` directory you specified in your project's ``settings.py`` file. You may wish to use Django template variables (e.g. ``{{ variable_name }}``) within your template. You'll be able to replace these with whatever you like within the corresponding view.
#. Find or create a new view within an application's ``views.py`` file.
#. Add your view-specific logic (if you have any) to the view. For example, this may involve extracting data from a database.
#. Within the view, construct a dictionary object which you can pass to the template engine as part of the template's *context*.
#. Make use of the ``RequestContext()`` class and ``render_to_response()`` helper function to generate the rendered response. Ensure you reference the correct template file for the first ``render_to_response()`` parameter!
#. If you haven't already done so, map the view to a URL by modifying your project's ``urls.py`` file - and the application-specific ``urls.py`` file if you have one.

The steps involved for getting a static media file - such as an image - onto one of your pages is another important process you should be very familiar with. Check out the steps below on how to do this.

#. Take the static media file you wish to use and place it within your project's ``static`` directory. This is the directory you specify in your project's ``STATICFILES_DIRS`` tuple within ``settings.py``.
#. Add a reference to the static media file to a template. For example, an image would be inserted into an HTML page through the use of the ``<img />`` tag. Remember to use the ``{% load static %}`` and {% static "filename" %} commands within the template to make your life easier!
#. Load the view that utilises the template you modified in your browser. Your static media should appear.

The next chapter will look at databases. We'll see how to make use of Django's excellent database layer to make your life easier and SQL free!

Exercises
---------
	* Convert the about page to use a template too from a template called ``about.html``.
	* Within the ``about.html`` template, add a picture stored within your project's static media.