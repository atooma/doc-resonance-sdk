.. _module-sync:

Data Sync
--------------------------

* **Identifier** - DATA-SYNC
* **Description** - Allows to control background synchronization settings.
* **Current Version** - 1
* **Initialization** - None
* **Destroy** - None
* **Dependencies** - :ref:`module-core`

Trigger Sync Enabled
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. cssclass:: table-bordered

+--------------+--------------------------------------------------------------------------------------------------------+
| Name         | ON                                                                                                     |
+--------------+--------------------------------------------------------------------------------------------------------+
| Description  | Enable background synchronization.                                                                     |
+--------------+--------------------------------------------------------------------------------------------------------+

Trigger Sync Disabled
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. cssclass:: table-bordered

+--------------+--------------------------------------------------------------------------------------------------------+
| Name         | OFF                                                                                                    |
+--------------+--------------------------------------------------------------------------------------------------------+
| Description  | Disable background synchronization.                                                                    |
+--------------+--------------------------------------------------------------------------------------------------------+

In practice, using this module will have the effect of controlling account synchronization flag reported in the screenshot below (taken from a Google Nexus 5).

.. _fig_module_sync:

	.. image:: _static/img/module_sync_1.png
	   :width: 40 %
	   :alt: Configuration Flag

	.. image:: _static/img/module_sync_2.png
	   :width: 40 %
	   :alt: Configuration Warning
	   :align: right
