The internals of themes configuration
=====================================

This chapter will explain the internals of an App-Theme and refers to **BusinessWebsiteThemeBundle**,
the default theme that comes with AlphaLemon CMS.

The Theme service
-----------------

An AlphaLemon CMS theme is defined as a service in the DIC **Dependency Injector Container** and its definition
is saved into the **Resources/config** folder as xml file.

This file is named **[bundle_name_extension_alias].xml**, so the **BusinessWebsiteThemeBundle**
has an alias named **business_website_theme** which is the one used for the file name.

.. code-block:: xml

    // Acme/Theme/BusinessWebsiteThemeBundle/Resources/config/business_website_theme.xml
    <services>
        <service id="business_website.theme" class="%alpha_lemon_theme_engine.theme.class%">
            <argument type="string">BusinessWebsiteTheme</argument>
            <tag name="alpha_lemon_theme_engine.themes.theme" />
        </service>
    </services>

This service defines an **AlTheme** object and its class has already been declared in the
ThemeEngine services configuration and it is identified by the **%alpha_lemon_theme_engine.theme.class%**
parameter.

The theme's id is defined as **[bundle_name_extension_alias].theme** and requires a
string argument which contains the theme's name without the Bundle extension, so
**BusinessWebsiteTheme** in this case.

To define this bundle as a Theme-App, the service has been tagged as
**alpha_lemon_theme_engine.themes.theme**.

The template service
--------------------

A Template is defined as an xml file saved under a dedicated folder under the **Resources/config** directory,
named **templates**. Let's see how the BusinessWebsiteThemeBundle's home template is defined:

.. code-block:: xml

    <parameters>
        <parameter key="business_website.home.external_stylesheets.cms" type="collection">
            <parameter>@BusinessWebsiteThemeBundle/Resources/public/css/cms_fix.css</parameter>
        </parameter>
    </parameters>

    <services>
        <service id="business_website.theme.template_assets.home" class="%alpha_lemon_theme_engine.template_assets.class%">
            <call method="setExternalStylesheets">
                <argument type="collection">
                    <argument>@BusinessWebsiteThemeBundle/Resources/public/css/reset.css</argument>
                    <argument>@BusinessWebsiteThemeBundle/Resources/public/css/layout.css</argument>
                    <argument>@BusinessWebsiteThemeBundle/Resources/public/css/style.css</argument>
                    <argument>@BusinessWebsiteThemeBundle/Resources/public/css/al_fix_style.css</argument>
                </argument>
            </call>
            <call method="setExternalJavascripts">
                <argument type="collection">
                    <argument>@AlphaLemonThemeEngineBundle/Resources/public/js/vendor/jquery/*</argument>
                    <argument>@BusinessWebsiteThemeBundle/Resources/public/js/cufon-yui.js</argument>
                    <argument>@BusinessWebsiteThemeBundle/Resources/public/js/al-cufon-replace.js</argument>
                    <argument>@BusinessWebsiteThemeBundle/Resources/public/js/Swis721_Cn_BT_400.font.js</argument>
                    <argument>@BusinessWebsiteThemeBundle/Resources/public/js/Swis721_Cn_BT_700.font.js</argument>
                    <argument>@BusinessWebsiteThemeBundle/Resources/public/js/jquery.easing.1.3.js</argument>
                    <argument>@BusinessWebsiteThemeBundle/Resources/public/js/jcarousellite.js</argument>
                </argument>
            </call>
        </service>

        <service id="business_website.theme.template.home.slots" class="%alpha_lemon_theme_engine.template_slots.class%">
            <tag name="business_website.theme.template.home" />
        </service>

        <service id="business_website.theme.template.home" class="%alpha_lemon_theme_engine.template.class%">
            <argument type="service" id="kernel" />
            <argument type="service" id="business_website.theme.template_assets.home" />
            <argument type="service" id="business_website.theme.template.home.slots" />
            <tag name="business_website.theme.template" />

            <call method="setThemeName">
                <argument type="string">BusinessWebsiteThemeBundle</argument>
            </call>
            <call method="setTemplateName">
                <argument type="string">home</argument>
            </call>
        </service>
    </services>

To template configuration initializes three mandatory services:

1. The template
2. The template assets
3. The template slots.

and an optional parameter. Let's see in detail.

The template service
~~~~~~~~~~~~~~~~~~~~

The template service defines an **AlTemplate** object which class has already been
declared in the ThemeEngine services configuration and is identified as
**%alpha_lemon_theme_engine.template.class%**.

.. code-block:: xml

    // Acme/Theme/BusinessWebsiteThemeBundle/Resources/config/templates/home.xml
    <service id="business_website.theme.template.home" class="%alpha_lemon_theme_engine.template.class%">
        <argument type="service" id="kernel" />
        <argument type="service" id="business_website.theme.template_assets.home" />
        <argument type="service" id="business_website.theme.template.home.slots" />
        <tag name="business_website.theme.template" />

        <call method="setThemeName">
            <argument type="string">BusinessWebsiteThemeBundle</argument>
        </call>
        <call method="setTemplateName">
            <argument type="string">home</argument>
        </call>
    </service>

The template id has been defined as **[bundle_name_extension_alias].theme.template.[template_name]**.

This service requires three arguments: the symfony's kernel service, a **business_website.theme.template_assets.home**
service and a **business_website.theme.template.home.slots** service which are the
services mentioned above, that will be defined in a while.

The service is tagged as **[bundle_name_extension_alias].theme.template**, in this
example **business_website.theme.template**.

As last the **setThemeName** and **setTemplateName** methods are called to define
respectly the theme's name an the template's name.

The template assets service
~~~~~~~~~~~~~~~~~~~~~~~~~~~

The template assets service defines an **AlTemplateAssets** object which class has
already been declared in the ThemeEngine services configuration and identified as
**%alpha_lemon_theme_engine.template_assets.class%**.

.. code-block:: xml

    // Acme/Theme/BusinessWebsiteThemeBundle/Resources/config/templates/home.xml
    <service id="business_website.theme.template_assets.home" class="%alpha_lemon_theme_engine.template_assets.class%">
            <call method="setExternalStylesheets">
                <argument type="collection">
                    <argument>@BusinessWebsiteThemeBundle/Resources/public/css/reset.css</argument>
                    <argument>@BusinessWebsiteThemeBundle/Resources/public/css/layout.css</argument>
                    <argument>@BusinessWebsiteThemeBundle/Resources/public/css/style.css</argument>
                    <argument>@BusinessWebsiteThemeBundle/Resources/public/css/al_fix_style.css</argument>
                </argument>
            </call>
            <call method="setExternalJavascripts">
                <argument type="collection">
                    <argument>@AlphaLemonThemeEngineBundle/Resources/public/js/vendor/jquery/*</argument>
                    <argument>@BusinessWebsiteThemeBundle/Resources/public/js/cufon-yui.js</argument>
                    <argument>@BusinessWebsiteThemeBundle/Resources/public/js/al-cufon-replace.js</argument>
                    <argument>@BusinessWebsiteThemeBundle/Resources/public/js/Swis721_Cn_BT_400.font.js</argument>
                    <argument>@BusinessWebsiteThemeBundle/Resources/public/js/Swis721_Cn_BT_700.font.js</argument>
                    <argument>@BusinessWebsiteThemeBundle/Resources/public/js/jquery.easing.1.3.js</argument>
                    <argument>@BusinessWebsiteThemeBundle/Resources/public/js/jcarousellite.js</argument>
                </argument>
            </call>
        </service>

This object is deputated to contain and manage the assets used by the template. The template assets id has
been defined as **[bundle_name_extension_alias].theme.template_assets.[template_name]**.

It calls the **setExternalStylesheets** method to add some external stylesheets to the template and
**setExternalJavascripts** to add some external javascripts.

The callable methods to define the template assets are:

1. setExternalStylesheets - Adds some stylesheets to the template
2. setExternalJavascripts - Adds some javascripts to the template
3. setInternalStylesheets - Adds an internal stylesheet to the template
4. setInternalJavascripts - Adds an internal javascript to the template

The template slots service
~~~~~~~~~~~~~~~~~~~~~~~~~~

The template slots service defines an **AlTemplateSlots** object which class has
already been declared in the ThemeEngine services configuration and it is identified
by the **%alpha_lemon_theme_engine.template_slots.class%** parameter.

.. code-block:: xml

    // Acme/Theme/BusinessWebsiteThemeBundle/Resources/config/templates/home.xml
    <service id="business_website.theme.template.home.slots" class="%alpha_lemon_theme_engine.template_slots.class%">
        <tag name="business_website.theme.template.home" />
    </service>

This object is deputated to manage the template slots. Each template managed by AlphaLemon is made by slots and each
slot is the place where one or more blocks live.

The template slots' id has been defined as **[bundle_name_extension_alias].theme.template.[template_name].slots** and
it has been tagged as **[bundle_name_extension_alias].theme.template.home**.

Declare the template slots
--------------------------

The last thing to do is to define the slots for each template.

Each slot is a service that define an **AlSlot** object. The sevice's class has
already been declared in the ThemeEngine services configuration and it is identified
by the **%alpha_lemon_theme_engine.slot.class%** parameter.

The services file has been created under the **Resources/config/templates/slots** folder of your Theme-App
and it has been named as **[template_name].xml**. Follows the implementation:

.. code-block:: xml

    // Acme/Theme/BusinessWebsiteThemeBundle/Resources/config/templates/slots/home.xml
    <services>
        <service id="business_website.theme.template.base.slots.logo" class="%alpha_lemon_theme_engine.slot.class%">
            <argument type="string">logo</argument>
            <argument type="collection" >
                <argument key="repeated">site</argument>
                <argument key="htmlContent">
                    <![CDATA[<img src="/uploads/assets/media/business-website-original-logo.png" title="Progress website logo" alt="Progress website logo" />]]>
                </argument>
            </argument>
            <tag name="business_website.theme.template.base.slots" />
        </service>

        [...]
    </services>

Here is represented only the implementation for a single slot, others follows the same rules.

This object requires as first argument a string that defines the slot name.

Second argument is an array of options which are well detailed in the `add a new theme`_ chapter.

As saw for other services, this service must be tagged following this scheme:
**[bundle_name_extension_alias].theme.template.[template_name].slots**.

The base config file
~~~~~~~~~~~~~~~~~~~~

All the repeated slots are added to a common file named **base.xml**. The name is mandatory.

Register the configuration files
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

At last, the configuration files must be registered in the **Dependency Injector Container**:

.. code-block:: php

    // Acme/Theme/BusinessWebsiteThemeBundle/DependencyInjection/FancyThemeExtension.php
    class BusinessWebsiteThemeExtension extends Extension
    {
        public function load(array $configs, ContainerBuilder $container)
        {
            // Register the services file
            $loader = new XmlFileLoader($container, new FileLocator(__DIR__.'/../Resources/config'));

            // This code is autogenerated - DON'T CHANGE IT MANUALLY

            $loader->load('business_website_theme.xml');

            $loader = new XmlFileLoader($container, new FileLocator(__DIR__ . '/../Resources/config/templates'));
            $loader->load('rightcolumn.xml');
            $loader->load('sixboxes.xml');
            $loader->load('home.xml');
            $loader->load('fullpage.xml');

            $loader = new XmlFileLoader($container, new FileLocator(__DIR__ . '/../Resources/config/templates/slots'));
            $loader->load('rightcolumn.xml');
            $loader->load('sixboxes.xml');
            $loader->load('base.xml');
            $loader->load('home.xml');
            $loader->load('fullpage.xml');

            // This code is autogenerated - DON'T CHANGE IT MANUALLY
        }

        public function getAlias()
        {
            return 'business_website_theme';
        }
    }

AlphaLemon CMS adds two placeholders to identify the autogenerated code.

.. class:: fork-and-edit

Found a typo ? Something is wrong in this documentation ? `Just fork and edit it !`_

.. _`Just fork and edit it !`: https://github.com/alphalemon/alphalemon-docs
.. _`add a new theme`: add-a-custom-theme-to-alphalemon-cms