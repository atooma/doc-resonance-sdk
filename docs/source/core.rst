.. _core:

Core Concepts
=======================================

Target of this section is to provide a high level description of the core concepts behind the Resonance environment. Focus will be on SDK main features and how to exploit them.


Activity Tracker
---------------------------------------

Monitoring Events
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Resonance activity tracker allows to listen for updates on user activity, with possibility of effectively monitoring specific transitions.

Below is reported the list of possible events that library is currently able to handle.

* Walking
* Running
* Biking
* Driving
* Still

Let's suppose in our Ancroid Activity / Fragment we would like to listen for all activity updates and execute specific code when an update occurs. Code would be as follows:

.. code-block:: java

  private Event mEvent;

  @Override
  public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mEvent = Event.Builder.create().all().doAction(new DoAction() {
        @Override
        public void execute() {
            // execute code
        }
    }).build();
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

In case, it's possible also to monitor specific transitions. For example:

.. code-block:: java

  mEvent = Event.Builder.create()
    .from(ActivityItem.ActivityType.CAR)
    .to(ActivityItem.ActivityType.WALKING)
    .doAction(new DoAction() {
      @Override
      public void execute() {
          // execute code
      }
  }).build();

There are specific constraints on order of methods for building events to monitor.

Accessing activity history
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Data Collector
---------------------------------------

Advisor
---------------------------------------
