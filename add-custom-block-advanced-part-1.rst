Create the Dropdown Menu App-Block
==================================

This chapter explains how to create the dropdown menu App-Block. In this chapter you 
will learn:

    1. How to add an App-Block to an existing bundle
    2. How to add a controller and a route to handle the implemented actions
    3. How to add an App-Block's extra asset to AlphaLemon CMS 
    4. How to override the default action to save the block's content

.. note::

    This chapter requires as pre-requisite the reading of the `Add a new App-Block`_
    chapter
    
Add an App-Block to an existing bundle
--------------------------------------

This Block inherits from the Button block created in the previous chapter, so we don't
add a new bundle to manage the block, but this App-Block is added to the BootstrapButtonTutorialBlockBundle.

The object
~~~~~~~~~~

Add the **AlBlockManagerBootstrapDropdownButtonTutorialBlock.php** file inside the 
**BootstrapButtonTutorialBlockBundle/Core/block**, open it and add the following code:

.. code-block:: php   

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Core/Block/AlBlockManagerBootstrapDropdownButtonTutorialBlock.php  
    namespace AlphaLemon\Block\BootstrapButtonTutorialBlockBundle\Core\Block;

    use AlphaLemon\AlphaLemonCmsBundle\Core\Content\Block\AlBlockManagerContainer;
    use AlphaLemon\AlphaLemonCmsBundle\Core\Content\Block\JsonBlock\AlBlockManagerJsonBlock;

    /**
     * Description of AlBlockManagerBootstrapButtonBlock
     */
    class AlBlockManagerBootstrapDropdownButtonTutorialBlock extends AlBlockManagerContainer
    {
        public function getDefaultValue()
        {
            $value = '
                    {
                        "0": {
                            "button_text": "Dropdown Button 1",
                            "button_type": "",
                            "button_attribute": "",
                            "button_gropup" : "none",
                            "items": [
                                {
                                    "data" : "Item 1", 
                                    "metadata" : {  
                                        "type": "link",
                                        "href": "#",
                                        "attributes": {}
                                    }
                                },
                                { 
                                    "data" : "Item 2", 
                                    "metadata" : {  
                                        "type": "link",
                                        "href": "#",
                                        "attributes": {}
                                    }
                                },
                                { 
                                    "data" : "Item 3", 
                                    "metadata" : {  
                                        "type": "link",
                                        "href": "#",
                                        "attributes": {}
                                    }
                                }
                            ]
                        }
                    }
                ';
            
            return array('Content' => $value);
        }
    }
    
This block has some attributes similar to the Button ones, then it manages a list 
of links, which are grouped under the **items** key.

To render the content we must override the default **renderHtml** method as follows:

 .. code-block:: php   
 
    protected function renderHtml()
    {
        $items = AlBlockManagerJsonBlock::decodeJsonContent($this->alBlock->getContent());
        
        return array('RenderView' => array(
            'view' => 'BootstrapButtonTutorialBlockBundle:Button:dropdown_button.html.twig',
            'options' => array('data' => $items[0]),
        ));
    }
    
We must define the parameters passed to the block's editor:
    
 .. code-block:: php   
    
    public function editorParameters()
    {
        $items = AlBlockManagerJsonBlock::decodeJsonContent($this->alBlock->getContent());
        $item = $items[0];
        $attributes = $item["items"];  
        unset($item["items"]); // The form does not require this information
        
        $formClass = $this->container->get('bootstrapbuttonblock.form');
        $buttonForm = $this->container->get('form.factory')->create($formClass, $item);
        
        return array(
            "template" => 'BootstrapButtonTutorialBlockBundle:Editor:_dropdown_editor.html.twig',
            "title" => "Dropdown button editor",
            "form" => $buttonForm->createView(),
            'attributes' => $attributes,  
        );
    }


The service
~~~~~~~~~~~

Open the **app_block.xml** and add the App-Block class as a service:

 .. code-block:: xml

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Resources/config/app_block.xml
    <parameters>
        [...]
        <parameter key="bootstrap_dropdown_button_tutorial_block.block.class">AlphaLemon\Block\BootstrapButtonTutorialBlockBundle\Core\Block\AlBlockManagerBootstrapDropdownButtonTutorialBlock</parameter>
    </parameters>

    <services>    
        [...]    
        <service id="bootstrap_dropdown_button_tutorial_block.block" class="%bootstrap_dropdown_button_tutorial_block.block.class%">
            <tag name="alphalemon_cms.blocks_factory.block" description="Dropdown Tutorial" type="BootstrapDropdownButtonTutorialBlock" group="bootstrap,Twitter Bootstrap" />
            <argument type="service" id="service_container" />
        </service>
    </services>
    
The content template
~~~~~~~~~~~~~~~~~~~~

Some attributes used by this new block are the same used by the Button block, so a
small refactor is needed.

Open the **button.html.twig** template and change it as follows:

.. code-block:: jinja

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Resources/views/Button/button.html.twig
    {% extends "BootstrapButtonTutorialBlockBundle:Button:_button_params.html.twig" %}

    {% block body %}
    <button class="btn{{ button_type }}{{ button_attribute }}{{ button_tutorial_block }}{{ button_enabled }}" {{ editor|raw }}>{{ button_text }}</button>
    {% endblock %}

The template now extends the **BootstrapButtonTutorialBlockBundle:Button:_button_params.html.twig**
which does not exist yet, so add this new file inside the **views/Button**, open it and 
paste the following code:

.. code-block:: jinja

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Resources/views/Button/_button_params.html.twig
    {% extends "AlphaLemonCmsBundle:Editor:base_editor.html.twig" %}

    {% set button_type = (data.button_type is defined and data.button_type) ? " " ~ data.button_type : "" %}
    {% set button_attribute = (data.button_type is defined and data.button_type) ? " " ~ data.button_attribute : "" %}
    {% set button_text = (data.button_text is defined and data.button_text) ? " " ~ data.button_text : "Click me" %}
    {% set button_tutorial_block = (data.button_tutorial_block is defined and data.button_tutorial_block) ? " " ~ data.button_tutorial_block : "" %}
    {% set button_enabled = (data.button_enabled is defined and data.button_enabled) ? " " ~ data.button_enabled : "" %}


This block has the button_gropup attribute which not belongs the Button block, so we will manage this
parameter in a separate template. 

Add the **dropdown_button_params.html.twig** template under the **views/Button** folder, 
open it and paste the following code inside:

.. code-block:: jinja

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Resources/views/Button/dropdown_button.html.twig
    {% extends "BootstrapButtonTutorialBlockBundle:Button:_button_params_params.html.twig" %}

    {% set button_dropup = "" %}
    {% set button_dropup_right = "" %}
    {% if data.button_dropup is defined and data.button_dropup != "none" %}
        {% set button_dropup = " dropup" %}
        {% if data.button_dropup == "right" %}
            {% set button_dropup_right = " pull-right" %}
        {% endif %}
    {% endif %}


Add the **dropdown_button.html.twig** template under the **views/Button** folder, 
open it and paste the following code inside:

.. code-block:: jinja

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Resources/views/Button/dropdown_button.html.twig
    {% extends "BootstrapButtonTutorialBlockBundle:Button:dropdown_button.html.twig" %}

    {% block body %}
    <div class="btn-group{{ button_dropup }}" {{ editor|raw }}>
        <a class="btn dropdown-toggle{{ button_type }}{{ button_attribute }}" data-toggle="dropdown" href="#">
            {{ button_text }}
            <span class="caret"></span>
        </a>
        <ul class="dropdown-menu{{ button_dropup_right }}">
            {% include "BootstrapButtonTutorialBlockBundle:Item:_dropdown_items.html.twig" with {'items': data.items} %}
        </ul>
    </div>
    {% endblock %}

This template includes the **BootstrapButtonTutorialBlockBundle:Item:_dropdown_items.html.twig**
view, which is responsible to render the dropdown items.

Add the **_dropdown_items.html.twig** template under the **views/Item** folder, 
open it and paste the following code inside:

.. code-block:: jinja

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Resources/views/Item/_dropdown_items.html.twig
    {% for item in items %}
        {% if item.children is defined and item.children|length > 0 %}
        <li class="dropdown-submenu">
            <a tabindex="-1" href="#">{{ item.data }}</a>
            <ul class="dropdown-menu">
                {% include "BootstrapButtonBlockBundle:Item:_dropdown_items.html.twig" with {'items': item.children} %}
            </ul>
        </li>
        {% else %}   
            {% if item.metadata.type is defined and item.metadata.type == 'divider' %}
            <li class="divider"></li>
            {% else %}
                {% if item.metadata.type is defined %}
                <li><a href="{{ item.metadata.href }}">{{ item.data }}</a></li>
                {% else %}
                <li><a href="#">{{ item.data }}</a></li>
                {% endif %} 
            {% endif %} 
        {% endif %}
    {% endfor %}
    
The last template to implement is the one deputated to render the editor. Add the
**_dropdown_editor.html.twig** template under the **views/Editor** folder, open it and
paste the following code inside:

.. code-block:: jinja

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Resources/views/Editor/_dropdown_editor.html.twig
    <div class="pull-left">
        <form id="al_item_form">
            <table>
                {% include "AlphaLemonCmsBundle:Item:_form_renderer.html.twig" %}
                <tr>
                    <td colspan="2" style="text-align: right">
                        <a class="al-editor-items btn" href="#" >Menu items</a>
                        <a class="al_editor_save btn btn-primary" href="#" >Save</a>
                    </td>
                </tr>
            </table>        
        </form>
    </div>
    <div id="al-dropdown-menu-items" class="pull-left" style="display:none;"></div>
    <div class="clearfix"></div>
    
This template is similar to the one that handles the button's editor but in addiction
adds the **Menu items** button and an empty div where the interface to manage the 
dropdown button's items will be rendered.
    
Render the items' editor
~~~~~~~~~~~~~~~~~~~~~~~~

The dropdown items' editor is rendered using an ajax transaction, so we need to add a
controller to process this request, then we inject the request's response into the
**al-dropdown-menu-items** element.

.. note::

    Items are displayed by the Jstree jquery plugin which is implemented by the JstreeBundle

Add the **JstreeDropdownButtonController.php** file inside the **BootstrapButtonTutorialBlockBundle/Controller**,
open it and paste the following code:

.. code-block:: php

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Core/Controller/JstreeDropdownButtonController.php
    namespace AlphaLemon\Block\BootstrapButtonTutorialBlockBundle\Controller;

    use Symfony\Bundle\FrameworkBundle\Controller\Controller;
    use AlphaLemon\AlphaLemonCmsBundle\Core\Form\ModelChoiceValues\ChoiceValues;
    use AlphaLemon\AlphaLemonCmsBundle\Core\Content\Block\JsonBlock\AlBlockManagerJsonBlock;

    class JstreeDropdownButtonController extends Controller
    {
        public function showAction()
        {
            $request = $this->container->get('request');
            
            // Fetches the block
            $factoryRepository = $this->container->get('alpha_lemon_cms.factory_repository');
            $blocksRepository = $factoryRepository->createRepository('Block');
            $block = $blocksRepository->fromPk($request->get('idBlock'));
            
            // Retrieves the dropbutton items
            $items = AlBlockManagerJsonBlock::decodeJsonContent($block->getContent());
            $item = $items[0];
            $attributes = $item["items"]; 
            
            $seoRepository = $factoryRepository->createRepository('Seo');
            
            // Prepares the options to pass to the template
            $options = array(               
                'attributes' => $attributes,                 
                'jstree_nodes' => json_encode($attributes), 
                'attributes_form' => 'BootstrapButtonTutorialBlockBundle:Jstree:_jstree_attribute.html.twig',                
                'pages' => ChoiceValues::getPermalinks($seoRepository, $request->get('languageId')),
            );
            
            return $this->container->get('templating')->renderResponse('JstreeBundle:Jstree:_jstree.html.twig', $options);
        }
    }
    
The controller simply renders the **JstreeBundle:Jstree:_jstree.html.twig** template

Add the route
~~~~~~~~~~~~~

We need to define a route to display the controller **showAction**. Add the **dropdown_button.xml**
inside the **Resources/config/routing** folder, open it an paste the following code:

.. code-block:: xml

    <?xml version="1.0" encoding="UTF-8" ?>

    <routes xmlns="http://symfony.com/schema/routing"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://symfony.com/schema/routing http://symfony.com/schema/routing/routing-1.0.xsd">

        <route id="_show_menu_editor" pattern="/backend/{_locale}/al_show_jstree">
            <default key="_controller">BootstrapButtonTutorialBlockBundle:JstreeDropdownButton:show</default>
            <default key="_locale">en</default>
            <requirement key="_method">POST</requirement>
        </route>
    </routes>

Add the **routing.yml**
file inside the **Resources/config** and add the following code:

.. code-block:: text

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Resources/config/routing.yml    
    jstree_dropdown_button:
        resource: "@BootstrapButtonTutorialBlockBundle/Resources/config/routing/dropdown_button.xml"

.. note::

    Don't worry about adding the route to main application routing file, the **AlphaLemonBootstrapBundle**
    does this task for you.
    
Execute the ajax transaction
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To execute and render the action we must implement an ajax transaction.

AlphaLemon CMS triggers several events at client side. One of them is raised when
the popover editor is opened, so we will handle that event to execute the ajax transaction.

Add the file **dropdown_menu_editor_tutorial.js** inside the **Resources/public/js** folder and
add the following code:

.. code-block:: js

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Resources/public/js/dropdown_menu_editor_tutorial.js
    $(document).ready(function() {
        $(document).on("popoverShow", function(event, element){
            
        });
    }); 
    
Before adding the ajax transaction code we must be sure that this code is executed
only for the **BootstrapDropdownButtonTutorialBlock**, so change that code as follows:

.. code-block:: js
    
    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Resources/public/js/dropdown_menu_editor_tutorial.js
    $(document).ready(function() {
        $(document).on("popoverShow", function(event, element){
            blockType = element.attr('data-type');
            if (blockType != 'BootstrapDropdownButtonTutorialBlock') {
                return;
            }
        });
    }); 
    
The **popoverShow** event passes as second argument the block we are editing. This means
we can access all the editor's attributes. Here we need to check the **data-type** attribute 
to be sure that the code is executed only for our block.

Now, under the the code just added, paste the ajax transaction code:

.. code-block:: js

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Resources/public/js/dropdown_menu_editor_tutorial.js
    $(".al-editor-items").on('click', function(){
       if ( ! $('#al-dropdown-menu-items').is(":visible") && $('#al-dropdown-menu-items').html().trim() == "" ) {
            $.ajax({
                  type: 'POST',
                  url: frontController + 'backend/' + $('#al_available_languages option:selected').val() + '/al_show_jstree',
                  data: {
                      'page' :  $('#al_pages_navigator').html(),
                      'language' : $('#al_languages_navigator').html(),  
                      'pageId' :  $('#al_pages_navigator').attr('rel'),
                      'languageId' : $('#al_languages_navigator').attr('rel'),                  
                      'idBlock' : element.attr('data-block-id')
                  },
                  beforeSend: function()
                  {
                      $('body').AddAjaxLoader();
                  },
                  success: function(html)
                  {
                      $('#al-dropdown-menu-items').html(html);
                  },
                  error: function(err)
                  {
                      $('body').showDialog(err.responseText);
                  },
                  complete: function()
                  {
                      $('body').RemoveAjaxLoader();
                  }
            });
        }

        $("#al-dropdown-menu-items").toggle();

        return false;
    });
    
This controls assures to execute the ajax transaction only the first time the editor is
opened:

.. code-block:: js

    if ( ! $('#al-dropdown-menu-items').is(":visible") && $('#al-dropdown-menu-items').html().trim() == "" )


Add an external asset
~~~~~~~~~~~~~~~~~~~~~

We must load this javascript file adding the asset to the website. This setting is 
quite simple to accomplish: just add some parameters to the configuration file.

Add the following code to **Resources/config/app_block.xml** file:

.. code-block:: xml

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Resources/config/app_block.xml
    <parameters>
        
        [...]
                
        <parameter key="bootstrapdropdownbuttontutorialblock.external_javascripts.cms" type="collection">
            <parameter>@BootstrapButtonTutorialBlockBundle/Resources/public/js/dropdown_menu_editor_tutorial.js</parameter>
        </parameter>
    </parameters>
    
So, to add an external asset from your App-Block, you must add a new parameter to the
Dependency Injector Container.

The rule to add an external file is:

.. code-block:: text    

    [block name in lower case].external_stylesheets
    [block name in lower case].external_javascripts

If you need to add one or more assets only when the editor is active, you must suffix 
the parameter name with **.cms**, as we did for this parameter.

.. note::

    Don't forget to run **php app/console assets:install web --env=alcms** to have your
    assets published under the Document Root of your website.


Save the block's content
~~~~~~~~~~~~~~~~~~~~~~~~
AlphaLemon CMS automatically add an handler that intecepts the **al_editor_save** element's
click event.

We are talking about the button added to the Block's editor which simply serializes
the form and call the method to save the Block's content.

This block has more than the form's values to save, so the default method must be overrided.
Open the **dropdown_menu_editor_tutorial.js** and add the following code after the code that handles 
the ajax transaction:

.. code-block:: js

    // src/AlphaLemon/Block/BootstrapButtonTutorialBlockBundle/Resources/public/js/dropdown_menu_editor_tutorial.js
    $('.al_editor_save').unbind().on('click', function()
    {
        var value = $('#al_item_form').serialize();
        if ($("#jstree").length > 0) {
            value += '&items=' + JSON.stringify($("#jstree").jstree("get_json", $("#jstree").jstree("select_node", -1)))
        }
        $('#al_item_form').EditBlock('Content', value);

        return false;
    });
    
This code simply serializes the form then slugifies the jstree nodes, then passes this 
value to **EditBlock** methos that saves the content.

To handle this change we need to override the **AlBlockManager's edit** method, which 
is deputated to save the block's content.

Add this code at the end of the **AlBlockManagerBootstrapDropdownButtonTutorialBlock**:

.. code-block:: php   
        
    protected function edit(array $values)
    {
        if (array_key_exists('Content', $values)) {
            $unserializedData = array();
            $serializedData = $values['Content'];
            parse_str($serializedData, $unserializedData);

            // re-encodes the jstree items
            $v = $unserializedData["al_json_block"];                
            if (array_key_exists("items", $unserializedData)) {
                unset($unserializedData["al_json_block"]);            
                $menuItems = json_decode($unserializedData["items"], true);
                $v += array('items' => $menuItems[0]["children"]); // Excludes the root node "Menu"
            } else {
                $items = AlBlockManagerJsonBlock::decodeJsonContent($this->alBlock->getContent());
                $v += array('items' => $items[0]["items"]);
            }

            $values['Content'] = json_encode(array($v));
        }

        return parent::edit($values); 
    }
    
Here the content returned by the jquery method is manipulated and then processed by the 
base method.
  
Use your App-Block
------------------

To use your new App-Block, just add it to your website!
  
Conclusion
----------

After reading this chapter you should be able to add an App-Block to an existing bundle,
add a controller and a route to handle the implemented actions, add an App-Block's extra 
asset to AlphaLemon CMS, override the default action to save the block's content

.. _`Add a new App-Block`: http://www.alphalemon.com/add-a-new-block-app-to-alphalemon-cms