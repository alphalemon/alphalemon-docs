Source Code
===========

**AlphaLemon CMS is hosted at GitHub** and uses **Git** for source control. In order to obtain 
the source code, you must first install Git on your system. Instructions for installing 
and setting up Git can be found at http://help.github.com/set-up-git-redirect.

**AlphaLemon CMS is a Symfony2 Bundle**, this means you need a full Symfony2 working 
application to use this Bundle. The easy way you have to start with it, is using
the **AlphaLemon CMS Sandbox**, a full Symfony2 application configured with AlphaLemon CMS
requirements.

If you want to create a local copy of the source to play with, you can clone 
the main repository using this command:

.. code:: text

    git clone https://github.com/alphalemon/AlphaLemonCmsSandbox.git

If you're planning on contributing to AlphaLemon CMS, then it's a good idea to fork the 
repository. You can find instructions for forking a repository at http://help.github.com/fork-a-repo/.

After forking the AlphaLemon CMS repository, you need to configure the application's
**composer.json** to use your forked repository:

.. code:: text

    {
        "repositories": [
            {
                "type": "vcs",
                "url": "[ YOUR GITHUB ALPHALEMON CMS FORKED REPO URL]"
            }
        ],
    }

Code standards
--------------

AlphaLemon CMS ecosystem php code is written following the `Symfony2 code standards`_.

AlphaLemon CMS follows `Best Practices for structuring Bundles`_ with some
small exceptions:

- Thirdy-part client-side vendor libraries could be added only for App-Blocks
- Test must live under the appropriate folder under the Tests directory

You should carefully read those documentation before start coding. 

Directory structure
-------------------

The AlphaLemon CMS directory and file is structured as all the Symfony2 Bundles, the only
exception regards it's core code which lives under the **Core** folder instead of the **src** 
one. 

The Bundle's structure is the following:

.. code:: text

    Bundle - AlphaLemon CMS internal App-Blocks bundles
    Command - AlphaLemon CMS CLI commands
    Controller - Symfony2 controllers which manages AlphaLemon CMS actions
    Core - AlphaLemon CMS core. Here are saved all the objects used by the cms itself
    DependencyInjection - The extension that loads AlphaLemon CMS services into the container
    Model - Propel's autogenerated classes
    Resources
        config - Configuration files
        dbupdate - Database patches 
            mysql - Mysql specific patches
        docs - AlphaLemon CMS docs
        public
            css
                skins
                    bootrap - Default AlphaLemon CMS skin
                        lib - Skin sass files
        skeleton - Twig skeleton files for generators
        translations - AlphaLemon CMS translation files
        views - AlphaLemon CMS twig templates
    Tests - Test suite
        Functional - Functional tests
        Integrated - Integrated tests
        Unit - Unit tests
    Twig - Twig extensions
    vendor - Populated with vendor libraries to run tests


.. class:: fork-and-edit

Found a typo ? Something is wrong in this documentation ? `Just fork and edit it !`_

.. _`Just fork and edit it !`: https://github.com/alphalemon/alphalemon-docs
.. _`Symfony2 code standards`: http://symfony.com/doc/current/contributing/code/standards.html
.. _`Best Practices for structuring Bundles`: http://symfony.com/doc/current/cookbook/bundles/best_practices.html