.. _example:

Building Own Applications
=================================================

Target of this section is providing some examples, showing in depth how Resonance SDK can be used for simplifying development of specific tasks.

Few steps are required for initializing the environment within own application.

Gradle configuration
---------------------------------------

.. code-block:: groovy
  :linenos:

  compile 'com.atooma.activitytracker:activitytracker:1.0.0'
  compile 'com.atooma.resonance:resonance:1.0.0'


Manifest configuration
---------------------------------------

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

Usage of Resonance SDK relies on creation of ``ResonanceApiClient`` class. You can easily use it as follows in your ``Application`` class.

.. code-block:: java
  :linenos:

  ResonanceApiClient.with(getApplicationContext()).start();

``start`` method will have the effect of starting to collect data to be delivered to server. Moreover, additional features will be provided for getting detailed information about user.

Implementing a parking reminder
---------------------------------------

It's enough to register following event within your *Application* class and implement logic of ``execute`` method.

.. code-block:: java
  :linenos:

  // building event to monitor
  Event event = Event.Builder.create()
    .from(ActivityItem.ActivityType.CAR)   // transition from Car
    .toAll()                               // to any activity
    .doAction(new Action() {               // action to execute
      @Override
      public void execute(ActivityItem from, ActivityItem to) {
          // execute code
      }
  }).build();
  // register event for monitoring
  EventHandler.getInstance().addEvent(mEvent);
