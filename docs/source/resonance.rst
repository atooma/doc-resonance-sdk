.. _resonance:

Resonance API Client
=======================================

Target of this section is to provide a high level description of the core concepts behind the Resonance environment. Focus will be on SDK main features and how to exploit them.

Theoretical Basis
---------------------------------------

Information about the activities listed in :ref:`activity-tracking-events` and the snapshot of the device containing geographical data of itself are uploaded on our server for storage and periodical analysis in order to identify:

- **Recurrent locations** - The system can identify the coordinates of the user's home and job defining as home "the place where the user sleeps" and as work "the major location where every-days activities are done".

- **User's routine** - The algorithms developed estimate the probability that the user is performing a specific activity at a specific time.

- **Points of interest** - Restaurants, cinemas, theaters, museums and so on, according to user's locations.

To process all these data we use temporal series analysis strategies as: study and evaluation of chaotic attractor regarding users movements, determination of maximum Lyapunov exponent :ref:`[ Wolf1985 ] <resonance-bib>`, :ref:`[ Mukherjee1997 ] <resonance-bib>`, :ref:`[ Franchi2014 ] <resonance-bib>` and clustering algorithms as *K-means* and *G-means*.

.. _resonance-class:

*ResonanceApiClient* class
---------------------------------------

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

Basing on data provided by data collector, Resonance SDK is able to provide details on locations that are frequently accessed by user. In particular, ``ResonanceAdvisor`` implements the following three methods:

.. code-block:: java
  :linenos:

  // Returns a list of locations visited by the current user / device
  void getRecurrentLocations(AdvisedElementsResponseHandler<AdvisedLocation> listener);

  // Returns a list of locations matching home type, visited by the current user / device
  void getHomeLocation(AdvisedElementsResponseHandler<AdvisedLocation> listener);

  // Returns a list of locations matching work type, visited by the current user / device
  void getWorkLocation(AdvisedElementsResponseHandler<AdvisedLocation> listener);

All methods return location asynchronously, through the ``AdvisedElementsResponseHandler`` interface, as shown below:

.. code-block:: java
  :linenos:

  advisor.getRecurrentLocation(new AdvisedElementsResponseHandler<AdvisedLocation>() {
    @Override
    public void onAdvisedElementsRetrievedListener(List<AdvisedLocation> locations) {
      // work with locations here
    }
  });

For all locations some relevant data are reported, allowing user to more easily decide whether to exploit them or not:

* **Time Spent** -
* **Hours** -

Moreover, in case of home and work, an additional **confidence** parameter is included, that is the probability reflecting the reliability of provided information.

Points of Interest
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

``ResonanceAdvisor`` can be used at any time for retrieving information on possible points of interest that are available in current device location, according to the specified search criteria reflecting point type.

.. code-block:: java
  :linenos:

  advisor.getRestaurants(new AdvisedElementsResponseHandler<AdvisedRestaurant>() {
    @Override
    public void onAdvisedElementsRetrievedListener(List<AdvisedRestaurant> elements) {
      // execute code
    }
  });

User Routine
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

User routine are a series of activities that users perfom during the week. Studing these routine enables to forecast the timeline of the users activities (e.g. what activity comes next), thus we can develop user - device interaction methods to give them information and suggestions to improve their everyday life.

Below is reported an example of how to retrieve user routine for a specific day of the week, as a list of ``AdvisedRoutineItem`` instances.

.. code-block:: java
  :linenos:

  advisor.getRoutine(ResonanceAdvisor.ROUTINE_TYPE_MONDAY,
        new AdvisedElementsResponseHandler<AdvisedRoutineItem>() {
      @Override
      public void onAdvisedElementsRetrievedListener(List<AdvisedRoutineItem> elements) {
        // execute code
      }
  });


Next Steps
---------------------------------------

A new system that harvest information from other services (*Facebook*, *Twitter*, *Openstreetmap*, *Tripadvisor*) and wearable devices is currently at study. It will improve the recognition of the activity currently performed and will enable us to develop suggestion and tips for users regarding their activities: have dinner, see a movie, listen to a concert and so on.

.. _resonance-bib:

Bibliography
---------------------------------------

+-----------------------+--------------------------------------------------------------------------------------------------------+
| **[ Wolf1985 ]**      | Wolf, A., et al. "Determining Lyapunov exponents from a time series."Physica D: Nonlinear Phenomena    |
|                       | 16.3 (1985): 285-317.                                                                                  |
+-----------------------+--------------------------------------------------------------------------------------------------------+
| **[ Mukherjee1997 ]** | Mukherjee, S., and Edgar, O., and Federico G.. "Nonlinear prediction of chaotic time series using      |
|                       | support vector machines." Neural Networks for Signal Processing [1997] VII. Proceedings of the 1997    |
|                       | IEEE Workshop. IEEE, 1997.                                                                             |
+-----------------------+--------------------------------------------------------------------------------------------------------+
| **[Franchi2014]**     | Franchi, M., and Leonardo R.. "Statistical properties of the maximum Lyapunov exponent calculated via  |
|                       | the divergence rate method." Physical Review E 90.6 (2014): 062920.                                    |
+-----------------------+--------------------------------------------------------------------------------------------------------+
