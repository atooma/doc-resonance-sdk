.. _module-location:

Location
--------------------------

* **Identifier** - LOCATION
* **Description** - Allows to determine device position.
* **Current Version** - 3
* **Initialization** - None
* **Destroy** - None
* **Dependencies** - :ref:`module-core`

Value Types
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. cssclass:: table-bordered

+-------------------+------------------------------------------------------------------------+---------------+---------------------+
| Name              | Description                                                            | From Version  | Wrapper Class       |
+===================+========================================================================+===============+=====================+
| **POSITION**      | Represents a position through latitude and longitude coordinates.      | 1             | VT_Position_Wrapper |
+-------------------+------------------------------------------------------------------------+---------------+---------------------+
| **AREA**          | Represents a circular area, defined through a centre (latitude and     | 1             | VT_Area_Wrapper     |
|                   | longitude) and a radius.                                               |               |                     |
+-------------------+------------------------------------------------------------------------+---------------+---------------------+

..
	+------------------------+--------------+---------------+------------------+
	| Name                   | Position     | From revision | 1                |
	+------------------------+--------------+---------------+------------------+
	| Description            | Represents a position through latitude and      |
	|                        | longitude coordinates.                          |
	+------------------------+--------------+---------------+------------------+
	| Binary encoding        | Two signed numeric values in format IEEE        |
	|                        | P754. The first one represents the latitude,    |
	|                        | while the second one represents the longitude.  |
	|                        | Both values must be interpreted in degrees.     |
	|                        | It follows an optional UTF-8 string, with the   |
	|                        | position address.                               |
	+------------------------+--------------+---------------+------------------+
	| String representation  | The address, if available, otherwise the        |
	|                        | coordinates, separated by comma. Every          |
	|                        | coordinate uses dot as decimal separator.       |
	+------------------------+--------------+---------------+------------------+
	| Exportable values      | Yes                                             |
	+------------------------+--------------+---------------+------------------+

	Value Type Area
	^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

	+------------------------+--------------+---------------+------------------+
	| Name                   | Area         | From revision | 1                |
	+------------------------+--------------+---------------+------------------+
	| Description            | Represents a circular area, defined through a   |
	|                        | centre (latitude and longitude) and a radius.   |
	+------------------------+--------------+---------------+------------------+
	| Binary encoding        | Two signed numeric values in format IEEE        |
	|                        | P754. The first one represents the latitude,    |
	|                        | while the second one represents the longitude.  |
	|                        | Both values must be interpreted in degrees.     |
	|                        | A third integer value (32 bit) represents       |
	|                        | radius in meters. Negative values are not       |
	|                        | allowed an max value is 1000000 m (1000 km).    |
	|                        | It follows an optional UTF-8 string, with the   |
	|                        | position address.                               |
	+------------------------+--------------+---------------+------------------+
	| String representation  | The address, if available, followed by radius,  |
	|                        | otherwise the coordinates, separated by comma.  |
	|                        | Every coordinate uses dot as decimal separator. |
	+------------------------+--------------+---------------+------------------+
	| Exportable values      | Yes                                             |
	+------------------------+--------------+---------------+------------------+

Trigger Location In
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. cssclass:: table-bordered

+--------------+---------------+--------------+--------------+-----------------+
| Name         | IN                                                            |
+--------------+---------------+--------------+--------------+-----------------+
| Description  | Executes when entering into an Area.                          |
+--------------+---------------+--------------+--------------+-----------------+

.. cssclass:: table-bordered

+--------------+---------------+--------------+--------------+-----------------+
|              | Type          | Name         | Required     | Description     |
+==============+===============+==============+==============+=================+
| Parameters   | LOCATION.AREA | AREA         | Yes          | Area to monitor |
+--------------+---------------+--------------+--------------+-----------------+

Trigger Location Out
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. cssclass:: table-bordered

+--------------+---------------+--------------+--------------+-----------------+
| Name         | OUT                                                           |
+--------------+---------------+--------------+--------------+-----------------+
| Description  | Executes when exiting from an Area.                           |
+--------------+---------------+--------------+--------------+-----------------+

.. cssclass:: table-bordered

+--------------+---------------+--------------+--------------+-----------------+
|              | Type          | Name         | Required     | Description     |
+==============+===============+==============+==============+=================+
| Parameters   | LOCATION.AREA | AREA         | Yes          | Area to monitor |
+--------------+---------------+--------------+--------------+-----------------+

Condition Checker In
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. cssclass:: table-bordered

+--------------+---------------+--------------+--------------+----------------------+
| Name         | IN                                                                 |
+--------------+---------------+--------------+--------------+----------------------+
| Description  | Returns *true* if current position is inside the specified area.   |
+--------------+---------------+--------------+--------------+----------------------+

.. cssclass:: table-bordered

+--------------+---------------+--------------+--------------+----------------------+
|              | Type          | Name         | Required     | Description          |
+==============+===============+==============+==============+======================+
| Parameters   | LOCATION.AREA | AREA         | Yes          | Area to monitor      |
+--------------+---------------+--------------+--------------+----------------------+

Condition Checker Out
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. cssclass:: table-bordered

+--------------+---------------+--------------+--------------+------------------------+
| Name         | OUT                                                                  |
+--------------+---------------+--------------+--------------+------------------------+
| Description  | Returns *true* if current position is outside of the specified area. |
+--------------+---------------+--------------+--------------+------------------------+

.. cssclass:: table-bordered

+--------------+---------------+--------------+--------------+------------------------+
|              | Type          | Name         | Required     | Description            |
+==============+===============+==============+==============+========================+
| Parameters   | LOCATION.AREA | AREA         | Yes          | Area to monitor        |
+--------------+---------------+--------------+--------------+------------------------+
