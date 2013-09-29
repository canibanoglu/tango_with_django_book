.. _tango-too-label:
Doing the Tango with Rango! 
===========================

Hopefully, you will have been able to complete the exercises given the workflows we provided, if not, or if you need a little help checkout snippets of code and use them within your version of Rango.



.. #########################################################################

List Categories on each page
----------------------------

Creating a Category List Template
.................................
Create a ``category_list.html`` template so that it looks like the code below:


.. code-block:: html
	
	<ul class="nav nav-list">
	   {% if cat_list %}
		{% for cat in cat_list %}
		  <li><a href="/rango/category/{{ cat.url }}">{{ cat.name }}</a></li>
		{% endfor %}
	   {% else %}
		<li>No categories at present.</li>
	   {% endif %}
	</ul>


Updating the Base Template
..........................
In the sidebar div add {% include "rango/category_list.html" %} so that the ``base.html`` contains:

.. code-block:: html
	
	<div class="well sidebar-nav">
	   {% block sidebar %}
	   {% endblock %}
		<div id="cats">
		   {% if cat_list %}
			<ul class="nav nav-list"><li>Category List</li></ul>	
			   {% include 'rango/category_list.html' with cat_list=cat_list %}
		   {% endif %}
		</div>	
	</div><!--/.well -->


Creating Get Category List Function
...................................
In ``rango/views.py``, create a function called *get_category_list* that returns the list of categories.

.. code-block:: python
	
	def get_category_list():
		cat_list = []
		cat_list = Category.objects.all()
		for cat in cat_list:
			cat.url = encode_url(cat.name)
			
		return cat_list

Updating Views
..............
Then call this function in each of the views that you want to display the category list in the sidebar, and pass the list into the context dictionary. For example, to have the categories showing on the index page, update the index view as follows:
	
.. code-block:: python
	
	def index(request): 
		context = RequestContext(request)
		cat_list = get_category_list()
		
		context_dict['cat_list'] = cat_list
		
		....
		
		return render_to_response('rango/index.html', context_dict, context)
	
Note: to add the category list to all the other pages you will need to do some refactoring to pass in all the context variables.
	
.. #########################################################################	

Search within a category page 
-----------------------------
Rango aims to provide users with a helpful directory of pages/links. At the moment, the search functionality is essentially independent of the categories but it would be nicer to have search integrated into the browsing. Let's assume that a user will first browse the category of interest first, and if they can't find the page that they want, they can then search for it. If they find a page that is suitable, then they can add it to the category that they are in. Let's tackle the first part here.

First we will need to remove the global search functionality and only let users search within a category - so we will essentially decommission the current search page and search view. Then, we'll need to:

Decommissioning Generic Search
..............................

Remove the generic *search* link from the menu bar by editing the ``base.html`` template, you can also remove the URL mapping in ``rango/urls.py``.

Creating Search Form Template
.............................
Take the search form from ``search.html`` and put it into the ``category.html`` - be sure to change the action to point to the category view, as shown below:

.. code-block:: html
	
	<form class="span8 form-search" id="search_form" method="post" action="/rango/category/{{ category_name_url }}/">
		{% csrf_token %}
        <input type="text" class="input-long search-query"  name="query" value="" id="query" />
        <button type="submit" class="btn btn-success" name="submit" value="Search">Search</button>
	</form>


Also include a div to house the results:

.. code-block:: html
	
	<div>
	{% if result_list %}
    			<br />
	<!-- Display search results in an ordered list -->
	<div style="clear: both;">
		<ol>
		{% for result in result_list %}
			<li>
				<strong><a href="{{ result.link }}">{{ result.title }}</a></strong><br />
				<p>{{ result.summary }}</p>
			</li>
		{% endfor %}
		</ol>
	</div>
	{% endif %}
	</div>



Updating Category View
......................
Update the category view to handle a POST request (i.e. when the user submits a search) and inject the results list into the context:
	
.. code-block:: python
	
	def category(request, category_name_url):
	    context = RequestContext(request)
		cat_list = get_category_list()
		category_name = decode_url(category_name_url)
		
		context_dict = {'cat_list': cat_list, 'category_name': category_name}
		
		try:
			category = Category.objects.get(name=category_name)
			context_dict['category'] = category

			pages = Page.objects.filter(category=category)
			context_dict['pages'] = pages
		except Category.DoesNotExist:
			pass
		
		if request.method == 'POST':
			query = request.POST['query'].strip()
			if query:
				result_list = run_query(query)
				context_dict['result_list'] = result_list
						
		return render_to_response('rango/category.html', context_dict, context)	
	
		

.. #########################################################################

View Profile 
------------
To add the view profile functionality undertake the following steps:

Creating the Profile Template
.............................
Create a new template called, ``profile.html``. In this template add the following code:

.. code-block:: html
	
	{% block body_block %}
	   <div class="hero-unit">
	   <h1> Profile <h1> <br/>
	   <h2>{{user.username}}</h2>
	   <p>Email: {{user.email}}</p>
	   <p>Website: <a href="{{userprofile.website}}">{{userprofile.website}}</a> </p>
	   <img src="{{userprofile.picture}}"  />
	   </div>
	{% endblock %}


Creating Profile View
......................
Create a view called, ``profile``, and add the following code:

.. code-block:: python

	from django.contrib.auth.models import User

	@login_required
	def profile(request):
		context = RequestContext(request)
		cat_list = get_category_list()
		context_dict = {'cat_list':cat_list}
		u = User.objects.get(username=request.user)
		try:
			up = UserProfile.objects.get(user=u)
		except:
			up = None
		
		context_dict['user'] = u
		context_dict['userprofile'] = up
		return render_to_response('rango/profile.html', context_dict, context)

Mapping Profile URL/View
...................
Create a url mapping of the form, ``rango/profile/`` that maps to the *profile* view. Do this by updating the urlpatterns in ``rango/urls.py`` so that it includes:

.. code-block:: python:
	
	url(r'^profile/$', views.profile, name='profile'),

Updating the Base Template
..........................
In ``base.html``, update the code to put a link to the profile page in the menu bar:

.. code-block:: html
	
	{% if user.is_authenticated %}
	
		<li><a href="/rango/profile" >Profile</a></li>
	
	{% endif %}	
	
.. #########################################################################

Track the click throughs of Pages
---------------------------------
Currently, Rango provides a direct link to external pages. This is not very good if you want to track the number of times each page is clicked/viewed. To count the number of times a page is viewed via Rango you will need to perform the following steps.


Creating a Track Url View
.........................
Create a new view called ``track_url`` in ``rango/views.py`` which takes a parameterised GET request (i.e. ``rango/goto/?page_id=1`` ), and updates the number of views for the page and then redirects to the actual URL.

.. code-block:: python	

	from django.shortcuts import redirect

	def track_url(request):
		context = RequestContext(request)
		page_id = None
		url = '/rango/'
		if request.method == 'GET':
			if 'page_id' in request.GET:
				page_id = request.GET['page_id']
				try:
					page = Page.objects.get(id=page_id)
					page.views = page.views + 1
					page.save()
					url = page.url
				except:
					pass
					
		return redirect(url)
	
Note that you have to import the Django Shortcut to redirect the user to the page that they clicked.	

Mapping URL
...........
In ``rango/urls.py`` add the following code to ``urlpatterns``:

.. code-block:: python
	
	url(r'^goto/$', views.track_url, name='track_url'),


Updating the Category Template
...............................
Update the ``category.html`` so that it uses ``rango/goto/?page_id=XXX`` instead of directly providing the direct URL for users to click:

.. code-block:: html
	
	{% if pages %}
		<ul>
		{% for page in pages %}
			<li>
			<a href="/rango/goto/?page_id={{page.id}}">{{page.title}}</a>
			            {% if page.views > 1 %}
			                - ({{ page.views }} views)
			            {% elif  page.views == 1 %}
			                - ({{ page.views }} view)
			            {% endif %}
			</li>
		{% endfor %}
		</ul>
	{% else %}
		<p>No pages in category.</p>
	{% endif %}

Here you can see that in the template we have added some control statements to display ``view`` or ``views`` or nothing depending on the number of ``page.views``.


Updating Category View
......................
Since we are tracking the number of click throughs you can now update the ``category`` so that you order the pages by the number of views. Also, click on a number of pages, and check out the Top Five Pages on the index page. 

