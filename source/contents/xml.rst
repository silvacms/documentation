.. _xml:

XML Import / Export
===================

XML Import
----------

Silva makes it possible to import content from an XML file. Before it
is possible to do this you must write an XML importer. Below is a
simple example to load the simple content type 'MyContent'.

.. code-block:: python

  from Products.Silva.silvaxml.xmlimport import SilvaBaseHandler
  from silva.core import conf as silvaconf

  # You need a namespace definition for your XML file
  NS_URI = 'http://mydomain/namespace/mysilvaproject'
  # You tell that's all importers in this Python module are going to use this namespace
  silvaconf.namespace(NS_URI)

  class MyContentHandler(SilvaBaseHandler):

      silvaconf.name('content')            # Name of the tag representing your content.

      def startElementNS(self, name, qname, attrs):
          if name == (NS_URI, 'content'):
              # Get the folder where the element should be, and create the content.
              folder = self.parent()
              content_id = self.generateOrReplaceId(attrs[(None,'id')])
              content_title = attrs[(None, 'title')]
              folder.manage_addProduct['Blog'].manage_addMyContent(content_id, content_title)

              # This is the new current element, them a handler will
              # process sub-tag it will get this element with self.parent()
              self.setResult(getattr(folder, content_id))

      def endElementNS(self, name, qname):
          pass                             # Do nothing here.

This handler will be able to load content from the following XML file:

.. code-block:: xml

  <?xml version="1.0" encoding="UTF-8" ?>
  <silva xmlns="http://infrae.com/ns/silva"
         xmlns:mysilvaproject="http://mydomain/namespace/mysilvaproject">
     <mysilvaproject:content id="xml_content" title="XML Content" />
  </silva>