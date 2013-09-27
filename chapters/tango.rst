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


