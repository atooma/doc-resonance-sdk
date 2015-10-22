.. _example:

Appendix 4 - Resonance Examples
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

  compile 'com.atooma:core:1.0.1'
  compile 'com.atooma:activitytracker:1.0.1'
  compile 'com.atooma:resonance:1.0.1'

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

.. _example-timeline:

Timeline
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This section provides details on an activity tracking application, built using Resonance SDK. Source code is available `here <https://github.com/atooma/android-resonance-sdk-samples>`_ on GitHub. Idea is to create a personal tracker, displaying current user activity as well as activity recorded for past days, using a ``ViewPager`` for organizing data on multiple fragments.

.. figure:: _static/img/activity/timeline.png
   :width: 250 px
   :alt: Daily Activities

Most interesting part is of course represented by ``TimelineFragment`` class, that encapsulates the main logic for accessing history and displaying real time information.

Below is reported a simplified implementation for ``loadData()`` method. It basically exploits ``ResonanceAdvisor`` for retrieving and showing data belonging to date provided in input.

.. code-block:: java
  :linenos:

  private void loadData(Date date) {
    mResonanceApiClient.getAdvisor().getDailyActivities(date,
        new AdvisedElementsResponseHandler<ActivityItem>() {
          @Override
          public void onAdvisedElementsRetrievedListener(List<ActivityItem> activities) {
            // updating dataset to show in ListView or RecyclerView
            mDataset.clear();
            mDataset.addAll(activities);
            mAdapter.notifyDataSetChanged();
          }
        });
  }

Of course, in case provided date is current one, it's important to update timeline in real time. That's why ``TimelineFragment`` registers a couple of activity tracking events to be monitored by Resonance, as shown below:

.. code-block:: java
  :linenos:

  // class instance variables
  private TransitionEvent mTransitionEvent;
  private DurationEvent mDurationEvent;

  // ...

  // implementation within onCreate method
  // mDate is date linked with current fragment
  mTransition = TransitionEvent.Builder.create()
      .all()
      .doAction(new Action() {
        @Override
        public void execute(ActivityItem from, ActivityItem to) {
          loadData(mDate);
        }
      }).build();

  mDurationEvent = DurationEvent.Builder.create()
      .all()
      .doAction(new Action() {
        @Override
        public void execute(ActivityItem from, ActivityItem to) {
          loadData();
        }
      }).build();

  // ...

  // register for updates in onResume, handling
  // updates only if date is today
  if (isToday()) {
    EventHandler.getInstance().addEvent(mTEvent);
    EventHandler.getInstance().addEvent(mDEvent);
    // ...
  }

Monitoring Battery Usage
---------------------------------------

Target of this section is providing a detailed guideline on how to track and display battery usage, with reference to specific running applications. Please notice that all steps mentioned in following sections require a device equipped with Android 5.0 or higher.

For further information on tools and strategies you can always refer to official Android Developers website, `here <https://developer.android.com/tools/performance/batterystats-battery-historian/index.html>`_ and `here <https://developer.android.com/tools/performance/batterystats-battery-historian/charts.html>`_.

Getting Stats from Device
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In order to get stats from your device, enable *debugging mode* by accessing to *Developer Options* section within *Settings > System*. Once this step is completed, connect device to your computer and execute following shell commands:

.. code-block:: bash
  :linenos:

  $ adb shell dumpsys batterystats or adb shell dumpsys batterystats --enable full-wake-history
  $ adb bugreport > bugreport.txt

Use command ``adb shell dumpsys batterystats --reset`` for resetting battery stats, once dump has been taken.
Please notice that by enabling full wakelock reporting the battery history log overflows in a few hours. Use this option for short test runs (3-4 hrs).

Displaying Captured Data
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

`Battery Historian Tool <https://github.com/google/battery-historian>`_ can be effectively used for displaying battery stats dumps captured on device. In order to use it you can run a server locally by setting up your environment for using `Go <https://golang.org/>`_ language:

.. code-block:: bash
  :linenos:

  $ go run cmd/battery-historian/battery-historian.go

Once server is running you can open browser, access url *http://localhost:9999*, then upload your *bugreport.txt*.
In the first page look into Top power consuming entities to see Atooma's impact on the battery.
Tab Historian 2.0 allows to see duration of each individual wakelock within com.atooma.wl process.

Alternatively it's possible to use `Docker <https://www.docker.com/>`_ and exploit an image ready with Battery Historian Tool and available on a dedicated `Bintray <https://bintray.com/>`_ repository:

.. code-block:: bash
  :linenos:

  $ docker login -u username -p apikey -e email atooma-docker-images.bintray.io
  $ docker pull atooma-docker-images.bintray.io/atooma/battery-historian
  $ docker run -d -p 8080:8080 atooma-docker-images.bintray.io/atooma/battery-historian

where:

* **username** is the username of your Bintray account
* **apikey** is the apikey of your Bintray account
* **email** is the email of your Bintray account

You can then open browser and access url *http://localhost:8080* (or virtual machine ip address if on OS X).
