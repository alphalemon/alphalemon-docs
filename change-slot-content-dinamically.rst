Change the contents of a slot dynamically
=========================================

AlphaLemon CMS allows you to add the website contents directly onto the web page you are editing in the backend. This content is written as
twig templates when they are deployed to the production environment.

Sometimes you may need to add content that displays some data retrieved by a database, or a contact form that must display the form itself to let the
website visitor add the required information. This would then process the data in order to display a proper message based on the form process result.

Those tasks cannot be achieved simply be using the AlphaLemon CMS editor, so you would need something to add dynamic data to your page. This task can
be achieved by implementing a listener. Let's see how.


The rendering process
---------------------

When AlphaLemon CMS has processed the user request, and it's ready to return the response, before giving back that response it dispatches a
**BeforePageRenderingEvent** event. Using this method you just need to implement a listener that listens to that event to change content on a slot.

This event is actually dispatched three times, following the schema below:

1. **page_renderer.before_page_rendering**
2. **page_renderer.before_[current language]_rendering**
3. **page_renderer.before_[current page]_rendering**

This architecture gives you a great set of possibilities to manage your contents exactly when you need. In fact a listener responding to the
**page_renderer.before_page_rendering** event is executed for every page of the website. Responding to the
**page_renderer.before_[current language]_rendering** event means that the listener is executed for every page that belongs to a specific language
and, at last responds to the **page_renderer.before_[current page]_rendering** event, meaning that the listener is executed only for that specific page.

Events are called in the order proposed so a specific page listener might override content rendered at language or at site level.


The listener
------------

Implementing the listener is quite simple. First of all you must create the class that executes the replacement of contents,
so under your deploy bundle simply add the following class:

.. code-block:: php

    // Acme/WebSiteBundle/Listener/IndexRenderingListener.php
    namespace Acme\WebSiteBundle\Listener\IndexRenderingListener

    use AlphaLemon\ThemeEngineBundle\Core\Rendering\Listener\BasePageRenderingListener;

    class IndexRenderingListener extends BasePageRenderingListener
    {
    }

As you may notice the class extends the **BasePageRenderingListener** which contains all the logic required to respond to the event and to
change the slot's content. The only thing you have to do is to specify which slots to edit, and the content to add or replace.

This task is achieved implementing the required protected function **renderSlotContents**, so change your class as follows:

.. code-block:: php

    // Acme/WebSiteBundle/Listener/IndexRenderingListener.php
    namespace Acme\WebSiteBundle\Listener\IndexRenderingListener

    use AlphaLemon\ThemeEngineBundle\Core\Rendering\Listener\BasePageRenderingListener;
    use AlphaLemon\ThemeEngineBundle\Core\Rendering\SlotContent\AlSlotContent;

    class IndexRenderingListener extends BasePageRenderingListener
    {
        protected function renderSlotContents()
        {
            $slotContent = new AlSlotContent();
            $slotContent->setContent("My great replaced content")
                        ->setSlotName('top_section_2')
                        ->replace();

            return array($slotContent);
        }
    }

In the example above an AlSlotContent is instantiated, then both the content and the slot name are set and, at last, the replace method is called.
The slot is transformed into an array and returned to the base class which will replace the content contained into the **top_section_2** slot
with the one defined in the AlSlotContent object.

Sometimes it could be useful to inject content into a slot instead of replacing the existing one; in this case just call the **inject()** method instead of
the **replace()** one.


Configure the listener in the DIC
---------------------------------

In order to be callable, this listener must be declared into the Dependency Container Injector, so add the following code to your services.xml file:

.. code-block:: xml

    Acme/WebSiteBundle/Resources/config/services.xml
    <parameters>
        <parameter key="acme_web_site.index_listener.class">Acme\WebSiteBundle\Listeners\IndexRenderingListener</parameter>
    </parameters>

    <services>
        <service id="acme_web_site.index_listener" class="%acme_web_site.index_listener.class%">
            <tag name="alpha_lemon_theme_engine.event_listener" event="page_renderer.before_index_rendering" method="onPageRendering" priority="0" />
            <argument type="service" id="service_container" />
        </service>
    </services>

Declaring a service in the DIC should be an operation you are familiar with, otherwise you can read about it in the Symfony2 documentation. Here, only the
**custom tag** declaration will be explained. As you will have noticed, the service exposes the following tag:

.. code-block:: xml

    <tag name="alpha_lemon_theme_engine.event_listener" event="page_renderer.before_index_rendering" method="onPageRendering" priority="0" />

The **name** option identifies the kind of event, and it must be **alpha_lemon_theme_engine.event_listener**. The called method is **onPageRendering**
and it is defined in the parent class of your listener.

More interesting is the event option, which in this case, is **page_renderer.before_index_rendering**: this means that this listener will be
called only for the index page, as explained in the **The rendering process** paragraph.

To have this listener work for all the site pages, the option would be **page_renderer.before_page_rendering**. Having it working for a specific
language it would be **page_renderer.before_en_rendering** (for English language).

.. note::

    When you declare a listener for a page, you must use the page name and not its permalink.

Add extra assets
----------------
As for App-Blocks, it may be necessary to add some assets for a specific listener. This
task can be achieved simply declaring those assets as parameters in the DIC:

.. code-block:: xml

    <parameters>
        <parameter key="acme_web_site.index_listener.alphalemon-cms-demo.external_javascripts" type="collection">            
            <parameter>@AlphaLemonThemeEngineBundle/Resources/public/js/vendor/jquery/*</parameter>
            <parameter>@AlphaLemonWebsiteBundle/Resources/public/vendor/tw-bootstrap/modals/js/bootstrap.min.js</parameter>
        </parameter>
        <parameter key="acme_web_site.index_listener.alphalemon-cms-demo.external_stylesheets" type="collection">
            <parameter>@AlphaLemonWebsiteBundle/Resources/public/vendor/tw-bootstrap/modals/css/bootstrap.min.css</parameter>
        </parameter>
    </parameters>

This configuration adds **twitter bootstrap** and **jquery** to **alphalemon-cms-demo** 
page.

The rule to define the parameter's key is the following:

.. code-block:: text

    [listener name].[page name].[asset type]

in our example

.. code-block:: text

    acme_web_site.index_listener.alphalemon-cms-demo.external_javascripts
    acme_web_site.index_listener.alphalemon-cms-demo.external_stylesheets

Assets are saved into the public folder of the **AlphaLemonWebsiteBundle**:

.. code-block:: text

    @AlphaLemonWebsiteBundle/Resources/public/vendor/tw-bootstrap/modals/css/bootstrap.min.css


This configuration adds the assets only in the declared page, **alphalemon-cms-demo** 
as well, but you have now learned that contents can be replaced for all the pages or for language,
and this architecture is followed for assets too.

So, to add assets for all pages your parameter's key will be:

.. code-block:: text

    [listener name].page.[asset type]

and for language


.. code-block:: text

    [listener name].[language name].[asset type]