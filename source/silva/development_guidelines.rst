Development Guidelines for Silva
********************************

.. contents::

.. note:: That documentation is intended for Silva core
   developers. But as development practice, this documentation may be
   interesting for other people as well.

Introduction
============

You can develop on Silva 2.0, 2.1 and the Silva 2.2 trunk. These
versions are consider as *current* and will be from hereafter referred
to as such.

To work on Silva we suggest setting up Silva using `buildout coming
from SVN <https://svn.infrae.com/buildout/silva/>`_.

Why do we use buildout?

- Installing Silva using this buildout procedure give us the
  opportunity to release more often instead of once every year and
  half.

- Our procedure follows the rule that states: *until your code is
  delivered, it's useless*. We like to write useful code, so people
  can use it.

- We can release more alpha and beta versions.

There are three kinds of buildouts
----------------------------------

1. The Trunk buildout. This is the lastest version of Silva.

2. Branch buildouts. These are older Silva versions (2.0 and 2.1).

3. Tag buildouts. Tags are released versions of Silva. Tags *must
   never be changed!*. If there is an issue in a tag you should fix
   the issue in the corresponding branch of Silva. For example if you
   to fix something in a 2.1 tag, you should fix it on the 2.1 branch,
   then release a new tag.

Silva components
----------------

Silva is made up of three kinds of components.

1. Extensions that are direct dependencies of Silva, such as
   Formulator, ParsedXML, or Annotations. These extensions should work
   for all current versions of Silva.

   If you work on any of those extensions, be sure to check that:

   1. Your changes work with all current versions of Silva by
      setting up an environement and running the tests.

   2. When you tag that extension, be sure to update all buildouts for
   *all current branches* of Silva to use the newly tagged extension.

2. Extensions that make up the core of Silva.

3. Extensions that add features to Silva and may work on more than one
   current version of Silva:

   - If an extension does not work for all current Silva versions, you
     need to port your changes to the more recent version of that
     extension, following the same pattern as you would for developing
     Silva itself.

   - If an extension works for more than one Silva version, you need
     to check that the extension still works for all others versions
     besides the Silva version you developed your feature on. If this
     is not the case, you must create branches of the extension for
     those versions of Silva.

All extensions have a trunk version, this should work with the Silva
trunk. The extension may also have branches for other Silva versions,
and tags for released versions. Like for buildouts, if you want to fix
something on a tag, do it on the corresponding branch and make a new
tag.

.. warning::

   All buildouts should refer to tags of Silva components. To add a
   new feature to a component, add a new tag of that component to the
   current buildouts versions.

Porting rules
=============

- If you work on an feature or bug fix on Silva 2.0 you *must* do the
  same for Silva 2.1 and the Silva trunk. Also if possible provide a
  unit test to prevent regression errors.

- If you work on a feature or bug fix for Silva 2.1 you *must* provide
  the same for the Silva trunk. Also if possible provide a unit test
  to prevent regression errors.

- If you work on a bug fix for the Silva trunk, you *must* provide the
  same on Silva 2.1.

Creating new branches
=====================

There are only two cases where you must create new branches:

- You want to have a version of an extension which still works for a
  previous version of Silva (backward incompatible changes): in this
  case the branch should be called ``extensionname-version``, for
  example ``SilvaLayout-1.4``. These versions mainly exist for us to
  maintain older versions of Silva, such as a bug fix. These branches
  are meant to be permanently stored in SVN.

- You want to try an experiment, and your are not sure about the end
  results. In situations like this you should create a branch
  resembling this naming standard: ``yourname-why``, for example
  ``sylvain-database-distribution``.

  These kinds of branches are likely to be merged as soon as they work
  correctly. An experimently branch a year or older will be because it
  would too difficult to merge to the trunk. If your are not happy
  with the outcome of the experiment **delete the branch**.

  .. warning::

     After merging a branch it **should be deleted**.

.. _setup-dev-env:

Setting up a development environment
====================================

Check out a fresh buildout for your development, and bootstrap it:

.. code-block:: sh

   $ svn co https://svn.infrae.com/buildout/silva/trunk
   $ python2.4 bootstrap.py --buildout-profile profiles/development.cfg

Edit your ``buildout.cfg`` to add what you need:

- Check out your Zope products in ``products``, **or** define a new
  section:

  .. code-block:: buildout

    [dev-products]
    recipe = plone.recipe.distros
    urls =
      where to download those products

    [instance]
    products += ${dev-products:location}

- For python based extensions, check them out in ``src``, and:

  .. code-block:: buildout

     [buildout]
     develop +=
        src/my.extension

     [instance]
     eggs +=
        my.extension
     zcml +=
        my.extension

  **Or** use infrae.subversion:

  .. code-block:: buildout

     [devel]
     recipe = infrae.subversion
     as_eggs = True
     location = devel
     urls =
        svn urls

If you plan to do a quick fix to a product or extension, just checkout
the product or extension into ``products`` or ``svn`` directory. If
you plan to add new features that are going to require a lot of
changes/time to implement, create a branch for your buildout,
configure it as explained above, putting all the needed configurations
in ``buildout.cfg``. Add the ``buildout.cfg`` file to your SVN branch
and commit it to SVN.

In order for buildout to access to your version, edit the file
``profiles/versions.cfg`` and to comment the line which defines the
version of the current package your are working on. *Do not commit
those changes.*

Run buildout:

.. code-block:: sh

   $ ./bin/buildout


Creating a tag of the buildout
==============================

Trunk and branches should always use tag version/release, so doing a
new release is easy:

.. code-block:: sh

   $ svn cp branch-or-trunk tag/Silva-version


Updating a buildout with a new tag
==================================

To do this you have to either:

- Edit ``profiles/base.cfg`` to refer to the tag you made for Zope
  based products.

- Edit ``profiles/versions.cfg`` and update the versions of the
  components you want to use. You must add and fix any new
  dependencies which have been added by your new tag.

And: ``svn commit``.

