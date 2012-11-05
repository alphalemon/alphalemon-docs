Add a custom App-Block to AlphaLemon CMS
========================================

This chapter explains how to add a new App-Block to AlphaLemon CMS.

What is a Block
---------------

A Block represents a content displayed on a web page. In addiction an **App-Block**
includes the editor to manage the block itself by AlphaLemon CMS, when the page
is in editing mode.

An App-Block could still be used outside AlphaLemon CMS as web page element.

How is structured a Block
-------------------------

An App-Block is a standalone symfony2 bundle. This approach has several advantages:

1. Is a Symfony2 Bundle
2. Is reusable in many web sites
3. Assets required by the content are packed into a well known structure

Create the FancyBlockBundle
---------------------------

Since AlphaLemon CMS beta release, a command that generates the addictional
files required by an App-Block is provided.

Run the following command from your console to generate a new App-Block bundle named
**FancyBlockBundle** that will live under the **src** folder:

.. code-block:: text

    php app/console alphalemon:generate:app-block --env=alcms

.. note::

    This command must be run under the alcms environment.

It will start the standard Symfony2 bundle generator command but will ask for some
more information.

Naming convenctions
~~~~~~~~~~~~~~~~~~~

An AppBlock should always be created using a well defined namespace:

.. code-block:: text

    AlphaLemon/Block/[Bundle Name]Bundle

When you follow this convenction, you get two advantages:

1. The App-Block is distributable by composer
2. The App-Block is autoloaded without changing anything

If you prefer not to follow this convenctions, you must run that command with the 
**--no-strict** options, then you must change the BundlesAutoloader  instantation 
in your **AppKernel** file as follows:

.. code-block:: php

    public function registerBundles()
    {
        [...]

        $bootstrapper = new \AlphaLemon\BootstrapBundle\Core\Autoloader\BundlesAutoloader(__DIR__, $this->getEnvironment(), $bundles, null, array([path 1], [path 2]));
    }

replacing the **[path n]** entries with the paths of your App-Blocks. 

Let's suppose you want to add a new App-Block under the **src/Acme/Blocks/MyAwesomeBlockBundle** 
folder of your application. The path to pass to the **BundlesAutoloader** is the following:

.. code-block:: php
    
    __DIR__ . '/../src/Acme/Blocks'

App-Block generation
~~~~~~~~~~~~~~~~~~~~

Let's see the bundle generation in detail.

.. code-block:: text

    Welcome to the Symfony2 bundle generator
    [...]

    Bundle namespace:

Enter the bundle name, as follows:

.. code-block:: text

    Bundle namespace: AlphaLemon/Block/FancyBlockBundle

The proposed bundle name **must be changed** to FancyBlockBundle otherwise you might
have troubles:

.. code-block:: text

    Bundle name [AlphaLemonBlockFancyBlockBundle]: FancyBlockBundle

    The bundle can be generated anywhere. The suggested default directory uses
    the standard conventions.

The standard directory is fine:

.. code-block:: text

    The bundle can be generated anywhere. The suggested default directory uses
    the standard conventions.

    Target directory [/home/alphalemon/www/AlphaLemonCmsSandbox/src]:

The generator asks you to choose the format you prefer because the standard Bundles'
generator asks for that, but, at the moment, AlphaLemon ignores your choice and uses the
annotation type by defaulr:

.. code-block:: text

    Determine the format to use for the generated configuration.

    Configuration format (yml, xml, php, or annotation) [annotation]:

Now you are asked for the App-Block description, which is the one displayed in the
contextual menu used to add a block to page:

.. code-block:: text

    Please enter the description that identifies your App-Block content.
    The value you enter will be displayed in the adding menu.

    App-Block description: Fancy block

Then you are asked for the App-Block group. App-Blocks that belongs the same group
are kept toghter in the block adding menu.

.. code-block:: text

    Please enter the group name to keep toghether the App-Blocks that belongs that group.

    App-Block group: my-group

Don't forget to let the command updates the AppKernel for you to enable the bundle.

.. note::

    This command does not manipulates the site's routes.

Well done! Your very first App-Bundle has been created!

The basis of AlBlockManager object
----------------------------------

AlphaLemon CMS requires you to implement a new class derived from the **AlBlockManager**
object.

This class can be placed everywhere into the FancyBlockBundle folder, but it is a
best practice to ad it under the **FancyBlockBundle/Core/Block** folder.

The command just run had already added this class for you, as follows:

.. code-block:: php

    namespace Acme\FancyBlockBundle\Core\Block;

    use AlphaLemon\AlphaLemonCmsBundle\Core\Content\Block\AlBlockManager;

    /**
    * Description of AlBlockManagerFancyBlock
    */
    class AlBlockManagerFancyBlock extends AlBlockManager
    {
        public function getDefaultValue()
        {
            return array('Content' => '<p>Default content</p>');
        }
    }

This new object simply extends the AlBlockManager base class and implements the **getDefaultValue**
method required by the parent object.

This implemented function defines the default value displayed on the web page, when
a new content is added, with a standard value, and should obviously changed to fit
your needs.

This method returns and array, which may have the following options:

1. HtmlContent
2. ExternalStylesheet
3. InternalStylesheet
4. ExternalJavascript
5. InternalJavascript

Combining the available options, you may have the desidered behaviour the block
must have, when it is added to the web page.

.. note::

    The ExternalStylesheet and ExternalJavascript must contain a string of assets
    separated by a comma value.

How to tell AlphaLemonCMS to manage the Bundle
----------------------------------------------

An App-Block Bundle is declared as services in the **Dependency Injector Container**.

The command has added a configuration file named **app_block.xml** under the **Resources/config**
folder of your bundle with the following code:

.. code-block:: xml

    <parameters>
        <parameter key="fancyblock.editor_settings" type="collection">
            <parameter key="rich_editor">true</parameter>
        </parameter>

        <parameter key="fancy_block.block.class">Acme\FancyBlockBundle\Core\Block\AlBlockManagerFancyBlock</parameter>
    </parameters>

    <services>
        <service id="fancy_block.block" class="%fancy_block.block.class%">
            <argument type="service" id="alpha_lemon_cms.events_handler" />
            <tag name="alphalemon_cms.blocks_factory.block" description="Fancy block" type="FancyBlock" group="" />
        </service>
    </services>

While the config file name is not mandatory, it is a best practice to use a separated
configuration file to define this service, to keep decoupled the configuration used 
in production from the configuration used by AlphaLemon CMS

The service
~~~~~~~~~~~

A new service named **fancy_block.block** has been declared and its class is the
**AlBlockManagerFancyBlock** talked above.

This service is processed by a **Compiler Pass** so it has been tagged as
**alphalemon_cms.blocks_factory.block**.

The block's tag accepts serveral options:

1. **name**: identifies the block. Must always be **alphalemon_cms.blocks_factory.block**
2. **description**: the description that describes the block in the menu used to add a new block on the page
3. **type**: the block's class type which **must be** the Bundle name without the Bundle suffix
4. **group**: blocks that belong the same group are kept togheter and displayed one next the other in the menu used to add a new block on the page

.. note::

    If you change your mind on description ad group names you chose when you run the
    command, you could change theme here mananually.

The editor configuration
~~~~~~~~~~~~~~~~~~~~~~~~

Another parameter named **fancyblock.editor_settings** has been added to the configuration
file, to enable the AlphaLemon's CMS base editor.

The key that identifies this service must be defined as **[ block type in lower case ].editor_settings**.

This editor manages all the properties related to the content:

1. The html content (rich_editor / html_editor)
2. The internal javascript (internal_js)
3. The internal stylesheet (internal_css)
4. The external javascripts (external_js)
5. The external stylesheets (external_css)

Each aspect is managed in a separate tab, so if you need to enable the section that
manages the html editor in rich mode and the section to manage the external javascripts,
you must configure your parameter as follows:

.. code-block:: xml

    // AlphaLemon/Block/FancyBlockBundle/Resources/config/service.xml
    <parameter key="fancyblock.editor_settings" type="collection">
        <parameter key="rich_editor">true</parameter>
        <parameter key="external_js">true</parameter>
    </parameter>

Enabling the block
------------------

To have the bundle workinkg, it has been enabled in the AppKernel class:

.. code-block:: php

    //  app/AppKernel.php
    public function registerBundles()
    {
        $bundles = array(
            [...]
            new Acme\FancyBlockBundle\FancyBlockBundle(),
        );

        [...]
    }

To check if everything works fine, open AlphaLemonCMS in your browser, enter in **Edit mode**,
right click on a block and verify that the **Fancy block** entry has been added to
**Add** menu.

You made a great work since now, so, glad yourself and add the Fancy block to the page.

The editor
----------

The service's configuration exposed some paragraph above is not enough to have the
editor rendered, in fact an addictional twig template is required.

The command wizard has already added this file for you under the **Resources/views/Block**
folder of the FancyBlockBundle and has named it **fancyblock_editor.html.twig**. The rule to
define this name is **[block type in lower case]_editor.html.twig**

The added code is really simple:

.. code-block:: jinja

    //  AlphaLemon/Block/FancyBlockBundle/Resources/views/fancy_block_editor.html.twig
    {% extends 'AlphaLemonCmsBundle:Block:base_editor.html.twig' %}

in fact it just extends the base twig template provided by AlphaLemon CMS.


Add a custom editor
~~~~~~~~~~~~~~~~~~~

Sometimes you may need to add a custom editor. What you need to do is to **follow the
naming conventions** exposed before to correctly name the editor, then add your custom
code to the editor.

An example could be this one:

.. code-block:: jinja

    {% extends 'AlphaLemonCmsBundle:Elfinder:media_library.html.twig' %}

    {% block init_script %}
    <script type="text/javascript" charset="utf-8">
        $(document).ready(function() {
            $('<div/>').dialogelfinder({
                url : frontController + 'backend/' + $('#al_available_languages').val() + '/al_elFinderMediaConnect',
                lang : 'en',
                width : 840,
                destroyOnClose : true,
                commandsOptions : {
                    getfile : {
                        onlyURL  : false,
                    }
                },
                handlers: {
                    destroy: function(event){ isEditorOpened = false;$('#al_editor_dialog').dialog('destroy').remove(); }
                },
                getFileCallback : function(file, fm) {
                    $('#al_file').val(file.path);
                    $('#al_file').EditBlock('HtmlContent');
                    $('#al_file').val('');
                }
            }).dialogelfinder('instance');
        });
    </script>
    {% endblock %}

which renders the ElFinder media library tool.

Assets
------

Adding the assets required by your App-Block is quite simple, just add some parameters to 
a configuration file.

Assets must be available both in production and when AlphaLemon CMS is active, so you
must adde them to the **services.xml** file.

To add some external assets open the **Resources/config/services.xml** file and
add the following:

.. code-block:: xml

    // FancyBlockBundle/Resources/config/services.xml
    <parameters>
        <parameter key="fancyblock.external_stylesheets" type="collection">
            <parameter>@FancyBlockBundle/Resources/public/css/style.css</parameter>
        </parameter>

        <parameter key="fancyblock.external_javascripts" type="collection">
            <parameter>@FancyBlockBundle/Resources/public/js/cufon-yui.js</parameter>
            <parameter>@FancyBlockBundle/Resources/public/js/al-cufon-replace.js</parameter>
        </parameter>
    </parameters>

So assets are added as a collection and the parameter is always named as follows:

.. code-block:: text
    
    [block name in lower case].external_stylesheets
    [block name in lower case].external_javascripts

If you need to add one or more assets only when the editor is active, add another collection
of assets and suffix the parameter name with **.cms**:

.. code-block:: xml

    <parameter key="fancyblock.external_stylesheets.cms" type="collection">
        <parameter>@FancyBlockBundle/Resources/public/css/fancy-cms.css</parameter>
    </parameter>

If you need to use some libraries used by AlphaLemon CMS, you must link the one used 
by AlphaLemon to avoid conficts. Those are saved in the ThemeEngineBundle and can be linked
as follows:

.. code-block:: xml

    @AlphaLemonThemeEngineBundle/Resources/public/js/vendor/jquery/jquery-last.min.js
    @AlphaLemonThemeEngineBundle/Resources/public/js/vendor/jquery-ui.min.js
    @AlphaLemonThemeEngineBundle/Resources/public/js/vendor/jquery.easing-1.3.js
    @AlphaLemonThemeEngineBundle/Resources/public/js/vendor/jquery.metadata.js
    @AlphaLemonThemeEngineBundle/Resources/public/js/vendor/jquery.ui.position.js


Autoload your bundle
~~~~~~~~~~~~~~~~~~~~

It's quite difficult to ask a user that uses AlphaLemon CMS and wants to try your Bundle
to add it to the AppKernel file of his application.

For this reason AlphaLemon takes advantage of the **BootstrapBundle** that takes care
to autoload a bundle.

The command wizard has been added the autoload.json file under the Bundle root folder.
Here's the code:

.. code-block:: text

    // autoload.json
    {
        "bundles" : {
            "Acme\\FancyBlockBundle\\FancyBlockBundle" : {
                "environments" : ["all"]
            }
        }
    }

This argument is well documented in the `BootstrapBundle`_ README file.


Enable the block service only for alcms environment
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

When you use your block declared in the AppKernel, as in this example, the block manager service is always 
loaded, but it is not needed in production, for this reason the command wizard has 
added some configuration files, managed by the **BootstrapBundle** to avoid loading
the block manager service in production.

This is made adding a new file named **config_alcms.yml** under the **Resources/config** 
folder of your bundle, with the following code:

.. code-block:: text

    imports:
    - { resource: "@FancyBlockBundle/Resources/config/app_block.xml" }

This configuration has been reproduced for all the **alcms** configuration files,
so the **config_alcms.yml** to **config_alcms_dev.yml** files has been created.

Share your App-Bundle
---------------------

If you followed the naming conventions exposed at the beginning of this chapter, your
App-Block is ready to be distributed.

A composer.json file has been created under the Bundle root folder. Obviously it must be 
manually updated to reflect your repository setting.

VCS
~~~

The very first thing you need is to put your code under a **VCS tool**. You may use 
whatever you want, but it's strongly suggested to use **git** as VCS and `github`_ 
as remote repository.

The composer.json file
~~~~~~~~~~~~~~~~~~~~~~

The Bundle is shared is by `composer`_ an awesome package manager tool. If you don't 
know it or how it works, there is a great documentation on their site which explains 
how to start with it.

The composer.json added by the command contains this code:

.. code-block:: text

    {
        "autoload": {
            "psr-0": { "AlphaLemon\\Block\\FancyBlockBundle\\FancyBlockBundle": ""
            }
        },
        "target-dir" : "AlphaLemon/Block/FancyBlockBundle",
        "minimum-stability": "dev"
    }

Adapt the App-Block to be distributable 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you have generated a bundle that does not follow the naming convenction, you can 
upgrade the bundle namespace renaming all the namespaces created by the bundles generator 
wizard.

So, to rename the namespaces you may use an editor that will replace all the occourences
of your old namespace to the new one:

    * old: Acme\FancyBlockBundle
    * new: AlphaLemon\Block\FancyBlockBundle

Packagist as remote repository... or not
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Publish your Bundle to **github** then add the Bundle to `packagist`_ to let it be
distributable by composer.

But there is a better solution instead using packagist: you should email us to add
your bundle to our packages system, to avoid spamming packagist with bundles made
for a specific application.

So feel free to write at **info [aT] alphalemon [DoT] com** to have your bundle managed
by our packagist.

Learn for existing App-Bundles
------------------------------

There are several full working, well commented App-Blocks you may explore, to learn how 
to add advanced configuration to create a great App-Bundle for AlphaLemon CMS.

.. _`github`: http://github.com
.. _`composer`: http://getcomposer.org
.. _`packagist`: http://packagist.org
.. _`BootstrapBundle`: http://github.com/alphalemon/bootstrapbundle