---
title: "Android Platform 28 SDK License Not Accepted"
date: 2019-11-10T06:07:07+08:00
draft: false
categories: ["flutter"]
tags: ["flutter","android"]
---

```
> Configure project :core
Checking the license for package Android SDK Platform 28 in /opt/android/sdk/licenses
Warning: License for package Android SDK Platform 28 not accepted.

FAILURE: Build failed with an exception.

* What went wrong:
A problem occurred configuring project ':core'.
> Failed to install the following Android SDK packages as some licences have not been accepted.
     platforms;android-28 Android SDK Platform 28
  To build this project, accept the SDK license agreements and install the missing components using the Android Studio SDK Manager.
  Alternatively, to transfer the license agreements from one workstation to another, see http://d.android.com/r/studio-ui/export-licenses.html
  
  Using Android SDK: /opt/android/sdk

* Try:
Run with --stacktrace option to get the stack trace. Run with --info or --debug option to get more log output. Run with --scan to get full insights.

* Get more help at https://help.gradle.org

BUILD FAILED in 28s
Exited with code 1

```

解决方法:

进入 `$Andorid_HOME/tools/bin`,执行`yes | ./sdkmanager --licenses`