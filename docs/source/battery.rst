.. _battery:

Monitoring Battery Usage
=======================================

Target of this section is providing some indications on how to track and display battery usage, with reference to specific running applications. Please notice that all steps mentioned below requires a device equipped with Android 5.0 or following versions.

Getting stats from device
---------------------------------------

Enable debugging mode on your device (Settings > System > Developer Options).
Connect phone to your computer.
Execute commands:
adb shell dumpsys batterystats or adb shell dumpsys batterystats --enable full-wake-history.
adb bugreport > bugreport.txt
Use command adb shell dumpsys batterystats --reset for resetting battery stats, once dump has been taken.
Please notice that by enabling full wakelock reporting the battery history log overflows in a few hours. Use this option for short test runs (3-4 hrs).

Displaying captured data for analysis
---------------------------------------

Use Battery Historian tool for displaying battery stats dump captured on phone.
Run Go server locally, by executing command: go run cmd/battery-historian/battery-historian.go
On your browser open URL localhost:9999 and upload your bugreport.txt
In the first page look into Top power consuming entities to see Atooma's impact on the battery.
Tab Historian 2.0 allows to see duration of each individual wakelock within com.atooma.wl process.

Using Docker for Battery Historian Tool
------------------------------------------

username: username of your user on bintray
apikey:   apikey of your user on bintray
email:    email of your user on bintray
docker login -u username -p apikey -e email atooma-docker-images.bintray.io
docker pull atooma-docker-images.bintray.io/atooma/battery-historian
docker run -d -p 8080:8080 atooma-docker-images.bintray.io/atooma/battery-historian

open http://localhost (or vm machine ip if on osx):8080
