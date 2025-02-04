---
title: "VR Gesture Combo"
permalink: /portfolio/vr-gesture-combo/
date: 2024-03-01
excerpt: "This is a tech demo in VR environment where objects can be cut as the user does."
header:
  image: /assets/images/vrGestureCombo_main_wide.png
  teaser: /assets/images/vrGestureCombo_finger_gun.gif
company: "Academic Project"
engine: "Unity"
platform: "Windows PC, Meta Quest 3"
skills: "C#, XR Hands"
---

| |
| **Company**<br>&nbsp;&nbsp;&nbsp;{{page.company}}								|||**Engine**<br>&nbsp;&nbsp;&nbsp;{{page.engine}}
| **Platform**<br>&nbsp;&nbsp;&nbsp;{{page.platform}}							|||**Skills**<br>&nbsp;&nbsp;&nbsp;{{page.skills}}	

## Experiment

In VR technology, more apps are utilizing hand tracking as an input method instead of controllers. With the release of Unity's API for setting custom gestures (XR Hands 1.4.0-pre.1), I conducted a simple test to see how it could be applied in a game.

To enhance gesture-based interactions, I implemented a customized combo system using the XR Hands API. In the video, the floating text boxes visualize the recognized combo sequences. When multiple gestures are detected, their corresponding texts appear in the same row, effectively displaying the executed combo in real time.

### Rapid Gestures: Finger Gun
<figure>
  <img src="/assets/images/vrGestureCombo_finger_gun.gif">
</figure>

This test involves a repeated combination of two gestures. As shown in the video, there are instances where the gesture input is ignored. This is due to a slight delay in gesture recognition when using the API. Because of this issue, rapidly repeated gestures may not be suitable for gameplay applications.

### Gestures in Sequence: Fireball
<figure>
  <img src="/assets/images/vrGestureCombo_fireball.gif">
</figure>

<figure>
  <img src="/assets/images/vrGestureCombo_fireball_cancel.gif">
  <figcaption>Cancel</figcaption>
</figure>

In this test, I focused on recognizing gestures more slowly and accurately. I configured the system to trigger a fireball when three specific gestures are performed in sequence. The results turned out quite well.

### Complex-intersected Gestures: Hand Signs from Naruto
<figure>
  <img src="/assets/images/vrGestureCombo_naruto.gif">
</figure>

In the Naruto anime, characters perform intricate hand signs to cast magic-like techniques. These hand signs require both hands to move in sync, frequently intertwining in a rapid sequence of 3 to 6 steps.

However, when testing this with the XR Hands API and Meta Quest 3, I encountered a major limitation—the system struggled to recognize each hand when they intersected. Due to this issue, it was completely unable to track or register the gestures correctly.

## References
 - [Unity XR Hands Package](https://docs.unity3d.com/Packages/com.unity.xr.hands@1.4)
 - [Unity XR Hands Setup by Dilmer Valecillos](https://blog.learnxr.io/xr-development/unity-xr-hands-package)