AlphaLemon CMS installation procedure
=====================================

AlphaLemon CMS is distributed using `composer`_, so the first thing to do is to get
composer.

.. note::

    This tutorial explains how to install AlphaLemon CMS into an existing project,
    in which the dependencies are managed by composer.

    To start an AlphaLemon CMS project from scratch, you should use the
    `AlphaLemon CMS sandbox`_.


Composer installation
---------------------

To install composer, move to the root folder of your application then run the following
command to download composer:

.. code-block:: text

    curl -s http://getcomposer.org/installer | php

If you do not have curl installed, then you can use:

.. code-block:: text
	
	 php -r "eval('?>'.file_get_contents('https://getcomposer.org/installer'));"


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
            },
            {
                "type": "git",
                "url": "https://github.com/alphalemon/Propel"
            }
        ],
        "require": {
            "alphalemon/alphalemon-cms-bundle": "1.0.*",
            "alphalemon/alphalemon-cms-installer-bundle": "1.0.*",
            "doctrine/common": "dev-bugfix as 2.3.0"
        },
        "minimum-stability": "dev",
    }

.. note::

    There is a bug in **doctrine/common** that causes the AlphaLemon CMS tests to fail.
    If you don't plan to run the test suit, you can safety remove that dependency.

    The propel dependency will be removed when the 1.6.8 version is released.

Install AlphaLemon CMS
----------------------

To install, run the following command

.. code-block:: text

    php composer.phar update


AlphaLemon CMS setup
--------------------

AlphaLemonCMS requires several steps in order to properly setup the CMS itself. Luckily
the **AlphaLemonCmsInstallerBundle** will do the full job for you, providing a web installer interface
or an interactive Symfony2 command to install AlphaLemonCMS.


Install other dependencies
--------------------------

AlphaLemon CMS requires by default the `yui compressor`_ which is useful to compact 
your assets into one, which reduces page time loading. Grab and unpack it into the **app/Resources/java**
folder and rename it **yuicompressor.jar**.

.. note::

    The compiled yui compressor is saved into the package's **build** folder.

    The **java** folder does not exist and must be created.


While it's strongly suggested to use this tool, you may not have it installed. In this case
you must add the following configuration to your **config_alcms.yml** file:

.. code-block:: text

    app/config/config_alcms.yml

    alpha_lemon_cms:
        enable_yui_compressor: false

.. note::

    The **config_alcms.yml** is created into the app/config folder by the AlphaLemon CMS
    installer, so you must install the CMS then add the configuration as shown.

The deploy bundle
-----------------

From the Symfony2 book:

    Before you begin, you'll need to create a bundle. Learn more about this topic
    from the `Symfony2 book`_

AlphaLemon CMS does not add anything new to Symfony2, so it requires you to create 
that bundle too.

By default AlphaLemon CMS looks for the **Acme/WebSite** bundle. Obviously you can
choose any name you wish for your bundle: the AlphaLemon CMS installer will ask you
for this.

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

Website controller
------------------
Websites routes are handled in production by a single **Controller** named by default
**Website**.

To have your website displayed, you must add this controller to your application. To
achieve this task you can add a new controller or simply modify the default one added
by Symfony. 

Add a **WebSiteController.php** file inside the Controller folder of your bundle. Open it 
and add this code:

.. code-block:: php
    
    namespace Your\Bundle\Controller

    use AlphaLemon\ThemeEngineBundle\Core\Rendering\Controller\FrontendController;

    class WebSiteController extends FrontendController
    {
    }

If you want to use a controller with a different name, you must obviously rename the
controller itself, then you must tell AlphaLemon CMS to generate the routes pointing
this controller.

This last step is achieved adding the following configuration to your **config_alcms.yml**
file:

.. code-block:: text

    // app/config/config_alcms.yml

    alpha_lemon_cms:
        deploy_bundle:
          controller: Site

Don't forget to rename the controller to **SiteController.php** and change the controller's 
code as follows:

.. code-block:: php
    
    namespace Your\Bundle\Controller

    use AlphaLemon\ThemeEngineBundle\Core\Rendering\Controller\FrontendController;

    class SiteController extends FrontendController
    {
    }


Add the installer routes for web interface
------------------------------------------
Finally, if you are going to use the web interface, you must add the routes for the
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

Provide the required information and you are done! Once the process is complete, a web
page is rendered with the process summary and gives you the information required
to start.

Permissions
-----------
Don't forget to setup the permissions on the installation folder as explained in the
`Symfony2 setup and configuration tutorial`_

Remove the AcmeDemoBundle
-------------------------
Symfony2 comes with a built-in demo which must be removed:

Delete the **src/Acme/DemoBundle** folder.

Delete the following code from **app/AppKernel.php**

.. code-block:: php

    // app/AppKernel.php
    $bundles[] = new Acme\DemoBundle\AcmeDemoBundle();


Delete the following code from **app/config/routing_dev.yml**

.. code-block:: text

    # app/config/routing_dev.yml
    _welcome:
        pattern: /
        defaults: { _controller: AcmeDemoBundle:Welcome:index }

    _demo_secured:
        resource: "@AcmeDemoBundle/Controller/SecuredController.php"
        type: annotation

    _demo:
        resource: "@AcmeDemoBundle/Controller/DemoController.php"
        type: annotation
        prefix: /demo


Clear your cache:

.. code-block:: text

    php app/console cache:clear


What to do if something goes wrong
----------------------------------
The AlphaLemon CMS installer changes some of the configuration files of your application,
so if something goes wrong during the setup, you could have problems running the install
process again after these changes have been implemented.

Luckily, the installer backs up those files, so to fix the problem, you have simply to
remove the files changed by the installer and restore the backed up ones.

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
.. _`Symfony2 setup and configuration tutorial`: http://symfony.com/doc/current/book/installation.html#configuration-and-setup
.. _`yui compressor`: https://github.com/yui/yuicompressor/downloads
.. _`Symfony2 book`: http://symfony.com/doc/current/book/page_creation.html#before-you-begin-create-the-bundle