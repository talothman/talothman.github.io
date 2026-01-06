---
layout: post
title:  "Exploring Hand Tracked Interaction for Holographic Displays"
author: Talal Alothman
date:   2020-10-18 12:00:00 -0400
description: Holographic Displays like the Looking Glass can use hand tracking for interaction but it's not that straight forward.
image: ../assets/videos/hand_tracked_holographic_display/snapshot.png
tags:
  - holographic display
  - Looking Glass
  - hand tracking
  - Leap Motion
  - XR
  - interaction design
  - spatial computing
  - 3D visualization
---

## TL;DR

- Group viewing of 3D content can solve the issue of user seclusion common in XR headsets
- Holographic displays (Looking Glass) have the benefit of adding immersive features (ex. depth queues) minus the seclusion and headset weight
- Egocentric approaches (common in AR/VR) for designing hand-tracked interaction do not transfer easily to the Looking Glass display
- Holographic display interaction lays somewhere on the spectrum of near-field to far-field interactions
- Selection-less based interaction is easier to use but limits user to interaction with single object
- Virtual sphere approaches provide for a reliable method of interaction that users can pick up quickly
- Sometimes simple interactions can introduce occlusion driving developers to implement "higher-calorie" interactions

## Headsets are Seclusive

A core challenge for most immersive environments is overcoming what little can be shared with the outside. Users wearing a headset cover their eyeballs with displays designed to block out external light while also requiring the user to position their eyes in the correct position for optimal clarity. Mirroring what the user sees attempts to reduce the secluded effect of this experience by display some version of what is being rendered on the near eyeball displays. With this (typically cropped) mirroring comes a reduction in immersive cues mainly caused by factors like the lack of stereo rendering and reduced field of view in addition to giving up controller on the observers angle of view. These factors contribute to the seclusion of immersive experiences in spaces shared with others.

<video autoplay loop muted playsinline size="#quarter">
    <source src="..\assets\videos\hand_tracked_holographic_display\immersion_comp.mp4" type="video/mp4">
</video>
*It's sometimes hard to share what's happening in a headset*

While networked immersive experiences have lessened the ability to share and interact with others in immersive environments, they don't scale well for large gatherings of users. What would need to happen is each user would require their own personal headset and other infrastructure (if headsets aren't standalone) to share the experiences being presented to them. Additionally, developing software that brings user's into the same space has gotten much easier in the past 2 years but remains a large investment of time. This presents us with a bottle neck. Either remain in seclusion or find a way to gather enough money to buy each user you intend on bringing into the experience. In the case of university classrooms, this challenge is abundantly clear.

A possible solution to building shared immersive experiences has also emerged in the form of holographic displays. One such display is the [Looking Glass (LGlass)](https://lookingglassfactory.com/). The goal is to be able to present depth queues similar to that of stereoscopic rendering on a display that is viewable from multiple angles and to groups of observers at the same time. This presents us with a possible way to display 3D data in an shared immersive manner without the need for headsets. The biggest challenge here is developing interaction techniques that allow users to interact with what's being viewed in an accurate, unobtrusive manner.

![](..\assets\img\hand_tracked_holographic_display\looking_glass.jpg#half alt="Scaling Gif")
*Looking Glass 8.9" (Looking Glass Factory)*

## Interacting with a Hologram

Our objective is to provide the ability to **view**, **rotate**, and **zoom** into a 3D model being displayed on the LGlass. Users should be able to view and manipulate the model in a **non-obtrusive** manner with the 3D model being displayed on a 8.9" inch display and viewable to a group of observers.

Some approaches to consider:

- A typical **mouse/pointer interface** could possibly suffice. Users are used to using a mouse to manipulate some 3D data. For this to work the user driving the manipulation would need to take up space in front of and to the side of the display to be able to drive the interaction with a mouse. This is awkward and is a source of occlusion for the observers around them. The driver could be set to the side but then they'd be displaced from the actual display.

<video autoplay loop muted playsinline size="#half">
    <source src="..\assets\videos\hand_tracked_holographic_display\mouse_manipulation.mp4" type="video/mp4">
</video>
*Example of a Mouse/Pointer Interface in Blender*

- Some versions of the Looking Glass support **touch interaction**. The display is a touch screen and provides data on finger touches similar to mobile phones. This could also work but occlusion issues are limiting. Imagine dragging on a display while others attempt to observe the 3D data under your hand. Not niace.

<video autoplay loop muted playsinline size="#quarter">
    <source src="..\assets\videos\hand_tracked_holographic_display\touch_manipulation.mp4" type="video/mp4">
</video>
*Example of mobile touch-based interface for 3D model viewing*

- **Hand-tracking** is another avenue that can be pursued. The [Leap Motion](https://www.ultraleap.com/product/leap-motion-controller/) provides real-time hand tracking with a capable tracking volume. This would provide some flexibility with user positioning and could provide a solution for our occlusion troubles. The challenge is that user's aren't used to interacting with digital content without a mediator (mouse/game controller). There exists numerous schemes to enable manipulation of 3D models using hand tracking. Some are designed to work well within headsets emphasizing [egocentric (think first-person)](http://www.ivanpoupyrev.com/wp-content/uploads/2016/10/EG98.pdf) interaction concepts. Others work well for manipulating content on static exocentric (think in reference to object or world) 2D displays.(1)

<video autoplay loop muted playsinline size="#half">
    <source src="..\assets\videos\hand_tracked_holographic_display\hand_tracking.mp4" type="video/mp4">
</video>
*Leap Motion Hand Tracking Visualization*

## Exploring Interaction Techniques

While the LGlass presents data in a holographic manner, facilitating some freedom with a viewers head position, egocentric interaction techniques are difficult to apply to a mostly static display. In addition, the LGlass renders content sharply only at a specific distance from the camera (zero-parallax plane). This adds more reason to exploring approaches that aren't purely egocentric as content that isn't always at the correct depth is blurry. Concepts like hand-menus and direct manipulation with a user's hand are challenging. We expect them to present visual distortions that contribute to occlusion and user confusion. We might be able to draw on interaction techniques that aren't necessarily egocentric.

There exists multiple approaches to designing interaction techniques for a 2D display. Some involve raycasting from some source point into the virtual environment. Some use changes to a hand's transform to rotate a 3D object. Additionally, some interaction techniques are bimanual, others only need a single hand. All in all, the domain of possible interaction techniques is vast and there has been much research into this space. Here are some approaches we can consider.

### Interaction Approaches

#### Direct-Hand

Users are used to using their hands to grab and manipulate objects in the real world. This technique replicates that experience in a virtual environment. The idea is that when a user's hand overlaps with the 3D model, the user can preform a specific grab gesture that would then let them map the rotation of their hand to the rotation of the 3D model. This type of interaction is typically used in near-field interactions. While providing for a natural way to interact in a virtual space, constrains on real life wrist rotations remain the biggest hinderance to this technique.

<video autoplay loop muted playsinline size="#half">
    <source src="..\assets\videos\hand_tracked_holographic_display\direct_hand.mp4" type="video/mp4">
</video>
*Direct-Hand Translation*

#### Raycast

Commonly used for far-field interactions, this technique utilizes rays that fire out of the user's hand into the virtual environment and interact with the 3D model. Utilizing some gesture, a user can then map the transform of an object that the ray intersect with to changes in their own hand's transform. This again can suffer from issues similar to that of the direct-hand approach.

<video autoplay loop muted playsinline size="#half">
    <source src="..\assets\videos\hand_tracked_holographic_display\raycast_translation.mp4" type="video/mp4">
</video>
*Raycast Translation*

#### Widget-Based

In this technique, visual elements appear to indicate the user's ability to preform a manipulation task. This approach can be coupled with either direct hand or raycast techniques to allow different near-field and far-field manipulation. Manipulation is typically constrained to a single axis to facilitate accuracy. This approach sometimes suffers from slow, tedious manipulation actions.

<video autoplay loop muted playsinline size="#half">
    <source src="..\assets\videos\hand_tracked_holographic_display\widget_scale.mp4" type="video/mp4">
</video>
*Widget-Based (Direct-Hand) Scaling*

#### Selection-less Single Hand Gestures

Some type of gesture could be used to identify a manipulation task regardless of what object is selected. The action we're looking for does not depend where the user's hand, a common approach in egocentric interaction techniques. We avoid the need to overlap with the actual object. Instead, a specific gesture identifies the beginning of our interaction. Transform changes such as position or rotation are then tracked to identify the amount of manipulation that needs to happen.

<video autoplay loop muted playsinline size="#half">
    <source src="..\assets\videos\hand_tracked_holographic_display\selectionless_single.mp4" type="video/mp4">
</video>
*Selection-less Single Gesture Rotation*

#### Selection-less Bimanual Gesture

Examples of this technique typically utilize the user's two hands to interact with the 3D model. One hand showing a gesture is used to identify the start of the manipulation task and as a reference point. The other moves in some manner relative to the first hand to identify the amount of the change in the manipulation. The gestures commonly used for this approach are fist and pinch gestures.

<video autoplay loop muted playsinline size="#half">
    <source src="..\assets\videos\hand_tracked_holographic_display\selectionless_bimanual.mp4" type="video/mp4">
</video>
*Selection-less Bimanual Gesture Rotation*

### Evaluation

All the approaches above have pros and cons. To reiterate, our main goals are to accurately and comprehensibly facilitate the following:

- two manipulation tasks, rotation and zooming
- optimize for in-focus rendering of the 3D model (the LGlass renders things in-focus along a single plane, the zero-parallax plane)
- the main driver's digital and real-life position and gestures do not block the view of other observing users

I'll use the above objectives to evaluate each of the general approaches mentioned. Red is bad, orange meh, and green being ideal.

| | **rotation/zooming** | **in-focus rendering** | **driver placement** |
|:--------:|:-------:|:--------:|:---------:|
| **Direct-Hand**| *grabbing and rotating 3D model comes naturally/there isn't a natural way to facilitate zooming, scaling could be possible analog* | *zooming or in this case, scaling would cause more of model to extend beyond zero-parallax plane causing blurriness* | **need for direct overlap of 3D model and hand is source of occlusion**|
| **Raycasting** | **no secondary reference point making it hard to apply [instinctual-like interactions](https://docs.microsoft.com/en-us/windows/mixed-reality/design/interaction-fundamentals) for robust aim**| *zooming or in this case, scaling would cause more of model to extend beyond zero-parallax plane causing blurriness* | **driver needs to placed dead center in front of display for raycasting to working properly** |
| **Widget-based** | *growing list of established affordances within immersive manipulation domain/scaling for zooming in* | *similar issues to above approaches with scaling being cause for blurry rendering of 3D model* | **need for direct hand or ray-casted metaphors brings driver placement baggage** |
| **Selectionless Single Hand Gestures** | *flexible isomorphic and non-isomorphic implementations possible (1)/might suffer from gesture segmentation issues* | ~~capable of using this approach to implement zooming functions avoiding the need apply scaling to work around zero-parallax plane issues~~ | ~~usage of one hand to apply manipulation changes reduces occlusion issues both virtually and physically~~ |
| **Selectionless Bimanual Gestures** | ~~large set of examples using bimanual interactions to apply rotation and zooming~~ | ~~capable of using this approach to implement zooming functions avoiding the need apply scaling to work around zero-parallax plane issues~~| *use of both hands could be source of occlusion taking up real-estate in the virtual space and in the physical space* |

It's worth mentioning that some of the above approaches fit neatly into "best used" for near-field (close or within arms length of user's perspective) and far-field (far or outside of the user's immediate access) interactions. For example, direct hand manipulation is great for near-field interactions as it's usually quite similar to humans interacting with object near to them in the real world. On the other hand, far-field interactions do not have similar analogies in the real world and hence need users to learn a lot more about how to use them. Raycasting techniques have become the go to approach to enable far-field interactions with mainstream adoption in numerous immersive games, tools, hardware runtimes (ex. Hololens 2 Shell).

Because of the limitations of the LGlass's zero parallax plane, it would be easy to assume that hands need to be rendered close to the 3D model of interest and therefore, we should adopt interactions that fit within a near-field type. My hunch is that for near-field interactions to work well, it's heavily dependent on an egocentric perspective into the virtual world and that really isn't the case with the limitations of our display. Nonetheless, near-field interactions could be a cause for many occlusion issues when interacting with a 3D model on the LGlass display. Interactions suited for this type of display sit somewhere between near-field and far-field interactions with a slight lean towards near-field interactions.

![](..\assets\img\hand_tracked_holographic_display\nearfield_farfield.png#half alt="Scaling Gif")
*Where ideal interaction for holographic displays on a Near-field Far-field Spectrum*

From the comparison table above, we can see both single-hand and bimanual gesture approaches have the most promise. The approach described below is the result of evaluating multiple interaction techniques according to the functional requirements and technical specifications of the hardware we're working with.

## Our Solution

Pinching has become the go to gesture for hand tracked interfaces. This is partially due to the current generation's hardware and software ability to segment this gesture better than others. Another reason for this gesture being adopted as the main form of interaction for hand-tracked headsets is the naturally occurring haptic feedback from your fingers pinching each other. Visual feedback on this gesture occurring typically provides even more confirmation for the user that they've pinched. We'll stick with it.

<video autoplay loop muted playsinline size="#half">
    <source src="..\assets\videos\hand_tracked_holographic_display\pinch_outline.mp4" type="video/mp4">
</video>
*Pinch Gesture Recognition with Outline Feedback*

As mentioned above, we're planning on avoiding a direct-hand manipulation approach. Therefore, in this approach can begin a rotation manipulation anywhere in the space as long as the visualization of the user's hand is present. By going this route, we avoid needing to overlap with the actual model in the virtual environment and provide for quick snappy rotation manipulations. The interaction technique used is a variation of the "Virtual Sphere" (1) family of interactions. The idea is simple; map the hands position to a 2D plane and generate vectors based on the difference between the current and past frame hand positions. The rotation axis is then the axis perpendicular to the direction of motion of the hand.

<video autoplay loop muted playsinline size="#half">
    <source src="..\assets\videos\hand_tracked_holographic_display\pinch_rotate.mp4" type="video/mp4">
</video>
*Selection-less Pinch Gesture Applying Rotation*

Now that we have rotations down, let's look at zooming. Ideally, we can introduce as little new gestures as possible so sticking with pinching would make sense. One approach to consider was mapping the rotation interaction to one hand and zoom interaction to the other. This would require users to remember which hand handled which interaction. It was confusing. Additionally, zooming with one hand required some way to target which location you were looking to zoom into. This process was awkward as users then needed their hands to over in front of display blocking out important visual feedback queues.

This is the main reason we opt for bimanual interaction for the zooming interaction. We also use a pinch gesture to instigate the interaction but now look for the existence of a pinch gesture on both tracked hands. This introduces the need for some delay to ensure we segment out single hand pinches which map to rotation. Additionally, now that we have the need for two hands, we utilize a cursor that maps to the center of the two hands and attaches itself to the surface of a model that it collides with in the depth axis. This provides an opportunity for the user's center field of view to remain clear of occlusions as they perform the gesture. The gesture itself is initiated once both hands pinch and changes distance between each hand increases or decreases modifying the zoom level.

<video autoplay loop muted playsinline size="#half">
    <source src="..\assets\videos\hand_tracked_holographic_display\bimanual_zoom.mp4" type="video/mp4">
</video>
*Cursor-Based Bimanual Zoom*

## Observations

Initial rotation implementation was mapped to the user's hand position on the X/Z plane (in a left-hand coordinate system). We found that users did not comprehend this and instead attempted to apply the rotation across the X/Y plane. This is possibly an artifact of past interaction with 2D displays. This can also be a symptom of the display not providing enough depth queues.

We also experimented with two types of rotation movements. One gave off a more elastic connection between the user's hand moving. The other did not involve any type of delay. We found that users preferred the snappier version even though our initial internal tests pointed out that it was prone to a little jitter and felt more "synthetic".

While ideally, we could avoid bimanual interactions in the goal of designing "low-calorie" techniques that don't tire the user out, the need to incorporate bimanual interactions was driven by hardware and software specifications overlapping with our core evaluation criteria. This approach can get tiring after some time and we see this as a limitation.

While interaction in our case was focused on one single model at a time, ideally we could facilitate some selection method that would allow us to interact with multiple objects in the same space.

Overall, the benefit of modern holographic displays are clear; allow for immersive-like displaying of 3D content that is not limited to a single user at a time and does not require the need to wear, often neck pain inducing, headsets. There's great value in that. Interaction remains experimental with approaches like the one above attempting to adopt and mix different interactions techniques for the sake of better user experience. It should be clear from the above discussion that interaction with holographic displays does not fit neatly into the near-field/far-field binaries. It would seem that there is a middle ground or even a different paradigm that could lean into interactions common in more typical 2D displays. Regardless, the space is ripe with opportunities to experiment and that's pretty exciting.

If the post above is interesting to you, I'd love to talk. Hit me up on [twitter](https://twitter.com/talalhalothman)!

## References

1: Joseph J. LaViola, Ernst Kruijff, Ryan P. McMahan, Doug A. Bowman, Ivan Poupyrev. 2017. 3D User Interfaces: Theory and Practice 2nd Edition. Addison Wesley Longman Publishing Co., Inc., USA.
