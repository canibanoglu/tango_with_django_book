.. _deploy-label:

Deploying the Project/Application
=================================

Create a PythonAnywhere Account
-------------------------------

PythonAnywhere (https://www.pythonanywhere.com/) is a Python development and hosting environment. It is easy to use, fast and flexible. Currently, they provide a free plan which sets you up with an adequate amount of space and cpu allowance to get a Django app up and running.

Go to
https://www.pythonanywhere.com/pricing/ and sign up for a free account. What's great about PythonAnywhere is that you don't require a credit card and you can upgrade your account at anytime via PayPal. Too easy!

Once you have your account with a username, you can deploy your django app to <username>.pythonanywhere.com. If you have an upgraded account then you'll be able to assign the application to a domain name.

The web interface provides a number of tabs when you view the Dashboard: Consoles, Files, Web, Schedule and Databases. They are pretty self explanatory. If you have an upgraded account, which is well worth it, then you will be able to ssh to the PythonAnywhere servers and access the console and file system that way. If not, then you can go into console and create a console of your choice. i.e. click on Python 2.7 to create a console running Python 2.7.

.. _virtual-environment:

Setup a Virtual Environment
---------------------------
PythonAnywhere has preloaded a number of default packages i.e. Python 2.7.4 and Django 1.3, and virtualenv, and virtualenvwrapper and pip. However, since we are using a different setup we need to create a virtual environment, which is good practice anyway.

Open a Bash console.

::
	
	$ source virtualenvwrapper.sh
	$ mkvirtualenv rango15
	
	
This will create a virtual environment called *rango15*, and your prompt should change to (rango15). It may take a minute or two to generate.

If you list all files in your home directory with *ls -la* you will see that there is a directory called *.virtualenvs*. This is where your virtual environments will be stored.

So if you issue *which pip* at the prompt, it will show:

::

	/home/<username>/.virtualenvs/rango15/bin/pip


Now you can customise the environment by installing the required packages - again these will take a bit of time to download and install:

::

	pip install -U django==1.5.4
	pip install pil
	

#TODO(leifos): add list of other packages to be installed, or better yet, extract this from the local virtual environment.

To check if django has been installed, check with *which django-admin.py*. It should be in:

::
	
	$ which django-admin.py
	/home/<username>/.virtualenvs/django15/bin/django-admin.py


PythonAnywhere  also provides instructions on `how to install the virtual environments <  https://www.pythonanywhere.com/wiki/VirtualEnvForNewerDjango>_.

To use/engage your virtual environment, when you create a bash shell in the future, you will need to execute:

::
	
	$ source virtualenvwrapper.sh
	$ workon rango15
	
To make life even easier you add *source virtualenvwrapper.sh* to your .bashrc file.

You will need to use this virtual environment to sync the database, to run population scripts, etc, while working on the rango app.

Clone your Git Repo
-------------------

git clone https://github.com/leifos/tango_with_django.git


Make sure you are in the rango15 virtual environment with *workon rango15*, then change directory to the tango_with_django direcoty. Now sync the database and populate the database with:

:: 

	$ python manage.py syncdb
	
	
	$ python populate_range.py
	

Set up your Web App
-------------------

Go to the Dashboard, click the web tab, and then add a new web app.

From the wizard, follow the instructions and select the manual configuration option.

If you open a new browser window and check out: <username>.pythonanywhere.com then you will see the default "Hello, World!" web page. Now let's hook it up to Rango so it runs instead!


Configure the WSGI file
-----------------------
In the web tab, for your web app, you will need to edit the wsgi.py (just click on the link).

The WSGI file is for the web server to map incoming requests to your domain <username>.pythonanywhere.com to your web application (rango). The good people at PythonAnywhere have set up a number of example WSGI files. For your application, you'll need to configure the Django example as follows:

.. code-block:: python
	
	# turn on the virtual environment for your application
	activate_this = '/home/<username>/.virtualenvs/rango15/bin/activate_this.py'
	execfile(activate_this, dict(__file__=activate_this))

	# +++++++++++ DJANGO +++++++++++
	# To use your own django app use code like this:
	import os
	import sys
	#
	## assuming your django settings file is at '/home/<username>/mysite/settings.py'
	path = '/home/<username>/tango_with_django/tango_with_django_project'
	if path not in sys.path:
		sys.path.append(path)

	os.environ['DJANGO_SETTINGS_MODULE'] = 'tango_with_django_project.settings'

	import django.core.handlers.wsgi
	application = django.core.handlers.wsgi.WSGIHandler()


Make sure you remove all the other code in the the wsgi.py.

#TODO(leifos): update paths etc.


This block of code first activates the virtual environment (rango15) - as this has been configured with all the required packages. 

Then it adds your project to the system path. You can also add in addition paths here, for instance, if you have a library of functions, that you want your application to use.

Finally, the WSGI handlers are invoked for your application.

Time to launch your application. Save your changes, return to the Web tab in the Dashboard, and  reload your application. Once the application is reloaded, click on the link to your deployed site:
http://<username>.pythonanywhere.com

Hopefully, you will see that a HTML version of your site has appeared. However, since we have not configured the static paths properly, the CSS and Javascript is not accessible to the client. You can sort this out next.

If you get a Bad Gateway 502 error, then you will have to wait a minute, and reload your application. 


Assign the Static Paths
-----------------------
From the Dashboard, go to the Web tab, and select the domain that will be hosting rango15, i.e. <username>.pythonanywhere.com

The in the static files section

	* Click on the "Enter URL" and enter /static/admin/, then hit return.
	* Click on "Enter Path" and enter the path to django's static admin files, this should be: /home/<username>/.virtualenvs/rango15/lib/python2.7/site-packages/django/contrib/admin/static/admin
	* Repeat the process for the URL /static/, and path /home/<username>/.... #TODO(leifos):add path here

Now reload your web app.



Other Settings
--------------

In settings.py,

If you set:
::
	
	Debug = False

then you need to set:

:: 
	
	HOSTS = [ <username>.pythonanywhere.com]



Log Files
---------
Deploying your application introduces another level of complexity and is likely to result in new errors or unsuspecting problems arising. The log files can provide vital clues when you are debugging and monitoring your application.

The log files can be viewed through the web tab on the Dashboard, however, if you access them via the bash console, then they can be found in */var/log/*. It is sometimes helpful to delete them or move them, so that you don't have to scroll through a log list of previous output.






