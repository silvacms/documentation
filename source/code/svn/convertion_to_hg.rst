
Converting a Subversion repository to Mercurial
===============================================

This snippet will guide you through the process of
converting a Subversion repository on https://svn.infrae.com
to a Mercurial repository on https://hg.infrae.com

.. contents::

Requirements
------------

You need to have either have Subversion 1.5 (or later) installed along with
either Subvertpy 0.7.3 (or later) or the Subversion SWIG Python bindings. You
need Mercurial 1.3 or later.

Installation of `hgsubversion`
------------------------------

You need to have mercurial installed and an extension called
`hgsubversion` as well. You can get it from bitbucked:

.. code-block:: sh

  $ hg clone https://bitbucket.org/durin42/hgsubversion

You need to be sure that `hgsubversion` is in your python path, so just
install it using the ``setup.py``:

.. code-block:: sh

  $ cd hgsubversion
  $ sudo python setup.py install

`hgsubversion` depends on a python package called subvertpy, to build this you
need the development headers for svn and apache (apr), so on Ubuntu run:

.. code-block:: sh

  $ sudo apt-get install libsvn-dev libapr1-dev

Next, we need to enable the extension, so open the ``~/.hgrc`` file and
add the following:

.. code-block:: ini

  [extensions]
  hgsubversion =

Check that `hgsubversion` is in the enabled extensions list:

.. code-block:: sh

  $ hg help extensions

Configuraiton
~~~~~~~~~~~~~

When the conversion starts, `hgsubversion` will convert the ``tags``
directory from svn to tags in Mercurial. However, in many projects
this directory is called ``tag`` instead of ``tags``.  You can tell
`hgsubversion` how the tag folder is called, in your ``~/.hgrc``
configuration file

.. code-block:: ini

  [hgsubversion]
  tagpaths = tag

Cloning a Subversion repository in Mercurial
--------------------------------------------

All you have to do is clone the Subversion repository from Mercurial:

.. code-block:: sh

  $ hg clone svn:https://svn.infrae.com/PACKAGE

When the cloning is finished, go in to the new mercurial repository:

.. code-block:: sh

  $ hg tags

You should now see a list of all the tags from the project.

You can copy all the ``svn:ignore`` properties from svn by running the
following command:

.. code-block:: sh

  $ hg svn genignore

This should produce a file called ``.hgignore`` with all the ignores in it.

Cloning the branches is a bit problematic, you need to copy the
``branch`` directory in subversion to ``branches``. You can also
specify a ``branchmap`` in the configuration file to rename the
branches to something more sensible. See the `hgsubversion` help for
more options:

.. code-block:: sh

  $ hg help hgsubversion

Creating the centralized Mercurial repository
---------------------------------------------

Now that we have a local Mercurial repository we can verify that the
conversion went okay, and push the repository to our centralized
repository (http://hg.infrae.com).

To do this we first need to log in to the `titan` server to add the
repository with the `create_mercurial.sh` script

.. code-block:: sh

  $ ssh titan.infrae.com
  $ cd /home/infrae/mercurial/repositories
  $ sudo -u www-data ./create_mercurial.sh <repo name>

Look at the output of the script, it will show you a couple of lines that
can be copied to the trac configuration, so your project becomes visible at
http://dev.infrae.com

Now, in our local mercurial project, we need to change the default push path,
so open the ``.hg/hgrc`` file, and change the following lines:

.. code-block:: diff

  -default = svn+https://svn.infrae.com/pyoai
  +default = https://hg.infrae.com/pyoai

Last step is to do the actual push in the local repository:

.. code-block:: sh

  $ hg push

