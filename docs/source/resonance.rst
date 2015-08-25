.. _resonance:

Resonance API Client
=======================================

Target of this section is to provide a high level description of the core concepts behind the Resonance environment. Focus will be on SDK main features and how to exploit them.

Data Collector
---------------------------------------

When starting ``ResonanceApiClient`` instance, ResonanceSDK automatically starts collecting data concerning device usage. Data are periodically delivered to server and allow to perform analysis on user behavior, determining relevant information about his preferences.

.. _resonance-advisor:

Advisor
---------------------------------------

Data analysis performed on Atooma backend is used in advanced functions provided to clients for getting user information.

.. code-block:: java
  :linenos:

  Context context = getApplicationContext();
  ResonanceAdvisor advisor = ResonanceApiClient.with(context).getAdvisor();
