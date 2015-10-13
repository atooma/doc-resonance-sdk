.. _example:

Building Own Applications
=================================================

Target of this section is giving details on steps to follow for creating a Resonance based application from scratch. Some examples will be also provided for showing how Resonance SDK can be used for simplifying development of specific tasks.

Few steps are required for initializing the environment:

1. Gradle configuration
2. Manifest configuration
3. Initialization of ``ResonanceApiClient``

All these steps are described in next sections.

Gradle configuration
---------------------------------------

As a first step, ``build.gradle`` script in your app module requires to include following dependencies:

.. code-block:: groovy
  :linenos:

  compile 'com.atooma.resonance:core:1.0.1'
  compile 'com.atooma.activitytracker:activitytracker:1.0.1'
  compile 'com.atooma.resonance:resonance:1.0.1'

Moreover, top level ``build.gradle`` file must include credentials for accessing to Atooma packages:

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

Please notice that you will get credentials by sending a brief presentation of yourself/Company and your project/app to :email:`developers@atooma.com` reporting as **Subject**: *ATOOMA RESONANCE SDK REQUEST - Name of the Company/Yourself*.

Together with credentials, you will also receive an ``Application Id``, that will be used in Manifest, as described in :ref:`example-manifest`.

.. _example-manifest:

Manifest configuration
---------------------------------------

Manifest requires configuration for handling data collector functionalities.

.. code-block:: xml
  :linenos:

  <meta-data
    android:name="com.atooma.resonance.sdk.ApplicationId"
    android:value="YOUR_ID" />

  <service
    android:name="com.atooma.resonance.ResonanceCollectorService"
    android:exported="false"
    android:process=":datacollector" />

  <provider
    android:name="com.atooma.resonance.provider.SnapshotProvider"
    android:authorities="com.atooma.datacollector.snapshotsYOUR_ID"
    android:exported="false" />

  <receiver
    android:name="com.atooma.resonance.sender.DataSenderTimerReceiver"
    android:exported="false"
    android:process=":datacollector" />

  <receiver
    android:name="com.atooma.resonance.sender.TimelineSenderTimerReceiver"
    android:exported="false" />

Using a dedicated private process for ``ResonanceCollectorService`` and ``DataSenderTimerReceiver`` is a choice made for keeping data collector load into a dedicated space. It's possible in any case to use main process without problems.

Working with Resonance API Client
---------------------------------------

Usage of Resonance SDK relies on creation of ``ResonanceApiClient`` class. You can easily use it in your ``Application`` class as shown below.

.. code-block:: java
  :linenos:

  ResonanceApiClient.with(getApplicationContext()).start();

``start`` method will have the effect of starting to collect data to be delivered to server. Moreover, additional features will be provided for getting detailed information about user.

More details on how to properly start ``ResonanceApiClient`` are available in section :ref:`resonance-class`.

Examples
---------------------------------------

This section provides some usage examples.

Parking reminder
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

It's enough to register following event within your *Application* class and implement logic of ``execute`` method.

.. code-block:: java
  :linenos:

  // building event to monitor
  Event event = TransitionEvent.Builder.create()
    .from(ActivityItem.ActivityType.CAR)   // transition from Car
    .toAll()                               // to any activity
    .doAction(new Action() {               // action to execute
      @Override
      public void execute(ActivityItem from, ActivityItem to) {
          LocationWrapper location = from.getLocation();
          // use location data
      }
  }).build();
  // register event for monitoring
  EventHandler.getInstance().addEvent(mEvent);
