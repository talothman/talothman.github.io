---
layout: post
title:  "Building Efficient Manipulation Interactions for 3DOF Virtual Reality Controllers Pt.2"
author: Talal Alothman
date:   2020-04-05 12:00:00 -0400
description: Developing 3DOF manipulation is hard. This post proposes an efficient, user friendly implementation as a solution.
image: ../assets/gif/scale.gif
tags:
  - virtual reality
  - VR
  - interaction design
  - 3DOF controller
  - Oculus Go
  - 3D manipulation
  - scaling
  - UX design
---

[In part one]({{ site.baseurl }}{% post_url 2020-03-09-Building-Efficient-Manipulation-Interactions-for-3DOF-Virtual-Reality-Controllers-Pt-1 %}), I discussed some of the challenges and solutions for building selection, translation, and rotation interactions for 3DOF controllers. In this post, I will further elaborate on the scale interaction in addition to methods of transitioning between the different interaction modes.

## Scale

More formally defined as the task of changing the size of an object(1), this task is fundamental to the proper manipulation of 3D models. Building on the touch-pad mapping techniques from the previous two implementations made sense. At the same time we needed to implement a technique that users could be familiar with. Mapping x-axis or y-axis changes on the touch-pad wasn't a good affordance. Why would swiping up-down or left-right cause a change in volume. Pinching was another consideration but bimanual gestures on a trackpad only accessible to the holding hand's thumb made for awkward interaction. As this task doesn't have analogies in the real world, made it harder. Unlike translation and rotation, scaling isn't part of our daily lives. We approached this problem by attempting to find metaphors in relation to what effect scaling has on 3D models. Expansion, compression, spread, suppress were all words that defined what happens when you scale.

It occurred to us that volume, as in the volume of a rigid body, was what was changing. Though the semantics are different, this relates to acoustic volume in which conceptions of more and less are shared. Our thought process was that we could be build analogies between the two concepts. We began to look at existing interfaces that handle volume controls. Some devices have dedicated hardware buttons. Others have software based sliders. There wasn't much variation unless you included [these](https://uxdesign.cc/the-worst-volume-control-ui-in-the-world-60713dc86950) horribly designed ones. Another implementation that came to mind was the beloved click wheel on those Ipods. It controlled volume and also handled UI navigation. I remembered how responsive and natural it was to use it and thought we could give it a shot.

From a hardware standpoint, the touchpad on the Go controller had similar ergonomics. It was a bit smaller but still allowed for comfortable reach for a user's thumb. The software implementation wasn't difficult either. From the touchpad we get a 2D vector of the location of a touching thumb. This represents the displacement on the x and y axes from the center of the touchpad. We then use the angular displacement from frame to frame and map that to some displacement in the scale of the selected object. We also defined deadzones around the center and circumference to reduce mistouches. As the user scrolls in a circular manner around the touchpad, the size of the object grows or shrinks. We followed a clock metaphor where moving clock-wise increases the scale of the object and moving counter clock-wise decreases it. This worked well. The sense of growing an object or shrinking it mapped well with the metaphor of increasing volume on a Ipod.

![](..\assets\gif\scale.gif#half alt="Scaling Gif")
*Scaling*

![](..\assets\gif\scale_mapping.gif#quarter alt="Scaling Mapping Gif")
*Scale Mapping*

## Manipulation Mode Cycling

All the manipulation interactions discussed cannot exist as possible actions by the user at once. Recall that we have only one controller with one touchpad. Attempting to predict what the user is attempting to achieve using data collected from initial touches is hard, especially when there's overlap with the manipulation mappings. Recall that the fishing reel technique requires users to scroll along the y-axis of the controller to bring the selected model closer or further back. That same mapping, y-axis scrolling should also allow the user to rotate the model around it's x-axis as mapped in the rotation interaction. How does one allow for these capabilities to exist without overwhelming the user and allow the user to accurately act on their intentions? Constrain them.

Instead of having all these functions accessible immediately and at any point, force the user to choose between different manipulation modes. This limits their degree of freedom when using the touchpad but allows them to clearly identify which manipulation they aim to use. To achieve this we map the touchpad's button to cycle between the different manipulation modes. Feedback is given to the user by identifying which mode they're in using representative icons. These icons function as our visual signifiers informing the user which manipulation state they're in.

![](..\assets\gif\mode_select_icon.gif#half alt="Mode Cycling Through Icon Gif")
*Cycling Through Manipulation Modes*

![](..\assets\gif\mode_change.gif#quarter alt="Mode Cycling Gif")
*Manipulation Mode Cycling Mapping*

## Conclusion

The manipulation implementations presented present one approach to facilitating user interaction in 3DOF headsets that use 3DOF controllers. While the limitations of 3DOF systems can be avoided by using newer standalone headsets that support 6DOF headsets and controllers, I believe the need to understand what limits 3DOF systems have, and ways to overcome them still remain relevant topics in the 3DUI space.

Throughout the development process, constraints on user interaction remained the most useful tool in clearly building manipulation techniques that were comprehensible. Taking into consideration the number of tasks that users needed to be able to do, allowing for the ability to do them all at once brought users to a state of confusion. Constraining degrees of freedom on manipulation tasks allowed user to compartmentalize what could and could not be done in their current state. It provided them with a way to confidently and accurately apply manipulation changes and observe changes to their clearly selected objects. This was clearly a good example of "less is more".

One thing to note is that the main interaction metaphor used in our implementation, ray-cast pointer, is clearly an extension of the pointer in WIMP interfaces. While the hope is that with more immersive environments, users will be introduced with more "natural" forms of interaction, the current state of immersive VR applications tends to build on user experience and the affordances from 2D WIMP interfaces. It's yet to be seen if hand or speech interfaces will replace pointer based ones in immersive environments but building on the long legacy of 2D based WIMP interfaces seems like a logical buffer step. I believe our implementation does a good job at that while addressing the limitations of 3DOF controllers.

## References

1: Joseph J. LaViola, Ernst Kruijff, Ryan P. McMahan, Doug A. Bowman, Ivan Poupyrev. 2017. 3D User Interfaces: Theory and Practice 2nd Edition. Addison Wesley Longman Publishing Co., Inc., USA.