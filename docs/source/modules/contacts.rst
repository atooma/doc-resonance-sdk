.. _module-contacts:

Contacts
--------------------------

* **Identifier** - CONTACTS
* **Description** - Allows interfacing with contacts available on device.
* **Current Version** - 1
* **Initialization** - None
* **Destroy** - None
* **Dependencies** - :ref:`module-core`

Value Types
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. cssclass:: table-bordered

+-----------------------+---------------------------------------------------------------+---------------+--------------------------+
| Name                  | Description                                                   | From Version  | Wrapper Class            |
+=======================+===============================================================+===============+==========================+
| **PHONE-NUMBER**      | Represent a phone number. It can be of two different types:   | 1             | VT_PhoneNumber_Wrapper   |
|                       |                                                               |               |                          |
|                       | * A literal number                                            |               |                          |
|                       | * A reference to a contact on device                          |               |                          |
+-----------------------+---------------------------------------------------------------+---------------+--------------------------+
| **EMAIL-ADDRESS**     | Represent an email address. It can be of two different types: | 1             | VT_EmailAddress_Wrapper  |
|                       |                                                               |               |                          |
|                       | * A literal string                                            |               |                          |
|                       | * A reference to a contact on device                          |               |                          |
+-----------------------+---------------------------------------------------------------+---------------+--------------------------+
