Mastering the Android Touch System
===================================

---------------
How Android Handles Touches
---------------
* Events start at the Activity with ```dispatchTouchEvent()```
* Events flow top down through views
  -Parents(ViewGroups) dispatch events to their children
  -Can intercept events at any time
* Events flow down the chain (and back up) until consumed
 -Views must declare interest by consuming ACTION_DOWN
 -Further events not delivered for efficiency
* Any unconsumed events end at the Activity with ```onTouchEvent()```
* Optional External ```OnTouchListener···can intercept touches on any View/ViewGroup

注：
 根据以上，Activity的```OnTouchListener```是最后一处接收到TOUCH EVENT的地方

* Activity.dispatchTouchEvent()
 -Always first to be called
 -Sends event to root view attached to Window
 -```OnTouchEvent()```
  * Called if no views consume the event
  * Always last to be called

* View.dispatchTouchEvent()
 -Sends event to listener first, if exists
  * ```View.OnTouchListener.OnTouch()```
 -If not consumed, processes the touch itself
  * ```View.onTouchEvent()```

* ViewGroup.dispatchTouchEvent()
 -Check ```onInterceptTouchEvent()```
  * Check if it should supersede children
 -For each child view, in reverse order they were added
  * If touch is relevant (inside view), ```child.dispatchTouchEvent()```
  * If not handled by previous, dispatch to next view
 -Handle touch directly (same as View)

 注：
  ```onInterceptTouchEvent()```会始终监听到EVENT事件，无论该事件被分发到任何child, 比方说
  ScrollView内的Button, Button只需要消耗click事件(ACTION_DOWN-->ACTION_UP), 一旦事件
  变为滑动(ACTION_MOVE), ScrollView则接管事件处理滑动, Button则接收到(ACTION_CANCEL)

* Touch Interception(```onInterceptTouchEvent()``` returns true)
 -Passes ACTION_CANCEL to active child
 -All future events handled directly by ViewGroup

* Child view can call ```requestDisallowTouchIntercept()``` to block ```onInterceptTouchEvent()```
 for the duration of the current gesture.
 -Flag is reset by framework on each new gesture(ACTION_DOWN)

---------------
Custom Touch Handling
---------------
* Handling touch events
 -Subclass to override ```onTouchEvent()```
 -Provide an ```OnTouchListener```

* Consuming events
 -Return true with ACTION_DOWN to show interest
  * Even if you aren't interested in ACTION_DOWN, return true
 -For other events, returning true simply stops further processing

* Useful *constants* available in ViewConfiguration
 -```getScaledTouchSlop()```
  * Distance move events might vary before they should be considered a drag
  注：判断滑动的距离
 -```getScaledMinimumFlingVelocity()```
  * Speed at which the system considers a drag to be a fling
  注：判断滑动后开始Fling的速度
 -```getScaledPagingTouchSlop()```
  * Touch slop used for a horizontal paging gesture (i.e. ViewPager)
 -Display values scaled for each device's density

* Forwarding touch events
-Call target's ```dispatchTouchEvent()```
-Avoid calling target's ```onTouchEvent()``` directly

* Stealing touch events （ViewGroup)
 -Subclass to override ```onInterceptTouchEvent()```
 -Return true when you want to take over
  * ALl subsequent events for the current gesture will come to your ```onTouchEvent```
   directly
  * ```onInterceptTouchEvent()``` will no longer be called for each event (one-shot redirect_)
 -Any current target will receive ACTION_CANCEL

---------------
Custom Touch Handling Warning
---------------
 * Call through to super whenever possible
  -```View.onTouchEvent() does a LOT of state management(pressed, chcked, etc.) that
  you will lose if you capture every touch
 * Protect ACTION_MOVE with slop checks
  -Fingers are fat and twitchy
 * Always Handle ACTION_CANCEL
  -Container views with action (like scrolling) will steal events and you will
   likely need to reset state
  -Remember after CANCEL, you will get nothing else
 * Don't intercept events until you're ready to take them all.
  -Intercept cannot be reversed until the next gesture.

