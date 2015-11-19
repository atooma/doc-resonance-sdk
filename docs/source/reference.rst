.. _reference:

Modules API Reference
=======================================

This section provide all details concerned with available SDK modules, with specifications for all related components (e.g. :ref:`coretrigger`, :ref:`coreconditionchecker`, :ref:`coreperformer`).

Please notice that some modules require OAuth authentication (e.g. :ref:`module-facebook`, :ref:`module-gmail`). More details on how to exploit such modules by effectively handling authentication, will be provided in section :ref:`module-oauth`.

.. cssclass:: table-bordered

+-------------------------------+------------------------------------------------------------------------------------------------------------------+---------------+---------+-------------------------+
| Module                        | Description                                                                                                      | Auth          | Version | Dependencies            |
+===============================+==================================================================================================================+===============+=========+=========================+
| :ref:`module-activitymanager` | Allows to handle applications installed on device.                                                               |               | 1       | :ref:`module-core`      |
+-------------------------------+------------------------------------------------------------------------------------------------------------------+---------------+---------+-------------------------+
| :ref:`module-airplane`        | Allows to control airlplane mode.                                                                                |               | 3       | :ref:`module-core`      |
+-------------------------------+------------------------------------------------------------------------------------------------------------------+---------------+---------+-------------------------+
| :ref:`module-alarm`           | Defines functionalities concerned with alarms and temporal controls.                                             |               | 3       | :ref:`module-core`      |
+-------------------------------+------------------------------------------------------------------------------------------------------------------+---------------+---------+-------------------------+
| :ref:`module-battery`         | Allows to control functionalities concerned with device battery.                                                 |               | 1       | :ref:`module-core`      |
+-------------------------------+------------------------------------------------------------------------------------------------------------------+---------------+---------+-------------------------+
| :ref:`module-bluetooth`       | Allows to control Bluetooth on device.                                                                           |               | 4       | :ref:`module-core`      |
+-------------------------------+------------------------------------------------------------------------------------------------------------------+---------------+---------+-------------------------+
| :ref:`module-camera`          | Defines functionalities for controlling device camera.                                                           |               | 1       | :ref:`module-core`      |
+-------------------------------+------------------------------------------------------------------------------------------------------------------+---------------+---------+-------------------------+
| :ref:`module-contacts`        | Allows interfacing with contacts available on device.                                                            |               | 1       | :ref:`module-core`      |
+-------------------------------+------------------------------------------------------------------------------------------------------------------+---------------+---------+-------------------------+
| :ref:`module-core`            | Defines core components for building rules and provides all basic value types to be used in other modules.       |               | 1       | \-                      |
+-------------------------------+------------------------------------------------------------------------------------------------------------------+---------------+---------+-------------------------+
| :ref:`module-sync`            | Allows to control background synchronization settings.                                                           |               | 1       | :ref:`module-core`      |
+-------------------------------+------------------------------------------------------------------------------------------------------------------+---------------+---------+-------------------------+
| :ref:`module-dropbox`         | Implements integration with Dropbox.                                                                             | Yes           | 4       | :ref:`module-core`      |
+-------------------------------+------------------------------------------------------------------------------------------------------------------+---------------+---------+-------------------------+
| :ref:`module-facebook`        | Implements integration with Facebook.                                                                            | Yes           | 1       | :ref:`module-core`      |
+-------------------------------+------------------------------------------------------------------------------------------------------------------+---------------+---------+-------------------------+
| :ref:`module-gcalendar`       | Implements integration with Google Calendar.                                                                     | Yes           | 1       | :ref:`module-core`      |
|                               |                                                                                                                  |               |         | :ref:`module-location`  |
+-------------------------------+------------------------------------------------------------------------------------------------------------------+---------------+---------+-------------------------+
| :ref:`module-gdrive`          | Implements integration with Google Drive.                                                                        | Yes           | 5       | :ref:`module-core`      |
+-------------------------------+------------------------------------------------------------------------------------------------------------------+---------------+---------+-------------------------+
| :ref:`module-gmail`           | Implements integration with Gmail.                                                                               | Yes           | 4       | :ref:`module-core`,     |
|                               |                                                                                                                  |               |         | :ref:`module-contacts`  |
+-------------------------------+------------------------------------------------------------------------------------------------------------------+---------------+---------+-------------------------+
| :ref:`module-headphone`       | Allows to check headphones presence.                                                                             |               | 1       | :ref:`module-core`      |
+-------------------------------+------------------------------------------------------------------------------------------------------------------+---------------+---------+-------------------------+
| :ref:`module-instagram`       | Implements integration with Instagram.                                                                           | Yes           | 4       | :ref:`module-core`      |
+-------------------------------+------------------------------------------------------------------------------------------------------------------+---------------+---------+-------------------------+
| :ref:`module-location`        | Allows to determine device position.                                                                             |               | 3       | :ref:`module-core`      |
+-------------------------------+------------------------------------------------------------------------------------------------------------------+---------------+---------+-------------------------+
| :ref:`module-notification`    | Allows to show notifications during rule execution.                                                              |               | 1       | :ref:`module-core`      |
+-------------------------------+------------------------------------------------------------------------------------------------------------------+---------------+---------+-------------------------+
| :ref:`module-shake`           | Provides features to handle and react to shake gestures.                                                         |               | 1       | :ref:`module-core`      |
+-------------------------------+------------------------------------------------------------------------------------------------------------------+---------------+---------+-------------------------+
| :ref:`module-sms`             | Allows to send and receive SMS.                                                                                  |               | 1       | :ref:`module-core`,     |
|                               |                                                                                                                  |               |         | :ref:`module-contacts`, |
|                               |                                                                                                                  |               |         | :ref:`module-telephony` |
+-------------------------------+------------------------------------------------------------------------------------------------------------------+---------------+---------+-------------------------+
| :ref:`module-telephony`       | Allows to control basic device features.                                                                         |               | 1       | :ref:`module-core`,     |
|                               |                                                                                                                  |               |         | :ref:`module-contacts`  |
+-------------------------------+------------------------------------------------------------------------------------------------------------------+---------------+---------+-------------------------+
| :ref:`module-tts`             | Executes Text To Speech functionalities.                                                                         |               | 1       | :ref:`module-core`      |
+-------------------------------+------------------------------------------------------------------------------------------------------------------+---------------+---------+-------------------------+
| :ref:`module-twitter`         | Implements integration with Twitter.                                                                             | Yes           | 1       | :ref:`module-core`      |
+-------------------------------+------------------------------------------------------------------------------------------------------------------+---------------+---------+-------------------------+
| :ref:`module-weather`         | Provides utilities for weather forecast and for monitoring current weather conditions.                           |               | 2       | :ref:`module-core`,     |
|                               |                                                                                                                  |               |         | :ref:`module-location`  |
+-------------------------------+------------------------------------------------------------------------------------------------------------------+---------------+---------+-------------------------+
| :ref:`module-wifi`            | Allows to control WiFi on device.                                                                                |               | 3       | :ref:`module-core`      |
+-------------------------------+------------------------------------------------------------------------------------------------------------------+---------------+---------+-------------------------+


.. _module-oauth:

OAuth Based Modules
------------------------------------

All modules implementing integration with external services require an authentication flow to be implemented before using them. Common choice in this sense is **OAuth**, that is an open standard for authorization. OAuth provides client applications a *secure delegated access* to server resources on behalf of a resource owner. In particular, idea behind OAuth is to allow alphanumeric **access tokens** to be issued to third-party clients by an authorization server, with the approval of the resource owner. The client then uses the access token to access the protected resources hosted by the resource server.

All modules having access to an external service need to implement the corresponding OAuth flow (in some cases like Facebook, such flow is already implemented by a dedicated SDK). Target is getting a valid access token to be used for all requests to the external server. Atooma SDK defines ab abstract class ``ChannelHandler`` that is used for implementing the relationship between a module and its OAuth flow. Such class provides three main methods and its usage requires three abstract methods to be implemented.

.. code-block:: java
  :linenos:

  /**
   * To be invoked for connecting to a specific channel,
   * retrieving corresponding valid access token.
   */
  public void connect(Context context);

  /**
   * To be invoked for disconnecting from a specific channel,
   * clearing stored access token.
   */
  public void disconnect(Context context);

  /**
   * To be invoked for checking whether a valid token
   * is already available or not. This method can be
   * used for deciding whether to activate a rule or not.
   */
  public boolean isConnected(Context context);

  /**
   * Method implementing logic for starting OAuth flow
   * for the current channel.
   */
  abstract protected void doConnection(Activity source);

  /**
   * Method implementing logic for retrieving username
   * from outcome of the OAuth flow execution.
   */
  abstract protected String extractUserFromResult(Intent data);

  /**
   * Method implementing logic for retrieving access token
   * from outcome of the OAuth flow execution.
   */
  abstract protected String extractTokenFromResult(Intent data);



Dropbox
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The OAuth authentication flow via Dropbox is already implemented by a dedicated SDK. The only information required to perform the authentication is the setting of some data within the Manifest file:

.. code-block:: xml
  :linenos:

  <meta-data
    android:name="dropbox_app_key"
    android:value="YOUR_APP_KEY"/>

  <meta-data
    android:name="dropbox_app_secret"
    android:value="YOUR_APP SECRET"/>


This data can be found in Dropbox Developer page, within the settings of your own application.

Besides the user application's data another necessary information is required, that is the declaration of the Dropbox Authentication Activity within the Manifest.

.. code-block:: xml
  :linenos:

  <activity
    android:name="com.dropbox.client2.android.AuthActivity"
    android:configChanges="orientation|keyboard"
    android:launchMode="singleTask"
    android:screenOrientation="portrait">
    <intent-filter>
      <!-- Change this to be 'db-' followed by your app key -->
      <data android:scheme="db-YOUR_APP_KEY"/>
      <action android:name="android.intent.action.VIEW" />
      <category android:name="android.intent.category.BROWSABLE" />
      <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
  </activity>


Google
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Let's consider for example Gmail module. A possible implementation of ``ChannelHandler`` is reported below:

.. code-block:: java
  :linenos:

  public class GmailChannelHandler extends ChannelHandler {

    @Override
    protected void doConnection(Activity source) {
      Intent intent = new Intent(source, GmailActivity.class);
      source.startActivityForResult(intent, 8888);
    }

    @Override
    protected String extractUserFromResult(Intent data) {
      return data.getStringExtra(GoogleOAuth2Activity.PARAM_EMAIL);
    }

    @Override
    protected String extractTokenFromResult(Intent data) {
      return data.getStringExtra(GoogleOAuth2Activity.PARAM_TOKEN);
    }

    @Override
    public boolean isConnected(Context context) {
      return !TextUtils.isEmpty(getToken(context));
    }

  }

Where ``GoogleOAuth2Activity`` is an SDK ``Activity`` that encapsulates the entire Google Oauth2 flow, simplifying the integration of Google services. As an example, for Gmail we'll have following implementation:

.. code-block:: java
  :linenos:

  public class GmailActivity extends GoogleOAuth2Activity {

    @Override
    protected String getScope() {
      return "https://mail.google.com/";
    }

  }

In case ``Play Services`` are not available, ``GoogleOAuth2Activity``
will handle authentication via web but it requires the setup of two meta-data fields
in the manifest:

.. code-block:: xml
  :linenos:

  <meta-data
    android:name="com.google.oauth.ClientId"
    android:value="your CLIENT_ID" />

  <meta-data
    android:name="com.google.oauth.ClientSecret"
    android:value="your CLIENT_SECRET" />


Instagram
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Authentication via Instagram requires additional meta-data fields in the manifest:

.. code-block:: xml
  :linenos:

  <meta-data
    android:name="instagram_client_id"
    android:value="YOUR_CLIENT_ID"/>

  <meta-data
    android:name="instagram_client_secret"
    android:value="YOUR_CLIENT_SECRET"/>

This data can be found in Instagram Developer page, within the settings of your own application. Make sure your redirect uri of your own instagram application is set to 'atooma://instagram'.

If all these steps are performed correctly, the user authenticated with your Instagram Application will be able to use the the facilities provided by the SDK.


Twitter
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Authentication via Twitter requires additional meta-data fields in the manifest:

.. code-block:: xml
  :linenos:

  <meta-data
    android:name="twitter_app_id"
    android:value="YOUR_APP_ID"/>

  <meta-data
    android:name="twitter_consumer_key"
    android:value="YOUR_CONSUMER_KEY"/>

  <meta-data
    android:name="twitter_consumer_secret"
    android:value="YOUR_CONSUMER_SECRET"/>

  <meta-data
    android:name="twitter_redirect_uri"
    android:value="YOUR_REDIRECT_URI"/>

This data can be found in Twitter Developer page, within the settings of your own application.
The redirect URI should be implemented on a dedicated server. The logic of the endpoint of the redirect URI must follow the instructions written `here <https://dev.twitter.com/web/sign-in/implementing>`_ on Twitter Developers Web Site.
Essentially, your endpoint will receive two parameters from Twitter: oauth_token and oauth_verifier. All you have to do is performing a HTTP POST to https://api.twitter.com/oauth/access_token in order to convert the request token to an access token. This POST request must contain the Authorization header with your Consumer Key, your Consumer Secret, and the oauth_token just received. The request must also contain the oauth_verifier obtained, with Content-Type: application/x-www-form-urlencoded.
A successful response contains the oauth_token, oauth_token_secret, user_id, and screen_name parameters. All you have to do is returning all these parameters in a JSON format. If all these steps are performed correctly, the user authenticated with your Twitter Application will be able to use the Triggers and Performers provided by the SDK.


.. include:: modules/activitymanager.rst
.. include:: modules/airplane.rst
.. include:: modules/alarm.rst
.. include:: modules/battery.rst
.. include:: modules/bluetooth.rst
.. include:: modules/camera.rst
.. include:: modules/contacts.rst
.. include:: modules/core.rst
.. include:: modules/sync.rst
.. include:: modules/dropbox.rst
.. include:: modules/facebook.rst
.. include:: modules/gcalendar.rst
.. include:: modules/gdrive.rst
.. include:: modules/gmail.rst
.. include:: modules/headphone.rst
.. include:: modules/instagram.rst
.. include:: modules/location.rst
.. include:: modules/notification.rst
.. include:: modules/shake.rst
.. include:: modules/sms.rst
.. include:: modules/telephony.rst
.. include:: modules/tts.rst
.. include:: modules/twitter.rst
.. include:: modules/wifi.rst
.. include:: modules/weather.rst
