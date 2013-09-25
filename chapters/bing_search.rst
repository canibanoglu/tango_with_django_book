.. _bing-label:

Adding External Search Functionality
====================================
At this stage, our Rango application is looking pretty good - a majority of our required functionality is implemented. However, instead of simply using Rango as a webpage directory, why not add some search functionality too? By doing so, Rango can then mimic first-generation web search engines, where pages were also provided in a categorised format.

To provide this functionality, we'll need to call upon the services of an external search engine to help us provide search results. Creating a web search engine of our own is well outside the scope of this book - so why not figure out how to call upon someone else's? In this chapter, we'll be making use of the *Bing Search Application Programming Interface* - or *Bing Search API* for short.

.. note:: According to the `relevant article on Wikipedia <http://en.wikipedia.org/wiki/Application_programming_interface>`_, an *Application Programming Interface (API)* specifies how software components should interact with one another. In the context of web applications, an API is considered as a set of HTTP requests along with a definition of the structures of response messages that each request can return. Any meaningful service that can be offered over the Internet can have its own API - we aren't limited to web search. For more information on web APIs, `Luis Rei provides an excellent tutorial on APIs <http://blog.luisrei.com/articles/rest.html>`_.

The Bing Search API
-------------------
The Bing Search API provides you with the ability to embed search results from the Bing search engine within your own applications. Through a straightforward interface, you can request results from Bing's servers to be returned in either XML or JSON. The data returned can be interpreted by a XML or JSON parser, with the results then rendered as part of a template within your application. Easy!

.. note:: Many different web APIs provide the ability to specify in what format results are returned to the requesting computer. For an example, `check out the documentation for the Echo Nest's API <http://developer.echonest.com/raw_tutorials/responses.html>`_. Note that the same data is returned in the two examples - the format in which the data is presented is the only difference. In many cases, using JSON is the preferred approach as it is more compact and thus requires less bandwidth.

Although the Bing API can handle requests for different kinds of content, we'll be focusing on web search only for this tutorial - as well as handling JSON responses. To use the API, we need to sign up for an API key which gives us the access to 5000 free queries per month.

Registering for a Bing API Key
..............................
To register for a Bing API key, you must first register for a free Microsoft account, which gives you access to a range of Microsoft services. If you have a Hotmail account, you already probably have one! You can create an account and login at `https://account.windowsazure.com/` - or if you 



By signing up for a free account, you gain the ability to perform up to 5000 queries per month. The API allows you to query for webpages, images and news articles. 


Instead of just browsing the directory of web pages in Rango - lets add in some web search functionality as well. To do this you will require the services of the Bing Search API. To gain access to this search you will need to register with Windows Azure Marketplace. Currently, the *Bing Search Application Programming Interface*, or *Bing API* provides 5000 transactions for free per month.

The Bing Search API
-------------------
The Bing Search API enables you to embed and customize search results in your application. The results returned by the API are either in XML or JSON. For the purposes of this tutorial, you will be working with the JSON results, the format of the results is detailed in the FAQ available at: https://datamarket.azure.com/dataset/5BA839F1-12CE-4CCE-BF57-A49D98D29A44

The Bing Search API is pretty straightforward. Essentially, it lets you send a query to Bing and returns a list of results. Results can be Web, Image, News, etc, but in this tutorial you will be using the Web results.

Register for a Bing API Key
...........................
Visit https://accountservices.passport.net and sign up for an Windows Azure Marketplace account and then register to use the Bing Search API at: https://datamarket.azure.com/dataset/5BA839F1-12CE-4CCE-BF57-A49D98D29A44

Add in search functionality
...........................
Create a Python module called *bing_search.py* and add the code below to it. This code sends a request to the Bing Search API and receives a JSON response from Bing containing the web results for the given query (defined by the *search_terms* string). Make sure that you add in your *bing_api_key*.


::

	import urllib, urllib2
	import json

	def run_query(search_terms):   
	    '''Issues a query to the Bing Search API.
	    Args:
	        search_terms: is a string containing the query terms
	    Returns:
	        results: a list where each record is a dict containing: title, link and summary.
	    Raises:
	        urllib exception
	    '''
    
	    # parameters for the search request
	    root_url = 'https://api.datamarket.azure.com/Bing/Search/'
	    source = 'Web' # source could be: Image, News, RelatedSearch, Video, or Web
	    results_per_page = 10
	    offset = 0
	    # Bing API expects the query to be in quotes, and quoted! Strange but True.
	    query = "'"+ search_terms +"'" 
	    quoted_query = urllib.quote(query)
	    # Construct the URL / search request
	    search_url = "%s%s?$format=json&$top=%d&$skip=%d&Query=%s" % (root_url, source, results_per_page, offset, quoted_query)
    
	    # Add the API key to the password manager. There IS no username.
	    username = ''
	    bing_api_key = '<---INSERT-YOUR-KEY-HERE--->'
	    password_mgr = urllib2.HTTPPasswordMgrWithDefaultRealm()
	    password_mgr.add_password(None, search_url, username, bing_api_key)
    
	    results = []
	    try:
	        # Prepare an authentication handler and open the URL
	        handler = urllib2.HTTPBasicAuthHandler(password_mgr)
	        opener = urllib2.build_opener(handler)
	        urllib2.install_opener(opener)
	        response = urllib2.urlopen(search_url).read()
	        # Convert the response to json and parse out the fields (title, link, and summary)
	        json_response = json.loads(response)
	        for result in json_response['d']['results']:
	            results.append({'title': result['Title'], 'link': result['Url'], 'summary': result['Description']} )
                        
	    except urllib2.URLError, e:
	        print "Error when querying Bing API", e
            
	    return results


Notice that once the response from Bing has been returned (by the call to *urllib2.urlopen(search_url).read()* ), a json object of results is obtained (hopefully). This is because in the search_url string the format *json* has been specified. Bing also supports xml format too. The method picks through the json object, and extracts the title, url and description of each result. For more information about the parameters the search_url can handle, and the format of the response returned by the Bing API see: http://datamarket.azure.com/dataset/bing/search and check out the Migration Guide and FAQ.


Putting Search in Rango
-----------------------

To add the search functionality we will need to perform the following steps:

* Create a search.html template, to include a HTML FORM to capture the query, and template code to present results
* Update the index view to handle POST requests from the form, and if there is a POST to issue the query and return the results.



Adding a Search Box/Form and Results
....................................

Add the following HTML and template code to *search.html* template:

::

	<FORM id="search_form" method="post" action="/rango/search/">
		{% csrf_token %}
		Search:
		<INPUT type="text" size="50" name="query" value="" id="query">
		<INPUT type="submit" name="submit" value="submit" />
	</FORM>

	{% if result_list %}
		{% for result in result_list %}
			<P><A HREF="{{result.link}}">{{result.title}} </A> <BR/>
				{{result.summary}}
			</P>
		{% endfor %}

	{% endif %}

This template tries to do two things: (1) it presents a search box and search button within a form, and (2) if the template detects that there are results in result_list, then it iterates through the result_list and displays the results. 

Adding a Results View/Template
..............................
The *search* view will need to handle a POST request to issue the query, and also pass any results onto the template.
To do this create a *search* view in *rango/views.py* with the following code:

::


	from bing_search import run_query

	def search(request):
		context = RequestContext(request)
		result_list = []
		if request.method == 'POST':
	    	query = request.POST['query'].strip()
			if query:
	    		result_list = run_query(query)

		return render_to_response('rango/search.html',{ 'result_list': result_list }, context)


Finally, you'll need to:

	* add in the url mapping in *rango/urls.py*, i.e.  url(r'^search/$', views.search, name='search'),
	* update base.html and include  *<A href="/rango/search/">Search</A> |* in the *page_navbar* DIV.


Exercises
---------

	* Add a main() function to the *bing_search.py* to test out the BING Search API i.e. so when you run *python bing_search.py* it issues a query.
	* The main function should ask a user for a query (from the command line), and then issue the query to the BING API via the run_query method and print out the top ten  results returned. 
	* Print out the rank, title and url for each result.


