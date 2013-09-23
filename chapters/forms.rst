.. _forms-label:

Fun with Forms
==============
Now that you're a view-creating genius, we'll move on and have a look at another nifty feature that Django has to offer us. The web development framework comes with really nice form-handling functionality, making it a straightforward process for you to gather information from users and send it back to your web application. According to `Django's documentation on forms <https://docs.djangoproject.com/en/1.5/topics/forms/>`_, the form handling functionality allows you to:

#. display an HTML form with automatically generated *form widgets* (like a text field or date picker);
#. check submitted data against a set of validation rules;
#. redisplay a form in case of validation errors; and
#. convert submitted form data to the relevant Python data types.

One of the major advantages of using Django's forms functionality is that using it could potentially save you a lot of time. You can create HTML forms - and the necessary server-side implementation to handle them - that tie in closely to your Django application's models. As part of this tutorial, we'll be looking at how to implement the necessary infrastructure that will allow users of Rango to add a new category to the database, and get you started on adding pages to categories.

Basic Workflow
--------------
While viewing data already in the database is one thing, it would be nice to learn how to create custom forms for your website that lets users enter data - all without needing to go through Django's admin interface. So how do we do this? Like our templates section, we have a basic workflow for how to add a new form into your web application.

#. If you haven't already got one, create a ``forms.py`` file within your Django application's directory to store form-related classes.
#. Create a ``ModelForm`` for each model that you wish to create a form for.
#. Customise the forms as you require or desire.
#. Create or update a view to handle the form - including *displaying* the form, *saving* the form data, and *flagging up errors* which may occur when the user enters data.
#. Create or update a template to display the form on.
#. Add a ``urlpattern`` to map to the new view (if you created a new one).

The process - as ever - is a relatively straightforward process. We'll be tweaking our existing code and adding a new view to demonstrate how everything fits together.

Creating Categories
-------------------
Let's get started and have some fun with forms! As the first of our workflow steps suggest, we should create a new ``forms.py`` file within our ``rango`` application directory. Although this step is not absolutely required, it does provide a neat way to provide a clean separation of form logic from view logic. This is opposed to creating all of our form-related classes within ``views.py``, too.

Within our new ``forms.py`` module, the first step is to create a class inheriting from Django's ``ModelForm``. `In essence, a ModelForm <https://docs.djangoproject.com/en/1.5/topics/forms/modelforms/#modelform>`_ is a *helper class* that allows you to create a Django ``Form`` from a preexisting model. As we've already got two models defined for Rango (``Category`` and ``Page``), we'll create ``ModelForms`` for both. In Rango's ``forms.py`` file, add the following code. Check out the inline commentary to see what's going on.

.. code-block:: python
	
	class CategoryForm(forms.ModelForm):
	    name = forms.CharFields(max_length=50, help_text="Please enter the category name.")
	    
	    # An inline class to provide additional information on the form.
	    class Meta:
	        # Provide an association between the ModelForm and a model
	        model = Category
	
	class PageForm(forms.ModelForm):
	    title = forms.CharField(max_length=100, help_text="Please enter the title of the page.")
	    url = forms.CharField(max_length=200, help_text="Please enter the URL of the page.")
	
	    class Meta:
	        # Provide an association between the ModelForm and a model
	        model = Page
	        
	        # What fields do we want to include in our form?
	        # This way we don't need every field in the model present.
	        # Some fields may allow NULL values, so we may not want to include them...
	        # Here, we are hiding the foreign keys and no. of views fields.
	        fields = ('title', 'url')

As we'll be inheriting from the ``ModelForm`` class, don't forget to include your, er, include statement at the top of the file.

.. code-block:: python
	
	from django import forms

Django provides us with a number of ways to customise the forms that are created on our behalf. In the code sample above, we've specified the widgets that we wish to use for each field to be displayed. For example, in our ``PageForm`` class, we've defined ``forms.CharField`` for both the ``title`` and ``url`` fields, as a text entry field is most appropriate. You can see from the parameters we pass that the maximum permissible length of each form field varies - with lengths of 100 and 200 characters.

Besides the ``CharField`` widget, many more are available for use. As an example, Django provides ``IntegerField`` (integer entry), ``ChoiceField`` (radio input buttons), and ``DateField`` (for date/time entry). There are many other field types you can use, which perform error checking for you (e.g. *is the value provided a valid integer?*). We highly recommend you have a look at the `official Django documentation <https://docs.djangoproject.com/en/1.5/ref/forms/widgets/>`_ on the subject to see what fields exist, and the arguments you can provide to customise them.

Perhaps the most important aspect of a class inheriting from ``ModelForm`` is the need to define *which model we're wanting to provide a form for.* We take care of this through our nested ``Meta`` class, which does as it says on the tin - it provides additional `metadata <http://en.wikipedia.org/wiki/Metadata>`_ for Django to act upon. Set the ``model`` attribute of the nested ``Meta`` class to the model you wish to use. For example, our ``CategoryForm`` class has a reference to the ``Category`` model. **This is a crucial step - with our model specified, Django can then take care of creating a form in the image of the specified model, and can then automatically handle the saving of form data to the model for you.** We also use the ``Meta`` class to specify which fields that we wish to include in our form through the ``fields`` tuple. Use a tuple of field names to specify the fields you wish to include.

.. note:: There's a lot to using Django forms - so much so that we couldn't possibly write about it all here - that might require another book! We highly recommend you check out the `official Django documentation <https://docs.djangoproject.com/en/1.5/ref/forms/>`_ on forms for further information.

With our ``ModelForm`` inheriting classes defined, we're now ready to create a new view to display and handle the posting of form data. We'll focus on creating a view that allows users of Rango to create new categories. As we will be creating a new view, we need a new function in Rango's ``views.py`` file. Let's check out the code for the new view. Hopefully it should by now look somewhat familiar in its structure.

.. code-block:: python
	
	def add_category(request):
	    # Get the context from the request.
	    context = RequestContext(request)
	    
	    # A HTTP POST?
	    if request.method == 'POST:
	        form = CategoryForm(request.POST)
	        
	        # Have we been provided with a valid form?
	        if form.is_valid():
	            # Save the new category to the database.
	            form.save(commit=True)
	            
	            # Now call the index() view.
	            # The user will be shown the homepage.
	            return index(request)
	        else:
	            # No form passed - ignore and keep going.
	            pass
	    else:
	        # If the request was not a POST, display the form to enter details.
	        form = CategoryForm()
	    
	    # Bad form (or form details), no form supplied...
	    # Render the form with error messages (if any).
	    return render_to_response('rango/add_category.html', {'form': form}, context)

We'll also want to include the shortcut function, ``render_to_response()`` and our ``CategoryForm`` class. This will result in adding the following import statements at the top of the ``views.py`` file.

.. code-block:: python
	
	from django.shortcuts import render_to_response
	from rango.forms import CategoryForm

The new ``add_category()`` view adds several key pieces of functionality for handling forms. First, we access the context surrounding the HTTP request. This then allows us to determine the type of request being made - whether it be a HTTP ``GET`` or ``POST``. This allows us to handle different requests appropriately - whether we want to show a form, or process form data - all from the same URL. Our example ``add_category()`` function can handle three different scenarios:

- showing a new, blank form for adding a category;
- saving form data provided by the user to the associated model, and rendering the Rango homepage; and
- if there are errors, redisplay the form with error messages.

We also make use of the shortcut function, ``render_to_response()``. This function call allows you to pass in a template, data specific to the view to be rendered, and the context. Without the shortcut function, you'd have to load a template, load a context, call render and then return a ``HttpResponse`` object.

.. note:: What do we mean by ``GET`` and ``POST``? They are two different types of *HTTP requests*. According to `Wikipedia <http://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods>`_, a HTTP ``GET`` is used to request a *representation of the specified resource.* In other words, we use a HTTP ``GET`` to retrieve a particular resource, whether it be a webpage, image or other file. In contrast, a HTTP ``POST`` *submits data from the client's web browser to be processed.* This type of request is used for example when submitting the contents of a HTML form. Ultimately, a HTTP ``POST`` may end up creating a new resource (e.g. a new database entry) on the server. This can later be accessed through a HTTP ``GET`` request.

Django's form-handling machinery has also been utilised to process the data returned from a user's browser via a HTTP ``POST`` request. It not only handles the saving of form data into the chosen model, but will also automatically generate any error messages for each form field (if any are required). This means that Django will not store any submitted forms with missing information which could potentially cause problems for your database's referential integrity. For example, supplying no value in the category name field will return an error, as the field cannot be blank.

You'll notice from the line in which we call ``render_to_response()`` that we refer to yet *another* template file. This new template, ``add_category.html``, contains the relevant Django template code for creating the actual HTML markup of your model's form. In the file ``templates/rango/add_category.html``, add the following markup.

.. code-block:: html
	
	<!DOCTYPE html>
	<html>
	    <head>
	        <title>Rango</title>
	    </head>
	    
	    <body>
	        <h1>Add a Category</h1>
	        
	        <form id="category_form" method="post" action="/rango/add_category/">
	            
	            {% csrf_token %}
	            {% for hidden in form.hidden_fields %}
	                {{ hidden }}
	            {% endfor %}	
	            
	            {% for field in form.visible_fields %}
	                {{ field.errors }}
	                {{ field.help_text}}
	                {{ field }}
	            {% endfor %}
	            
	            <input type="submit" name="submit" value="Create Category" />
	        </form>
	    
	    </body>
	
	</html>

Now, what does this code do? You can see that within the ``<body>`` of the HTML page, we place a ``<form>`` element. Looking at the attributes for the ``<form>`` element, you can see that all data captured within this form is sent to the URL ``/rango/add_category/`` as a HTTP ``POST`` request (the ``method`` attribute is case insensitive, so you can do ``POST`` or ``post`` - both provide the same functionality). Within the form, we have two for loops - one controlling *hidden* form fields, the other *visible* form fields - with visible fields controlled by the ``fields`` attribute of your ``ModelForm``'s ``Meta`` class. These loops produce HTML markup for each form element. For visible form fields, we also add in any errors that may be present with a particular field, and help text which can be used to explain to the user what he or she needs to enter.

.. note:: The need for hidden as well as visible form fields is necessitated by the fact that HTTP is a `stateless protocol <http://en.wikipedia.org/wiki/Stateless_protocol>`_. You can't persist state between different HTTP requests, which can make designing web applications a pretty frustrating task at times. To overcome this limitation, hidden HTML form fields were created which allows web applications to pass important information to a client (which cannot be seen on the rendered page) in a HTML form, only to be sent back to the originating server when the user submits the form. Have a look `here <http://www.echoecho.com/htmlforms07.htm>`_ for more information on hidden HTML form fields.

You should also notice we include a small snippet of code - ``{% csrf_token %}``. This is a *Cross-Site Request Forgery (CSRF) token*, which helps to protect and secure the ``POST`` action that is initiated on the subsequent submission of a form. **The CSRF token is required by the Django framework. If you forget to include a CSRF token in your forms, a user may encounter errors when he or she submits the form.** Check out the `official Django documentation <https://docs.djangoproject.com/en/1.5/ref/contrib/csrf/>`_ for more information on CSRF tokens, and `this <http://stackoverflow.com/questions/11287493/understanding-of-csrf-token>`_ excellent Stack Overflow question and answer page.

With this template saved, we can now map our new ``add_category()`` view to a URL. Looking at our HTML code example, we point the form to the URL ``/rango/add_category/`` upon form submission. If we have our URL and view function name, we can do our mapping! Open up Rango's ``urls.py`` file (at ``<workspace>/tango_with_django_project/rango/urls.py``), and modify the ``urlpatterns`` tuple to look like the following.

.. code-block:: python
	
	urlpatterns = patterns('',
	    url(r'^$', views.index, name='index'),
	    url(r'^add_category/$', views.add_category, name='add_category'), # NEW MAPPING!
	    url(r'^(?P<category_name_url>\w+)', views.category, name='category'),)

Note the order in which we placed our new URL mapping. Django looks for a matching URL, starting with the first tuple entry. It then moves along the tuple sequentially until a match is found (a HTTP 404 error is raised if no match is found). In our example, the URL ``/add_category/`` is our new URL for adding a category. As such, this must always return the add category form, and should take precedence over the category view mapping, which could match to any string combination. If the URL provided does not match ``/add_category/``, Django then falls back to the category view mapping as a last resort. Take a look at the `official Django documentation <https://docs.djangoproject.com/en/1.5/topics/http/urls/#how-django-processes-a-request>`_ for more information on how Django interprets which URL mapping to use.

As a final step, it's a good idea to make the new category view accessible to users. To do this, let's edit our ``index.html`` template, located at ``templates/rango/index.html`` from your Django project's root directory. Add the following HTML hyperlink just before the ``</body>`` closing tag.

.. code-block:: html
	
	<a href="/rango/add_category/">Add a New Category</a>

This will add a new hyperlink at the bottom of Rango's homepage, nicely linking up the add category view. Now you can try it all out! Run your Django development server, and navigate to ``http://127.0.0.1:8000/rango/``. Use your new hyperlink to jump to the add category view, and try adding a category. Check out Figure :num:`fig-rango-form-steps` for what screenshots of what we saw.

.. _fig-rango-form-steps:

.. figure:: ../images/rango-form-steps.pdf
	:figclass: align-center

	Adding a new category to Rango with our new form. The diagram illustrates the steps involved.

Creating Pages
--------------
Now that you've successfully included a form to add new categories, the next logical step is to create a form that allows users to add pages. This process will be essentially the same process that follows the workflow that we described above. You'll need to create a new view (and URL mapping for it), along with a new template. To get you started, here's the view logic for you. Think about how it'll plug into Rango's ``urlpatterns`` tuple and how your new template will plug into the view. Check out the inline commentary which explains what's going on.

.. code-block:: python
	
	def add_page(request, category_name_url):
	    context = RequestContext(request)

	    category_name = decode_category(category_name_url)
	    if request.method == 'POST':
	        form = PageForm(request.POST)
	
	        if form.is_valid():
	            # This time we cannot commit straight away.
	            # Not all fields are automatically populated!
	            page = form.save(commit=False)
	
	            # Retrieve the associated Category object so we can add it.
	            cat = Category.objects.get(name=category_name)
	            page.category = cat
	
	            # Also, create a default value for the number of views.
	            page.views = 0
				
	            # With this, we can then save our new model instance.
	            page.save()
	            
	            # Now that the page is saved, display the category instead.
	            return category(request, category_name)
	        else:
	            print form.errors
	    else:
	        form = PageForm()

	    return render_to_response( 'rango/add_page.html', 
	            {'category_name_url': category_name_url, 
	             'category_name': category_name, 'form': form},
	             context)

Don't forget to include our ``PageForm`` class from Rango's ``forms.py`` module with the following import statement.

.. code-block:: python
	
	from rango.forms import PageForm

Cleaner Forms
.............
Since we have defined the ``url`` attribute in the ``Page`` model to be a ``URLField``, Django expects to be provided with a fully formed URL. Since it can be cumbersome for users to type in an entire URL like ``http://www.url.com``, we can override the ``clean()`` method implemented in ``ModelForm``. For example, in the ``PageForm`` class, include the following method that checks if ``http://`` is included at the start of a new URL - and if not, prepends ``http://`` to the string.

.. code-block:: python

	def clean(self):
	    cleaned_data = self.cleaned_data
	    url = cleaned_data.get('url')
	    
	    if not url.startswith('http://'):
	        url = 'http://' + url
	    
	    cleaned_data['url'] = url
	    return cleaned_data

This trivial example shows how we can clean the data being passed through the form before being stored. This is pretty handy, especially when particular fields need to have default values - or data within the form is missing, and we need to handle such data entry problems.

Exercises (LEIF TODO?)
----------------------
- What happens when you don't enter in a category name, and hit the submit button?
- What happens when you try to add in a category that already exists?

- how do we handle add to categories that dont exist,
- how do we gracefully handle add a category that already exists.

