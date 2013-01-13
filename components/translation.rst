.. index::
    single: Translation
    single: Components; Translation

The Translation Component
=========================

    The Translation component provides tools to internationalize your
    application.

Installation
------------

You can install the component in many different ways:

* Use the official Git repository (https://github.com/symfony/Translation);
* :doc:`Install it via Composer</components/using_components>` (``symfony/translation`` on `Packagist`_).

Usage
-----

The :class:`Symfony\\Component\\Translation\\Translator` class is the main
entry point for translating your application. You will load message catalogues
in it and you will translate strings that match one of these messages. The
``Translator`` class can even handle difficult messages, like pluralization
ones.

A simpelst code looks like this::

    use Symfony\Component\Translation\Translator;
    use Symfony\Component\Translation\MessageSelector;
    use Symfony\Component\Translation\Loader\ArrayLoader;

    $translator = new Translator('fr_FR', new MessageSelector());
    $translator->addLoader('array', new ArrayLoader());
    $translator->addResource('array', array(
        'Hello World!' => 'Bonjour',
    ), 'fr_FR');
    
    echo $translator->trans('Hello World!');

We will explain this code in the next sections.

Instantiate the ``Translator``
------------------------------

We need to instantiate the ``Translator`` before we can use him. The
constructor of the ``Translator`` requires two arguments: The current locale
and a message selector. The current locale is the one you used to use in the
translations in your applications, but you can change this per translation.
The message selector is a class which determines how we handle pluralization,
we will come back to this later in the document just put a new instance of
:class:`Symfony\\Component\\Translation\\MessageSelector` here.

.. code-block:: php

    use Symfony\Component\Translation\Translator;
    use Symfony\Component\Translation\MessageSelector;

    $translator = new Translator('fr_FR', new MessageSelector());

Loading Message Catalogues
--------------------------

A Message Catalogue is like a dictionary of translations for a specific
locale. The ``Translator`` can handle multiple message catalogues for the same
locale.

Before the ``Translator`` can begin translating string, we need to load
message catalogues.

Adding Loaders
~~~~~~~~~~~~~~

The Translation component uses Loader classes to load catalogues. You can load
multiple resources for the same locale, it will be combined into one
catalogue.

The component comes with some default Loaders and you can create your own
Loader too. The default loaders are:

* :class:`Symfony\\Component\\Translation\\Loader\\ArrayLoader` - to load
  catalogues from PHP arrays.
* :class:`Symfony\\Component\\Translation\\Loader\\CsvFileLoader` - to load
  catalogues from csv files.
* :class:`Symfony\\Component\\Translation\\Loader\\PhpFileLoader` - to load
  catalogues from php files.
* :class:`Symfony\\Component\\Translation\\Loader\\XliffFileLoader` - to load
  catalogues from xliff (xml) files.
* :class:`Symfony\\Component\\Translation\\Loader\\YamlFileLoader` - to load
  catalogues from yaml files (requires the :doc:`Yaml component</components/yaml>`).

All loaders, except the ``ArrayLoader``, requires :doc:`the Config component</components/config/index>`

At first, you should add a loader to the ``Translator``::

    use Symfony\Component\Translation\Loader\YamlFileLoader;

    // ...
    $translator->addLoader('yaml', new YamlFileLoader());

The first argument is the key to which we can refer the loader in the translator
and the second argument is an instance of the loader itself. You will use the
key to determine which loader to use while loading the message catalogues

Loading catalogues
~~~~~~~~~~~~~~~~~~

Now you have added the loader you want to use, you can begin loading the
message catalogues. You will do this with the
:method:`Symfony\\Component\\Translation\\Translator::addResource` method.
This method requires 3 arguments: The first one is the key of the loader
(which you added in the section above), the second one is the resource (a
filename or an array if you use the ``ArrayLoader``) and the tirth one the
locale for this message catalogues.

Loading files
"""""""""""""

We begin by creating a ``message`` file:

.. configuration-block::

    .. code-block:: yaml

        # translations/messages.fr.yml
        Symfony2 is great: J'aime Symfony2

    .. code-block:: xml

        <!-- translations/messages.fr.xliff -->
        <?xml version="1.0"?>
        <xliff version="1.2" xmlns="urn:oasis:names:tc:xliff:document:1.2">
            <file source-language="en" datatype="plaintext" original="file.ext">
                <body>
                    <trans-unit id="1">
                        <source>Symfony2 is great</source>
                        <target>J'aime Symfony2</target>
                    </trans-unit>
                </body>
            </file>
        </xliff>

    .. code-block:: php

        // translations/messages.fr.php
        return array(
            'Symfony2 is great' => 'J\'aime Symfony2',
        );

    .. code-block:: csv

        ; translations/messages.fr.csv
        "Symfony2 is great"; "J'aime Symfony2"

Now we can load them using the loader we added::

    // ...
    $translator->addResource('yaml', 'translations/messages.fr.yml', 'fr_FR');

This will load our message catalogue and at it to the ``fr_FR`` locale.

Loading Arrays
""""""""""""""

If you need something simple (for unit tests) or you won't require another
component, you can use PHP arrays.

You can load them with the ``ArrayLoader`` and the second argument will be the
array to load, instead of the file to load::

    // ...
    $translator->addResource('array', array(
        'Symfony2 is great' => 'J\'aime Symfony2',
    ), 'fr_FR');

Translating strings
-------------------

Translation is done with the :method:`Symfony\\Component\\Translation\\Translator::trans`
method::

    // ...
    echo $translator->trans('Symfony2 is great');

The ``Translator`` will now search for the string ``'Symfony2 is great'`` in
the current locale (set in the constructor, ``fr_FR`` in this example). He
will return the translated string if he can find something and he will return
the input string if he cannot find something.

Fallback locale
~~~~~~~~~~~~~~~

You can set a fallback locale to fallback to if the translator cannot find
something. You can set it with the
:method:`Symfony\\Component\Translation\Translator::setFallbackLocale` method.
In our example, we can add a fallback locale for ``en_GB``::

    // ...
    $translator->setFallbackLocale('en_GB');

We need to load the ``en_GB`` message catalogues and it will translate
something in English if a French translation is not found.

Translate strings
-----------------

After you have loaded your Message Catalogues, you can begin to translate your
strings. This is done with the
:method:`Symfony\\Component\\Translation\\Translator::trans` method::

    // ...
    $translator->addResource('array', array(
        'Hello World!' => 'Bonjour',
    ), 'fr_FR');
    $translator->addResource('array', array(
        'Hello World!' => 'Hello World',
    ), 'en_GB');

    echo $translator->trans('Hello World!');
    // >> 'Bonjour'

By default, the ``trans`` method uses the locale that is set in the
constructor of the ``Translator``. If you want to translate another locale,
you can change that by setting the fourth argument to the locale::

    // ...
    echo $translator->trans('Hello World!', array(), 'messages', 'en_GB');
    // >> 'Hello World!'

Parameters in strings
---------------------

You can also have parameters in your string. For instance, if you have a greet
message on the users dashboard, you have a parameter ``%name%``. You can use
that in your string::

    // ...
    $translator->addResource('array', array(
        'Hello User' => 'Bonjour %name%!',
    ), 'fr_FR');
    $translator->addResource('array', array(
        'Hello User' => 'Hello %name%!',
    ), 'en_BG');

You can set the value of that parameter in the second argument of the ``trans`` method::

    // ...
    echo $translator->trans('Hello User', array('%name%' => 'Fabian'));
    // >> 'Bonjour Fabian!'

.. note::

    It may become usefull to use keyword messages (like `greet.user`) here.
    Read more about that here: 
    ":ref:`Using Real or Keyword Messages<real-keyword-messages>`"

.. _Packagist: https://packagist.org/packages/symfony/translation
