---
layout: post
title: Compiling Testing the Ionic App on the Android
---

To be able to compile the Ionic Application, Cordova, Android SDK and ADB must be installed. `npm install -g cordova`.

Plug in the Android phone and turn on developer mode. Run the command `adb devices -l` to get the device's ID.

Next run, `cordova run android --target=YOUR_PHONE_ID` to compile the Ionic Application to an APK package.

Once completed, the application should automatically run on the phone. To view any Problems that occur that you would usually view with the console and command `console.log`, use Chrome's Device Inspection in Chrome with the link `chrome://inspect/#devices`. This should allow the device to view all applications currently running on the phone. Find the newly created app and from there, all logs can be seen. `chrome://flags` also has many other options that can be useful.

### Problems

If the build fails, run `ionic platform rm android` and `ionic platform add android` again. An error might occur because the app's ID is incorrect.

Another problem occurs because Ionic might need to be updated. In this case, run `npm uninstall ionic` and `npm install ionic` to re-install some of the packages.
