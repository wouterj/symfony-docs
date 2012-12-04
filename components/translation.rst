.. index::
    single: Translation
    single: Components; Translation

The Translation Component
=========================

    The Translation component provides functions to easilly translate your
    application.

Installation
------------

You can install the component in many different ways:

* Use the official Git repository (https://github.com/symfony/Translation);
* :doc:`Install it via Composer</components/using_components>` (``symfony/translation`` on `Packagist`_).

Usage
-----

The :class:`Symfony\\Component\\Translation\\Translator` class provides tools 
for loading translation files and generating translated strings from these, 
including support for pluralization::

    use Symfony\Component\Translation\Translator;
    use Symfony\Component\Translation\MessageSelector;
    use Symfony\Component\Translation\Loader\ArrayLoader;

    $translator = new Translator('fr_FR', new MessageSelector());
    $translator->addLoader('array', new ArrayLoader());
    $translator->addResource('array', array(
        'Hello World!' => 'Bonjour',
    ), 'fr_FR');
    
    echo $translator->trans('Hello World!');

Message Catalogues
------------------

The messages are stored in message catalogues inside the ``Translator``
class. A Message Catalogue is like a dictionary of translations for a specific 
locale.

Loading catalogues
~~~~~~~~~~~~~~~~~~

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

If you use another loader than the ``ArrayLoader`` you should require the
:doc:`Config component</components/config/index>` too.

At first, you should add a loader to the ``Translator``::

    // ...
    $translator->addLoader('array', new ArrayLoader());

The first argument is the key to which we can refer the loader in this class
and the second argument is an instance of the loader itself. After this, you
can add your resources using the correct loader::

    // ...
    $translator->addResource('array', array(
        'Hello World!' => 'Bonjour',
    ), 'fr_FR');

The first argument is the key of the loader we use, the second argument is the
resource (which is an array in this case) and the tirth argument is the locale.

.. note::

    If you use a ``*FileLoader`` class to load your resources, the
    ``addResources`` method looks like this::

        // ...
        $translator->addLoader('yaml', new YamlFileLoader());
        $translator->addResource('yaml', 'path/to/messages.fr.yml', 'fr_FR');



.. _Packagist: https://packagist.org/packages/symfony/translation
