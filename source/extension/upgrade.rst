
.. _handling-upgrades:

Handling upgrades between Silva versions
----------------------------------------

You might want to perform some modification on existing content types
and services stored in ZODB when a Silva site is upgraded to a newer
version of Silva. This can be done with the help of an upgrade step
that will executed during upgrades, :ref:`upgrading-your-installation`.

For instance, to upgrade a ``Silva Blog Item`` content to Silva
3.0 alpha 1 by adding a magical attribute:

.. code-block:: python
   :linenos:

   from silva.core.upgrade.upgrade import BaseUpgrader, AnyMetaType

   class MyUpgrader(BaseUpgrader):

       def upgrade(self, content):
           """You can upgrade your content in this function. It must return
           the upgraded content.
           """
           content._magic_attribute = 42
           return content

   upgrade_items = MyUpgrader('3.0a1', 'Silva Blog Item')


- line 3 defines the upgrade step by inheriting from the base class
  ``BaseUpgrader``,

- line 5 to 10 defines the upgrade method. It will automatically be
  called for every content matching the given meta type declared on
  line 12. It must return the upgraded content, even if it is the same
  than the original one,

- line 12 register the upgrader to be executed then the upgrade
  process reach the version ``3.0a1`` for the content that have a meta
  type ``Silva Blog Item``.

``AnyMetaType`` can be used to declare that the step would be run
against all the ZODB content, whatever their meta types is (including
Silva content and not Silva content).

If your upgrade accross multiple version in one pass, upgraders will
be sorted so that the one registered for lower Silva version are
executed before before the one registered for higher Silva versions.

For your upgrade step to properly work, your Silva extension must
include ``silva.core.upgrade`` in its ``configure.zcml`` before
calling Grok.
