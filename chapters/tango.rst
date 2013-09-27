.. _tango-label:

Making Rango Tango! Exercises
=============================

So far we have been adding in different pieces of functionality to Rango. We've been building up the application in this manner to get you familiar with the Django Framework and to learn about how to construct the various parts of a website that you are likely to do in your own projects. With respect to Rango, however, the application at the moment is not very cohesive. In this chapter we challenge you to improve the application and its user experience by bringing together some of the functionality that we have already implemented along with added some other functionality to further improve the user experience.

To make Rango more coherent and integrated it would be nice to:

	* Integrate the browsing and searching with categories, i.e.: 
		* Let users search within/on a category page 
		* Provide categories on every page
		
	* Provide services for Registered Users
		* Let users view their profile	
		
	* Track the click throughs of Pages and Categories i.e.
		* Count the number of times a page and category is viewed via Rango

	* Make the application more dynamic with AJAX
		* Add inline category suggestions
		* Add a "Like Button" to let registered users "like" a particular category
		* Add an "Add Button" to let registered users quickly and easier add a Page to the Category in which they are searching.
		

To do this, it is a good idea to formulate a work flow for each task.Breaking tasks down into sub-tasks will simplify the problems and make it easier to implement. In this chapter, we will provide you with the workflow for each task, then in the following chapter, we have included the code and notes on how to go about implementing the code for each task.

Integrate the browsing and searching with categories
---------------------------------------------------- 
Rango aims to provide users with a helpful directory of pages/links. At the moment, the search functionality is essentially independent of the categories but it would be nicer to have search integrated into the browsing. Let's assume that a user will first browse the category of interest first, and if they can't find the page that they want, they can then search for it. If they find a page that is suitable, then they can add it to the category that they are in. Let's tackle the first part here.

Let users search within/on a category page 
..........................................

	* Remove the generic *search* link from the menu bar.
	* Create some cookies to remember what category the user is currently in (category id and category name).
	* Copy and paste the search form from the *search.html* template into the *category.html* template, so that users can search directly from the category page.
		* Hmmm.. copy and paste.... surely there is a much better way...
		* Use {% include "search_form.html" %} and only create the search form once in a template called *search_form.html*.
		* See https://docs.djangoproject.com/en/dev/ref/templates/builtins/#include
	* Update the *search* view to read the category name and id from the cookies and pass that data onto the *search.html* template
	* In *search.html* display what category the search is taking place in.
	
Alternatively:
	* Instead of using cookies, use a similar approach as to the approach taken for adding pages (i.e. in the *add_pages* view), where you encoded the category name in the url. 
	* So the search url mapping could be extended to receive urls such as: *rango/search/<category_url>/
	* The category_url can be decoded to obtain the category name which can be passed on to the template.
	

Provide categories on every page
.................................
It would be nice to show the different categories that users can browse through on any page and not just the home page. To do this you'll need to do the following:

	* Create a *category_list.html* template and migrate the template code for presenting the categories list from  *index.html* to this new template.
	* Use the {% include "rango/category_list.html" %} to now include this template code into the *base.html* template within the sidebar.
		* i.e. this means the all pages will now include categories (assuming the cat_list data is applied.)
		* Put template code for displaying categories with in an if statement: {% if cat_list %}
		* This will make sure that only templates that are providing with the cat_list data try to render this component
	* Now, go and copy and paste the code that gets the list of categories from the *index* view into your category view......
		* Whoa! Stop... go back.. rewind...wtf!
		* You could copy and paste the code. It is very tempting isn't it.. you lazy... 
		* Let's try and do it a better way and instead create a helper function that will return the list of categories.
		* Create a function called *get_category_list* that returns the list of categories.
		* Then call this function in each of the views that you want to display the category list in the sidebar.
			* Pass the category list data (cat_list) through to the template to be rendered.


Provide services for Registered Users
-------------------------------------

Let users view their profile 
.............................

	* Create a template called, *profile.html*. In this template add in the fields associated with the user profile and the user (i.e. username, email, website and picture, etc)
	* Add a link called *Profile* which is only available to the user who's profile page it is. i.e. the user needs to be logged in and authenticated, and the profile on display must be their profile.
		* i.e. use 	{% if user.is_authenticated %}  in the template to test whether they are or not.
	* Create a view called, *profile*, this view will obtain the data required to render the user profile template/pages.
	* Create a url mapping of the form, *rango/profile/* that maps to the *profile* view.


	
Track the click throughs of Pages within Rango categories
--------------------------------------------------------- 
Currently, Rango provides a direct link to external pages. This is not very good if you want to track the number of times each page is clicked/viewed. To count the number of times a page is viewed via Rango you will need to:

 	* Create a new view called *track_url* and a new url mapping called *goto* that maps the url *rango/goto* to the view *track_url*.
	* The track_url view will examine the GET request parameters and pull out the url and page_id.
	 	* i.e. assume the GET requests will be something like: rango/goto/?page_id=1&url=http://www.example.com
	
		* The view will then find page associated with the url/page_id given the parameterization and increment the views field.
		* Then it will redirect the user to the specified url using Django's redirect method.
		* However, if there are no parameters in the GET request for url and page_id, or the parameters do not return a Page object, then redirect the to the index view/page.
		
	* Update the *category.html* so that it uses *rango/goto/?page_id=XXX&url=YYY* instead of directly providing the url YYY for users to click.

Make the application more dynamic with AJAX
-------------------------------------------
To make the interaction with the Rango application more seamless you can add in a number of features that use AJAX. If you haven't used AJAX before or would like to know more about it before using it, check out the AJAX tutorial provided by the W3C Schools: http://www.w3schools.com/ajax/default.ASP

It provides a pretty good introduction to how existing technologies are integrated to reduce the number of page loads, and instead let only parts of a web page be reloaded.

To simplify the AJAX components you can use a library like JQuery. If you are using the Twitter CSS Bootstrap toolkit then JQuery will already be added in. Otherwise, download the latest version of JQuery and include it within your application.

To include JQuery within your application, in the static folder create a *js* folder and plonk the JQuery javascript file (jquery.js) here. Then in your *base* template in the <head> element, include:

::

	 <script src="{{STATIC_URL}}/js/jquery.js"></script>
	
If you aren't familiar with JQuery it is worth checking out http://jquery.com
 and going through some examples in the documentation. The documentation provides numerous worked examples of the different functionality that the JQuery API provides.	


Add inline category suggestions
...............................
It would be really neat if we could provide a fast way for users to find a category, rather than browsing through a long list. To do this you can create a suggestion component which lets users type in a letter or part of a word, and then the system response by providing a list of suggested categories, that the user can select from. As the user types a series of requests will be made to the server to fetch the suggested categories relevant to what the user has entered. To do this you will need to do the following:


	* Parameterized the function *get_category_list* such that its definition is as follows:
	
		::
	
			def get_category_list(max_results=0, starts_with=''):


		* Update the function such that it returns all categories in Category if *max_results* equals zero and *start_with* is an empty string or null.
		* If *max_results* is greater than zero (and an integer) then the maximum number of results returned is determined by *max_results*.
		* If *starts_with* is non-empty, then all categories that start with this string are resulted up to the number of *max_results* (under *max_results* is zero, in which case all matching categories are returned)
		* The function returns a list of category objects annotated with the encoded category denoted by the attribute, *url*


	* Create a view called *suggest_category* which will examine the request and pick out the category query string.
		* Assume that a GET request is made and attempt to get the *query* attribute.
		* If the query string is not empty, ask the Category model to get the top 8 categories that start with the query string.
		* The list of category objects will then be combined into a piece of XHTML via template. 
		
	* Create a template called *suggestions.html* that will iterate through each category in the list and put them into a XHTML list. 
	
		* Hold up a second......
		* The template will be very similar in nature to the *category_list.html* you created to populate the sidebar. 
		* In fact it is more or less identical, in which case you can re-use it and save on creating needless templates.
		* This piece of XHTML of the categories will be returned to the client which will need to inject this XHTML within the search page.
	
	* To let the client ask for this data, you will need to create a url mapping lets call it *cat_suggestions*
	
	* With the mapping, view, and template for this view in place, you will now need to update the *base.html* template and add in some javascript so that the categories can be displayed as the user types.
	
	
	* In the *base.html* template, modify the sidebar block so that a div with an id="cats" encapsulates the categories being presented. The JQuery/AJAX will update this element.
	
		* Above this <div> add an input box for a user to enter the letters of a category, i.e.:
	
			::
				<INPUT type="text" size="30" name="suggestion" value="" id="suggestion">
		
		* Create a javascript file in *static/js* called, *ajax-examples.js*. 		
		* Include it within your *base.html* with: 
	
			::
			
				<script src="{{STATIC_URL}}/js/ajax-examples.js"></script>	
		
	* With these elements added into the templates, you can now create the JQuery to update the categories list as the user types.
		* Associate an on keypress event handler to the *input* with id="suggestion"
			* $('#suggestion').keyup(function(){ ... })
		* On keyup, issue an ajax call to retrieve the updated categories list
			* Then use the JQuery *.get()* function i.e. *$(this).get( ... )*
		* If the call is successful, replace the content of the <div> with id="cats" with the data received.
			* Here you can use the JQuery *.html()* function i.e. *$('#cats').html( data )*
		
	* Bam! You now have category suggestion operational in your application.
		* How sweet is that?
	


Add a "Like Button" to let registered users "like" a particular category
.........................................................................

	* Create a new field called, *Likes* as an integer within the *Category* model.
		* Delete your database file 
		* Re-sync your database, with *python manage.py syncdb*.
		* It is always good to do this to make sure that you are using the up to date database.
		* Check the tables created with *python manage.py sql rango*. For the  Category table you should see the additional field: "likes".
		* Update your population script to set the value of likes equal zero for each category. Then repopulate your database.
		* Update the *add_category* view to set the default value of likes equal to zero, as well.
		* Update the CategoryForm to only include field 'name', i.e. put: 
			* fields = ('name') in the Meta class.
		
		
	* In the *category.html* template:
		* Add in a "Like" button with id="like".
		* Add in a template tag to display the number of likes: {{% likes %}}
		* Place this inside a div with id="like_count": <div id="like_count">{{ likes }} </div>
		* This sets up the template to capture likes and to display likes.
		
	* Now in the *category* view you will need to provide the template with these category details.
		
		
	* Create a view called, *like_category* which will examine the request and pick out the category_id and then increment the number of likes for that category.
		* Assume that it is a GET request.
		* Don't forgot to add in a the url mapping; so map the view to *rango/cat_like/*
		* i.e. the GET request will then be rango/cat_like/?category_id=XXX
		* The view's response is simply the new total number of likes for that category.
		
	* Now in "ajax-examples.js" you will need to add some JQuery to perform an AJAX GET request.
		* If the request is successful, then update the #like_count element, and hide the like button.
		
	

Add an "Add to Category Button" 
...............................
To let registered users quickly and easier add a Page to the Category in which they are searching.

* tba
