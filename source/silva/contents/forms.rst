
Adding forms to your content
============================

To be able to add your content from the :term:`SMI`, you need to
create an *add form*. Probably you want to have an *edit form* to
modify your content as well.

.. contents::

Creating a form descrption
--------------------------

To create a form, you need first to create a :term:`Zope interface`
that will describe which fields your form should contains, using
:term:`Zope schema fields`.

.. glossary::

   *Zope schema fields*
     A Zope schema field is a :term:`Zope interface` attribute that
     describe the type of this attribute (is it a string, an
     integer...), and gives information about its properties (is it
     required, what is its English name...). Those schema fields are a
     particularly useful standard way to describe form fields.

In case of an add form, you can make your field definition interface
inherit from those interfaces, who defined an ``id`` and a ``title``
for your content:

.. autointerface:: silva.core.conf.interfaces.IIdentifiedContent

.. autointerface:: silva.core.conf.interfaces.ITitledContent


Creating an add form
--------------------

.. code-block:: python

   from zope import interface, schema

   class IMyContent(interface.Interface):

       email = schema.TextLine(
           title=u"Email address",
           description=u"Your email address, free of spam.",
           required=False)

       contact_url = schema.URI(
           title=u"Contact URL",
           required=True)


.. class:: zeam.form.silva.SMIAddForm

   Create an add form. The form already have *Cancel*, *Save* and
   *Save and edit* actions.

   The form will effectively add, and edit the content.

   .. method:: _add()

      Override this method if you whish to customize the creation of
      the content.

   .. method:: _edit()

      Overide this method, if you didn't override :py:meth:`_add`
      and whish to customize the edition of the new content.


Creating an edit form
---------------------

.. class:: zeam.form.silva.SMIEditForm

   Create an edit form. The form already have *Cancel* and *Save*
   actions.


Creating a public form
----------------------

A public form is a form that won't be displayed in the :term:`SMI` but
to the public as a public view. Like for a public view, the layout
system will include the layout around the form.

For this create your form by inheriting from:

.. class:: zeam.form.silva.PublicForm

   Create a form for the public front-end of the site.

Apart from the class which you have to inherit from, public form works
the same way than the other forms.

Embedding a public form in a existing view
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can embbed a form as a :term:`Content Provider` or a
:term:`Viewlet` in a public view. To do this you can create your form
by inheriting from one of those classes:

.. class:: zeam.form.silva.PublicViewletForm

   Create a form in a :term:`Viewlet`.

.. class:: zeam.form.silva.PublicContentProviderForm

   Create a form in a :term:`Content Provider`.

.. seealso::

   :ref:`creating-a-default-view`


Using Complex forms
-------------------

More complicated forms can be created, like it is done for the access
tab in the :term:`SMI`.

Complex form components that can be used in the :term:`SMI` are:

.. class:: zeam.form.silva.SMIComposedForm

   Create a composed form: the form is composed of other sub forms,
   that can interact between each of them.

.. class:: zeam.form.silva.SMISubForm

   Create a sub form. It have to be associated to a
   :py:class:`SMIComposedForm<zeam.form.silva.SMIComposedForm>` or a
   :py:class:`SMISubFormGroup<zeam.form.silva.SMISubFormGroup>` in
   order to be used.

.. class:: zeam.form.silva.SMISubFormGroup

   Create a group of sub form inside a
   :py:class:`SMIComposedForm<zeam.form.silva.SMIComposedForm>`.

.. class:: zeam.form.silva.SMISubTableForm

   Create a sub form that can work on multiple items at once. This
   create a table in the user interface, with each table columns
   representing each form fields, and each line representing each
   content.  This form can host table actions, that are executed
   against each selected lines upon form submission.

   Like for :py:class:`SMISubForm<zeam.form.silva.SMISubForm>`, it
   have to be associated to a
   :py:class:`SMIComposedForm<zeam.form.silva.SMIComposedForm>` or a
   :py:class:`SMISubFormGroup<zeam.form.silva.SMISubFormGroup>` in
   order to be used.

.. class:: zeam.form.silva.SMIViewletForm

   Create a form as a :term:`Viewlet` in the :term:`SMI`.

.. class:: zeam.form.silva.SMIContentProviderForm

   Create a form as a :term:`Content Provider` in the :term:`SMI`.
