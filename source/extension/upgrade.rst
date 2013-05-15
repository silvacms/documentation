
Handling upgrades
-----------------

You might want to perform some modification on existing content stored
in ZODB when a new version of Silva or your Silva extension is
installed. This can be done with the help of an upgrade step.


An upgrade step can be used to upgrade content between two versions of
Silva. The upgrade method of the upgrader will be called against each
content of the given content type. Here, a sample to upgrade ``Silva
Document`` and ``Silva Link`` contents to Silva ``2.1``:

.. code-block:: python
   :linenos:

   from Products.Silva.upgrade import BaseUpgrader, AnyMetaType

   class MyUpgrade(BaseUpgrader):

       def upgrade(self, obj):
           """You can upgrade your content in this function.
           """
           pass

   myUpgradeForDocument = MyUpgrade(2.1, 'Silva Document') # This register the step for Silva Document
   myUpgradeForLink = MyUpgrade(2.1, 'Silva Link') # This register the step for Silva Link


``AnyMetaType`` can be used to declare that the step would be run
against all the contents, whatever their meta types.
