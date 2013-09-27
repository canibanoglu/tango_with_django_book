	
AJAX, Django and JQuery
=======================

To make the interaction with the Rango application more seamless let's add in a number of features that use AJAX, such as:
	* Add a "Like Button" to let registered users "like" a particular category
	* Add inline category suggestions - so that when a user types they can quickly find a category
	* Add an "Add Button" to let registered users quickly and easily add a Page to the Category

AJAX essentially is a combination of technologies that are integrated together to reduce the number of page loads. Instead of reloading the full page, only part of the page or the data in the page is reloaded. 	If you haven't used AJAX before or would like to know more about it before using it, check out the AJAX tutorial provided by the W3C Schools: http://www.w3schools.com/ajax/default.ASP . 

To simplify the AJAX components you can use a library like JQuery. If you are using the Twitter CSS Bootstrap toolkit then JQuery will already be added in. Otherwise, download the latest version of JQuery and include it within your application.

To include JQuery within your application, in the static folder create a *js* folder and plonk the JQuery javascript file (jquery.js) here. Then in your *base* template in the <head> element, include:

.. code-block:: html
	
	<script src="{{STATIC_URL}}/js/jquery.js"></script>

If you aren't familiar with JQuery it is worth checking out http://jquery.com and going through some examples in the documentation. The documentation provides numerous worked examples of the different functionality that the JQuery API provides.	


Add a "Like Button" 
--------------------
It would be nice to let user, who are registered, denote that they "like" a particular category. In the following workflow, we will let users "like" categories, but we will not be keeping track of what categories they have "liked", we'll be trusting them not to click the like button multiple times.


Workflow
........

To let users "like" certain categories then you will have to undertake the following workflow:

	* If you haven't done so already, create a new field called, *Likes* as an integer within the *Category* model.
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


Updating the Category Model
...........................

* Create a new field called, *Likes* as an integer within the *Category* model.

In *models.py*:

::

	class Category(models.Model):
	    name = models.CharField(max_length=128, unique=True)
	    likes = models.IntegerField()

	    def __unicode__(self):
	        return self.name


* Update the CategoryForm to only include field 'name':

::

	class CategoryForm(forms.ModelForm):
		name = forms.CharField(max_length=50, help_text='Please enter the name of the category.')
		class Meta:
		    # associate the model, Category, with the ModelForm
		    model = Category
	        fields = ('name',)





Updating Category Template
..........................

* In the *category.html* template:
	* Add in a "Like" button with id="like".
	* Add in a template tag to display the number of likes: {{% likes %}}
	* Place this inside a div with id="like_count": <div id="like_count">{{ likes }} </div>

::

	<div>
	<b id="like_count">{{ likes}}</b> people like this category 
	<button id ="likes" data-catid="{{category_id}}" class="btn btn-mini btn-primary" type="button">Like</button>
	<div>


Update the Add Category View
.............................

* Update the *add_category* view to set the default value of likes equal to zero, as well.
	* Now in the *category* view you will need to provide the template with these category details.
	
	::


		def category(request, category_name_url):
			template = loader.get_template('rango/category.html')
			cat_list = get_category_list()
			category_name = decode_category(category_name_url)
			cat = Category.objects.get(name=category_name)

			if cat:
				# selects all the pages associated with the selected category
				pages = Page.objects.filter(category=cat)
				category_id = cat.id
				likes = cat.likes
			context_dict = {'cat_list': cat_list, 'category_name_url': category_name_url, 'category_name': category_name, 'category_id': category_id, 'likes': likes, 'pages':pages }
			context = RequestContext(request, context_dict)
			return HttpResponse(template.render(context))


Create a Like Category View
...........................

* Create a view called, *like_category* which will examine the request and pick out the category_id and then increment the number of likes for that category.
	* Assume that it is a GET request.
	* Don't forgot to add in a the url mapping; so map the view to *rango/cat_like/*


::
	
	
	@login_required
	def like_category(request):
	    context = RequestContext(request)
	    cat_id = None
	    if request.method == 'GET':
	        cat_id = request.GET['category_id']
	    else:
	        cat_id = request.POST['category_id']


	    likes = 0
	    if cat_id:
	        c = Category.objects.get(id=int(cat_id))
	        if c:
	            likes = c.likes + 1
	            c.likes = likes
	            c.save()

	    return HttpResponse(likes)

Making the AJAX request
.......................

* Now in "ajax-examples.js" you will need to add some JQuery to perform an AJAX GET request.
	* If the request is successful, then update the #like_count element, and hide the like button.

	::
	
	
		$('#likes').click(function(){
	        var catid;
	        catid = $(this).attr("data-catid");
	         $.get('/rango/cat_like/', {category_id: catid}, function(data){
	                   $('#like_count').html(data);
	                   $('#likes').hide();
	               });
	    });
		

Adding inline category suggestions
----------------------------------

It would be really neat if we could provide a fast way for users to find a category, rather than browsing through a long list. To do this you can create a suggestion component which lets users type in a letter or part of a word, and then the system response by providing a list of suggested categories, that the user can select from. As the user types a series of requests will be made to the server to fetch the suggested categories relevant to what the user has entered. 


Workflow
........

To do this you will need to do the following:

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
		* This piece of HTML containing the suggested categories will be returned to the client.

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


Exercises
---------

	* Update the "like" functionality so that the application keeps track of what each user likes. i.e. you will have to add in another model to record whether the user likes a category.
	* To let registered users quickly and easily add a Page to the Category put an "Add" button next to each search result.

