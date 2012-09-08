AlphaLemon CMS installation procedure
=====================================

AlphaLemon CMS is distributed using `composer`_, so the first thing to do is to get
composer.

.. note:

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

Open your composer .json file and add the following code:

.. code-block:: text

    {
        "repositories": [
            {
                "type": "composer",
                "url": "http://apps.alphalemon.com/"
            }
        ],
        "require": {
            "alphalemon/alphalemon-cms-bundle": "dev-master",
            "alphalemon/alphalemon-cms-installer-bundle": "dev-master"
        }
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



.. _`composer`: http://getcomposer.org
.. _`AlphaLemon CMS sandbox`: http://github.com/AlphaLemonCmsSandbox
.. _`symfony2 setup and configuration tutorial`: http://symfony.com/doc/current/book/installation.html#configuration-and-setup
