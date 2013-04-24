App-Block with Included Blocks
==============================

This chapter explains how to create a custom App-Block which includes other blocks.

In this chapter you will learn:

    1. Create a block that includes other blocks
    2. Add an editor for the main block
    3. Make a block not wisible in the adder menu.
    4. Create a block that manage a list of blocks
    5. Use the inlinelist plugin

The Thumbnail Block
-------------------

The Thumbnail Block is a perfect example to explain this feature. 

.. note::

    Just for convenience this App-Block is added to the **BootstrapButtonTutorialBlockBundle**,
    in the real world it lives on dedicated bundle.
    
Add an App-Block to an existing bundle
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Add the **AlBlockManagerBootstrapThumbnailTutorialBlock.php** file inside the **Core/Block**, 
open it and paste the following code:

.. code-block:: php

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Core/Block/AlBlockManagerBootstrapThumbnailTutorialBlock.php
    namespace AlphaLemon\Block\BootstrapButtonTutorialBlockBundle\Core\Block;

    use AlphaLemon\AlphaLemonCmsBundle\Core\Content\Block\JsonBlock\AlBlockManagerJsonBlockContainer;

    class AlBlockManagerBootstrapThumbnailTutorialBlock extends AlBlockManagerJsonBlockContainer
    {
        public function getDefaultValue()
        {
            $value = '
                {
                    "0" : {
                        "width": "span3"
                    }
                }';
            
            return array('Content' => $value);
        }
        
        protected function renderHtml()
        {
            $items = $this->decodeJsonContent($this->alBlock->getContent());
            $item = $items[0];
            
            return array('RenderView' => array(
                'view' => 'BootstrapButtonTutorialBlockBundle:Thumbnail:thumbnail.html.twig',
                'options' => array(
                    'thumbnail' => $item,
                ),
            ));
        }
        
        public function editorParameters()
        {
            $items = $this->decodeJsonContent($this->alBlock->getContent());
            $item = $items[0];
            
            $formClass = $this->container->get('bootstrap_thumbnail.form');
            $form = $this->container->get('form.factory')->create($formClass, $item);
            
            return array(
                "template" => 'BootstrapThumbnailBlockBundle:Editor:_thumbnail_editor.html.twig',
                "title" => "Thumbnail editor",
                "form" => $form->createView(),
            );
        }
    }
    
Both the **renderHtml** and **editorParameters** should be familiar because we talked about 
them, in the other chapters about custom App-Blocks.

The template
~~~~~~~~~~~~

To include a block inside another block, you must use the twig's **renderIncludedBlock** 
function, added by AlphaLemon CMS.

This method requires, as mandatory attribute, the key name which is the same of **slotName**
attribute you use in `renderSlot`_ method, when you build a theme.

This attribute is a string that identifies unequivocally the place on the page, where
the block is rendered.

There are several possibilities to achieve this task, the one we suggest is to define this
key as follows:

.. code-block:: text 

    Block's id ~ "-" ~ Key in the array of items ~ "-" ~ Block number

The first token of this key string is the **id of the current block**.

The second token of this key string is an array key, in fact a json block is always defined 
as a list of items that belongs an array, so we can use the array's key of current item.

The third element is a zero based progressive number manually added, that defines the
block's position.

Add the **thumbnail.html.twig** inside the **Resources/views/Thumbnail**, open it
and paste the following code:

.. code-block:: jinja

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Resources/views/Thumbnail/thumbnail.html.twig
    {% extends "AlphaLemonCmsBundle:Editor:base_editor.html.twig" %}

    {% block body %}
    {% if key is not defined %}
    {% set key = 0 %}
    {% endif %}
    {% set baseSlotName = block_manager.get.getId ~ "-" ~ key %} 
    <li{% if thumbnail.width is defined and thumbnail.width != "none" %} class="{{ thumbnail.width }}"{% endif %} {{ editor|raw }}>
        <div class="thumbnail">
            {% set blockSlotName = baseSlotName ~ "-0" %}  
            {{ renderIncludedBlock(blockSlotName, block_manager, "Image", true) }}
            <div class="caption">
                {% set blockSlotName = baseSlotName ~ "-1" %} 
                {{ renderIncludedBlock(blockSlotName, block_manager, "Text", true) }}
            </div>
        </div>
    </li>
    {% endblock %}


The first part of the key here is defined by the following instruction:

.. code-block:: jinja

    {% set baseSlotName = block_manager.get.getId ~ "-" ~ key %} 
    
the current block's id is fetched from the **block_manager** object which is always
passed to the editor and the **key** is passed to the template as a parameter from another
template or is defined as **0** when the **key** parameter is not defined:

.. code-block:: jinja

    {% if key is not defined %}
    {% set key = 0 %}
    {% endif %}

The included block is rendered as follows

.. code-block:: jinja
            
    {% set blockSlotName = baseSlotName ~ "-0" %}  
    {{ renderIncludedBlock(blockSlotName, block_manager, "Image", true) }}
    
The first step is to define the **blockSlotName** variable which joins the block's
position, **0** in this example, to **baseSlotName** variabl,e then the key is passed to
the **renderIncludedBlock** method.

In addiction the **renderIncludedBlock** accepts the following arguments:

    - **parent**: the parent block manager. **Default: null**
    - **type**: the block type to add. **Default: Text**
    - **addWhenEmpty**: when true adds a block when the slot is empty. **Default: false**
    - **defaultContent**: when empty, it uses the block's default text. **Default: empty**
    - **extraAttributes**: adds extra attributes to the tag where the editor is rendered. **Default: empty**
    

The editor form
~~~~~~~~~~~~~~~

This block includes other blocks, but requires an editor to change the width of the thumbnail.
We use a Symfony2 form which has only a combo box which contains the available images
widths.

Add the **AlThumbnailType.php** file inside the **Core/Form**, 
open it and paste the following code:

.. code-block:: php

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Core/Form/AlThumbnailType.php
    namespace AlphaLemon\Block\BootstrapButtonTutorialBlockBundle\Core\Form;

    use AlphaLemon\AlphaLemonCmsBundle\Core\Form\JsonBlock\JsonBlockType;
    use Symfony\Component\Form\FormBuilderInterface;

    class AlThumbnailType extends JsonBlockType
    {
        public function buildForm(FormBuilderInterface $builder, array $options)
        {
            parent::buildForm($builder, $options);
            
            $builder->add('width', 'choice', 
                array('choices' => 
                    array(
                        'none' => 'none',
                        'span1' => 'span1 (60px)',
                        'span2' => 'span2 (140px)',
                        'span3' => 'span3 (220px)',
                        'span4' => 'span4 (300px)',
                        'span5' => 'span5 (380px)',
                        'span6' => 'span6 (460px)',
                        'span7' => 'span7 (540px)',
                        'span8' => 'span8 (620px)',
                        'span9' => 'span9 (700px)',
                        'span10' => 'span10 (780px)',
                        'span11' => 'span11 (860px)',
                        'span12' => 'span12 (940px)',
                    )
                )
            );     
        }
    }

The editor form
~~~~~~~~~~~~~~~

Add the **_thumbnail_editor.html.twig** file inside the **Resources/views/Editor**, 
open it and paste the following code:

.. code-block:: jinja

    <form id="al_item_form">
        <table>
            {% include "AlphaLemonCmsBundle:Item:_form_renderer.html.twig" %}
            <tr>
                <td colspan="2" style="text-align: right">
                    <a class="al_editor_save btn btn-primary" href="#" >Save</a>
                </td>
            </tr>
        </table>
    </form>

The service
~~~~~~~~~~~
    
Open the **app_block.xml** and add the App-Block class as a service:


.. code-block:: xml

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Resources/config/app_block.xml
    <parameters>
        [...]
        <parameter key="bootstrap_thumbnail_tutorial.block.class">AlphaLemon\Block\BootstrapButtonTutorialBlockBundle\Core\Block\AlBlockManagerBootstrapThumbnailTutorialBlock</parameter>        
    </parameters>
    
    <services>
        [...]
        <service id="bootstrap_thumbnail_tutorial.block" class="%bootstrap_thumbnail_demo.block.class%">
            <tag name="alphalemon_cms.blocks_factory.block" description="Thumbnail Tutorial" type="BootstrapThumbnailTutorialBlock" group="bootstrap,Twitter Bootstrap" />
            <argument type="service" id="service_container" />
        </service>
    </services>
    
Then add the form as service:

.. code-block:: xml

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Resources/config/app_block.xml
    <parameters>
        [...]
        <parameter key="bootstrap_thumbnail_demo.form.class">AlphaLemon\Block\BootstrapButtonTutorialBlockBundle\Core\Form\AlThumbnailType</parameter>
    </parameters>
    
    <services>
        [...]
        <service id="bootstrap_thumbnail.form" class="%bootstrap_thumbnail.form.class%">
        </service>
    </services>
    
Make a block not visible in the adder menu
------------------------------------------

Making a block not visible in the adder menu is simple as add the **getIsInternalBlock** 
method to block manager:

.. code-block:: php

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Core/Block/AlBlockManagerBootstrapThumbnailsTutorialBlock.php
    class AlBlockManagerBootstrapThumbnailsTutorialBlock extends AlBlockManagerJsonBlockContainer
    {
        [...]
        
        public function getIsInternalBlock()
        {
            return true;
        }
    }

By default this method returns **false**, so just override the base method and return 
**true** to achieve this task.


Implement a list of App-Blocks
------------------------------

If you carefully read the thumbnail's template code, you surely notice that this block
cannot live on its own. In fact the output html code results in a **li** element which 
is just an item of a list of items. This is the reason because it has been declared as 
**internal block**

The ThumbnailsTutorial App-Block class
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Let's now implement the **ThumbnailsTutorial** block. As usual we start to add the block's
class, so create the **AlBlockManagerBootstrapThumbnailsDemoBlock.php** inside the
**Core/Block** folder, open it and paste this code:

.. code-block:: php

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Core/Block/AlBlockManagerBootstrapThumbnailsDemoBlock.php
    namespace AlphaLemon\Block\BootstrapThumbnailBlockBundle\Core\Block;

    use AlphaLemon\AlphaLemonCmsBundle\Core\Content\Block\JsonBlock\AlBlockManagerJsonBlock;
    use AlphaLemon\AlphaLemonCmsBundle\Core\Content\Block\AlBlockManagerContainer;

    class AlBlockManagerBootstrapThumbnailsTutorialBlock extends AlBlockManagerContainer
    {
        public function getDefaultValue()
        {        
            $value = '
                {
                    "0" : {
                        "type": "BootstrapThumbnailTutorialBlock"
                    },
                    "1" : {
                        "type": "BootstrapThumbnailTutorialBlock"
                    }
                }';
            
            return array('Content' => $value);
        }

        protected function renderHtml()
        {
            $items = AlBlockManagerJsonBlock::decodeJsonContent($this->alBlock->getContent());
            
            return array('RenderView' => array(
                'view' => 'BootstrapThumbnailBlockBundle:Thumbnail:thumbnails.html.twig',
                'options' => array('values' => $items),
            ));
        }
    }
    
Please, give a look to the **getDefaultValue** method to notice that this block will 
manage a list of **BootstrapThumbnailTutorialBlock** objects.

The template
~~~~~~~~~~~~

Create the **thumbnails.html.twig** inside the **BootstrapButtonTutorialBlockBundle/Core/Resources/views/Thumbnail** folder, 
open it and paste this code:

.. code-block:: jinja

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Resources/views/Thumbnail/thumbnails.html.twig
    {% extends "AlphaLemonCmsBundle:Editor:base_editor.html.twig" %}

    {% block body %}
    <ul class="thumbnails al-thumbnail-list" {{ editor|raw }}>
        {% if values|length > 0 %}
        {% for key, thumbnail in values %}
        {% set baseSlotName = block_manager.get.getId ~ "-" ~ key %} 
        
        {% set attributes = 'data-hide-blocks-menu=true data-item=' ~ key ~ ' data-slot-name=' ~ baseSlotName %}
        {{ renderIncludedBlock(baseSlotName, block_manager, thumbnail.type, true, "", attributes) }}    
        {% endfor %}
        {% else %}
        <li class="al-empty">Any thumbnail added</li>
        {% endif %}
    </ul>
    {% endblock %}

This template includes other blocks, in this case **BootstrapThumbnailTutorialBlock** objects,
so we have defined a slot name based on the block's id and from the item's array key:

.. code-block:: jinja

    {% set baseSlotName = block_manager.get.getId ~ "-" ~ key %} 
    
To define a list we must add two attributes to the element's editor tag, which are
    
    - data-item
    - data-slot-name
    
The **data-item** is simple the item's key and it is used to remove the item from the list,
the **data-slot-name** is the slot name where the item lives: here 's the definition:

.. code-block:: jinja

    {% set attributes = 'data-hide-blocks-menu=true data-item=' ~ key ~ ' data-slot-name=' ~ baseSlotName %}
    
This attributes string is passed as last argument of the **renderIncludedBlock** function
and it is written in the tag where the editor's attributes are rendered.

You may notice that there is declared another annotation, the **data-hide-blocks-menu**
which tells AlphaLemon to skip to render the blocks menu for the current block,
this because we don't need a tool to add and remove blocks, because a list adds its 
own buttons to achieve this task.

The **renderIncludedBlock** has been defined as follows:

.. code-block:: jinja

    {{ renderIncludedBlock(baseSlotName, block_manager, thumbnail.type, true, "", attributes) }}

so it gets the **attributes** just defined, as last argument.

The service
~~~~~~~~~~~
    
Open the **app_block.xml** and add the App-Block class as a service:

.. code-block:: xml

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Resources/config/app_block.xml
    <parameters>
        [...]
        <parameter key="bootstrap_thumbnails_tutorial.block.class">AlphaLemon\Block\BootstrapButtonTutorialBlockBundle\Core\Block\AlBlockManagerBootstrapThumbnailsTutorialBlock</parameter>        
    </parameters>
    
    <services>
        [...]
        <service id="bootstrap_thumbnails_tutorial.block" class="%bootstrap_thumbnails_tutorial.block.class%">
            <tag name="alphalemon_cms.blocks_factory.block" description="Thumbnails Tutorial" type="BootstrapThumbnailsTutorialBlock" group="bootstrap,Twitter Bootstrap" />
            <argument type="service" id="service_container" />
        </service>
    </services>


The javascript
~~~~~~~~~~~~~~

To manage a list of elements we need to add a javascript to manage this kind of block.
AlphaLemon CMS provides a jquery plugin which does all the job for you.

Add the **thumbnail_tutorial_editor.js** file inside the **Resources/public/js**
folder, open it and paste the following code:

.. code-block:: js

    $(document).ready(function() {
        $(document).on("blockEditing", function(event, element){
            if (element.attr('data-type') != 'BootstrapThumbnailsTutorialBlock') {
                return;
            }

            element.inlinelist('start', { addValue: '{"operation": "add", "value": { "type": "BootstrapThumbnailBlock" }}'     });
        });

        $(document).on("blockStopEditing", function(event, element){ 
            if (element.attr('data-type') != 'BootstrapThumbnailsTutorialBlock') {
                return;
            }

            element.inlinelist('stop');
        });
    });

Here we define two handlers, one that listens to the **blockEditing** event and another
one that listens to **blockStopEditing** event, which occours respectely when you
start and stop to edit a block.

In both of events we make sure that the our code is executed only when the block is
a **BootstrapThumbnailsTutorialBlock**.

To init an inline list, you just need to call the **inlinelist** method on the element
that represents the list itself.

This method could be extended defining the **addValue** parameter, giving a custom value to use
then a new item is added. When this parameter is nor defined, AlphaLemon CMS opens the
**adder menu** to let you choose the block you want to add.

In addiction you can define the **target** parameter. By default this block works with 
**li** tags, so just define this parameter to change this behavior. For example the 
**ButtonsGroup** block must work only with buttons so the implemented code is the following:

.. code-block:: js

    element.inlinelist('start', { 
      target: 'button',
      addValue: '{"operation": "add", "value": { "type": "BootstrapButtonBlock" }}'
    });

At last you may provide two callback functions: the **addItemCallback** and the 
**deleteItemCallback** which occours when a new item is added or an item is deleted.

To stop a list the code is quite trivial:

.. code-block:: js
    
    element.inlinelist('stop');
    
To enable the javascript just define the **bootstrapdropdownbuttontutorialblock.external_javascripts.cms**
parameter in the **app_block.xml**:

.. code-block:: xml

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Resources/config/app_block.xml
    <parameters>
        [...]
        <parameter key="bootstrapdropdownbuttontutorialblock.external_javascripts.cms" type="collection">
            <parameter>@BootstrapButtonTutorialBlockBundle/Resources/public/js/dropdown_menu_editor_tutorial.js</parameter>
        </parameter>
    </parameters>
    
.. note::

    If you are not use symlinks for your assets, you must run the 
    **./php app/console assets:install web --env=alcms [--symlink]** command.
        
Conclusion
----------

After reading this chapter you should be able to create a block that includes other 
blocks, add an editor for the main block, make a block not wisible in the adder menu,
create a block that manage a list of blocks and use the inlinelist plugin.

.. _`renderSlot`: add-a-custom-theme-to-alphalemon-cms#the-slots