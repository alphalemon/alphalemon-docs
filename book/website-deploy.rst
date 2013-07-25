Website deploying
=================

This chapter explains in detail how to deploy your website from the AlphaLemon CMS
backend editor, to the stage or production environments.

Deploy the website
------------------

AlphaLemon CMS website deploying process consist in converting the data entered in the
backend to Symfony2 templates and routes, and to copy assets from the backend to 
stage or production environments.

AlphaLemon CMS generates a Twig template for each page you entered in the backend. This
template inherits from the assigned template and creates Twig blocks that override
the ones defined in the original template.

Each page generates a Symfony2 route to map the url (permalink) with the Symfony2
controller which renders the page.

At last, it removes the current assets and replaces them copying all the new assets 
from the backend to stage or production folders.

All these files are generated and copied to the **Deploy bundle** defined when 
AlphaLemon CMS has been installed.

.. note::

    The deploying process is very quick but it takes some time because, when the
    AlphaLemon CMS process is ended, AlphaLemon CMS must call the Symfony2 commands 
    to reintall assets, dump assets for assetic and clean the cache.


The deploying process
^^^^^^^^^^^^^^^^^^^^^

Deploying the website is a hard work, but only for AlphaLemon CMS, in fact
you are only required to choose the deploying environment.

To deploy for the stage environment simply click the **Deploy stage** button
from the top toolbar.

To deploy for the production environment simply click the **Deploy prod** button
from the top toolbar.

Both of them will prompt a confirmation message.

The deploying generation result
-------------------------------

When you deploying for the stage environment, AlphaLemon CMS generates the 
following folders and files into the deploy bundle:

.. code:: text

    Resources
        config
            site_routing_stage.yml
        public
            stage
                css
                js
                files
                media
        view
            AlphaLemonStage
                [language [n]]
                    base
                        base_template_1.html.twig                        
                        [...]
                        base_template_[n].html.twig
                    page_1.html.twig
                    [...]
                    page_[n].html.twig

.. note::
    
    Files generated for stage environment can be safety removed or ignored in production.
    
                 
When you deploying for the production environment, AlphaLemon CMS generates the 
following folders and files into the deploy bundle:

.. code:: text

    Resources
        config
            site_routing.yml
        public
            css
            js
            files
            media
        view
            AlphaLemon
                [language[n]]
                    base
                        base_template_1.html.twig                        
                        [...]
                        base_template_[n].html.twig
                    page_1.html.twig
                    [...]
                    page_[n].html.twig



Working in locale
-----------------

When you have installed AlphaLemon CMS directly on your remote server your changes
are immediaty displayed on your website, after deploying.

If you manage your website on your laptop, you must transfer files to the remote 
server after you deployed the website.

AlphaLemon CMS does not provide any tool to do this job, so you can refer this
Symfony2 cookbook entry which covers the topic in detail.


.. class:: fork-and-edit

Found a typo ? Something is wrong in this documentation ? `Just fork and edit it !`_

.. _`Just fork and edit it !`: https://github.com/alphalemon/alphalemon-docs