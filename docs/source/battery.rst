.. _battery:

Monitoring Battery Usage
=======================================

Target of this section is providing a detailed guideline on how to track and display battery usage, with reference to specific running applications. Please notice that all steps mentioned in following sections require a device equipped with Android 5.0 or higher.

For further information on tools and strategies you can always refer to official Android Developers website, `here <https://developer.android.com/tools/performance/batterystats-battery-historian/index.html>`_ and `here <https://developer.android.com/tools/performance/batterystats-battery-historian/charts.html>`_.

Getting Stats from Device
---------------------------------------

In order to get stats from your device, enable *debugging mode* by accessing to *Developer Options* section within *Settings > System*. Once this step is completed, connect device to your computer and execute following shell commands:

.. code-block:: bash
  :linenos:

  $ adb shell dumpsys batterystats or adb shell dumpsys batterystats --enable full-wake-history
  $ adb bugreport > bugreport.txt

Use command ``adb shell dumpsys batterystats --reset`` for resetting battery stats, once dump has been taken.
Please notice that by enabling full wakelock reporting the battery history log overflows in a few hours. Use this option for short test runs (3-4 hrs).

Displaying Captured Data
---------------------------------------

`Battery Historian Tool <https://github.com/google/battery-historian>`_ can be effectively used for displaying battery stats dumps captured on device. In order to use it you can run a server locally by setting up your environment for using `Go <https://golang.org/>`_ language:

.. code-block:: bash
  :linenos:

  $ go run cmd/battery-historian/battery-historian.go

Once server is running you can open browser, access url *http://localhost:9999*, then upload your *bugreport.txt*.
In the first page look into Top power consuming entities to see Atooma's impact on the battery.
Tab Historian 2.0 allows to see duration of each individual wakelock within com.atooma.wl process.

Alternatively it's possible to use `Docker <https://www.docker.com/>`_ and exploit an image ready with Battery Historian Tool and available on a dedicated `Bintray <https://bintray.com/>`_ repository:

.. code-block:: bash
  :linenos:

  $ docker login -u username -p apikey -e email atooma-docker-images.bintray.io
  $ docker pull atooma-docker-images.bintray.io/atooma/battery-historian
  $ docker run -d -p 8080:8080 atooma-docker-images.bintray.io/atooma/battery-historian

where:

* **username** is the username of your Bintray account
* **apikey** is the apikey of your Bintray account
* **email** is the email of your Bintray account

You can then open browser and access url *http://localhost:8080* (or virtual machine ip address if on OS X).
