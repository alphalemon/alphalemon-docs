Translate AlphaLemon CMS in your language
=========================================

This document will explain in detail how to translate AlphaLemon CMS in your language.

.. note::

    Before go, you need to knows that you are required to fork **AlphaLemon CMS** bundle 
    to translate AlphaLemon: learn how at http://alphalemon,com/how-to-get-alphalemon-cms-source-code-and-bundle-structure

AlphaLemon CMS uses Symfony2's Translation component to manage the interface translation.
Catalogues live under the standard AlphaLemonCmsBundle's **Resources/translations** 
folder.

Each catalogue is written as **xliff** file and its name follows the Symfony2 notation,
for a bundle:

.. code:: text

    AlphaLemonCmsBundle.[locale].xliff

Translate AlphaLemon CMS in your language
-----------------------------------------

To start translating AlphaLemon CMS, simply copy the **AlphaLemonCmsBundle.en.xliff** and the
**messages.en.xliff** catalogues and rename them with the locale you want to translate.

Let's assume you want to translate AlphaLemon CMS in Italian: you need to know the i18n
Italian notation, which is **"it"**. When you know this, you can start. 

.. note::

    AlphaLemon CMS default language is English, and while it is not mandatory, it is 
    better to start a new translation from this one.
    
Catalogues are written using xliff format a derivation of xml markup language. They are 
structured as follows:

.. code:: xml

    <?xml version="1.0"?>
    <xliff version="1.2" xmlns="urn:oasis:names:tc:xliff:document:1.2">
        <file source-language="en" datatype="plaintext" original="file.ext">
        <body>
            <trans-unit id="1">
                <source>The block has been successfully removed</source>
                <target>The block has been successfully removed</target>
            </trans-unit>
            <trans-unit id="2">
                <source>The block has not been removed</source>
                <target>The block has not been removed</target>
            </trans-unit>
            
            [ ... ]       
        </body>
        </file>
    </xliff>
    
What you must do is to replace each target section with the translation of the message in your
language:

.. code:: xml

    <?xml version="1.0"?>
    <xliff version="1.2" xmlns="urn:oasis:names:tc:xliff:document:1.2">
        <file source-language="en" datatype="plaintext" original="file.ext">
        <body>
            <trans-unit id="1">
                <source>The block has been successfully removed</source>
                <target>Il blocco è stato correttamente eliminato</target>
            </trans-unit>
            <trans-unit id="2">
                <source>The block has not been removed</source>
                <target>Il blocco non è stato eliminato</target>
            </trans-unit>
            
            [ ... ]       
        </body>
        </file>
    </xliff>
    
Client-side translations
------------------------

Although the most of internationalization messages lives at server-side and are managed
by Symfony2 catalogues, there are some other messages that live at client-side and
cannot be included with these catalogues.

Client-side catalogues are saved at **Resources/public/js/lang** and are named just only
with **locale** plus the extension, so English catalogue will be **en.js**, Italian
will be **it.js** and so on.

To translate the English catalogue in Italian, just copy the **en.js** into **it.js**. 
These catalogues are very simple, in fact there is just declare one javascript object 
which contains all the messages:

.. code:: javascript

    var lang = {
        'A %type% block is not rendered when the editor is active' : 'A %type% block is not rendered when the editor is active',
        'This operation is not allowed when you are editing the contents' : 'This operation is not allowed when you are editing the contents',
        
         [...]
    };
    
To translate the messages, just substitute the messages placed at the right of each colon,
as follows:

.. code:: javascript

    var lang = {
        'A %type% block is not rendered when the editor is active' : 'Un blocco di tipo %type% non viene renderizzato quando l\'editor è attivo',
        'This operation is not allowed when you are editing the contents' : 'Non puoi effettuare questa operazione mentre stai editanto i contenuti',
    
         [...]
    };


.. class:: fork-and-edit

Found a typo ? Something is wrong in this documentation ? `Just fork and edit it !`_

.. _`Just fork and edit it !`: https://github.com/alphalemon/alphalemon-docs