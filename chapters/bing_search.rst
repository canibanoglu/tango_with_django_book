Adding External Services: Search Functionality
==============================================

Instead of just browsing the directory of web pages in Rango - lets add in some web search functionality as well. To do this you will require the services of the Bing Search API. To gain access to this search you will need to register with Windows Azure Marketplace. Currently, the Bing Search API provides 5000 transactions for free per month.

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


