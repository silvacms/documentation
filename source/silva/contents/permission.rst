.. _available-permissions:

Available Permissions
=====================

This page list all permissions available in Silva for developing new
extensions. You can use them inside all rendering components, with
``silvaconf.require`` like this:

.. code-block:: python

   from silva.core.views import views as silvaviews
   from silva.core import conf as silvaconf

   class MyView(silvaviews.View):
       silvaconf.require('zope2.View')

Permissions reference
---------------------

``zope2.Public``
    Everybody has this permission.

``zope2.AccessContentsInformation``
    Zope 2 permission needed to access a content information.

``zope2.View``
    Zope 2 permission needed to view a content.

``silva.ReadSilvaContent``
    Silva permission needed to view a non-published content.

``silva.ChangeSilvaContent``
    Silva permission needed to modify a Silva content.

``zope2.ManageProperties``
    Zope 2 permission needed to modify Zope objects.

``zope2.DeleteObjects``
    Zope 2 permission needed to delete Zope objects.

``silva.ApproveSilvaContent``
    Silva permission needed to approve and publish a Silva content.

``silva.ManageSilvaContent``
    Silva permission needed to manage content options.

``silva.ChangeSilvaAccess``
    Silva permission needed to modify user access.

``silva.ManageSilvaContentSettings``
    Silva permission needed to manage content presentation options.

``zope2.ManageUsers``
    Zope 2 permission needed to manage users (add/remove new ones).

``zope2.ViewManagementScreens``
    Zope 2 permission needed to view Silva and Zope managements
    screens and advanced options. Only administrators users should have
    this permission.


Default Permission attribution
------------------------------

This table summarize the default attribution of permissions to Silva roles:

+--------------------------------------+---------------+------------+------------+------------+-----------------+-------------+
|                                      | ``Anonymous`` | ``Reader`` | ``Author`` | ``Editor`` | ``ChiefEditor`` | ``Manager`` |
+======================================+===============+============+============+============+=================+=============+
| ``zope2.Public``                     |  X            | X          | X          | X          | X               | X           |
+--------------------------------------+---------------+------------+------------+------------+-----------------+-------------+
| ``zope2.AccessContentsInformation``  |  X            | X          | X          | X          | X               | X           |
+--------------------------------------+---------------+------------+------------+------------+-----------------+-------------+
| ``zope2.View``                       |  X            | X          | X          | X          | X               | X           |
+--------------------------------------+---------------+------------+------------+------------+-----------------+-------------+
| ``silva.ReadSilvaContent``           |               | X          | X          | X          | X               | X           |
+--------------------------------------+---------------+------------+------------+------------+-----------------+-------------+
| ``silva.ChangeSilvaContent``         |               |            | X          | X          | X               | X           |
+--------------------------------------+---------------+------------+------------+------------+-----------------+-------------+
| ``zope2.DeleteObjects``              |               |            | X          | X          | X               | X           |
+--------------------------------------+---------------+------------+------------+------------+-----------------+-------------+
| ``zope2.ManageProperties``           |               |            | X          | X          | X               | X           |
+--------------------------------------+---------------+------------+------------+------------+-----------------+-------------+
| ``silva.ApproveSilvaContent``        |               |            |            | X          | X               | X           |
+--------------------------------------+---------------+------------+------------+------------+-----------------+-------------+
| ``silva.ManageSilvaContent``         |               |            |            | X          | X               | X           |
+--------------------------------------+---------------+------------+------------+------------+-----------------+-------------+
| ``silva.ChangeSilvaAccess``          |               |            |            |            | X               | X           |
+--------------------------------------+---------------+------------+------------+------------+-----------------+-------------+
| ``silva.ManageSilvaContentSettings`` |               |            |            |            | X               | X           |
+--------------------------------------+---------------+------------+------------+------------+-----------------+-------------+
| ``zope2.ManageUsers``                |               |            |            |            |                 | X           |
+--------------------------------------+---------------+------------+------------+------------+-----------------+-------------+
| ``zope2.ViewManagementScreens``      |               |            |            |            |                 | X           |
+--------------------------------------+---------------+------------+------------+------------+-----------------+-------------+


