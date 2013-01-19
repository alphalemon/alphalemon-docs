AlphaLemon CMS installation procedure
=====================================

AlphaLemon CMS is distributed using `composer`_, so the first thing to do is to get
composer.

.. note::

    This tutorial explains how to install AlphaLemon CMS into an existing project
    which dependencies are managed by composer.

    To start an AlphaLemon CMS project from the scratch, you should use the
    `AlphaLemon CMS sandbox`_.


Composer installation
---------------------

To install composer, move to the root folder of your application then run the following
command to download composer:

.. code-block:: text

    curl -s http://getcomposer.org/installer | php


Add AlphaLemon to your composer.json file
-----------------------------------------

AlphaLemon CMS requires a full working Symfony 2.1 application, so download the latest
Symfony2 release then open the composer.json file and add the following code:

.. code-block:: text

    {
        "repositories": [
            {
                "type": "composer",
                "url": "http://apps.alphalemon.com/"
            },
            {
                "type": "git",
                "url": "https://github.com/alphalemon/common"
            }
        ],
        "require": {
            "alphalemon/alphalemon-cms-bundle": "dev-master",
            "alphalemon/alphalemon-cms-installer-bundle": "dev-master",
            "doctrine/common": "dev-bugfix as 2.3.0"
        },
        "minimum-stability": "dev",
    }

Install AlphaLemon CMS
----------------------

To install run the following command

.. code-block:: text

    php composer.phar update


AlphaLemon CMS setup
--------------------

AlphaLemonCMS requires several steps to be accomplished to properly setup the CMS itself. Luckily
the **AlphaLemonCmsInstallerBundle** will do all the job for you, providing a web installer interface
or an interactive symfony2 command to install AlphaLemonCMS.


Install other dependencies
--------------------------

AlphaLemon CMS requires `tinyMCE`_ installed into the **web/js** folder, so grab 
and unpack it into that directory.

.. note::

    The **web/js** folder does not exist when you start a Symfony2 application from
    the scratch, so you need to create it by yourself.

    The tinyMCE package contains the required **tiny_mce** library under the **tinymce/jscripts/** 
    Be sure to unpack directy that library under the **web/js** folder.

AlphaLemon CMS requires by default the `yui compressor`_ which is useful to compact 
your assets into one, reducing page time loading. Grab and unpack it into the **app/Resources/java**
folder and rename it **yuicompressor.jar**.

.. note::

    The compiled yuicompressor is saved into the package's **build** folder.

    The **java** folder does not exist and must be created.


While it's strongly suggested to use this tool, you may not install it. In this case
you must add the following configuration to your **config_alcms.yml** file:

.. code-block:: text

    app/config/config_alcms.yml

    alpha_lemon_cms:
        enable_yui_compressor: false

.. note::

    The **config_alcms.yml** is created into the app/config folder by the AlphaLemon CMS
    installer, so you must install the CMS then add the configuration just exposed.

The deploy bundle
-----------------

From the Symfony2 book:

    Before you begin, you'll need to create a bundle. Learn more from this topic
    from the `Symfony2 book`_

AlphaLemon CMS does not add anything new to Symfony2, so it requires you to create 
that bundle too.

By default AlphaLemon CMS looks for the **Acme/WebSite** bundle. Obviously you can
choose the name you wish for your bundle: the AlphaLemon CMS installer will ask for
it.

Add the AlphaLemon CMS installer bundle to AppKernel
----------------------------------------------------

To enable the AlphaLemon CMS installer you must add it to your AppKernel file:

.. code-block:: php

    //app/AppKernel.php

    public function registerBundles()
    {
        $bundles = array(

            [...]   
            
            new AlphaLemon\CmsInstallerBundle\AlphaLemonCmsInstallerBundle(),
        );
    }

Add the installer routes for web interface
------------------------------------------
At last, if you are going to use the web interface, you must add the routes for the
install bundle:

.. code-block:: text
    
    // app/config/routing.yml
    _AlphaLemonCmsInstallerBundle:
        resource: "@AlphaLemonCmsInstallerBundle/Resources/config/routing.yml"

.. note::

    This setting is not required if you install using the console.


Installing from the console
---------------------------

Installing AlphaLemonCMS from the console is really easy:

.. code-block:: text

    app/console alphalemon:install-cms

This will run the interactive command. Provide the required information and you are done! Point
your browser at

.. code-block:: text

    http://localhost/alcms.php/backend/en/index

to start using AlphaLemonCMS.

Installing using the web interface
----------------------------------

To start AlphaLemon CMS installation, simply point your browser at:

.. code-block:: text

    http://localhost/app_dev.php/install

Provide the required information and you are done! After the process ended, a web
page is rendered with the process summary and gives you the information required
to start.

Permissions
-----------
Don't forget to setup the permissions on the installation folder as explained in the
`symfony2 setup and configuration tutorial`_

Custom deploy bundle
--------------------
During the installation process, the wizard or the web interface asks you to specify 
the **Deploy bundle**.

If you specificy a deploy bundle different than the proposed one, you must change manually
the controller that handles all the routes generated by AlphaLemon CMS.

To achieve that task, add a **WebSiteController.php** under your deploy bundle's Controller 
folder, or simply rename the default one to **WebSiteController.php**, then change the 
controller as follows:

.. code-block:: php

    use AlphaLemon\ThemeEngineBundle\Core\Rendering\Controller\FrontendController;

    class WebSiteController extends FrontendController
    {
    }

At the moment the controller name is hardcoded, but this will chang in the future,
just adding a configurable parameter.


What to do if something goes wrong
----------------------------------
The AlphaLemon CMS installer changes some configuration files of your application,
so, if something goes wrong during the setup, you could have problems to run the install
process again, due to the changes mentioned above.

Luckily, the installer backup those files, so to fix the problem, you have simply to
remove the files changed by the installer and restore the backupped ones.

Those files are:

.. code-block:: text

    app/AppKernel.php
    app/config/config.yml
    app/config/routing.yml

For all of those files, the installer creates a specular copy with the **.bak** extension
before changing the file itself.

If the bak file does not exist, it means that the file has not been changed yet.


.. _`composer`: http://getcomposer.org
.. _`AlphaLemon CMS sandbox`: http://github.com/AlphaLemonCmsSandbox
.. _`symfony2 setup and configuration tutorial`: http://symfony.com/doc/current/book/installation.html#configuration-and-setup
.. _`tinyMCE`: http://www.tinymce.com/download/download.php
.. _`yui compressor`: https://github.com/yui/yuicompressor/downloads
.. _`Symfony2 book`: http://symfony.com/doc/current/book/page_creation.html#before-you-begin-create-the-bundle