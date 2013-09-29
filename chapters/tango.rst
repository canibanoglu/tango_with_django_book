.. _tango-label:

Making Rango Tango! Exercises
=============================

So far we have been adding in different pieces of functionality to Rango. We've been building up the application in this manner to get you familiar with the Django Framework, and to learn about how to construct the various parts of a website that you are likely to make in your own projects. Rango however at the present moment is not very cohesive. In this chapter, we challenge you to improve the application and its user experience by bringing together functionality that we've already implemented alongside some awesome new additions.

To make Rango more coherent and integrated it would be nice to add the following functionality.

* Integrate the browsing and searching within categories, i.e.:
 - provide categories on every page;
 - provide some way to search through categories (see Chapter :ref:`ajax-label`); and
 - instead of have a disconnected search page, let users search for pages within a category so that they can then add these pages to the category (see Chapter :ref:`ajax-label`)
	
* Provide services for Registered Users, i.e.:
 - Let users view their profile	
 - Let users edit their profile
 - Let users see the list of users and their profiles.
		
* Track the click throughs of Categories and Pages, i.e.
 - Count the number of times a category is viewed
 - Count the number of times a page is viewed via Rango
 - Collect likes for categories (see Chapter :ref:`ajax-label`)

.. note:: We won't be working through all of these tasks right now. Some will be taken care of in Chapter :ref:`ajax-label`, while some will be left to you to complete as additional exercises.

Before we start to add this additional functionality we will make a todo list to plan our workflow for each task. Breaking tasks down into sub-tasks will greatly simplify the implementation and mean that we are attacking each one with a clear plan. In this chapter, we will provide you with the workflow for a number of the above tasks. From what you have learnt so far, you should be able to fill in the gaps and implement most of it on your own. The following chapter however includes the code walkthrough, along with notes on how we have implemented each task.


Provide categories on every page
--------------------------------

It would be nice to show the different categories that users can browse through on any page and not just the index page. There are a number of ways that this can be achieved, but given what we have learnt so far we could undertake the following workflow:

* Create a ``category_list.html`` template and migrate the template code for presenting the categories list from  ``index.html`` to this new template. This template wont be a full HTML document only part of, which we can include in other templates.
* Use the ``{% include "rango/category_list.html" %}`` to now include this template code into the ``base.html`` template within the sidebar.
 - This means that all pages will now include categories, assuming the ``cat_list`` data is passed through in the context dictionary. 
 - Put this include within an if statement: ``{% if cat_list %}`` to make sure that only templates that are providing the cat_list data try to render this component.
* Copy and paste the code that gets the list of categories from the *index* view into your category view......
 - Whoa! Stop... go back.. rewind...wtf!
 - You could copy and paste the code. It is very tempting isn't it :-) 
 - Let's try and do it a better way and instead create a helper function that will return the list of categories.
* Create a function called ``get_category_list`` that returns the list of categories.
 - We can then call this function, whenever we want to get the category list to present in the sidebar.
* Pass the category list data (``cat_list``) through to the template.

			
Search within a category page 
-----------------------------
Rango aims to provide users with a helpful directory of pages/links. At the moment, the search functionality is essentially independent of the categories but it would be nicer to have search integrated into the browsing. Let's assume that a user will first browse the category of interest first, and if they can't find the page that they want, they can then search for it. If they find a page that is suitable, then they can add it to the category that they are in. Let's tackle the first part here.

First we will need to remove the global search functionality and only let users search within a category - so we will essentially decommission the current search page and search view. Then, we'll need to:

* Remove the generic *search* link from the menu bar.
* Take the search form from ``search.html`` and put it into the ``category.html`` instead. Also include the template code to render the results.
* Update the category view to handle a POST request (i.e. when the user submits a search)
 - The view will then include any search results in the context dictionary for the template to render


View Profile 
------------
* Create a template called, ``profile.html``. In this template add in the fields associated with the user profile and the user (i.e. username, email, website and picture, etc)
* Create a view called, ``profile``, this view will obtain the data required to render the user profile template/pages.
* Create a url mapping of the form, ``rango/profile/`` that maps to the *profile* view.
* In the base template add a link called ``Profile`` into the menu bar. This should only be available to users logged in, i.e. use ``{% if user.is_authenticated %}``
	
Track the click throughs of Pages
---------------------------------
Currently, Rango provides a direct link to external pages. This is not very good if you want to track the number of times each page is clicked/viewed. To count the number of times a page is viewed via Rango you will need to:

* Create a new view called ``track_url`` and a new url mapping called ``goto`` that maps the url ``rango/goto`` to the view ``track_url``.
* The ``track_url`` view will examine the GET request parameters and pull out the ``page_id``.
- So the GET requests will be something like: ``rango/goto/?page_id=1``
- The view should then find the page associated with the URL/page_id given the parameterisation and increment the views field.
- The view will then redirect the user to the specified URL using Django's redirect method.
- However, if there are no parameters in the GET request for URL and page_id, or the parameters do not return a Page object, then redirect the user to the index view/page.
		
* Update the ``category.html`` so that it uses ``rango/goto/?page_id=XXX`` instead of using the direct URL.


