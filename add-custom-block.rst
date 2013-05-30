Add a custom App-Block to AlphaLemon CMS
========================================

This chapter explains how to create a custom App-Block and use it with AlphaLemon CMS. 
In detail it explains how the create, the **BootstrapButtonTutorialBlockBundle**, which 
lets you manage a Twitter Bootstrap button using a nice ajax interface.

In this chapter you will learn:

    1. How to create a new App-Block using the built-in command
    2. How to create a new object to manage the content rendered on the page
    3. How to create a service that handles that object
    4. How to manage a json content instead of an html content
    5. How to create a template to display the content
    6. How to create an editor to manage the content


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

Create the BootstrapButtonTutorialBlockBundle
---------------------------------------------

Since AlphaLemon CMS 1.0 beta release, a command to generate the addictional
files required by an App-Block is provided.

Run the following command from your console to generate a new App-Block bundle named
**BootstrapButtonTutorialBlockBundle** that will live under the **src** folder:

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

    // app/AppKernel.php
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

    Bundle namespace: AlphaLemon/Block/BootstrapButtonTutorialBlockBundle

The proposed bundle name **must be changed** to BootstrapButtonTutorialBlockBundle otherwise you might
have troubles:

.. code-block:: text

    Bundle name [AlphaLemonBlockBootstrapButtonTutorialBlockBundle]: BootstrapButtonTutorialBlockBundle

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

    App-Block description: Button Tutorial

Then you are asked for the App-Block group. App-Blocks that belongs the same group
are kept toghter in the block adding menu.

.. code-block:: text

    Please enter the group name to keep toghether the App-Blocks that belongs that group.

    App-Block group: bootstrap,Twitter Bootstrap
    
Don't forget to let the command updates the AppKernel for you to enable the bundle.

.. note::

    This command does not manipulates the site's routes.

Well done! Your very first App-Bundle has been created! The App-Block just created is
already usable.

The basis of AlBlockManager object
----------------------------------

AlphaLemon CMS requires you to implement a new class derived from the **AlBlockManager**
object. This object manages a simple html content, but to define a Twitter Bootstrap button,
we must define several parameters to manage the aspect of this block:

    - The displayed text
    - The type (primary, info, success ...)
    - The size
    - If it spans the parent's full width
    - If it is disabled
    
The best way to manage a content like this, is to define it in a json format. AlphaLemon 
CMS provides the  **AlBlockManagerJsonBlock** class that inherits from AlBlockManager 
to manage this kind of contents. 

In addiction there is another derived class, the **AlBlockManagerJsonBlockContainer**
class which derives from the **AlBlockManagerJsonBlock** which requires as first argument
the Symfony2 container: this is the object we will use for this block.

This class can be placed everywhere into the bundle's folder, but it is a best practice 
to add it insite the **[Bundle]/Core/Block** folder.

The command just run had already added this class for you, as follows:

.. code-block:: php

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Core/Block/AlBlockManagerBootstrapButtonTutorialBlock.php  
    namespace AlphaLemon\BootstrapButtonTutorialBlockBundle\Core\Block;

    use AlphaLemon\AlphaLemonCmsBundle\Core\Content\Block\JsonBlock\AlBlockManagerJsonBlockContainer;

    /**
    * Description of BootstrapButtonTutorialBlockBundle
    */
    class BootstrapButtonTutorialBlockBundle extends AlBlockManagerJsonBlockContainer
    {
        public function getDefaultValue()
        {
            $value = 
                '
                    {
                        "0" : {
                            "block_text": "Default value"
                        }
                    }
                ';

            return array('Content' => $value);
        }

        protected function renderHtml()
        {
            // Examined later
        }

        public function editorParameters()
        {
            // Examined later
        }
    }

This new object simply extends the **AlBlockManagerJsonBlockContainer** base class and 
implements the **getDefaultValue** method required by the parent object.

This implemented function defines the default value displayed on the web page, when
a new content is added, with a standard value, and should obviously changed to fit
your needs.

This method returns and array, which may have the following options:

1. Content
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

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Resources/config/app_block.xml
    <parameters>
        <parameter key="bootstrap_button_tutorial_block.block.class">AlphaLemon\Block\BootstrapButtonTutorialBlockBundle\Core\Block\AlBlockManagerBootstrapButtonTutorialBlock</parameter>
    </parameters>

    <services>        
        <service id="bootstrap_button_tutorial_block.block" class="%bootstrap_button_tutorial_block.block.class%">
            <tag name="alphalemon_cms.blocks_factory.block" description="Button" type="BootstrapButtonBlock" group="bootstrap,Twitter Bootstrap" />
            <argument type="service" id="service_container" />
        </service>
    </services>

While the config file name is not mandatory, it is a best practice to use a separated
configuration file to define this service, to keep decoupled the configuration used 
in production from the configuration used by AlphaLemon CMS

The service
~~~~~~~~~~~

A new service named **bootstrap_button_tutorial_block.block** has been declared and adds the
**BootstrapButtonTutorialBlockBundle** object to the **Dependency Injector Container**.

This service is processed by a **Compiler Pass** so it has been tagged as **alphalemon_cms.blocks_factory.block**.

The block's tag accepts serveral options:

1. **name**: identifies the block. Must always be **alphalemon_cms.blocks_factory.block**
2. **description**: the description that describes the block in the menu used to add a new block on the page
3. **type**: the block's class type which **must be** the Bundle name without the Bundle suffix
4. **group**: blocks that belong the same group are kept togheter and displayed one next the other in the menu used to add a new block on the page

.. note::

    If you change your mind on description ad group names you chose when you run the
    command, you could change theme here mananually.
        
Customize the autogenerated AlBlockManagerBootstrapButtonTutorialBlock
----------------------------------------------------------------------

Change the AlBlockManagerBootstrapButtonTutorialBlock class as follows:

.. code-block:: php

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Core/Block/AlBlockManagerBootstrapButtonTutorialBlock.php
    use AlphaLemon\AlphaLemonCmsBundle\Core\Content\Block\JsonBlock\AlBlockManagerJsonBlockContainer;

    class AlBlockManagerBootstrapButtonTutorialBlock extends AlBlockManagerJsonBlockContainer
    {
        public function getDefaultValue()
        {
            $value = 
                '
                    {
                        "0" : {
                            "button_text": "Button 1",
                            "button_type": "",
                            "button_attribute": "",
                            "button_tutorial_block": "",
                            "button_enabled": ""
                        }
                    }
                ';
            
            return array('Content' => $value);
        }
    }
    
.. note::
    
    The AlBlockManagerJson by default manages a list of items. This bundle manages only
    one item, so we could avoid to define the item 0, but the json is written to respect 
    consistency.

Render the block's content
--------------------------

**AlBlockManager** object provides the **getHtml** method to return the html rendered 
from the block's content. 

By default this method renders the **AlphaLemonCmsBundle:Block:base_block.html.twig** view:

.. code-block:: jinja

    // AlphaLemon/AlphaLemonCmsBundle/Resources/views/Block/base_block.html.twig
    {{ block is defined ? block.getContent|raw : "" }}
    
This simple view renders the text saved into the block's content field or returns a blank
string when any block is given.

It's quite easy to understand that this view is not so useful to render our json block,
so we need to extend the getHtml method to render another view, but since 1.1.x release, 
this method has been declared as **final**, so it is not overridable anymore. 

Luckylly it calls the **renderhtml** protected method, the one that must be extended to render 
a different view than the default one. 

This method has already been added by the command that generates the App-Block:

.. code-block:: php

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Core/Block/AlBlockManagerBootstrapButtonTutorialBlock.php
    protected function renderHtml()
    {
        $items = $this->decodeJsonContent($this->alBlock->getContent());

        return array('RenderView' => array(
            'view' => 'BootstrapButtonTutorialBlockBundle:Content:bootstrapbuttontutorialblock.html.twig',
            'options' => array('item' => $items[0]),
        ));
    }
    
This method overrides the default **renderHtml** method. Content is decoded and the
item is passed to the **BootstrapButtonTutorialBlockBundle:Content:bootstrapbuttontutorialblock.html.twig** view.

Let's give a small customization. Replace the view as follows:

.. code-block:: php

    return array('RenderView' => array(
        'view' => 'BootstrapButtonTutorialBlockBundle:Button:button.html.twig',
        'options' => array('data' => $items[0]),
    ));

then rename the **views/Content** folder as **views/Button** and the template file name 
from **bootstrapbuttontutorialblock.html.twig** to **button.html.twig**.

.. note::

    This change introduces a Compatibility Break with the previous release. To adapt
    your old methods to the new object, simply replace the getHtml with the new renderHtml
    method, respecting the method modifier.
    
The button template
~~~~~~~~~~~~~~~~~~~

The **button.html.twig** template contains the following code:

.. code-block:: jinja

    {% extends "AlphaLemonCmsBundle:Editor:base_editor.html.twig" %}

    {% block body %}

    {# Customize this code to render your content #}
    <div {{ editor|raw }}>{{ item.block_text }}</div>
    
    {% endblock %}

change it as follows

.. code-block:: jinja

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Resources/views/Button/button.html.twig
    {% extends "AlphaLemonCmsBundle:Editor:base_editor.html.twig" %}
    
    {% block body %}
    
    {% set button_type = (data.button_type is defined and data.button_type) ? " " ~ data.button_type : "" %}
    {% set button_attribute = (data.button_type is defined and data.button_type) ? " " ~ data.button_attribute : "" %}
    {% set button_text = (data.button_text is defined and data.button_text) ? " " ~ data.button_text : "Click me" %}
    {% set button_tutorial_block = (data.button_tutorial_block is defined and data.button_tutorial_block) ? " " ~ data.button_tutorial_block : "" %}
    {% set button_enabled = (data.button_enabled is defined and data.button_enabled) ? " " ~ data.button_enabled : "" %}

    {# Customize this code to render your content #}
    <button class="btn{{ button_type }}{{ button_attribute }}{{ button_tutorial_block }}{{ button_enabled }}">{{ button_text }}</button>
    
    {% endblock %}
    
The button template is quite simple: we check if all the expected params are defined, then 
these parameters are passed to button tag.

The editor
----------

Since AlphaLemon CMS 1.1.x the blocks's editor is rendered into a Twitter Bootstrap
popover. This component defines the html text into the **data-content** RDF annotation
and, while this parameter is settable by javascript, AlphaLemon CMS uses the classical
approach: this means that the editor is directly bundled with the content into that
RDF annotation.

You might have noticed that the **button.html.twig** template already extends the 
**AlphaLemonCmsBundle:Editor:base_editor.html.twig** which defines the attribute used
by AlphaLemon CMS to render the block, which is assigned to **editor** variable in the
parent template. 

To have the editor injected into the button tag, you must add the **{{ editor|raw }}**
instruction into the html tag which will be updated, in our case the button tag.

Change the code has follows

.. code-block:: jinja

    <button class="btn{{ button_type }}{{ button_attribute }}{{ button_tutorial_block }}{{ button_enabled }}" {{ editor|raw }}>{{ button_text }}</button>

The instruction just added, simple adds the **data-editor="true"** attribute to the html
tag which is replaced with the editor data, when the page is rendered

The editor template
~~~~~~~~~~~~~~~~~~~

The interface that manages the button attributes is designed implementing a Symfony2 
form.

The commands generator already added an editor for you, the **bootstrapbuttontutorialblock.html.twig**
template under the **views/Editor** folder.

As we did for the button content, let's renaming it as **button_editor.html.twig**. The
template contains the following code:

.. code-block:: jinja

    {% include "AlphaLemonCmsBundle:Editor:base_editor_form.html.twig" %}
    
The template includes the **AlphaLemonCmsBundle:Editor:base_editor_form.html.twig** a template
deputated to render a generic form and renders a button to save the changes.

.. note::

    AlphaLemon CMS automatically attaches an handler to **.al_editor_save** element, 
    to save contents. In the next chapter you will learn how to override this method.

The editor form
~~~~~~~~~~~~~~~

The commands generator already added a base form, the **BootstrapButtonTutorialBlockType.php** 
class inside the **Core/Form** folder. So let's renaming it as **AlButtonType.php**. 

This form contains the following code:

.. code-block:: php

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Core/Form/AlButtonType.php
    namespace AlphaLemon\Block\BootstrapButtonTutorialBlockBundle\Core\Form;

    use AlphaLemon\AlphaLemonCmsBundle\Core\Form\JsonBlock\JsonBlockType;
    use Symfony\Component\Form\FormBuilderInterface;

    class AlBootstrapButtonTutorialBlockType extends JsonBlockType
    {
        public function buildForm(FormBuilderInterface $builder, array $options)
        {
            parent::buildForm($builder, $options);

            // Add here your fields
            $builder->add('block_text');
        }
    }
    
we must rename the class to **AlButtonType** and add the fields required to manage the
button's attributes. Change the class as follows:

.. code-block:: php

    class AlButtonType extends JsonBlockType
    {
        public function buildForm(FormBuilderInterface $builder, array $options)
        {
            parent::buildForm($builder, $options);
            
            // Add here your fields
            $builder->add('button_text');
            $builder->add('button_type', 'choice', array('choices' => array('' => 'base', 'btn-primary' => 'primary', 'btn-info' => 'info', 'btn-success' => 'success', 'btn-warning' => 'warning', 'btn-danger' => 'danger', 'btn-inverse' => 'inverse')));
            $builder->add('button_attribute', 'choice', array('choices' => array("" => "normal", "btn-mini" => "mini", "btn-small" => "small", "btn-large" => "large")));
            $builder->add('button_tutorial_block', 'choice', array('choices' => array("" => "normal", "btn-block" => "block")));
            $builder->add('button_enabled', 'choice', array('choices' => array("" => "enabled", "disabled" => "disabled")));
        }
    }
    
The form inherits from the **JsonBlockType** form which defines the form's name used
to retrieve the values from the ajax transaction used to save the form values and
removes the csrf_protection to false.

While it is not mandatory, this form is added to **DIC**, so the commands generator 
has defined it in the **app_block.xml** file as follows:

.. code-block:: xml

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Resources/config/app_block.xml
    <parameters>
        [...]

        <parameter key="bootstrap_button_tutorial_block.form.class">AlphaLemon\Block\BootstrapButtonTutorialBlockBundle\Core\Form\AlBootstrapButtonTutorialBlockType</parameter>        
    </parameters>

    <services>       
        [...]

        <service id="bootstrap_button_tutorial_block.form" class="%bootstrap_button_tutorial_block.form.class%">
        </service>
    </services>
    
You must change the form's class definition as follows:

.. code-block:: xml
    
    <parameter key="bootstrap_button_tutorial_block.form.class">AlphaLemon\Block\BootstrapButtonTutorialBlockBundle\Core\Form\AlButtonType</parameter>        
    
Render the editor
~~~~~~~~~~~~~~~~~
To render the editor we must pass this form to the editor itself. This task is achieved
by the **editorParameters** method which has been added to AlBlockManagerBootstrapButtonTutorialBlock
by the generator command.

This method is used to define the parameter which are passed to the editor and overrides 
the method defined in the **AlBlockManager** object, which returns an empty array by default:

.. code-block:: php

    public function editorParameters()
    {
        $items = $this->decodeJsonContent($this->alBlock->getContent());
        $item = $items[0];

        $formClass = $this->container->get('bootstrapbuttontutorialblock.form');
        $form = $this->container->get('form.factory')->create($formClass, $item);

        return array(
            "template" => 'BootstrapButtonTutorialBlockBundle:Editor:bootstrapbuttontutorialblock.html.twig',
            "title" => "My awesome App-Block",
            "form" => $form->createView(),
        );
    }

We need to adjust it a little to reflect the changes we made:


.. code-block:: php

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Core/Block/AlBlockManagerBootstrapButtonTutorialBlock.php
    public function editorParameters()
    {
        $items = $this->decodeJsonContent($this->alBlock->getContent());
        $item = $items[0];
        
        $formClass = $this->container->get('bootstrap_button_tutorial_block.form');
        $buttonForm = $this->container->get('form.factory')->create($formClass, $item);
        
        return array(
            "template" => "BootstrapButtonTutorialBlockBundle:Editor:button_editor.html.twig",
            "title" => "Button editor",
            "form" => $buttonForm->createView(),
        );
    }
    
This function generates the form and then returns an array which contains the template
to render, the title displayed on the popover and the form.

Use your App-Block
------------------

To use your new App-Block, just add it to your website!
    
Conclusion
----------

After reading this chapter you should be able to create a new App-Block using the built-in 
command, create a new object to manage the content rendered on the page, create a service 
that handles that object, manage a json content instead of an html content, create a 
template to display the content and create an editor to manage the content.


.. class:: fork-and-edit

Found a typo ? Something is wrong in this documentation ? `Just fork and edit it !`_

.. _`Just fork and edit it !`: https://github.com/alphalemon/alphalemon-docs