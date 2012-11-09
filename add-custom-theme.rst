Add a custom Theme-App to AlphaLemon CMS
========================================

This chapter explains how to add a new theme AlphaLemon CMS.

What is a Theme
---------------

An AlphaLemon CMS Theme Application could be defined as a collection of twig templates which have their own assets like
javascripts, stylesheets and images, packaged into a well-known structure, that defines a website design.

How is structured a Theme
-------------------------

A Theme-App is a standalone symfony2 bundle. This approach has several advantages:

1. Is a Symfony2 Bundle
2. Is reusable in many web sites
3. Assets required by the content are packed into a well known structure

Create the FancyThemeBundle
---------------------------
Alphalemon has two built-in commands which help to add a new theme:

- **alphalemon:generate:app-theme**
- **alphalemon:generate:themplates**

The first command generate a new App-Theme Bundle, the second one generate the templates configuration files.

To start a new theme, you must run the **alphalemon:generate:app-theme** command from your console.

This command extends the Symfony's generate:bundle command, so the process should be familiar. Run the following command to
start:

.. code-block:: text

    php app/console alphalemon:generate:app-theme

You'll get the following response:

.. code-block:: text

        Welcome to the Symfony2 bundle generator

    [...]

    Bundle namespace:

Answer as follows:

.. code-block:: text

    Bundle namespace: Acme/Theme/FancyThemeBundle

The proposed bundle name **must be changed** to FancyThemeBundle otherwise you might have troubles:

.. code-block:: text

    Bundle name [AcmeThemeFancyThemeBundle]: FancyThemeBundle

Next options could be left as proposed or you may change them to fit your needs.

Don't forget to let the command updates the AppKernel for you to enable the bundle.

.. note::

    This command does not manipulates the site's routes.

Your first App-Theme has been created, but, wait a moment, way a new command if
the creation procedure is the same of a normal bundle?

Easy, because this commands adds a new configuration file into the **config** folder that sets
the services to define the theme. See `themes internal configuration`_ to learn more.

Add the twig templates
----------------------

When the theme is created, you must start to add your twig templates to the theme bundle.

The **generate:app-theme** command, adds a new **Theme** folder under the **Resources/views**
folder of your App-Theme bundle: your templates must be placed inside that folder.

The Theme folder structure
~~~~~~~~~~~~~~~~~~~~~~~~~~
The Theme folder could be basically structured as follows:

.. code-block:: text

    Theme
        home.html.twig
        internal.html.twig

This theme has two templates, the **home** and the **internal** ones. This structure
works for sure, but, instead of that, a real world example might probably look like 
the following one:

.. code-block:: text

    Theme
        base
            base.html.twig
        home.html.twig
        internal.html.twig

so a base template is saved into a theme's subfolder and it contains the common parts 
of the website's layout, while the other two templates inherit from the base template.

The theme's configuration generated from a structure like that, produces two templates
and three slots configuration files, in fact the files saved into the theme's root folder 
become a template file, while a slot file is generated for all the templates, plus one
named **base.xml** that contains the common slots found in files saved into subfolders.

Don't worry about the generation process for now, because it is explained in detail 
in the next paragraphs.

You might need to add more separation to templates, so your structure might look like 
the following:

.. code-block:: text

    Theme
        base
            base.html.twig
        support
            template_a.html.twig
            template_b.html.twig
        home.html.twig
        internal.html.twig
        internal_1.html.twig

in this case the home template might inherit from the **template_a.html.twig** and 
the other two templates from the **template_b.html.twig** and, both of them, inherit 
from **base.html.twig**

Supposing that both the templates saved into the support folder have some slots 
definition, that slots are merged with that found into the **base.html.twig** saved 
into the **base.xml** file. 

The design
~~~~~~~~~~

AlphaLemon Cms uses **twig** as template engine, so when you have converted the templates to html,
you must write them to twig.

Clean the template
~~~~~~~~~~~~~~~~~~

First of all the template does not need the header section because it is inherited by the symfony's
base twig template or from another custom one, so remove everything is external to the body tag:

.. code-block:: html

    <!DOCTYPE html>
    <html>
        <head>
            <title></title>
            <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
            <link href="stylesheets/screen.css" media="screen, projection" rel="stylesheet" type="text/css" />
            <link rel="stylesheet" href="stylesheets/960.css" />
        </head>
        <body>
            [ JUST KEEP THIS ]
        </body>
    </html>
	
The twig template
~~~~~~~~~~~~~~~~~
Create a new twig template file called **home.html.twig** under the **Resources/views/Theme** folder, open it
and add the following code:

.. code-block:: html+jinja

    {% extends base_template %}

    {% block body %}
    {% endblock %}

The template must extend the template defined by the **base_template** variable, which is managed by the
ThemeEngineBundle and it must have a body **block** where the contents saved from the html template
you are creating must be placed:

.. code-block:: html+jinja

    {% block body %}
        [ JUST KEEP THIS ]
    {% endblock %}

The slots
~~~~~~~~~

Now you must identify the slots on the template. The **slot** is the html tag that contains the content you want to edit. For
example consider the following code:

.. code-block:: html

    <div id="header">
        <div id="logo">
            <a href="#"><img src="images/logo.png" title="Download AlphaLemonCMS" alt="" /></a>
        </div>
    </div>
    [...]

The content to edit is the one contained inside the div that has the logo id, so the only thing to do is to replace that content
with a built-in twig function called **renderSlot**:

.. code-block:: html+jinja

    <div id="header">
        <div id="logo">
            {{ renderSlot('logo') }}
        </div>
    </div>
    [...]

This function requires a string as argument which is the name of the slot.

The id assigned to the slot is not mandatory, so you could name it as you prefer, but it is best practice to
name the slot's id and the slot name in the same way.

Another best practice to follow is to use the **renderSlot** function inside a **div** tag, so avoid something like this:

.. code-block:: html+jinja

    <p id="logo">
        {{ renderSlot('logo') }}
    </p>

.. note::

    Don't throw away the replaced code, it will be used in a while

Prepare your template to be overriden
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

That code is enough to render the contents placed on the slot logo, but if you plan to distribute your theme, you must
wrap the renderSlot function with a block instruction:

.. code-block:: html+jinja

    <div id="header">
        <div id="logo">
            {% block logo %}
            {{ renderSlot('logo') }}
            {% endblock %}
        </div>
    </div>
    [...]

Define the template assets
~~~~~~~~~~~~~~~~~~~~~~~~~~
Each template comes with one or more external assets, like javascript and stylesheet files. Those assets
are declared in a comment section as follows:

.. code-block:: html+jinja

    {# BEGIN-EXTERNAL-STYLESHEETS
    @BusinessWebsiteThemeBundle/Resources/public/css/reset.css
    @BusinessWebsiteThemeBundle/Resources/public/css/style.css
    END-EXTERNAL-STYLESHEETS #}

There are four recognized sections by AlphaLemon CMS:

.. code-block:: text

    BEGIN-EXTERNAL-STYLESHEETS / END-EXTERNAL-STYLESHEETS
    BEGIN-EXTERNAL-JAVASCRIPTS / END-EXTERNAL-JAVASCRIPTS
    BEGIN-CMS-STYLESHEETS / END-CMS-STYLESHEETS
    BEGIN-CMS-JAVASCRIPTS / END-CMS-JAVASCRIPTS

The first section is used to declare the website's external stylesheets files and the second is for javascripts.
The other two sections are used to declare respectively stylesheets ot javascripts that AlphaLemon Cms must load
only when the CMS is active.

Those section should be added at the top of the page and none of them is mandatory.

Define the slot attributes
~~~~~~~~~~~~~~~~~~~~~~~~~~

To define the attributes of each slot you must add a comment which contains those attributes as follows:

.. code-block:: html+jinja

    <div id="header">
        <div id="logo">
            {# BEGIN-SLOT
                name: logo
                repeated: site
                htmlContent: |
                    <img src="/uploads/assets/media/business-website-original-logo.png" title="Progress website logo" alt="Progress website logo" />
            END-SLOT #}
            {% block logo %}
            {{ renderSlot('logo') }}
            {% endblock %}
        </div>
    </div>
    [...]

Let's explain carefully. Each attribute section must start with **BEGIN-SLOT** directive and closed by the
**END-SLOT** directive.

Attributes must be written in valid **yml** syntax. Yml requires a perfect indentation, so the first line defines the intentation for
the other attributes:

.. code-block:: html+jinja

    {# BEGIN-SLOT
        name: logo
          repeated: site
        htmlContent: |
            <img src="/uploads/assets/media/business-website-original-logo.png" title="Progress website logo" alt="Progress website logo" />
    END-SLOT #}

The code above will fail because the second attribute has a wrong indentation. When
this happens, the section is skipped and the service is not instantiated.

The **name** option is mandatory and when it is omitted the slot is skipped.

Addictional optional arguments
------------------------------

In addiction to **name** option, there are some attributes you could define:

1. blockType
2. htmlContent
3. repeated

The blockType option
~~~~~~~~~~~~~~~~~~~~

Defines the block type that AlphaLemon CMS must add for that slot, when a new page is added. By default, the block type
added is Text.

The htmlContent option
~~~~~~~~~~~~~~~~~~~~~~

the **htmlContent** option overrides the default content added by the block type, so when you need to use the
default value added by the block, simply don't declare this option.

The repeated option
~~~~~~~~~~~~~~~~~~~

Most of the contents displayed on a web page are repeated through the website pages. For example the site logo
usually is the same for all the site's pages, while a navigation menu is the same for a specific language.

The repeated option manages this behavior and repeats the content for the blocks that live on a slot. The
possibile values for this option are:

1. page (default)
2. language
3. site

When this argument is not declared, a block repeated at page level is added.

None of them is required, but when you don't need to specify any attribute, you must however
define the section:

.. code-block:: html+jinja

    {# BEGIN-SLOT
        name: logo
    END-SLOT #}


While this comments could be placed everywhere on your template, it's strongly suggested to place it
above the **renderSlot** call.


Let the magic starts
~~~~~~~~~~~~~~~~~~~~
When your templates are ready, you may let the magic starts, running the second command exposed
at the begininng of this tutorial:

.. code-block:: text

    alphalemon:generate:templates FancyThemeBundle

This command will generate the config files that defines the theme's templates and their slots. If
there's something goes wrong, a notice is displayed.

Override a template
-------------------

Let's assume that you want to use a new theme, called **AwesomeThemeBundle** and that this theme has two templates, named home.twig.html and internal.twig.html.

When the **renderSlot** function has been explained, it has been presented as best practice to adopt for distributable themes, to wrap the render block function
with a block section to let the template overridable.

To override a template, simple create a new folder called as the new theme you want to use, so **AwesomeThemeBundle**, under the **app/Resources/views** folder
of your application than add a new **home.twig.html**, open it and add the following code:

.. code-block:: jinja

    // app/Resources/views/AwesomeThemeBundle/home.twig.html
    {% extends 'AwesomeThemeBundle:Theme:home.html.twig' %}

    {% block left_sidebar %}
    {{ renderSlot('top_section_1') }}
    {% endblock %}

This code overrides the **AwesomeThemeBundle's home.html.twig** template replacing the **left_sidebar** slot with the contents saved with the **top_section_1** slot
you have filled in your previous **home.twig.html** template.

.. _`themes internal configuration`: the-internals-of-theme-configuration