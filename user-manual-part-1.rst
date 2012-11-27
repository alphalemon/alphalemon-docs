AlphaLemon CMS User Manual -1-
==============================

AlphaLemon CMS is a Content Management System application, built on top of Symfony2
Framework, really easy to use.

In this chapter you will learn how the CMS is configured, which are the predefined
block types provided with the CMS and how to manage them.

Environments
------------
What is an **Environment**? This os the definition from the Symfony2 book:

    An application can run in various environments. The different environments share the same PHP code
    (apart from the front controller), but use different configuration. 

    For instance, a dev environment will
    log warnings and errors, while a prod environment will only log errors. Some files are rebuilt on each
    request in the dev environment (for the developer's convenience), but cached in the prod environment.
    
    All environments live together on the same machine and execute the same application.

    A Symfony2 project generally begins with three environments (dev, test and prod), though creating new
    environments is easy. 

    You can view your application in different environments simply by changing the
    front controller in your browser. To see the application in the dev environment, access the application
    via the development front controller:

    http://localhost/app_dev.php/hello/Ryan

    If you'd like to see how your application will behave in the production environment, call the prod front
    controller instead:    

    http://localhost/app.php/hello/Ryan

The key to access an environment is the front-controller, a single php file that serves 
the whole application. 

The **app.php** file is the default front-controller to use the production environment,
the app_dev.php to use the development environment.

AlphaLemon CMS adds its own environments named **alcms** and **alcms_dev** to separate 
the backend from the frontend, which means where you will edit the website from the 
frontend, which will remain the default Symfony2 one.

To enter the backend you'll use the following url:

.. code-block:: text

    http://localhost/alcms.php/

But that's not enough, in fact, to secure the AlphaLemon CMS, an additional token has been added,
so the final url, to enter the backend of a fresh installation is:

.. code-block:: text

    http://localhost/alcms.php/backend/en/index

So the url has the **backend** token explained above and there are declared explicitely  
the default language **en**, and the home page, which is named **index** by default.

.. note::

    The **alcms environment** is totally decoupled from the frontend environment, so every 
    change you made in the backend will not affect the website until the site is deployed.

Authentication
--------------

The access AlphaLemon CMS you must authenticate and a default user is provided, so
when promped, authenticate yourself using the following credentials:

.. code-block:: text

    user:       admin
    password:   admin

The admin user can manage users, so after loggin-in, you should immedialty change the password
for that user.

Users and roles management
--------------------------
This section is not ready yet.

Editing the website
-------------------
AlphaLemon CMS renders the requested website page and adds a fixed toolbar at the top 
of the screen.

.. image:: //bundles/alphalemonwebsite/media/manual/toolbar.jpg

The toolbar contains all the commands you need to manage the website.

Moving through the pages
------------------------
Moving through the website's pages is simply as clicking the link that points the page 
you want to edit. 

.. image:: //bundles/alphalemonwebsite/media/manual/move.jpg

It the picture above you see the main menu of AlphaLemon CMS website. Some of those
links are linked to other pages of the website, for example the **Docs** page. 

To edit the download page you just need to click the **Docs** link and AlphaLemon CMS
moves to the requeste page.

This works only for internal pages, in fact if you'd click on a link that points an 
external resource, obviously you'll be moved to that website, leaving AlphaLemon CMS.

Sometimes it could happen that an internal page is not linked or the link is not visible
in the current page. In this case you may reach that page using the **Navigation Menu**
placed into the toolbar:

.. image:: //bundles/alphalemonwebsite/media/manual/navigation_menu.jpg

That tool let you choose between the whole website's languages and pages.

`Watch the screencast to see how you can move through pages`_

Manage contents
---------------
The page contents are managed directly on the web page and every change you made is 
displayed in real time on the page.

Each content is placed into a **slot** and it can contain one or more **blocks**.
Each block represents a content on the page. Since now, I contents will always call 
blocks.

To start edit a block, click the **Edit** button on the toolbar. This causes that the 
editable blocks are immediaty surrounded by a red dotted border rectangle, which is
the interface used to interact with the block itself.

To perform an action on a block, you must place the cursor of your mouse inside the
sqaure's area and click the mouse button.

Wnen you **left click** on the square, you'll perform the **default action**, that is edit the
block's content, when you'll **right click** into the rectangle, you'll open the **contextual
menu**: this tool gives you the total control on the block.

Add a block
~~~~~~~~~~~
From the contextual menu, you must choose the block to add, by clicking the block type 
entry from the Add block submenu.

.. image:: //bundles/alphalemonwebsite/media/manual/contextual_menu.jpg

When you add a new block, it is always inserted under the one you clicked.

AlphaLemon CMS, in its basic configuration, comes with some standard block types which
are the base block required to build a website, but more can be added to improve
your own environment. The standard blocks you can choose from, are:

* **Hypertext** - A standard html block.
* **File** - A block that handles a file.
* **Menu** - A list of links
* **Javascript** - A javascript block.
* **Languages menu** - A menu which renders the languages of your website as links.

.. note::

    As you might have noticed from the picture above, there are more than five blocks
    available on the contextual menu, in fact it is really easy to create and share new 
    blocks: see `how to create a new block`_  and give a look to the `available blocks`_
    on alphalemon website.

Edit a block
~~~~~~~~~~~~
Each block has a dedicated editor to manage its content. Where it was possible, the
same editor has been reused, to implement the less numbers of interfaces and simplify
the learning curve. For example, the Text and the Menu blocks have the same editor.
Each block editor will be explained separately next above.

Remove a block
--------------
To remove a block, just click on the **Remove block** entry from the contextual menu.

`Watch the screencast to see how you can manage blocks`_

Standard block Types
--------------------

As you have learned in the previous paragraph, AlphaLemon has several base block types
which are enough to build the whole website. Let's see in detail.

Hypertext
~~~~~~~~~
This is the standard html block, you can use to enter the text and format it, adding
styles, like bold, italic and so on. You can create links, add media files like images, 
flash movies etc.

It is very similar to a word processor, so it is quite easy to use and understand,
because you probably are already familiar with that kind of interface.

File
~~~~
A block that handles a file, for example pdf, zip, html and so on. That file could 
be displayed opened and its contents are displayed on the page or closed, so it is 
displayed as a link. 

Menu
~~~~
This block is the one used to create the navigation menu to link the pages of the website.

A menu is made by an unordered list of links, so the interface is the same used by 
the Text block, but has just the commands needed to manage that list and to create 
or remove the links.

Script
~~~~~~
This is the most powerful block type you get, because it lets you enter an entire
javascript tool in an easy way. It is made by five fields:

    * Html Code
    * External Javascripts
    * Internal Javascript
    * External Stylesheets
    * Internal Stylesheet

As you might guess, you can add a piece of html code, external and internal stylesheets
and javascripts.

Basically this block could be used to **add a code from another site**, for example the
code you get from the **youtube** website to display a video on the page, or **google** 
ad-sense code. 

It could be used to add a full javascript add-on, like an images gallery. If you'll give
a look to the rotating images on the http://alphalemon.com website homepage, that javascript
tool has been added using the javascript block.

While you may use this approach when you need to display a content like that, **you
should add a new block that made that job**.

In this way you are able to add a dedicated editor for that block and to reuse it 
in more website, without reinventig the weel each time.

In addiction you might `share it`_ with the AlphaLemon CMS community.


.. _`Watch the screencast to see how you can move through pages`: http://alphalemon.com/alphalemon-cms-page-browsing-screencast-tutorial
.. _`how to create a new block`: http://alphalemon.com/add-a-new-block-app-to-alphalemon-cms
.. _`available blocks`: http://alphalemon.com/extend-your-alphalemon-cms-application-adding-new-app-blocks
.. _`Watch the screencast to see how you can manage blocks`: http://alphalemon.com/alphalemon-cms-contents-management-screencast-tutorial
.. _`share it`: http://alphalemon.com/extend-your-alphalemon-cms-application-adding-new-app-blocks