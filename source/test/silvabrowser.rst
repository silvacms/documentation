
Silva Browser
-------------

.. module:: Products.Silva.tests.SilvaBrowser

.. class:: SilvaBrowser

   .. function:: click_button_labeled()
    
      Click on a button or psuedo button (a link dressed up as button).
  
      Example: ``delete``, ``close``, ``publish now``, ``addables``
      (pseudo button).

   .. function:: click_href_labeled()
  
      Click on a link.
  
      Example: ``logout``, ``index``, created or existing content types.

   .. function:: click_tab_named()
  
      Click on a specific tab name.
  
      Example: ``contents``, ``preview``, ``access``

   .. function:: get_addables_list()
  
     Return a list of addable metatypes from the select menu.
  
     Usage: check what content is available for a particular role.

   .. function:: get_addform_title()
    
     Return a normalized ``<h2>`` title for add forms.
  
     Example: *create Silva Document*.
  
     Usage: check if the proper add form, or title is being retrieved.

   .. function:: get_alert_feedback()

     Return the alert message in the page
  
     Example: *Could not delete <<index>>*.
  
     Usage: check if an SMI error occurred.

   .. function:: get_content_data()

     Return a list of dictionaries describing the content objects.
    
   .. function:: get_content_ids()

     Return a list of ids for objects in a current container.
  
     Usage: check if existing or created content exists.

   .. function:: get_href_named()

     Return an href with a specific label.
  
     Usage: retrieve a link from a specific page to test location.

   .. function:: get_listing_h2

     Return the content type and name of the ``<h2>`` in the listing table.
  
   .. function:: get_status_and_url():

     Return HTTP status and the URL.
  
     Usage: check the HTTP error code for logging out or check location.

   .. function:: get_status_feedback()

     Return the status message in the page.
  
     Example: ``Added Silva Folder``, ``Version Approved``

   .. function:: get_tabs_named()

     Return a specific tab name from ``<div class="tabs">``.
  
     Example: ``editor``
  
     Usage: check location specific tabs.

   .. function:: get_middleground_buttons()

     Return a specific button from the ``<div class="middleground">``.
  
     Example: ``settings...``, ``addables...``
  
     Usage: check locations specific buttons.

   .. function:: get_url()

     Return the current URL.
  
     Usage: check current location.

   .. function:: get_root_url()

     Return the ZMI root URL.
  
     Usage: jump from current location to the ZMI.

   .. function:: go(url)

     :param url: URL to open.

     Open a specific browser page and return the HTTP status code and current URL.
    
   .. function:: html2text()

     Return children of an html element, stripping out child elements,
     and normalizing text nodes. Supports ``click_button_labeled()``,
     ``get_addform_title()``, ``get_listing_h2()``,
     ``get_tabs_named()``, ``get_middlegroung_buttons()``.

   .. function:: login()
  
     Login to the SMI

   .. function:: logout()
  
     Logout of the SMI

   .. function:: make_content()
    
     Makes content of a specific type as a specific user, with one or
     more fields filled in.

   .. function:: make_default_content()

   .. function:: open_file()
  
     Format the path to data/ for test files.

   .. function:: select_addable()

     Select a meta_type form the addables list.

   .. function:: select_all_content()

     Toggle all content item checkboxes.

     Usage: Selecting all content to close, then delete.

   .. function:: select_content()

     Toggle a content item checkbox.

     Usage: Select a content item to close.

   .. function:: select_delete_content()

     Select and then delete a content item.

   .. function:: smi_url()

     Return the SMI URL for current URL.

   .. function:: sef_fields()

     Fill multiple ``field_object`` controls where keyword is a fieldname
     and value. This is support for the ``make_content()`` method.

   .. function:: set_id_field()
    
     Set the id field.

   .. function:: set_title_field()
    
     Set the title field.

   .. function:: set_policy_field()
    
     Set the policy field.
  
     Example: Set the default document as ``Silva Document`` when
     creating a Silva Folder.

   .. function:: set_image_field()
    
     Set the image field.

   .. function:: set_file_field()
    
     Set the file field.

   .. function:: set_ghost_url_field()
    
     Set the ghost url field.

   .. function:: set_url_field()
    
     Set the url field.

   .. function:: set_link_type_field()
    
     Set the link type button.

   .. function:: set_depth_field()
    
     Set the depth field.
