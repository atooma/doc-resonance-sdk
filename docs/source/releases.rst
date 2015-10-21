.. _releases:

Releases
=======================================

Stable Gradle Configuration
---------------------------------------

Below is reported current stable Gradle configuration.

.. code-block:: groovy
  :linenos:

  // ai part
  compile 'com.atooma:core:1.0.1'
  compile 'com.atooma:activitytracker:1.0.1'
  compile 'com.atooma:resonance:1.0.1'
  // engine part
  compile 'com.atooma:engine:1.0.7'
  compile 'com.atooma:engine-modules:1.0.9'

Please notice that, using different version combinations for reported libraries may lead to unexpected behaviors.

Changelog
---------------------------------------

* **Oct 16, 2015 - Engine 1.0.7 / Modules 1.0.9 / Resonance 1.0.1**

  * Added first implementation for module Google Drive

  * Added trigger for module :ref:`module-gcalendar`: ``ON-OVERLOAD``

  * Improved trigger for module :ref:`module-gmail`: ``INCOMING``. Mails can be marked as read in case rule triggers.

  * Added extendible subsystem for identifying possible conflicts between rules and within a rule definition itself.

  * Added API for getting sub-activities from ``STILL`` and ``VEHICLE`` statuses

* **Oct 9, 2015 - Engine 1.0.6 / Modules 1.0.8**

  * Added first implementation for module :ref:`module-dropbox`

  * Bugs squashed here and there

* **Sep 30, 2015 - Modules 1.0.7**

  * Added triggers for module :ref:`module-instagram`: ``NEW-PHOTO-FROM-ME``, ``NEW-PHOTO-FROM-FOLLOWING``, ``NEW-VIDEO-FROM-ME``, ``NEW-VIDEO-FROM-FOLLOWING``

  * Bugs squashed here and there

* **Sep 25, 2015 - Modules 1.0.6**

  * Allowed usage of Google channels on devices lacking of ``Play Services``

  * Added first implementation for module :ref:`module-twitter`

  * Added first implementation for module :ref:`module-instagram`. Only trigger ``NEW-LIKE`` is present.

  * Added trigger ``ON-EVENT`` in module :ref:`module-gcalendar`
