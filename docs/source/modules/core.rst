.. _module-core:

Core
--------------------------

* **Identifier** - CORE
* **Description** - Defines core components for building rules and provides all basic value types to be used in other modules.
* **Current Version** - 1
* **Initialization** - None
* **Destroy** - None
* **Dependencies** - None

Business Types
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. cssclass:: table-bordered

+-------------------+---------------------------------------------------------------------------------------------------------------+
| Name              | Description                                                                                                   |
+===================+===============================================================================================================+
| NumberFilter      | Filter for Number type. Includes constants for representing filter type (``LESS``, ``EQUAL``, ``GREATER``)    |
+-------------------+---------------------------------------------------------------------------------------------------------------+
| PercentFilter     | Filter for Percent type. Includes constants for representing filter type (``LESS``, ``EQUAL``, ``GREATER``)   |
+-------------------+---------------------------------------------------------------------------------------------------------------+
| TextFilter        | Filter for Number type. Includes constants for representing filter type (``EQUALS``, ``CONTAINS``,            |
|                   | ``STARTS_WITH``, ``ENDS_WITH``)                                                                               |
+-------------------+---------------------------------------------------------------------------------------------------------------+

Value Types
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. cssclass:: table-bordered

+-----------------------+----------------------------------------------------------+---------------+--------------------------------+
| Name                  | Description                                              | From Version  | Wrapper Class                  |
+=======================+==========================================================+===============+================================+
| **STRING**            | Represents a random length string.                       | 1             | VT_String_Wrapper              |
+-----------------------+----------------------------------------------------------+---------------+--------------------------------+
| **TEXT-FILTER**       | Represent a filter for text strings. It is made of       | 1             | VT_TextFilter_Wrapper          |
|                       | an index aimed to identify filter type, a boolean        |               |                                |
|                       | allowing to define if comparison should be case          |               |                                |
|                       | sensitive and a string with value to use for filtering   |               |                                |
|                       | matching.                                                |               |                                |
+-----------------------+----------------------------------------------------------+---------------+--------------------------------+
| **BOOLEAN**           | Represent a boolean value (*true* or *false*).           | 1             | VT_Boolean_Wrapper             |
+-----------------------+----------------------------------------------------------+---------------+--------------------------------+
| **NUMBER**            | Represent a decimal number.                              | 1             | VT_Number_Wrapper              |
+-----------------------+----------------------------------------------------------+---------------+--------------------------------+
| **NUMBER-FILTER**     | Represent a filter for integer and decimal numbers.      | 1             | VT_NumberFilter_Wrapper        |
|                       | It is made of an index aimed to identify filter type and |               |                                |
|                       | a decimal number to use for filtering.                   |               |                                |
+-----------------------+----------------------------------------------------------+---------------+--------------------------------+
| **PERCENT**           | Represent an integer percent value.                      | 1             | VT_Percent_Wrapper             |
+-----------------------+----------------------------------------------------------+---------------+--------------------------------+
| **PERCENT-FILTER**    | Represent a filter for integer percent values.           | 1             | VT_PercentFilter_Wrapper       |
|                       | It is made of an index aimed to identify filter type and |               |                                |
|                       | an integer number to use for filtering.                  |               |                                |
+-----------------------+----------------------------------------------------------+---------------+--------------------------------+
| **TIMESTAMP**         | Represent a date and time with millisectonds precision.  | 1             | VT_Timestamp_Wrapper           |
+-----------------------+----------------------------------------------------------+---------------+--------------------------------+
| **DATE**              | Represent a date.                                        | 1             | VT_Date_Wrapper                |
+-----------------------+----------------------------------------------------------+---------------+--------------------------------+
| **TIME**              | Represent a time with milliseconds precision.            | 1             | VT_Time_Wrapper                |
+-----------------------+----------------------------------------------------------+---------------+--------------------------------+
| **DURATION**          | Represent a duration, expressed in seconds.              | 1             | VT_Duration_Wrapper            |
+-----------------------+----------------------------------------------------------+---------------+--------------------------------+
| **DAY-OF-WEEK**       | Represent a day of the week.                             | 1             | VT_DayOfWeek_Wrapper           |
+-----------------------+----------------------------------------------------------+---------------+--------------------------------+
| **DAY-OF-WEEK-ARRAY** | Represent an array of days of the week. No duplicates    | 1             | VT_DayOfWeekArray_Wrapper      |
|                       | are allowed and array max size is 7.                     |               |                                |
+-----------------------+----------------------------------------------------------+---------------+--------------------------------+
| **URI**               | Represent a reference to a resource.                     | 1             | VT_Uri_Wrapper                 |
+-----------------------+----------------------------------------------------------+---------------+--------------------------------+
