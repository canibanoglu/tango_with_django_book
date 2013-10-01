.. _requirements-label:

Getting Ready to Tango
======================

Before you can begin to tango with Django, you need to ensure that you have everything you need installed on your computer, and that you have a sound understanding of your development environment. This chapter walks you through what you need and what you need to know.

For this tutorial, you'll require the following key pieces of software.

* Python 2.7.5
* Django 1.5.4

As Django is a web application framework written in the Python programming language, you will be required to have a working knowledge of Python. If you haven't used Python before or you simply wish to brush up on your skills, then we highly recommend that you check out and work through one or more of the following guides:

* **A quick tutorial** - Learn Python in 10 Minutes by Stavros, http://www.korokithakis.net/tutorials/python/
* **The Official Python Tutorial**, http://docs.python.org/2/tutorial/
* **A brilliant book**: Think Python: How to Think like a Computer Scientist by Allen B. Bowney, http://www.greenteapress.com/thinkpython/
* **An amazing online course**: Learn to Program, by Jennifer Campbell and Paul Gries, https://www.coursera.org/course/programming1


Using the Terminal
------------------
In order to set up your environment learning how to use the Command Line Interpreter (CLI) provided by your Operating System is really important. Through the course of this tutorial you will be interacting with the CLI routinely. If you are already familiar with using the command line interface you can skip directly to :ref:`Installing the Software <installing-software>` Section.

UNIX-based operating systems all use a similar-looking `terminal <http://www.ee.surrey.ac.uk/Teaching/Unix/unixintro.html>`_. Descendants, derivatives and clones of UNIX include `Apple's OS X <http://en.wikipedia.org/wiki/OS_X>`_ and the `many available Linux distributions <http://en.wikipedia.org/wiki/List_of_Linux_distributions>`_ available today. All of these operating systems contain a core set of commands which help you navigate through your filesystem and launch programs, all without the need of any graphical interface. This section provides the key commands you should familiarise yourself with.

.. note:: This tutorial is focused towards users of UNIX-based or UNIX-derived operating systems. While Python and Django can run in a Windows-based environment, many of the commands that we use in this book are for  UNIX-based terminals. These commands can however be replicated in Windows by using the graphical user interface, `using the relevant command in a Windows Command Prompt <http://www.ai.uga.edu/mc/winforunix.html>`_, or using `Windows PowerShell <http://technet.microsoft.com/en-us/library/bb978526.aspx>`_ which provides an CLI like UNIX.

Upon launching a new terminal instance, you'll typically be presented with something like:

::
	
	sibu:~ john$

This is called the *prompt*, where the system is waiting to execute your every command. The prompt you see varies depending on the operating system you are using, but all look generally very similar. In the example above, there are three key pieces of information to observe:

* your username and computer name (username of ``john`` and computer name of ``sibu``);
* your *current working directory* (the tilde, or ``~``); and
* the privilege of your user account (the dollar sign, or ``$``).

The dollar sign (``$``) typically indicates that the user is a standard user account. Conversely, a hash symbol (``#``) may be used to signify the user logged in has `root privileges <http://en.wikipedia.org/wiki/Superuser>`_. Whatever symbol is present is used to signify that the computer is awaiting your input. 

Open up a Terminal window and see what your prompt looks like.

When you are using the Terminal, it is important to know where you are in the file system. To find out where you are issue the command ``pwd``. This will display your present working directory. For example:

::
	
	Last login: Mon Sep 23 11:35:44 on ttys003
	Leifs-MacBook-Air:~ leif$ pwd
	/Users/leif
	Leifs-MacBook-Air:~ leif$

You can see that my present working directory is: /User/leif.

 Also, you will note that my prompt indicates that my present working directory is ~. This is because the tilde (``~``) represents your *home* directory. Whenever we refer to <workspace> will will be referring to your home directory.

Note that the base directory in any UNIX-based file system is the root directory and it is denoted by a single forward slash (``/``).

If you are not in your home directory you can change directory (cd) to your home directory by issuing:

::
	
	$ cd ~

Let's create a directory called code, to do this use the make directory command (mkdir):

::
	
	$ mkdir code
	
And to move to this directory enter, ``cd code``. If you now check your present working directory you'll notice that you will be in <workspace>/code/. This may also be reflected by your prompt, for example:

::
	
	Leifs-MacBook-Air:~ leif$ mkdir code
	Leifs-MacBook-Air:~ leif$ cd code
	Leifs-MacBook-Air:code leif$ 
	Leifs-MacBook-Air:code leif$ pwd
	/Users/leif/code
	

To list the files that are in a directory you can issue the command `ls`. And to see hidden files (if any) you can issue the command `ls -a`. If you cd back to your home directory ( cd ~) and then issue ls, you'll see that you have something called ``code`` in your home directory. To find out a bit more about what is in your directory issue ``ls -l``. This will provide a more detailed listing of the files and whether it is a directory or not (denoted by a ``d`` at the start of the line):

::
	
	Leifs-MacBook-Air:~ leif$ cd ~ 
	Leifs-MacBook-Air:~ leif$ ls -l 
	
	drwxr-xr-x   36 leif  staff    1224 23 Sep 10:42 code


The output also contains information on the permissions associated to the directory, who created it (leif), the group (staff), the size, date/time and name. 

Below we have provided a summary of some of the other commands that you will find useful. One last thing that you will find useful, is that sometimes it is helpful to be able to edit files within your console. A simple CLI editor is called pico or nano (depending on the operating system). It is easy to use and can be picked up within minutes (unlike vi, for instance).


Core Commands
*************
All UNIX-based operating systems come with a series of built-in commands, with most focusing exclusively on file management. The commands you will use most frequently are listed below, each with a short explanation on what they do and how to use them.

- ``pwd``: *Prints* your current *working directory* to the terminal. The full path of where you are presently is displayed.
- ``ls``: Prints a list of files in the current working directory to the terminal. By default, you do not see the sizes of files - this can be achieved by appending ``-lh`` to ``ls``, giving the command ``ls -lh``.
- ``cd``: In conjunction with a path, allows you to *change* your current working *directory*. For example, the command ``cd /home/john/`` changes the current working directory to ``/home/john/``. You can also move up a directory level without having to provide the `absolute path <http://www.uvsc.edu/disted/decourses/dgm/2120/IN/steinja/lessons/06/06_04.html>`_ by using two dots, e.g. ``cd ..``.
- ``cp``: Copies files and/or directories. You must provide the *source* and the *target*. For example, to make a copy of the file ``input.py`` in the same directory, you could issue the command ``cp input.py input_backup.py``.
- ``mv``: Moves files/directories. Like ``cp``, you must provide the *source* and *target*. This command is also used to rename files. For example, to rename ``numbers.txt`` to ``letters.txt``, issue the command ``mv numbers.txt letters.txt``. To move a file to a different directory, you would supply either an absolute or relative path as part of the target - like ``mv numbers.txt /home/david/numbers.txt``.
- ``mkdir``: Creates a directory in your current working directory. You need to supply a name for the new directory after the ``mkdir`` command. For example, if your current working directory was ``/home/john/`` and you ran ``mkdir music``, you would then have a directory ``/home/john/music/``. You will need to then ``cd`` into the newly created directory to access it.
- ``rm``: Shorthand for *remove*, this command removes or deletes files from your filesystem. You must supply the filename(s) you wish to remove. Upon issuing a ``rm`` command, you will be prompted if you wish to delete the file(s) selected. You can also remove directories `using the recursive switch <http://www.computerhope.com/issues/ch000798.htm>`_. Be careful with this command - recovering deleted files is very difficult, if not impossible!
- ``rmdir``: An alternative command to remove directories from your filesystem. Provide a directory that you wish to remove. Again, be careful: you will not be prompted to confirm your intentions.
- ``sudo``: A program which allows you to run commands with the security privileges of another user. Typically, the program is used to run other programs as ``root`` - the `superuser <http://en.wikipedia.org/wiki/Superuser>`_ of any UNIX-based or UNIX-derived operating system.

.. note:: This is only a brief list of commands. Check out ubuntu's documentation on `Using the Terminal <https://help.ubuntu.com/community/UsingTheTerminal>`_  for a more detailed overview, or the `Cheat Sheet 
 <http://fosswire.com/post/2007/08/unixlinux-command-cheat-sheet/>`_ by FOSSwire for a quick reference guide.


.. _installing-software:

Installing the Software
-----------------------
Now that you have a decent understanding of how to interact with the terminal, you can begin to install the software required for this tutorial.

Installing Python
*****************
So, how do you go about installing Python 2.7.5 on your computer? You may already have Python installed on your computer - and if you are using a Linux distribution or OS X, you will definitely have it installed. Some of your operating system's functionality `is implemented in Python <http://en.wikipedia.org/wiki/Yellowdog_Updater,_Modified>`_, hence the need for an interpreter!

Unfortunately, nearly all modern operating systems utilise a version of Python that is older than what we require for this tutorial. There's many different ways in which you can install Python, and many of them are sadly rather tricky to accomplish. We demonstrate the most commonly used approaches, and provide links to additional reading for more information.

.. warning:: This section will detail how to run Python 2.7.5 *alongside* your current Python installation. It is regarded as poor practice to remove your operating system's default Python installation and replace it with a newer version. Doing so could render aspects of your operating system's functionality completely broken!

Apple OS X
..........
The most simple way to get Python 2.7.5 installed on your Mac is to download and run the simple installer provided on the official Python website. You can download the installer from `here <http://www.python.org/ftp/python/2.7.5/python-2.7.5-macosx10.6.dmg>`_, or by visiting the webpage http://www.python.org/getit/releases/2.7.5/.

.. warning:: Ensure that you download the ``.dmg`` file that is relevant to your particular OS X installation!

#. Once you have downloaded the ``.dmg`` file, double-click it in the Finder.
#. The file mounts as a separate disk and a new Finder window is presented to you.
#. Double-click the file ``Python.mpkg``. This will start the Python installer.
#. Continue through the various screens to the point where you are ready to install the software. You may have to provide your password to confirm that you wish to install the software.
#. Upon completion, close the installer and eject the Python disk. You can now delete the downloaded ``.dmg`` file.

You should now have an updated version of Python installed, ready for Django! Easy, huh?

Linux Distributions
...................
Unfortunately, there are many different ways in which you can download, install and run an updated version of Python on your Linux distribution. To make matters worse, methodologies vary from distribution to distribution! For example, the instructions for installing Python on `Fedora <http://fedoraproject.org/>`_ may differ from those to install it on `Ubuntu <http://www.ubuntu.com/>`_.

However, not all hope is lost. An awesome tool (or a *Python environment manager*) called `pythonbrew <https://github.com/utahta/pythonbrew>`_ can address our problem. It provides an easy way to install and manage different versions of Python, meaning you can leave your operating system's default Python installation alone. Hurrah!

Taken from the instructions provided `here <https://github.com/utahta/pythonbrew>`_ and `here <http://stackoverflow.com/questions/5233536/python-2-7-on-ubuntu>`_, the following steps will install Python 2.7.5 on your Linux distribution.

#. Open a new terminal instance.
#. Run the command ``curl -kL http://xrl.us/pythonbrewinstall | bash``. This will download the installer and run it within your terminal for you. This installs pythonbrew into the directory ``~/.pythonbrew``. Remember, the tilde (~) represents your home directory!
#. You then need to edit the file ``~/.bashrc``. In a text editor (such as gedit, nano, vim or emacs), add the following to a new line at the end of ``~/.bashrc``: ``[[ -s $HOME/.pythonbrew/etc/bashrc ]] && source $HOME/.pythonbrew/etc/bashrc``
#. Once you have saved the updated ``~/.bashrc`` file, close your terminal and open a new one. This allows the changes you make to take effect.
#. Run the command ``pythonbrew install 2.7.5`` to install Python 2.7.5.
#. You then have to *switch* Python 2.7.5 to the *active* Python installation. Do this by running the command ``pythonbrew switch 2.7.5``.
#. Python 2.7.5 should now be installed and ready to go.

.. note:: Directories and files beginning with a period or dot can be considered the equivalent of *hidden files* in Windows. `Dot files <http://en.wikipedia.org/wiki/Dot-file>`_ are not normally visible to directory-browsing tools, and are commonly used for configuration files. You can use the ``ls`` command to view hidden files by adding the ``-a`` switch (short for *all*) to the end of the command, giving the command ``ls -a``.

.. _requirements-install-python-windows:

Windows
.......
By default, Microsoft Windows comes with no installations of Python. This means that you do not have to worry about leaving existing versions be; installing from scratch should work just fine. You can download a 64-bit version of Python from `here <http://www.python.org/ftp/python/2.7.5/python-2.7.5.amd64.msi>`_, or a 32-bit version of Python from `here <http://www.python.org/ftp/python/2.7.5/python-2.7.5.msi>`_. If you aren't sure which one to download, you can determine if your computer is 32-bit or 64-bit by looking at the instructions provided `on the Microsoft website <http://windows.microsoft.com/en-gb/windows7/32-bit-and-64-bit-windows-frequently-asked-questions>`_.

#. When the installer is downloaded, open the file from the location to which you downloaded it.
#. Follow the on-screen prompts to install Python.
#. Close the installer once completed, and delete the downloaded file.

Once the installer is complete, you should have a working version of Python ready to go. By default, Python 2.7.5 is installed to the folder ``C:\Python27``. We recommend that you leave the path as it is.

Upon the completion of the installation, open a command prompt and enter the command ``python``. If you see the Python prompt, installation was successful. However, in certain circumstances, the installer may not set your Windows installation's ``PATH`` environment variable correctly. This will result in the ``python`` command not being found. Under Windows 7, you can rectify this by performing the following:

#. Click the *Start* button, right click *My Computer* and select *Properties*.
#. Click the *Advanced* tab.
#. Click the *Environment Variables* button.
#. In the *System variables* list, find the variable called *Path*, click it, then click the *Edit* button.
#. At the end of the line, enter ``;C:\python27;C:\python27\scripts``. Don't forget the semicolon - and certainly *do not* add a space.
#. Click OK to save your changes in each window.
#. Close any Command Prompt instances, open a new instance, and try run the ``python`` command again.

This should get your Python installation fully working. For Windows XP, `this <http://www.computerhope.com/issues/ch000549.htm>`_ tutorial demonstrates how to set your path variable, and `this <http://stackoverflow.com/a/14224786>`_ answer shows you how to get to the path modification dialog in Windows 8.

Setting Up the ``PYTHONPATH``
*****************************
With Python now installed, we now need to check that the installation was successful. To do this, we need to check that the ``PYTHONPATH``
`environment variable <http://en.wikipedia.org/wiki/Environment_variable>`_ is setup correctly. ``PYTHONPATH`` provides the Python interpreter with the location of additional Python `packages and modules <http://stackoverflow.com/questions/7948494/whats-the-difference-between-a-python-module-and-a-python-package>`_ which add extra functionality to the base Python installation. Without a correctly set ``PYTHONPATH``, we'll be unable to install Django!

First, let's verify that our ``PYTHONPATH`` variable exists. Depending on the installation technique you chose, this may or may not have been done for you. To do this on your UNIX-based or UNIX-derived operating system, issue the following command in a terminal.

``$ echo $PYTHONPATH``

On a Windows-based machine, open a Command Prompt and issue the following command.

``$ echo %PYTHONPATH%``

If all works, you should then see output that looks something similar to the example below. On a Windows-based machine, you will obviously see a Windows path, most likely originating from the C drive.

``/opt/local/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/site-packages:``

This is the path to your Python installation's ``site-packages`` directory, where additional Python packages and modules are stored. If you see a path, you can continue to the next part of this tutorial. However, if you do not see anything, you'll need to do a little bit of detective work to find out the path. On a Windows installation, this should be a trivial exercise: ``site-packages`` is located within the ``lib`` folder of your Python installation directory. For example, if you installed Python to ``C:\Python27``, ``site-packages`` will be at ``C:\Python27\Lib\site-packages\``.

UNIX-based and UNIX-derived operating systems however require a little bit of detective work to discover the path of your ``site-packages`` installation. To do this, launch the Python interpreter. The following terminal session demonstrates the commands you should issue.

.. code-block:: python
	
	$ python
	
	Python 2.7.5 (v2.7.5:ab05e7dd2788, May 13 2013, 13:18:45) 
	[GCC 4.2.1 (Apple Inc. build 5666) (dot 3)] on darwin
	Type "help", "copyright", "credits" or "license" for more information.
	
	>>> import site
	>>> print site.getsitepackages()[0]
	
	'/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/site-packages'
	
	>>> quit()

Calling ``site.getsitepackages()`` returns a list of paths that point to additional Python package and module stores. The first typically returns the path to your ``site-packages`` directory - changing the list index position may be required depending on your installation. If you receive an error stating that ``getsitepackages()`` is not present within the ``site`` module, verify you're running the correct version of Python. Version 2.7.5 should include the function you need to call. Previous versions of the interpreter do not possess this functionality.
	
The string which is shown as a result of executing ``print site.getsitepackages()[0]`` is the path to your installation's ``site-packages`` directory. Taking the path, we now need to add it to your configuration. On a UNIX-based or UNIX-derived operating system, edit your ``.bashrc`` file once more, adding the following to the bottom of the file.

``export PYTHONPATH=$PYTHONPATH:<PATH_TO_SITE-PACKAGES>``

Replace ``<PATH_TO_SITE-PACKAGES>`` with the path to your ``site-packages`` directory. Save the file, and quit and reopen any instances of your terminal.

On a Windows-based computer, you must follow the instructions shown in Section :num:`requirements-install-python-windows` to bring up the environment variables settings dialog. Add a ``PYTHONPATH`` variable with the value being set to your ``site-packages`` folder, which is typically ``C:\Python27\Lib\site-packages\``.

Pip, the Python Package Manager
*******************************

Installing and setting up your development environment is a really important part of any project. While it is possible to install Python Packages such as Django separately, this can lead to numerous problems and hassles later on. For example, how would you share your set up with another developer,  how would you set up the same environment on your new machine, how would you upgrade to the latest version of the package? Using a package manager removes much of the hassle involved in setting up and configuring your environment. It will also ensure that the package you install is the correct for the version of Python you are using along with installing any other packages that are dependent upon the one you want to install.

We will be using the *Pip* package manager. Download the installer ``get-pip.py`` from the `Pip website <http://www.pip-installer.org/en/latest/installing.html>`_. This can be easily done via the terminal (on UNIX-based Operating systems):

::
	
	
	``$ curl -O https://raw.github.com/pypa/pip/master/contrib/get-pip.py``
	
	``$ python get-pip.py``


The first command executes ``curl``, a program used for transferring files. It will download the ``get-pip.py`` file to your current working directory. If ``curl`` cannot retrieve the file, try ``http`` instead of ``https`` in the URL path.

The second command then executes the ``get-pip.py`` file using the Python interpreter. Note that you may have to use``sudo python get-pip.py`` depending on your accounts privileges. 

Windows-based computers do not natively come with a ``curl`` equivalent. To retrieve the required ``get-pip.py`` file for Windows, access the URL with your web browser, and save the file to somewhere on your hard drive. With a command prompt set to the folder where the downloaded file is located, you can open a Command Prompt, navigate to the directory in which the ``.py`` file was downloaded to, and issue the following command.

``$ python get-pip.py``

This will launch the Python script, and download everything to the correct location.

Once you have run the installation process for Pip, you should be able to launch Pip from your terminal. To do so, just type ``pip``. This should present you with a list of commands and switches that Pip accepts.

Installing Django
*****************
Once the Python package manager Pip is successfully installed on your computer, installing Django is easy. Open a Command Prompt or terminal window, and issue the following command.

``$ pip install -U django==1.5.4``

If you are using a UNIX-based or UNIX-derived operating system and receive complaints about insufficient permissions, you will need to run the command with elevated privileges using the ``sudo`` command. If this is the case, you must then run the following command instead.

``$ sudo pip install -U django==1.5.4``

The package manager will download Django and install it in the correct location for you. Upon completion, Django should be successfully installed. Note, if you didn't include the `==1.5.4` then the most recent version of Django would be installed.


Installing Python Imaging Library
*********************************
During the course of building Rango, we will be uploading and handling images. This means we will need support from the Python Imaging Library, to install this package issue the following command:

``$ pip install pil``

Again, use sudo, if required.


Installing Other Python Packages
********************************
It is worth noting that additional Python packages can be easily downloaded using the same manner. `The Python Package Index <https://pypi.python.org/pypi>`_ provides a listing of all the packages available through Pip.

To get a list of the packages installed:

``$ pip list``


Sharing your Package List
*************************
You can also get a list of the packages installed in a format that can be shared with other developers. To do this issue:

``$ pip freeze > requirements.txt``

If you examine ``requirements.txt`` using either the command ``more`` or ``cat`` requirements.txt you will see the same information but in a slightly different format. The ``requirements.txt`` can then use to install the same setup by issuing:

``$ pip install -r requirements.txt --no-index --find-links``


Integrated Development Environment
----------------------------------
While not absolutely necessary, a good Python-based integrated development environment (IDE) can be very helpful to you during the development process. Several exist, with perhaps JetBrains' *PyCharm* and *PyDev* (a plugin of the `Eclipse IDE <http://www.eclipse.org/downloads/>`_) standing out as popular choices. The `Python Wiki <http://wiki.python.org/moin/IntegratedDevelopmentEnvironments>`_ provides an up-to-date list of Python IDEs. Research which one is right for you - and be aware that some may require you to purchase a licence. Ideally, you'll want to select an IDE that supports integration with Django. PyCharm and PyDev both support Django integration out of the box - though you will have to point the IDE to the version of Python that you are using.

Exercises
---------

* Install Python 2.7.5 and Pip
* Play around with your CLI and create a directory called *code*, which we use to create our projects in.
* Install Django and Pil

Virtual Environments
********************
So we are almost all set to go. However, before we continue it is worth pointing out that while this setup is fine to begin with it has some drawbacks. What if you had another Python application that requires a different version to run? Or you wanted to switch to the new version of Django, but still wanted to maintain your Django 1.5.4 project?

The solution to this is to use `virtual environments <http://simononsoftware.com/virtualenv-tutorial/>`_. Virtual environments allow multiple installations of Python and their relevant packages to exist in harmony, without disrupting one another. This is the generally accepted approach to configuring a Python setup nowadays. We don't go into much detail about them in this chapter because of their complexity, but in the chapter on :ref:`Deploying your Application<virtual-environment>` we will go through setting up a virtual environment. However, if you are really keen you check out `A non-magical introduction to Pip and Virtualenv for Python Beginners <http://dabapps.com/blog/introduction-to-pip-and-virtualenv-python/>`_ by Jamie Matthews.


Code Repository
***************
Another thing we should point out is that when you develop code you should always house your code within a repository such as SVN or GIT. We wont be going through this right now, so that we can get stuck into developing an application in Django, however, in the Append we have provided a :ref:`crash course on GIT <git-crash-course>`. We highly recommend that you set up a GIT repository for your own projects.





