AlphaLemon CMS User Manual -1-
==============================

AlphaLemon CMS is a Content Management System application, built on top of Symfony2
Framework, really easy to use.

In this chapter you will learn how the CMS is configured, which are the predefined
block types provided with the CMS and how to manage them.

Environments
------------

AlphaLemon CMS works on its own environment, called **alcms**, this means that the
Symfony2's **app** environment is the one where your application frontend runs,
while the **alcms** one is the backend where the website can be edited.


If you need to debug AlphaLemon CMS, there is the **alcms_dev** environment.

The app environment
~~~~~~~~~~~~~~~~~~~

The **app** environment doesn't require to spend many words: it is the place where your
application runs. It works as a normal Symfony2 project so you can work with it as you
usually do, with a standard Symfony2 application.

The alcms environment
~~~~~~~~~~~~~~~~~~~~~

The alcms environment is the backend of your website, where you can edit the blocks
of your website, add new pages, languages and themes, in other words, it is the place
where you can manage and control your website. This environment is totally decoupled
from the frontend so every change you made will not affect the website until you
deploy it.


Authentication
--------------

The backend should be a protected zone, so only authenticated users can enter. AlphaLemon
wants to be as simple as possible, so it doesn't implement natively complex user's
credentials and roles, but it requires a simply authentication.

While it sound strange, AlphaLemon CMS, by default, is not secured. This because
the user can secure the website only when he really need it.

Editing the website
-------------------

The editor is very simple and adds a toolbar menu at the top of the webpage. Here
are placed all the commands you need to manage the whole website. Under it, the website
is rendered exactly as it will be deployed.

Moving through the pages
------------------------

Moving through the website's pages is simply as clicking the link you want to reach
on the rendered web page, in fact, the site is explorable exactly as in the production
environment.

So if you have a link called **features**, you just click it and the feature's page.
is opened. However it could happen that a page is not linked on the website's menu,
so you can reach it by selecting the page from a combo-box placed at the top-right
corner of the toolbar menu.

Just before the pages' combo there is another one where are displayed all the available
languages for your website.

Add, edit and remove blocks
-----------------------------

Add, edit and remove blocks operations are made directly on the web page and every
change you made is displayed in real time on the page.

When you are inside the page you want to edit, just click the **Edit** button on
the toolbar, and the editable blocks are immediaty surrounded by a red dotted border
rectangle. Blocks editing is made interacting with those squares, let's see how.

Add a block
-------------

To add a new block you must right click on an existing block or if any block exists
on a slot, a message takes place into the empty slot that lets you interact with it.

This opens the contextual menu that lets you manage the block itself. Now you must
choose the block to add, by clicking the block type entry from the Add block submenu.

AlphaLemon CMS, in its basic configuration, comes with some standard block types which
are the base block required to build a website, but more can be added to improve
your own environment. The standard blocks you can choose from, are:

    * **Text** - A standard text block which can be attached by several formatted styles.
    * **File** - A block that represent a file
    * **Menu** - A list of links on one row or column
    * **Javascript** - A javascript block. It could be an external tool, with its own stylesheets and javascripts
    * **Languages menu** - A menu which has the languages of your website
    * **Languages Menu** - A list of links that represents the website's languages

.. note:

    A block is always added under the one you clicked.

Edit a block
------------

To start edit a block you could click the **Edit block** entry on the contextual menu,
or simply click inside the dotted rectangle and open the editor.

Each block has a dedicated editor to manage the content. Where it was possible, the
same editor has been reused, to implement the less numbers of interfaces and simplify
the learning curve. For example, the Text and the Menu blocks have the same editor.
Each block editor will be explained separately next above.

Remove a block
--------------

To remove a block, just click the **Remove block** entry from the contextual menu.

Standard block Types
--------------------

As you have learned in the previous paragraph, AlphaLemon has several base block types
which are enough to build the whole website. Let's see in detail.

Text
~~~~

This is the standard html block, you can use to enter the text and format it, to create
links, to add media files like images, flash movies and so on.

It is very similar to a word processor, so it is quite easy to use and understand,
because you probably are already familiar with that kind of interface.

File
~~~~

This block represents a file. It could be displayed on the page or displayed as a link

Menu
~~~~

This block is the one used to create the navigation menu to link the pages of the website.

A menu is made by an unordered list of links, so the interface is the same used by the Text block,
but has just the commands to manage the unordered list and to create or remove the links.

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