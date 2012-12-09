Implement a json block type
===========================

When you need to add a block made by items and each items has two or more properties,
you might be tempted to add a new table to the database. This will work for sure but
a better solution could take place: using a json content to achieve that task.

AlphaLemon provides an abstract block manager that manages that special content and
an editor that can be used to add, edit and remove the items for that block.

.. note::

    This chapter implements a custom block, so you should refer the `add custom block`_
    chapter for more details on how implement a custom app-block before go over with this
    one.

A real example
--------------
As real example here is presented the **BusinessCarouselBundle**, an html content
that displays one or more comments from several people on something.


The desing
~~~~~~~~~~
Let's see how the BusinessCarouselBundle is implemented on the web page:

.. code-block:: html

    <div class="testimonials">
        <div id="testimonials">
          <ul>
            <li>
                <div>
                    Working with AlphaLemon Cms is amazing!
                </div>
                <span>
                    <strong class="color1">John Doe,</strong> <br />
                    Ceo
                </span>
            </li>
            <li>
                <div>
                    This web application is really cool!
                </div>
                <span>
                    <strong class="color1">Jane Doe,</strong> <br/>
                    Art director
                </span>
            </li>
          </ul>
        </div>
        <a href="#" class="up"></a>
        <a href="#" class="down"></a>
    </div>

The carousel is made as an unordered list and each item of that list contains a
comment text, the name, the surname and the role of the person who made the comment.
Under the list there are two links that control the items scroll.

The content
~~~~~~~~~~~

The **BusinessCarouselBundle** must manage a serie of items and each item has several
properties, so this kind of block is a perfect example to inherit from a **JsonBlock**.
The content will be saved as follows:

.. code-block:: text

    {
        "0" : {
            "name" : "John",
            "surname" : "Doe",
            "role" : "Ceo",
            "comment" : "Working with AlphaLemon Cms is amazing!"
        },
        "1" : {
            "name" : "Jane",
            "surname" : "Doe",
            "role" : "Art director",
            "comment" : "This web application is really cool!"
        }
    }

This content has two items, and each item has four properties: the name, surname,
role and comment.

The Block Manager
-----------------

The Block Manager object will inherit from the abstract class **AlBlockManagerJsonBlock**
instead the usual **AlBlockManager**:

.. code-block:: php

    // AlphaLemon/Block/BusinessCarouselBundle/Core/Block/AlBlockManagerBusinessCarousel.php
    namespace AlphaLemon\Block\BusinessCarouselBundle\Core\Block;

    use AlphaLemon\AlphaLemonCmsBundle\Core\Content\Block\JsonBlock\AlBlockManagerJsonBlock;

    class AlBlockManagerBusinessCarousel extends AlBlockManagerJsonBlock
    {
        public function getDefaultValue()
        {
            $value =
            '{
                "0" : {
                    "name" : "John",
                    "surname" : "Doe",
                    "role" : "Ceo",
                    "comment" : "This web application is really cool!"
                }
            }';

            return array(
                'Content' => $value,
                'InternalJavascript' => '$(".carousel").startCarousel();'
            );
        }
    }

The default value will add only an item, defined by the **Content** option and
the **InternalJavascript** manages the javascript that starts the carousel.

To have your block returning the desidered output, the **getHtml** method of parent
class has been redefined:

.. code-block:: php

    // AlphaLemon/Block/BusinessCarouselBundle/Core/Block/AlBlockManagerBusinessCarousel.php
    namespace AlphaLemon\Block\BusinessCarouselBundle\Core\Block;

    use AlphaLemon\AlphaLemonCmsBundle\Core\Content\Block\JsonBlock\AlBlockManagerJsonBlock;

    class AlBlockManagerBusinessCarousel extends AlBlockManagerJsonBlock
    {
        [...]
        public function getHtml()
        {
            if (null === $this->alBlock) {
                return "";
            }

            $items = $this->decodeJsonContent($this->alBlock->getContent(), true);

            return array(
                "RenderView" => array(
                    "view" => "BusinessCarouselBundle:Carousel:carousel.html.twig",
                    "options" => array(
                        "items" => $items,
                    )
                )
            );
        }
    }

The code is quite simple to understand and self explained in the code, however it
fetches the block content, decodes it into an array of items and returns an array
specifing the template to render.

This array of options must have the mandatory **RenderView** option key which must 
contain another array with two options: the mandatory **view** option and the optional
**options**.

Sometimes it might be necessary to render more views, it this case the array should
be formatted as follows:

.. code-block:: php

    return array(
        "RenderView" => array(
            "views" => array(
                    array(
                        "view" => "BusinessCarouselBundle:Carousel:carousel.html.twig",
                        "options" => array(
                            "items" => $items,
                    ),
                    array(
                        "view" => "BusinessCarouselBundle:Carousel:carousel_1.html.twig",
                    ),
                )
        )
    );

The template view
~~~~~~~~~~~~~~~~~
The template that renders the carousel must be added under the **Resources/views/Carousel**
folder and it must contain the following code:

.. code-block:: jinja

    <div class="carousel_container">
        <div class="carousel">
            {% if items|length > 0 %}
            <ul>
                {% for item in items %}
                <li>
                    <div>{{ item.comment }}</div>
                    <span>
                        <strong class="color1">{{ item.name }} {{ item.surname }},</strong>
                        <br />
                        {{ item.role }}
                    </span>
                </li>
                {% endfor %}
            </ul>
            {% else %}
            <p>Any item has been added</p>
            {% endif %}
        </div>
        <a href="#" class="up"></a>
        <a href="#" class="down"></a>
    </div>


The editor
----------

AlphaLemon CMS provides a base editor to mananage that kind of content, which is
made by two twig templates, called **list** and **item**.

The names are quite esplicative, however the **list** displays the content items
as a list and the **item** represent a single item and can be used to add and edit
an item.

So, to manage your json block, you just need to add two new templates which extends
those base twig templates.

The BusinessCarousel list editor
--------------------------------

A new twig template called **businesscarousel_list.html.twig** has been created
under the **Resources/views/Block** bundle's folder, then the following code
has been added:

.. code-block:: jinja

    // AlphaLemon/Block/BusinessCarouselBundle/Resources/views/Block/businesscarousel_list.html.twig
    {% extends "AlphaLemonCmsBundle:Block:Json/list.html.twig" %}

The name is mandatory and must be composed as follows: **[bundle name in lower case]_list.html.twig**

Display the editor
------------------

AlphaLemon CMS looks for a **[app_block_name_in_lower_case]_editor.html.twig** as
default template when a content must be edited but, in this case, we need to open a
different editor instead of the default one.

This task is achieved implementing a listener that listen to the **actions.block_editor_rendering**
event.

AlphaLemon provides two pre-configured listeners: the **RenderingListEditorListener** to render the editor which displays the items list and the
**RenderingItemEditorListener** to render the editor which displays the single item.

This block requires to display the items as a list so the following class has been added:

.. code-block:: php

    //AlphaLemon/Block/BusinessCarouselBundle/Core/Listener/RenderingEditorListener.php
    namespace AlphaLemon\Block\BusinessCarouselBundle\Core\Listener;

    use AlphaLemon\AlphaLemonCmsBundle\Core\Listener\JsonBlock\RenderingListEditorListener;

    class RenderingEditorListener extends RenderingListEditorListener
    {
        protected function configure()
        {
            return array('blockClass' => '\AlphaLemon\Block\BusinessCarouselBundle\Core\Block\AlBlockManagerBusinessCarousel');
        }
    }

The listener implements the parent's abstract method **configure** which must return
an array of options. In this case the only option needed is the **blockClass** that
must return the path of the class to use to render the list.

Render the editor for the item as base editor
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

When your block doesn't manager a list of items but only a single item, you need
to render the **RenderingItemEditorListener**. This listener requires you to define
the **formClass** option:

.. code-block:: php

    namespace AlphaLemon\Block\BusinessDropCapBundle\Core\Listener;

    use AlphaLemon\AlphaLemonCmsBundle\Core\Listener\JsonBlock\RenderingItemEditorListener;

    class RenderingEditorListener extends RenderingItemEditorListener
    {
        protected function configure()
        {
            return array(
                'blockClass' => '\AlphaLemon\Block\BusinessDropCapBundle\Core\Block\AlBlockManagerBusinessDropCap',
                'formClass' => '\AlphaLemon\Block\BusinessDropCapBundle\Core\Form\BusinessDropCapType',
            );
        }
    }

.. note:

    This class does not belongs the BusinessCarouselBundle and it is given just as example.

Adding the listener to the Dependency Injector Container
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To have the listener working, it must be declared in the DIC:

.. code-block:: xml

    // AlphaLemon/Block/BusinessCarouselBundle/Resources/config/services.xml
    <parameter key="businesscarouseleditor_rendering.class">AlphaLemon\Block\BusinessCarouselBundle\Core\Listener\RenderingEditorListener</parameter>

    <services>
        <service id="businesscarouseleditor_rendering.class" class="%businesscarouseleditor_rendering.class%">
            <tag name="alcms.event_listener" event="actions.block_editor_rendering" method="onBlockEditorRendering" priority="0" />
        </service>
    </services>

The form to edit an item
------------------------

The last step to have a JsonBlock working is to declare a form to manage a single item.

AlphaLemon CMS provides a pre-configured form that implements the common required
functionalities, so we just extend that class as follows:

.. code-block:: php

    // AlphaLemon/Block/BusinessCarouselBundle/Core/Form/BusinessCarouselType.php
    namespace AlphaLemon\Block\BusinessCarouselBundle\Core\Form;

    use AlphaLemon\AlphaLemonCmsBundle\Core\Form\JsonBlock\JsonBlockType;
    use Symfony\Component\Form\FormBuilderInterface;

    class BusinessCarouselType extends JsonBlockType
    {
        public function buildForm(FormBuilderInterface $builder, array $options)
        {
            parent::buildForm($builder, $options);

            $builder->add('name');
            $builder->add('surname');
            $builder->add('role');
            $builder->add('comment', 'textarea');
        }
    }

As you see, the only required thing is to instantiate the form's fields extending
the **buildForm** method.

.. note:

    Don't forget to call the parent's method, because it defines a required field for you.

The item's editor
~~~~~~~~~~~~~~~~~

As we did for the list editor, we have to implement an editor for the item, so a new twig template called **businesscarousel_item.html.twig**
has been created under the **views/Block** bundle's folder, then the following code has been added:

.. code-block:: jinja

    // AlphaLemon/Block/BusinessCarouselBundle/Resources/views/Block/businesscarousel_item.html.twig
    {% extends "AlphaLemonCmsBundle:Block:Json/item_and_list.html.twig" %}

This editor extends the base **item.html.twig** and adds a button to return back to list editor.

.. _`add custom block`: add-a-new-content-to-alphalemon-cms