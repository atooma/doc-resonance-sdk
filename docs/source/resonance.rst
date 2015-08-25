.. _resonance:

Resonance API Client
=======================================

Target of this section is to provide a high level description of the core concepts behind the Resonance environment. Focus will be on SDK main features and how to exploit them.

``ResonanceApiClient`` is a singleton class that should be started together with Android Application.

It follows common logic to be adopted for a Resonance ready application:

.. code-block:: java
  :linenos:

  public class ResonanceApp extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        // building ResonanceApiClient instance
        ResonanceApiClient client = ResonanceApiClient.with(this);
        // starting ResonanceApiClient instance
        client.start();
    }
  }

In reported code, logic for initializing ``ResonanceApiClient`` instance is encapsulated into ``with()`` method, that is responsible for defining several parameters in several areas (e.g. parameters for activity tracking, endpoints base url and so on). Nevertheless it's possible on client side to exploit ``ResonanceApiClient.Builder`` class for manually define ``ResonanceApiClient`` configuration, but this practice is discouraged in most cases, since basic settings are commonly adequate for most cases.

.. note::

  As discussed in :ref:`example` section, it's possible to configure some Resonance SDK components to be executed within a dedicated private process. In such case, strategy to be adopted for initializing and starting ``ResonanceApiClient`` class is slightly different. Suggestion is to invoke an ``IntentService`` within own ``Application`` class and there to prevent initialization and start of multiple ``ResonanceApiClient`` instances. This is possible because, regardless of process that is executing ``Application`` code, ``IntentService`` method ``onHandleIntent`` will be always executed within the same process, defined at Manifest level.


Data Collector
---------------------------------------

When starting ``ResonanceApiClient`` instance, ResonanceSDK automatically starts collecting data concerning device usage. Data are periodically delivered to server with adequate strategy for minimizing battery consumption, and allow to perform analysis on user behavior, determining relevant information about his preferences. This is essential for making predictions as well as for identifying changes in his regular behavioral pattern.

Going in depth with description of data collector is not essential in this documentation, since developer doesn't need to interact with it in any way.

.. _resonance-advisor:

Advisor
---------------------------------------

Outcome of data analysis performed by Atooma backend is made available through a set of useful functions implemented by a dedicated class that is ``ResonanceAdvisor``. Below is shown code for getting such instance:

.. code-block:: java
  :linenos:

  Context context = getApplicationContext();
  ResonanceAdvisor advisor = ResonanceApiClient.with(context).getAdvisor();

Advised Locations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Locations by kind
Confidence

Points of Interest
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Points of interest by kind

User Routine
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Points of interest by kind
