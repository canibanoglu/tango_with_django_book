.. _tango-too-label:
Doing the Tango with Rango!
===========================

The template code snippets below assume you are using the Twitter's CSS Bootstrap Toolkit.

Hopefully, you will have been able to complete the exercises in the previous chapter. Otherwise, you can checkout snippets of code and use them within your version of Rango.

Let users view their profile 
.............................

	* Create a template called, *profile.html*. In this template add in the fields associated with the user profile and the user (i.e. username, email, website and picture, etc)
	
	::
	
		
		{% block content %}
		<H2>{{user.username}}</H2>

		<a href="{{userprofile.website}}">{{userprofile.website}}</a>

		<img src="{{MEDIA_URL}}{{userprofile.picture}}" />

		{% endblock %}
	
	
	
	* Add a button/link called *Profile* which is only available to the user who's profile page it is. i.e. the user needs to be logged in and authenticated, and the profile on display must be their profile. Place this in your *base.html*:
	
	::
	
		{% if user.is_authenticated %}
		<li><a href="/rango/profile" >Profile</a></li>
		{% endif %}
	
	
	
	
	* Create a view called, *profile*, this view will obtain the data required to render the user profile template/pages.
	
	
	::
		
		
		@login_required
		def profile(request):
			context = RequestContext(request)
			cat_list = get_category_list()
			u = User.objects.get(username=request.user)
			try:
				
				up = UserProfile.objects.get(user=u)
			except:
				up = None
			
			return render_to_response('rango/profile.html', {'user':u, 'userprofile': up, 'cat_list': cat_list }, context)
		
		
	* Create a url mapping of the form, *rango/profile/* that maps to the *show_profile* view i.e: add *url(r'^profile/$', views.profile, name='profile'),* to urlpatterns.
	




Provide categories on every page
.................................

* Create a *category_list.html* template and migrate the template code for presenting the categories list from  *index.html* to this new template.


*category_list.html* template:

::

	
	<UL class="nav nav-list">
		<li class="nav-header">Category List</li>
		{% if cat_list %}
			{% for cat in cat_list %}
				<LI><A HREF="/rango/cat/{{ cat.url }}">{{ cat.name }}</A></LI>
			{% endfor %}
		{% else %}
			<LI>No categories at present.</LI>
		{% endif %}
		<LI><A HREF="/rango/cat_add"> Add category</A></LI>
	</UL>



* Use the {% include "rango/category_list.html" %} to now include this template code into the *base.html* template within the sidebar.

*base.html* template:

::
	

	 <div class="well sidebar-nav">

		{% if cat_list %}
		<UL class="nav nav-list">
			<li class="nav-header">Find a Category</li>
			<form>
			<label></label>
			<li><input  class="input-medium search-query" type="text" name="suggestion" value="" id="suggestion" /></li>
			</form>
			</ul>
			<div id="cats">
			{% include 'rango/category_list.html' with cat_list=cat_list %}
			</div>	
		{% endif %}

  		 {% block sidebar %}

		 {% endblock %}
      </div><!--/.well -->


* Create a function called *get_category_list* that returns the list of categories.

::
	
	
	def get_category_list(max_results=0, starts_with=''):
	    cat_list = []
	    if starts_with:
	        cat_list = Category.objects.filter(name__startswith=starts_with)
	    else:
	        cat_list = Category.objects.all()

	    if max_results > 0:
	        if len(cat_list) > max_results:
	            cat_list = cat_list[:max_results]

	    for cat in cat_list:
	        cat.url = encode_category(cat.name)

	    return cat_list


	* Then call this function in each of the views that you want to display the category list in the sidebar.
	
	
	::
	
	
		def index(request): 
		    #    return HttpResponse("Rango say: Hello World!")
		    cat_list = get_category_list()
			....
			
			
	* Pass the category list data (cat_list) through to the template to be rendered. For example:
	
	
	::
	
		context = RequestContext(request, { 'cat_list' : cat_list })
		
		
		

Restricting add category/pages 
...............................

* Decorate the *add_page* and *add_category* views with the  @login_required decorator method.
	
	
	::
	
		@login_required
		def add_category(request):
			...
			
			
		@login_required	
		def add_page(request, category_name_url):
			...
	
	
* Optionally, only show the "add new page" and "add new category" functionality to the user if the user is authenticated.
* If you don't do this then users will be redirected to the login page (which is fine too).
	
	
	::
		
		{% if user.is_authenticated %}
		<LI><A HREF="/rango/cat_add"> Add category</A></LI>
		{% endif %}
	




	
	
Add a "Like Button" 
...................

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







* In the *category.html* template:
	* Add in a "Like" button with id="like".
	* Add in a template tag to display the number of likes: {{% likes %}}
	* Place this inside a div with id="like_count": <div id="like_count">{{ likes }} </div>

::

	<div>
	<b id="like_count">{{ likes}}</b> people like this category 
	<button id ="likes" data-catid="{{category_id}}" class="btn btn-mini btn-primary" type="button">Like</button>
	<div>



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
		

