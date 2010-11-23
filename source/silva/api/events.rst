Silva events
============

When action in Silva are done Zope events are triggered. You can
listen to them, and execute some code when they are triggered.

Zope events
-----------

All of the basic Zope events are used:

- :py:interface:`zope.lifecycleevent.interfaces.IObjectCreatedEvent`,

- :py:interface:`zope.lifecycleevent.interfaces.IObjectModifiedEvent`,

- :py:interface:`zope.lifecycleevent.interfaces.IObjectCopiedEvent`,

- :py:interface:`zope.container.interfaces.IObjectAddedEvent`,

- :py:interface:`zope.container.interfaces.IObjectRemovedEvent`,

- :py:interface:`zope.container.interfaces.IContainerModifiedEvent`,


With, :term:`Grok`, you can *listen* for those events and execute some
Python code. For instance, if you wish do something when a content is
added to a folder:

.. code-block:: python

   from five import grok
   from silva.core.interfaces import IContainer
   from zope.container.interfaces import IContainerModifiedEvent

   @grok.subscribe(IContainer, IContainerModifiedEvent)
   def container_modified(container, event):
       # Do some updated on container
       pass

.. warning::

   When a container is moved, you will received a moved event for the
   container and all content contained within it, recursively. If you
   wish to be sure that the event you receive is really about the
   container which moved, you can do the following test:

   .. code-block:: python

      if container == event.object:
          # Really container
      else:
          # A parent of the container moved.
