.. _examples:

Building Own Applications
=======================================

Target of this section is providing some examples, showing in depth how Resonance SDK can be used for simplifying development of specific tasks.

All examples reported in next sections require your environment to be configured according to specifications reported in sections :ref:`releases-stable` and :ref:`manifest-stable`.

.. note:: Code for all examples is available on following GitHub repositories:

  * `Rules Engine Examples <https://github.com/atooma/atooma-engine-sdk-samples>`_.

  * `Resonance Examples <https://github.com/atooma/android-resonance-sdk-samples>`_.

Example 0 - Rule Engine Basics
---------------------------------------

Let's suppose we would like to create a simple rule allowing to show a toast notification when connecting to a specific WiFi network.

Atooma SDK already provides a couple of modules that can be combined for such scope: :ref:`module-wifi` and :ref:`module-notification`. It's easily possible to exploit them for building the rule described by the XML below:

.. code-block:: xml
  :linenos:

  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <rule xmlns="http://www.atooma.com/sdk/rule" version="1">
    <title>Notify when connecting to WiFi</title>
    <description>Shows a notification when connecting to WiFi</description>
    <required-module version="1" id="CORE" />
    <required-module version="1" id="WIFI" />
    <required-module version="1" id="NOTIFICATION" />
    <property literal="string" typeId="STRING" typeModule="CORE" id="par-0">
      home_wifi
    </property>
    <property literal="string" typeId="STRING" typeModule="CORE" id="par-1">
      Connected to home WiFi!
    </property>
    <trigger id="CONNECTED" module="WIFI">
      <parameter id="SSID">
        <property-ref id="par-0" />
      </parameter>
    </trigger>
    <performer id="TOAST" module="NOTIFICATION">
      <parameter id="TEXT">
        <property-ref id="par-1" />
      </parameter>
    </performer>
  </rule>

For having such rule running on a own application, it's commonly enough to implement an *Application* class similar to the one reported below:

.. code-block:: java
  :linenos:

  public class WhiteLabelApplication extends Application {

    @Override
    public void onCreate() {
      super.onCreate();
      // starting rules engine (if needed)
      if (!Atooma.with(this).isEngineInited()) {
        // getting core modules
        Module[] modules = Modules.getBasicModules();
        // initializing Atooma engine
        Atooma.with(this).init(context, MainActivity.class, modules);
        // loading specific rules from assets folder
        loadRule("wifi_notification.xml");
      }
    }

    /**
     * Support method allowing to load a rule from assets.
     */
    private void loadRule(String rule) {
      // trying to load all rules
      try {
        // loading xml with rule definition
        InputStream stream = getAssets().open(xml);
        // loading rule definition from xml
        Atooma.with(this).loadRule(stream);
      } catch (IOException ex) {
        Log.e("Atooma", "unable to open xml from assets: " + xml);
      }
    }

  }


Example 1 - External Providers
--------------------------------------------

Let's now suppose we would like to take rule previously defined and dynamically adapt WiFi network name to device / user. In such case we cannot statically define it within rule definition. We need instead to use a proper provider class and use it as reported in the example below:

.. code-block:: xml
  :linenos:

  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <rule xmlns="http://www.atooma.com/sdk/rule" version="1">
    <title>Notify when connecting to home WiFi</title>
    <description>Shows a notification when connecting to home WiFi</description>
    <required-module version="1" id="CORE" />
    <required-module version="1" id="WIFI" />
    <required-module version="1" id="NOTIFICATION" />
    <property literal="string" typeId="STRING" typeModule="CORE" id="par-1">
      Connected to home WiFi!
    </property>
    <trigger id="CONNECTED" module="WIFI">
      <parameter id="SSID">
        <external-provider-call
          className="com.atooma.sample.WiFiProvider"
          methodName="getSSID" />
      </parameter>
    </trigger>
    <performer id="TOAST" module="NOTIFICATION">
      <parameter id="TEXT">
        <property-ref id="par-1" />
      </parameter>
    </performer>
  </rule>

WiFi network name will be defined by using static method ``getSSID()`` from class ``WiFiProvider``, that will be implemented as follows:

.. code-block:: java
  :linenos:

  public class WifiProvider {

    public static VT_String_Wrapper getSSID() {
      String ssid = getHomeWiFi();
      return new VT_String_Wrapper(ssid);
    }

    private static String getHomeWiFi() {
      String ssid = "";
      // logic for getting home wifi name here
      // (e.g. from shared preferences or sqlite)
      return ssid;
    }

  }

Please notice that class ``VT_String_Wrapper`` is a wrapper for ``STRING`` data type, defined within Atooma SDK.

.. _examples-two:

Example 2 - Custom Modules
---------------------------------------

Let's suppose we would like to create an application allowing to change device wallpaper when plugging headphones. Wallpaper should be defined according to current location.

Basing on provided description, this is just a simple rule with one trigger and one performer:

* Event condition is already handled by Atooma SDK through the :ref:`module-headphone` module, so we don't need to implement anything.
* Performer part requires the implementation of a dedicated module. We can think about an integration with ``FLICKR``, allowing to get photos from a specific Flickr group, basing on location coordinates provided by devices.

Analyzing in depth the implementation of backend is out of our scope. Let's just imagine to have the following endpoint, working as an indirection between Android clients and Flickr server:

.. code-block:: java

	GET http://www.mydomain.coma/api/flickr/pictures/<lat>/<lon>

What we need to do is defining a proper Flickr module, implementing one single component, that is the required performer ``PE_Wallpaper``.

.. code-block:: java
  :linenos:

  public class _FLICKR extends Module {

    private static final String MODULE_ID = "FLICKR";

    private static final int MODULE_VERSION = 1;

    public _FLICKR() {
      super(MODULE_ID, MODULE_VERSION);
    }

    @Override
    protected void declareDependencies() {
      declareDependency("CORE", 1);
    }

    @Override
    protected boolean init() {
      return true;
    }

    @Override
    protected void registerComponents() {
      registerPerformer("WALLPAPER", 1, new PE_SetWallpaper());
    }

    @Override
    protected void destroy() {
    }

  }

Rule definition will be as follows:

.. code-block:: xml
  :linenos:

  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <rule xmlns="http://www.atooma.com/sdk/rule" version="1">
    <title>Wallpaper on demand</title>
    <description>Update wallpaper when plugging headphone</description>
    <required-module version="1" id="CORE" />
    <required-module version="1" id="HEADPHONE" />
    <required-module version="1" id="FLICKR" />
    <trigger id="HEADPHONE-PLUGGED" module="HEADPHONE" />
    <performer id="WALLPAPER" module="FLICKR" />
  </rule>

For details on the implementation of requested Flickr performer you can refer to code available `here <https://github.com/atooma/atooma-engine-sdk-samples>`_ on GitHub.

Modules with Authentication
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Let's suppose we would like to implement a module as an integration for an external service (e.g. Facebook, Gmail and so on). In such case it's always essential to include an implementation for the authentication workflow (commonly based on OAuth).

Atooma SDK comes with some classes allowing to simplify management of such workflow, providing some utilities for activating / deactivating external services connection as well as for checking whether connection is active or not.

.. code-block:: java
  :linenos:

  /**
   * Class used for handling connection / disconnection operations,
   * with reference to a specific external service, called Channel.
   */
  abstract class ChannelHandler

  /**
   * Class used for representing an Atooma Module implementing
   * an integration with an external service, so requiring a
   * dedicated ChannelHandler to be provided.
   */
  abstract class ChannelModule

  /**
   * Singleton class used for managing all available
   * channel handlers.
   */
  class ChannelsManager

Let's go in depth with ``ChannelHandler`` first. Following methods must be implemented:

.. code-block:: java
  :linenos:

  /**
   * Returns the id of module that this ChannelHandler is
   * aimed to handle.
   */
  abstract protected String getModuleId();

  /**
   * Execute code for connecting to Channel. This commonly
   * consists in access to a specific authentication Activity.
   */
  abstract protected void doConnection(Activity source);

  /**
   * Implements logic for extracting token and username
   * from result intent.
   */
  abstract protected void extractAndSaveDataFromResult(Context context, Intent data);

Additional methods are available for getting information like stored token.

It follow a sample implementation for Gmail:

.. code-block:: java
  :linenos:

  @Override
  protected void doConnection(Activity source) {
    Intent intent = new Intent(source, GmailActivity.class);
    source.startActivityForResult(intent, 8888);
  }

  @Override
  public boolean isConnected(Context context) {
    return !TextUtils.isEmpty(getToken(context));
  }

  @Override
  protected String getModuleId() {
    return "GMAIL";
  }

  @Override
  protected void extractAndSaveDataFromResult(Context context, Intent data) {
    // getting params
    String username = data.getStringExtra(GoogleOAuth2Activity.PARAM_EMAIL);
    String token = data.getStringExtra(GoogleOAuth2Activity.PARAM_TOKEN);
    // store params
    saveUser(context, username);
    saveToken(context, token);
  }

Moving to ``ChannelModule`` class, it's just important to properly implement following abstract methods:

.. code-block:: java
  :linenos:

  /**
   * Returns the id of the ChannelHandler. It's common
   * to use module id for this step.
   */
  abstract protected String getChannelHandlerKey();

  /**
   * Returns a ChannelHandler instance.
   */
  abstract protected ChannelHandler buildChannelHandler();

In case of Gmail we'll have something like this:

.. code-block:: java
  :linenos:

  @Override
  protected String getChannelHandlerKey() {
    return Channels.GMAIL;
  }

  @Override
  protected ChannelHandler buildChannelHandler() {
    return new GmailChannelHandler();
  }

As already discussed, purpose of ``ChannelHandler`` classes is not only to activate / deactivate a connection. They can also be used through ``ChannelHandler`` for checking whether a channel is activated or not. This is essential when trying to load a rule that include modules interacting with external services. Of course in case some channels are not activated it's possible that rule cannot be activated too.

Below is reported a code excerpt, showing how to activate rules depending on used modules:

.. code-block:: java
  :linenos:

  //...

  Atooma atooma = Atooma.with(context);
  ChannelsManager cm = ChannelsManager.with(context);
  try {
      if (atooma.getActiveRules().containsKey(id)) {
          Log.d(LOG_TAG, "already active rule: " + xml);
          return;
      }
      // loading xml with rule definition
      InputStream stream = context.getAssets().open(xml);
      // building rule definition
      RuleDefinition def = XMLDeserializer.deserialize(stream, id);
      if (cm.isActivable(def)) {
          // loading rule definition into engine
          atooma.loadRule(def);
          Log.d(LOG_TAG, "activated rule: " + xml);
      } else {
          Log.d(LOG_TAG, "unable to activate rule: " + xml);
      }
  } catch (IOException ex) {
      Log.e(LOG_TAG, "unable to open xml from assets: " + xml);
  } catch (XMLRuleException ex) {
      Log.e(LOG_TAG, "unable to deserialize xml: " + xml);
  }

Example 3 - XML As A Template
---------------------------------------

Let's suppose we would like to implement an application allowing to show a notification when entering into specific locations (e.g. Home, Work and so on). Application will expose a single ``Activity`` allowing to check active rules and create new rules based on current location, as reported in the screenshot below.

.. figure:: _static/img/example.png
   :width: 500 px
   :alt: Sample Application
   :align: center

Of course we can rely on existing modules :ref:`module-location` and :ref:`module-notification` for implementing logic, but here we are going to use a rule definition in XML format as a template for multiple rules, instead of building just a simple rule definition from it.

Using an XML as a template requires data to be dynamic and External Providers are the right mechanism for dealing with dynamic data. Our XML will be as follows:

.. code-block:: xml
  :linenos:

  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <rule xmlns="http://www.atooma.com/sdk/rule" version="1">
    <title>Notify when entering location</title>
    <description>-</description>
    <required-module version="1" id="CORE" />
    <required-module version="3" id="LOCATION" />
    <required-module version="1" id="NOTIFICATION" />
    <trigger id="IN" module="LOCATION">
      <parameter id="AREA">
        <external-provider-call
          className="com.atooma.sdk.samples.providers.OwnLocationsProvider"
          methodName="getArea" />
      </parameter>
    </trigger>
    <performer id="TOAST" module="NOTIFICATION">
      <parameter id="TEXT">
        <external-provider-call
          className="com.atooma.sdk.samples.providers.NotificationsProvider"
          methodName="getMessage" />
      </parameter>
    </performer>
  </rule>

The real issue here is understanding how to handle locations as well as to ensure that multiple rule definitions are created from XML basing on such locations:

* :ref:`xml-template-strategy` describes an effective strategy for exploiting an XML template, handling corresponding rules data.
* :ref:`xml-template-classes` provides details on SDK classes, preventing developer from dealing with complexity of template data management.

.. _xml-template-strategy:

Strategy In Depth
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

As a first step we can proceed by exploiting ``LocationsManager`` class that is available in :ref:`commons`. Once local storage is populated with some location objects, what we can easily do is defining a method iterating over all locations and loading rules from XML template into the execution engine.

.. code-block:: java
  :linenos:

  /**
   * Support method for loading location based rules
   * starting from locations provider
   */
  public static void loadLocationRules(Context context, String template) {
    try {
      // getting singleton for accessing locations provider
      LocationsManager lm = LocationsManager.getInstance();
      // getting list of locations
      List<LocationWrapper> locations = lm.readLocationsList(context);
      // iterating over locations
      for (LocationWrapper location : locations) {
        // loading rule based on location
        loadRule(context, template, location);
      }
    } catch (AuthorityNotSetException ex) {
      Log.e(LogTags.LOCATION, ex.getMessage(), ex);
    }
  }

Let's now go in depth with definition of ``loadRule`` method reported in code above. Idea is quite simple. Since we need to load rules one by one, it's enough to push each location into a queue handled by the ``OwnLocationsProvider`` class (that is the external provider class reported in the XML) and then to request Atooma SDK to load the rule. When building it, ``getArea`` method within ``OwnLocationsProvider`` will be invoked, returning the head of the queue.

.. figure:: _static/img/locations_provider.png
   :alt: Interaction Model
   :align: center

Below is reported the implementation of the method.

.. code-block:: java
  :linenos:

  /**
   * Support method for loading a location based rule encoded
   * by the provided xml template into engine, returning a
   * boolean value telling if operation was successfully
   * completed or not.
   */
  public static boolean loadRule(Context context, String xml, LocationWrapper location) {
    // can activate rule?
    if (OwnLocationsProvider.pushLocation(location)) {
      // we are using a template, so we must use location
      // as seed for building rule id
      String id = MD5.compute(xml) + location.getLabel();
      loadRule(context, xml, id);
      return true;
    }
    return false;
  }

Please notice that here we are using a custom identifier for rules, that is made by combining a hash of rule title (that is always the same since we are using the same XML template) with location label. With this strategy we are sure that same rule (or better, same rule on same location) won't be loaded twice.

Here it is the implementation of the ``OwnLocationsProvider`` class:

.. code-block:: java
  :linenos:

  public class OwnLocationsProvider {

    private static final String LOG_TAG = "Location";

    /**
     * Collection used for putting locations to be used
     * as output for getArea method
     */
    private static List<LocationWrapper> LOCATIONS_QUEUE = new ArrayList<>();

    /**
     * Collection used for putting locations already used in
     * with current rule
     */
    private static List<LocationWrapper> ACTIVE_RULES = new ArrayList<>();

    /**
     * Provider method used in xml rule location_notification.xml
     */
    public static VT_Area_Wrapper getArea() {
      LocationWrapper wrapper = popLocation();
      if (wrapper != null) {
        return new VT_Area_Wrapper(wrapper);
      } else {
        Log.e(LOG_TAG, "missing location parameter");
        return null;
      }
    }

    /**
     * Add a location to the collection storing the ones
     * to be used for activating rules
     */
    public static synchronized boolean pushLocation(LocationWrapper location) {
      // putting in queue just in case rule doesn't exist yet
      boolean toPush = !ACTIVE_RULES.contains(location);
      if (toPush) {
        ACTIVE_RULES.add(location);
        LOCATIONS_QUEUE.add(location);
      }
      return toPush;
    }

    /**
     * Get a location from the collection storing the ones
     * to be used for activating rules, then removes it from
     * such collection
     */
    public static synchronized LocationWrapper popLocation() {
      LocationWrapper location = null;
      int index = LOCATIONS_QUEUE.size() - 1;
      if (index >= 0) {
        location = LOCATIONS_QUEUE.get(index);
        LOCATIONS_QUEUE.remove(index);
      }
      return location;
    }

  }

.. _xml-template-classes:

SDK Classes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Dealing with rules data in case of XML templates may be tricky. That's why Atooma SDK comes with some useful classes that help developers with such task. Please notice that these classes are available starting from SDK libraries version 1.0.1.

Let's suppose we would like to handle location based rules mentioned in previous section. What we need to do is to extend following classes:

.. code-block:: java
  :linenos:

  /**
   * Representation of template rules data. In our case, implementation
   * of this class should include just management of a Location object.
   */
  class TemplateRule

  /**
   * Representation of the queue for rules data. It mus implement
   * method getWrapperObjects, that is aimed to provide a map with
   * all values to be pushed into rule with an external provider call.
   */
  class TemplateRuleDataQueue<T extends TemplateRule>

  /**
   * Representation of the storage for rules data. It is
   * currently implemented by using SharedPreferences, but
   * in future multiple implementations will be provided.
   */
  class TemplateDataStorageHelper<T extends TemplateRule>

Below is reported implementation for all classes mentioned above:

.. code-block:: java
  :linenos:

  public class LocationRuleData extends TemplateRule {

    private LocationWrapper mLocation;

    public LocationRuleData(String id, String template, LocationWrapper location) {
      super(id, template);
      mLocation = location;
    }

    public LocationWrapper getLocation() {
      return mLocation;
    }

    @Override
    protected TemplateRuleDataQueue getQueue() {
      // let's suppose to exploit a singleton implementation
      return LocationRuleDataQueue.getInstance();
    }

    @Override
    protected TemplateDataStorageHelper getStorageHelper() {
      // let's suppose to exploit a singleton implementation
      return LocationRuleStorageHelper.getInstance();
    }

  }

.. code-block:: java
  :linenos:

  public class LocationRuleDataQueue extends TemplateRuleDataQueue<LocationRuleData> {

    public static final String LOCATION_PARAM = "location";

    // ...

    @Override
    protected Map<String,ValueTypeWrapper> getWrapperObjects(LocationRuleData item) {
      Map<String,ValueTypeWrapper> wrappers = new TreeMap<>();
      wrappers.put(LOCATION_PARAM, new VT_Area_Wrapper(item.getLocation()));
      return wrappers;
    }

  }

.. code-block:: java
  :linenos:

  public class LocationRuleStorageHelper extends TemplateDataStorageHelper<LocationRuleData> {

    // ...

    @Override
    protected String getStorageFilename() {
      return "locations";
    }

    @Override
    protected Class<LocationRuleData[]> getItemArrayClass() {
      return LocationRuleData[].class;
    }

  }

On top of these classes, below is shown the implementation of External Provider static class.

.. code-block:: java
  :linenos:

  public class LocationRuleProvider {

    public static VT_Area_Wrapper getArea(String ruleId) {
      VT_Area_Wrapper wrapper = (VT_Area_Wrapper) LocationRuleDataQueue.getInstance()
        .getParamWrapper(ruleId, LocationRuleDataQueue.LOCATION_PARAM);
      return wrapper;
    }

  }

Example 4 - Parking Reminder
--------------------------------------------

This is a typical example showing potential of Activity Tracking within Resonance SDK. In order to retrieve parking position of user car, it's enough to register following event within *Application* class and implement logic of ``execute`` method.

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

Example 5 - Timeline
--------------------------------------------

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
