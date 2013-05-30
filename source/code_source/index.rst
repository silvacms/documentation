Code Sources on the file system
===============================

This document explains how to deal with Code Sources on the file
system level. Code Sources are usually first created in the
:term:`ZMI` and then dumped to the file system. This allows their code
to be checked into a source code control system and simplifies future
maintenance of Code Sources within Silva sites.

A Manager role is needed to create a Code Source in the
:term:`ZMI`. This process is explained in the user documentation:
http://silvacms.org/docs/user/manager/creating_a_code_source

Sometimes a newly created Code Source is a one-off, meaning it won't
be used elsewhere. However if the Code Source needs to be maintained
and/or used in multiple sites then it makes sense to manage the Code
Source via Silva's Code Source Service. FMI see:
http://silvacms.org/docs/user/manager/code_source_service

A Silva developer can create a product that will contain all the Code
Sources used in various sites. These sources will then be available in
the Code Source Service.

Exporting and creating a Code Source on the filesystem
------------------------------------------------------
 
You can define Code Sources on the file system, inside a Python
package. By declaring this package in the ``setup.py`` file, the
``service_codesources`` in :term:`ZMI` will be able to propose to
managers to install those Code Sources.

For instance in case of the ``silva.app.blog`` extension you need to:

- create a sub-package to host the Code Sources, like
  ``silva.app.blog.codesources``,

- add the following parameters to the call to the function ``setup``
  in the ``setup.py`` file:

  .. code-block:: python

     entry_points="""
     [Products.SilvaExternalSources.sources]
     defaults = silva.app.blog.codesources
     """,

After this modification you need to re-run buildout for the
modification in the ``setup.py`` file and restart your Silva
server:

.. code-block:: sh

   $ cd Silva
   $ bin/buildout -v
   $ bin/paster serve debug.ini restart

Now your package is properly configured to host code sources.

Exporting a new Code Source previously created in the ZMI
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Once a Code Source is created and tested in the ZMI,
its code can be dumped to the file system. 
To export a new Code Source in a configured extension, you need first
to create a directory that has the same name as your Code Source
inside the previously created ``codesources`` folder in the Python package 
that is configured to host the Code Sources. 
In this new directory you need to create an empty file called
``source.ini``.

For instance, in the case of the ``silva.app.blog`` extension you can
create a directory called ``cs_feedback`` to export a Code Source
called ``cs_feedback`` in it. You should now have the following
directories and files in your extension::

  src/silva/app/blog/codesources
  src/silva/app/blog/codesources/__init__.py
  src/silva/app/blog/codesources/cs_feedback
  src/silva/app/blog/codesources/cs_feedback/source.ini

Afterwards you can either restart your Silva server or click on the button
*Refresh* located on the tab *Install Code Sources* of the
``service_codesources`` in the :term:`ZMI`. Your Code Source,
``cs_feedback`` should now appear in the list of installable Code
Sources and be marked as broken.

If you now visit in the :term:`ZMI` the edit tab of your Code Source, you
should be able to select next to the *Location* option the value
``silva.app.blog:/silva/app/blog/codesources/cs_feedback`` and click
on the button *Save changes*. After this you should see four new buttons 
(see the figure below), among these buttons there's *Export to filesystem* button. 
Clicking on this button should export the files of your Code Source on the filesystem 
in the newly created directory.

.. figure:: update_export_cs.png
        :alt: Update and Export buttons in the ZMI
        :align: left
Update and Export buttons in the ZMI

If you're working on a remote server then the *Export and download as ZIP* 
function may be useful. This will export Code Source files packed in a ZIP file. 
After clicking on it, save the ZIP to your disk and expand it.

Depending on the Code Source contents, there will be three or more
files. As an example, exporting the TOC Code Source as zip will give an archive
that will contain::

  icon.png  parameters.xml  source.ini  toc.pt  toc_sort_on.py

* The icon which is used in the :term:`SMI`.

* The parameters XML file which contains the parameters form.

* The ``source.ini`` contains configuration, the same as in the Edit
  screen form.

* The ``toc.pt`` is the renderer, which can be a page template or Python script.

* A helper script, ``toc_sort_on.py``, is also present.

The directory containing these items can be moved into the
'codesources' directory of your product. It makes sense to give the
directory the same name as the id in the ``source.ini``.

If you give the name a prefix this will cause the sources to be
grouped together in the ZMI, e.g. Silva's default Code Sources all
have an id that is prefixed with ``cs_``.

Creating a new Code Source directly on the filesystem
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To create a Code Source on the file system you need a few files.
The most important one is ``source.ini``. This file follows a
windows like INI file format to define the properties and settings of
the Code Source.

.. code-block:: buildout
   :linenos:

   [source]
   id = cs_feedback
   title = Blog article feedback
   render_id = render_feedback
   description = Display the feedback that was posted on the
      article of a blog.
   usuable = on
   previewable = on

- Line 1 defines a configuration section to store the Code Source
  parameters,

- Line 2 defines the default identifier that will be used to create
  the Code Source during the installation. The identifier is used as
  well to associate the Code Source on the filesystem with an instance
  in the ZODB,

- Line 3 defines the title that the Silva user will see and use in
  order to select the Code Source in Silva content,

- Line 4 defines the name of the script or page template that will be
  used in order to render the Code Source in Silva content,

- Line 5 to 6 define an optional description that the Silva user will
  see in addition to the title,

- Line 7 sets the flag ``usuable`` to on. This flag will allow the
  Silva user to use this Code Source and create newly set of
  parameters in Silva content. If set to off only existing set
  parameters can be edited in Silva content,

- Line 8 sets the flag ``previewable`` to on, that will enable to
  preview Code Sources in the WYSIWYG editor. Preview in the
  editor can be disabled, in cases the Code Source
  contains complicated Javascript that might not work in the editor.

If your Code Source needs parameters, you can add a file called
``parameters.xml`` that contains the XML definition of a Formulator
form.

You can add an icon to your Code Source, by adding a file called
``icon.png`` or ``icon.gif`` to your Code Source.

Any other file you add in the Code Source will be installed inside it.

For instance in the case of the ``silva.app.blog`` extension, you
should have the following files in the code source at the end::

  src/silva/app/blog/codesources
  src/silva/app/blog/codesources/__init__.py
  src/silva/app/blog/codesources/cs_feedback
  src/silva/app/blog/codesources/cs_feedback/source.ini
  src/silva/app/blog/codesources/cs_feedback/icon.png
  src/silva/app/blog/codesources/cs_feedback/parameters.xml
  src/silva/app/blog/codesources/cs_feedback/render_feedback.pt
