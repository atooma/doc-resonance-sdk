.. _core:

Core Concepts
=======================================

Target of this section is to provide a high level description of the core concepts behind the Resonance environment. Focus will be on SDK main features and how to exploit them.


Activity Tracker
---------------------------------------

Resonance SDK comes with an integrated library for simplifying user activity detection. It has two main purposes:

1. Listening for event updates (e.g. user starts driving, user starts biking after walking, and so on).
2. Accessing information concerning tracked daily activities.

Following sections describe more in depth both features, providing detailed examples.

Monitoring Events
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Activity tracker allows to listen for updates on user activity, with possibility of effectively monitoring specific transitions.

Below is reported the list of possible events that library is currently able to handle.

* Walking
* Running
* Biking
* Driving
* Still

Let's suppose in out Android application we would like to listen for all updates and execute specific code when each of them occur. It's enough to register an event as follows.

.. code-block:: java

    // building event to monitor
    Event event = Event.Builder.create()
      .all()                     // all updates
      .doAction(new DoAction() { // action to execute
        @Override
        public void execute() {
            // execute code
        }
    }).build();
    // register event for monitoring
    EventHandler.getInstance().addEvent(mEvent);

Of course it's possible to exploit *Builder* for defining more complex scenarios, as reported in the example below.

.. code-block:: java

  // building event to monitor
  Event event = Event.Builder.create()
    .from(ActivityItem.ActivityType.CAR)   // transition from Car
    .to(ActivityItem.ActivityType.WALKING) // to Walking
    .doAction(new DoAction() {             // action to execute
      @Override
      public void execute() {
          // execute code
      }
  }).build();
  // register event for monitoring
  EventHandler.getInstance().addEvent(mEvent);

*EventHandler* provides an additional method *removeEvent* for eventually removing events in specific contexts, for example when monitoring should occur within an Activity only.

.. code-block:: java

  private Event mEvent;

  @Override
  public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mEvent = // build your event here
  }

  @Override
  public void onResume() {
    super.onResume();
    EventHandler.getInstance().addEvent(mEvent);
    // ...
  }

  @Override
  public void onPause() {
    super.onPause();
    EventHandler.getInstance().removeEvent(mEvent);
    // ...
  }

There are specific constraints on order of methods for building events to monitor.

Accessing activity history
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Activity tracking library automatically stores data concerning user daily activities, making them available through an easy to use interface.

Let's suppose for example we would like to get daily activities for current day. We can exploit following code:

.. code-block:: java

  Context context = getApplicationContext();
  ActivityTracker tracker = ResonanceApiClient.with(context).getActivityTracker();
  List<ActivityItem> activities = tracker.getActivityStore().getTodayActivities(true);

Where boolean parameter of *getTodayActivities* method simply reflects sorting strategy for returned items.

Additional methods are available for getting data of past days.


Data Collector
---------------------------------------

When starting *ResonanceApiClient* instance, ResonanceSDK automatically starts collecting data concerning device usage. Data are periodically delivered to server and allow to perform analysis on user behavior, determining relevant information about his preferences.

Advisor
---------------------------------------

Data analysis performed on Atooma backend is used in advanced functions provided to clients for getting user information.
