Change the theme of your website 
================================

This chapter explains how to change the theme of your website.

In this chapter you will learn:

    1. How to map the current theme templates with the ones of new theme
    2. How to map the slots for each template
    3. How to customize a template, overriding it
    4. How to override a template extended by other templates
    5. How to add custom assets to new theme
    6. How to finalize the theme change

Introduction
------------

An AlphaLemon CMS theme is made by templates, a set of twig files which contain a special 
twig function deputated to render the content on the page. 

Each template is made by slots, a placeholder where one or more blocks are placed.
A block represent the content displayed on the page.

AlphaLemon CMS parses all the website's pages and changes the templates as requested,
then backs up all the blocks' contents and for each page and adds the default contents 
from new templates.

At the end of this operation, your website is rebuilt using the new theme and previous
contents can be restored mapping the slots from previous templates with the new theme
templates.

For this chapter I change the theme used on alphalemon.com website with the **BootbusinessTheme**,
the new bundled theme which comes with a fresh install of AlphaLemon CMS.

.. note::

    Please be careful, this operation is obtrusive. Before start it is strongly suggested 
    to make a database backup.

Mapping the templates
---------------------

The very first step to do is to map the current website theme's templates with the 
templates of the **BootbusinessTheme** theme. To do this, I'll open the Themes panel 
from the top toolbar.

On the left there is the active theme used on the website and on the right there are
the available themes.

I look for the **BootbusinessThemeBundle** and I click on the **Activate** button 
placed under the **BootbusinessTheme** to start this procedure.

A new window opens and the current Theme's templates are listed on the left and
near each template, there is a combo box which contains the new Theme's templates.

.. image:: //bundles/alphalemonwebsite/media/change_theme/img_01.jpg

AlphaLemon CMS automatically selects the templates with the same name, others must be
selected manually.

AlphaLemon website theme is made by two templates: 

    - home
    - internal
    
The first one is automacally mapped because the **BootbusinessTheme** has a template
named with the same name, while I must manually map the **internal** template. I choose 
the one named **empty**, simply choosing it from the combo box.

.. image:: //bundles/alphalemonwebsite/media/change_theme/img_02.jpg

Everything is right now, so I click the **Change** button to start the procedure.

Map slots
---------

At this point we want to recover the most of the previous contents. This step is
achieved matching the slots from the previous template with the ones of the current template.

.. image:: //bundles/alphalemonwebsite/media/change_theme/img_03.jpg

When the page has been reloaded, AlphaLemon CMS added the **Slots** and the **Finalize** 
buttons to **Commands Panel** on the left.

To start matching the slots click on the **Slots** button, blocks are now active and
get sorrounded by a green square when you place the mouse over them.

.. image:: //bundles/alphalemonwebsite/media/change_theme/img_04.jpg

Now I click on the content that contains the main slider and a panel that contains
all the previous template's slot names appears on the right bottom corner.

Slots are grouped by repeated status.

Alphalemon.com has a slider on the homepage, so I want to match this slot to import the 
previous slot blocks into this one: to do this I simply click on the screenshot_box.

.. image:: //bundles/alphalemonwebsite/media/change_theme/img_05.jpg

That's ok and I click the **Save** button to confirm.

.. image:: //bundles/alphalemonwebsite/media/change_theme/img_06.jpg

Obviously images don't fit, so I should redefine them, but this is not the pourpose of 
this tutorial.

Now I select again the same slot: the **screenshot_box** changed its color to a light 
gray to indicate that the slot has been used. You can click on the slot to restore the
previous contents and click the **Undo** button to call back the saved content.

Override a template
-------------------

Now I want to import the content placed on **main_content** slot into the slot where 
there is displayed the firts rows of thumbnails. 

.. image:: //bundles/alphalemonwebsite/media/change_theme/img_07.jpg

In this case the paragraph's title is written inside this block, while the new template 
separates the title from the paragraph just above.

.. image:: //bundles/alphalemonwebsite/media/change_theme/img_08.jpg

There two ways to adjust this situation:

    - copy the title from the new paragraph to the other one
    - override the template and remove the title paragraph
    
While the first way is easier than the second one, I choose to move the second way to mainatain
consistency between templates.

The template I'm working on is the **home.html.twig** one, so to override it, I must 
create the same file under the **app/Resources/views/BootbusinessThemeBundle**, as well
explained in the `Symfony2 cookbook`_.

.. note::

    Since now, you should switch to dev environment **- alcms_dev.php -** because
    working with templates in prod environment requires frequently to clear the cache 
    to have your change displayed. 

I copy the **home** file under those folder, then I change the following code:

.. code:: jinja
    
    // app/Resources/views/BootbusinessThemeBundle/home.html.twig
    {% block content_title_1 %}    
        {# BEGIN-SLOT
            name: content_title_1
            repeated: page
            htmlContent: |
                <h2>Our products</h2>
        END-SLOT #}
        {{ renderSlot('content_title_1') }}  
    {% endblock %}
    
as follows:

.. code:: jinja

    // app/Resources/views/BootbusinessThemeBundle/home.html.twig
    {% block content_title_1 %}    
    {% endblock %}

The change is applied after a page reload and the **content_title_1** is not rendered 
anymore.

I repeat the same steps to import the **video_content** slot.

.. image:: //bundles/alphalemonwebsite/media/change_theme/img_09.jpg

Alphalemon.com website has more slots than the current template, so I must add some
new slots to the overriding template:

.. code:: jinja
    
    // app/Resources/views/BootbusinessThemeBundle/home.html.twig
    <div class="container">
        [...]
        <div class="row-fluid">
            {% block apps_content %} 
                {# BEGIN-SLOT
                    name: apps_content
                    repeated: page
                END-SLOT #}
                {{ renderSlot('apps_content') }}   
            {% endblock %}     
        </div>
        <div class="row-fluid">
            {% block blocks_content %} 
                {# BEGIN-SLOT
                    name: blocks_content
                    repeated: page
                END-SLOT #}
                {{ renderSlot('blocks_content') }}   
            {% endblock %}     
        </div>
        <div class="row-fluid">
            {% block themes_content %} 
                {# BEGIN-SLOT
                    name: themes_content
                    repeated: page
                END-SLOT #}
                {{ renderSlot('themes_content') }}   
            {% endblock %}     
        </div>
    </div>

Then I must recreate the theme structure using the built-in command:

.. code:: text

    php app/console alphalemon:generate:templates BootbusinessThemeBundle
    
Now the page must be reloaded to get the changes just made. 

.. image:: //bundles/alphalemonwebsite/media/change_theme/img_10.jpg

AlphaLemon CMS does not know this new slots, so it displays them as an empty slot.
I just need to map this new slots as did before, to import the contents from provious
theme.

.. image:: //bundles/alphalemonwebsite/media/change_theme/img_11.jpg

Override an extended template
-----------------------------

I repeat the mapping process for the footer's blocks. At the end of this step I must
be sure to avoid to render the section's titles because titles come with contents.

.. image:: //bundles/alphalemonwebsite/media/change_theme/img_12.jpg

The footer is a common section shared between templates, this means that one or more
templates used by this theme inherit from the view where the footer section is defined.

This is a problem for templates inheritance, because it is not enough to redefine the 
base template under the **app/Resources/views/BootbusinessThemeBundle** but we need 
to override all the theme's templates, changing the **extends** directive.

First of all I copied the **base.html.twig** template from **BootbusinessThemeBundle/Resources/views/base**
to **app/Resources/views/BootbusinessThemeBundle/base**, then I redefined the blocks that 
renders the titles:

.. code:: jinja
    
    // app/Resources/views/BootbusinessThemeBundle/base/base.html.twig
        {% block footer_title_1 %}
        {% endblock %}
        [...]
        {% block footer_title_2 %}
        {% endblock %}
        [...]
        {% block footer_title_3 %}
        {% endblock %}
        [...]

then I opened the **home.html.twig** and I changed the **extends** instruction as follows:

.. code:: jinja
   
    // app/Resources/views/BootbusinessThemeBundle/home.html.twig
    {% extends '::BootbusinessThemeBundle/base/base.html.twig' %}

At last I copied all the templates under **BootbusinessThemeBundle/Resources/views**
and I changed the **extends** function as did for **home.html.twig** template.

Add custom assets
-----------------

Now all the listed elements in the footer section have the bullets displayed: to hide
them I add a new stylesheet to manage this aspect outside the original theme.

I add a new folder **app** under the application's **web** folder and I add a new
**style.css** file, with the followig code inside:

.. code:: jinja

    // web/app/style.css
    footer li {
        list-style: none;
    }

To load the stylesheet I add a new entry to **stylesheets** function into the 
**base.html.twig** template:

.. code:: jinja
    
    // app/Resources/views/BootbusinessThemeBundle/home.html.twig
    {% block post_external_stylesheets %}
        {% stylesheets  filter='?yui_css,cssrewrite' 'bundles/bootbusinesstheme/css/font-awesome.css' 
                                                     'bundles/bootbusinesstheme/css/font-awesome-ie7.css'
                                                     'bundles/bootbusinesstheme/css/boot-business.css'
                                                     'app/style.css'
        %}        
        <link href="{{ asset_url }}" rel="stylesheet" type="text/css" media="all" />
        {% endstylesheets %}
    {% endblock %}

.. image:: //bundles/alphalemonwebsite/media/change_theme/img_13.jpg

Obviously I should implement more adjustments to have a nicer result, but this is not
the pourpose of this tutorial.
    
.. note::

    There's no intructions on Symfony2 book/cookbook about assets overriding, so feel
    free to add your custom assets in the place you prefer.

The stock of the situation
--------------------------
    
This would therefore appear to be a good point to take stock of the situation.

The home page is quite finished: the main contents section of the page is completed, there 
are some contents which would be modified without grabbing blocks from previous theme.

Main toolbar which has not been touched anymore, because the block's types are different, 
infact this new theme uses the **BootstrapNavbarBlock** while the previous website used 
the **Menu** block. In this situation I must rewrite the links without recovering the 
main menu. 

.. note::

    This last operation goes beyond the pourpose of this tutorial, so it has not been
    made.

As last thing, I enter inside one of the other pages, no matters which one, to recover
the main content, so I repeated the operation to assign the previous slot to the new one.

.. image:: //bundles/alphalemonwebsite/media/change_theme/img_14.jpg

This block is a File content loaded with twig, so when I do the change, the operation
seems to fail, but it is only needed a page reload to have it working as expected.

.. image:: //bundles/alphalemonwebsite/media/change_theme/img_15.jpg

The most important thing to notice is that, while this slot is repeated at page level, 
- this means that each page has its own content which differs from other pages -, it 
is not required to do this operation for each page, because the slot is the same for
all pages.

I must import the stylesheets that renders the php code, so I open the **empty.html.twig**
template and I add the following code:

.. code:: jinja

    // app/Resources/views/BootbusinessThemeBundle/empty.html.twig
    {% block post_external_stylesheets %}
        {{ parent() }}
         <link href="/bundles/bootbusinesstheme/css/internal.css" rel="stylesheet" type="text/css" media="all" />     
         <link href="/bundles/bootbusinesstheme/css/pygments.css" rel="stylesheet" type="text/css" media="all" />
         <link href="/bundles/bootbusinesstheme/css/code.css" rel="stylesheet" type="text/css" media="all" />
    {% endblock %}

This adds the required stylesheets to my page. Don't forget to call the **{{ parent() }}** 
instruction otherwise the stylesheets defined in base.html.twig won't be rendered.

I remove the page title as well:

.. code:: jinja

    <div>
        {% block page_title %}
        {% endblock %} 
    </div>

.. image:: //bundles/alphalemonwebsite/media/change_theme/img_16.jpg

Finalize the theme
------------------

When the slot's porting is completed, I can finalize the theme change. This operation
can be made in two ways:

    - Partial
    - Full
    
**Partial finalization** only removes the changed slots which has already been mapped with 
a slot from previous theme, **Full finalization** removes both the changed blocks and the
backupped blocks, so if you Use a partial finalization, you can continue with mapping slots,
when you full finalize, this is not possible anymore.

.. note::

    The **Full finalization** will remove the **Slots** and **Finalize** buttons


.. class:: fork-and-edit

Found a typo ? Something is wrong in this documentation ? `Just fork and edit it !`_

.. _`Just fork and edit it !`: https://github.com/alphalemon/alphalemon-docs
.. _`Symfony2 cookbook`: http://symfony.com