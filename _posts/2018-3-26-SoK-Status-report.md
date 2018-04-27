---
layout: post
title: SoK Status Report
subtitle: Technical details
tags: ['KDE','Season of KDE','Kdenlive']
---

Here, I have added the technical details of the SoK work. 

Project Name: Improve on monitor controls for image transformations in Kdenlive

A Brief Description: For this SoK, I had to accomplish the following task : In addition to resizing and moving the image on the monitor, adding the functionality to –

    Adjust rotation directly on monitor screen
    Adjust opacity directly on monitor screen

Status of the project: Complete.

Mentor: Jean-Baptiste Mardelle

Implementation:

The work involved creating qml files for sliders on screen which when moved, the effect should persist in the video frame selected. For this I created OpacitySlider.qml and RotationSlider.qml files. The orientation of these sliders on screen is managed in another qml file - kdenlivemonitoreffectscene.qml.

The use of signal and slots from Qt C++ is the fundamental unit in this work. When the sliders are operated, signals are emitted from the root of the sliders. These signals are connected from the qml files with qmlmanager.cpp file. The functions to emit the signals are written in qmlmanager.cpp.

After this, the connect call occurs between qmlmanager.cpp and monitor.cpp to provide the effect on screen. Further the use of widgets to handle the above call is done separately for rotation and opacity slider in keyframewidget.cpp and geometrywidget.cpp files respectively.

Here is the diagram I have created to explain the connect calls between the files : 

![SoK-Tech](/img/SoK-work.jpg)

**What I learnt during SoK**

Qml

Advanced functions in C++ (signal and slot in Qt)

Work with CMake tool

Neon OS usage

Work on Arcanist and Phabricator tools.
