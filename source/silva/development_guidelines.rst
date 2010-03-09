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
  with the outcome of the experiment delete the branch.

  .. note::

     After merging a branch it should be deleted.

.. _setup-dev-env:

Setting up a development environment
====================================

Check out a fresh buildout for your development, and bootstrap it:

.. code-block:: sh

   $ svn co https://svn.infrae.com/buildout/silva/trunk
   $ python2.4 bootstrap.py --buildout-profile profiles/development.cfg

Edit your ``buildout.cfg`` to add what you need:

- Check out your Zope products in ``products``, **or** define a new
  section::

    [dev-products]
    recipe = plone.recipe.distros
    urls =
      where to download those products

    [instance]
    products += ${dev-products:location}

- For python based extensions, check them out in ``src``, and::

    [buildout]
    develop +=
       src/my.extension

    [instance]
    eggs +=
       my.extension
    zcml +=
       my.extension

  **Or** use infrae.subversion::

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

Releasing and creating tags
===========================

Before releasing a tag, make sure all tests run successfully with the
new version of your component.

.. code-block:: sh

   $ ./bin/instance test -s Products.Silva

Knowing your extension has tests, run them and any other tests for
extensions that depend on your extension. If your extension does not
have test. *It should!*

.. _tag-zope-product:

Creating a tag of a Zope product
--------------------------------

0. Run tests.

1. Fill the ``HISTORY.txt`` using valid ReST. Replace the
   ``(unreleased)`` at the end of line with the current date *using
   the following format* ``(year-month-day)``.

2. Remove the ``(unreleased)`` in the ``version.txt`` file, and double
   check that the version number there and in ``HISTORY.txt`` are
   correct (i.e. corresponds to the branch and have not been released
   yet).

3. Do a ``svn commit``.

4. Create the tag:

   .. code-block:: sh

      $ svn cp product/branch-or-trunk product/tag/product_name-version/

5. Edit ``HISTORY.txt`` to add a new header::

      ProductName version (unreleased)
      --------------------------------

      - ....

6. Edit ``version.txt`` to increase the version, and add
   ``(unreleased)`` at the end.

Creating a Python-only extension tag
------------------------------------

0. Run the tests.

1. Fill the ``HISTORY.txt`` using valid ReST. Replace the
   ``(unreleased)`` at the end of the line with the current date
   *using the following format* ``(year-month-day)``. Check that the
   ``README.txt`` is up to date with the latest information.

2. Check the validity of your documentation:

   .. code-block:: sh

      $ python2.x setup.py egg_info

   Should not give an error, and:

   .. code-block:: sh

      $ python2.x setup.py --long-description | rst2html > description.html

   This also should not produce and error. Open ``description.html``
   in your web browser and check that the document is okay.

3. Do a ``svn commit``.

4. Create the tag:

   .. code-block:: sh

      $ svn cp extension/branch-or-trunk extension/tag/extension_name-version

5. Do a new check-out of your tag:

   .. code-block:: sh

      $ svn co extension/tag/extension_name-version release
      $ cd release

6. In the checkout, edit ``setup.py`` and remove the dev marker from
   the version.

7. Commit these changes (this is the *one* exception to the rule that
   you should never check in anything on a tag.):

   .. code-block:: sh

      $ svn commit -m "Remove dev marker."

8. Upload your egg on http://infrae.com/download:

   Open up your ~/.pypirc file and make sure it has an entry for infrae.com

   .. code-block:: sh

      [distutils]
      index-servers = pypi
                      infrae
      [pypi]
      username = user
      password = password

      [infrae]
      username = user
      password = password
      repository = http://infrae.com/download
      realm = Zope

   Next, run the update

   .. code-block:: sh

      $ python2.x setup.py register sdist upload -r infrae

9. Go back to your trunk or branch checkout:

   .. code-block:: sh

      $ cd ..
      $ rm -rf release

10. Update the version in ``setup.py``.

11. Update ``docs/HISTORY.txt`` to add a new header for that version::

      ProductName version (unreleased)
      --------------------------------

      - ....

12. Commit the changes: ``svn commit``

Creating a tag of a Zope product packaged as an Python extension
----------------------------------------------------------------

0. Run tests.

1. Create a tag of the Zope product like described before
   (see :ref:`tag-zope-product`).

2. Check the validity of your documentation:

   .. code-block:: sh

      $ python2.4 setup.py egg_info

   should not give an error, and neither should:

   .. code-block:: sh

      $ python2.4 setup.py --long-description | rst2html > description.html

   Open ``description.html`` in a web browser and check the documentation.

3. Do a ``svn commit``.

4. Create the tag:

   .. code-block:: sh

      $ svn cp extension/branch-or-trunk extension/tag/extension_name-version

5. Do a new check-out of your tag:

   .. code-block:: sh

      $ svn co extension/tag/extension_name-version release
      $ cd release

6. In the checkout, edit ``setup.py`` and remove the dev marker from
   the version.

7. In the ``Products`` sub-folder, edit the ``svn:externals``
   properties to use the Zope product you tagged:

   .. code-block:: sh

      $ svn propedit svn:externals Products

8. Update to be sure you have the latest code of the tag:

   .. code-block:: sh

      $ svn up

9. Commit the changes (this is the *one* exception to the rule that
   you should never check in anything on a tag.):

   .. code-block:: sh

      $ svn commit -m "Remove dev marker."

10. Upload your egg on https://dist.infrae.com:

   .. code-block:: sh

      $ python2.4 setup.py register sdist upload -r https://www.infrae.com/download

11. Go back to your trunk or branch checkout:

    .. code-block:: sh

      $ cd ..
      $ rm -rf release

12. Update the version in ``setup.py``.

13. Commit the changes: ``svn commit``

Creating a tag of the buildout
------------------------------

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