Welcome to Resonance Android SDK's documentation!
======================================================

Welcome to the official reference for developing with Resonance Android SDK powered by Atooma.

.. note::

	This documentation is a work in progress. We are working hard every day for providing all of you an awesome developer experience, so stay tuned!

	Documentation sources are available on `GitHub <https://github.com/atooma/doc-resonance-sdk>`_, so if you find typos, errors, or think something can be communicated better, fork repository and make a pull request.

	We appreciate your understanding and patience!

This documentation is aimed to provide in depth details on how to approach development and it is organized as follows:

.. toctree::
	:numbered:
	:maxdepth: 1

	intro
	core
	engine
	xml
	programming
	reference
	activity
	resonance
	..commons
	examples

..
	* :ref:`intro` - Here you can find answers to questions like *what is Resonance?* and *what can I do with Resonance SDK?*. Your understanding of Atooma potential starts from here!

	* :ref:`core` - Interested in Resonance SDK? Great! First step is to go deep into this section for understanding main concepts concerned with Atooma environment and platform.

	* :ref:`engine` - Here are provided details on main classes to be used for allowing Rules Engine to work.

	* :ref:`xml` - Defining complex rules with Atooma is extremely simple and doesn't require to write a huge amount of lines of code. It's enough to have a basic knowledge of XML and check our :ref:`reference` for creating awesome automations.

	* :ref:`programming` - Atooma SDK already provides many sensors but... what if this is not enough? If you need additional features you can simply create them!

	* :ref:`reference` - Here it is our API reference, providing details on all modules that are currently available with Atooma SDK. You can exploit all of them for building your great applications!

	* :ref:`activity` -

	* :ref:`resonance` -

	* :ref:`commons` - Here are described classes and utilities included in Atooma Commons Library. Its purpose is providing  a ready to use implementation for useful features and functionalities.

	* :ref:`examples` - This sections provides some examples, showing how Resonance SDK simplify development. Focus on core features of your application and let Atooma do the rest!

	* :ref:`releases` - Here is reported changelog as well as list of stable packages to use!

.. _releases-stable:

Stable Gradle Configuration
---------------------------------------

Below is reported current stable Gradle configuration for Resonance SDK for Android. Please be aware that using different version combinations may lead to unexpected behaviors.

.. code-block:: groovy
  :linenos:

  compile 'com.atooma:engine:2.0.1'
  compile 'com.atooma:modules-mobile:2.0.1'
  compile 'com.atooma:modules-server:2.0.1'
  compile 'com.atooma:modules-google:2.0.1'
  compile 'com.atooma:resonance:2.0.1'

In addition to application configuration, top level ``build.gradle`` must also include credentials for accessing Atooma packages.
You can get credentials by following instructions reported in section :ref:`intro-needs`.

.. code-block:: groovy
  :linenos:

  allprojects {
    repositories {
      jcenter()
      maven {
        url  "http://atooma.bintray.com/resonance"
        credentials {
          username 'YOUR USERNAME'
          password 'YOUR PASSWORD'
        }
      }
    }
  }

.. _manifest-stable:

Basic Manifest Configuration
---------------------------------------

Below is reported the basic Manifest configuration allowing to use resonance effectively. Additional directives may be needed in case you decide to use specific modules. All related details are reported in section :ref:`reference`.

.. code-block:: xml
  :linenos:

  <uses-permission android:name="YOUR_PACKAGE_NAME.permission.RESONANCE" />

  <permission
    android:name="YOUR_PACKAGE_NAME.permission.RESONANCE"
    android:protectionLevel="signature" />

  <!-- ... -->

  <meta-data
    android:name="com.atooma.resonance.sdk.ApplicationId"
    android:value="YOUR_ID" />

  <!-- ... -->

  <provider
    android:name="com.atooma.activitytracker.history.ActivitiesProvider"
    android:authorities="com.atooma.resonance.activitiesYOUR_ID"
    android:exported="false" />

  <provider
    android:name="com.atooma.activitytracker.history.LocationsProvider"
    android:authorities="com.atooma.resonance.locationsYOUR_ID"
    android:exported="false" />

  <provider
    android:name="com.atooma.resonance.provider.SnapshotProvider"
    android:authorities="com.atooma.resonance.snapshotsYOUR_ID"
    android:exported="false" />

Please notice that the ``Application Id`` required in Manifest is provided by Atooma Team together with credentials for accessing repositories.

Changelog
---------------------------------------

* **Dec 2, 2015**

  .. cssclass:: table-bordered

  +-----------------------+--------------+--------------------------------------------------------------------+
  | Package               | Version      | Changes                                                            |
  +=======================+==============+====================================================================+
  | ``engine``            | ``2.0.1``    | RULE_TRIGGERED permission replaced by RESONANCE in manifest.       |
  |                       |              | ``getActiveModules`` method now available in ``Atooma`` class.     |
  |                       |              | Updated meta data to be provided for Instagram authentication.     |
  |                       |              | Bugs squashed here and there.                                      |
  +-----------------------+--------------+--------------------------------------------------------------------+
  | ``modules-mobile``    | ``2.0.1``    | Code optimization.                                                 |
  +-----------------------+--------------+--------------------------------------------------------------------+
  | ``modules-server``    | ``2.0.1``    | Code optimization.                                                 |
  +-----------------------+--------------+--------------------------------------------------------------------+
  | ``modules-google``    | ``2.0.1``    | Code optimization.                                                 |
  +-----------------------+--------------+--------------------------------------------------------------------+
  | ``resonance``         | ``2.0.1``    | Improved activity tracking with main focus on path recognition.    |
  |                       |              | Location provider configuration to be added in manifest.           |
  |                       |              | ACTIVITY_DETECTION and DATA_COLLECTOR_ENTRY_PERSISTED permissions  |
  |                       |              | replaced by RESONANCE in manifest.                                 |
  +-----------------------+--------------+--------------------------------------------------------------------+

* **Nov 10, 2015**

	.. cssclass:: table-bordered

  +-----------------------+--------------+--------------------------------------------------------------------+
  | Package               | Version      | Changes                                                            |
  +=======================+==============+====================================================================+
  | ``engine``            | ``2.0.0``    | Code optimization.                                                 |
  +-----------------------+--------------+--------------------------------------------------------------------+
  | ``modules-mobile``    | ``2.0.0``    | Package just includes all modules related to mobile device         |
  |                       |              | features.                                                          |
  +-----------------------+--------------+--------------------------------------------------------------------+
  | ``modules-server``    | ``2.0.0``    | Package just includes modules related to server device except the  |
  |                       |              | ones related to Google services.                                   |
  +-----------------------+--------------+--------------------------------------------------------------------+
  | ``modules-google``    | ``2.0.0``    | Package just includes modules related to google services.          |
  |                       |              | :ref:`module-gmail` trigger ``INCOMING`` has been improved for     |
  |                       |              | allowing to receive real time updates instead of relying on        |
  |                       |              | periodic checks.                                                   |
  +-----------------------+--------------+--------------------------------------------------------------------+
  | ``resonance``         | ``2.0.0``    | Code optimization.                                                 |
  +-----------------------+--------------+--------------------------------------------------------------------+

Please notice that ``modules-mobile``, ``modules-server`` and ``modules-google`` do not depend on each other. This means developer can use even just one of them in case he's just interested in a subset of integrations.

* **Oct 21, 2015**

  .. cssclass:: table-bordered

  +-----------------------+--------------+--------------------------------------------------------------------+
  | Package               | Version      | Changes                                                            |
  +=======================+==============+====================================================================+
  | ``engine``            | ``1.0.7``    | \-                                                                 |
  +-----------------------+--------------+--------------------------------------------------------------------+
  | ``engine-modules``    | ``1.0.10``   | :ref:`module-gdrive` triggers have been added: ``FILEDELETED``,    |
  |                       |              | ``DIRDELETED``, ``SPACEUSED``, ``FILEMODIFIED``.                   |
  |                       |              +--------------------------------------------------------------------+
  |                       |              | :ref:`module-gdrive` performers have been added: ``FILEADD``,      |
  |                       |              | ``FILEDELETE``.                                                    |
  +-----------------------+--------------+--------------------------------------------------------------------+
  | ``resonance``         | ``1.0.1``    | \-                                                                 |
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
  | ``resonance``         | ``1.0.0``    | \-                                                                 |
  +-----------------------+--------------+--------------------------------------------------------------------+

* **Sep 30, 2015**

  .. cssclass:: table-bordered

  +-----------------------+--------------+--------------------------------------------------------------------+
  | Package               | Version      | Changes                                                            |
  +=======================+==============+====================================================================+
  | ``engine``            | ``1.0.5``    | \-                                                                 |
  +-----------------------+--------------+--------------------------------------------------------------------+
  | ``engine-modules``    | ``1.0.7``    | :ref:`module-instagram` triggers have been added:                  |
  |                       |              | ``NEW-PHOTO-FROM-ME``, ``NEW-PHOTO-FROM-FOLLOWING``,               |
  |                       |              | ``NEW-VIDEO-FROM-ME``, ``NEW-VIDEO-FROM-FOLLOWING``.               |
  +-----------------------+--------------+--------------------------------------------------------------------+
  | ``resonance``         | ``1.0.0``    | \-                                                                 |
  +-----------------------+--------------+--------------------------------------------------------------------+

* **Sep 25, 2015**

  .. cssclass:: table-bordered

  +-----------------------+--------------+--------------------------------------------------------------------+
  | Package               | Version      | Changes                                                            |
  +=======================+==============+====================================================================+
  | ``engine``            | ``1.0.5``    | \-                                                                 |
  +-----------------------+--------------+--------------------------------------------------------------------+
  | ``engine-modules``    | ``1.0.6``    | Allowed usage of Google channels on devices lacking of             |
  |                       |              | ``Play Services``.                                                 |
  +                       +              +--------------------------------------------------------------------+
  |                       |              | :ref:`module-twitter` first implementation  has been added.        |
  +                       +              +--------------------------------------------------------------------+
  |                       |              | :ref:`module-instagram` first implementation  has been added.      |
  +                       +              +--------------------------------------------------------------------+
  |                       |              | :ref:`module-gcalendar` trigger has been added: ``ON-EVENT``.      |
  +-----------------------+--------------+--------------------------------------------------------------------+
  | ``resonance``         | ``1.0.0``    | \-                                                                 |
  +-----------------------+--------------+--------------------------------------------------------------------+
