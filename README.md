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
![Reconstruction of Activity in an Abnormal Situation](https://user-gold-cdn.xitu.io/2019/3/8/1695c1aaea26f833?w=583&h=381&f=png&s=10238)

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
>   + [An article on Activity, View, and Window is enough.](https://blog.csdn.net/zane402075316/article/details/69822438)

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
>   + [Android horizontal and vertical screen switch to load different layouts](https://blog.csdn.net/u010365819/article/details/76618443)

#### 8, how to start the activity of other applications?
> + Reference answer:
> + In the case of guaranteed access, the IntentFilter matching of the target Activity through the implicit Intent, the principle is:
> + An intent only matches the action, category, and data in the intent-filter of an Activity at the same time to complete the match.
> + An Activity can have multiple intent-filters, an intent can start the Activity as long as it successfully matches any set of intent-filters;
> + Recommended articles:
> + [action, category, data specific matching rules] (https://blog.csdn.net/sunzhaojie613/article/details/77433994)

#### 9, Activity startup process? (emphasis)
> + Reference answer:
> + **Click the App icon and call it to AMS remotely through startActivity. In AMS, the newly launched activity is pushed into the activity stack by the activityrecord structure, and the remote binder is called back to the original process, so that the original process enters the pause state. After the process pause, notify AMS that I have paused**
> + ** At this time, AMS then judges whether it needs to start a new process according to whether the flag in the intent of the activity in the stack contains the flag of new_task, and starts the new process through the function of startProcessXXX**
> + ** After the new process is started, the main function of ActivityThread is called by reflection. The main function calls looper.prepar and lopper.loop to start the message queue loop mechanism. Finally, I told AMS remotely that I started. AMS callback handleLauncherAcitivyt loads the activity. In the handlerLauncherActivity, the OnCreate of the Application and the onCreate of the Activity are called by reflection, and the onResume of the Activity is called by the reflection in the handleResumeActivity.
![](https://user-gold-cdn.xitu.io/2019/3/8/1695c1aaec5faf60?w=769&h=581&f=png&s=188283)
> + Recommended articles:
> + [Android four components start mechanism Activity startup process] (https://blog.csdn.net/qq_30379689/article/details/79611217)

### Fragment

#### 1. Let's talk about the life cycle of Fragment?
> + Reference answer:
> + Fragment from the creation to the destruction of the entire life cycle involved in the order: onAttach () → onCreate () → onCreateView () → onActivityCreated () → onStart () → onResume () → onPause () → onStop () → onDestroyView()→onDestroy()→onDetach(), which has a number of similar methods to the Activity, and different methods are:
> + **onAttach()**: Called when the Fragment is associated with the Activity;
> + **onCreateView()**: When the fragment creates a view call, after onCreate;
> + **onActivityCreated()**: Called when the activity associated with the Fragment completes onCreate();
> + **onDestroyView()**: Called when the layout in the Fragment is removed;
> + **onDetach()**: Called when the Fragment and Activity are disassociated;
> + Recommended articles:
> + [Android Fragment Advantage] (https://www.cnblogs.com/shaweng/p/3918985.html)

#### 2. Talk about the difference between Activity and Fragment?
> + Reference answer:
> + Similarities: can include layout, can have their own life cycle
> + Differences:
> + Fragment has 4 more callback cycles compared to Activity, which is more flexible in control operations;
> + Fragment can be written directly in the XML file, or dynamically added in the Activity;
> + Fragment can use the show()/hide() or replace() to switch the Fragment at any time, and the switching will not have obvious effects, the user experience will be good; although the Activity can also switch, but the Activity switch There will be obvious page turning or other effects, and it is not very good for the user to switch between a small part of the content;

#### 3, the difference between add and replace in Fragment (Fragment overlap)
> + Reference answer:
> + add does not reinitialize fragment, replace every time. So if you get the data in the fragment life cycle, use replace to repeat the acquisition;
> + When adding the same fragment, replace will not change, add will report IllegalStateException;
> + replace removes all fragments of the same id first, then adds the current fragment, and add overrides the previous fragment. So if you use add, it will usually be accompanied by hide() and show() to avoid layout overlap;
> + Use add, if the application is placed in the background, or otherwise destroyed by the system, then open, the fragment referenced in hide() will be destroyed, so there will still be layout overlap bugs, you can use replace or add, add a tag parameter;
![](https://user-gold-cdn.xitu.io/2019/3/14/1697b9862e1ce518?w=1800&h=878&f=png&s=215465)

#### 4, the difference between getFragmentManager, getSupportFragmentManager, getChildFragmentManager?
> + Reference answer:
> + getFragmentManager() gets the manager of the ** parent container of the fragment,
getChildFragmentManager() gets the manager of the subcontainer** in the fragment.
If it is a fragment nested fragment, then you need to use getChildFragmentManager();
> + Because Fragment is a component that appears in the 3.0 Android system API version, so the system above 3.0 can directly call getFragmentManager() to get the FragmentManager() object, and below 3.0, you need to call getSupportFragmentManager() to get it indirectly;

#### 5, the difference between FragmentPagerAdapter and FragmentStatePagerAdapter and usage scenarios
> + Reference answer:
> + Same: Both inherit PagerAdapter
> + Differences: Each Fragment of **FragmentPagerAdapter** is persisted in the FragmentManager and will not be destroyed as long as the user can return to the page. Therefore, it applies to those pages whose data is relatively static**, and the number of Fragment** is also relatively small**;
**FragmentStatePagerAdapter** only keeps the current page. When the page is not visible, the Fragment will be eliminated and its resources will be released. Therefore, it is suitable for those cases where the ** data dynamics are large, the ** occupies more memory, and the more Fragment;

### Service

#### 1. Talk about the life cycle of Service?
> + Reference Answer: The life cycle of Service involves six methods
> + **onCreate()**: If the service has not been created, the onCreate() callback will be executed after calling startService(); if the service is already running, calling onService() will not execute the onCreate() method. In other words, onCreate() will only be called when the service is created for the first time. If you execute startService() multiple times, it will not call onCreate() repeatedly. This method is suitable for some initialization work.
> + **onStartComand()**: Called when the service starts, this method is suitable for some data loading work, such as creating a thread here to download data or play music;
> + **onBind()**: Called when the service is bound;
> + **onUnBind()**: Called when the service is unbundled;
> + **onDestroy()**: Called when the service is stopped;
> + Recommended articles:
> + [Android component series --- Android Service component in-depth analysis] (https://www.cnblogs.com/smyhvae/p/4070518.html)

#### 2, Service two startup methods? Where is the difference?
> + Reference Answer: Two startup modes of Service
> + **startService()**: Calling startService in this way, onCreate() will only be called once, and multiple calls to startSercie will execute the onStartCommand() and onStart() methods multiple times. If the stopService() or stopSelf() method is not called externally, the service will run all the time.
> + **bindService()**: If the service has not been created before **, the system callback order is onCreate()→onBind(). If the service ** has been bound before calling the bindService() method, calling the bindService() method multiple times will not create the service and binding multiple times. If the caller wants to unbind the service** that is being bound**, you can call the unbindService() method with the callback order onUnbind()→onDestroy();
![](https://user-gold-cdn.xitu.io/2019/3/8/1695c1aaec35fdba?w=407&h=520&f=png&s=58930)
> + Recommended articles:
> + [Android Service two startup methods] (https://www.jianshu.com/p/4c798c91a613)

#### 3. How to ensure that Service is not killed?
> + Reference answer:
> + **onStartCommand mode, return START_STICKY or START_REDELIVER_INTENT**
> + START_STICKY: If START_STICKY is returned, it means that the process running by the Service is forcibly killed by the Android system, the Android system will still set the Service to the started state (that is, the running state), but the intent object passed in by the onStartCommand method will no longer be saved.
> + START_NOT_STICKY: If START_NOT_STICKY is returned, it means that the service will not be recreated after the process running by the service is forcibly killed by the Android system.
> + START_REDELIVER_INTENT: If you return START_REDELIVER_INTENT, its return is similar to START_STICKY, but the difference is that the system will keep the last intent passed to the onStartCommand method again and pass it again to the onStartCommand method of the recreated Service.
> + ** Increase the priority of the Service**
In the AndroidManifest.xml file for the intent-filter can be set to the highest priority by the android:priority = "1000" attribute, 1000 is the highest value, if the number is smaller, the lower the priority, and applicable to the broadcast;
> + ** Restart Service in onDestroy method**
When the service goes to onDestroy(), it sends a custom broadcast, and when it receives the broadcast, restarts the service;
> + **Improve the priority of the Service process**
Process priority from high to low: foreground process a visual process a service process a background process an empty process
You can use startForeground to put the service in the foreground state, so when you have low memory, the probability of being killed is lower;
> + **System Broadcast Monitor Service Status**
> + **Install APK to /system/app and turn it into a system-level app**
> + **Note**: The above mechanisms are not guaranteed to be 100% guaranteed, unless the system is whitelisted and die with the system.

#### 4. Can I start time-consuming operations in the Service? How to do it ?
> + Reference answer:
> + Service does not run in a child thread by default, nor does it run in a separate process. It is also executed in the main thread (UI thread). In other words, don't perform time-consuming operations in the Service, unless you manually open a child thread, there may be cases where the main thread is blocked (ANR);

#### 5, which system service has been used?
> + Reference answer:
![](https://user-gold-cdn.xitu.io/2019/3/8/1695c1aaecb6ae48?w=560&h=306&f=png&s=139877)

#### 6. Understand ActivityManagerService? What role does it play?
> + Reference answer:
ActivityManagerService is the core service in Android. It is mainly responsible for the startup, switching, scheduling and application process management and scheduling of the four components in the system. Its responsibilities are similar to the process management and scheduling modules in the operating system.
> + Recommended articles:
>   + [ActivityManagerService analysis - AMS startup process](https://blog.csdn.net/caohang103215/article/details/79597260)

### Broadcast Receiver

#### 1. How many forms does broadcasting have? What are the characteristics?
> + Reference answer:
> + Ordinary broadcast: The developer defines the intent broadcast (most commonly used), all broadcast receivers will receive the broadcast information at almost the same time, ** the order of acceptance is random**;
> + Ordered broadcast: The broadcasts sent out are received by the broadcast receiver** in order. Only one broadcast receiver can receive the broadcast message at the same time, when the logic in the broadcast receiver is executed. After that, the broadcast will continue to be delivered, and the broadcast receiver with a high priority will receive the broadcast message first. An ordered broadcast can be truncated by the receiver so that subsequent receivers cannot receive it;
> + Local broadcast: Send and receive broadcasts only in your own application, that is, only your own applications can receive them, the data is more secure and more efficient, but only ** dynamic registration** can be used;
> + Sticky Broadcast: This kind of broadcast will be stuck**, and when a receiver matching the broadcast is registered, the receiver will receive the broadcast;
> + Recommended articles:
>   + [Android four components: the most comprehensive analysis in the history of BroadcastReceiver](https://www.jianshu.com/p/ca3d87a4cdf3)

#### 2, two registration methods for broadcasting?
> + Reference answer:
![](https://user-gold-cdn.xitu.io/2019/3/8/1695c1aaec0b6b71?w=1025&h=240&f=png&s=54491)

#### 3. What is the principle of broadcast transmission and reception? (Binder mechanism, AMS)
> + Reference answer:
![](https://user-gold-cdn.xitu.io/2019/3/8/1695c1aaeac26f8d?w=576&h=386&f=png&s=12604)
> + Recommended articles:
> + [Bottom implementation principle of broadcasting] (https://www.jianshu.com/p/02085150339c)

### ContentProvider

#### 1. How much does ContentProvider know?
> + Reference answer:
As one of the four major components, ContentProvider is mainly responsible for storing and sharing data. Different from file storage, SharedPreferences storage, and SQLite database storage, the data saved by the latter can only be used by the application, while the former can allow data sharing between different applications. Choose which part of the data to share only to ensure that there is no risk of leakage of private data in the program.
> + Recommended articles:
> + [Android: Knowledge about ContentProvider is here! ](https://blog.csdn.net/carson_ho/article/details/76101093)

#### 2, ContentProvider permission management?
> + Reference answer:
> + read and write separation
> + privilege control - accurate to the table level
> + URL Control

#### 3, talk about the relationship between ContentProvider, ContentResolver, ContentObserver?
> + Reference answer:
> + **ContentProvider**: Manage data, provide data addition, deletion, and change operations. The data source can be database, file, XML, network, etc. ContentProvider provides a unified interface for accessing these data, which can be used to process between processes. data sharing.
> + **ContentResolver**: ContentResolver can manipulate data in different ContentProviders for different URIs, and external processes can interact with ContentProvider through ContentResolver.
> + **ContentObserver**: Observe the data changes in the ContentProvider and notify the outside world of the changes.

### data storage

#### 1. Describe the Android data persistence storage method?
> + Reference Answer: There are several common ways for the Android platform to implement data persistence:
> + SharedPreferences storage: a lightweight data storage method, essentially based on the key-value key-value pair data stored in the XML file, usually used to store some simple configuration information (such as various configuration information of the application);
> + SQLite database storage: a lightweight embedded database engine, which is very fast, takes up very little resources, and is often used to store a large amount of complex relational data;
> + ContentProvider: One of the four major components for data storage and sharing, not only to allow data sharing between different applications, but also to choose which part of the data to share, to ensure that the private data in the program will not Risk of leakage;
> + File file storage: The method of writing and reading files is the same as the program that implements I/O in Java;
> + network storage: mainly store related data in the remote server, the relevant data of the user operation can be synchronized to the server;

#### 2. Application scenarios of SharedPreferences? Precautions?
> + Reference answer:
> + SharedPreferences is a lightweight data storage method, essentially based on the key-value key-value pairs stored in the XML file, usually used to store some simple configuration information, such as int, String, boolean, float and long;
> + Note:
> + ** Do not store large complex data, which will cause the memory GC, blocking the main thread to make the page jam ANR**
> + **Do not operate in multi-process mode Sp**
> + ** Don't edit and apply multiple times, try to modify the commit once in bulk**
> + ** Suggest apply, use less commit**

> + Recommended articles:
> + [The most comprehensive and clear SharedPreferences analysis in history] (https://blog.csdn.net/geekerhw/article/details/79713068)
> + [SharedPreferences usage and considerations in multi-process] (http://zmywly8866.github.io/2015/09/09/sharedpreferences-in-multiprocess.html)

#### 3. What is the difference between the application and commit of SharedPrefrences?
> + Reference answer:
> + apply does not return a value and commit returns boolean to indicate whether the modification was successful.
> + apply commits the modified data atom to memory, and then asynchronously commits it to the hardware disk, and the commit is synchronously committed to the hardware disk, so when multiple concurrent commit commits, they wait for the commit being processed to be saved. After the disk is in operation, it reduces efficiency. Apply is only the atomic commit to the content, and the function that calls apply will directly overwrite the previous memory data, which improves the efficiency to a certain extent.
> + apply method will not prompt for any failed prompts. Since sharedPreference is a single instance in a process, there is generally no concurrency conflict. If you don't care about the result of the submission, it is recommended to use apply. Of course, you need to ensure that the submission is successful and there are follow-up actions.

#### 4. Understand the transaction operations in SQLite? How is it done?
> + Reference answer:
> + SQLite starts the transaction by default when doing CRDU operation, then translates the SQL statement into the corresponding SQLiteStatement and calls its corresponding CRUD method. At this time, the whole operation is performed on the temporary file of the rollback journal, only the operation is completed successfully. Will update the db database, otherwise it will be rolled back;

#### 5. Is there any good way to do batch operations using SQLite?
> + Reference answer:
> + Use SQLiteDatabase's beginTransaction method to open a transaction, convert batch operation SQL statements into SQLiteStatement and perform bulk operations, endTransaction()

#### 6. How to delete individual fields of a table in SQLite
> + Reference answer:
> + SQLite database only allows to add fields without allowing to modify and delete table fields, only create new tables to retain the original fields, delete the original table

#### 7. What optimizations will be used when using SQLite?
> + Reference answer:
> + Use transactions for bulk operations
> + Close Cursor in time to avoid memory leaks
> + Time-consuming operation asynchronous: the operation of the database belongs to the local IO time-consuming operation, it is recommended to put it into the asynchronous thread for processing
> + Capacity adjustment of ContentValues: ContentValues ​​internally uses HashMap to store Key-Value data. The initial capacity of ContentValues ​​is 8, which doubles when expanding. Therefore, it is recommended to estimate the content filled in by ContentValues, set a reasonable initialization capacity, and reduce unnecessary internal expansion operations.
> + Use index to speed up retrieval: Recommended index for search operations with large magnitude and high query requirements

### IPC

#### 1. What is the relationship between processes and threads in Android? the difference?
> + Reference answer:
> + Thread is the smallest unit** of the CPU schedule, while the thread is a **limited system resource
> + Process generally refers to an execution unit, a program or an application on a PC and mobile device
> + In general, an App program** has at least one ** process, a process ** has at least one ** thread (contains the relationship with the included),
Generally speaking, there is a process in the App factory, the thread is the production line inside, but the main thread (main production line) has only one, and the child thread (sub-production line) can have multiple
> + The process has its own independent address space, and the threads in the process share this address space, you can ** concurrent ** execute
> + Recommended articles:
> + [Android developer official documentation - process and thread] (https://developer.android.com/guide/components/processes-and-threads?hl=en)

#### 2. How to open multiple processes? Can the application open N processes?
> + Reference answer:
> + Assign properties to the four components in AndroidMenifest android:process to open multi-process mode
> + N processes can be started under the memory allowed
> + Recommended explanation:
> + [How to open multi-process? Can the application open N processes? ](https://github.com/qmsggg/qmsggg_BlogCollect/issues/158)

#### 3. Why do I need IPC? Possible problems with multi-process communication?
> + Reference answer:
> + All four components (Activity, Service, Receiver, ContentProvider) running in different processes will fail to share data. This is because Android allocates separate virtual machines for each application. Different virtual machines have different memory allocations. The address space, which causes multiple copies of objects in the same virtual machine to access the same class. For example, common examples (** by opening multiple processes to get more memory space, sharing data between two or more applications, WeChat family bucket**)
> + In general, using multi-process communication can cause the following problems
> + ** Static member and singleton mode are completely invalid**: Independent virtual machine caused
> + ** Thread synchronization mechanism is fully effective**: Independent virtual machine caused
> + **SharedPreferences reliability decline**: This is because Sp does not support two processes concurrently read and write, there is a chance to cause data loss
> + **Application will be created multiple times**: Android system will allocate a separate virtual machine when creating a new process, so this process is actually the process of starting an application, naturally also creating a new Application
> + Recommended articles:
> + [Android developer official documentation - process and thread] (https://developer.android.com/guide/components/processes-and-threads?hl=en)

#### 4, IPC mode in Android, advantages and disadvantages of various methods, why choose Binder?
> + Reference answer:
![](https://user-gold-cdn.xitu.io/2019/3/8/1695c1ab2aabf780?w=1240&h=720&f=jpeg&s=126001)
Compared with the traditional IPC mechanism on Linux, such as System V, Socket, where is Binder?
> + ** High transmission efficiency and operability**: The main influencing factor of transmission efficiency is the number of memory copies. The less the number of copies, the higher the transmission rate. From the perspective of Android process architecture: For message queues, Sockets, and pipes, data is first copied from the sender's cache to the kernel-developed cache, and then copied from the kernel cache to the receiver's cache. Copy, as shown:
![](https://user-gold-cdn.xitu.io/2019/3/8/1695c427354bbec4?w=500&h=185&f=png&s=54991)
For Binder, the data is copied from the sender's buffer to the kernel's buffer, and the receiver's cache and the kernel's cache are mapped to the same physical address, saving a data copy process, as shown in the figure. :
![](https://user-gold-cdn.xitu.io/2019/3/8/1695c428e3c4a95a?w=485&h=234&f=png&s=81083)
Due to the complexity of shared memory operations, Binder's transmission efficiency is the best.
> + ** Convenient implementation of C / S architecture **: Linux's IPC mode is not based on C / S architecture except Socket, and Socket is mainly used for communication between networks and transmission efficiency is low. Binder is based on the C/S architecture. The server and client are relatively independent and have good stability.
> + **High security**: The receiver of the traditional Linux IPC cannot obtain the reliable UID/PID of the other process, so that the identity of the other party cannot be authenticated. The Binder mechanism assigns the UID/PID to each process and will communicate with the Binder. Validity detection based on UID/PID.
> + Recommended articles:
> + [Why does Android use Binder as an IPC mechanism? ](https://www.zhihu.com/question/39440766)

#### 5, the role and principle of the Binder mechanism?
> + Reference answer:
> + Linux system divides a process into **userspace** and **kernelspace**. For the process, the user space data can not be shared, the kernel space data can be shared, in order to ensure security and independence, a process can not directly operate or access another process, that is, the Android process is independent and isolated. , which requires data communication between processes
[Traditional IPC mechanism principle] (https://user-gold-cdn.xitu.io/2019/3/8/1695c1ab41198d5c?w=1240&h=853&f=jpeg&s=53125)

> + A complete Binder IPC communication process usually looks like this:
> + First the Binder driver creates a data receive buffer in the kernel space;
> + Then open a kernel cache area in the kernel space, establish the mapping relationship between the kernel cache area and the data receive buffer area in the kernel, and the mapping relationship between the data receive buffer area and the receive process user space address in the kernel;
> + The sender process copies the data to the kernel cache in the kernel through the system call copyfromuser(). Since the user space of the kernel cache and the receiving process has a memory map, it is equivalent to sending data to the user space of the receiving process. This completes an interprocess communication.
![Binder mechanism principle] (https://user-gold-cdn.xitu.io/2019/3/8/1695c1ab2efe8dc5?w=1240&h=885&f=jpeg&s=63773)

#### 6. What is the role of ServiceManager in the Binder framework?
> + Reference answer:
> + **Binder Framework** is based on the C/S architecture. It consists of a series of components, including Client, Server, ServiceManager, and Binder driver. Client, Server, and Service Manager run in user space, and Binder driver runs in kernel space.
![](https://user-gold-cdn.xitu.io/2019/3/8/1695c1ab50cf525f?w=1240&h=640&f=jpeg&s=40718)
> + **Server&Client**: Server & Client. Communication between Client-Server is performed on the infrastructure provided by the Binder driver and Service Manager.
> + **ServiceManager** (like the DNS domain name server) service manager, convert the Binder name to a reference to the Binder in the Client, so that the Client can obtain a reference to the Binder entity in the Server through the Binder name.
> + **Binder driver** (like router): responsible for the underlying support of the establishment, delivery, counting management and data transfer interaction of the binder communication between processes.
![](https://user-gold-cdn.xitu.io/2019/3/8/1695c1ab5abdf775?w=1117&h=1220&f=png&s=308318)
Image from Carson_Ho article - Android cross-process communication: Graphical explanation Binder mechanism Principle

#### 7. Why does the Bundle pass the object serialization? What is the difference between Serialzable and Parcelable?
> + Reference answer:
> + Because bundles only support basic data types when passing data, serialization is required to be converted to a storable or transportable essence state (byte stream) when passing objects. The serialized object can be transferred between the network, IPC (such as the Activity, Service, and Reciver that starts another process), or it can be stored locally.
> + Two ways to implement serialization: Implement the Serializable/Parcelable interface. The different points are as follows:
![](https://user-gold-cdn.xitu.io/2019/3/8/1695c349f019c41f?w=500&h=393&f=png&s=104411)

#### 8. Tell me about AIDL? What is the principle? How to optimize the use of AIDL in multiple modules?
> + Reference answer:
> + AIDL (Android Interface Definition Language): If you want to call a method in another process in a process, you can use AIDL to generate serializable parameters. AIDL will generate a proxy class for the server object. Through its client implementation of the method of indirectly calling the server object.
> + The essence of AIDL is that the system provides a set of tools to quickly implement Binder. Key categories and methods:
> + **AIDL interface**: Inherited IInterface.
> + **Stub class**: Binder's implementation class, the server provides services through this class.
> + **Proxy class**: The local proxy of the server, the way the client invokes the server through this class.
> + **asInterface()**: The client calls the Binder object returned by the server to be converted into the AIDL interface type object required by the client. If the client and the server are in a unified process, the Stub object itself is directly returned. Otherwise, the Stub.proxy object encapsulated by the system is returned.
> + **asBinder()**: Returns the Binder object of the proxy Proxy according to the current call.
> + **onTransact()**: In the Binder thread pool running the server, when the client initiates a cross-process request, the remote request will be processed by the underlying encapsulation of the system.
> + **transact()**: Runs on the client, suspending the current thread while the client initiates the remote request. The server's onTransact() is then called until the remote request returns, and the current thread continues execution.
> + When there are multiple business modules that need AIDL for IPC, you need to create a specific aidl file for each module, then there will be a lot of corresponding services. There will inevitably be a problem of serious system resource consumption and excessive application of heavyweight. The solution is to establish a Binder connection pool, that is, the Binder request of each service module is uniformly forwarded to a remote service to execute, thereby avoiding repeated creation of the Service.
> + **How ​​it works**: Each business module creates its own AIDL interface and implements this interface, then provides its own unique identifier and its corresponding Binder object to the server. The server only needs a Service. The server provides a queryBinder interface, which returns the corresponding Binder object according to the characteristics of the business module. After the different business modules get the required Binder object, the remote method can be called.

### View

#### 1. Tell the drawing process of View?
> + Reference answer:
> + View workflow mainly refers to the three processes of measure, layout, and draw, namely measurement, layout and drawing, where measure determines the ** measurement width/height of the view**, and the layout determines the final width/height of the view. ** and ** four vertex positions**, while draw draws View** onto the screen**
> + The drawing process of View follows the following steps:
> + **Draw background** background.draw(canvas)
> + ** draw yourself** (onDraw)
> + **Drawing children**(dispatchDraw)
> + **Draw Decoration**(onDrawScollBars)
![](https://user-gold-cdn.xitu.io/2019/3/8/1695c1ab5b9705a3?w=670&h=559&f=png&s=298536)
> + Recommended articles:
> + [official documentation] (https://developer.android.com/reference/android/view/View)
> + [Drawing Process of Android View] (https://www.jianshu.com/p/5a71014e7b1b)
> + [Android application layer View drawing process and source code analysis] (https://blog.csdn.net/yanbober/article/details/46128379)

#### 2. What is MotionEvent? Contains several events? Under what conditions will it occur?
> + Reference answer:
> + MotionEvent is a series of events that occur after a finger touches the screen. Typical event types are as follows:
> + **ACTION_DOWN**: Finger just touched the screen
> + **ACTION_MOVE**: Finger moves on the screen
> + **ACTION_UP**: The moment the finger is released from the screen
> + **ACTION_CANCELL**: Triggers when the finger remains pressed and transitions from the current control to the outer control
> + Under normal circumstances, a finger touching the screen will trigger a series of click events, consider the following:
> + Click on the screen to release, event sequence: DOWN→UP
> + Click the screen to slide for a while and then release, the event sequence is DOWN→MOVE→.....→MOVE→UP

#### 3. Describe the View event delivery distribution mechanism?
> + Reference answer:
> + View event distribution is essentially the process of distributing MotionEvent events. That is, when a MotionEvent occurs, the system passes the click event to a specific View.
> + Click event delivery order: **Activity(Window)→ViewGroup→ View**
> + The event distribution process is done by three methods:
> + **dispatchTouchEvent**: Used to distribute events. If the event can be passed to the current View, then this method must be called, the return result is affected by the current View's onTouchEvent and the lower-level View's dispatchTouchEvent method, indicating whether the current event is consumed.
> + **onInterceptTouchEvent**: Called inside the above method to intercept the event. This method is only available in ViewGroup, and View (without ViewGroup) is not available. Once intercepted, the ViewGroup's onTouchEvent is executed, and the event is processed in the ViewGroup without being distributed to the View. And only once, returning results indicating whether to intercept the current event
> + **onTouchEvent**: Called in the dispatchTouchEvent method to handle the click event, returning a result indicating whether the current event is consumed

#### 4, how to solve the event conflict of View? What is the example encountered in development?
> + Reference answer:
> + Common conflicts in the development of the slide conflict between the ScrollView and the RecyclerView, the RecyclerView inline slides in the same direction
> + Processing rules for sliding conflicts:
> + For sliding conflicts due to inconsistent external sliding and internal sliding directions, you can determine who will intercept the event based on the direction of the sliding.
> + For sliding conflicts caused by the consistency of the external sliding direction and the internal sliding direction, it is possible to specify when to let the external View intercept the event and when the event is intercepted by the internal View according to the business requirements.
> + For the nesting of the above two cases, it is relatively complicated, and it can also find a breakthrough point in the business according to the demand.
> + Implementation of sliding conflicts:
> + **External Intercept Method**: Refers to the click event is intercepted by the parent container first, if the parent container needs this event to intercept, otherwise it will not intercept. Specific method: you need to override the parent container's onInterceptTouchEvent method to make the corresponding interception internally.
> + **Internal interception method**: The parent container does not intercept any events, but passes all events to the child container. If the child container needs this event, it will be consumed directly, otherwise it will be processed by the parent container. Specific method: need to cooperate with the requestDisallowInterceptTouchEvent method.


#### 5, the difference between scrollTo() and scollBy()?
> + Reference answer:
> + scollBy internally calls scrollTo, which is based on relative sliding of the current position; and scrollTo is absolutely sliding, so if the same method is used to call the scrollTo method multiple times, since the initial position of the View is constant, it will only appear once. View scrolling effect
> + Both can only slide the View content, not the View itself. Can use the Scroller to have an excessive sliding effect
> + Recommended articles:
> + [View sliding principle and implementation] (https://www.jianshu.com/p/a177869b0382)

#### 6. How does Scroller realize the elastic sliding of View?
> + Reference answer:
> + Call the startScroll() method when the MotionEvent.ACTION_UP event fires. This method does not perform the actual sliding operation, but records the sliding correlation amount (sliding distance, sliding time).
> + Then call the invalidate/postInvalidate() method to request the View to redraw, causing the View.draw method to be executed.
> + When View repaints, it will call the computeScroll method in the draw method, and computeScroll will go to the Scroller to get the current scrollX and scrollY; then use the scrollTo method to achieve sliding; then call the postInvalidate method to perform the second redraw, and In the same process as before, this repeatedly causes the View to continuously slide a small amount, and multiple small slidings constitute a flexible sliding until the entire sliding ends.
![](https://user-gold-cdn.xitu.io/2019/3/8/1695c37ec2b0e11b?w=1148&h=746&f=jpeg&s=157379)

#### 7, the difference between invalidate() and postInvalidate()?
> + Reference answer:
> + invalidate() and postInvalidate() are used to refresh the View. The main difference is that invalidate() is called in the main thread. If it is used in the child thread, it needs to cooperate with the handler; and postInvalidate() can be called directly in the child thread.

#### 8, the difference between SurfaceView and View?
> + Reference answer:
> + View needs to refresh the screen in the UI thread, and SurfaceView can refresh the page in the child thread
> + View is suitable for active update, and SurfaceView is suitable for passive update, such as frequent refresh. This is because if you use View to refresh frequently, it will block the main thread, resulting in interface jam.
> + SurfaceView has implemented double buffering at the bottom layer, but View does not, so SurfaceView is more suitable for pages that require a lot of data processing when refreshing and refreshing frequently (such as video playback interface).

#### 9, How to customize the view to consider the model adaptation?
> + Reference answer:
> + Reasonable use of warp_content, match_parent
> + As far as possible, use RelativeLayout
> + For different models, use different layout files in the corresponding directory, android will automatically match.
> + Try to use a 9 picture.
> + Use density-independent pixel units dp,sp
> + Introduce the android percentage layout.
> + Cut the large resolution image when cutting the image and apply it to the layout. It also has a good display on small resolution phones.

### Handler

#### 1. Talk about the role of the message mechanism Handler? What are the elements? What the process is like ?
> + Reference answer:
> + Responsible for **cross-thread communication**, because ** can't do time-consuming operations on the main thread, and child threads can't update UI**, so when the user needs to update the UI after time-consuming operations in the child thread, The Handler switches the operation of the UI to the main thread for execution.
> + is divided into four major elements
> + **Message: A message that needs to be delivered. The message is divided into hardware-generated messages (such as buttons, touches) and software-generated messages.
> + **MessageQueue (Message Queue)**: Responsible for the storage and management of messages, responsible for managing the Message sent by the Handler. Reading will automatically delete messages, and there are advantages to singular list maintenance, insertion and deletion. In its next() method, it loops indefinitely, constantly judging whether there is a message, and returning this message and removing it.
> + **Handler (message processor)**: Responsible for the sending and processing of Message. It mainly sends various message events (Handler.sendMessage()) and handles corresponding message events (Handler.handleMessage()) to the message pool. According to the first-in-first-out execution, the structure of the single-linked list is used internally.
> + **Looper (message pool)**: Responsible for the associated thread and the distribution of the message. Under this thread, the Message is obtained from the MessageQueue and distributed to the Handler. When the Looper is created, a MessageQueue is created. When the loop() method is called, the message is called. The loop begins, where the next() method of the messageQueue is called continuously, and is processed when there is a message, otherwise it is blocked in the next() method of the messageQueue. When Looper's quit() is called, it will call the quit() of the messageQueue. At this point, next() will return null, and then the loop() method will exit.
> + The specific process is as follows
![](https://user-gold-cdn.xitu.io/2019/3/11/1696ad811957795d?w=737&h=512&f=png&s=27154)
> + A Looper is created when the main thread is created, and a message queue is also created inside Looper. When the key Handler is created, the current thread's Looper is taken, and the message queue is obtained through the Looper object, and then the Handler adds a Message to the message queue through the **MessageQueue.enqueueMessage** in the child thread.
> + Open the message loop via **Looper.loop()** to continuously poll the call **MessageQueue.next()**, get the corresponding Message and pass it to the Handler via **Handler.dispatchMessage**, finally call ** Handler.handlerMessage** handles the message.

#### 2. Can a thread create multiple Handlers, the correspondence between Handler and Looper?
> + Reference answer:
> + ** A Thread can only have one Looper, one MessageQueen, and can have multiple Handlers**
> + **Based on one thread, their order of magnitude relationship is: Thread(1) : Looper(1) : MessageQueue(1) : Handler(N)**

#### 3, the difference between soft references and weak references
> + Reference answer:
> + **Soft Reference**: If an object only has soft references, the garbage collector will not reclaim it when there is sufficient memory; if there is not enough memory, the memory of those objects will be reclaimed. As long as the garbage collector does not recycle it, the object can always be used by the program.
> + **weak reference**: If an object only has weak references, then during the garbage collector thread scan, once an object with only weak references is found, regardless of whether the current memory space is sufficient or not, it will be recycled. RAM.
> + The fundamental difference between the two is that only objects with weak references have a shorter life cycle and may be recycled at any time. Objects with only soft references are only reclaimed when there is not enough memory, and are usually not reclaimed when there is enough memory.
![](https://user-gold-cdn.xitu.io/2019/3/11/1696c45ababaed39?w=858&h=378&f=jpeg&s=57940)
> + Recommended articles:
> + [Four reference types in Java: strong references, soft references, weak references, and virtual references] (https://segmentfault.com/a/1190000015282652)

#### 4, the cause of memory leak caused by Handler and the best solution
> + Reference answer:
> + Reason for disclosure:
> + Handler allows us to send a delayed message, and if the user closes the activity during the delay, the activity will leak. This leak is because Message will hold the Handler, and because of the nature of Java, the inner class will hold the outer class, so that the Activity will be held by the Handler, which will eventually lead to the disclosure of the Activity.
> + Solution:
> + ** Define the Handler as a static inner class, hold a weak reference to the Activity internally, and call handler.removeCallbacksAndMessages(null) in Acitivity's onDestroy() to remove all messages in time. **

#### 5. Why is the system not recommended to access the UI in a child thread?
> + Reference answer:
> + Android UI controls are not ** thread safe**, if concurrent access in multiple threads may cause UI controls to be in an unpredictable state
> + At this point you may ask why the system does not add a lock mechanism to the UI control access? because
> + Locking mechanism will make the UI access logic complex
> + Locking mechanism will reduce UI access efficiency, because locking will block the execution of certain threads
![](https://user-gold-cdn.xitu.io/2019/3/13/16975fdd364417e0?w=1208&h=579&f=png&s=24683)

#### 6. Why doesn't the Looper infinite loop cause the application to die?
> + Reference answer:
> + The main method of the main thread is ** message loop **, once you exit the message loop, then your application will quit, the Looer.loop () method may cause the main thread to block, but as long as its message loop does not Blocked, the ANR exception will not be generated if the event can be processed all the time.
> + The **ANR** is not blocked by the main thread, but the main thread's Looper message processing process ** task blocking **, unable to respond to gesture operations, can not refresh the UI in time.
> + ** Blocking and program non-response ** is not necessarily related, although the main thread is blocked when there is no message to process, but as long as the message can be processed immediately, the program will not be unresponsive.

#### 7. What happens to the message queue after using PostDealy of Handler?
> + Reference answer:
> + If there is only this message in the queue, the message will not be sent, but the time to wake up will be calculated. The Looper will be blocked first, and it will be woken up by the time. However, if a new message is to be added at this time, the header of the message queue is longer than the delay time, and then inserted into the head, sorted according to the trigger time, the time of the team leader is the smallest, and the time of the team tail is the largest.

#### 8, can you directly create a new Handler in the child thread? How to do it?
> + Reference answer:
> + No, because in the main thread**, the Activity contains a Looper object inside, it will automatically manage the Looper, processing the message sent in the child thread. For the **sub-thread**, there are no objects to help us maintain the Looper object, so we need to manually maintain it. So to open the Handler in the child thread, first create a Looper, and open the Looper loop.
![](https://user-gold-cdn.xitu.io/2019/3/14/1697b3a7257d670a?w=1312&h=878&f=png&s=129237)
> + Recommended articles:
> + [Android asynchronous message processing mechanism is fully parsed, let you thoroughly understand from the source code] (https://blog.csdn.net/guolin_blog/article/details/9991569)

#### 9, How can Message be created? Which effect is better, why?
> + Reference Answer: Can be created in three ways:
> + Generate instances directly**Message m = new Message**
> + via **Message m = Message.obtain**
> + via **Message m = mHandler.obtainMessage()**
> + The latter two are better, because the number of messages in the default message pool of Android is 10, and the latter two are to take a Message instance directly in the message pool, so as to avoid generating multiple Message instances.

### Thread
#### 1. What are the benefits of thread pool? Four thread pool usage scenarios, understanding of several parameters of the thread pool?
> + Reference answer:
> The benefit of using a thread pool is to reduce the time spent creating and destroying threads and the overhead of system resources to solve the problem of insufficient resources. If you do not use the thread pool, it may cause the system to create a large number of similar threads, resulting in the consumption of memory or "over-switching" problem, the summary is
> + ** Reuse existing threads, reduce the overhead of object creation and extinction, and have good performance. **
> + ** can effectively control the maximum number of concurrent threads, improve the utilization of system resources, while avoiding excessive resource competition and avoiding congestion. **
> + ** Provides functions such as scheduled execution, periodic execution, single thread, and concurrent number control. **
> + Thread pool in Android is directly or indirectly through the configuration of ThreadPoolExecutor to achieve different characteristics of the thread pool. The most common classes in Android have different characteristics of the thread pool are:
> + **newCachedThreadPool**: Only non-core threads, the maximum number of threads is very large, all threads will create new threads for new tasks when they are active, otherwise they will use idle threads (60s idle time, will be recycled after passing, so There are 0 threads in the thread pool possible to handle the task.
> + Advantages: Any task will be executed immediately (the task queue SynchronousQuue is equivalent to an empty collection); it is more suitable for executing a large number of less time-consuming tasks.
> + **newFixedThreadPool**: only the core thread, and the number is fixed, all threads are active, because the queue has no limit size, the new task will wait for execution, the thread will not release the worker thread when the thread pool is idle, it will take up certain System resources.
> + Advantages: Faster response to external requests
> + **newScheduledThreadPool**: The number of core threads is fixed, non-core threads (the number of cores that are idle and will be reclaimed immediately) is unlimited.
> + Advantages: Performing scheduled tasks and repetitive tasks with fixed cycles
> + **newSingleThreadExecutor**: Only one core thread, ensuring that all tasks are completed in order in the same thread
> + Advantages: no need to deal with thread synchronization issues
> + You can see from the source code that the above four thread pools are actually implemented using the ThreadPoolExecutor class.
![](https://user-gold-cdn.xitu.io/2019/3/14/1697b39705b3f2e3?w=1870&h=1022&f=png&s=294409)
> + Recommended articles:
> + [java thread pool resolution and use of four thread pools] (https://blog.csdn.net/ztchun/article/details/57413255)

#### 2, Android also know which classes are convenient for thread switching?
> + Reference answer:
> + **AsyncTask**: The underlying package of thread pool and Handler is convenient for performing background tasks and UI operations in child threads.
> + **HandlerThread**: A thread with a message loop that can use Handlers internally.
> + **IntentService**: is an asynchronous, automatic stop service, internally using HandlerThread.

#### 3, talk about the principle of AsyncTask
> + Reference answer:
> + ** There are two thread pools (SerialExecutor and THREAD_POOL_EXECUTOR) and one Handler (InternalHandler) in the AsyncTask, where the thread pool SerialExecutor is used for queuing tasks, while the thread pool THREAD_POOL_EXECUTOR is used to actually perform tasks, and the InternalHandler is used to execute the environment. Switch from the thread pool to the main thread. **
> + **sHandler is a static Handler object. In order to be able to switch the execution environment to the main thread, this requires the sHandler object to be created in the main thread. Since static members are initialized when the class is loaded, this disguise requires that the AsyncTask class must be loaded in the main thread, otherwise the AsyncTask in the same process will not work properly. **

#### 4. What is the use of IntentService?
> + Reference answer:
> + **IntentService** can be used to ** execute the time-consuming ** task, when the task is completed, it will automatically stop **, and because the IntentService is the reason of the service, different from the ordinary Service, IntentService can be ** Automatically create a child thread ** to perform the task, which results in its priority is higher than the simple thread, not easy to be killed by the system, so the IntentService is more suitable to perform some ** high priority ** background tasks.

#### 5, the difference between creating a thread directly in the Activity and creating a thread in the service?
> + Reference answer:
> + ** is created in the Activity **: The Thread is for this Activity service, complete the task of this specific Activity, actively notify the Activity some messages and events, after the Activity is destroyed, the Thread is not alive Meaning.
> + ** is created in Service**: This is the only way to guarantee the longest lifecycle of Thread. As long as the entire Service does not exit, Thread can always be executed in the background, usually created in Service's onCreate(). Destroyed in onDestroy(). Therefore, the Thread created in the Service is suitable for long-term execution of some background tasks independent of the APP. The more common one is to maintain a long connection with the server in the Service.

#### 6, ThreadPoolExecutor's work strategy?
> + Reference Answer: ThreadPoolExecutor will follow the following rules when performing tasks
> + If the number of threads in the thread pool ** does not reach the number of core threads, then a core thread is started directly to perform the task.
> + If the number of threads in the thread pool** has reached ** or exceeds the number of core threads, then the task will be queued into the task queue for execution.
> + If the task cannot be inserted into the task queue at point 2, this is often because the task queue is full. At this time, if the number of threads ** does not reach the maximum value specified by the thread pool**, then a non-start will be started immediately. The core thread to perform the task.
> + If the number of threads in point 3 has reached the maximum value specified by the thread pool**, then the task is refused, and ThreadPoolExecutor will call the rejectedExecution method of RejectedExecutionHandler to notify the caller.

#### 7, the difference between Handler, Thread and HandlerThread?
> + Reference answer:
> + **Handler**: In android, it is responsible for sending and processing messages, through which it can realize message communication between other branch threads and the main thread.
> + **Thread**: The smallest unit in the Java process that performs the operation, which is the basic unit that executes the processor scheduling. A program that runs separately in a process.
> **HandlerThread**: A class HandlerThread that inherits from Thread. Android does not have any wrapper around Thread in Java. Instead, it provides a class HandlerThread class that inherits from Thread. This class does a lot of Java Thread. Convenient packaging. HandlerThread inherits from Thread, so it is essentially a Thread. The difference with the normal Thread is that it directly implements the Looper implementation internally, which is essential for the Handler message mechanism. With our own looper, we can distribute and process messages in our own threads. If you don't use HandlerThread, you need to manually call Looper.prepare() and Looper.loop().

#### 8, the principle of ThreadLocal
> + Reference answer:
> + ThreadLocal is a class for creating thread-local variables. The usage scenario is as follows:
> + ** Implement a single thread singleton and a single thread context information store, such as transaction ids. **
> + ** Implementing thread-safe, non-thread-safe objects become thread-safe after using ThreadLocal, because each thread will have a corresponding instance. Host some thread-related data to avoid passing parameters back and forth in the method. **
> + When you need to use multi-threading, there is a variable that does not need to be shared. In this case, you don't have to use the so complicated keyword to lock. Each thread is equivalent to open a space in the heap memory. For the buffer of the shared variable, the shared variable in the heap memory is read and operated through the buffer, and ThreadLocal is equivalent to the memory in the thread and a local variable. Each time the data of the thread itself can be read and manipulated, there is no need to interact with variables in main memory through the buffer. It does not modify the main memory data as synchronized, and then copies the main memory data to the working memory in the thread. ThreadLocal allows threads to monopolize resources and store them inside threads, avoiding CPU congestion and causing CPU throughput to drop.
> + Include a ThreadLocalMap in each Thread, the key of ThreadLocalMap is the object of ThreadLocal, and value is exclusive data.

#### 9, whether multi-threading will be efficient (pros and cons)
> + Reference answer:
> + The advantages of multi-threading:
> + ** Convenient and efficient memory sharing - Memory sharing in multiple processes is inconvenient and will offset the benefits of multi-process programming**
> + ** Lighter context switching overhead - no need to switch address space, no need to change CR3 registers, no need to empty TLB**
> + **The task on the thread is automatically destroyed after execution **
> + The disadvantages of multithreading:
> + **Opening a thread requires a certain amount of memory space (by default, each thread occupies 512KB)**
> + **If you open a lot of threads, it will take up a lot of memory space and reduce the performance of the program**
> + ** The more threads, the more overhead the cpu has on the calling thread**
> + **Programming is more complicated, such as communication between threads, multi-threaded data sharing**
> + In summary, multi-threading** does not necessarily improve efficiency, but in the case of tight memory space, it is a burden, so in daily development, try to
> + ** Don't create, destroy threads, use thread pools**
> + ** Reduce inter-thread synchronization and communication (most critical)**
> + ** Avoid the need to share frequently written data**
> + ** Reasonably arrange shared data structure to avoid false sharing**
> + **Using non-blocking data structures/algorithms**
> + ** Avoid system calls (such as mmap) that may cause scalability issues**
> + ** Avoid generating a lot of page faults and try to use Huge Page**
> + **Use user-mode lightweight threads instead of kernel threads if possible**

#### 10, multi-threading, let you make a single case, what do you do
> + Reference answer:
> + There are many factors to consider when setting up a singleton pattern in multithreading, such as thread safety - lazy loading - code security: such as preventing serialization attacks, preventing reflection attacks (preventing reflection from private method calls) - performance factors
> + There are a variety of implementation methods, hungry, lazy (thread safe, thread non-secure), double check (DCL), inner class, and enumeration
![](https://user-gold-cdn.xitu.io/2019/3/15/16980b520b353f34?w=1160&h=842&f=png&s=123575)
> + Recommended articles:
> + [Summary of Singleton Mode] (https://xxxblank.github.io/2017/09/14/singleTon/)

#### 11, in addition to notify, what else can wake up the thread
> + Reference answer:
> + When a thread with an Object lock calls the wait() method, it will cause the current thread to join the object.wait wait queue, and release the currently occupied Object lock, so that other threads have the opportunity to acquire the Object lock and get the Object. The lock thread calls the notify() method to randomly wake up a thread in the Object.wait wait queue (the wakeup is random regardless of the order of join, and the high priority wakeup probability is high)
> + Wake up all threads if the notifyAll() method is called. **Note: After calling the notify() method, the object lock will not be released immediately, and the Object lock will be released after the thread finishes executing. **

#### 12, What is ANR? What happens with ANR? How to avoid it? How to quickly locate the ANR problem without looking at the code?
> + Reference answer:
> + ANR (Application Not Responding): When the operation cannot be processed by the system for a period of time, the ANR dialog box will pop up at the system level.
> + ANR may be generated because there is no response to user input events within 5s, BroadcastReceiver is not terminated within 10s, and Service is not terminated within 20s
> + Want to avoid ANR, do not do time-consuming operations in the main thread, but by opening sub-threads, such as inheriting Thread or implementing Runnable interface, using AsyncTask, IntentService, HandlerThread, etc.
> + Recommended articles:
> + [How to quickly analyze and locate ANR] (https://www.jianshu.com/p/cfa9ed42e379)

### Bitmap
#### 1. What problems should I pay attention to when using Bitmap?
> + Reference answer:
> + **To choose the right image specification (bitmap type)**: Usually when we optimize Bitmap, when we need to do performance optimization or prevent OOM, we usually use RGB_565, because ALPHA_8 only has transparency, it shows no meaning in general picture, Bitmap .Config.ARGB_4444 shows that the picture is unclear, Bitmap.Config.ARGB_8888 takes up the most memory. :
> + ALPHA_8 1 byte of memory per pixel
> + ARGB_4444 2 bytes of memory per pixel
> + ARGB_8888 4 bytes of memory per pixel (default)
> + RGB_565 2 bytes of memory per pixel
> + **Reduced sampling rate**: Use of BitmapFactory.Options parameter inSampleSize, first set options.inJustDecodeBounds to true, just to read the size of the image, after comparing the size of the image and the size to be displayed The calculateInSampleSize() function calculates the specific value of inSampleSize and gets the value. options.inJustDecodeBounds is set to false to read image resources.
> + **Reuse memory**: It is through soft reference (recycling when memory is not enough), multiplex memory block, no need to re-apply a new memory for this bitmap, avoiding a memory allocation and Recycling improves operational efficiency.
> + ** Recycle memory in time using the recycle() method**.
> + **Compressed images**.

#### 2, Bitmap.recycle() will be recycled immediately? When will it be recycled? If there is no place to use this Bitmap, why is garbage collection not directly recycled?
> + Reference answer:
> + It can be learned from the source code that after loading the Bitmap into memory, it contains the two parts of the memory area**. To put it simply, part of it is the **Java part**, and part of it is the **C part**. This Bitmap object is allocated by the Java part. When not in use, the system will automatically recycle it.
> + But the corresponding **C can be used in the memory area, the virtual machine can not be directly recycled, this can only call the underlying function release. So you need to call the recycle() method to release the memory of the C part.
> + bitmap.recycle () method is used to recover the memory occupied by the Bitmap, then the bitmap is empty, and finally use System.gc () to call the system garbage collector for recycling, call System.gc () is not guaranteed Start the recycling process immediately, just to speed up the recovery.

#### 3, a calculation of the memory and memory usage of a Bitmap
> + Reference answer:
> + Bitamp memory size = width pixel x (inTargetDensity / inDensity) x height pixel x (inTargetDensity / inDensity)x memory byte size occupied by one pixel
> + Note: Here inDensity represents the dpi of the target image (under which resource folder), inTargetDensity represents the dpi of the target screen, so you can find that inDensity and inTargetDensity will stretch the width and height of the Bitmap, thus changing the memory occupied by Bitmap. the size of.
> + There are two ways to get the memory footprint in the Bitmap.
> + **getByteCount()**: API12 join, which represents the minimum amount of memory required to store the pixels of the Bitmap.
> + **getAllocationByteCount()**: API19 join, which represents the amount of memory allocated for Bitmap in memory, instead of the getByteCount() method.
> + When ** does not reuse Bitmap**, getByteCount() and getAllocationByteCount return the same result. When decoding a picture by multiplexing Bitmap**, then getByteCount() indicates the size of the memory occupied by the newly decoded picture, and getAllocationByteCount() indicates the size of the memory occupied by the multiplexed Bitmap.

#### 4, cache update strategy in Android?
> + Reference answer:
> + Android's cache update policy** There is no uniform standard**. Generally speaking, the cache policy mainly includes three types of operations: add, get, and delete caches, but whether it is memory cache or storage device cache. Their cache capacity is limited, so delete some old caches and add new caches. How to define cached old and new is a strategy, ** different strategies correspond to different cache algorithms**
> + For example, you can simply define the old and new cache according to the last modification time of the file. When the cache is full, the cache with the last modification time is removed. This is a caching algorithm, but it is not perfect.

#### 5, the principle of LRU?
> + Reference answer:
> + To reduce traffic consumption, a caching strategy can be used. The commonly used caching algorithm is LRU (Least Recently Used): when the cache is full, the cached objects that have been used the least recently are prioritized. There are two main ways:
> + **LruCache (memory cache)**: The LruCache class is a thread-safe generic class: internally uses a LinkedHashMap to store external cache objects in a strongly referenced manner, and provides get and put methods to complete cache acquisition and The add operation removes the cached object that was used earlier when the cache is full, and then adds a new cached object.
> + **DiskLruCache**: Cache effect by writing cached objects to the file system

### Performance Optimization
#### 1. In the three-level cache of the picture, the picture is loaded into the memory. What happens if the memory bursts quickly? How to deal with it?
> + Reference answer:
> + First we need to know how the image's level 3 cache is
![](https://user-gold-cdn.xitu.io/2019/3/19/169956a4db82e9f3?w=1319&h=529&f=png&s=9441)
Do not recycle if there is enough memory. Reclaim soft reference objects when there is not enough memory

#### 2. If you load a 500*500 png HD image in memory, how much memory should you use?
> + Reference answer:
> + **Do not consider the screen ratio**: Memory usage = 500 * 500 * 4 = 1000000B ≈ 0.95MB
> + **Consider screen ratio**: Occupied memory = Width pixel x (inTargetDensity / inDensity) x Height pixel x (inTargetDensity / inDensity)x Memory byte size occupied by one pixel
> + inDensity indicates the dpi of the target image (under which resource folder), inTargetDensity indicates the dpi of the target screen
![](https://user-gold-cdn.xitu.io/2019/3/19/169957db5956a922?w=239&h=277&f=png&s=11171)

#### 3, WebView performance optimization?
> + Reference answer:
> + In the process of loading web pages, native, network, back-end processing, CPU will participate, each has the necessary work and dependencies; let them process in parallel rather than block each other to make the page load faster:
> + **WebView initialization is slow, you can request data at the same time of initialization, so that the backend and network do not idle. **
> + **Common JS localization and lazy loading, using a third-party browsing kernel**
> + ** The backend processing is slow, allowing the server to split the trunk output. At the same time as the backend computing, the front end also loads the network static resources. **
> + ** The script executes slowly, letting the script run at the end without blocking page parsing. **
> + ** At the same time, reasonable preloading and pre-caching can make the bottleneck of loading speed smaller. **
> + **WebView initialization is slow, you can initialize a WebView to be used at any time. **
> + **DNS and links are slow, find ways to reuse the domain names and links used by the client. **
![WebView startup time](https://user-gold-cdn.xitu.io/2019/3/20/16998e3a43726040?w=1534&h=504&f=png&s=28905)
> + Recommended articles:
> + [WebView Performance, Experience Analysis and Optimization] (https://tech.meituan.com/2017/06/09/webviewperf.html)

#### 4、How does Bitmap handle large images, such as a large 30M image, how to prevent OOM?
> + Reference Answer: To avoid OOM problems, you need to manage the loading of large images, mainly by scaling to reduce the memory footprint of the image.
> + The four types of methods (**decodeFile, decodeResource, decodeStream, decodeByteArray**) that load images provided by BitmapFactory support the BitmapFactory.Options parameter. It is convenient to sample and scale an image by using the inSampleSize parameter.
> + For example, a 1024*1024 HD picture. Then it occupies 1024*1024*4, which is 4MB. If inSampleSize is 2, then the sampled image takes up only 512*512*4, which is 1MB (**Note: According to the latest official documentation, inSampleSize The value should always be an index of 2, ie 1, 2, 4, 8, etc. If the external input is less than 2, the system will choose the index closest to 2 by default, such as 2**)
> + Comprehensive consideration. The image can be loaded efficiently by sampling rate. The flow is as follows
> + ** Set the inJustDecodeBounds parameter of BitmapFactory.Options to true and load the image**
> + **Retrieve the original width and height information of the image from BitmapFactory.Options, which correspond to the outWidth and outHeight parameters**
> + ** Calculate the sampling rate inSampleSize** according to the sampling rate rule and the required size of the target View
> + ** Set the inJustDecodeBounds parameter of BitmapFactory.Options to false and reload the image**
![](https://user-gold-cdn.xitu.io/2019/3/20/1699929ec27be435?w=2048&h=1706&f=png&s=412340)
> + Recommended articles:
> + [Android efficiently load large image, multi-image solution, effectively avoid program OOM] (https://blog.csdn.net/guolin_blog/article/details/9316683)

#### 5, memory recovery mechanism and GC algorithm (the advantages and disadvantages of various algorithms and application scenarios); GC principle timing and GC objects
> + Reference answer:
> + There are two mechanisms for memory decision objects to be reclaimable:
> + **Reference Counting Algorithm**: Add a reference counter to the object. Whenever there is a place to reference it, the counter value is incremented by one; when the reference fails, the counter value is decremented by one; the counter is zero at any time. The object is no longer usable. However, in the mainstream Java virtual machine, the reference counting algorithm is not used to manage memory. The main reason is that it is difficult to solve the problem of mutual circular reference between objects, so another object survival judgment algorithm has emerged.
> + **reachability analysis**: Through a series of objects called "GCRoots" as the starting point, search down from these nodes, the searched path is called *** reference chain ** When an object is connected to the GC Roots without any reference chain, it proves that the object is not available. It can be used as an object of GC Roots: the object referenced in the virtual machine stack, mainly refers to the **local variable in the stack frame, the object referenced by the **Native** method in the local method stack, and the ** class in the method area. Static ** attribute referenced object, method area ** constant ** referenced object
> + GC recovery algorithms are as follows:
> + **Generation Collection Algorithm**: It is an algorithm used by current commercial virtual machines. According to the different life cycle of the object, the Java heap is divided into new generation and old age, and the most appropriate according to the characteristics of each age. Collection algorithm.
> + New generation: A large number of objects die, only a small number survive. With the "copy algorithm", you only need to copy a small number of surviving objects.
> + **Copy Algorithm**: Divide the available memory into two equal-sized blocks, using only one of them at a time. When this piece of memory is used up, "copy" the still surviving object to another piece, and then clean up this piece of memory space once. ** Easy to implement and efficient to run. When the object survival rate is high, more copying operations are required, and the efficiency will be lower.
> + Old age: The object has a high survival rate. Use "Mark-Clean Algorithm" or "Mark-Finish Algorithm" to mark fewer recycle objects.
> + **Marker-Clear Algorithm**: First mark all objects that need to be reclaimed, and then "clear" all marked objects. **The process of marking and clearing is not efficient. After cleaning, it will generate a large amount of non-contiguous memory fragmentation. Too much space fragmentation may result in insufficient continuity when a large object needs to be allocated in the future. Memory has to trigger another garbage collection action in advance. **
> + **Marking-Organizing Algorithm**: First, "mark" all the objects that need to be recycled, and then "finish" the objects so that the surviving objects move to one end, and finally clear the memory outside the end boundaries. The **markup algorithm moves all surviving objects to one end and processes non-surviving objects so they don't produce memory fragmentation**
> + Recommended article: [Illustration Java garbage collection mechanism] (https://blog.csdn.net/justloveyou_/article/details/71216049)

#### 6, the difference between memory leaks and memory overflow? What tools does AS have to detect memory leaks?
> + Reference answer:
> + **Out of memory**: means that when the program is applying for memory, there is not enough memory space for it to use, out of memory; for example, an integer is applied, but it is stored in long to save it. The next number is the memory overflow.
> + **memory leak**: refers to the program can not release the requested memory space after applying for memory, a memory leak hazard can be ignored, but the memory leak accumulation is very serious, no matter how much memory, sooner or later Being occupied by light. **memory leak will eventually lead to out of memory! **
> + Find memory leaks can use the **AndroidProfiler** tool or **MAT** that comes with Android Studio

#### 7, performance optimization, how to ensure that the application does not start up? How to deal with black and white screen?
> + Reference answer:
> + **Application startup speed**, depending on what you did when you were in the application, for example, you integrated a lot of sdk, and the sdk init operation needs to be implemented in the main thread so there will be a feeling of stuttering. Delay the load or open the child thread if it is not necessary
> + In addition, the two major factors affecting the interface ** are ** interface drawing and data processing. **
> + Layout optimization (using include, merge tags, complex layouts recommended using ConstraintLayout, etc.)
> + OnCreate() does not perform time-consuming operations. Subdivide the View displayed on the page and display it in the AsyncTask step by step. It is better to use Handler. In this way, the user sees the display of the Views with layers and steps. It is not the first to see a black screen, then display all the views at once. It's best to make an animation and the effect is more natural.
> + The goal of using multithreading is to reduce the time of onCreate() and onReume() as much as possible so that users can see the page and manipulate the page as soon as possible.
> + Reduce the main thread blocking time.
> + Improve the efficiency of the Adapter and AdapterView.
> + Recommended article: [Android performance optimization memory detection, Catton optimization, power optimization, APK slimming] (https://blog.csdn.net/csdn_aiyang/article/details/74989318)
> + ** Black and white screen causes **: When we start an application, the system will check if there is such a process, if it does not exist, the system service will first check the information of the intent in the startActivity, and then go Create a process, and finally start Acitivy, which is a cold start. The problem of starting a white screen is the one that occurred during this time. Before the system loads the layout, the system first initializes the window. In this step, the system will specify its theme theme color according to the theme we set. The setting in Style determines the display. Is it a white screen or a black screen?
> + windowIsTranslucent and windowNoTitle, set both properties to true (there will be a significant Caton experience, not recommended)
> + If the startup page is just a picture, set a new theme for the startup page, set the theme's android:windowBackground property to the startup page background image.
> + Use the layer-list to make a picture launcher_layer.xml, set it to the background of the launch page-specific theme, and set it to the background of the launch page layout.
> + Recommended articles:
> + [Android startup page solution Raiders] (https://blog.csdn.net/zivensonice/article/details/51691136)

#### 8. Strong reference is set to null, will it be recycled?
> + Reference answer:
> + ** will not immediately release the memory occupied by the object. ** If the object's reference is set to null, it simply breaks the reference relationship to the object in the current thread stack frame, and the garbage collector is the thread running in the background, only when the user thread runs to a safe point Or the security zone will scan the object reference relationship. If the object is not referenced, the object will be marked. At this time, the object memory will not be released immediately, because some objects are recoverable (recovering the reference in the finalize method). The object memory is cleared only when it is determined that the object cannot recover the reference.

#### 9, the difference between ListView and RecyclerView
> + Reference answer:
> + Animation difference:
> + In **RecyclerView**, there are many animation APIs built in, such as: notifyItemChanged(), notifyDataInserted(), notifyItemMoved(), etc. If you need to customize the animation effect, you can do it by implementing (RecyclerView.ItemAnimator class) Define the animation effect, then call RecyclerView.setItemAnimator();
> + But **ListView** does not implement animation effects, but we can implement the animation of the item in the Adapter itself;
> + Refresh the difference:
> + ListView usually refreshes the data with global refresh notifyDataSetChanged (), which will consume resources very much; ** can not achieve partial refresh **, but if you want to achieve partial refresh in the ListView **, still can Realized, when an item data is refreshed, we can implement an onItemChanged() method in the Adapter, get the position of the item in the method (you can use getFirstVisiblePosition()), and then call the getView() method to refresh the item. The data;
> + RecyclerView can achieve partial refresh, for example: notifyItemChanged();
> + Cache difference:
> + RecyclerView has two levels of cache than ListView, supports multiple items from the ItemView cache, supports developers to customize the cache processing logic, and supports all RecyclerView sharing the same RecyclerViewPool (cache pool).
> + ListView and RecyclerView caching mechanisms are basically the same, but the cache is used differently
> + Recommended articles:
> + [[Tencent Bugly dry goods sharing] Android ListView and RecyclerView comparison analysis - caching mechanism] (https://zhuanlan.zhihu.com/p/23339185)
> + [Comparative comparison of ListView and RecyclerView] (https://blog.csdn.net/shu_lance/article/details/79566189)
> + [Android development: ListView, AdapterView, RecyclerView comprehensive analysis] (https://www.jianshu.com/p/4e8e4fd13cf7)

#### 10, what is the adapter of the ListView adapter?
> + Reference answer:
![](https://user-gold-cdn.xitu.io/2019/3/20/1699a79b39f0c556?w=658&h=546&f=png&s=31636)
> + **BaseAdapter**: Abstract class, in actual development we will inherit this class and rewrite related methods, the most used adapter!
> + **ArrayAdapter**: Supports generic operations, the simplest one, can only display one line of text ~
> + **SimpleAdapter**: An adapter that also has good scalability, you can customize a variety of effects!
> + **SimpleCursorAdapter**: listView for displaying simple text types, generally used in the database, but a bit outdated, not recommended!

#### 11, LinearLayout, FrameLayout, RelativeLayout performance comparison, why?
> + Reference answer:
> + RelativeLayout will make the child View call 2 times onMeasure, LinearLayout will also call the child View 2 times onMeasure when there is weight
> + RelativeLayout's child View will cause efficiency problems if the height is different from the RelativeLayout. This problem will be more serious when the child view is complex. If possible, try to use padding instead of margin.
> + Use LinearLayout and FrameLayout instead of RelativeLayout without affecting the depth of the hierarchy.

### JNI

#### 1. Do you know about JNI?
> + Reference answer:
> + The advantage of Java is ** cross-platform**, but because of its cross-platform features, its ability to interact locally is not strong enough**, some operating system-related features of Java can not be completed, so ** Java provides JNI specifically for interacting with native code. With JNI, users can call native code written in C and C++**
> + NDK is a collection of tools provided by Android. It is easier to access native code through JNI in Android through NDK.
> + Improve the security of your code. NDK improves the security of Android programs due to difficulty in decompilation of so libraries
> + It is very convenient to use the existing C/C++ open source library
> + Easy to port the platform. Dynamic libraries implemented in C/C++ can be easily used on other platforms
> + Improve the efficiency of the program in certain situations, but it does not significantly improve the performance of Android programs

#### 2. How to load the NDK library? How to register Native functions in JNI, how many registration methods?
> + Reference answer:
```
     Public class JniTest{
         / / Load the NDK library
         Static{
             System.loadLirary("jni-test");
         }
     }

```
> + Two ways to register JNI functions
> + **Static method**
> + **Dynamic Registration**
> + Recommended articles:
> + [Two ways to register JNI functions] (https://blog.csdn.net/wwj_748/article/details/52347341)
> + [Android JNI articles - Getting Started to Abandon] (https://www.jianshu.com/p/3dab1be3b9a4)

#### 3. What features have you implemented with JNI? How to achieve it? (encryption processing, audio and video, graphics and image processing)
> + Reference answer:
> + Recommended articles:
> + [Android JNI articles - ffmpeg Get audio and video thumbnails] (https://www.jianshu.com/p/411761bd5f5b)

### Design Patterns
#### 1. What design patterns do you know?
> + Reference answer:
> + **Created mode, a total of five types: factory method mode, abstract factory mode, singleton mode, builder mode, prototype mode.
> + **Structure mode, a total of seven types**: adapter mode, decorator mode, proxy mode, appearance mode, bridge mode, combination mode, flyweight mode.
> + ** Behavioral mode, a total of eleven **: strategy mode, template method mode, observer mode, iteration sub mode, responsibility chain mode, command mode, memo
Mode, state mode, visitor mode, mediator mode, interpreter mode.

#### 2. Talk about MVC, MVP and MVVM. Where is the good place?
> + Reference answer:
> + **MVC**:
> + View layer (View) corresponds to the xml layout file and the dynamic view of the java code
> + Control layer (Controller) The control layer of Android in MVC is undertaken by Activity. Activity is mainly used as the initialization page to display data operations, but because the XML view function is too weak, the Activity is responsible for the display of the view. To join the control logic, there are too many functions.
> + Model Layer For the business model, the data structure and related classes are established. It is mainly responsible for network requests, database processing, and I/O operations.
> + Summary
> + has a certain layering, the model is completely decoupled, the controller and the view do not decouple the layer and the interaction between the layers. Try to use the callback or use the message mechanism to complete, try to avoid directly holding the controller and view in the android can not Be completely separated, but at the logic level of the code, it must be clearly stated that the business logic is placed in the model layer, which can better reuse and modify the added business.
> + **MVP**
> + By introducing the interface BaseView, the corresponding view components such as Activity, Fragment to achieve BaseView, achieve the independence of the view layer, through the middle layer Preseter to achieve the complete decoupling of Model and View. MVP** completely solves the problem that the View and Controller in MVC are unclear, but with the increase of business logic, a page may be very complicated, the UI changes are very many, there will be a lot of cases, This will cause the View interface to be very large.
> + **MVVM**
> + MVP We said that with the increase of business logic and the UI changes, there will be a lot of UI related cases, which will cause the View interface to be very large. MVVM solves this problem. Through the two-way binding mechanism, the data and UI content are realized. If you want to change one of the parties, the other party can update the design concept in time, thus saving a lot of writing in the View layer. In many cases, you only need to change the data.
> + How do you choose?
> + If the project is simple, there is no complexity, and the future changes are not big, then do not use design patterns or architectural methods, just need to package each module, easy to call, not to use design patterns or architectural methods. use.
> + For biased display apps, most of the business logic is on the back end. The main function of the app is to display data, interaction, etc. It is recommended to use mvvm.
> + For tool classes or to write a lot of business logic app, use mvp or mvvm.
> + Recommended articles:
> + [MVC, MVP, MVVM, how should I choose? ](https://juejin.im/post/5b3a3a44f265da630e27a7e6)

#### 3, after the p layer is encapsulated. If the p layer data is too large, how to solve it?
> + Reference answer:
> + For the MVP mode, if the data layer logic is too bloated, it is recommended to introduce **RxJava** or **Dagger**. The more complex the logic, the better the superiority of RxJava.
> + Recommended articles:
> + [(Improved class) RxJava+OkHttp+Retrofit+Dagger2+MVP framework (kotlin version)](https://juejin.im/post/5c6e601cf265da2dc675b69e#comment)

#### 4, Can you give a few examples from Android to talk about what design pattern is used?
> + Reference answer:
> + **AlertDialog, Notification source code used Builder (builder) mode to complete parameter initialization**
> + **Okhttp internally uses the Chain of Responsibility mode to complete the call of each Interceptor interceptor**
> + **RxJava observer mode; singleton mode; GridView adapter mode; Intent prototype mode**
> + **Basic development of BaseActivity abstract factory pattern**


#### 5, What is the difference between decorative mode and proxy mode?
> + Reference answer:
> + The difference between decorator mode and proxy mode is that
> + Both are **extensions on the methods of the class**, but the **decorator mode** emphasizes the enhancement itself, and after being decorated you can use the enhanced functionality on the enhanced class.
> + **Proxy mode ** emphasizes that you want others to help you to do some responsibilities that do not have much to do with your business (logging, setting the cache). The proxy mode is to achieve object control, because the object being proxied It is often difficult to obtain directly or not to be exposed inside.

#### 6. How many ways to implement singleton mode? What is the purpose of the double-layer lock in the lazy style? What is the purpose of the two judgments?
> + Reference answer:
> + Singleton mode implementation methods are available: ** hungry, lazy (thread safe, thread non-secure), double check (DCL), inner class, and enumeration**
> + so-called ** double-layer test lock (testing the instance object twice before and after locking)**: Locking is for the first time the object is instantiated, and there is a second in the lock. The layer is empty because there may be multiple threads entering the first layer if the internal judgment, and waiting outside the lock code block, if the lock does not perform the second test, there will still be cases of instantiating multiple objects.
> + Recommended articles:
> + [Summary of Singleton Mode] (https://xxxblank.github.io/2017/09/14/singleTon/)

#### 7, some open source frameworks used, introduce a source code, internal implementation process.
> + Reference answer:
> + Interview frequent visitors: Okhttp, Retrofit, Glide, RxJava, GreenDao, Dagger, etc.
> + Recommended articles:
> + [Android OkHttp source code analysis tutorial (1)] (https://juejin.im/post/5c46822c6fb9a049ea394510)
> + [Android OkHttp source code analysis tutorial (2)] (https://juejin.im/post/5c4682d2f265da6130752a1d)

#### 8. How should Fragment be decoupled if used in Adapter?
> + Reference answer:
> + interface callback
> + broadcast

### Android advanced extension point

#### 1. How to perform unit testing, how to ensure the stability of the app?
> + Reference answer:
> + To test Android apps, the following types of automated unit tests are usually created
> + **Local Test**: Run only on the local machine JVM to minimize execution time. This unit test does not depend on the Android framework, or even if there is a dependency, it is convenient to use the simulation framework to simulate dependencies to achieve Isolating the purpose of Android dependencies, simulating frameworks such as Google's recommended Mockito;
> + [**Android official website - build local unit test**] (https://developer.android.com/training/testing/unit-testing/local-unit-tests.html)
> + **Test Test**: Unit test running on real machine or simulator, because it needs to run to the device, it is slow, these tests can access the instrument (Android system) information, such as the context of the application under test, generally Ground, it is not convenient to use this method when simulating through the simulation framework;
> + [**Android official website - build meter unit test**] (https://developer.android.com/training/testing/unit-testing/instrumented-unit-tests.html)
> + Note: Unit tests are not suitable for testing complex UI interaction events
> + Recommended article: [Android unit test only see this one is enough] (https://juejin.im/post/5b57e3fbf265da0f47352618)
> + App stability is mainly determined by the overall system architecture design, and can not ignore the detailed specification of code programming, the so-called "thousands of embankments, collapsed in the ant hole", once considered poorly, seemingly insignificant code fragments may Will bring the collapse of the overall software system, so in addition to their own ** localization test** before going online, you need to do **Monkey stress test**
> + A small number of interviewers may be extended, such as Gradle automated testing, model adaptation testing, etc.
    
#### 2. How to view the recycling status of an object in Android?
> + Reference answer:
> + First understand the scenarios and uses of the four reference types of Java (strong references, soft references, weak references, virtual references)
> + Give a scenario example: **SoftReference** object is used to save soft references, but it is also a Java object, so when the soft reference object is recycled, although the get method of this **SoftReference** object returns Null, but the **SoftReference** object itself is not null, and at this point the **SoftReference** object no longer has the value of existence, requires an appropriate cleanup mechanism, avoiding a large number of **SoftReference** objects **Memory leak**
> + Therefore, Java provides **ReferenceQueue** to handle the recycling of referenced objects. When the object referenced by **SoftReference** is GC, **JVM** will first add the **softReference** object to the **ReferenceQueue** queue. When we call the poll() method of the **ReferenceQueue**, if the queue is not an empty queue, then the Reference object added earlier will be returned and removed.
![](https://user-gold-cdn.xitu.io/2019/3/27/169bd0c28741e8e0?w=1950&h=1058&f=png&s=262083)
> + Recommended articles:
> + [Four reference types in Java: strong references, soft references, weak references, and virtual references] (https://segmentfault.com/a/1190000015282652#articleHeader3)

#### 3. How is the size of Apk compressed?
> + Reference answer:
> + A full APK contains the following directories (drag APK files to Android Studio):
> + **META-INF/**: Contains the **CERT.SF** and **CERT.RSA** signature files and the **MANIFEST.MF** manifest file.
> + **assets/**: Contains application resources that the app can retrieve using the **AssetManager** object.
> + **res/**: Contains uncompiled resources resources.arsc.
> + **lib/**: Contains compiled code specific to the processor software layer. This directory contains subdirectories for each platform, such as **armeabi, armeabi-v7a, arm64-v8a, x86, x86_64, and mips**.
> + **resources.arsc**: Contains compiled resources. This file contains the XML content in all configurations of the **res/values/** folder. The packaging tool extracts this XML content, compiles it into a binary format, and archives the content. This content includes language strings and styles, as well as content paths that are directly included in the **resources.arsc*8 file, such as layout files and images.
> + **classes.dex**: Contains classes compiled in the **DEX** file format understandable by the **Dalvik / ART** virtual machine.
> + **AndroidManifest.xml**: Contains the core Android manifest file. This file lists the application's name, version, access rights, and referenced library files. This file uses Android's binary XML format.
![](https://user-gold-cdn.xitu.io/2019/3/27/169bd48e53be9928?w=1878&h=415&f=png&s=52339)
> + lib, class.dex and res take up more than 90% of the space, so these three are the focus of optimizing the Apk size (the actual situation is not unique)
> + ** Reduce res, compress graphic files**
> + Image file compression is for jpg and png images. We usually place multiple sets of images of different resolutions to fit different screens, and we can make appropriate cuts here. In actual use, it is enough to keep only one or two sets (if you keep one set, it is recommended to keep xxhdpi, if you add two sets, add hdpi), then compress the remaining pictures (jpg is optimized with MAP). Pngquant compression)
> + **Reduce dex file size**
> + Add resource confusion
![](https://user-gold-cdn.xitu.io/2019/3/27/169be5f6fe340e53?w=566&h=141&f=png&s=9307)
> + shrinkResources is true to remove unreferenced resources and work with code compression.
> + minifyEnabled to true means that code compression is enabled through ProGuard, confusing the code with the configuration of proguardFiles and removing unused code.
> + Code confusing improves security while compressing apk.
> + Recommended article: [Android Confusion Best Practices] (https://www.jianshu.com/p/cba8ca7fc36d)
> + **Reduce lib file size**
> + Since many third-party libraries are referenced, the lib folder usually takes up a lot of space, especially if there are so libraries. Many so libraries will introduce armeabi, armeabi-v7a and x86 at the same time. Here you can only keep one of armeabi or armeabi-v7a. In fact, mainstream apps such as WeChat do this.
> + Just configure directly in build.gradle, NDK configuration is the same
![](https://user-gold-cdn.xitu.io/2019/3/27/169be64df0148df3?w=219&h=95&f=png&s=2712)
> + Recommended articles:
> + [APK Slimming] (https://www.jianshu.com/p/5921e9561f5f)

#### 4. How to configure multi-channel package through Gradle?
> + Reference answer:
> + The first step is to understand the reasons for setting up multiple channels. Add different identifiers in the installation package, cooperate with the automatic burying point, and the application carries the channel information when requesting the network, which is convenient for the background to do operational statistics, for example, statistics on the download amount of our application in different application markets.
> + Here is an example of Union League statistics
> + First set the dynamic channel variable in the manifest.xml file:
![](https://user-gold-cdn.xitu.io/2019/3/28/169c30d0cdbfb111?w=486&h=103&f=png&s=7268)
> + Then configure productFlavors in the build.gradle in the app directory, which is to configure the packaged channels:
![](https://user-gold-cdn.xitu.io/2019/3/28/169c31116ef61848?w=661&h=356&f=png&s=41701)
> + Finally the Teminal output command line below the editor
> + **Execute ./gradlew assembleRelease and will release the release package for all channels;**
> + **Execute ./gradlew assembleVIVO, which will release the release and debug packages of the VIVO channel;**
> + **Execute ./gradlew assembleVIVORelease will generate the VIVO release package. **
> + Recommended articles:
> + [Meituan Android Automation Tour - Walle Generation Channel Pack] (https://github.com/Meituan-Dianping/walle)

#### 5, plug-in principle analysis
> + Reference answer:
> + **Plug-in ** refers to the section that divides the APK into **host** and **plugins**. Think of the module or function that needs to be implemented as a separate extraction. When the APP is running, we can dynamically load or replace the plugin** part to reduce the size of the host**.
> + Host: This is the currently running APP.
> + Plugins: Relative to plugin technology, it is to load the running apk class files.
> + and **Hot Fix** is based on fixing bugs, emphasizing the repair of known bugs without the need to install the application twice. can
![](https://user-gold-cdn.xitu.io/2019/4/7/169f6c21ae14fee0?w=1208&h=1134&f=jpeg&s=321789)
> + ** class loading mechanism**
> + Two kinds of class loaders commonly used in Android, **DexClassLoader** and **PathClassLoader**, they all inherit from **BaseDexClassLoader**, the difference between the two is that **PathClassLoader** can only be loaded* * Internal storage directory ** dex / jar / apk file. **DexClassLoader** supports loading **specified directory** (not limited to internal) dex/jar/apk file
> + **Plug-in communication**: You can access the classes by generating the corresponding DexClassLoader for the plug-in apk, which can be divided into single DexClassLoader and multiple DexClassLoader structures.
> + If you use the **Multiple ClassLoader mechanism**, the main project reference plugin class needs to first load the class through the plugin's ClassLoader and then call its method via **reflection**. Plug-in frameworks generally manage access to classes in individual plug-ins through a unified portal and impose certain restrictions.
> + If you use the **Single ClassLoader mechanism**, the main project can ** access the classes in the plugin directly through the ** class name. This approach has the drawback. If two different plug-in projects reference different versions of a library, the program may go wrong.
> + **Resource loading**
> + The principle is to add the path of the plugin apk to the AssetManager and create the Resource object to load the resource by reflection. There are two ways to handle it:
> + Consolidation: addAssetPath adds all plugins and main project paths; since the AssetManager adds all plugins and main project paths, the generated Resource can access both the plugin and the main project resources. However, since the main project and each plug-in are compiled independently, the generated resource id will have the same situation, and a resource conflict will occur during the access.
> + Stand-alone: ​​Each plug-in only adds its own apk path. The resources of each plug-in are isolated from each other, but if you want to share resources, you must get the corresponding Resource object.
> + Recommended articles:
> + [Android dynamic loading technology easy to understand introduction] (https://segmentfault.com/a/1190000004062866#articleHeader1)
> + [In-depth understanding of Android plug-in technology] (https://yq.aliyun.com/articles/361233?utm_content=m_40296)
> + [Why do hot updates] (https://www.cnblogs.com/baiqiantao/p/9160806.html)

#### 6, componentization principle
> + Reference answer:
> + ** Reasons for introducing componentization**: Projects become larger and larger as demand increases, and the increase in scale leads to complex intertwining of various business errors, between each business module. The code is not constrained, causing the blurring of the code boundaries, code conflicts sometimes occur, changing a small problem may cause some new problems, pulling the whole body, adding a new requirement, need to be familiar with the relevant code logic, increase development time
> + ** Avoid re-creating wheels and save development and maintenance costs. **
> + ** It is possible to rationally arrange manpower for business benchmarks through components and modules to improve development efficiency. **
> + **Different projects can share a single component or module to ensure uniformity of the overall technical solution. **
> + ** Prepare for the future plug-in to share the same set of underlying models. **
> + **Componentized development process** is to split a fully functional App or module into **Multiple Modules**, each submodule can be compiled and run **, or can be arbitrarily combined Another new app or module, each module does not depend on each other but can interact with each other, but when it is finally released, these components are combined into one apk, and in some special cases, it can even be upgraded** or **downgrade**
> + Give a simple model example
![APP Architecture Diagram] (https://user-gold-cdn.xitu.io/2019/4/7/169f692c69f60c06?w=932&h=806&f=png&s=39959)
![APP code structure diagram] (https://user-gold-cdn.xitu.io/2019/4/7/169f690af8eea279?w=716&h=750&f=png&s=214708)
App is the main application, ModuleA and ModuleB are two business modules (** relatively independent, do not affect each other **), Library is the basic module, contains the dependent libraries required by all modules, and some tool classes: such as network access, time tools Wait
> + **Note: The basic components provided to each business module need to be split into aar or library according to the specific situation. For example, the more stable components such as login and basic network layer are generally packaged directly into aar, which reduces the compilation time. And like the custom View component, because there will be more changes with the iteration of the version, it will be directly separated into the Library by source code**
> + Recommended articles:
> + [Dry goods | See the component architecture practice from the Zhixing Android project] (https://mp.weixin.qq.com/s?__biz=MjM5MDI3MjA5MQ==&mid=2697268363&idx=1&sn=3db2dce36a912936961c671dd1f71c78&scene=21#wechat_redirect)


#### 7, cross-component communication
> + Reference answer:
> + **Cross-component communication scenario:**
> + The first is page jumps between components (Activity to Activity, Fragment to Fragment, Activity to Fragment, Fragment to Activity) and data transfer at jump (basic data types and serializable custom class types) ).
> + The second is a call between a custom class and a custom method between components (the component provides services out).
> + ** Cross-component communication scenario analysis**:
> + The page jump between the first ** components ** is simple to implement, you want to pass different types of data when you provide a corresponding API.
> + The custom class between the second component and the call to the **custom method** are slightly more complicated and require the ARouter to work with the public service (CommonService) in the schema:
> + Business module for providing services:
> + Declare the Service interface (containing the custom method that needs to be called) in the public service (CommonService), then implement the Service interface in its own module, and expose the implementation class through the ARouter API.
> + Business module using the service:
> + Get the Service interface (polymorphic hold, actually hold the implementation class) through the ARouter API, you can call the custom method declared in the Service interface, so that the interaction between the modules can be achieved.
> + In addition, you can use AndroidEventBus' unique tag, which makes it easier to locate the code for sending events and accepting events during development. If you group the component names as the prefix of the tag, you can better manage and view each. The events of the components, of course, do not recommend excessive use of EventBus.
> + ** How to manage too many routing tables? **
> + RouterHub exists in the base library and can be seen as a communication protocol that all components need to comply with. It can not only put routing address constants, but also various Key values ​​named when passing data across components, with appropriate comments. Any component developer does not need to communicate in advance. As long as they rely on this protocol, they know how to work together, which not only improves efficiency but also reduces the risk of error. The agreed things are naturally stronger than verbal.
> + Tips: If you feel that it is too much trouble to write each routing address in the RouterHub of the base library, you can also create a private RouterHub inside each component, which will not need to put the routing address across components into the private RouterHub. Management, only the routing addresses that need to be cross-components are placed in the public RouterHub of the base repository. This is also a recommended method if you do not need to centrally manage all routing addresses.
> + **ARouter routing principle: **
> + ARouter maintains a routing table Warehouse, which holds all the module jump relationships. The ARouter route jump actually calls the startActivity jump. It uses the native Framework mechanism, but only creates the jump by apt annotation. Transfer rules and artificially intercept jumps and set jump conditions.
> + Common componentization schemes are as follows
![](https://user-gold-cdn.xitu.io/2019/4/7/169f6a3d472ef431?w=1828&h=945&f=png&s=615437)

#### 8. Implementation of routing and burying points in componentization
> + Reference answer:
> + Because in the componentization, each business module is independent of each other, and there is no interdependent relationship, so a business module can not access the code of other business modules, if you want to from A business The A page of the module jumps to the B page of the B business module, which cannot be implemented by the module itself. This requires a cross-component communication scheme - **Router **
> + **Route** There are two main scenarios:
> + The first is ** page jump between components ** (Activity to Activity, Fragment to Fragment, Activity to Fragment, Fragment to Activity) and data transfer at jump (basic data types and serializable Custom class type)
> + The second is the call between the custom class ** and ** custom methods between the components** (the component provides services out)
> + Its principle ** is to generate a mapping table according to certain rules of some classes distributed in different component modules (data structure is usually Map, Key is a string, Value is a class or object), and then needed When you get the class or object from the mapping table according to the string from the mapping table, it is essentially the class lookup.
> + Buried points are collected in the specific process of the application to track the status of the application.
> + ** Code Buried Point**: When a certain event occurs, the corresponding interface in the SDK is called to send the buried point data. Baidu Statistics, Union, TalkingData, Sensors Analytics and other third-party data statistics service providers mostly adopt this scheme.
> + ** Full Buried Point**: Full Buried Point refers to all the behaviors that are generated in the Web Page/App and satisfy certain conditions, all reported to the background server.
> + **Visualization Buried Point**: Configure the collection node through a visualization tool (such as Mixpanel), automatically parse the configuration on the Android side and report the buried point data to achieve the so-called ** automatic burying point**
> + **No burying point**: It is not really no need to bury the point, but the Android side automatically collects all events and reports buried data, filtering out useful data in the back-end data calculation.
> + Recommended articles:
> + [Android componentized open source solution implementation] (https://juejin.im/post/5a7ab8846fb9a0634514a2f5)

#### 9, Hook and instrumentation
> + Reference answer:
> + **Hook** is a technique for **changing API execution results**, able to perform system **redirect** (application's **trigger event** and **background logic) Processing** is performed step by step according to the event flow. **Hook** means intercepting and monitoring the transmission of events before the event is delivered to the destination, like a hook hook event, and can be hooked When dealing with events, handle some specific events of your own, such as reverse cracking the app)
![](https://user-gold-cdn.xitu.io/2019/4/17/16a2a2f8cf3e4448?w=625&h=211&f=png&s=14871)
> + Hook mechanism in Android, there are roughly two ways:
> + For root privileges, direct Hook system, you can kill all apps.
> + No root privileges, but only Hook own app, can't do anything for other apps in the system.
> + **Insert ** is to modify the third-party code in a static way, that is, compile the source code (intermediate code) from the compile stage, and then repackage it, it is ** static tampering**; and * *Hook** does not need to recompile the third-party source code or intermediate code in the compile stage. It is a modification of the call by reflection at runtime, which is a kind of **dynamic tampering**
> + Recommended articles:
> + [Android plug-in principle analysis - dynamic proxy of Hook mechanism] (http://weishu.me/2016/01/28/understand-plugin-framework-proxy-hook/)
> + [android plugging basic concept] (https://blog.csdn.net/fei20121106/article/details/51879047)
> + [Android Reverse Journey] (http://www.520monkey.com/)

#### 10, Android's signature mechanism?
> + Reference answer:
> + Android's signature mechanism includes **Message Summary**, **Digital Signature** and **Digital Certificate**
> + **Message Summary**: On the message data, execute a one-way Hash function to generate a fixed-length hash value
> + **Digital Signature**: A method of storing message signatures in electronic form. A complete digital signature scheme should consist of two parts: **Signature Algorithm and Verification Algorithm**
> + **Digital Certificate**: A digitally signed document containing the public key owner information and the public key signed by the Certificate Authentication Center
> + Recommended articles:
> + [An article to understand Android v1 & v2 signature mechanism] (https://blog.csdn.net/freekiteyu/article/details/84849651)

#### 11, What is the difference between v3 signature key and v2 and v1?
> + Reference answer:
> + In the signature of **v1 version**, the signature exists in the apk package as a file. This version of the apk package is a standard zip package. The difference between **V2** and **V1** is **V2** is to sign the entire zip package, and a **apk signature block** is added to the zip package, which stores the signature information.
![](https://user-gold-cdn.xitu.io/2019/4/1/169d7c3ea2437de3?w=1692&h=520&f=png&s=77618)
> + **v2 version ** Signing Block itself is mainly divided into three parts:
> + **SignerData** (Signer Data): mainly includes the signer's certificate, the entire APK integrity check hash, and some necessary information
> + **Signature** (Signature): Developer's signature data for the SignerData part of the data
> + **PublicKey** (public key): public key data for verification
> + **v3 version** The signature block is also divided into the same three parts. Unlike v2, in the SignerData section, v3 adds the attr block, which consists of smaller level blocks. A certificate information can be stored in each level block. The previous level block certificate verifies the next level certificate, and so on. The certificate of the last level block must conform to the certificate in SignerData itself, that is, the certificate to which the public key used to sign the entire APK belongs.
![](https://user-gold-cdn.xitu.io/2019/4/1/169d7c5908d3c159?w=1643&h=1790&f=png&s=101056)
> + Recommended articles:
> + [APK Signature Scheme v3] (https://source.android.google.com/security/apksigning/v3)
> + [Android P v3 Signature New Features] (https://xuanxuanblingbling.github.io/ctf/android/2018/12/30/signature/)

#### 12, big change between Android5.0~10.0
> + Reference answer:
> + **Android5.0 new features**
> + **MaterialDesign design style**
> + **Support for 64-bit ART virtual machine** (5.0 launched ART virtual machine, Dalvik before 5.0. The difference is: Dalvik, every time you run, bytecode needs to be converted to machine by just-in-time compiler Code (JIT). ART, when the application is first installed, the bytecode is pre-compiled into machine code (AOT).
> + Notification details can be designed by users themselves
> + **Android6.0 new features**
> + **Dynamic Rights Management**
> + Support fast charging switching
> + Support folder drag and drop application
> + Camera added professional mode
> + **Android7.0 new features**
> + **Multi-window support**
> + **V2 Signature**
> + Enhanced Java8 language mode
> + night mode
> + **Android8.0(O) new features**
> + **Optimization Notification**: Notification Channel Notification Flag Sleep Notification Timeout Notification Settings Notification Clear
> + **Picture in Picture Mode**: Activity settings in the list android:supportsPictureInPicture
> + **Background restrictions**
> + Auto Fill Frame
> + System Optimization
> + etc. Optimize a lot
> + **Android9.0(P) new features**
> + **Indoor WIFI positioning**
> + ** "Liu Hai" screen support**
> + Security Enhancement
> + etc. Optimize a lot
> + **Android10.0(Q) new features**
> + **Night mode**: All applications on your phone can be set to Diagnostics mode.
> + **Desktop Mode**: Provides a PC-like experience, but is far from a replacement for a PC.
> + **Screen Recording**: Turns on by long pressing "Screenshot" in the "Power" menu.
> + Recommended articles:
> + [Android Developers Official Documentation] (https://developer.android.com/guide/topics/manifest/uses-sdk-element.html#ApiLevels)

#### 13, say Measurepec this class
> + Reference answer:
> + Function: Determine the size of the View by the wide measurement value **widthMeasureSpec** and the high measurement value**heightMeasureSpec**
> + Composition: A 32-bit int value, the upper 2 bits represent **SpecMode** (measurement mode), and the lower 30 bits represent **SpecSize** (size in a certain measurement mode).
> + Three modes:
> + **UNSPECIFIED**: The parent container does not have any restrictions on View, how big it is. Often used inside the system.
> + **EXACTLY** (Precision mode): The parent view specifies an exact size SpecSize for the child view. Corresponds to match_parent or specific value in LyaoutParams.
> + **AT_MOST** (maximum mode): The parent container specifies a maximum size SpecSize for the child view, and the size of the View cannot be greater than this value. Corresponds to wrap_content in LayoutParams.
> + Decisive factor: The value is determined by the layout parameter LayoutParams** of the ** child View and the **MeasureSpec** value of the parent container. The specific rules are as follows: ![](https://user-gold-cdn.xitu.io/2019/4/1/169d7a649cc67de5?w=700&h=230&f=png&s=60337)

#### 14, please list the common layout types in Android, and briefly describe their usage and layout efficiency.
> + Reference answer:
> + Common layouts in Android are divided into **traditional layout** and **new layout**
> + Traditional layout (writing XML code, code generation):
> + **FrameLayout (FrameLayout)**:
> + **LinearLayout**:
> + **AbsoluteLayout**:
> + ** Relative Layout (RelativeLayout)**:
> + **TableLayout**:
> + New layout (**Visual drag control**, writing XML code, code generation):
> + **Constrained layout (ConstrainLayout)**:
![](https://user-gold-cdn.xitu.io/2019/4/18/16a2f8e1327c53b4?w=1220&h=950&f=png&s=183074)
> + Note: The picture is from Carson_Ho [Android: Common Layout Introduction & Property Settings] (https://blog.csdn.net/carson_ho/article/details/51719519)
> + For nested multi-layer View, its layout efficiency: **LinearLayout = FrameLayout >> RelativeLayout**

#### 15. Differentiate the usage of Animation and Animator, and outline its principle.
> + Reference answer:
> + **Types of animations**: The former only has **transparency**, **rotation**, **translation**, **scale** 4 attributes, and for the latter, as long as it is the property of the control And there is a way to set the property of this property to perform a **dynamic change** effect on the property.
> + **Operable objects**: The former can only animate the **UI component**, but the property animation can animate almost any object** (regardless of whether it is displayed on the screen).
> + **Animation play order**: In Animator, AnimatorSet controls multiple animations through playTogether(), playSequentially(), animSet.play().with(), before(), after() Work together to achieve precise control of the animation playback sequence
![](https://user-gold-cdn.xitu.io/2019/4/18/16a2fb2adc635679?w=1768&h=1670&f=png&s=380046)

#### 16. What image loading library has you used? Where is Glide's source code design subtle?
> + Reference answer:
> + Image loading library: **Fresco, Glide, Picasso**, etc.
> + The design of Glide is subtle:
> + **Glide's lifecycle binding**: You can control the loading state of the image to synchronize with the life cycle of the current page, so that the entire loading process starts/resumes, stops, and destroys as the state of the page
> + **Glide's Cache Design**: Caching the Resource by (L3 Cache, Lru Algorithm, Bitmap Reuse)
> + **Glide's full loading process**: Exposing a series of methods for the Request operation using the Engine engine class
> + Recommended articles:
> + [Glide source code analysis] (https://user-gold-cdn.xitu.io/2019/4/24/16a4ec49c3af1f5c)

#### 17, how to bypass the 9.0 limit?
> + Reference answer:
![](https://user-gold-cdn.xitu.io/2019/4/19/16a33b0c703f615b?w=1267&h=542&f=png&s=67847)

#### 18. Which network loading libraries have you used? OkHttp, Retrofit implementation principle?
> + Reference answer:
> + Network loading library: OkHttp, Retrofit, xUtils, Volley, etc.
> + Recommended articles:
> + [Android OkHttp source code analysis tutorial (1)] (https://juejin.im/post/5c46822c6fb9a049ea394510)
> + [Android OkHttp source code analysis tutorial (2)] (https://juejin.im/post/5c4682d2f265da6130752a1d)

#### 19. How is this for the application update? (Grayscale, forced update, sub-region update)
> + Reference answer:
> + **Internal Update**:
> + Get the online version number through the interface, versionCode
> + Compare online versionCode and local versionCode to pop up update window
> + Download APK file (file download)
> + Install APK
> + **Grayscale update**:
> + Find a special version for a single channel.
> + Make upgrades to the upgrade platform, allowing upgrade notifications or even version upgrades for some users.
> + Open a separate download portal.
> + The two versions of the code are all in the app package, and then the test framework is embedded in the app to control which version is displayed. The test framework is responsible for communicating with the server-side API. The server-side controls the distribution of the A/B version on the app, so that a specified group of users can see the A version, and other users see the B version. The server will have a corresponding report to show the number and effect comparison of the A/B version. Finally, it can be controlled by the background of the server, and all users can switch to the A or B version online~
> + ** Either way, you need to do a version management job, assign a special version number to show the difference. Of course, since it is grayscale, data monitoring (regular data, new characteristic data, main business data) is still in place, and the data pile to be played should be played. In addition, the gray version is best to have the ability to recover, generally it is forced to upgrade the next official version. **
> + ** Forced update**:
> + The general processing is to open the application and notify the user that there is a version update. The pop-up window can be cancelled without a cancel button. In this way, the user can only choose to update or close the application. Of course, the cancel button can also be added, but if the user chooses to cancel, the application is directly exited.
> + **Incremental Update**:
> + Binary Differential Tool bsdiff is the corresponding patch synthesis tool that generates patch file .patch files based on two different versions of binary files. The old apk file is combined with the indefinite file by bspatch to synthesize a new apk. Note that the version is differentiated by the md5 value of the apk file.

#### 20. Will you use Kotlin and Fultter? Talk about your understanding
> + Reference answer:
> + Kotlin is a cross-platform, statically typed general-purpose programming language with type inference. Kotlin is designed to be fully interoperable with Java, and the JVM version of its standard library relies on Java class libraries, but type inference allows for a cleaner syntax.
> + Flutter is an open source mobile application development framework created by Google. It is used to develop Android and iOS apps, as well as the main method for creating apps for Google Fuchsia.
> + About the importance of kotlin, I believe that everyone can understand in daily development, apply to the actual development, need to avoid syntactic sugar (such as single-column mode, null judgment, higher-order functions, etc.)
> + As for Flutter, the official Google documentation is still not perfect. There are fewer projects written in this language on the market. If you need specific in-depth, please refer to the free fish and official documents.
