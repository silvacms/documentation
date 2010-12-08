.. _xml:

XML Import and Export
=====================

If you want to have your content, or a folder containing your content
to work with the XML import and export feature of Silva, you need to
write an XML importer and an XML exporter.


.. _xml-importer:

XML Importer
------------

Silva makes possible to import content from an XML file. To enable this
feature, you need to write an XML importer. Below is a simple example
to handle import the simple content type *Blog*:

.. code-block:: python
   :linenos:

   from Products.Silva.silvaxml.xmlimport import SilvaBaseHandler
   from silva.core import conf as silvaconf

   NS_URI = 'http://mydomain/namespace/blog'
   silvaconf.namespace(NS_URI)

   class BlogImporter(SilvaBaseHandler):
       silvaconf.name('blog')

       def startElementNS(self, name, qname, attrs):
           if name == (NS_URI, 'blog'):
               # Get the folder where the element should be, and create the content.
               folder = self.parent()
               blog_id = self.generateOrReplaceId(attrs[(None,'id')])
               factory = folder.manage_addProduct['silva.app.blog']
               factory.manage_addBlog(blog_id, '')

               self.setResultId(content_id)

       def endElementNS(self, name, qname):
           if name == (NS_URI, 'blog'):
               self.storeMetadata()
               self.notifyImport()


This importer will be able to create a blog from the following XML file:

.. code-block:: xml

  <?xml version="1.0" encoding="UTF-8" ?>
  <silva xmlns="http://infrae.com/namespace/silva"
         xmlns:silva-content="http://infrae.com/namespace/metadata/silva-content"
         xmlns:silva-blog="http://mydomain/namespace/blog">
    <silva-blog:blog id="technical_blog">
      <metadata>
        <set id="silva-content">
          <silva-content:maintitle>
            Technical Blog.
          </silva-content:maintitle>
        </set>
      </metadata>
    </silva-blog:blog>
  </silva>


XML Exporter
------------

To create the previous XML file shown in the :ref:`xml-importer` you
need to write an XML exporter:

.. code-block:: python
   :linenos:

   from Products.Silva.silvaxml.xmlexport import SilvaBaseProducer
   from five import grok
   from zope.interface import Interface

   class BlogExporter(SilvaBaseProducer):
         grok.adapts(Blog, Interface)

         def sax(self):
             self.startElement('blog', {'id': self.context.getId()})
             self.metadata()
             self.endElement('blog')
