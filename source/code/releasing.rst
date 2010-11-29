Releasing and tagging Software
==============================

Before tagging and releasing, make sure all tests run successfully with the
new version of your component.

.. code-block:: sh

   $ ./bin/test

Knowing your extension has tests, run them and any other tests for
extensions that depend on your extension. If your extension does not
have test. *It should!*

.. _tag-zope-product:

Creating a tag of a Zope product
--------------------------------

0. Run tests.

1. Fill the ``HISTORY.txt`` using valid ReST. Replace the
   ``(unreleased)`` at the end of line with the current date *using
   the following format* ``(year/month/day)``.

2. Remove the ``(unreleased)`` in the ``version.txt`` file if the file
   exists, and double check that the version number there and in
   ``HISTORY.txt`` are correct (i.e. corresponds to the branch and
   have not been released yet).

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
   *using the following format* ``(year/month/day)``. Check that the
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

   .. _configuration-pypirc:

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

10. Upload your egg on https://infrae.com/download:

    (You should have configured your ``.pypirc`` file before)

   .. code-block:: sh

      $ python2.4 setup.py register sdist upload -r infrae

11. Go back to your trunk or branch checkout:

    .. code-block:: sh

      $ cd ..
      $ rm -rf release

12. Update the version in ``setup.py``.

13. Commit the changes: ``svn commit``.
