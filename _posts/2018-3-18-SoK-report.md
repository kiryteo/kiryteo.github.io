---
layout: post
title: Season of KDE, 2018
subtitle: Final Report
tags: ['KDE','Season of KDE','Kdenlive']
---

Season of KDE is an open source application development programme
hosted by KDE.

I worked for the past two months on one of the most popular open source
video editor – Kdenlive. I would like to thank KDE and Kdenlive community
for giving me this opportunity to explore and work on such an amazing
project.

For this SoK, I had to accomplish the following task :
In addition to resizing and moving the image on the monitor, adding the
functionality to -
1. Adjust rotation directly on monitor screen
2. Adjust opacity directly on monitor screen

**Implementation details**

The idea was to provide sliders on screen for user to easily adjust the
rotation and opacity features of the selected frame. The task required the
thorough implementation of the ‘signal and slot’feature of Qt
embedded into C++. For implementing the sliders and their orientation on
screen I used qml.

![Earlier version](/img/before-work.png)


The signal and slot feature helps in triggering the changes from function to
function present in different files. Whenever the slider value changes, a
signal is emitted (function call) and corresponding slot (another function to
receive this event) is called for the effect to take place. Value change needs
to be triggered in transform section in effects category and also the effect
should persist in the selected frame.


![Current version](/img/after-work.png)

**Verdict**

During this SoK, I also started using the amazing Ubuntu-based KDE Neon
OS for its suitable system Qt which was required for the project. The work
also involved cmake build tool, arcanist tool and use of Phabricator for
code review and discussion. I learned a lot while working with these tools
other than the project work. The building process for Kdenlive included
many dependencies. This process required simplification which I tried
after going through the build process many times. I modified the
Kdenlive/KF5 development build process in the documentation.
The Season of KDE has been a wonderful learning experience for me and it
has taught me a lot of new things which would surely help me in my
further projects.
Kdenlive has been the first major open source project I have been a part of
and to have worked upon. I would especially like to thank my mentor Jean-
Baptiste Mardelle for his continuos guidance and support throughout the
Season of KDE. At multiple times during this work, his help has been
crucial.
I will continue my contribution to Kdenlive, trying with some new features
– this being such a powerful application. Of course, being a part of KDE
community further would be a thing to cherish.!


***Ashwin Samudre***

KDE Identity username - ashwins
