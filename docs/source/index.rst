Welcome to Resonance SDK's documentation!
=========================================

Welcome to the official reference for developing with Resonance SDK powered by Atooma.

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
	commons
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

Below is reported current stable Gradle configuration for Resonance SDK Android Libraries.

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
