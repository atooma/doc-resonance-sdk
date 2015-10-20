.. _xml:

Defining Rules With XML
=======================================

Dealing with the definition of a rule by using Java classes and objects can be extremely complex. That's why, Atooma SDK provides an XML schema, allowing to more easily define rules according to the model described in previous sections.

.. code-block:: xml
  :linenos:

  <?xml version="1.0" encoding="UTF-8"?>
  <schema xmlns="http://www.w3.org/2001/XMLSchema"
          targetNamespace="http://www.atooma.com/sdk/rule"
          xmlns:tns="http://www.atooma.com/sdk/rule"
          elementFormDefault="qualified">

    <!-- rule title definition -->
    <simpleType name="rule-title">
      <restriction base="string">
        <minLength value="1"></minLength>
        <maxLength value="50"></maxLength>
      </restriction>
    </simpleType>

    <!-- rule description definition -->
    <simpleType name="rule-description">
      <restriction base="string">
        <minLength value="1"></minLength>
        <maxLength value="250"></maxLength>
      </restriction>
    </simpleType>

    <!-- definition for literal value to be used in properties -->
    <simpleType name="literal-type">
      <restriction base="string">
        <enumeration value="string" />
        <enumeration value="number" />
        <enumeration value="percent" />
        <enumeration value="timestamp" />
        <enumeration value="date" />
        <enumeration value="time" />
        <enumeration value="duration" />
        <enumeration value="boolean" />
        <enumeration value="binary" />
        <enumeration value="comparator" />
      </restriction>
    </simpleType>

    <!-- definition for required module element -->
    <complexType name="required-module">
      <attribute name="id" type="string" use="required" />
      <attribute name="version" type="unsignedInt" use="required" />
    </complexType>

    <!-- property definition -->
    <complexType name="property">
      <simpleContent>
        <extension base="string">
          <attribute name="id" type="string" use="required" />
          <attribute name="typeModule" type="string" use="required" />
          <attribute name="typeId" type="string" use="required" />
          <attribute name="literal" type="tns:literal-type" use="required" />
        </extension>
      </simpleContent>
    </complexType>

    <!-- definition for trigger parameter with allowed sources -->
    <complexType name="trigger-parameter">
      <choice minOccurs="1" maxOccurs="1">
        <element name="property-ref" type="tns:property-ref" />
        <element name="external-provider-call" type="tns:external-provider-call" />
      </choice>
      <attribute name="id" type="string" use="required" />
    </complexType>

    <!-- definition for condition checker parameter with allowed sources -->
    <complexType name="condition-parameter">
      <choice minOccurs="1" maxOccurs="1">
        <element name="property-ref" type="tns:property-ref" />
        <element name="trigger-variable" type="tns:trigger-variable" />
        <!--element name="provider-call" type="tns:provider-call" /-->
        <element name="external-provider-call" type="tns:external-provider-call" />
      </choice>
      <attribute name="id" type="string" use="required" />
    </complexType>

    <!-- definition for performer parameter with allowed sources -->
    <complexType name="performer-parameter">
      <choice minOccurs="1" maxOccurs="1">
        <element name="property-ref" type="tns:property-ref" />
        <element name="trigger-variable" type="tns:trigger-variable" />
        <element name="provider-call" type="tns:provider-call" />
        <element name="performer-variable" type="tns:performer-variable" />
        <element name="external-provider-call" type="tns:external-provider-call" />
      </choice>
      <attribute name="id" type="string" use="required" />
    </complexType>

    <!-- definition for property reference -->
    <complexType name="property-ref">
      <attribute name="id" type="string" use="required" />
    </complexType>

    <!-- definition for call to external provider -->
    <complexType name="external-provider-call">
      <attribute name="className" type="string" use="required" />
      <attribute name="methodName" type="string" use="required" />
    </complexType>

    <!-- trigger variable definition -->
    <complexType name="trigger-variable">
      <attribute name="id" type="string" use="required" />
    </complexType>

    <!-- performer variable definition -->
    <complexType name="performer-variable">
      <attribute name="module" type="string" use="required" />
      <attribute name="performer" type="string" use="required" />
      <attribute name="id" type="string" use="required" />
    </complexType>

    <!-- trigger definition -->
    <complexType name="trigger">
      <sequence>
        <element name="parameter" type="tns:trigger-parameter"
                 minOccurs="0" maxOccurs="unbounded"/>
      </sequence>
      <attribute name="module" type="string" use="required" />
      <attribute name="id" type="string" use="required" />
    </complexType>

    <!-- condition checker definition -->
    <complexType name="condition">
      <sequence>
        <element name="parameter" type="tns:condition-parameter"
                 minOccurs="0" maxOccurs="unbounded" />
      </sequence>
      <attribute name="module" type="string" use="required" />
      <attribute name="id" type="string" use="required" />
      <attribute name="inverse" type="boolean" use="optional" default="false" />
    </complexType>

    <!-- performer definition -->
    <complexType name="performer">
      <sequence>
        <element name="parameter" type="tns:performer-parameter"
                 minOccurs="0" maxOccurs="unbounded" />
      </sequence>
      <attribute name="module" type="string" use="required" />
      <attribute name="id" type="string" use="required" />
    </complexType>

    <!-- rule definition -->
    <complexType name="rule">
      <sequence>
        <element name="title" type="tns:rule-title"
                 minOccurs="1" maxOccurs="1" />
        <element name="description" type="tns:rule-description"
                 minOccurs="0" maxOccurs="1" />
        <element name="required-module" type="tns:required-module"
                 minOccurs="0" maxOccurs="unbounded" />
        <element name="property" type="tns:property"
                 minOccurs="0" maxOccurs="unbounded" />
        <element name="trigger" type="tns:trigger"
                 minOccurs="1" maxOccurs="1" />
        <element name="condition" type="tns:condition"
                 minOccurs="0" maxOccurs="4" />
        <element name="performer" type="tns:performer"
                 minOccurs="1" maxOccurs="5" />
      </sequence>
      <attribute name="version" type="unsignedInt" use="required" />
    </complexType>

    <!-- root element rule -->
    <element name="rule" type="tns:rule" />

  </schema>

As you can see, starting from bottom of the schema, each rule has a structure defined by the following block:

.. code-block:: xml
  :linenos:

  ...
  <complexType name="rule">
    <sequence>
      <!-- mandatory rule title -->
      <element name="title" type="tns:rule-title"
               minOccurs="1" maxOccurs="1" />
      <!-- optional rule description -->
      <element name="description" type="tns:rule-description"
               minOccurs="0" maxOccurs="1" />
      <!-- eventual required modules -->
      <element name="required-module" type="tns:required-module"
               minOccurs="0" maxOccurs="unbounded" />
      <!-- eventual properties -->
      <element name="property" type="tns:property"
               minOccurs="0" maxOccurs="unbounded" />
      <!-- one and only one trigger -->
      <element name="trigger" type="tns:trigger"
               minOccurs="1" maxOccurs="1" />
      <!-- up to 4 condition checkers -->
      <element name="condition" type="tns:condition"
               minOccurs="0" maxOccurs="4" />
      <!-- up to 5 performers -->
      <element name="performer" type="tns:performer"
               minOccurs="1" maxOccurs="5" />
    </sequence>
    <attribute name="version" type="unsignedInt" use="required" />
  </complexType>
  ...

Definitions for trigger, condition checker and performer elements are similar. Let's consider for example trigger definition:

.. code-block:: xml
  :linenos:

  ...
  <complexType name="trigger">
    <!-- eventual list of parameters -->
    <sequence>
      <element name="parameter" type="tns:trigger-parameter"
               minOccurs="0" maxOccurs="unbounded"/>
    </sequence>
    <!-- belonging module -->
    <attribute name="module" type="string" use="required" />
    <!-- trigger identifier -->
    <attribute name="id" type="string" use="required" />
  </complexType>
  ...

It's easy to notice that this definition perfectly matches with details provided in section :ref:`coretriggerdef`. In particular, triggers have a list of parameters that can be statically defined within the rule or injected by using an external provider call, as reported in *trigger-parameter* element definition:

.. code-block:: xml
  :linenos:

  ...
  <complexType name="trigger-parameter">
    <choice minOccurs="1" maxOccurs="1">
      <!-- parameter can be inner rule property or result of provider call -->
      <element name="property-ref" type="tns:property-ref" />
      <element name="external-provider-call" type="tns:external-provider-call" />
    </choice>
    <!-- parameter identifier -->
    <attribute name="id" type="string" use="required" />
  </complexType>
  ...

Details on properties and external provider calls will be provided in following sections.

Property Values
-----------------

Let's consider definition of a simple property, as exposed in previous section:

.. code-block:: xml
  :linenos:

  ...
  <!-- property-ref is just the identifier of a property -->
  <complexType name="property-ref">
    <attribute name="id" type="string" use="required" />
  </complexType>

  <!-- property is a complex type -->
  <complexType name="property">
    <simpleContent>
      <extension base="string">
        <attribute name="id" type="string" use="required" />
        <attribute name="typeModule" type="string" use="required" />
        <attribute name="typeId" type="string" use="required" />
        <attribute name="literal" type="tns:literal-type" use="required" />
      </extension>
    </simpleContent>
  </complexType>

  <!-- literal finally contains values -->
  <simpleType name="literal-type">
    <restriction base="string">
      <enumeration value="string" />
      <enumeration value="number" />
      <enumeration value="percent" />
      <enumeration value="timestamp" />
      <enumeration value="date" />
      <enumeration value="time" />
      <enumeration value="duration" />
      <enumeration value="boolean" />
      <enumeration value="binary" />
      <enumeration value="comparator" />
    </restriction>
  </simpleType>
  ...

As reported in the XML excerpt above, values assigned to rule properties can be represented in different formats, defined through the ``literal`` attribute of ``property`` tag. A detailed description for all available literal formats is reported below:

* ``binary`` - Binary sequence represented as a *Base64* string. Property value type class will be used for decoding the binary sequence, according to its own model.

* ``string`` - A simple string.

* ``number`` - String to be interpreted as a number, either integer or decimal. *Dot* character is used as a separator between integer and decimal parts. It's also possible to represent negative numbers by using *minus* character as a prefix for the value.

* ``percent`` - String to be interpreted as an unsigned integer number, with value between *0* e *100* (included).

* ``boolean`` - ``true`` or ``false``.

* ``timestamp`` - String representing a timestamp with format ``yyyy/mm/dd hh:mm[:ss[.mmm]]``. Hour value is between  *0* and *23*. Seconds and milliseconds are optional. In case milliseconds are specified, seconds must be reported too.

* ``date`` - String representing a date with format ``yyyy/mm/dd``.

* ``time`` - String representing a timestamp with format ``hh:mm[:ss[.mmm]]``. Hour value is between  *0* and *23*. Seconds and milliseconds are optional. In case milliseconds are specified, seconds must be reported too.

* ``duration`` - String to be interpreted as an unsigned integer number, representing a duration in seconds.

* ``comparator`` - Uppercase string, representing a number comparator. Possible values are: ``LESS``, ``LESS_EQUAL``, ``EQUAL``, ``GREATER_EQUAL`` e ``GREATER``.

External Provider Calls
------------------------------

Working with rules commonly requires parameters to be dynamically configured. Let's think for example about rules requesting home location. Of course, home location differs from user to user, so we cannot statically include such data within the rule definition.

For such kind of scenarios, it would be extremely useful to rely on functions allowing to provide different data depending on device, user and context. This is exactly what external provider calls allow to do.

Below is reported the definition of and external provider call within the XML schema:

.. code-block:: xml
  :linenos:

  <!-- definition for call to external provider -->
  <complexType name="external-provider-call">
    <attribute name="className" type="string" use="required" />
    <attribute name="methodName" type="string" use="required" />
  </complexType>

Exploiting a call to an external provider just requires to define a generic class (no specific extension is required) implementing a static method without parameters, as reported in following example:

.. code-block:: xml
  :linenos:

  ...
  <trigger id="CONNECTED" module="WIFI">
    <parameter id="SSID">
      <external-provider-call
        className="com.atooma.sample.HomeLocationProvider"
        methodName="getHome" />
    </parameter>
  </trigger>
  ...

Please notice that ``className`` attribute must include also class package, so be aware in case you perform changes on packages organization.

It follows a possible implementation for ``HomeLocationProvider`` class:

.. code-block:: java
  :linenos:

  public class HomeLocationProvider {

    public static VT_Location_Wrapper getHome() {
      // custom logic for retrieving home location
      LocationWrapper home = getHomeLocation();
      // returning location wrapper for home
      return new VT_Location_Wrapper(home);
    }

    ...

  }

Class ``VT_Location_Wrapper`` is a wrapper class used for encapsulating ``Area`` value type into a convenient common format. It follows a sample definition:

.. code-block:: java
  :linenos:

  public class VT_Area_Wrapper implements ValueTypeWrapper {

    public static final String VT_MODULE = "LOCATION";
    public static final String VT_ID = "AREA";

    private LocationWrapper value;

    public VT_Area_Wrapper(LocationWrapper value) {
      this.value = value;
    }

    @Override
    public Object getValue() {
      return buildArea(value);
    }

    private Area buildArea(LocationWrapper wrapper) {
      Position p = new Position(wrapper.getLatitude(),
          wrapper.getLongitude(), wrapper.getAddress());
      List<CellPosition> cells = new ArrayList<CellPosition>();
      // building cell position
      for (LocationCell location : wrapper.getCells()) {
        CellPosition cell = new CellPosition();
        cell.setCid(location.getCid());
        cell.setLac(location.getLac());
        cells.add(cell);
      }
      return new Area(p, 500, cells);
    }

  }

All wrapper classes must implement the interface ``ValueTypeWrapper`` and accordingly define method ``getValue``, returning value type that this wrapper belongs to. Moreover, all classes implementing ``ValueTypeWrapper`` must include a couple of static parameters:

* **VT_MODULE** - The identifier of the module that this wrapper belongs to.
* **VT_ID** - The identifier of the value type that this wrapper belongs to.

Please notice that currently Atooma SDK defines Value Type Wrapper class for all value types defined within basic modules.

.. note::

  In many cases, dealing with complex value types directly within XML definition could be difficult. That's because developer has to provide a binary value encoded in Base64 format, according to serialization / deserialization strategy defined by the value type itself. Below is reported a simple example.

  .. code-block:: xml
    :linenos:

    <property id="par-0" literal="binary"
      typeId="NUMBER-FILTER" typeModule="CORE">
        A0A0AAAAAAAA
    </property>
    ...
    <parameter id="FILTER">
      <property-ref id="par-0" />
    </parameter>

  Using external providers and wrapper classes can simplify this activity by moving definition of parameter values into java code.

  .. code-block:: xml
    :linenos:

    <parameter id="FILTER">
      <external-provider-call
        className="com.atooma.sdk.samples.providers.MyRuleProvider"
        methodName="getMyTextFilter" />
    </parameter>

  .. code-block:: java
    :linenos:

    public class MyRuleProvider {
      public static VT_TextFilter_Wrapper getMyTextFilter() {
        NumberFilter filter = new NumberFilter(NumberFilter.GREATER, 20);
        return new VT_TextFilter_Wrapper(filter);
      }
    }


Example
---------------

Following example describe how an if-do rule expressed in natural language can be represented by using the model and XML format described in previous sections.

Please notice that names of modules, value types, triggers, condition checkers and performers are from fantasy. Defining them is not a target of this section.

* **Natural form** - *When receiving an SMS while driving, read it*
* **Normalized form** - *If an SMS is received and my speed is greater than 20km/h, read sender name and message content*

Below is reported the rule definition in XML format:

.. code-block:: xml
  :linenos:

  <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
  <rule xmlns="http://www.yourule.it/simple/rule-definition"
        id="b7b7c82b-6ecc-452f-b786-2ec82d88b046"
   		version="1">
    <!-- title -->
    <title>Read SMS while driving</title>
    <!-- empty description -->
    <description></description>
    <!-- declaring required modules -->
    <required-module version="1" id="CORE" />
    <required-module version="1" id="SMS" />
    <required-module version="1" id="CONTACTS" />
    <required-module version="1" id="GPS" />
    <required-module version="1" id="TEXT-TO-SPEECH" />
    <!-- static properties available within the rule -->
    <property id="par-0" literal="binary"
      typeId="NUMBER-FILTER" typeModule="CORE">
        A0A0AAAAAAAA
    </property>
    <property id="par-1" literal="string"
      typeId="STRING" typeModule="CORE">
        incoming message
    </property>
    <property id="par-2" literal="string"
      typeId="STRING" typeModule="CORE">
        message content from
    </property>
    <!-- trigger definition on incoming sms -->
    <trigger id="INCOMING" module="SMS" />
    <!-- condition checker definition on speed -->
    <condition inverse="false" id="SPEED" module="GPS">
      <parameter id="FILTER">
        <!-- value for speed filter is taken from property -->
        <!-- ref par-0, that is binary because number filter -->
        <!-- is a custom value type -->
        <property-ref id="par-0" />
      </parameter>
    </condition>
    <!-- performer definitions -->
    <performer id="SPEAK" module="TEXT-TO-SPEECH">
      <parameter id="TEXT">
        <!-- message taken from property reference -->
        <property-ref id="par-1" />
      </parameter>
    </performer>
    <performer id="SPEAK" module="TEXT-TO-SPEECH">
      <parameter id="TEXT">
        <!-- message injected from trigger -->
        <trigger-variable id="SENDER-NAME" />
      </parameter>
    </performer>
    <performer id="SPEAK" module="TEXT-TO-SPEECH">
      <parameter id="TEXT">
        <!-- message taken from property reference -->
        <property-ref id="par-2" />
      </parameter>
    </performer>
    <performer id="SPEAK" module="TEXT-TO-SPEECH">
      <parameter id="TEXT">
        <!-- message injected from trigger -->
        <trigger-variable id="TEXT" />
      </parameter>
    </performer>
  </rule>

.. _srd:

Atooma binary format
---------------------------------

The Atooma binary format (SRD, *Serialized Rule Definition*) is an encoding used for representing a rule definition through a compact byte sequence. Even if rules in such format are not human readable, their parsing is much more efficient and memory required is minimized.

Having a clear understanding of how binary format works is extremely important because the definition of new value types requires the implementation of proper methods for encoding / decoding data to / from their SRD representation.

For such purpose, it follows a brief description of the binary representation for the primitive data types.

* **Integer** - Integers are represented with 4 bytes in Big Endian mode (network order).
* **Binary sequence** - Binary sequences are represented as follows:

  * An integer indicating the length of the sequence. In case value is negative, sequence is null. In case value is zero, sequence is empty. In case value is positive, sequence is populated.
  * In case sequence is populated, a number of bytes described by the length value.

* **String** - Strings are represented as binary sequences, encoding characters according to UTF-8 charset.
* **Boolean** - Booleans are represented with a single byte. ``0x0`` is used for representing ``FALSE``, while ``0x1`` is used for representing ``TRUE``.
* **Number** - Numbers are decimal values. Their binary representation is aligned with IEEE P754 with double precision (8 bytes).
* **Timestamp** - Timestamp values (date and time, with mills precision) are represented with UNIX Time format (number of mills elapsed in UTC from midnight of January 1, 1970). Such value is encoded using a 64 bit integer, represented with 8 bytes in Big Endian mode (network order).
* **Date** - Dates are represented by saving the number of days (positive or negative) from January 1, 1970. Such value is encoded using a 32 bit integer, represented with 4 bytes in Big Endian mode (network order).
* **Time** - Times are represented by saving the number of mills from the midnight of the previous day (0 time). Such value (always positive) is encoded using a 32 bit integer, represented with 4 bytes in Big Endian mode (network order).

When defining new value types, serialization / deserialization logic must be defined for allowing Atooma to handle properties in SRD format. Please notice that binary format is also essential for defining properties in the XML definition of a rule.
