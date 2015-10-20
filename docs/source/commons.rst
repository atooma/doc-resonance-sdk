.. _commons:

Commons Library
=======================================

Atooma SDK provides on a set of reusable components, packed into a specific Commons Library. An overview of (some of) such components is reported below:

+---------------------------------------+------------------------------------------------------------------------------------------+
| Class                                 | Description                                                                              |
+=======================================+==========================================================================================+
| :ref:`class-locationsmanager`         | Singleton allowing to manage locations through access to specific content provider.      |
+---------------------------------------+------------------------------------------------------------------------------------------+
| `OAuth1Activity OAuth2Activity`_      | Activities used for simplifying management of the the OAuth 1 & 2 flows.                 |
+---------------------------------------+------------------------------------------------------------------------------------------+

.. _class-locationsmanager:

LocationsManager
---------------------------------------

Atooma Commons Library includes a Content Provider allowing to manage locations to be used within :ref:`module-location` module. Including such provider in own application is extremely simple, since it requires just a declaration in Android Manifest and a directive in Application class as follows:

.. code-block:: xml
  :linenos:

  <provider
    android:name="com.atooma.commons.location.LocationsProvider"
    android:authorities="com.atooma.sdk.sample.locations"
    android:exported="false"
    android:enabled="true" />

.. code-block:: java
  :linenos:

  @Override
  public void onCreate() {
    super.onCreate();
    // defining locations provider authority
    LocationsProvider.setAuthority("com.atooma.sdk.sample.locations");
    // ...
  }

Please notice that authority is defined by developers. It's just important that same value is assigned both in Android Manifest and Application class.

.. note:: In case authority is not defined in Application class, ``AuthorityNotSetException`` is thrown

Once configuration is completed, it's possible to exploit singleton class ``LocationsManager`` for reading / writing locations. Such class includes following methods:

.. code-block:: java
  :linenos:

  /**
   * Returns a location from storage, searching by it's label.
   * Returns null in case no location is found.
   */
  LocationWrapper readLocationByLabel(Context context, String label)

  /**
   * Returns all location from storage.
   */
  List<LocationWrapper> readLocationsList(Context context)

  /**
   * Adds a location to the storage. Method returns an exception
   * in case location already exists.
   */
  void addLocation(Context context, LocationWrapper wrapper)

  /**
   * Update an existing location already available in storage,
   * basing on its label. Method throws an exception in case
   * location does not exist.
   */
  void updateLocation(Context context, LocationWrapper wrapper)

  /**
   * Deletes provided locations from storage, basing on their
   * labels.
   */
  void deleteLocations(Context context, List<LocationWrapper> locations)

  /**
   * Deletes all locations from storage.
   */
  void clearLocations(Context context)


.. _OAuth1Activity OAuth2Activity: programming.html#devperformers

OAuth 1 & 2
---------------------------------------

Activities used for simplifying management of the the OAuth 1 & 2 flows. Additional details to be provided soon.

.. _class-md5:
