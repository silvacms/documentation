Development Guidelines for Silva
********************************

.. contents::

.. note:: That documentation is intended to Silva core developer. But
   as development practice, they might be interesting to other people as
   well.

Introduction
============

You can develop on Silva 2.0, 2.1 and the trunk 2.2. They are
hereafter referred to as *current versions* of Silva.

To work on Silva, you are kindly request to setup it Silva using
`buildout coming from SVN <https://svn.infrae.com/buildout/silva/>`_.

Those instruction to work on Silva may seems boring and require a lot
to do, but:

- It less us do a lot more release instead of one every one and half
  year.

- It follow the rules that says: *until your code is delivered, it's
  useless*. We like to write usefull code, so code used by People.

- This let us release lot of alpha or beta, but that's fine.

It's somewhat like Steeve Alexander explained for Launchpad.

There are three kinds of buildouts
----------------------------------

1. The Trunk buildout, which is the lastest version of Silva.

2. Branch buildouts, which are older Silva versions (2.0 and 2.1 for
   instance).

3. Tag buildouts, which are released version of Silva. *They must be
   never changed*. If you want to fix an issue which affects a tag,
   you must do it in the corresponding branch of Silva (if you want to
   fix something in a 2.1 tag, you fix it on the 2.1 branch) and
   release a new tag.

There are three kinds of Silva components
-----------------------------------------

1. Extensions that are direct dependencies of Silva, such as
   Formulator, or ParsedXML, or Annotations. They should work for all
   current versions of Silva.

   If you work on one of those, you have to check that:

   1. Your changes work with all current versions of Silva by
      setting up an environement and running the tests.

   2. When you tag that extension, to update buildouts for *all
      current branches* of Silva to use that new tag.

2. Extensions that make up the core of Silva.

3. Extensions that add features to Silva and may work on more than one
   current version of Silva:

   - If an extension does not work for all current versions, you need
     to port your changes on the newer version of that extension,
     following the same pattern as you would for developing Silva
     itself.

   - If an extension does work for more than one version of Silva, you
     need to check that it's still working for others versions than
     the one you developed your feature for. If it's not the case, you
     need to create branches of the extension for those versions of
     Silva.

All extensions have a trunk version, which should work with Silva
trunk, and may have branches for other Silva versions, and tags for
released versions. Like for buildouts, if you want to fix something on
a tag, do it on the corresponding branch and make a new tag.


.. warning::

   All buildouts  should refer to tags  of Silva components.  To add a
   new feature to a component, add  a new tag of that component to the
   current versions buildouts.


Porting rules
=============

- If you work on an feature or bug fix on (for) 2.0 you *must* provide
  the same on (for) 2.1 and (for) the trunk. Also provide a unit test
  if at all possible, to prevent regression errors.

- If you work on an feature or bug fix (for) 2.1 you *must* provide
  the same on (for) the trunk. Also provide a unit test if at all
  possible, to prevent regression errors.

- If you work on a bug fix (for) the trunk, you *must* provide the
  same on (for) 2.1.


Creating new branches
=====================

There is only two cases where you want (and have) to create new
branches:

- You want to have a version of an extension which still work for a
  previous version of Silva (backward incompatible changes): in that
  case the branch should be called ``extensionname-version``, for
  example ``SilvaLayout-1.4``. They exists mainly to maintain that
  extension for that specific older version of Silva (i.e. bug
  fixes). Those branches are likely to stay forever in SVN.

- You want to make an experimentation, and your are not sure about the
  ending of it. In that case the branch should be called with
  ``yourname-why``, for example ``sylvain-database-distribution``.

  Those branches are likely to be merged as soon as they work and
  you're happy with theirs changes. An experimentation of one year
  old is already deprecated and too far difficult to merge, it should
  just be deleted. If your are not happy with those changes you can
  delete it right now.

  .. note::

     All merged branches should be deleted right after.


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


If you plan to do a quick fix to product or extension, just do the
checkout in ``products`` or ``svn``. If you plan to add new features
that are going to require a lot of changes/time to implement, create a
branch for your buildout, configure it as explained above, putting all
the configuration in ``buildout.cfg``, add that file to your SVN
branch (and commit).

In order to let buildout use your version, you need to edit the file
``profiles/versions.cfg`` and to comment the line which define the
version of the current package your are working on. *Do not commit
those changes.*

Run buildout:

.. code-block:: sh

   $ ./bin/buildout



Releasing and creating tags
===========================

Before releasing a tag, make sure that all tests should work for Silva
with this new version of your component:

.. code-block:: sh

   $ ./bin/instance test -s Products.Silva

And run the tests of your extension (and any others which depend on
it) if it has tests. (Which it should!)


.. _tag-zope-product:

Creating a tag of a Zope product
--------------------------------

0. Run tests.

1. Fill the ``HISTORY.txt`` using valid ReST. Replace the
   ``(unreleased)`` at the end of line by the date of today *using the
   following format* ``(year-month-day)``.

2. Remove the ``(unreleased)`` in ``version.txt``, and double check
   that the version number there and in ``HISTORY.txt`` are correct
   (i.e. correspond to the branch and have not been released yet).

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


Creating a tag of a Python-only extension
-----------------------------------------

0. Run the tests.

1. Fill the ``HISTORY.txt`` using valid ReST. Replace the
   ``(unreleased)`` at the end of line by the date of today *using the
   following format* ``(year-month-day)``. Check that the
   ``README.txt`` is up to date with the last information.

2. Verify the validity of your documentation:

   .. code-block:: sh

      $ python2.4 setup.py egg_info

   Should not give an error, and:

   .. code-block:: sh

      $ python2.4 setup.py --long-description | rst2html > description.html

   Should not either. Open ``description.html`` in your web browser
   and check that this looks good.

3. Do a ``svn commit``.

4. Create the tag:

   .. code-block:: sh

      $ svn cp extension/branch-or-trunk extension/tag/extension_name-version

5. Do a new check-out of your tag:

   .. code-block:: sh

      $ svn co extension/tag/extension_name-version release
      $ cd release

6. In that checkout, edit ``setup.py`` and remove the dev marker from
   the version.

7. Commit those changes (this is the *one* exception to the rule that
   you should never check in anything on a tag.):

   .. code-block:: sh

      $ svn commit -m "Remove dev marker."

8. Upload your egg on https://dist.infrae.com:

   .. code-block:: sh

      $ python2.4 setup.py register sdist upload -r https://dist.infrae.com/download

9. Go back to your trunk or branch checkout:

   .. code-block:: sh

      $ cd ..
      $ rm -rf release

10. Update the version in ``setup.py``.

11. Update ``docs/HISTORY.txt`` to add a new header for that version::

      ProductName version (unreleased)
      --------------------------------

      - ....

12. Commit thoses changes: ``svn commit``


Creating a tag of a Zope product packaged as an Python extension
----------------------------------------------------------------

0. Run tests.

1. Create a tag of the Zope product like described before
   (see :ref:`tag-zope-product`).

2. Verify the validity of your documentation:

   .. code-block:: sh

      $ python2.4 setup.py egg_info

   should not give an error, and neither should:

   .. code-block:: sh

      $ python2.4 setup.py --long-description | rst2html > description.html

   Open ``description.html`` in your web browser and check this looks
   good.

3. Do a ``svn commit``.

4. Create the tag:

   .. code-block:: sh

      $ svn cp extension/branch-or-trunk extension/tag/extension_name-version

5. Do a new check-out of your tag:

   .. code-block:: sh

      $ svn co extension/tag/extension_name-version release
      $ cd release

6. In that checkout, edit ``setup.py`` and remove the dev marker from
   the version.

7. In the ``Products`` sub-folder, edit the ``svn:externals``
   properties to use the Zope product you tagged:

   .. code-block:: sh

      $ svn propedit svn:externals Products

8. Update to be sure to have the code of the tag:

   .. code-block:: sh

      $ svn up

9. Commit those changes (this is the *one* exception to the rule that
   you should never check in anything on a tag.):

   .. code-block:: sh

      $ svn commit -m "Remove dev marker."

10. Upload your egg on https://dist.infrae.com:

   .. code-block:: sh

      $ python2.4 setup.py register sdist upload -r https://dist.infrae.com/download

11. Go back to your trunk or branch checkout:

    .. code-block:: sh

      $ cd ..
      $ rm -rf release

12. Update the version in ``setup.py``.

13. Commit thoses changes: ``svn commit``


Creating a tag of the buildout
------------------------------

Trunk and branches should always use tag version/release, so doing a
new release is easy:

.. code-block:: sh

   $ svn cp branch-or-trunk tag/Silva-version


Updating the buildout with a new tag
====================================

You have to either:

- Edit ``profiles/base.cfg`` to refer to the tag you made for Zope
  based products.

- Edit ``profiles/versions.cfg`` and update the versions of the
  components you want to use. You must add and fix any new
  dependencies which have been added by your new tag.

And: ``svn commit``.
