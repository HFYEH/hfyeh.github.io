---
layout: post
title: "ADB Commands Cheat Sheet"
tags: [android]
---

This is a adb commands memo.

<!--more-->

``` shell
android list targets          # Show all android platforms

# AVD Operations
android list avd              # Show all AVD
android create avd --name <name> --target <target>
android delete avd --name <name>
emulator -avd <name> -sdcard <card>.img (-skin 1280x800)

# Create SD Card
mksdcard 1024M <card>.img

# DDMS
ddms

# ADB
adb devices                   # Show all available devices
adb install -r debug.apk      # Install apk to device
adb pull <remote> <local>     # Retrieve files from device
adb push <local> <remote>     # Write files to device
adb shell                     # Log in to the device
adb root                      # Get root privilege
adb logcat -s <tag>           # View log with given tag
adb forward tcp:5555 tcp:8000 # Port forwarding

```
