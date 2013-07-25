AlphaLemon CMS security
=======================

This chapter covers in detail the AlphaLemon CMS security layer, explaining how to add, 
edit and remove users and roles, and how to customize the AlphaLemon CMS default security 
policy.

Users and roles definition
--------------------------

AlphaLemon CMS bases its security on the Symfony2 security layer to secure the backend 
editor and provides an high-level interface to manage users and roles.

A user is someone who can access to the backend, providing an username and a password.

A role defines the rights on available resources.

A user must have a role and this role grants or deny the access to a particular resource.

.. note::

    While it is not mandatory, you are encouraged to read about security on 
    `Symfony2 documentation`_

Roles
-----

AlphaLemon CMS has three predefined roles, in its default configuration:

1. ROLE_SUPER_ADMIN
2. ROLE_ADMIN
3. ROLE_USER
    
**ROLE_SUPER_ADMIN** and **ROLE_ADMIN** have the same rights and have granted the access
to whole AlphaLemon CMS resorces.

**ROLE_USER** have granted the access to whole AlphaLemon CMS resorce, instead of the
website deploy and the users management.

Add a new Role
~~~~~~~~~~~~~~

Click the **Security** button on the top toolbar, to open the security panel

.. image:: //bundles/alphalemonwebsite/media/manual/img-31.jpg

then click the **Roles** button placed beside the **New** button, under the users list.

.. image:: //bundles/alphalemonwebsite/media/manual/img-32.jpg

The available roles are now listed. Click the **New** button and enter the new role 
into the **Role** textbox. 

.. image:: //bundles/alphalemonwebsite/media/manual/img-33.jpg

.. note::

    A valid role must start with **ROLE_** prefix
    
Click on the **Save** button to confirm your changes.

Click on the **Roles** button to return back to roles list.

Edit a Role
~~~~~~~~~~~

To edit a role, simply click on the **Edit** button near the role you want to 
modify, then proceed as explained in **Add a new Role** section.

Delete a Role
~~~~~~~~~~~~~

To delete a role, simply click on the **Delete** button near the role you want to 
remove and confirm your choice.


Users management
~~~~~~~~~~~~~~~~

Users management works exactly as explained for roles, the only difference is
about the number of information required, which is higher for users than roles.

.. image:: //bundles/alphalemonwebsite/media/manual/img-34.jpg


Advanced configuration
----------------------

When you add new roles and/or you want to implement a restrictive security policy,
you need to modify manually the AlphaLemon CMS security configuration file.

The default **AlphaLemon CMS security file** is saved into the AlphaLemon CMS 
**Resources/config** folder and it is imported into the **AlphaLemon CMS config_alcms.yml**, 
file as shown below:

.. code-block:: text

    // AlphaLemonCmsBundle/config/config_alcms.yml
    imports:
    [...]
        - { resource: "@AlphaLemonCmsBundle/Resources/config/security.yml" }

AlphaLemon CMS is highly decoupled, and the security layer is not an exception. 

The implemented configuration impacts only on AlphaLemon CMS backend because is specific
for the **alcms** environments. This means you can implement your own security policy in 
production when you need it, without collide with the backend.

The security file in detail
---------------------------

Here is a detailed explanation on how the security file is created.


The firewall
~~~~~~~~~~~~

The implemented firewall is quite simple, in fact it secures all the urls which start 
with the **backend** token:

.. code-block:: text

        [...]
        alphalemon_cms:
            pattern:    ~/backend
            form_login:
                check_path:  /backend/login_check
                login_path:  /backend/login
            logout:
                path:   /backend/logout
                target: /backend/
            http_basic:


The access control list
~~~~~~~~~~~~~~~~~~~~~~~

The basic access control list secures the **users** area which is granted to all the users 
who have at least the **ROLE_ADMIN** role and both the deploying routes which can be 
browsed only by users who have at least the **ROLE_ADMIN** role.

.. code-block:: text

    access_control:
        - { path: "~/backend/[a-z]+/al_(stage|production)Deploy", role: ROLE_ADMIN }
        - { path: ~/backend/users, roles: ROLE_ADMIN }
        - { path: ~/backend, roles: ROLE_USER }


The role hierarchy
~~~~~~~~~~~~~~~~~~

The last configuration is for the role hierarchy, which is implemented as follows:

.. code-block:: text

    role_hierarchy:
        ROLE_ADMIN:       ROLE_USER
        ROLE_SUPER:ADMIN: [ROLE_USER, ROLE_ADMIN, ROLE_ALLOWED_TO_SWITCH]


How to customize the security.yml file
--------------------------------------

Symfony does not permit to import or configure a security file from another 
configuration file, so the only way to change the implemented rules is to modify 
the **security.yml** file that comes with AlphaLemon CMS.

It is really a bad idea to work on the security file that comes with
**AlphaLemonCmsBundle** bundle, because when you would upgrade the cms, the changes 
you have made will be lost.

To avoid that, you must copy the AlphaLemon's security file into the application's 
config folder, rename it, for example, to **security_cms.yml**, and change the import 
directive in the config_alcms.yml:

.. code-block:: text

    // app/config/config_alcms.yml
    imports:
    [...]
    - { resource: "security_cms.yml" }

Customizing the security for your website
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
A real life example could be the following one: you may need to add a new role, 
called **ROLE_PUBLISHER**, to allow granted users, which belongs that role, to publish 
the website, to leverage site admins from this task.

First of all you must add that role in the website as explained before, then you must 
change the rule that secures the deploy action as follows:

.. code-block:: text

    access_control:
        - { path: ~/backend/[a-z]+/al_(stage|production)Deploy, role: ROLE_PUBLISHER }
        - { path: ~/backend/users, roles: ROLE_ADMIN }
        - { path: ~/backend, roles: ROLE_USER }

To let that work you must change the role_hierarchy as follows:

.. code-block:: text

    role_hierarchy:
        ROLE_PUBLISHER:         ROLE_USER
        ROLE_ADMIN:             ROLE_PUBLISHER
        ROLE_SUPER_ADMIN:       ROLE_ADMIN

You can learn more about this reading the `Symfony2 security chapter`_.

Let's now assume that you want to avoid users granted by **ROLE_USER** role to delete 
contents.

The route that points this action is the **deleteBlock**, so you must add the new security
rule as follows:

.. code-block:: text

    access_control:
        - { path: ~/backend/[a-z]+/deleteBlock, role: ROLE_PUBLISHER }
        - { path: ~/backend/[a-z]+/al_deploy, role: ROLE_PUBLISHER }
        - { path: ~/backend/users, roles: ROLE_ADMIN }
        - { path: ~/backend, roles: ROLE_USER }


.. class:: fork-and-edit

Found a typo ? Something is wrong in this documentation ? `Just fork and edit it !`_

.. _`Just fork and edit it !`: https://github.com/alphalemon/alphalemon-docs
.. _`Symfony2 documentation`: http://symfony.com/doc/current/book/security.html
.. _`Symfony2 security chapter`: http://symfony.com/doc/current/book/security.html
