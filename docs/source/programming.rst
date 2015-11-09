.. _programming:

Developing Modules
===================================

Atooma SDK allows to create new modules and components by exploiting a pre-defined set of *abstract classes*, as reported in table below:

.. cssclass:: table-bordered

+------------------------+-------------------------------------------------+
| Component              | Classes                                         |
+========================+=================================================+
| `Module`_              | ``com.atooma.engine.Module``                    |
+------------------------+-------------------------------------------------+
| `Value Type`_          | ``com.atooma.engine.ValueType``                 |
+------------------------+-------------------------------------------------+
| `Trigger`_             | ``com.atooma.engine.IntentBasedTrigger``,       |
|                        | ``com.atooma.engine.AlarmBasedTrigger``,        |
|                        | ``com.atooma.engine.Trigger``                   |
+------------------------+-------------------------------------------------+
| `Condition Checker`_   | ``com.atooma.engine.ConditionChecker``          |
+------------------------+-------------------------------------------------+
| `Performer`_           | ``com.atooma.engine.Performer``                 |
+------------------------+-------------------------------------------------+

.. _Module: programming.html#devmodules
.. _Value Type: programming.html#devvaluetypes
.. _Trigger: programming.html#devtriggers
.. _Condition Checker: programming.html#devconditioncheckers
.. _Performer: programming.html#devperformers

.. _devmodules:

Creating Modules
-----------------------------------

Creating new modules requires to extend the abstract class ``com.atooma.engine.Module``, including the following protected constructor and set of abstract methods to be implemented:

.. code-block:: java
  :linenos:

  /**
   * @param id
   *        Module identifier, to be unique among all available
   *        modules within Atooma enabled application.
   * @param version
   *        Module version, to be incremented every time new
   *        components are added.
   */
  protected Module(String id, int version) { ... }

  /**
   * Allows to declare dependencies of this module
   * on other modules
   */
  protected abstract void declareDependencies();

  /**
   * Allows to initialize this module.
   */
  protected abstract boolean init();

  /**
   * Allows to register components
   * (e.g. trigger, performer and so on).
   */
  protected abstract void registerComponents();

  /**
   * Allows to deallocate resources when destroying
   * this module.
   */
  protected abstract void destroy();

..
	The first parameter is the identifier of the module, that must be unique among all available modules within the Atooma enabled application. The second parameter is the module version, that must be incremented every time new components are added within the module (e.g. new triggers, condition checkers, perfomers and value types).

All the methods above will be described in details in the following sections.

Declaring Dependencies
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Modules may depend on other modules in two different ways:

1. **Explicit dependency** - This scenario occurs when one component within module relies on a value type belonging to another module. Let's suppose for example that module performer uses value type ``CORE.STRING``. This means that module depends on ``CORE`` module version 1. Reason for version 1 is that ``STRING`` value type is available in ``CORE`` module starting from that version.
2. **Implicit dependency** - This scenario occurs when a module requires functionalities from another module for being initialized. Let's think about ``SMS`` module. Obviously such module is meaningless in case device doesn't include proper hardware for GSM communications. Presence of such hardware is determined by ``TELEPHONY`` module. In case ``TELEPHONY`` is not available, ``SMS`` itself is useless. That's why ``SMS`` depends on ``TELEPHONY`` version 1.

All dependencies must be declared within method ``declareDependencies()``, that must include proper calls to the inherited method ``declareDependency(String id, int version)``.

For example, in case of ``SMS``:

.. code-block:: java
  :linenos:

  @Override
  protected void declareDependencies() {
    declareDependency("CORE", 1);
    declareDependency("TELEPHONY", 1);
  }

.. note:: When a module depends on another module, the first one will be loaded only if second one is successfully loaded and initialized.

Initializing Module
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Modules are initialized by invoking method ``init()``, that must define all operations to be perfomed for having module up and running. Method returns a boolean value for indicating whether initialization was successfully completed or not.

.. note:: In case a module fails its initialization block, all modules depending on it won't be initialized too.

For example, during its initialization, ``TELEPHONY`` module checks whether telephony service is available and a SIM card is present within the device or not. If not, the module and all modules depending on it won't be loaded.

.. code-block:: java
  :linenos:

  @Override
  protected boolean init() {
    Context context = getContext();
    TelephonyManager tm = (TelephonyManager) context
		    .getSystemService(Context.TELEPHONY_SERVICE);
    if (tm == null) {
      return false;
    }
    int simState = tm.getSimState();
    if (simState == TelephonyManager.SIM_STATE_ABSENT) {
      return false;
    }
      return true;
  }

Registering Components
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Once module initialization is completed, method ``registerComponents()`` is used for registering all components implemented by the module itself. Different registration methods are available for handling all possible component types:

.. code-block:: java
  :linenos:

  /**
   * Allows to register a value type.
   */
  protected void registerValueType(String id, int sinceVersion, ValueType vt)

  /**
   * Allows to register a trigger.
   */
  protected void registerTrigger(String id, int sinceVersion, Trigger tr)

  /**
   * Allows to register a condition checker.
   */
  protected void registerConditionChecker(String id, int sinceVersion, ConditionChecker cc)

  /**
   * Allows to register a performer.
   */
  protected void registerPerformer(String id, int sinceVersion, Performer pe)

All methods above include three parameters:

1. The identifier of the component, that must be unique within its belonging group (e.g. just one trigger can have ``RECEIVED`` as identifier).
2. The module version starting from which component was available (this must be less or equal to the module version).
3. The component to register.

Destroying Module
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When Atooma engine is deactivated (e.g. when turning off device) all loaded modules are destroyed, following an order that is opposite to the one used for loading them.

Method ``destroy()`` is aimed to encapsulate logic for releasing specific module resources.

Example
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Below is reported a sample structure for ``SMS`` module, including all concepts explained in previous sections.

.. code-block:: java
  :linenos:

  public class _SMS extends Module {

    private static final String MODULE_ID = "SMS";
    private static final int MODULE_VERSION = 1;

    public _SMS() {
      super(MODULE_ID, MODULE_VERSION);
    }

    @Override
    protected void declareDependencies() {
      declareDependency("CORE", 1);
      declareDependency("TELEPHONY", 1);
    }

    @Override
    protected boolean init() {
      return true;
    }

    @Override
    protected void registerComponents() {
      registerTrigger("INCOMING", 1, new TR_Incoming());
      registerPerformer("SEND", 1, new PE_Send());
    }

    @Override
    protected void destroy() {
    }

  }

Initialization is always successful, unless dependencies are not fully satisfied. Two components are declared. First one is a trigger, allowing to listen for incoming message events. Second one is a performer, allowing to send messages.

Concurrency & Synchronization
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Modules and components have just one single instance within Atooma engine. This means for example that there is just one instance of ``_SMS`` module class and just one instance of its trigger ``TR_Incoming``.

Atooma engine works in multi-threading mode and this means that several rules can be simultanously executed. When two or more rules use the same component, the same instance is shared between them.

Basing on this, it's extremely important to properly handle eventual concurrency and synchronization issues when developing modules and components.

.. note:: A good practice is to keep inside component classes some data structure for handling information related to specific rules (e.g. a Map with rule id as key).

.. warning:: Keep always in mind that same external resources can be accessed simultanously in different points even if their management is demanded to a single component.

.. _devvaluetypes:

Creating Value Types
-----------------------------------

Value types are components aimed to define, describe and manage data handled by the other components. Some examples are ``STRING``, ``NUMBER``, ``TIMESTAMP`` and ``POSITION``. For each value type it's important to define a binary representation, as already described in section :ref:`srd`. It's convenient to represent values for a specific type by using dedicated Java objects to be used at runtime. Classes implementing value types are responsible for the conversion between Java objects and their binary representation and vice versa.

In order to implement a value type, class ``com.atooma.engine.ValueType`` must be extended. Implementation of following methods is required:

.. code-block:: java
  :linenos:

  public Class<?> getValueClass()
  public byte[] encode(Object value)
  public Object decode(byte[] value) throws Exception
  public String getStringRepresentation(Object value)

Declaring Java Class
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The implementation of the value type must declare which Java class to use for representing values for the managed type. Such class is declared by implementing method:

.. code-block:: java
  :linenos:

  protected Class<?> getValueClass()

For example, ``CORE.TIMESTAMP`` value type, used for representing date and time, internally uses a ``Long`` object (number of mills according to UNIX time notation). Its ``getValueClass()`` method is implemented as follows:

.. code-block:: java
  :linenos:

  @Override
  public Class<?> getValueClass() {
    return Long.class;
  }

Encoding and Decoding Binary Format
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Transformation from Java object to binary sequence is implemented through method:

.. code-block:: java
  :linenos:

  protected byte[] encode(Object value)

When invoking it, method receives as argument a Java object of the type defined by the ``getValueClass()`` method. Provided value is never ``null``.

The opposite transformation is executed by defining method:

.. code-block:: java
  :linenos:

  protected Object decode(byte[] value) throws Exception

Received value is never ``null`` (but the byte array can be empty). It's good practice to check if received binary sequence is consistent with the value type definition. If not, method should throw an ``Exception``.

Utilities for Encoding / Decoding
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

``ValueType`` class provides to its subclasses some methods for simplifying the encoding / decoding operations for primitive Java types. Methods available for encoding are:

.. code-block:: java
  :linenos:

  protected static byte[] encodeBoolean(boolean value)
  protected static byte[] encodeInt(int value)
  protected static byte[] encodeLong(long value)
  protected static byte[] encodeDouble(double value)
  protected static byte[] encodeString(String value)

For example, in case of ``CORE.TIMESTAMP``, the received object will be of ``Long`` type. The binary representation, in such case, will be performed by encoding the value of the received long variable as follows:

.. code-block:: java
  :linenos:

  @Override
  protected byte[] encode(Object value) {
    long longValue = ((Long) value).longValue();
    return encodeLong(longValue);
  }

All the encoding methods mentioned above can be also used for encoding the atomic information of more complex data structures. An example is about GPS coordinates, that can be encoded as a sequence of two real numbers.

Methods for decoding are reported below:

.. code-block:: java
  :linenos:

  protected static boolean decodeBoolean(byte[] value) throws Exception
  protected static int decodeInt(byte[] value) throws Exception
  protected static long decodeLong(byte[] value) throws Exception
  protected static double decodeDouble(byte[] value) throws Exception
  protected static String decodeString(byte[] value) throws Exception

For example, in case of ``CORE.TIMESTAMP``:

.. code-block:: java
  :linenos:

  @Override
  protected Object decode(byte[] value) throws Exception {
    if (value.length != 8) {
      throw new Exception("Invalid TIMESTAMP binary value");
    }
    return new Long(decodeLong(value));
  }

Value Type Arrays
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Sometimes it's important to handle data in form of array. Some examples can be an array of URLs containing a list of favorite web addresses or an array of phone numbers with all references for a contact.

For implementing an array of value types it's possible to extend the abstract class:

.. code-block:: java
  :linenos:

  com.atooma.engine.ArrayValueType

This class extends ``ValueType`` and requires the implementation of the following abstract methods:

.. code-block:: java
  :linenos:

  protected abstract byte[] encodeItem(Object item)
  protected abstract Object decodeItem(byte[] item) throws Exception

These methods must implements the procedure for the encoding / decoding of single elements within the entire array (they should be the same used for the value type of the single element of the array).

Example
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: java
  :linenos:

  class VT_Timestamp extends ValueType {

    @Override
    public Class<?> getValueClass() {
      return Long.class;
    }

    @Override
    public byte[] encode(Object value) {
      long longValue = ((Long) value).longValue();
      return encodeLong(longValue);
    }

    @Override
    public Object decode(byte[] value) throws Exception {
      if (value.length != 8) {
        throw new Exception("Invalid TIMESTAMP binary value");
      }
      return new Long(decodeLong(value));
    }

    @Override
    public String getStringRepresentation(Object value) {
      String pattern = getContext().getResources()
          .getString(R.string.mod_core_timestamp_pattern);
      DateFormat df = new SimpleDateFormat(pattern);
      return df.format(new Date((Long) value));
    }

  }

Registering Value Types
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Every value type must be registered to module it belongs to as follows:

.. code-block:: java
  :linenos:

  @Override
  protected void registerComponents() {
    registerValueType("TIMESTAMP", 1, new VT_Timestamp());
  }

.. _devtriggers:

Creating Triggers
-------------------------------------------------

Triggers are components responsible for rules activation when a specific event occurs. Some examples are ``WIFI.ENABLED`` or ``SMS.INCOMING``. Every trigger may require an optional list of input parameters and provide a list of output variables.

In order to create a new trigger it's possible to extend one specific abstract class among the followings:

* ``com.atooma.engine.IntentBasedTrigger`` - Activation of **intent-based triggers** relies on the reception of a specific intent from Android system.
* ``com.atooma.engine.AlarmBasedTrigger`` - Activation of **alarm-based triggers** relies on periodic checks of specific conditions.
* ``com.atooma.engine.Trigger`` - Activation of generic **triggers** relies on custom logic not matching with neither of the previous cases.

Regardless of trigger type, all triggers are required to implement the following methods:

.. code-block:: java
  :linenos:

  protected void declareParameters()
  protected void declareVariables()

Additional methods are requested depending on trigger type:

**IntentBasedTrigger**

.. code-block:: java
  :linenos:

  protected IntentFilter getIntentFilter(String ruleId, Map<String,Object> parameters)
  protected void received(String ruleId, Map<String,Object> parameters, Intent intent)

**AlarmBasedTrigger**

.. code-block:: java
  :linenos:

  protected ScheduleInfo getScheduleInfo(String ruleId, Map<String,Object> parameters)
  protected void timeout(String ruleId, Map<String,Object> parameters)

**Trigger**

.. code-block:: java
  :linenos:

  protected void invoke(String ruleId, Map<String,Object> parameters)
  protected void revoke(String ruleId)

Declaring Parameters
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Parameters required by triggers must be declared in the implementation of the abstract method:

.. code-block:: java
  :linenos:

  protected void declareParameters()

This method must include a sequence of calls to the inherited method:

.. code-block:: java
  :linenos:

  protected void declareParameter(String id, String module, String typeId, boolean required)

* ``id`` is the identifier of the required parameter.
* ``module`` and ``typeId`` identify value type, indicating respectively the module and the identifier of the referenced value type.
* ``required`` indicates whether parameter is mandatory or can be ``null``.

Below is reported an example:

.. code-block:: java
  :linenos:

  @Override
  protected void declareParameters() {
    declareParameter("CONNECTED", "CORE", "BOOLEAN", true);
  }

Declaring Variables
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Variables that can be used as output for a trigger must be explicitly declared in the implementation of method:

.. code-block:: java
  :linenos:

  protected void declareVariables()

This method must include a sequence of calls to the inherited method:

.. code-block:: java
  :linenos:

  protected void declareVariable(String id, String module, String typeId)

* ``id`` is the identifier of the output variable.
* ``module`` and ``typeId`` identify value type, indicating respectively the module and the identifier of the referenced value type.

Below is reported an example:

.. code-block:: java
  :linenos:

  @Override
  protected void declareVariables() {
    declareVariable("SMS-TEXT", "CORE", "STRING");
    declareVariable("SMS-SENDER", "CORE", "STRING");
    declareVariable("SMS-TIME", "CORE", "TIMESTAMP");
  }

Activating Rules
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When the event monitored by a trigger occurs, the trigger itself must notify the rule execution engine, allowing to proceed with rule next steps (either condition checkers or performers).

The following method must be used for this purpose:

.. code-block:: java
  :linenos:

  protected void trigger(String ruleId, Map<String, Object> variables)

* ``ruleId`` is the identifier of the rule to activate.
* ``variables`` is a map with all the output variables. The key of the map is the variable identifier (according to declarations made in method ``declareVariables()``), while value must be of the Java type declared by the value type.

.. note:: It's mandatory for a trigger to produce as output all variables declared in method ``declareVariables()``.

Intent Based Triggers
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Activation of **intent-based triggers** is based on the reception of a specific intent from Android system. Some examples of intent-based triggers are ``SMS.RECEIVED`` and ``WIFI.CONNECTION-STATE-CHANGED``.

In order to develop an intent-based trigger, the abstract class ``com.atooma.engine.IntentBasedTrigger`` must be implemented. Following methods are requested:

.. code-block:: java
  :linenos:

	protected IntentFilter getIntentFilter(String ruleId, Map<String, Object> parameters)
  protected void received(String ruleId, Map<String, Object> parameters, Intent intent)

Trigger implementation must generate and return an intent filter when following method is invoked:

.. code-block:: java
  :linenos:

  protected IntentFilter getIntentFilter(String ruleId, Map<String, Object> parameters)

* ``ruleId`` is the identifier of the rule to create the intent filter for.
* ``parameters`` is a map with all the parameters that are input for the rule, according to names and types defined in method ``declareParameters()``.

All intents intercepted by the generated filter will be notified to the trigger through an invocation of the method:

.. code-block:: java
  :linenos:

	protected void received(String ruleId, Map<String, Object> parameters, Intent intent)

* ``ruleId`` is the identifier of the rule.
* ``parameters`` is a map with all the parameters that are input for the rule, according to names and types defined in method ``declareParameters()``.
* ``intent`` is the intercepted intent.

Of course all values within the received intent must be checked before invoking the ``trigger()`` method.

An example of intent-based trigger is ``SMS.INCOMING``, that is the trigger intercepting the incoming SMS event. When such event occur, Android system notifies all interested objects throught a broadcast intent called ``android.provider.Telephony.SMS_RECEIVED``, that also includes details concerned with the message itself (e.g. sender, content and so on).

.. code-block:: java
  :linenos:

  class TR_Incoming extends IntentBasedTrigger {

    @Override
    protected void declareParameters() {
      declareParameter("SENDER", "CONTACTS", "PHONE-NUMBER", false);
      declareParameter("TEXT-FILTER", "CORE", "TEXT-FILTER", false);
    }

    @Override
    protected void declareVariables() {
      declareVariable("TEXT", "CORE", "STRING");
      declareVariable("TIMESTAMP", "CORE", "TIMESTAMP");
      declareVariable("SENDER-NAME", "CORE", "STRING");
      declareVariable("SENDER-PHONE-NUMBER", "CONTACTS", "PHONE-NUMBER");
    }

    @Override
    protected IntentFilter getIntentFilter(String ruleId, Map<String, Object> parameters) {
      return new IntentFilter("android.provider.Telephony.SMS_RECEIVED");
    }

    @Override
    protected void received(String ruleId, Map<String, Object> parameters, Intent intent) {
      Bundle extras = intent.getExtras();
      Object[] pdus = (Object[]) extras.get("pdus");
      if (pdus != null) {
        for (Object pdu : pdus) {
          SmsMessage sms = SmsMessage.createFromPdu((byte[]) pdu);
          // we must inject declared variables into rule execution context
          Map<String, Object> variables = new HashMap<String, Object>();
          variables.put("TEXT", sms.getMessageBody());
          variables.put("TIMESTAMP", sms.getTimestampMillis());
          String origin = sms.getDisplayOriginatingAddress();
          variables.put("SENDER-NAME", sms.getDisplayOriginatingAddress());
          PhoneNumber phoneNumber = null;
          if (origin != null) {
            phoneNumber = PhoneNumber.resolvePhoneNumber(origin, getContext());
            variables.put("SENDER-PHONE-NUMBER", phoneNumber);
          }
          PhoneNumber senderFilter = (PhoneNumber) parameters.get("SENDER");
          TextFilter textFilter = (TextFilter) parameters.get("TEXT-FILTER");
          boolean senderMatch = senderFilter == null ||
              (phoneNumber != null && phoneNumber.equals(senderFilter));
          boolean textMatch = textFilter == null ||
              textFilter.filter(sms.getMessageBody());
          // triggering rule only if sender and text match
          // with provided values
          if (senderMatch && textMatch) {
            trigger(ruleId, variables);
          }
        }
      }
    }
  }

Alarm Based Triggers
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Activation of **alarm-based triggers** is based on periodic verification of specific events. A typical example is represented by the integration with web services or third party API, but they can be useful iven in case it's not possible to exploit any Android Intent. Some examples of intent-based triggers are ``CORE.ALARM`` and ``FACEBOOK.STATUS_UPDATED``.

In order to develop an alarm-based trigger, the abstract class ``com.atooma.engine.AlarmBasedTrigger`` must be implemented. Following methods are requested:

.. code-block:: java
  :linenos:

  protected ScheduleInfo getScheduleInfo(String ruleId, Map<String, Object> parameters)
  protected void onTimeout(String ruleId, Map<String, Object> parameters)

Trigger implementation must generate and return all information for defining a proper timer (scheduling) when following method is invoked:

.. code-block:: java
  :linenos:

  protected ScheduleInfo getScheduleInfo(String ruleId, Map<String, Object> parameters)

* ``ruleId`` is the identifier of the rule.
* ``parameters`` is a map with all the parameters that are input for the rule, according to names and types defined in method ``declareParameters()``.

It's possible to use different constructors for building different types of ``ScheduleInfo`` objects.

.. code-block:: java
  :linenos:

  public ScheduleInfo(boolean repeat, boolean exact, boolean wakeUp,
	    Long triggerAtTime, Long interval)

* ``repeat`` indicates whether timer must be reinitialized after first timeout.
* ``exact`` is used for declaring if timer expiration must be strictly linked with parameters ``triggerAtTime`` and ``interval`` or if it's possible for Android to optimize it taking care of eventual other contiguous timers.
* ``wakeUp`` if *true* forces device to wake up when timer timeout occurs, if *false* allow to postpone timeout code execution to the next wake up.
* ``triggerAtTime`` is the time in ``System.currentTimeMillis()`` of the first timeout (if past, trigger will be immediately activated).
* ``interval`` is the trigger repetition interval in mills. It is ignored if ``repeat`` value is *false*.

Instead of using an explicit constructor it's also possible to exploit specific factory methods of ``ScheduleInfo`` class for building timers.

.. code-block:: java
  :linenos:

  public static ScheduleInfo singleExecution(long time)

This method creates a ``ScheduleInfo`` representing a timer with single execution at specific time and is equivalent to:

.. code-block:: java
  :linenos:

  new ScheduleInfo(false, true, true, time, null);

While:

.. code-block:: java
  :linenos:

  public static ScheduleInfo inexactRepeatEvery(long interval)

This method creates a ``ScheduleInfo`` representing a timer with immediate execution, to be repeated every *interval* mills without strict and specific execution time. It's equivalent to:

.. code-block:: java
  :linenos:

  new ScheduleInfo(true, false, true, System.currentTimeMillis(), interval);

At every timer timeout, trigger method ``onTimeout()`` is invoked. Its implementation includes logic for checking whether ``trigger()`` method must be invoked for activating the rule or not.

An example of alarm-based trigger is ``CORE.ALARM``, that works exactly like an alarm and doesn't require any verification in ``onTimeout()`` method.

.. code-block:: java
  :linenos:

  public class TR_Alarm extends AlarmBasedTrigger {

    @Override
    protected void declareParameters() {
      declareParameter("TIME", "CORE", "TIMESTAMP", true);
      declareParameter("REPEAT", "CORE", "BOOLEAN", true);
    }

    @Override
    protected void declareVariables() {
      // no variable defined
    }

    @Override
    protected ScheduleInfo getScheduleInfo(String ruleId, Map<String, Object> parameters) {
      Long time = (Long) parameters.get("TIME");
      Boolean repeat = (Boolean) parameters.get("REPEAT");
      // repeat interval (24 hours)
      // to be ignored if repeat == false
      long aDay = 1000l * 60l * 60l * 24l;
      // the next deadline
      long triggerAtTime = time.longValue();
      if (System.currentTimeMillis() > triggerAtTime) {
        // if provided time is before current,
        // alarm is moved to next day
        long difference = System.currentTimeMillis() - triggerAtTime;
        int days = (int) Math.ceil(difference / aDay);
        triggerAtTime += (aDay * days);
      }
      return new ScheduleInfo(repeat, true, true, triggerAtTime, aDay);
    }

    @Override
    protected void onTimeout(String ruleId, Map<String, Object> parameters) {
      // no condition to be verified
      // possible to directly proceed
      // with rule triggering
      trigger(ruleId, parameters);
    }

  }

Custom Triggers
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In case trigger to be implemented doesn't match to any of the previous categories, it's possible to proceed at low level by directly extending the ``com.atooma.engine.Trigger`` class.

Methods to be implemented are reported below:

.. code-block:: java
  :linenos:

  protected void invoke(String ruleId, Map<String, Object> parameters)
  protected void revoke(String ruleId)

Method:

.. code-block:: java
  :linenos:

  protected void invoke(String ruleId, Map<String, Object> parameters)

Is used for implementing logic deciding when a rule must be activated.

* ``ruleId`` is the identifier of the rule.
* ``parameters`` is a map with all the parameters that are input for the rule, according to names and types defined in method ``declareParameters()``.

As soon as a rule doesn't want anymore to receive notification about event to be monitored, following method will be invoked:

.. code-block:: java
  :linenos:

  protected void revoke(String ruleId)

* ``ruleId`` is the identifier of the rule that asked for event notification stop.

Registering Triggers
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Every trigger must be registered to module it belongs to as follows:

.. code-block:: java
  :linenos:

  @Override
  protected void registerComponents() {
    registerTrigger("RECEIVED", 1, new TR_Received());
  }

.. _devconditioncheckers:

Creating Condition Checkers
-------------------------------------------------

Condition checkers are used by a module for allowing to check a condition when they are invoked and return a boolean outcome. They may require optional parameters.

In order to create a new condition checker abstract class ``com.atooma.engine.ConditionChecker`` must be extended, including implementation of following methods:

.. code-block:: java
  :linenos:

  protected void declareParameters()
  protected boolean invoke(String ruleId, Map<String, Object> parameters)

Declaring Parameters
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Parameters required by a condition checker must be declared within method:

.. code-block:: java
  :linenos:

  protected void declareParameters()

This method must include a sequence of calls to the inherited method:

.. code-block:: java
  :linenos:

  protected void declareParameter(String id, String module, String typeId, boolean required)

* ``id`` is the identifier of the required parameter.
* ``module`` and ``typeId`` identify value type, indicating respectively the module and the identifier of the referenced value type.
* ``required`` indicates whether parameter is mandatory or can be ``null``.

Below is reported an example:

.. code-block:: java
  :linenos:

  @Override
  protected void declareParameters() {
    declareParameter("STR1", "CORE", "STRING", true);
    declareParameter("STR2", "CORE", "STRING", true);
    declareParameter("CASE-SENSITIVE", "CORE", "BOOLEAN", true);
  }

Invoking Condition Checkers
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When a rule requires condition handled by a condition checker to be verified, the following method is invoked:

.. code-block:: java
  :linenos:

  protected boolean invoke(String ruleId, Map<String, Object> parameters)

* ``ruleId`` is the identifier of the rule that asked for condition verification.
* ``parameters`` is a map with all the parameters that are input for the rule, according to names and types defined in method ``declareParameters()``.

Method complete its execution returning a boolean value, *true* if condition is verified, *false* otherwise.

Example
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

``WIFI.CONNECTED`` condition checker allows to control if WiFi is enabled and connected. It requires an optional parameter ``SSID`` with Type ``CORE.STRING``, that reports the identifier of the WiFi network to check. In case such parameter is not defined, connection to any network is positively evaluated.

.. code-block:: java
  :linenos:

  public class CC_Connected extends ConditionChecker {

    @Override
    protected void declareParameters() {
      declareParameter("SSID", "CORE", "STRING", false);
    }

    @Override
    protected boolean invoke(String ruleId, Map<String, Object> parameters) {
      WifiManager wifiManager = (WifiManager) getContext()
          .getSystemService(Context.WIFI_SERVICE);
      WifiInfo info = wifiManager.getConnectionInfo();
      if (info != null) {
        String ssid = (String) parameters.get("SSID");
        if (ssid == null || (ssid = ssid.trim()).length() == 0) {
          return true;
        } else {
          WifiManager manager = (WifiManager) getContext()
              .getSystemService(Context.WIFI_SERVICE);
          WifiInfo wifiInfo = manager.getConnectionInfo();
          if (wifiInfo != null) {
            String networkSsid = wifiInfo.getSSID();
            if (ssid.equalsIgnoreCase(networkSsid)) {
              return true;
            }
          }
        }
      }
      return false;
    }
  }

Registering Condition Checkers
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Every trigger must be registered to module it belongs to as follows:

.. code-block:: java
  :linenos:

  @Override
  protected void registerComponents() {
    registerConditionChecker("STR-EQUALS", 1, new CC_StrEquals());
  }

Trigger and Condition Checker Affinity
-------------------------------------------------

Even if trigger and condition checkers are classes with own lifecycle, extending different superclasses, some semantic associations can be sometimes defined between them.

When declaring a trigger and a condition checker with same ID, they are considered as related. This means that regardless of which component is choosen, engine may decide to use trigger or condition checker depending on situation.

Making a trigger and a condition checker as related requires in any case some constraints to observe:

1. They must have the same list of parameters
2. Trigger cannot export variables. Trigger exporting variables cannot be related to a condition checker.
3. They must declare the same UI category.

.. _devperformers:

Creating Performers
-------------------------------------------------

Performers are components responsible of executing operations on demand, as requested by rules they are declared in. They may require optional parameters and can produce optional variables in output.

In order to create a new condition checker abstract class ``com.atooma.engine.Performer`` must be extended, including implementation of following methods:

.. code-block:: java
  :linenos:

  protected void declareParameters()
  protected void declareVariables()
  protected Map<String, Object> invoke(String ruleId, Map<String, Object> parameters)

Declaring Parameters
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Parameters required by a performer must be declared within method:

.. code-block:: java
  :linenos:

  protected void declareParameters()

This method must include a sequence of calls to the inherited method:

.. code-block:: java
  :linenos:

  protected void declareParameter(String id, String module, String typeId, boolean required)

* ``id`` is the identifier of the required parameter.
* ``module`` and ``typeId`` identify value type, indicating respectively the module and the identifier of the referenced value type.
* ``required`` indicates whether parameter is mandatory or can be ``null``.

Below is reported an example:

.. code-block:: java
  :linenos:

  @Override
  protected void declareParameters() {
    declareParameter("TEXT", "CORE", "STRING", true);
  }

Declaring Variables
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Variables that can be used as output for a performer must be explicitly declared in the implementation of method:

.. code-block:: java
  :linenos:

  protected void declareVariables()

This method must include a sequence of calls to the inherited method:

.. code-block:: java
  :linenos:

  protected void declareVariable(String id, String module, String typeId)

* ``id`` is the identifier of the output variable.
* ``module`` and ``typeId`` identify value type, indicating respectively the module and the identifier of the referenced value type.

Below is reported an example:

.. code-block:: java
  :linenos:

  @Override
  protected void declareVariables() {
    declareVariable("RESULT", "CORE", "BOOLEAN");
  }

Invoking Performers
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When a rule requires a performer to execute a set of actions, the following method is invoked:

.. code-block:: java
  :linenos:

  protected Map<String, Object> invoke(String ruleId, Map<String, Object> parameters)

* ``ruleId`` is the identifier of the rule that asked for condition verification.
* ``parameters`` is a map with all the parameters that are input for the rule, according to names and types defined in method ``declareParameters()``.

Method complete its execution returning a map of variables, to be compliant with declarations made in method ``declareVariables()``.

Example
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

``NOTIFICATION.TOAST`` performer shows a toast message to the user. It requires a parameter ``TEXT`` with Type ``CORE.STRING``, that represents the message to be displayed.

.. code-block:: java
  :linenos:

  class PE_Toast extends Performer {

    @Override
    protected void declareParameters() {
      declareParameter("TEXT", "CORE", "STRING", true);
    }

    @Override
    protected void declareVariables() {
      // no variables
    }

    @Override
    protected Map<String, Object> invoke(String ruleId, Map<String, Object> parameters) {
      final String text = (String) parameters.get("TEXT");
      Handler handler = new Handler(Looper.getMainLooper());
      handler.post(new Runnable() {
        public void run() {
          Toast.makeText(getContext(), text, Toast.LENGTH_LONG).show();
        }
      });
      return new HashMap<String, Object>();
    }

  }

Registering Performers
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Every trigger must be registered to module it belongs to as follows:

.. code-block:: java
  :linenos:

  @Override
  protected void registerComponents() {
    registerPerformer("TOAST", 1, new PE_Toast());
  }
