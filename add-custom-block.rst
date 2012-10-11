Add a custom App-Block to AlphaLemon CMS
========================================

This chapter explains how to add a new App-Block to AlphaLemon CMS.

What is a Block
---------------

A Block represents a content displayed on a web page. In addiction an **App-Block**
includes the editor to manage the block itself by AlphaLemon CMS, when the editor
is active.

An App-Block could still be used outside AlphaLemon CMS as web page element.

How is structured a Block
-------------------------

An App-Block is a standalone symfony2 bundle. This approach has several advantages:

1. Is a Symfony2 Bundle
2. Is reusable in many web sites
3. Assets required by the content are packed into a well known structure

Create the FancyBlockBundle
---------------------------

Since AlphaLemon CMS beta release is provided a command that generates the addictional
files required by an App-Block.

Run the following command from your console to generate a new App-Block bundle named
**FancyBlockBundle** that will live under the **src** folder:

.. code-block:: text

    php app/console alphalemon:generate:app-block --env=alcms

.. note::

    This command must be run under the alcms environment.

It will start the standard Symfony2 bundle generator command but will ask for some
more information.

    This command has an optional **--strict** parameter that forces you to define
    the bundle's namespace following the AlphaLemon's standard name and should be used
    when you plan to distribute the App-Block.

Let's see the bundle creation in detail.

.. code-block:: text

    Welcome to the Symfony2 bundle generator
    [...]

    Bundle namespace:

Enter the bundle name, as follows:

.. code-block:: text

    Bundle namespace: Acme/FancyBlockBundle

The proposed bundle name **must be changed** to FancyBlockBundle otherwise you might
have troubles:

.. code-block:: text

    Bundle name [AcmeFancyBlockBundle]: FancyBlockBundle

    The bundle can be generated anywhere. The suggested default directory uses
    the standard conventions.

The standard directory is fine:

.. code-block:: text

    The bundle can be generated anywhere. The suggested default directory uses
    the standard conventions.

    Target directory [/home/alphalemon/www/AlphaLemonCmsSandbox/src]:

Choose the format you prefer:

.. code-block:: text

    Determine the format to use for the generated configuration.

    Configuration format (yml, xml, php, or annotation) [annotation]:

Now you are asked for the App-Block description

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

This class can be placed everywhere into the FancyBlockBundle's folder, but it is a
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
            return array('HtmlContent' => '<p>Default content</p>');
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
This service must be available just only when AlphaLemon CMS is active, so when the
**alcms** environment is in use.

The command added a configuration file named **app_block.xml** under the
**Resources/config** folder of your bundle with the following code:

.. code-block:: xml

    <parameters>
        <parameter key="fancy_block.editor_settings" type="collection">
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
configuration file to define this service.

The service
~~~~~~~~~~~

A new service named **fancy_block.block** has been declared and its class is the
**AlBlockManagerFancyBlock** talked above.

This service requires as first argument an **AlContentEventsHandler** object, defined
by the **alpha_lemon_cms.events_handler** parameter.

This service is processed by a **Compiler Pass** so it has been tagged as
**alphalemon_cms.blocks_factory.block**.

The block's tag accepts serveral options:

1. **name**: identifies the block. Must always be **alphalemon_cms.blocks_factory.block**
2. **description**: the description that describes the block in the menu used to add a new block on the page
3. **type**: the block's class type which **must be** the Bundle's name without the Bundle suffix
4. **group**: blocks that belong the same group are kept togheter and displayed one next the other in the menu used to add a new block on the page

.. note::

    If you change your mind on description ad group names you chose when you run the
    command, you could change theme here mananually.

The editor
~~~~~~~~~~

Another parameter named **fancy_block.editor_settings** has been added to the configuration
file, to enable the AlphaLemon's CMS base editor.

The key that identifies this service must be defined as **[ extension_alias ].editor_settings**.

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

Add the block to the Dependency Injector Container
--------------------------------------------------
In the previous paragraph we saw that the service must be only enable in the **alcms**
environment.  To achieve this task a new file named **config_alcms.yml** has been
created under the **Resources/config** folder of your bundle, with the following code:

.. code-block:: text

    imports:
    - { resource: "@FancyBlockBundle/Resources/config/app_block.xml" }

This configuration has been reproduced for all the **alcms** configuration files,
so the **config_alcms.yml** to **config_alcms_dev.yml** files has been created.

Enabling the block
------------------

To have the bundle work, it has been enabled in the AppKernel class:

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

To check if everything is right, open AlphaLemonCMS in your browser, enter in **Edit mode**,
right click on a block and verify that the **Fancy block** entry has been added to
**Add** menu.

You made a great work since now, so, glad yourself and add the Fancy block to the page.

The editor
----------

The service's configuration exposed some paragraph above is not enough to have the
editor rendered, in fact an addiction twig template is required.

The command wizard has already been added this file for you under the **Resources/views/Block**
folder of the FancyBlockBundle, named **fancy_block_editor.html.twig**. The rule to
define this name is **[extension_alis]_editor.html.twig**

The added code is really simple:

.. code-block:: jinja

    //  AlphaLemon/Block/FancyBlockBundle/Resources/views/fancy_block_editor.html.twig
    {% extends 'AlphaLemonCmsBundle:Block:base_editor.html.twig' %}

in fact only a base twig template is defined.


Custom editor
~~~~~~~~~~~~~

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

Autoload your bundle
~~~~~~~~~~~~~~~~~~~~

It's quite difficult to ask a user that uses AlphaLemon CMS and wants to try your Bundle
to add it to the AppKernel file of his application.

For this reason AlphaLemon takes advantage of the **BootstrapBundle** that takes care
to autoload a bundle.

The command wizard has been added the autoload.json file under the Bundle's root folder.
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

.. note::

    When you take advantage of bundles autoloading, the bundle's entry in
    **AppKernel** file must ne removed.


Share your App-Bundle
---------------------

The Bundle just created is not shareable, because the bundle's namespace is not conformed
to AlphaLemon CMS standards.

At the beginning of this chapter the **--strict** option has been presented.

When the command runs with this option, it forces you to declare a namespace that follows
this rule:

    **AlphaLemon/Block/[BundleName]Bundle**

When you use this option a composer.json file is created under the Bundle's root folder.
Obviously it must be manually updated to reflect your repository setting.

VCS
~~~

The very first thing you need is to put your code under a **VCS tool**. You may use whatever you want, but it's strongly suggested to
use **git** as VCS and `github`_ as remote repository.

The composer.json file
~~~~~~~~~~~~~~~~~~~~~~

The Bundle is shared is by `composer`_ an awesome package manager tool. If you don't know it or how it works, there
is a great documentation on their site which explains how to start with it.

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

The composer.json file
~~~~~~~~~~~~~~~~~~~~~~

If you want to upgrade the bundle's namespace for the App-Block created in this tutorial,
you must rename all the namespaces created by the bundles generator wizard to reflect
the standard one.

So, to rename the namespaces you may use an editor that will replace all the occourences
of your old namespace to the new one:

    * old: Acme\FancyBlockBundle
    * new: AlphaLemon\Block\FancyBlockBundle

Packagist as remote repository, or not
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Publish your Bundle to **github** then add the Bundle to `packagist`_ to let it be
distributable by composer.

But there is a better solution instead using packagist: you should email us to add
your bundle to our packages system, to avoid spamming packagist with bundles made
for a specific application.

So feel free to write at **info [aT] alphalemon [DoT] com** to have your bundle managed
by our packagist.

Learn for existing App-Bundles
------------------------------

There are several full working, well commented App-Blocks you may explore, to learn how to add advanced configuration to create a
great App-Bundle for AlphaLemon CMS.

.. _`github`: http://github.com
.. _`composer`: http://getcomposer.org
.. _`packagist`: http://packagist.org
.. _`BootstrapBundle`: http://github.com/alphalemon/bootstrapbundle
