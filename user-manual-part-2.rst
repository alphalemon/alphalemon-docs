AlphaLemon CMS User Manual -2-
==============================
In this chapter you will learn how to manage languages, pages, themes and how to
deploy your website.

Pages
-----
First of all, I'll explain how a page is defined in the CMS structure. 

A page is identified by an **unique internal name**, and has attached one of the available 
templates from the active theme used by the website.

In addiction, it has a set of attributes, used by the search engines to categorize
your site. Those attributes are:

    * Permalink
    * Title
    * Description
    * Keywords

Title, description and keywords are **metatags**, some hidden elements that belongs
the page, used by search engines to indicize the page in their database.

The **permalink** is a static permanent link that identifies the page outside the site, 
and it could be translated for each language of your website.

For example, you may have a page named contacts which might have a permalink **contact-us** 
for the English version and another permalink **contattaci** for the Italian page.

Add a new page
~~~~~~~~~~~~~~
To add a new page, you have to open the page panel by clicking the **Pages** command
on the main toolbar. 

A form which contains all the information required by a new page is displayed.

.. image:: //bundles/alphalemonwebsite/media/manual/pages_management.png

You should fill the all the fields, providing the required information, then click the 
**Save** button.

If everything is ok, the new page is added both to the list on the right and to the 
navigation menu.

The home page option
~~~~~~~~~~~~~~~~~~~~
This option let you promote a page as the home page of your website. This page is
important because it is the first page displayed when a user that browsers your website,
does not require any specific page.

For eaxample, when you open the http://alphalemon.com website, you are not specifying
any page, so the home page is displayed.

The checkbox cannot be used when you are editing the home page, so, the only way you
have to change the home page of your website is to choose another page than the home
and promote it.

The home page cannot be removed from the website.

The published option
~~~~~~~~~~~~~~~~~~~~
This option is useful in a team which members work on the website at the same time, because
it explicitely forces someone to declare that the page is ready to be published on the
website.

In fact a page is not published by default, so only the users which have enough privileges
could change this option and publish a page.

Another situation is when you are working on a page and you want keep it private,
because it is not yet ready for the publication.

Edit an existing page
~~~~~~~~~~~~~~~~~~~~~
To edit a page you must choose the language from the Languages combobox, then click
on the page name you want to edit.

The page's data will be loaded into the page's form and you are able to change them. 
Click on Save button to commit your changes.

Remove page's attributes for a specific language
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Each page has specific attributes related to the SEO section, for each language on
the website.

To remove the attributes for a specific language, select the language and the page 
from the Pages Selector section, then click the **Delete** button.

This action will not delete the page, just only the attributes for that language.

Remove a page
~~~~~~~~~~~~~
To completely remove a page select it and leave the Languages combobox blank, then
click the **Delete** button

`Watch the screencast to see how pages are managed by AlphaLemon CMS`_

Languages
---------
To add a new language, you have to open the languages panel by clicking the **Languages**
command on the main toolbar.

.. image:: //bundles/alphalemonwebsite/media/manual/languages_management.jpg

Add a new language
------------------
To add a new language simply choose the language from the Language combobox and click
the Save button. 

If everything is ok, the new language is added both to the list on the right and to the 
navigation menu.

When a new language is added, all the main language's blocks are copied to the new one.

The main language option
~~~~~~~~~~~~~~~~~~~~~~~~
This option let you promote a language as the main language of your website. This language 
is important because it is the first language displayed when a user that browsers your website,
does not request any specific language.

For eaxample, when you open the http://alphalemon.com website, you are not specifying
any language, so the main language is the language choosen.

The checkbox cannot be used when you are editing th main language, so, the only way you
have to change the main language of your website is to choose another language than the main
and promote it.

The  main language cannot be removed from the website.

Edit an existing language
~~~~~~~~~~~~~~~~~~~~~~~~~

To edit a language you must choose it from the Languages selector panel. The language's
data will be loaded, so you can change it. Click on Save button to commit your changes.

Remove a language
~~~~~~~~~~~~~~~~~

To completely remove a language select it from the Languages selector panel, then
click the Delete button.

`Watch the screencast to see how languages are managed by AlphaLemon CMS`_

Themes
------
To manage the theme of your website, you have to open the themes panel by clicking 
the **Theme** command on the toolbar.

This panel is divided in two columns: the left one displays the current active theme,
the right column displays all the available themes and on the bottom.

Manage the themes
~~~~~~~~~~~~~~~~~
Themes could be added in two ways:

1. Create a custom theme
2. Add an existing theme

To create a custom theme, you should read the `dedicated tutorial`_.

Each existing theme provides the instructions you need, to install it.

Preview a theme
~~~~~~~~~~~~~~~
When you have created your own theme or you have grabbed one, you would like to preview
it, so open the Themes panel and click the **Preview and activate** button placed under
the theme you want to see.

Here you'll have a toolbar as the main one, where are placed the name of the theme
you are watching, the current template and some commands.

Templates are explored by using the available templates combobox and are displayed 
exactly how the theme's designer has build the theme itself.

In this module you are able to activate the theme, but this will be explained in a
dedicated tutorial.

In the meanwhile, you could have a look to the `screencast`_ that shows how the new theme 
used by the http://alphalemon.com website has been activated from the previous desing.

Activate a theme
~~~~~~~~~~~~~~~~
This option simply activates a theme. It let you map the templates of the new theme
with the templates of the previous one, but you should use the **Preview and activate**
module to do this operation. 

.. note:

    This was the tool used for activating a theme before the Preview and activate module
    was implemented.
    

Start from this theme
~~~~~~~~~~~~~~~~~~~~~
This option destroys all the pages, languages and contents of your website and starts
a new site with the selected theme.

This option must be used when you are starting a new website from the scratch and
you want to use a theme different from the default one, provided by AlphaLemon CMS.


Deploy the site
---------------
To deploy the website, simply click the **Deploy** button on the main toolbar. AlphaLemon
CMS will save the pages as twig templates, will copy the assets from the backend to the
frontend and will generate the routes for the web site.

.. class:: fork-and-edit

Found a typo ? Something is wrong in this documentation ? `Just fork and edit it !`_

.. _`Just fork and edit it !`: https://github.com/alphalemon/alphalemon-docs
.. _`Watch the screencast to see how pages are managed by AlphaLemon CMS`: http://alphalemon.com/alphalemon-cms-pages-management-screencast-tutorial
.. _`Watch the screencast to see how languages are managed by AlphaLemon CMS`: http://alphalemon.com/alphalemon-cms-screencast-languages-management-tutorial
.. _`dedicated tutorial`: http://alphalemon.com/add-a-custom-theme-to-alphalemon-cms
.. _`screencast`: http://alphalemon.com/alphalemon-cms-themes-preview-screencast-tutorial