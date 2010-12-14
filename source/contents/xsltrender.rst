
XSLT Renderer
=============

An XLST Renderer can be used to customize the rendering of a
Silva Document or any other inheriting content type.

You can create a new `XSLT <http://www.w3.org/TR/xslt>`_ renderer to
transform your public view in a new way:

.. code-block:: python

  from Products.Silva.transform.renderer.xsltrendererbase import XSLTRendererBase
  from silva.core import conf as silvaconf

  class XSLTBlogRenderer(XSLTRendererBase):

      silvaconf.context(Blog)
      silvaconf.title('XSLT Renderer for blogs')
      silvaconf.XSLT('myrules.xslt')

Your XSLT file should be in the same directory as your Python code. In
order to use the XSLT renderer, your content must have an XML
exporter.

.. note::

   - You can define the namespace only on one class. Also the name is
     set at the class level.

   - If you don't set a name, your handler will not be
     registered. That's useful when you want to create a handler which
     is used inside another handler.

   - The base class SilvaBaseHandler in :ref:`xml` provide some
     utilities to help you to create your content, like the handling
     of metadata for example.
