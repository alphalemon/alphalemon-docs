How to edit blocks
==================

This chapter explains in detail how to manage website contents.

Blocks
------

A **Block** is a container for one kind of content. A block can contain a simple
content like an hypertext, an image, a link but it can contain more articulated kind
of contents, like a button, a carousel, a thumbnail from Twitter Bootstrap
or a content to add the Facebook like button to your website.

A Block come always with an editor able to manage the handled content. Usually editors are
rendered inside a popover placed under the block to edit, except hypertext contents, which
are directly managed on the page, using an inline editor.

.. image:: //bundles/alphalemonwebsite/media/manual/img-5.jpg

Here you can see the editor for a Twitter Boostrap button.

The block you are editing changes to blue but the blocks menu remains active, so
you can always change the editor just clicking another block.

.. image:: //bundles/alphalemonwebsite/media/manual/img-6.jpg


Interact with blocks
--------------------

To start editing a page, you must click the **Edit** button. This action turns on the
editable blocks, which are highlighted by a red rectangle that overlays the active block,
when the mouse is placed over it.

On the right bottom corner of this highlighter square, there is a small toolbar
which contains three icons:

1. Plus icon adds a new block under the active one
2. Cross icon moves the block
3. Trash icon removes the active block

.. image:: //bundles/alphalemonwebsite/media/manual/img-7.jpg
    
.. note::

    The move function is not yet implemented
    
Add a block
^^^^^^^^^^^

To add a new block, you must highlight it and then click on the **plus icon** placed 
into the small toolbar attached to the block highlighter.

A panel, which contains all the available blocks, opens over the plus icon. Blocks are listed 
in groups. AlphaLemon CMS comes with several base blocks which are grouped under the
**Default** group, while other listed blocks could have been added by thirdy-part themes
or could have been required directly from the application

.. image:: //bundles/alphalemonwebsite/media/manual/img-8.jpg

When you add an addictional App-Block to AlphaLemon CMS, this is displayed under the 
group it had been assigned by the block's developer.

To add a block to the page simply click over it.

.. image:: //bundles/alphalemonwebsite/media/manual/img-9.jpg

here a new hypertext content block has been added.

Remove a block
^^^^^^^^^^^^^^

To remove a block you must highlight it and then click on the **thrash icon** placed 
into the small toolbar attached to the block highlighter.

.. image:: //bundles/alphalemonwebsite/media/manual/img-10.jpg

You will be promped to confirm the operation.

Edit a block
^^^^^^^^^^^^

To start editing the block's content, you must highlight a block and then click into
its area to open the editor.

In nearly every AlphaLemon CMS blocks have a editor that opens into a popover placed 
under the active block, but the default hypertext editor let you edit blocks inline 
on the page.

.. image:: //bundles/alphalemonwebsite/media/manual/img-11.jpg

Each editor is different than the others, so you may have a form where you can enter the
attributes for an image, you may have a single textarea where to enter a script from
an external website which will insert an embedded video to your page, you may have a 
list of images and a form to change their attributes as for the Twitter bootstrap carousel.

All popover editors provide a **Save** button placed at the bottom right of the popover 
itself. This button saves the content to the database. For hypertext inline editors 
the save button is placed inside the editor's toolbar.

Sometimes it could happen that all the blocks placed on a slot are removed. In this 
case AlphaLemon CMS adds a placeholder that explains this situation.

This placeholder works as a normal block with the only difference that it is not editable.

.. image:: //bundles/alphalemonwebsite/media/manual/img-12.jpg

Included blocks
^^^^^^^^^^^^^^^

An included block is a block that contains two or more basic blocks which are editable 
one by one and the includer block may be editable too.

A perfect example to explain this kind of block, is the **BootbusinessThumbnail** App-Block.
It is a gray bordered container that includes an image and a hypertext.

.. image:: //bundles/alphalemonwebsite/media/manual/img-13.jpg

You can change the image

.. image:: //bundles/alphalemonwebsite/media/manual/img-14.jpg

modify the hypertext to describe the image 

.. image:: //bundles/alphalemonwebsite/media/manual/img-15.jpg

and change the size of the container, editing the container block.

.. image:: //bundles/alphalemonwebsite/media/manual/img-16.jpg

.. note::

    A slot which contains an included block, can accept only a single block. For example 
    you are not allowed to add a Twitter Bootstrap button, or any other kind of blocks 
    under the hypertext included block.
    
List of blocks
^^^^^^^^^^^^^^

A List of blocks is a particular block which can contain singles and/or included blocks
and renders them in a horizontal or vertical row.

When these blocks are edited, each child block gets two icons placed in the bottom right
corner of the block itself. 

Plus icon adds another block next the one you clicked, thrash icon removes the block.

A perfect example is the **BootbusinessThumbnailsList** which displays one or more
**BootbusinessThumbnail** blocks in a horizontal row.

.. image:: //bundles/alphalemonwebsite/media/manual/img-17.jpg

This block is designed to add always a BootbusinessThumbnail: this means that, when you
click the add button a new thumbnail is added.

Despite of that, the Menu Block has another behavior when you click the add button: 
it let you choose the block you want to add, from the Blocks adder panel.


Add a new block type to your application
----------------------------------------

Blocks could be added to your application in two ways:

1. Create a custom block
2. Add an existing block in your composer.json file

To create a custom block, you should read the `dedicated tutorial`_, while to add an
existing block to your application using composer, you must follow the instructions 
provided by each thirdy-part block.


.. class:: fork-and-edit

Found a typo ? Something is wrong in this documentation ? `Just fork and edit it !`_

.. _`Just fork and edit it !`: https://github.com/alphalemon/alphalemon-docs
.. _`dedicated tutorial` : add-a-new-block-app-to-alphalemon-cms
