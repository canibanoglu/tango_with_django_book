Online Manuals
**************
Most programs that can be launched from a terminal come with them a comprehensive form of online software documentation manual. These manuals can be easily accessed from the terminal directly by issuing the ``man`` command - short for manual. The ``man`` command should be supplied with the name of the program you wish to find out more about. For example, to discover more about the ``ls`` command, type ``man ls``. The prompt will disappear from the terminal, and you will be presented with documentation for ``ls``. Check out Figure :num:`fig-man` for a screenshot of the ``man`` program in operation.

.. _fig-man:

.. figure:: ../images/man.png
	:figclass: align-center
	
	The ``man`` program displaying the online manual for the ``ls`` command.

It's easy to navigate through documentation. The most straightforward way to do this is to use your up and down arrow keys to move up and down through the documentation respectively. Generally, the syntax of how to use the program is displayed at the top, with more in-depth discussion following after. To quit the manual and return to the prompt, simply push q on your keyboard.

.. note:: Although manual documentation is available on your computer, you can also access the manuals on the World Wide Web. Websites such as http://www.linuxmanpages.com provide manual pages for thousands of programs, all neatly categorised.

.. _requirements-installation-label:



you will be essentially creating a number of URL to view mappings. Views don't just have to return web pages. As you will see later on, they can return `JSON <http://en.wikipedia.org/wiki/JSON>`_/`XML <http://en.wikipedia.org/wiki/XML>`_ objects (useful for `AJAX requests <http://en.wikipedia.org/wiki/Ajax_(programming)>`_), or any other type of file you like (through the use of static files). More complex URL mappings that include forms of parameterisation can also be handled by Django - see `here <https://docs.djangoproject.com/en/1.5/topics/http/urls/>`_ for more information. *Creating clear, logical URL mappings is a hugely important aspect of developing a web application.*



In an attempt to make this concept easier to understand, Figure :num:`fig-relational-schema-basic-models` shows a relational schema representing the two models defined above.

.. _fig-relational-schema-basic-models:

.. figure:: ../images/relational-schema-basic-models.pdf
	:figclass: align-center

	A relational schema diagram which shows the two database tables created as a result of the Django models defined previously.
	
	
	However, there are external libraries which can perform such checks and sort out such changes, for example,

	`South <http://south.aeracode.org/>`_,. However, we don't discuss South in this tutorial. Have a look at the `official South tutorial <http://south.readthedocs.org/en/latest/tutorial/index.html>`_ if you'd like to learn more.
	
	
	Getting to grips with Django's Model-View-Template pattern is an important part of using the framework - and is often a stumbling block for many students. It can also be difficult to conform to the pattern if you have been developing in other frameworks or other tools, where you have had to do all the SQL grunt work yourself. However


	 These are stored within a Python list, demonstrated with the example taken from the Django shell below.

	.. code-block:: python

		>>> Category.objects.all()
		[<Category: home>, <Category: sport>, <Category: fun>]