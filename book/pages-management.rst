Pages management
================

This chapter explains in detail how an AlphaLemon CMS page is structured and how to 
entirely manage website pages.

The panel interface
-------------------
Website pages are managed using the **Pages panel** click the **Pages** button on 
the main toolbar placed at the top of the page to open it.

The panel is divided in two columns: the bigger placed on the left contains the interface 
to add and modify a page, while on the right there is a list which contains all the website 
pages.

The main column is tabbed, so there are many sections you must check before add a 
page.

The page tab
------------

The **Page** tab contains the page attributes described below.

.. image:: //bundles/alphalemonwebsite/media/manual/img-28.jpg


The page name option
~~~~~~~~~~~~~~~~~~~~
The **Page name** is an unique internal name that globally identifies the page. This
value is inserted into the text box which has the same name.

This information could be manipulated buy AlphaLemon CMS when you enter some particular 
charaters like spaces or accented letters.

For example the **contact us** page name is transformed to **contact-us**.

This information is mandatory.


The template name option
~~~~~~~~~~~~~~~~~~~~~~~~
The **Template name** represents the name of the layout the page will get. Template
names belongs the theme you are using in your website.

This value must be selected using the combobox which has the same name.

This information is mandatory.


The home page option
~~~~~~~~~~~~~~~~~~~~
The website's home page is the first page displayed when a user, that browses your website,
does not require any specific page, for example http://alphalemon.com address.

If you want that the page you are adding or editing will become the website home page, 
just check this option.

You cannot degrade the current home page, this means you cannot unckeck the **Home page**
checkbox for the home page.

The home page cannot be removed from the website, so, when you need to remove it,
you must first promote another page as home.


The published option
~~~~~~~~~~~~~~~~~~~~
This option is useful in a team where members work on the website at the same time, because
it explicitely forces someone with appropriete rights, to declare that the page is 
ready to be published in production.

In fact a page is not published by default, so only the users which have enough privileges
could change this option and declare a page as publishable.

Another situation when this option is useful could be when you are working on a page and 
you want keep it private just because it is not ready to be published yet.


The seo tab
-----------

The **SEO** tab contains the Search Engines Options attributes described below.

.. image:: //bundles/alphalemonwebsite/media/manual/img-29.jpg


The permalink option
~~~~~~~~~~~~~~~~~~~~

The **permalink** represents a static permanent link that identifies the page outside
the site.

This value is inserted into the textarea which has the same name.

A permalink could be translated for each language of your website, for example, 
you may have a page named **contacts** which may have a permalink **contact-us** 
for the English version and another permalink **contattaci** for the Italian language.

Permalink is the most important information you will provide for each page because it is
the name used from the external to reach it. This means you must carefully choose each
permalink. 

Let's dive into an example.

Think about the http://alphalemon.com website download page: we could have choosen a 
parmalink called **download** and it would have worked fine, but we defined a long description
to define this page: **download-alphalemon-cms-for-symfony2-framework**.

This is not a simply matter of taste, in fact we provide a lot of information about 
our product in that permalink: we specify the **download** keyword, the product name, 
which is obviously **alphalemon**, we declared that it is a **cms** and that is written 
to work with the **symfony2 framework**.

All these information should produce a better result when a search engine indexes that
page. 

Obviously the permalink must be consistent with contents saved in the page, otherwise 
search engines will penalize your site.

This information is manipulated, when neeed, as for the **Page name** attribute.

The title option
~~~~~~~~~~~~~~~~
This information represents the page title and it is dispayed in the top bar of the
browser. 

It should not contain more than 50 characters and must be consistent with page's contents.

This value is inserted into the textarea which has the same name.

This is another important information you must provide while it is not mandatory,
because search engines give it a great importance for indexing the page, so, as for 
permalink you must very accurate to choose the page title.


The description option
~~~~~~~~~~~~~~~~~~~~~~
This information must provide a small brief description about the arguments exposed
on the page. 

This information is not displayed anywhere, it should not contain more than 255 characters 
and must be consistent with page's contents.

This value is inserted into the textarea which has the same name.

While it is not mandatory as for the title, it is another important information you 
must provide.


The keywords option
~~~~~~~~~~~~~~~~~~~
This information should provide a list ok keywords used in the page. This one has
been widely abused in the past, so many search engines ignore it today.


The sitemap tab
---------------

A sitemap is a file which is automatically generated by AlphaLemon CMS each time the 
website is deployed. 

This file helps search engines to correctly indicize the pages of your website.

.. image:: //bundles/alphalemonwebsite/media/manual/img-30.jpg

From this tab you can set the sitemap attributes for the page.

To learn more about the information you can provide in this section, read the 
`sitemap protocol`_.

Add a new page
--------------

To add a new page you must be sure that any other page is selected in the pages list 
and that the form is completely blank. This is the situation you get when you open the panel.

Fill all the required information and click the **Save** button to confirm.

Select and de-select a page
---------------------------

To select a page you must choose it from the pages list on the right, clicking on the 
page name. 

After that, you must choose the language from the **Languages** combobox placed over 
the pages list, to load the page's attributes for the choosen language

To deselect a page, just click on it.

Edit a page
-----------

To edit a page you must first select it, then you can change what you need and click on
the **Save** button to confirm your changes. 


Delete a page
-------------

To delete a page you must first select it, then you must click the **Delete**
button placed under the pages list.

When you need to do this operation, you are not required to choose a language
because AlphaLemon CMS removes all the page attributes.


.. class:: fork-and-edit

Found a typo ? Something is wrong in this documentation ? `Just fork and edit it !`_

.. _`Just fork and edit it !`: https://github.com/alphalemon/alphalemon-docs
.. _`sitemap protocol`: http://www.sitemaps.org/protocol.html
