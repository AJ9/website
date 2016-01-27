---
layout: page
title: Animations
permalink: /animations/
---

The animation system in Flutter is based on typed
[`Animation`](http://docs.flutter.io/flutter/animation/Animation-class.html)
objects. Widgets can either incorporate these animations in their build
functions directly by reading their current value and listening to their
state changes or they can use the animations as the basis of more elaborate
animations that they pass along to other widgets.

## Animation

The primary building block of the animation system is the
[`Animation`](http://docs.flutter.io/flutter/animation/Animation-class.html)
class.  An animation represents a value of a specific type that can change
over the lifetime of the animation. Most widgets that perform an animation
receive an `Animation` object as a parameter, from which they read the current
value of the animation and to which they listen for changes to that value.

### `addListener`

Whenever the animation's value changes, the animation notifies all the
listeners added with
[`addListener`](http://docs.flutter.io/flutter/animation/Animation/addListener.html).
Typically, a [`State`](http://docs.flutter.io/flutter/widgets/State-class.html)
object that listens to an animation will call
[`setState`](http://docs.flutter.io/flutter/widgets/State/setState.html) on
itself in its listener callback to notify the widget system that it needs to
rebuild with the new value of the animation.

This pattern is so common that there are two widgets that help widgets rebuild
when animations change value:
[`AnimatedComponent`](http://docs.flutter.io/flutter/widgets/AnimatedComponent-class.html)
and
[`AnimatedBuilder`](http://docs.flutter.io/flutter/widgets/AnimatedBuilder-class.html).
The first, `AnimatedComponent`, is most useful for stateless animated widgets.
To use `AnimatedComponent`, simply subclass it and implement the
[`build`](http://docs.flutter.io/flutter/widgets/AnimatedComponent/build.html)
function. The second, `AnimatedBuilder`, is useful for more complex components
that wish to include an animation as part of a larger build function. To use
`AnimatedBuilder`, simply construct the widget and pass it a `builder` function.

### `addStatusListener`

Animations also provide a
[`AnimationStatus`](http://docs.flutter.io/flutter/animation/AnimationStatus-class.html),
which indicates how the animation will evolve over time. Whenever the animation's
status changes, the animation notifies all the listeners added with
[`addStatusListener`](http://docs.flutter.io/flutter/animation/Animation/addStatusListener.html).
Typically, animations start out in the `dismissed` status, which means they're
at the beginning of their range. For example, animations that progress from 0.0
to 1.0 will be `dismissed` when their value is 0.0. An animation might then run
`forward` (e.g., from 0.0 to 1.0) or perhaps in `reverse` (e.g., from 1.0 to
0.0). Eventually, if the animation reaches the end of its range (e.g., 1.0), the
animation reaches the `completed` status.

## AnimationController

To create an animation, first create a
[`AnimationController`](http://docs.flutter.io/flutter/animation/AnimationController-class.html).
As well as being an animation itself, an `AnimationController` lets you control
the animation. For example, you can tell the controller to play the animation
[`forward`](http://docs.flutter.io/flutter/animation/AnimationController/forward.html)
or [`stop`](http://docs.flutter.io/flutter/animation/AnimationController/stop.html)
the animation and later
[`resume`](http://docs.flutter.io/flutter/animation/AnimationController/resume.html)
it. You can also [`fling`](http://docs.flutter.io/flutter/animation/AnimationController/fling.html)
animations, which uses a physical simulation, such as a spring, to drive the
animation.

Once you've created an animation controller, you can start building other
animations based on it. For example, you can create a
[`ReverseAnimation`](http://docs.flutter.io/flutter/animation/ReverseAnimation-class.html)
that mirrors the original animation but runs in the opposite direction (e.g.,
from 1.0 to 0.0). Similarly, you can create a
[`CurvedAnimation`](http://docs.flutter.io/flutter/animation/CurvedAnimation-class.html)
whose value is adjusted by a [curve](http://docs.flutter.io/flutter/animation/Curves-class.html).

## Tweens

To animate beyond the 0.0 to 1.0 interval, you can use a
[`Tween<T>`](http://docs.flutter.io/flutter/animation/Tween-class.html), which
interpolates between its
[`begin`](http://docs.flutter.io/flutter/animation/Tween/begin.html)
and [`end`](http://docs.flutter.io/flutter/animation/Tween/end.html)
values. Many types have specific `Tween` subclasses that provide type-specific
interpolation. For example,
[`ColorTween`](http://docs.flutter.io/flutter/animation/ColorTween-class.html)
interpolates between colors and
[`RectTween`](http://docs.flutter.io/flutter/animation/RectTween-class.html)
interpolates between rects. You can define your own interpolations by creating
your own subclass of `Tween` and overriding its
[`lerp`](http://docs.flutter.io/flutter/animation/Tween/lerp.html)
function.

By itself, a tween just defines how to interpolate between two values. To get
a concrete value for the current frame of an animation, you also need an
animation to determine the current state. There are two ways to combine a tween
with an animation to get a concrete value:

1. You can [`evaluate`](http://docs.flutter.io/flutter/animation/Tween/evaluate.html)
   the tween at the current value of an animation. This approach is most useful
   for components that are already listening to the animation and hence
   rebuilding whenever the animation changes value.

2. You can [`animate`](http://docs.flutter.io/flutter/animation/Animatable/animate.html)
   the tween based on the animation. Rather than returning a single value, the
   animate function returns a new `Animation` that incorporates the tween. This
   approach is most useful when you want to give the newly created animation to
   another component, which can then read the current value that incorporates
   the tween as well as listen for changes to the value.