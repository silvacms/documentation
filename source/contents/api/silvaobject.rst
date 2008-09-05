
Silva Object
============

``SilvaObject`` is the base class for all Silva content objects.

.. module:: Products.Silva.SilvaObject

.. class:: SilvaObject

   Title information:

   .. function:: get_title()

      Return the title of the object.

   .. function:: get_short_title()

      Return the short title of the object.

   .. function:: get_title_or_id()

      Return the title, and if not set return the id of the object
      instead.

   Date information:

   .. function:: get_creation_datetime()

      Return the date of creation of the object.

   .. function:: get_modification_datetime()

      Return the last modification date of the object.

   Version management:

   .. function:: get_editable()

      Return the editable object corresponding to that content.

   .. function:: get_previewable()

      Return the previewable (new version not yet publish but maybe
      approved) object corresponding to that content.

   .. function:: get_viewable()

      Return the viewable (public version) object corresponding to
      that content.


