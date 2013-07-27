Change the contents on a slot using a listener
==============================================

Sometimes you may need to display some data retrieved from the server side, for example
a table which values are fetched from a database, or you may need to display a form
and implement the logic to precess it.

Those tasks cannot be achieved using AlphaLemon CMS editor but you can implement a 
Symfony2 listener to do that job.


The rendering process
---------------------

When AlphaLemon CMS has processed the user request, just one second before returning 
the response, it dispatches a **BeforePageRenderingEvent** event. 

You can implement a listener that responds that event and will return an html output
which will used to change the content on the slot.

This event is dispatched three times:

1. **page_renderer.before_page_rendering**
2. **page_renderer.before_[current language]_rendering**
3. **page_renderer.before_[current page]_rendering**

A listener that responds to the **page_renderer.before_page_rendering** event is called 
for every page of the website, so the returned content will be used on the whole pages
of your website.

A listener that responds to the **page_renderer.before_[current language]_rendering** 
event is called for every page that belongs to a specific language, so the returned 
content will be used on the whole pages for that language.

A listener that responds to the **page_renderer.before_[current page]_rendering** 
event is called only for that page.

Events are called exaclty in the order proposed above, so a specific page listener 
might override a content rendered at language or at site level.


The listener
------------

To implement the listener you must create a class under your deploy bundle that will 
replace the slot contents:

.. code-block:: php

    // Acme/WebSiteBundle/EventListener/IndexRenderingListener.php
    namespace Acme\WebSiteBundle\EventListener\IndexRenderingListener

    use AlphaLemon\ThemeEngineBundle\Core\Rendering\EventListener\BasePageRenderingListener;

    class IndexRenderingListener extends BasePageRenderingListener
    {
    }

The listener extends the **BasePageRenderingListener**, which contains all the logic 
required to respond to the event and to manage the slot's content. 

Parent class requires the derived class implements the **renderSlotContents** method,
which must return an array of **AlSlotContent** objects:

.. code-block:: php

    // Acme/WebSiteBundle/Listener/IndexRenderingListener.php
    namespace Acme\WebSiteBundle\EventListener\IndexRenderingListener

    use AlphaLemon\ThemeEngineBundle\Core\Rendering\EventListener\BasePageRenderingListener;
    use AlphaLemon\ThemeEngineBundle\Core\Rendering\SlotContent\AlSlotContent;

    class IndexRenderingListener extends BasePageRenderingListener
    {
        protected function renderSlotContents()
        {
            $slotContent = new AlSlotContent();
            $slotContent
                ->setContent('My great replaced content')
                ->setSlotName('top_section_2')
                ->replace()
            ;

            return array($slotContent);
        }
    }

As you can see, an **AlSlotContent** object is instantiated, then both the content and the 
slot name are set and, at last, the replace method is called.

This function must return an array, then the base class will traverse it and performs 
the operation specified by the AlSlotContent, in this specific case the content contained 
into the **top_section_2** slot is replaced by the one defined in the AlSlotContent object.

Sometimes it could be useful to inject content into a slot instead of replacing the 
existing one: in this case just call the **inject()** method instead of the **replace()**.

.. code-block:: php

    $slotContent
        ->setContent("My great injected content")
        ->setSlotName('top_section_2')
        ->inject()
    ;


Configure the listener in the Dependency Container Injector
-----------------------------------------------------------

In order to be executed, this listener must be declared into Symfony2 Dependency Container 
Injector, **DIC** from now, as follows:

.. code-block:: xml

    Acme/WebSiteBundle/Resources/config/services.xml
    <parameters>
        <parameter key="acme_web_site.index_listener.class">Acme\WebSiteBundle\EventListeners\IndexRenderingListener</parameter>
    </parameters>

    <services>
        <service id="acme_web_site.index_listener" class="%acme_web_site.index_listener.class%">
            <tag name="alpha_lemon_theme_engine.event_listener" event="page_renderer.before_index_rendering" method="onPageRendering" priority="0" />
            <argument type="service" id="service_container" />
        </service>
    </services>

Under the hood, Symfony2 will translate this declaration and will create the **IndexRenderingListener**
in the container. Learn more about that [LINK]

The **tag** definition specifies the service purpose, in this case is to listen to
and event.

.. code-block:: xml

    <tag name="alpha_lemon_theme_engine.event_listener" event="page_renderer.before_index_rendering" method="onPageRendering" priority="0" />

The **name** option identifies the event to listen, and it must always be 
**alpha_lemon_theme_engine.event_listener** for listeners that must respond to the event
we are talking about. 

The **method** called is **onPageRendering**, which is defined in the **BasePageRenderingListener** 
class.

The event option is defined as **page_renderer.before_index_rendering**: 
this means that this listener will be called only for the index page, as explained 
in the **The rendering process** paragraph.

If you want to call this listenr for the whole site pages, you must replace the event option 
with **page_renderer.before_page_rendering**, while if you want to call this listener for 
a specific language, you must replace the event option with **page_renderer.before_en_rendering**.

In this case this listener will be called when the language is **en**.

.. note::

    When you declare a listener for a page, you must use the page name and not its permalink.

Add extra assets
----------------
Sometimes it could be required to add some external assets with the listener. This
can be achieved simply declaring those assets as parameters in the DIC:

.. code-block:: xml

    Acme/WebSiteBundle/Resources/config/services.xml
    <parameters>
        <parameter key="acme_web_site.index_listener.alphalemon-cms-demo.external_javascripts" type="collection">            
            <parameter>@AlphaLemonThemeEngineBundle/Resources/public/js/vendor/jquery/*</parameter>
            <parameter>@AlphaLemonWebsiteBundle/Resources/public/vendor/tw-bootstrap/modals/js/bootstrap.min.js</parameter>
        </parameter>
        <parameter key="acme_web_site.index_listener.alphalemon-cms-demo.external_stylesheets" type="collection">
            <parameter>@AlphaLemonWebsiteBundle/Resources/public/vendor/tw-bootstrap/modals/css/bootstrap.min.css</parameter>
        </parameter>
    </parameters>

This specific configuration adds the specified assets to the page where the listener
is called.

The following rule defines the parameter's key:

.. code-block:: text

    [listener name].[page name].[asset type]

in our example:

.. code-block:: text

    acme_web_site.index_listener.alphalemon-cms-demo.external_javascripts
    acme_web_site.index_listener.alphalemon-cms-demo.external_stylesheets

Assets are saved into the public folder of the **AlphaLemonWebsiteBundle** and fetched
usidn a relative path:

.. code-block:: text

    @AlphaLemonWebsiteBundle/Resources/public/vendor/tw-bootstrap/modals/css/bootstrap.min.css


This configuration adds the assets only for the declared page, **alphalemon-cms-demo** 
as well, but can add assets for the whole pages of a language or site.

So, to add assets for the whole site's page your parameter's key will be:

.. code-block:: text

    [listener name].page.[asset type]

and for language


.. code-block:: text

    [listener name].[language name].[asset type]


.. class:: fork-and-edit

Found a typo ? Something is wrong in this documentation ? `Just fork and edit it !`_

.. _`Just fork and edit it !`: https://github.com/alphalemon/alphalemon-docs
