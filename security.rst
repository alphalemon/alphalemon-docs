AlphaLemon CMS security
=======================

AlphaLemon CMS comes with a preconfigured security file that contains the appropriate rules to secure AlphaLemon CMS.

This file is saved in the AlphaLemon CMS **Resources/config** folder, so enabling it simply means adding an import directive
into the **config_alcms.yml** file:

.. code-block:: text

    // app/config/config_alcms.yml
    imports:
    [...]
    - { resource: "@AlphaLemonCmsBundle/Resources/config/security.yml" }

This enables the security for the **alcms*** environments, in fact the **config_alcms.yml** is imported by both
**config_alcms_dev.yml** and **config_alcms_test.yml**.

.. note::

    If you installed AlphaLemon CMS using the installer, this configuration is automatically added.

As you should know, AlphaLemon CMS is highly decoupled, and the security is not an exception. In fact the security
configuration is applied only to AlphaLemon CMS, and not to the frontend (deploy) Bundle. This means that your
frontend environment could be secured on its own.

The user interface
------------------
After having secured your application, two more commands are added to the AlphaLemon CMS toolbar:

    - Users
    - Logout

The second of these just allows you to close your authenticated session, while the **Users** command lets you manage users and roles dynamically.

The interface is quite simple: when you open the panel, you get the list of the users enabled for the website and you can add, edit or delete them.

The command **Role** opens the roles' manager panel which works exactly like the previous one, letting you add, edit or delete the website roles.

The security file in detail
---------------------------

Here is a detailed explanation of how the security file is created:


The firewall
~~~~~~~~~~~~

The implemented firewall is quite simple, in fact it secures all the urls, starting with **backend**:

.. code-block:: text

        [...]
        alphalemon_cms:
            pattern:    ^/backend
            form_login:
                check_path:  /backend/login_check
                login_path:  /backend/login
            logout:
                path:   /backend/logout
                target: /backend/
            http_basic:


The access control list
~~~~~~~~~~~~~~~~~~~~~~~

The basic access control list secures the **users** area which is granted to all the users who have at least the
**ROLE_ADMIN** role and the **al_local_deploy** route. This can be browsed only by users who have the **ROLE_ADMIN**
role.

.. code-block:: text

    access_control:
        - { path: "^/backend/[a-z]+/al_local_deploy", role: ROLE_ADMIN }
        - { path: ^/backend/users, roles: ROLE_ADMIN }
        - { path: ^/backend, roles: ROLE_USER }


The role hierarchy
~~~~~~~~~~~~~~~~~~

The last configuration is for the role hierarchy, which is implemented as follows:

.. code-block:: text

    role_hierarchy:
        ROLE_ADMIN:       ROLE_USER
        : [ROLE_USER, ROLE_ADMIN, ROLE_ALLOWED_TO_SWITCH]

So a user who has the **ROLE_SUPER_ADMIN** role can access the **user** area and the **al_local_deploy** route.


How to customize the security.yml file
--------------------------------------

Symfony does not permit you to import or configure the security file from another configuration file, so the
only way to change the implemented rules is to change the **security.yml** file that comes with AlphaLemon CMS.

However, it is not a good idea to directly edit the **AlphaLemonCmsBundle** bundled file, because when you
upgrade the Bundle, the changes you have made will be lost.

To avoid that, you must copy the AlphaLemon's security file into the applications config folder and change
the import directive in the config_alcms.yml:

.. code-block:: text

    // app/config/config_alcms.yml
    imports:
    [...]
    - { resource: "security_cms.yml" }

.. note::

    This assumes that the security file has been renamed to **security_cms.yml**.

Customizing the security for your website
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Let's assume you may need to add a new role, called **ROLE_PUBLISHER**, to allow granted users to publish
the website instead of the site admins.

First of all you must add that role in the website, then you must change the deploy action as follows:

.. code-block:: text

    access_control:
        - { path: ^/backend/[a-z]+/al_deploy, role: ROLE_PUBLISHER }
        - { path: ^/backend/users, roles: ROLE_ADMIN }
        - { path: ^/backend, roles: ROLE_USER }

To let that work you must change the role_hierarchy as follows:

.. code-block:: text

    role_hierarchy:
        ROLE_PUBLISHER:         ROLE_USER
        ROLE_ADMIN:             ROLE_PUBLISHER
        ROLE_SUPER_ADMIN:       ROLE_ADMIN

You can learn more in `security from Symfony2 book`_.

Let's now assume that you want to avoid users granted by **ROLE_USER** role to delete contents.

The route that performs this action is the **deleteBlock**, so it is secured as follows:

.. code-block:: text

    access_control:
        - { path: ^/backend/[a-z]+/deleteBlock, role: ROLE_PUBLISHER }
        - { path: ^/backend/[a-z]+/al_deploy, role: ROLE_PUBLISHER }
        - { path: ^/backend/users, roles: ROLE_ADMIN }
        - { path: ^/backend, roles: ROLE_USER }


.. _`security from Symfony2 book`: http://symfony.com/doc/current/book/security.html