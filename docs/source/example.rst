.. _example:

Building applications with Resonance SDK
=================================================

Target of this section is providing some examples, showing in depth how Resonance SDK can be used for simplifying development of specific tasks.

Few steps are required for initializing the environment within own application.

Gradle configuration
---------------------------------------

.. code-block:: groovy

  compile 'com.atooma.activitytracker:activitytracker:1.0.0'
  compile 'com.atooma.resonance:resonance:1.0.0'


Manifest configuration
---------------------------------------

.. code-block:: xml

  <meta-data android:name="com.atooma.resonance.sdk.ApplicationId" android:value="YOUR_ID" />

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

  <service
      android:name="com.atooma.resonance.sender.DataSenderDemandService"
      android:exported="false"
      android:process=":datacollector" />


Sample application
---------------------------------------

.. code-block:: java

  ResonanceApiClient.with(getApplicationContext()).start();
