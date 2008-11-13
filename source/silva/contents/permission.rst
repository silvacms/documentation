Available Permissions
=====================

This page list all interesting permissions available in Silva to
develop new extensions. This is Zope 3 permission. You can use them
inside all rendering components, with ``silvaconf.require`` like this:

.. code-block:: python

   from silva.core.views import views as silvaviews
   from silva.core import conf as silvaconf

   class MyView(silvaviews.View):

       silvaconf.require('zope2.View')

Permissions:

``zope2.Public``

    Everybody have this permission

``zope2.AccessContentsInformation``

    Zope 2 access to contents information.

``zope2.View``

    Zope 2 view document.

``silva.ReadSilvaContent``

    View a Silva object.

``silva.ChangeSilvaContent``

    Modify a Silva object.

``silva.ApproveSilvaContent``

    Approve a Silva object.

``silva.ChangeSilvaAccess``

    Modify access rights to documents and folders.

``zope2.ManageProperties``

    Manage information on Zope objects.

``zope2.ManageUsers``

    Manager users in the zope site (add/remove new ones).

``zope2.ViewManagementScreens``

    View Silva and Zope managements screens and options.

