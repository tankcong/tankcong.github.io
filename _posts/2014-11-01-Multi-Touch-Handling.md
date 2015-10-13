---
layout: post
title: Mastering the Android Touch System (2)
author: 聪记
header-img: img/post_header_universe.jpg
tags: 
    - Android
---

# Mastering the Android Touch System (2）

如果还多Android View Touch System有疑惑，那么一定要看这个[视频](https://www.youtube.com/watch?v=EZAoJU-nUyI)，
示例工程在[这里](https://github.com/devunwired/custom-touch-examples)

## Multi-Touch Handling

* `MotionEvent.getPointerCount()`
    * How many pointers are currently on the screen?
* Use the ACTION_POINTER_DOWN and ACTION_POINTER_UPevents to detect secondary pointers
    * `MotionEvent.getActionMasked()`
    * `MotionEvent.getActionIndex()`
* Use MotionEvent methods that take a pointer index parameter to get data for a specific
 pointer
    * Methods with no parameter always return data for the FIRST pointer

## Batching
* For efficiency, ACTION_MOVE events can be batched together in a single MotionEvent
* Latest (current) event is always returned by standard methods
    * `getX()`, `getY()`, `getEventTime()`
* Event occurring between this ACTION_MOVE and the last are found with historical methods
    * `getHistoricalX()`, `getHistoricalY()`, `getHistoricalEventTime()`
    * `getHistoricalSize() returns number of batched events
* Can reconstruct all events as they occurred in time for maximum precision

## Hover Events

> 鼠标、手势划过效果  
> Generic motion events describe joystick movements, mouse hovers, track pad touches,
> scroll wheel movements and other input events. The source of the motion event specifies the class of input that was received.

* Actions indicating a pointer is inside the bounds of the view, but isn't currently down
    * ACTION_HOVER_ENTER
    * ACTION_HOVER_EXIT
    * ACTION_HOVER_MOVE
* API Level 14
* Same behavior, different callbacks
    * `View.onGenericMotionEvent()`
    * `View.onGenericMotionEventListener`
* No default visual feedback in framework

## System Touch Handlers
* Don't jump right to custom touch handling if you don't have to...
* `OnClickListener`
* `OnLongClickListener`
* `OnTouchListener`
    * Monitor individual MotionEvents without a subclass
    * Can consume touches from a listener
    * Can pre-empt view's handling
* `OnScrollListener`/`View.onScrollChanged()`
    * View with existing scroll functionality has scrolled

---
* For more complex touch interaction
* GestureDector
    * `onDown()`, `onSingleTapUp()`, `onDoubleTap()`
    * `onLongPress()`
    * `onScroll()` (user dragging finger)
    * `onFling()` (user released drag with velocity)
* ScaleGestureDetector
    * `onScaleBegin()`, `onScale()`, `onScaleEnd()`
    > Detectors接收MotionEvents参数，通过各种计算得到当前的动作（Fling, Scrolling etc.)
* Handled via `OnTouchListener` or `onTouchEvent()`
* Disadvantages
    * Comsume UP events and exposes no interface for CANCEL events
    * May require added touch handling if these cases need special handling(e.g. 
    resetting a View's appearance)

## Touch Delegate
* Specialized object to assist in forwarding touches from a parent view to its child
* Allows for the touch area of a specific view to be different than its actual bounds
* Called in `onTouchEvent()` of attached View
    * Events have to make it that far without being consumed by a child or listener
* `TouchDelegate` is designed to be set on the PARENT and passed the CHILD view that
touches should be forwarded to, i.e.
```
ViewGroup parent;
View child;
Rect touchArea;
parent.setTouchDelegate(new TouchDelegate(touchArea, child));
```
> 注：比方说有一个Parent View中太小的View icon，这里需要扩大该icon的点击范围。那么就可以在
> Parent View中定义这样的区域，将事件交给Child View处理。