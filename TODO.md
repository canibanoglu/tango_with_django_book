Chapter 1
	- fix erd diagram

Chapter 2
	- e-mail from Mike Gleen
		- setup Ubuntu 13.4 and try work through and replicate his problems.
		- chapter probably needs some considerable work.

Chapter 5
- take population script from rst put it into the code base
- update the screen shot of the admin interface

Chapter 7
- Courtesy of Can Obanoglu - In 7.2.1, we start a paragraph with "Besides the CharField and IntegerField widget, many more are available for use." It would be a good idea to add some more fields to our models that use something OTHER than the trivial IntegerField. So maybe we could add a DatePicker or something to specify when an instance was created or something? A good idea!




Do we want a note alerting people to the fact that if you forget to add a slash to the end of a URL, you could end up with problems?
Where could we put this warning?

Deployment Chapter
- add list of packages to be installed other than Django and pil

Courtesy of pywebdesign
- What about refactoring to use class-based views?
- Adding a chapter to show how django-allauth could be used to integrate signups.

CLASS-BASED VIEWS (@pywebdesign)
================================
Function based views are very useful and adequate for many other uses which means that an optimal tutorial should cover them too. I find myself looking at your code in views.py and trying to think of a solution to teach class based views without eclipsing function based view! Here's my shortly tough solution:

Make an optional chapter where you teach to convert some views to class based one? This seams to be great and can also allow you to discuss the topic of class based VS function based views. Maybe you will run in some compatibility problem tough, if someone can't follow the tutorial after doing this conversion. The optional chapter should then be made to be read at the end.

With this, your tutorial could become a cornerstone to learning django, I think.

about allauth: here's 2 interesting tutorial:
http://www.sarahhagstrom.com/2013/09/the-missing-django-allauth-tutorial/
https://speakerdeck.com/tedtieken/signing-up-and-signing-in-users-in-django-with-django-allauth