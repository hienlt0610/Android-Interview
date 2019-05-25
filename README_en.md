# Android-Interview
> Note: Because the actual development and reference answers will be different, and then afraid to mislead everyone, so the answer to these interview questions is to understand! The interviewer will ask questions about the knowledge points mentioned in the resume, so don't answer the questions and understand more.
## Android articles

### Activity

#### 1, said the Activity life cycle?
> + Reference Answer: Under normal circumstances, the common life cycle of Activity is only the following 7
> + **onCreate()**: Indicates that Activity** is being created**, commonly used to initialize work**, such as calling setContentView to load interface layout resources, initializing the required data for Activity, etc.
> + **onRestart()**: Indicates that Activity** is restarting**. In general, OnRestart will be called when the current Acitivty is changed from invisible to visible.
> + **onStart()**: Indicates that Activity** is being started**, Activity** is visible but not in the foreground**, still in the background, unable to interact with the user;
> + **onResume()**: Indicates that Activity** gets focus**, then Activity** is visible and in the foreground** and starts to be active, which is the difference from onStart;
> + **onPause()**: Indicates that Activity** is stopping**, you can do some work such as storing data, stopping animation**, etc., but it can't be too time consuming, because it will affect the new Activity. Display, onPause must be executed first, the new Activity's onResume will be executed;
> + **onStop()**: Indicates that Activity** is about to stop**, you can do some slightly heavyweight recovery work, such as logging out of the broadcast receiver, closing the network connection, etc., also not too time consuming;
> + **onDestroy()**: Indicates that Activity** is about to be destroyed**, this is the last callback in the Activity lifecycle, often doing **recycling work, resource release**;
> + Extension: From the ** life cycle **, onCreate and onDestroy are paired, respectively identifying the creation and destruction of the Activity, and only one call **;
From the fact that Activity** is visible, onStart and onStop are paired, and these two methods may be called multiple times**;
From the fact that Activity** is in the foreground, onResume and onPause are paired, these two methods may be called multiple times**;
In addition to this distinction, there are no other significant differences in actual use;

#### 2, Activity A will start another Activity B will call which methods? If B is a transparent theme or is it a DialogActivity?
> + Reference: Activity A starts another Activity B, the callback is as follows
> + Activity A's onPause() → Activity B's onCreate() → onStart() → onResume() → Activity A's onStop();
> + If B is a transparent theme or a DialogActivity, then A's onStop will not be called back;

#### 3, say the role of the onSaveInstanceState () method? When will it be called?
> + Reference Answer: Occurrence condition: Under abnormal circumstances (** When the system configuration changes, the Activity is killed and re-created, the resource memory is insufficient, causing the low-priority Activity to be killed**)
> + The system will call onSaveInstanceState to save the state of the current Activity. This method is called before onStop and has no established timing relationship with onPause.
> + When the Activity is rebuilt, the system will call onRestoreInstanceState, and the Bundle object ** saved by the onSave (abbreviation) method will be passed to ** to onRestore (abbreviation) and onCreate(), so it can be judged by these two methods. Whether Activity** is rebuilt**, called after onStart;
[[Reconstruction of Activity in an Abnormal Situation] (https://user-gold-cdn.xitu.io/2019/3/8/1695c1aaea26f833?w=583&h=381&f=png&s=10238)

#### 4, say the four startup modes and application scenarios of Activity?
> + Reference answer:
> + **standard standard mode**: Each time an Activity is started, a new instance will be re-created. Regardless of whether the instance already exists, the Activity of this mode will enter the task stack to which the Activity that started it belongs by default.
> + **singleTop stack top reuse mode**: If the new Activity is already at the top of the stack of the task stack, then the Activity will not be recreated, and the **onNewIntent** method will be called back if the new Activity instance already exists but Not at the top of the stack, the Activity will still be recreated;
> + **singleTask stack reuse mode**: As long as the Activity exists in a task stack, then launching this Activity multiple times will not re-create the instance, and callback **onNewIntent** method, this mode starts Activity A, The system first looks for the existence of the desired task stack, if it does not exist, it will re-create a task stack, and then put the instance of the created A on the stack;
> + **singleInstance single instance mode**: This is an enhanced singleTask mode. Activities with this mode can only be located in a single task stack, and there is only one instance in this task stack;

#### 5, understand which Activity flag flag Flags?
> + **FLAG_ACTIVITY_NEW_TASK :** Corresponds to the singleTask startup mode, the effect is the same as specifying the startup mode in XML;
> + **FLAG_ACTIVITY_SINGLE_TOP :** Corresponds to the singleTop startup mode, the effect is the same as specifying the startup mode in XML;
> + **FLAG_ACTIVITY_CLEAR_TOP :** Activity with this flag bit, when it starts, all the activities above it in the same task stack will be popped. This flag bit will generally appear with the singleTask pattern. In this case, if the instance of the started Activity already exists, the system will call back onNewIntent. If the activated activity is started in standard mode, then it and the Activity above it will be popped, the system will create a new Activity instance and put it into the stack;
> + **FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS :** Activities with this tag will not appear in the history Activity list;

#### 6. What is the relationship between Activity and window, view?
> + Reference answer:
> + Activity will be called when created **attach () ** method initializes a **PhoneWindow (inherited from Window) **, ** each Activity contains a unique PhoneWindow**
> + Activity **setContentView** is actually called **getWindow().setContentView** to set the View to PhoneWindow, and PhoneWindow is internally **addView**, **removeView by **WindowManager** **, **updateViewLayout** These three methods to manage the View, **WindowManager is essentially an interface, and finally implemented by WindowManagerImpl**
> + extension
> + **WindowManager** creates a **Surface** object for each **Window**, and the application can then draw whatever it wants to draw with this **Surface**. For **WindowManager**, this is just a rectangular area.
> + **Surface** is actually an object holding a matrix of pixels, which is part of the image that is displayed on the screen. We see that each **Window** displayed (including dialogs, full-screen **Activity**, status bar, etc.) has its own **Surface**. The final display may have occlusion problems between **Window**, which is to render the final display through the **SurfaceFlinger** object, so that they are displayed with the correct **Z-order**. In general, **Surface** has one or more caches (generally 2), which are refreshed by double buffering, so that you can add a new cache while drawing.
> + **View** is the **UI** element for interaction in **Window**. **Window** only **attach** a **View Tree**, when **Window** needs to be redrawn (eg, when **View** calls **invalidate**) Eventually converted to **Window** **Surface**, **Surface** is locked (**locked**) and returns **Canvas** object, at this time **View** get ** Canvas** object to draw yourself. When all **View** is drawn, **Surface** is unlocked (**unlock**), and **post** is drawn to the drawing cache for drawing, and **Surface Flinger** is used to organize each ** Window**, showing the final entire screen
> + Recommended articles:
> + [Activity, View, Window understanding of an article is enough] (https://blog.csdn.net/zane402075316/article/details/69822438)

#### 7. What is the change in the life cycle of the horizontal and vertical screens?
> + Reference answer:
> + **Do not set **Activity android:configChanges, the cut screen will destroy the current Activity, and then reload the call each life cycle, will be executed once when cutting the horizontal screen, will be executed twice when cutting the vertical screen;
onPause() →onStop()→onDestory()→onCreate()→onStart()→onResume()
> + Set the android:configChanges="**orientation**" of the Activity, after the model test
> + ** Under Android 5.1, API 23 level, the screen will still recall each life cycle, only once when cutting horizontally and vertically.
> + ** Under Android9 ie API 28 level, the cut screen will not recall each life cycle, only the onConfigurationChanged method will be executed.
> + **[After official check] (https://developer.android.com/guide/topics/manifest/activity-element)**, the original words are as follows
> + If your app targets **Android 3.2 ie API level 13** or higher (as declared by the minSdkVersion and targetSdkVersion properties), then the "screenSize" configuration should also be declared because when the device is in landscape and portrait orientation This configuration also changes when switching between. This configuration change will not restart the activity even if it is running on Android 3.2 or higher.
> + When setting the android:configChanges="**orientation|keyboardHidden|screenSize**" of the Activity, the model test passes, the cut screen will not recall each life cycle, only the onConfigurationChanged method will be executed;
> + Recommended articles:
> + [Android horizontal and vertical screen switch to load different layouts] (https://blog.csdn.net/u010365819/article/details/76618443)

#### 8, how to start the activity of other applications?
