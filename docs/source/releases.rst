.. _releases:

Releases
=======================================

Stable Gradle Configuration
---------------------------------------

Below is reported current stable Gradle configuration.

.. code-block:: groovy
  :linenos:

  compile 'com.atooma:engine:1.0.7'
  compile 'com.atooma:engine-modules:1.0.10'
  compile 'com.atooma:resonance:1.0.1'

Please notice that, using different version combinations for reported libraries may lead to unexpected behaviors.

Changelog
---------------------------------------

* **Oct 16, 2015**

  .. cssclass:: table-bordered

  +-----------------------+--------------+--------------------------------------------------------------------+
  | Package               | Version      | Changes                                                            |
  +=======================+==============+====================================================================+
  | ``engine-modules``    | ``1.0.10``   | :ref:`module-gdrive` triggers have been added: ``FILEDELETED``,    |
  |                       |              | ``DIRDELETED``, ``SPACEUSED``, ``FILEMODIFIED``.                   |
  |                       |              +--------------------------------------------------------------------+
  |                       |              | :ref:`module-gdrive` performers have been added: ``FILEADD``,      |
  |                       |              | ``FILEDELETE``.                                                    |
  +-----------------------+--------------+--------------------------------------------------------------------+

* **Oct 16, 2015**

  .. cssclass:: table-bordered

  +-----------------------+--------------+--------------------------------------------------------------------+
  | Package               | Version      | Changes                                                            |
  +=======================+==============+====================================================================+
  | ``engine``            | ``1.0.7``    | Added extendible subsystem for identifying possible conflicts      |
  |                       |              | between rules and within a rule definition itself.                 |
  +-----------------------+--------------+--------------------------------------------------------------------+
  | ``engine-modules``    | ``1.0.9``    | :ref:`module-gdrive` first implementation has been added.          |
  |                       |              +--------------------------------------------------------------------+
  |                       |              | :ref:`module-gcalendar` trigger has been added: ``ON-OVERLOAD``.   |
  |                       |              +--------------------------------------------------------------------+
  |                       |              | :ref:`module-gmail` trigger has been added improved. ``INCOMING``  |
  |                       |              | allows to mark mail as read once it is received.                   |
  +-----------------------+--------------+--------------------------------------------------------------------+
  | ``resonance``         | ``1.0.1``    | API for getting ``STILL`` and ``VEHICLE`` sub-activities has been  |
  |                       |              | added.                                                             |
  +-----------------------+--------------+--------------------------------------------------------------------+

* **Oct 9, 2015**

  .. cssclass:: table-bordered

  +-----------------------+--------------+--------------------------------------------------------------------+
  | Package               | Version      | Changes                                                            |
  +=======================+==============+====================================================================+
  | ``engine``            | ``1.0.6``    | Bugs squashed here and there.                                      |
  +-----------------------+--------------+--------------------------------------------------------------------+
  | ``engine-modules``    | ``1.0.8``    | :ref:`module-dropbox` first implementation has been added.         |
  +-----------------------+--------------+--------------------------------------------------------------------+

* **Sep 30, 2015**

  .. cssclass:: table-bordered

  +-----------------------+--------------+--------------------------------------------------------------------+
  | Package               | Version      | Changes                                                            |
  +=======================+==============+====================================================================+
  | ``engine-modules``    | ``1.0.7``    | :ref:`module-instagram` triggers have been added:                  |
  |                       |              | ``NEW-PHOTO-FROM-ME``, ``NEW-PHOTO-FROM-FOLLOWING``,               |
  |                       |              | ``NEW-VIDEO-FROM-ME``, ``NEW-VIDEO-FROM-FOLLOWING``.               |
  +-----------------------+--------------+--------------------------------------------------------------------+

* **Sep 25, 2015**

  .. cssclass:: table-bordered

  +-----------------------+--------------+--------------------------------------------------------------------+
  | Package               | Version      | Changes                                                            |
  +=======================+==============+====================================================================+
  | ``engine-modules``    | ``1.0.6``    | Allowed usage of Google channels on devices lacking of             |
  |                       |              | ``Play Services``.                                                 |
  +                       +              +--------------------------------------------------------------------+
  |                       |              | :ref:`module-twitter` first implementation  has been added.        |
  +                       +              +--------------------------------------------------------------------+
  |                       |              | :ref:`module-instagram` first implementation  has been added.      |
  +                       +              +--------------------------------------------------------------------+
  |                       |              | :ref:`module-gcalendar` trigger has been added: ``ON-EVENT``.      |
  +-----------------------+--------------+--------------------------------------------------------------------+
