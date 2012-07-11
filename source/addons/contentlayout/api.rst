
Content Layout Application Programming Interface
================================================

.. contents::

Design
------

A design is effectively the template of a
:py:interface:`~silva.core.contentlayout.interfaces.IPage`. It follow
this simple API:

.. autointerface:: silva.core.contentlayout.interfaces.IDesign

Slots are defined to be used on a design. They follow this API:

.. autointerface:: silva.core.contentlayout.interfaces.ISlot

Slots contained themselves blocks. They have no API:

.. autointerface:: silva.core.contentlayout.interfaces.IBlock

Blocks can be restricted into Slots with the help restrictions:

.. autointerface:: silva.core.contentlayout.interfaces.ISlotRestriction

Different type of restrictions are implemented, depending of the type
of blocks included in the slots.

Page contents
-------------

A :py:interface:`~silva.core.interfaces.content.IVersionedContent`
that have a version using the page feature, or a
:py:interface:`~silva.core.interfaces.content.INonPublishable` must
implement the following interface in order to be able to use the page
editor.

.. autointerface:: silva.core.contentlayout.interfaces.IPageAware

A :py:interface:`~silva.core.interfaces.content.IVersion` or
:py:interface:`~silva.core.interfaces.content.INonPublishable` that
directly uses the page editor must implement the following interface.

.. autointerface:: silva.core.contentlayout.interfaces.IPage

This implementation can reuse the following class:

.. class:: silva.core.contentlayout.designs.design.DesignAccessors

   Mixin class used to implement
   :py:interface:`~silva.core.contentlayout.interfaces.IPage`.

Block features
--------------

Every :py:interface:`~silva.core.contentlayout.interfaces.IBlock` have
a controller, that is used to manage and render it:

.. autointerface:: silva.core.contentlayout.interfaces.IBlockController

A :py:interface:`~silva.core.contentlayout.interfaces.IBlock` can be
added in a couple of different scenarios. This is described with the
help of a list of configurations. Each configuration will be listed to
the user and add the block differently:

.. autointerface:: silva.core.contentlayout.interfaces.IBlockConfigurations

.. autointerface:: silva.core.contentlayout.interfaces.IBlockConfiguration


Model contents
--------------

A model is a versioned content defined in :term:`SMI` that can be used
as a design for an another page or model.

.. autointerface:: silva.core.contentlayout.interfaces.IPageModel

.. autointerface:: silva.core.contentlayout.interfaces.IPageModelVersion


Events
------

An :term:`Zope event` is triggered when a template is associated, or
unassociated with a model.

.. autoevent:: silva.core.contentlayout.interfaces.IDesignEvent

.. autoevent:: silva.core.contentlayout.interfaces.IDesignAssociatedEvent

.. autoevent:: silva.core.contentlayout.interfaces.IDesignDeassociatedEvent

