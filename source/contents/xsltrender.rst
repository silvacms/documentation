
XSLT Renderer
=============

You can create a new XSLT renderer to render your public view that
way:

.. code-block:: python

  from Products.Silva.transform.renderer.xsltrendererbase import XSLTRendererBase
  from silva.core import conf as silvaconf

  class XSLTBlogRenderer(XSLTRendererBase):

      silvaconf.context(Blog)
      silvaconf.title('XSLT Renderer over blogs')
      silvaconf.XSLT(myrules.xslt')


Your XSLT file is searched from the directory where your Python code
is. To be able to use that renderer, your content must have a XML
exporter.

.. note::

   - You can define the namespace only on one class, by setting it at
     the class level like for the name.

   - If you don't set a name, your handler will not be
     registered. That's useful when you want to create handler which
     have only a meaning inside one other.

   - The base class ``SilvaBaseHandler`` provide some utilities to
     help you to create your content, like the handling of metadata
     for example.
