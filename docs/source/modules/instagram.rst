.. _module-instagram:

Instagram
--------------------------

* **Identifier** - INSTAGRAM
* **Description** - Implements integration with Instagram.
* **Initialization** - Check availability of Instagram Client ID as meta-data. Set up redirect uri as meta-data (Only the logic of the redirect uri should be implemented and running on a dedicated server). Implementation of Server Side (Explicit) Flow. More info in https://instagram.com/developer/authentication/
* **Destroy** - None
* **Dependencies** - :ref:`module-core`

Trigger New Like
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. cssclass:: table-bordered

+--------------+-------------------+---------------+--------------+-----------------------------+
| Name         | NEW-LIKE                                                                       |
+--------------+-------------------+---------------+--------------+-----------------------------+
| Description  | Executes when a new "like" is received on a user's media posted on Instagram.  |
|              |                                                                                |
+--------------+-------------------+---------------+--------------+-----------------------------+
| Logic        | Periodic check every 30 minutes.                                               |
+--------------+-------------------+---------------+--------------+-----------------------------+

Trigger New Photo From Me
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. cssclass:: table-bordered

+--------------+-------------------+---------------+--------------+-----------------------------+
| Name         | NEW-PHOTO-FROM-ME                                                              |
+--------------+-------------------+---------------+--------------+-----------------------------+
| Description  | Executes when a new photo is posted by the user on Instagram.                  |
|              |                                                                                |
+--------------+-------------------+---------------+--------------+-----------------------------+
| Logic        | Periodic check every 20 minutes.                                               |
+--------------+-------------------+---------------+--------------+-----------------------------+

Trigger Photo From Following
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. cssclass:: table-bordered

+--------------+-------------------+---------------+--------------+-----------------------------+
| Name         | NEW--PHOTO-FROM-FOLLOWING                                                      |
+--------------+-------------------+---------------+--------------+-----------------------------+
| Description  | Executes when a new photo is posted by a user followed by the current user.    |
|              |                                                                                |
+--------------+-------------------+---------------+--------------+-----------------------------+
| Logic        | Periodic check every 20 minutes.                                               |
+--------------+-------------------+---------------+--------------+-----------------------------+

Trigger Video From Me
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. cssclass:: table-bordered

+--------------+-------------------+---------------+--------------+-----------------------------+
| Name         | NEW-VIDEO-FROM-ME                                                              |
+--------------+-------------------+---------------+--------------+-----------------------------+
| Description  | Executes when a new video is posted by the user on Instagram.                  |
|              |                                                                                |
+--------------+-------------------+---------------+--------------+-----------------------------+
| Logic        | Periodic check every 20 minutes.                                               |
+--------------+-------------------+---------------+--------------+-----------------------------+

Trigger New Video From Following
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. cssclass:: table-bordered

+--------------+-------------------+---------------+--------------+-----------------------------+
| Name         | NEW-VIDEO-FROM-FOLLOWING                                                       |
+--------------+-------------------+---------------+--------------+-----------------------------+
| Description  | Executes when a new video is posted by a user followed by the current user.    |
|              |                                                                                |
+--------------+-------------------+---------------+--------------+-----------------------------+
| Logic        | Periodic check every 20 minutes.                                               |
+--------------+-------------------+---------------+--------------+-----------------------------+
