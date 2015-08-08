.. _core:

Core Concepts
=======================================

Target of this section is to provide a high level description of the core concepts behind the Resonance environment.


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
