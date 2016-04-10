#Setup for the Adafruit Huzzah
-------------

1. Make sure you follow the [Adafruit setup guide](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide)
2. Download or clone this library into your <arduinosketchfolder>/libraries/ folder
    * First, if you don't have a 'libraries' folder, create it!
    * Download: 
        * Click the DOWNLOADS above, and download a zip of this repo 
        * Move the whole folder into <arduinosketchfolder>/libraries/
        * Rename the folder to "Spacebrew"
    * Clone:
        ```
        $cd <arduinosketchfolder>/libraries/
        $git clone https://github.com/mattfelsen/spacebrew-esp8266.git Spacebrew
        ```

3. Download dependencies:
    * https://github.com/Links2004/arduinoWebSockets
    * https://github.com/me-no-dev/ESPAsyncTCP

4. Edit the Websockets library to turn on 'async' mode
    * Open ```Websockets.h``` in arduinoWebSockets/src
    * Comment out line 57, and uncomment line 58. It should look like this:
    ```
    #if defined(ESP8266) || defined(ESP31B)
    //#define WEBSOCKETS_NETWORK_TYPE NETWORK_ESP8266
    #define WEBSOCKETS_NETWORK_TYPE NETWORK_ESP8266_ASYNC
    #else
    #define WEBSOCKETS_NETWORK_TYPE NETWORK_W5100
    #endif
    ```

5. Restart the Arduino IDE, if you had it running!

includes
--------------

These should be placed at the top of your sketch:

<pre>
#include &lt;SPI.h>
#include &lt;Spacebrew.h>
#include &lt;Ethernet.h>
#include &lt;WebSocketClient.h>
</pre>

setup
--------------
* At the top of the file:
    * ```Spacebrew sb;```
* In setup:
    * ```Ethernet.start(mac);//as per the Ethernet library example, where mac is a byte[6]```
* In loop:
    * ```sb.monitor();```

usage
--------------
publishing
==============

* range:
    * `sb.addPublish("PubName", RANGE);`
    * `sb.addPublish("OtherPubName", 255);`
* string:
    * `sb.addPublish("PubStringName", STRING);`
    * `sb.addPublish("AnotherStringOutput", "default string");`
* boolean:
    * `sb.addPublish("BoolPub", BOOLEAN);`
    * `sb.addPublish("Different_Bool", true);`
* other:
    * `sb.addPublish("Custom Publisher", "img_url");`

subscribing
=============
* range:
    * `sb.addSubscribe("SubName", RANGE);`
    * `sb.addSubscribe("OtherSubName", "range");`
* string:
    * `sb.addSubscribe("SubStringName", STRING);`
    * `sb.addSubscribe("AnotherStringInput", "string");`
* boolean:
    * `sb.addSubscribe("BoolSub", BOOLEAN);`
    * `sb.addSubscribe("Different_Bool", "boolean");`
* other:
    * `sb.addSubscribe("Custom Subscriber", "crazy_type");`

receving messages from subscribers
=========================
* range:
    sb.onRangeMessage(receivedRange);
    and then...
<pre>
    void receivedRange(char* name, int value){
        Serial.print("[onRangeMessage]");
        Serial.print(name);
        Serial.print(": ");
        Serial.println(value);
    }
</pre>
* string:
    sb.onStringMessage(receivedString);
    and then...
<pre>
    void receivedString(char* name, char* value){
        Serial.print("[onStringMessage]");
        Serial.print(name);
        Serial.print(": ");
        Serial.println(value);
    }
</pre>
* boolean:
    sb.onBooleanMessage(receivedBool);
    and then...
<pre>
    void receivedBool(char* name, bool value){
        Serial.print("[onBoolMessage]");
        Serial.print(name);
        Serial.print(": ");
        Serial.println(value ? "true" : "false);
    }
</pre>
* other:
    sb.onOtherMessage(receivedOther);
    and then...
<pre>
    void receivedOther(char* name, char* value){
        Serial.print("[onOtherMessage]");
        Serial.print(name);
        Serial.print(": ");
        Serial.println(value);
    }
</pre>

connecting to a server
=================
```
uint8_t mac[] = {0xa4, 0xe7, 0xee, 0x03, 0xfd, 0x32};
Ethernet.begin(mac);
sb.connect("sandbox.spacebrew.cc", "Rename_Me", "Inputs and Outputs?");
```

Where:
* `mac`: the mac address for the Ethernet library to get an IP, may need to be unique
* `"sandbox.spacebrew.cc"`: the address for the computer where you are running the spacebrew server
* `"Rename_Me"`: the name for this client
* `"Inputs and Outputs?"`: a description for this client which will be exposed via the admin


sending messages via publishers
================
* range:
    ```sb.send("myRangePub", 255);```
* string:
    ```sb.send("someStringOutput", "bang!");```
* boolean:
    ```sb.send("myBoolPub", true);```
* other:
    ```sb.send("customOutput", "img_url", "http://nyopoliticker.files.wordpress.com/2012/08/614px-hallandoatesalbumcover.jpeg");```
