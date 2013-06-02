Add a custom route outside the CMS
==================================
AlphaLemon CMS automatically creates a Symfony2 route for each page that you add to your
website and uses a permalink to describe the page link. 

In addition, each route can be translated for any language used on your website.

This approach could be a limit when you want to add a page managed by an action from
a custom controller. 

Since AlphaLemon CMS Release Candidate 2, this gap is covered in an elegant way. In 
fact when you deploy your website, AlphaLemon CMS creates the base views for each theme's
template, then you just need to extend one of them and inject additional data 
in a custom action.

The project
-----------
We are going to add a custom route to display the details related to a generic
product on a web page.

We will fetch this information from a database, using **Doctrine ORM**, to demonstrate how
AlphaLemon CMS, which is powered by **Propel ORM**, simply sits beside your website and allows you to
choose the tools you feel most comfortable with, without you having to use something you don't want
to.

The webpage layout will be one of the templates that comes with the **BootbusinessThemeBundle**
and the data fetched from the database will be displayed into some template's blocks.

.. note::

    This tutorial grabs some code from the Symfony2 Doctrine chapter and implements 
    some of the  entities proposed there.
    

Add a new route
---------------
Be sure your deploy bundles **routing.yml** file is imported in the apps **routing.yml**
configuration, otherwise import it as follows:

.. code-block:: text

    # app/config/routing.yml
    _WebSiteBundle:
        resource: "@AlphaLemonWebSiteBundle/Resources/config/routing.yml"

Open the **routing.yml** file under the **Resources/config** folder of your deploy
bundle and add the following route:

.. code-block:: text

    # src/AlphaLemon/WebSiteBundle/Resources/config/routing.yml
    _product:
        pattern: /{_locale}/product/{id}
        defaults: { _controller: AlphaLemonWebSiteBundle:WebSite:product }
        
Add the entity that manages the product
---------------------------------------
Add the **Entity** folder inside your deploy bundle, and inside it create the **Product.php**
file using the following code:

.. code-block:: php

    // src/AlphaLemon/WebSiteBundle/Entity/Product.php
    namespace AlphaLemon\WebSiteBundle\Entity;

    use Doctrine\ORM\Mapping as ORM;

    /**
    * @ORM\Entity
    * @ORM\Table(name="product")
    */
    class Product
    {
        /**
        * @ORM\Id
        * @ORM\Column(type="integer")
        * @ORM\GeneratedValue(strategy="AUTO")
        */
        protected $id;
        /**
        * @ORM\Column(type="string", length=100)
        */
        
        protected $name;
        /**
        * @ORM\Column(type="decimal", scale=2)
        */
        protected $price;
        
        /**
        * @ORM\Column(type="text")
        */
        protected $description;
    }
    
Next, run the following Terminal command to generate the getters and setters:

.. code-block:: text

    php app/console doctrine:generate:entities AlphaLemon/WebSiteBundle/Entity/Product
        
        
Add the action
--------------
Open the ***WebSiteController.php** file and add a new action as set out below:

.. code-block:: php

    // src/AlphaLemon/WebSiteBundle/Controller/WebSiteController.php
    class WebSiteController extends FrontendController
    {
        public function productsAction()
        {
            $product = $this->getDoctrine()
                ->getRepository('AlphaLemonWebSiteBundle:Product')
                ->find($id);

            if (!$product) {
                throw $this->createNotFoundException('No product found for id '.$id);
            }
              
            return $this->render('AlphaLemonWebSiteBundle:Product:product.html.twig', array(
                'product' => $product,
                'base_template' => $this->container->getParameter('alpha_lemon_theme_engine.base_template'),
            ));
        }
    }
    
Add the template
----------------
Now add a new **product.html.twig** template under the bundle's views folder:

.. code-block:: jinja

    // src/AlphaLemon/WebSiteBundle/Resources/views/product.html.twig
    {% extends 'AlphaLemonWebSiteBundle:' ~ environment_folder ~ ':' ~ app.request.get('_locale') ~ '/base/empty.html.twig' %}
    
    {% block page_title %}
        <h3>{{ product.getName }}</h3>
    {% endblock %}

    {% block content %}
    <table>
      <tr>
        <td>Description</td>
        <td>{{ product.getDescription }}</td>
      </tr>
      <tr>
        <td>Price</td>
        <td>{{ product.getPrice}}</td>
      </tr>
    </table>
    {% endblock %}
    
**Here is the trick.**

The template extends the **empty.html.twig** base generated template,
and overrides two of its blocks: the **page_title** block, where the product name
is displayed, and the **content** block which contains the product details.

You may have noticed that the **extends** instruction contains the **environment_folder**
variable which handles the current environment. In fact, AlphaLemon CMS can deploy the
website for the stage and/or production environment. In this way the template is "environment
agnostic".

The **environment_folder** has not been declared yet: let's do that now.

Open the controller and change it as follows:

.. code-block:: php

    // src/AlphaLemon/WebSiteBundle/Controller/WebSiteController.php
    class WebSiteController extends FrontendController
    {
        public function productAction($id)
        {
            $product = $this->getDoctrine()
                ->getRepository('AlphaLemonWebSiteBundle:Product')
                ->find($id);

            if (!$product) {
                throw $this->createNotFoundException('No product found for id '.$id);
            }
              
            return $this->render('AlphaLemonWebSiteBundle:Product:product.html.twig', array(
                'product' => $product,
                'base_template' => $this->container->getParameter('alpha_lemon_theme_engine.base_template'),
                'environment_folder' => $this->getEnvironmentFolder(),
            ));
        }
        
        protected function getEnvironmentFolder()
        {
            return strpos($this->container->getParameter('kernel.environment'), 'stage') === false ? $this->container->getParameter('alpha_lemon_theme_engine.deploy.templates_folder') : $this->container->getParameter('alpha_lemon_theme_engine.deploy.stage_templates_folder');
        }
    }
    
The new **getEnvironmentFolder** protected method has been added and called in the
template parameters definition.
        
        
Deploy your website
-------------------
At the moment, the website base templates have not been created yet. So now we are going to
do that.

To deploy the website for the stage environment simply open the toolbar and click the 
**Deploy stage** button.

.. note::

    Deploying for the stage environment to develop this new page keeps you safe from adding
    something under development, whilst in production.

When AlphaLemon CMS completes the deploying operation, open a new tab in your browser and 
enter in the stage dev environment: **http://localhost/stage_dev.php/en/product/1**

.. note::

    Obviously you will need to have at least one record in your table to see the page correctly rendered,
    otherwise you get an exception.

Conclusion
----------

The result does not look impressive, but this is not the purpose of this tutorial. 

You have now learned how to add a custom route to a website powered by
AlphaLemon CMS and how to manage data from a database, using **Doctrine** ORM instead of 
**Propel**.


.. class:: fork-and-edit

Found a typo? Is something not correct in this documentation? `Just fork and edit it!`_

.. _`Just fork and edit it!`: https://github.com/alphalemon/alphalemon-docs
.. _`Add a new App-Block`: http://www.alphalemon.com/add-a-new-block-app-to-alphalemon-cms
