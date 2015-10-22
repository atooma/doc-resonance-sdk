.. _engine:

Rules Engine
=======================================

Target of this section is to provide details on main classes included in Resonance SDK, with main reference to Rules Engine part.

.. _Rule Definition: #ruledefinition

.. _ruledefinition:

The *RuleDefinition* Class
---------------------------------------

Rules are represented within the execution engine by using instances belonging to the ``RuleDefinition`` class. Going in depth with details about its implementation is not essential. What is important is to understand is how to get a ``RuleDefinition`` instance starting from its XML representation.

For such purpose, Atooma SDK provides an important utility, the ``XMLDeserializer``, that is a class implementing useful methods for properly deserializing XML files. In particular static method ``deserialize`` allows to get a ``RuleDefinition`` instance, starting from its XML representation, as shown below:

.. code-block:: java
  :linenos:

  // getting rule definition with provided id
  RuleDefinition def_1 = XMLDeserializer.deserialize(stream, id);

  // getting rule definition with automatically generated id
  RuleDefinition def_2 = XMLDeserializer.deserialize(stream);

As you can see, two methods are defined for deserializing an XML stream and building the corresponding ``RuleDefinition`` object. Main difference is represented by the possibility of manually defining an identifier for the rule.

Rule identifiers are extremely important because they are used as keys within rule execution engine. This means that same rule can be loaded twice in case of different identifier, while it is loaded only once in case identifier is the same. Be aware that asking ``XMLDeserializer`` to automatically assign a rule identifier has the effect of generating a UUID value for it. That's why it's commonly preferred to define a custom strategy for assigning identifiers to rules (e.g. using an hash of rule title can be a simple example).

The *Atooma* Class
---------------------------------------

Working with Atooma Rules Engine requires developers to use ``Atooma`` singleton class, that is the main interface for accessing all core Atooma features and functionalities, such as loading and unloading rules.

It follows a list of the main methods and properties that can be used for properly configuring and exploiting Atooma class:

.. code-block:: java
  :linenos:

  /**
   * Initialize the engine, loading all provided modules.
   */
  void start(Module[] module)

  /**
   * Load a rule into the Atooma engine, starting from the corresponding
   * xml stream. Returns true in case operation is completed successfully,
   * false otherwise.
   */
  boolean loadRule(InputStream stream, RuleConflictsChecker checker)

  /**
   * Load a rule into the Atooma engine, starting from its Rule Definition.
   * Returns true in case operation is completed successfully, false otherwise.
   */
  void loadRule(RuleDefinition def, RuleConflictsChecker checker)

  /**
   * Returns a map with all active rule definitions, having rule ids
   * as keys.
   */
  Map<String,RuleDefinition> getActiveRules()

  /**
   * Unload rule from engine basing on its id and stopping its execution.
   */
  boolean unloadRule(RuleDefinition def)

  /**
   * Returns true if engine is inited, false otherwise.
   */
  boolean isEngineInited()

  /**
   * Stops engine execution.
   */
  void halt()

The *RuleConflictsChecker* Class
---------------------------------------

Atooma doesn't impose any constraint on rules creation. It means that users / developers can create even rules with potential inconsistencies. For example, let's consider the rule: **IF** (WiFi ON, WiFi OFF) **DO** (Notification Toast). Of course, such rule will never fire, because it implements two opposing conditions.

In order to handle such kind of situations, Atooma provides an extendible class, that is ``RuleConflictsChecker``. Such class is responsible for checking whether a rule loading may create problems or not. In particular, there are two possible high level conflicts to be taken into account:

1. **Internal Conflicts** - An internal conflict occurs when the definition of a rule prevent it from firing (as in the example described before).

2. **External Conflicts** - An external conflict occurs when the definition of a rule has components that can interfere with other rule definitions.

``RuleConflictsChecker`` class implements following methods for encapsulating the whole conflicts verification logic:

.. code-block:: java
  :linenos:

    /**
     * Adds a checker for internal conflicts.
     */
    void addInternalChecker(RuleInternalConflictsChecker checker);

    /**
     * Adds a checker for external conflicts.
     */
    void addExternalChecker(RuleExternalConflictsChecker checker);

    /**
     * Checker for internal conflicts within provided
     * RuleDefinition. true is returned in case rule
     * can be activated anyway, false otherwise.
     */
    boolean checkInternalConflicts(RuleDefinition def);

    /**
     * Checker for external conflicts between provided
     * RuleDefinition and all the other active rules.
     * true is returned in case rule can be activated
     * anyway, false otherwise.
     */
    boolean checkExternalConflicts(RuleDefinition def);

    /**
     * Checker for external conflicts between provided
     * RuleDefinition and the other rules in input.
     * true is returned in case rule can be activated
     * anyway, false otherwise.
     */
    boolean checkExternalConflicts(RuleDefinition def, Collection<RuleDefinition> defs);

Basing on signatures reported above, it is clear that ``RuleConflictsChecker`` class requires additional checkers to be provided to it in order to implement the overall conflicts verification strategy. Purpose of ``RuleInternalConflictsChecker`` and ``RuleExternalConflictsChecker`` classes is to provide an abstraction for internal and external conflict concepts. Providing new conflict verification criteria means extending such classes, by implementing following methods:

.. code-block:: java
  :linenos:

  //RuleInternalConflictsChecker
  abstract boolean hasConflicts(RuleDefinition def);

  // RuleExternalConflictsChecker
  abstract boolean haveConflicts(RuleDefinition def, RuleDefinition other);

Please notice that all conflict checkers are created by providing a boolean parameter that is used for declaring whether rule must be activated regardless outcome of conflicts verification or not.

Let's suppose we would like to check whether multiple rules share the same trigger. Below is reported sample code for implementation of the corresponding ``RuleExternalConflictsChecker``:

.. code-block:: java
  :linenos:

  public class SameTriggerConflictsChecker extends RuleExternalConflictsChecker {

    public SameTriggerConflictsChecker(boolean activateAnywayay) {
      super(activateAnywayay);
    }

    @Override
    protected boolean haveConflicts(RuleDefinition def, RuleDefinition other) {
      TriggerDefinition tr1 = def.getTriggerDefinition();
      TriggerDefinition tr2 = other.getTriggerDefinition();
      // In case rule we are going to activate has same trigger
      // of another active rule, there can be unexpected behaviors
      return tr1.getModule().equals(tr2.getModule()) && tr1.getId().equals(tr2.getId());
    }

  }

In terms of usage, below is reported sample code for activating a rule taking care of multiple conflict verification criterias:

.. code-block:: java
  :linenos:

  RuleConflictsChecker checker = new RuleConflictsChecker();
  checker.addExternalChecker(new SameTriggerConflictsChecker(true));
  // ...
  Atooma.with(context).loadRule(def, checker);

About Opposite Conditions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Atooma SDK provides implementation for two conflict checkers:

* ``SameTriggerConflictsChecker`` extending ``RuleExternalConflictsChecker``

* ``OppositeConditionsConflictsChecker`` extending ``RuleInternalConflictsChecker``

First one was already discussed in previous section, while second one requires some insights. Starting from version 1.0.7, Atooma engine includes a mechanism for easily declaring component that can be considered as opposite. In practice it's enough to use the following declaration in own modules:

.. code-block:: java
  :linenos:

  /**
   * Declares to components as opposite by specifying their ids.
   */
  void registerOppositeConditions(String idOne, String idTwo);

By default following rules are already defined for all modules, including the custom ones created by developers:

.. code-block:: java
  :linenos:

  /**
   * Such declarations must be used within
   * registerComponents method for new modules.
   */
  registerOppositeConditions("ENABLED", "DISABLED");
  registerOppositeConditions("CONNECTED", "DISCONNECTED");
  registerOppositeConditions("ON", "OFF");

  Transition Events
  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  Let's suppose in out Android application we would like to listen for all updates and execute specific code when each of them occur. It's enough to register a ``TransitionEvent`` as follows.

  .. code-block:: java
    :linenos:

    // building event to monitor
    Event event = TransitionEvent.Builder.create()
      .all()                     // all updates
      .doAction(new Action() {   // action to execute
        @Override
        public void execute(ActivityItem from, ActivityItem to) {
          // execute code
        }
    }).build();
    // register event for monitoring
    EventHandler.getInstance().addEvent(mEvent);

  Of course it's possible to exploit ``Builder`` for defining more complex scenarios, as reported in the example below.

  .. code-block:: java
    :linenos:

    // building event to monitor
    Event event = TransitionEvent.Builder.create()
      .from(ActivityItem.ActivityType.CAR)   // transition from Car
      .to(ActivityItem.ActivityType.WALKING) // to Walking
      .doAction(new Action() {               // action to execute
        @Override
        public void execute(ActivityItem from, ActivityItem to) {
            // execute code
        }
    }).build();
    // register event for monitoring
    EventHandler.getInstance().addEvent(mEvent);

  Duration Events
  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  ``DurationEvent`` objects can be used in all situations where we would like to receive updates on activities that are on progress. Let's suppose for example we would like to be notified when driving car for more than 1 hour. We can proceed as follows:

  .. code-block:: java
    :linenos:

    // building event to monitor
    Event event = DurationEvent.Builder.create()
      .of(ActivityItem.ActivityType.CAR)      // activity Car
      .isMoreThan(TimeUnit.HOURS.toMillis(1)) // for more than 1 hour
      .doAction(new Action() {                // action to execute
        @Override
        public void execute(ActivityItem from, ActivityItem to) {
            // execute code
        }
    }).build();
    // register event for monitoring
    EventHandler.getInstance().addEvent(mEvent);

  Handling Events
  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  Looking at code shown in past sections it is obvious that ``EventHandler`` is a singleton class used for registering events to be monitored. It's important to highlight that such class not only provides ``addEvent()`` method, but also ``removeEvent()`` method. This method is used for removing events in specific contexts, for example when monitoring should occur within an ``Activity`` only.

  .. code-block:: java
    :linenos:

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


  Accessing activity history
  ---------------------------------------

  Activity tracking library automatically sends data concerning user daily activities to Atooma backend for processing, building history and processing outcomes available through an easy to use programming interface.

  Let's suppose for example we would like to get daily activities for current day. We can exploit following code:

  .. code-block:: java
    :linenos:

    // building java.util.Date to retrieve activities for
    Date date = ...
    // building listener for getting list of ActivityItem objects
    AdvisedElementsResponseHandler<ActivityItem> listener = ...
    // getting resonance advisor
    Context context = getApplicationContext();
    ResonanceAdvisor advisor = ResonanceApiClient.with(context).getAdvisor();
    advisor.getDailyActivities(date, listener);

  Please notice that more details on ``ResonanceAdvisor`` class will be provided in section :ref:`resonance-advisor`.

  Interface AdvisedElementsResponseHandler is used by ``ResonanceAdvisor`` for asynchronously returning lists of objects. It's enough in this sense to implement method ``onAdvisedElementsRetrievedListener``, taking the list of returned elements as input param:

  .. code-block:: java
    :linenos:

    AdvisedElementsResponseHandler<ActivityItem> listener =
      new AdvisedElementsResponseHandler<>() {
        @Override
        public void onAdvisedElementsRetrievedListener(List<ActivityItem> activities) {
          // do something with activities here
        }
      };

  Returned list of ``ActivityItem`` instances is an objects based representation for a timeline, as shown in the following image.

  .. figure:: _static/img/activity/timeline.png
     :width: 250 px
     :alt: Daily Activities

  More details on timeline implementation are reported in section :ref:`example-timeline`.
