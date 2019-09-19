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

#### 5、使用SQLite做批量操作有什么好的方法吗？
>  + 参考回答：
>    + 使用SQLiteDatabase的beginTransaction方法开启一个事务，将批量操作SQL语句转化为SQLiteStatement并进行批量操作，结束后endTransaction()

#### 6、如何删除SQLite中表的个别字段
>  + 参考回答：
>    + SQLite数据库只允许增加字段而不允许修改和删除表字段，只能创建新表保留原有字段，删除原表

#### 7、使用SQLite时会有哪些优化操作？
>  + 参考回答：
>    + 使用事务做批量操作
>    + 及时关闭Cursor，避免内存泄露
>    + 耗时操作异步化：数据库的操作属于本地IO耗时操作，建议放入异步线程中处理
>    + ContentValues的容量调整：ContentValues内部采用HashMap来存储Key-Value数据，ContentValues初始容量为8，扩容时翻倍。因此建议对ContentValues填入的内容进行估量，设置合理的初始化容量，减少不必要的内部扩容操作
>    + 使用索引加快检索速度：对于查询操作量级较大、业务对查询要求较高的推荐使用索引

### IPC

#### 1、Android中进程和线程的关系？ 区别？
> + 参考回答：
>   + 线程是CPU调度的**最小单元**，同时线程是一种**有限**的系统资源
>   + 进程一般指一个执行单元，在PC和移动设备上一个程序或则一个应用
>   + 一般来说，一个App程序**至少有一个**进程，一个进程**至少有一个**线程（包含与被包含的关系），
通俗来讲就是，在App这个工厂里面有一个进程，线程就是里面的生产线，但主线程（主生产线）只有一条，而子线程（副生产线）可以有多个
>   + 进程有自己独立的地址空间，而进程中的线程共享此地址空间，都可以**并发**执行
> + 推荐文章：
>   + [Android developer官方文档--进程和线程](https://developer.android.com/guide/components/processes-and-threads?hl=zh-cn)

#### 2、如何开启多进程 ？ 应用是否可以开启N个进程 ？ 
> + 参考回答：
>   + 在AndroidMenifest中给四大组件指定属性android:process开启多进程模式
>   + 在内存允许的条件下可以开启N个进程
> + 推荐讲解：
>   + [如何开启多进程?应用是否可以开启N个进程？](https://github.com/qmsggg/qmsggg_BlogCollect/issues/158)

#### 3、为何需要IPC？多进程通信可能会出现的问题？
> + 参考回答：
>   + 所有运行在不同进程的四大组件（Activity、Service、Receiver、ContentProvider）共享数据都会失败，这是由于Android为每个应用分配了独立的虚拟机，不同的虚拟机在内存分配上有不同的地址空间，这会导致在不同的虚拟机中访问同一个类的对象会产生多份副本。比如常用例子（**通过开启多进程获取更大内存空间、两个或则多个应用之间共享数据、微信全家桶**）
>   + 一般来说，使用多进程通信会造成如下几方面的问题
>       + **静态成员和单例模式完全失效**：独立的虚拟机造成
>       + **线程同步机制完全实效**：独立的虚拟机造成
>       + **SharedPreferences的可靠性下降**：这是因为Sp不支持两个进程并发进行读写，有一定几率导致数据丢失
>       + **Application会多次创建**：Android系统在创建新的进程会分配独立的虚拟机，所以这个过程其实就是启动一个应用的过程，自然也会创建新的Application
> + 推荐文章：
>   + [Android developer官方文档--进程和线程](https://developer.android.com/guide/components/processes-and-threads?hl=zh-cn)

#### 4、Android中IPC方式、各种方式优缺点，为什么选择Binder？
> + 参考回答：
![](https://user-gold-cdn.xitu.io/2019/3/8/1695c1ab2aabf780?w=1240&h=720&f=jpeg&s=126001)
与Linux上传统的IPC机制，比如System V，Socket相比，Binder好在哪呢？
>   + **传输效率高、可操作性强**：传输效率主要影响因素是内存拷贝的次数，拷贝次数越少，传输速率越高。从Android进程架构角度分析：对于消息队列、Socket和管道来说，数据先从发送方的缓存区拷贝到内核开辟的缓存区中，再从内核缓存区拷贝到接收方的缓存区，一共两次拷贝，如图：
![](https://user-gold-cdn.xitu.io/2019/3/8/1695c427354bbec4?w=500&h=185&f=png&s=54991)
而对于Binder来说，数据从发送方的缓存区拷贝到内核的缓存区，而接收方的缓存区与内核的缓存区是映射到同一块物理地址的，节省了一次数据拷贝的过程，如图：
![](https://user-gold-cdn.xitu.io/2019/3/8/1695c428e3c4a95a?w=485&h=234&f=png&s=81083)
由于共享内存操作复杂，综合来看，Binder的传输效率是最好的。
>   + **实现C/S架构方便**：Linux的众IPC方式除了Socket以外都不是基于C/S架构，而Socket主要用于网络间的通信且传输效率较低。Binder基于C/S架构 ，Server端与Client端相对独立，稳定性较好。
>   + **安全性高**：传统Linux IPC的接收方无法获得对方进程可靠的UID/PID，从而无法鉴别对方身份；而Binder机制为每个进程分配了UID/PID且在Binder通信时会根据UID/PID进行有效性检测。
> + 推荐文章：
>   + [为什么 Android 要采用 Binder 作为 IPC 机制？](https://www.zhihu.com/question/39440766)

#### 5、Binder机制的作用和原理？
>  + 参考回答：
>    + Linux系统将一个进程分为**用户空间**和**内核空间**。对于进程之间来说，用户空间的数据不可共享，内核空间的数据可共享，为了保证安全性和独立性，一个进程不能直接操作或者访问另一个进程，即Android的进程是相互独立、隔离的，这就需要跨进程之间的数据通信方式
![传统IPC机制原理](https://user-gold-cdn.xitu.io/2019/3/8/1695c1ab41198d5c?w=1240&h=853&f=jpeg&s=53125)

>    + 一次完整的 Binder IPC 通信过程通常是这样：
>        + 首先 Binder 驱动在内核空间创建一个数据接收缓存区；
>        + 接着在内核空间开辟一块内核缓存区，建立内核缓存区和内核中数据接收缓存区之间的映射关系，以及内核中数据接收缓存区和接收进程用户空间地址的映射关系；
>        + 发送方进程通过系统调用 copyfromuser() 将数据 copy 到内核中的内核缓存区，由于内核缓存区和接收进程的用户空间存在内存映射，因此也就相当于把数据发送到了接收进程的用户空间，这样便完成了一次进程间的通信。
![Binder机制原理](https://user-gold-cdn.xitu.io/2019/3/8/1695c1ab2efe8dc5?w=1240&h=885&f=jpeg&s=63773)

#### 6、Binder框架中ServiceManager的作用？
> + 参考回答：
>   + **Binder框架** 是基于 C/S 架构的。由一系列的组件组成，包括 Client、Server、ServiceManager、Binder驱动，其中 Client、Server、Service Manager 运行在用户空间，Binder 驱动运行在内核空间
![](https://user-gold-cdn.xitu.io/2019/3/8/1695c1ab50cf525f?w=1240&h=640&f=jpeg&s=40718)
>        + **Server&Client**：服务器&客户端。在Binder驱动和Service Manager提供的基础设施上，进行Client-Server之间的通信。
>        + **ServiceManager**（如同DNS域名服务器）服务的管理者，将Binder名字转换为Client中对该Binder的引用，使得Client可以通过Binder名字获得Server中Binder实体的引用。
>        + **Binder驱动**（如同路由器）：负责进程之间binder通信的建立，传递，计数管理以及数据的传递交互等底层支持。
![](https://user-gold-cdn.xitu.io/2019/3/8/1695c1ab5abdf775?w=1117&h=1220&f=png&s=308318)
图片出自Carson_Ho文章 —— Android跨进程通信：图文详解 Binder机制 原理

#### 7、Bundle传递对象为什么需要序列化？Serialzable和Parcelable的区别？
> + 参考回答：
>    + 因为bundle传递数据时只支持基本数据类型，所以在传递对象时需要序列化转换成可存储或可传输的本质状态（字节流）。序列化后的对象可以在网络、IPC（比如启动另一个进程的Activity、Service和Reciver）之间进行传输，也可以存储到本地。
>    + 序列化实现的两种方式：实现Serializable/Parcelable接口。不同点如图：
![](https://user-gold-cdn.xitu.io/2019/3/8/1695c349f019c41f?w=500&h=393&f=png&s=104411)

#### 8、讲讲AIDL？原理是什么？如何优化多模块都使用AIDL的情况？
> + 参考回答：
>    + AIDL(Android Interface Definition Language，Android接口定义语言)：如果在一个进程中要调用另一个进程中对象的方法，可使用AIDL生成可序列化的参数，AIDL会生成一个服务端对象的代理类，通过它客户端实现间接调用服务端对象的方法。
>    + AIDL的本质是系统提供了一套可快速实现Binder的工具。关键类和方法：
>        + **AIDL接口**：继承IInterface。
>        + **Stub类**：Binder的实现类，服务端通过这个类来提供服务。
>        + **Proxy类**：服务器的本地代理，客户端通过这个类调用服务器的方法。
>        + **asInterface()**：客户端调用，将服务端的返回的Binder对象，转换成客户端所需要的AIDL接口类型对象。如果客户端和服务端位于统一进程，则直接返回Stub对象本身，否则返回系统封装后的Stub.proxy对象
>        + **asBinder()**：根据当前调用情况返回代理Proxy的Binder对象。
>        + **onTransact()**：运行服务端的Binder线程池中，当客户端发起跨进程请求时，远程请求会通过系统底层封装后交由此方法来处理。
>        + **transact()**：运行在客户端，当客户端发起远程请求的同时将当前线程挂起。之后调用服务端的onTransact()直到远程请求返回，当前线程才继续执行。
>    +   当有多个业务模块都需要AIDL来进行IPC，此时需要为每个模块创建特定的aidl文件，那么相应的Service就会很多。必然会出现系统资源耗费严重、应用过度重量级的问题。解决办法是建立Binder连接池，即将每个业务模块的Binder请求统一转发到一个远程Service中去执行，从而避免重复创建Service。
>        + **工作原理**：每个业务模块创建自己的AIDL接口并实现此接口，然后向服务端提供自己的唯一标识和其对应的Binder对象。服务端只需要一个Service，服务器提供一个queryBinder接口，它会根据业务模块的特征来返回相应的Binder对象，不同的业务模块拿到所需的Binder对象后就可进行远程方法的调用了

### View

#### 1、讲下View的绘制流程？
> + 参考回答：
>   + View的工作流程主要是指measure、layout、draw这三大流程，即测量、布局和绘制，其中measure确定View的**测量宽/高**，layout确定View的**最终宽/高**和**四个顶点的位置**，而draw则将View**绘制到屏幕**上
>   + View的绘制过程遵循如下几步：
>      + **绘制背景** background.draw(canvas) 
>      + **绘制自己**（onDraw）
>      + **绘制 children**（dispatchDraw）
>      + **绘制装饰**（onDrawScollBars）
![](https://user-gold-cdn.xitu.io/2019/3/8/1695c1ab5b9705a3?w=670&h=559&f=png&s=298536)
>   + 推荐文章：
>     + [官方文档](https://developer.android.com/reference/android/view/View)
>     + [Android View的绘制流程](https://www.jianshu.com/p/5a71014e7b1b)
>     + [Android应用层View绘制流程与源码分析](https://blog.csdn.net/yanbober/article/details/46128379)

#### 2、MotionEvent是什么？包含几种事件？什么条件下会产生？
> + 参考回答：
>   + MotionEvent是手指接触屏幕后所产生的一系列事件。典型的事件类型有如下：
>     + **ACTION_DOWN**：手指刚接触屏幕
>     + **ACTION_MOVE**：手指在屏幕上移动
>     + **ACTION_UP**：手指从屏幕上松开的一瞬间
>     + **ACTION_CANCELL**：手指保持按下操作，并从当前控件转移到外层控件时触发
>   + 正常情况下，一次手指触摸屏幕的行为会触发一系列点击事件，考虑如下几种情况：
>     + 点击屏幕后松开，事件序列：DOWN→UP
>     + 点击屏幕滑动一会再松开，事件序列为DOWN→MOVE→.....→MOVE→UP

#### 3、描述一下View事件传递分发机制？
> + 参考回答：
>   + View事件分发本质就是对MotionEvent事件分发的过程。即当一个MotionEvent发生后，系统将这个点击事件传递到一个具体的View上
>   + 点击事件的传递顺序：**Activity（Window）→ViewGroup→ View**
>   + 事件分发过程由三个方法共同完成：
>     + **dispatchTouchEvent**：用来进行事件的分发。如果事件能够传递给当前View，那么此方法一定会被调用，返回结果受当前View的onTouchEvent和下级View的dispatchTouchEvent方法的影响，表示是否消耗当前事件
>     + **onInterceptTouchEvent**：在上述方法内部调用，对事件进行拦截。该方法只在ViewGroup中有，View（不包含 ViewGroup）是没有的。一旦拦截，则执行ViewGroup的onTouchEvent，在ViewGroup中处理事件，而不接着分发给View。且只调用一次，返回结果表示是否拦截当前事件
>     + **onTouchEvent**： 在dispatchTouchEvent方法中调用，用来处理点击事件，返回结果表示是否消耗当前事件

#### 4、如何解决View的事件冲突 ？ 举个开发中遇到的例子 ？
> + 参考回答：
>    + 常见开发中事件冲突的有ScrollView与RecyclerView的滑动冲突、RecyclerView内嵌同时滑动同一方向
>    + 滑动冲突的处理规则：
>        + 对于由于外部滑动和内部滑动方向不一致导致的滑动冲突，可以根据滑动的方向判断谁来拦截事件。
>        + 对于由于外部滑动方向和内部滑动方向一致导致的滑动冲突，可以根据业务需求，规定何时让外部View拦截事件，何时由内部View拦截事件。
>        + 对于上面两种情况的嵌套，相对复杂，可同样根据需求在业务上找到突破点。
>    + 滑动冲突的实现方法：
>        + **外部拦截法**：指点击事件都先经过父容器的拦截处理，如果父容器需要此事件就拦截，否则就不拦截。具体方法：需要重写父容器的onInterceptTouchEvent方法，在内部做出相应的拦截。
>        + **内部拦截法**：指父容器不拦截任何事件，而将所有的事件都传递给子容器，如果子容器需要此事件就直接消耗，否则就交由父容器进行处理。具体方法：需要配合requestDisallowInterceptTouchEvent方法。


#### 5、scrollTo()和scollBy()的区别？
> + 参考回答：
>   + scollBy内部调用了scrollTo，它是基于当前位置的相对滑动；而scrollTo是绝对滑动，因此如果使用相同输入参数多次调用scrollTo方法，由于View的初始位置是不变的，所以只会出现一次View滚动的效果
>   + 两者都只能对View内容的滑动，而非使View本身滑动。可以使用Scroller有过度滑动的效果
> + 推荐文章：
>   + [View 的滑动原理和实现方式](https://www.jianshu.com/p/a177869b0382)

#### 6、Scroller是怎么实现View的弹性滑动？
> + 参考回答：
>    + 在MotionEvent.ACTION_UP事件触发时调用startScroll()方法，该方法并没有进行实际的滑动操作，而是记录滑动相关量（滑动距离、滑动时间）
>    + 接着调用invalidate/postInvalidate()方法，请求View重绘，导致View.draw方法被执行
>    + 当View重绘后会在draw方法中调用computeScroll方法，而computeScroll又会去向Scroller获取当前的scrollX和scrollY；然后通过scrollTo方法实现滑动；接着又调用postInvalidate方法来进行第二次重绘，和之前流程一样，如此反复导致View不断进行小幅度的滑动，而多次的小幅度滑动就组成了弹性滑动，直到整个滑动过成结束
![](https://user-gold-cdn.xitu.io/2019/3/8/1695c37ec2b0e11b?w=1148&h=746&f=jpeg&s=157379)

#### 7、 invalidate()和postInvalidate()的区别 ？
> + 参考回答：
>   + invalidate()与postInvalidate()都用于刷新View，主要区别是invalidate()在主线程中调用，若在子线程中使用需要配合handler；而postInvalidate()可在子线程中直接调用。

#### 8、SurfaceView和View的区别？
> + 参考回答：
>    + View需要在UI线程对画面进行刷新，而SurfaceView可在子线程进行页面的刷新
>    + View适用于主动更新的情况，而SurfaceView适用于被动更新，如频繁刷新，这是因为如果使用View频繁刷新会阻塞主线程，导致界面卡顿
>    + SurfaceView在底层已实现双缓冲机制，而View没有，因此SurfaceView更适用于需要频繁刷新、刷新时数据处理量很大的页面（如视频播放界面）

#### 9、自定义View如何考虑机型适配 ?
> + 参考回答：
>    + 合理使用warp_content，match_parent
>    + 尽可能的是使用RelativeLayout
>    + 针对不同的机型，使用不同的布局文件放在对应的目录下，android会自动匹配。
>    + 尽量使用点9图片。
>    + 使用与密度无关的像素单位dp，sp
>    + 引入android的百分比布局。
>    + 切图的时候切大分辨率的图，应用到布局当中。在小分辨率的手机上也会有很好的显示效果。

### Handler

#### 1、谈谈消息机制Handler作用 ？有哪些要素 ？流程是怎样的 ？
> + 参考回答：
>   + 负责**跨线程通信**，这是因为**在主线程不能做耗时操作，而子线程不能更新UI**，所以当子线程中进行耗时操作后需要更新UI时，通过Handler将有关UI的操作切换到主线程中执行。
>   + 具体分为四大要素
>     + **Message（消息）**：需要被传递的消息，消息分为硬件产生的消息（如按钮、触摸）和软件生成的消息。
>     + **MessageQueue（消息队列）**：负责消息的存储与管理，负责管理由 Handler发送过来的Message。读取会自动删除消息，单链表维护，插入和删除上有优势。在其next()方法中会无限循环，不断判断是否有消息，有就返回这条消息并移除。
>     + **Handler（消息处理器）**：负责Message的发送及处理。主要向消息池发送各种消息事件（Handler.sendMessage()）和处理相应消息事件（Handler.handleMessage()），按照先进先出执行，内部使用的是单链表的结构。
>     + **Looper（消息池）**：负责关联线程以及消息的分发，在该线程下从 MessageQueue获取 Message，分发给Handler，Looper创建的时候会创建一个 MessageQueue，调用loop()方法的时候消息循环开始，其中会不断调用messageQueue的next()方法，当有消息就处理，否则阻塞在messageQueue的next()方法中。当Looper的quit()被调用的时候会调用messageQueue的quit()，此时next()会返回null，然后loop()方法也就跟着退出。
>   + 具体流程如下
![](https://user-gold-cdn.xitu.io/2019/3/11/1696ad811957795d?w=737&h=512&f=png&s=27154)
>     + 在主线程创建的时候会创建一个Looper，同时也会在在Looper内部创建一个消息队列。而在创键Handler的时候取出当前线程的Looper，并通过该Looper对象获得消息队列，然后Handler在子线程中通过**MessageQueue.enqueueMessage**在消息队列中添加一条Message。
>     + 通过**Looper.loop()** 开启消息循环不断轮询调用 **MessageQueue.next()**，取得对应的Message并且通过**Handler.dispatchMessage**传递给Handler，最终调用**Handler.handlerMessage**处理消息。

#### 2、一个线程能否创建多个Handler，Handler跟Looper之间的对应关系 ？
> + 参考回答：
>   + **一个Thread只能有一个Looper，一个MessageQueen，可以有多个Handler**
>   + **以一个线程为基准，他们的数量级关系是： Thread(1) : Looper(1) : MessageQueue(1) : Handler(N)**

#### 3、软引用跟弱引用的区别
> + 参考回答：
>   + **软引用**：如果一个对象只具有软引用，则内存空间充足时，垃圾回收器就不会回收它；如果内存空间不足了，就会回收这些对象的内存。只要垃圾回收器没有回收它，该对象就可以一直被程序使用。
>   + **弱引用**：如果一个对象只具有弱引用，那么在垃圾回收器线程扫描的过程中，一旦发现了只具有弱引用的对象，不管当前内存空间足够与否，都会回收它的内存。
>   + 两者之间**根本区别**在于：只具有弱引用的对象拥有更短暂的生命周期，可能随时被回收。而只具有软引用的对象只有当内存不够的时候才被回收，在内存足够的时候，通常不被回收。
![](https://user-gold-cdn.xitu.io/2019/3/11/1696c45ababaed39?w=858&h=378&f=jpeg&s=57940)
> + 推荐文章：
>   + [Java中的四种引用类型：强引用、软引用、弱引用和虚引用](https://segmentfault.com/a/1190000015282652)

#### 4、Handler 引起的内存泄露原因以及最佳解决方案
> + 参考回答：
>   + 泄露原因：
>     + Handler 允许我们发送延时消息，如果在延时期间用户关闭了 Activity，那么该 Activity 会泄露。 这个泄露是因为 Message 会持有 Handler，而又因为 Java 的特性，内部类会持有外部类，使得 Activity 会被 Handler 持有，这样最终就导致 Activity 泄露。
>   + 解决方案：
>     + **将 Handler 定义成静态的内部类，在内部持有Activity的弱引用，并在Acitivity的onDestroy()中调用handler.removeCallbacksAndMessages(null)及时移除所有消息。**

#### 5、为什么系统不建议在子线程访问UI？
> + 参考回答：
>   + Android的UI控件不是**线程安全**的，如果在多线程中并发访问可能会导致UI控件处于不可预期的状态
>   + 这时你可能会问为何系统不对UI控件的访问加上锁机制呢？因为
>     + 加锁机制会让UI访问逻辑变的复杂
>     + 加锁机制会降低UI的访问效率,因为加锁会阻塞某些线程的执行
![](https://user-gold-cdn.xitu.io/2019/3/13/16975fdd364417e0?w=1208&h=579&f=png&s=24683)

#### 6、Looper死循环为什么不会导致应用卡死？
> + 参考回答：
>   + 主线程的主要方法就是**消息循环**，一旦退出消息循环，那么你的应用也就退出了，Looer.loop（）方法可能会引起主线程的阻塞，但只要它的消息循环没有被阻塞，能一直处理事件就不会产生ANR异常。
>   + 造成**ANR**的不是主线程阻塞，而是主线程的Looper消息处理过程发生了**任务阻塞**，无法响应手势操作，不能及时刷新UI。
>   + **阻塞与程序无响应**没有必然关系，虽然主线程在没有消息可处理的时候是阻塞的，但是只要保证有消息的时候能够立刻处理，程序是不会无响应的。

#### 7、使用Handler的postDealy后消息队列会有什么变化？
> + 参考回答：
>   + 如果队列中只有这个消息，那么消息不会被发送，而是计算到时唤醒的时间，先将Looper阻塞，到时间就唤醒它。但如果此时要加入新消息，该消息队列的对头跟delay时间相比更长，则插入到头部，按照触发时间进行排序，队头的时间最小、队尾的时间最大

#### 8、可以在子线程直接new一个Handler吗？怎么做？
> + 参考回答：
>   + 不可以，因为在**主线程**中，Activity内部包含一个Looper对象，它会自动管理Looper，处理子线程中发送过来的消息。而对于**子线程**而言，没有任何对象帮助我们维护Looper对象，所以需要我们自己手动维护。所以要在子线程开启Handler要先创建Looper，并开启Looper循环
![](https://user-gold-cdn.xitu.io/2019/3/14/1697b3a7257d670a?w=1312&h=878&f=png&s=129237)
> + 推荐文章：
>   + [Android异步消息处理机制完全解析，带你从源码的角度彻底理解](https://blog.csdn.net/guolin_blog/article/details/9991569)

#### 9、Message可以如何创建？哪种效果更好，为什么？
> + 参考回答：可以通过三种方法创建：
>   + 直接生成实例**Message m = new Message**
>   + 通过**Message m = Message.obtain**
>   + 通过**Message m = mHandler.obtainMessage()**
> + 后两者效果更好，因为Android默认的消息池中消息数量是10，而后两者是直接在消息池中取出一个Message实例，这样做就可以避免多生成Message实例。

### 线程
#### 1、线程池的好处？ 四种线程池的使用场景，线程池的几个参数的理解？
> + 参考回答：
>   + 使用线程池的好处是减少在创建和销毁线程上所花的时间以及系统资源的开销，解决资源不足的问题。如果不使用线程池，有可能造成系统创建大量同类线程而导致消耗完内存或则“过度切换”的问题，归纳总结就是
>     + **重用存在的线程，减少对象创建、消亡的开销，性能佳。**
>     + **可有效控制最大并发线程数，提高系统资源的使用率，同时避免过多资源竞争，避免堵塞。**
>     + **提供定时执行、定期执行、单线程、并发数控制等功能。**
>   + Android中的线程池都是直接或间接通过配置ThreadPoolExecutor来实现不同特性的线程池.Android中最常见的类具有不同特性的线程池分别为：
>     + **newCachedThreadPool**：只有非核心线程，最大线程数非常大，所有线程都活动时会为新任务创建新线程,否则会利用空闲线程 ( 60s空闲时间,过了就会被回收,所以线程池中有0个线程的可能 )来处理任务.
>       + 优点：任何任务都会被立即执行(任务队列SynchronousQuue相当于一个空集合);比较适合执行大量的耗时较少的任务.
>     + **newFixedThreadPool**：只有核心线程，并且数量固定的，所有线程都活动时，因为队列没有限制大小，新任务会等待执行，当线程池空闲时不会释放工作线程，还会占用一定的系统资源。
>       + 优点：更快的响应外界请求
>     + **newScheduledThreadPool**：核心线程数固定,非核心线程（闲着没活干会被立即回收数）没有限制.
>       + 优点：执行定时任务以及有固定周期的重复任务
>     + **newSingleThreadExecutor**：只有一个核心线程,确保所有的任务都在同一线程中按序完成
>       + 优点：不需要处理线程同步的问题
>   + 通过源码可以了解到上面的四种线程池实际上还是利用 ThreadPoolExecutor 类实现的
![](https://user-gold-cdn.xitu.io/2019/3/14/1697b39705b3f2e3?w=1870&h=1022&f=png&s=294409)
> + 推荐文章：
>   + [java线程池解析和四种线程池的使用](https://blog.csdn.net/ztchun/article/details/57413255)

#### 2、Android中还了解哪些方便线程切换的类？
> + 参考回答：
>   + **AsyncTask**：底层封装了线程池和Handler，便于执行后台任务以及在子线程中进行UI操作。
>   + **HandlerThread**：一种具有消息循环的线程，其内部可使用Handler。
>   + **IntentService**：是一种异步、会自动停止的服务，内部采用HandlerThread。

#### 3、讲讲AsyncTask的原理
> + 参考回答：
>   + **AsyncTask中有两个线程池（SerialExecutor和THREAD_POOL_EXECUTOR）和一个Handler（InternalHandler），其中线程池SerialExecutor用于任务的排队，而线程池THREAD_POOL_EXECUTOR用于真正地执行任务，InternalHandler用于将执行环境从线程池切换到主线程。**
>   + **sHandler是一个静态的Handler对象，为了能够将执行环境切换到主线程，这就要求sHandler这个对象必须在主线程创建。由于静态成员会在加载类的时候进行初始化，因此这就变相要求AsyncTask的类必须在主线程中加载，否则同一个进程中的AsyncTask都将无法正常工作。**

#### 4、IntentService有什么用 ？
> + 参考回答：
>   + **IntentService**可用于**执行后台耗时**的任务，当任务执行完成后会**自动停止**，同时由于IntentService是服务的原因，不同于普通Service，IntentService可**自动创建子线程**来执行任务，这导致它的优先级比单纯的线程要高，不容易被系统杀死，所以IntentService比较适合执行一些**高优先级**的后台任务。

#### 5、直接在Activity中创建一个thread跟在service中创建一个thread之间的区别？
> + 参考回答：
>   + **在Activity中被创建**：该Thread的就是为这个Activity服务的，完成这个特定的Activity交代的任务，主动通知该Activity一些消息和事件，Activity销毁后，该Thread也没有存活的意义了。
>   + **在Service中被创建**：这是保证最长生命周期的Thread的唯一方式，只要整个Service不退出，Thread就可以一直在后台执行，一般在Service的onCreate()中创建，在onDestroy()中销毁。所以，在Service中创建的Thread，适合长期执行一些独立于APP的后台任务，比较常见的就是：在Service中保持与服务器端的长连接。

#### 6、ThreadPoolExecutor的工作策略 ？
> + 参考回答：ThreadPoolExecutor执行任务时会遵循如下规则
>   + 如果线程池中的线程数量**未达到**核心线程的数量，那么会直接启动一个核心线程来执行任务。
>   + 如果线程池中的线程数量**已经达到**或则超过核心线程的数量，那么任务会被插入任务队列中排队等待执行。
>   + 如果在第2点无法将任务插入到任务队列中，这往往是由于任务队列已满，这个时候如果在线程数量**未达到线程池规定的最大值**，那么会立刻启动一个非核心线程来执行任务。
>   + 如果第3点中线程数量**已经达到线程池规定的最大值**，那么就拒绝执行此任务，ThreadPoolExecutor会调用RejectedExecutionHandler的rejectedExecution方法来通知调用者。

#### 7、Handler、Thread和HandlerThread的差别？
> + 参考回答：
>   + **Handler**：在android中负责发送和处理消息，通过它可以实现其他支线线程与主线程之间的消息通讯。
>   + **Thread**：Java进程中执行运算的最小单位，亦即执行处理机调度的基本单位。某一进程中一路单独运行的程序。
>   + **HandlerThread**：一个继承自Thread的类HandlerThread，Android中没有对Java中的Thread进行任何封装，而是提供了一个继承自Thread的类HandlerThread类，这个类对Java的Thread做了很多便利的封装。HandlerThread继承于Thread，所以它本质就是个Thread。与普通Thread的差别就在于，它在内部直接实现了Looper的实现，这是Handler消息机制必不可少的。有了自己的looper，可以让我们在自己的线程中分发和处理消息。如果不用HandlerThread的话，需要手动去调用Looper.prepare()和Looper.loop()这些方法。

#### 8、ThreadLocal的原理
> + 参考回答：
>   + ThreadLocal是一个关于创建线程局部变量的类。使用场景如下所示：
>     + **实现单个线程单例以及单个线程上下文信息存储，比如交易id等。**
>     + **实现线程安全，非线程安全的对象使用ThreadLocal之后就会变得线程安全，因为每个线程都会有一个对应的实例。 承载一些线程相关的数据，避免在方法中来回传递参数。**
>   + 当需要使用多线程时，有个变量恰巧不需要共享，此时就不必使用synchronized这么麻烦的关键字来锁住，每个线程都相当于在堆内存中开辟一个空间，线程中带有对共享变量的缓冲区，通过缓冲区将堆内存中的共享变量进行读取和操作，ThreadLocal相当于线程内的内存，一个局部变量。每次可以对线程自身的数据读取和操作，并不需要通过缓冲区与 主内存中的变量进行交互。并不会像synchronized那样修改主内存的数据，再将主内存的数据复制到线程内的工作内存。ThreadLocal可以让线程独占资源，存储于线程内部，避免线程堵塞造成CPU吞吐下降。
>   + 在每个Thread中包含一个ThreadLocalMap，ThreadLocalMap的key是ThreadLocal的对象，value是独享数据。

#### 9、多线程是否一定会高效（优缺点）
> + 参考回答：
>   + 多线程的优点：
>     + **方便高效的内存共享 - 多进程下内存共享比较不便，且会抵消掉多进程编程的好处**
>     + **较轻的上下文切换开销 - 不用切换地址空间，不用更改CR3寄存器，不用清空TLB**
>     + **线程上的任务执行完后自动销毁**
>   + 多线程的缺点：
>     + **开启线程需要占用一定的内存空间(默认情况下,每一个线程都占512KB)**
>     + **如果开启大量的线程,会占用大量的内存空间,降低程序的性能**
>     + **线程越多,cpu在调用线程上的开销就越大**
>     + **程序设计更加复杂,比如线程间的通信、多线程的数据共享**
>   + 综上得出，多线程**不一定**能提高效率，在内存空间紧张的情况下反而是一种负担，因此在日常开发中，应尽量
>     + **不要频繁创建，销毁线程，使用线程池**
>     + **减少线程间同步和通信（最为关键）**
>     + **避免需要频繁共享写的数据**
>     + **合理安排共享数据结构，避免伪共享（false sharing）**
>     + **使用非阻塞数据结构/算法**
>     + **避免可能产生可伸缩性问题的系统调用（比如mmap）**
>     + **避免产生大量缺页异常，尽量使用Huge Page**
>     + **可以的话使用用户态轻量级线程代替内核线程**

#### 10、多线程中,让你做一个单例,你会怎么做
> + 参考回答：
>   + 多线程中建立单例模式考虑的因素有很多，比如线程安全 -延迟加载-代码安全:如防止序列化攻击，防止反射攻击(防止反射进行私有方法调用) -性能因素
>   + 实现方法有多种，饿汉，懒汉(线程安全，线程非安全)，双重检查(DCL),内部类，以及枚举
![](https://user-gold-cdn.xitu.io/2019/3/15/16980b520b353f34?w=1160&h=842&f=png&s=123575)
>   + 推荐文章：
>     + [单例模式的总结](https://xxxblank.github.io/2017/09/14/singleTon/)

#### 11、除了notify还有什么方式可以唤醒线程
> + 参考回答：
>   + 当一个拥有Object锁的线程调用 wait()方法时，就会使当前线程加入object.wait 等待队列中，并且释放当前占用的Object锁，这样其他线程就有机会获取这个Object锁，获得Object锁的线程调用notify()方法，就能在Object.wait 等待队列中随机唤醒一个线程（该唤醒是随机的与加入的顺序无关，优先级高的被唤醒概率会高）
>   + 如果调用notifyAll（）方法就唤醒全部的线程。**注意:调用notify()方法后并不会立即释放object锁，会等待该线程执行完毕后释放Object锁。**

#### 12、什么是ANR ? 什么情况会出现ANR ？如何避免 ？ 在不看代码的情况下如何快速定位出现ANR问题所在 ？
> + 参考回答：
>   + ANR（Application Not Responding，应用无响应）：当操作在一段时间内系统无法处理时，会在系统层面会弹出ANR对话框
>   + 产生ANR可能是因为5s内无响应用户输入事件、10s内未结束BroadcastReceiver、20s内未结束Service
>   + 想要避免ANR就不要在主线程做耗时操作，而是通过开子线程，方法比如继承Thread或实现Runnable接口、使用AsyncTask、IntentService、HandlerThread等
>   + 推荐文章：
>     + [如何快速分析定位ANR](https://www.jianshu.com/p/cfa9ed42e379)

### Bitmap
#### 1、Bitmap使用需要注意哪些问题 ？
> + 参考回答：
>   + **要选择合适的图片规格（bitmap类型）**：通常我们优化Bitmap时，当需要做性能优化或者防止OOM，我们通常会使用RGB_565，因为ALPHA_8只有透明度，显示一般图片没有意义，Bitmap.Config.ARGB_4444显示图片不清楚，Bitmap.Config.ARGB_8888占用内存最多。：
>     + ALPHA_8   每个像素占用1byte内存        
>     + ARGB_4444 每个像素占用2byte内存       
>     + ARGB_8888 每个像素占用4byte内存（默认）      
>     + RGB_565 每个像素占用2byte内存
>   + **降低采样率**：BitmapFactory.Options 参数inSampleSize的使用，先把options.inJustDecodeBounds设为true，只是去读取图片的大小，在拿到图片的大小之后和要显示的大小做比较通过calculateInSampleSize()函数计算inSampleSize的具体值，得到值之后。options.inJustDecodeBounds设为false读图片资源。
>   + **复用内存**：即通过软引用(内存不够的时候才会回收掉)，复用内存块，不需要再重新给这个bitmap申请一块新的内存，避免了一次内存的分配和回收，从而改善了运行效率。
>   + **使用recycle()方法及时回收内存**。
>   + **压缩图片**。

#### 2、Bitmap.recycle()会立即回收么？什么时候会回收？如果没有地方使用这个Bitmap，为什么垃圾回收不会直接回收？
> + 参考回答：
>   + 通过源码可以了解到，加载Bitmap到内存里以后，是包含**两部分内存区域**的。简单的说，一部分是**Java部分**的，一部分是**C部分**的。这个Bitmap对象是由Java部分分配的，不用的时候系统就会自动回收了
>   + 但是那个对应的**C可用**的内存区域，虚拟机是不能直接回收的，这个只能调用底层的功能释放。所以需要调用recycle()方法来释放C部分的内存
>   + bitmap.recycle()方法用于回收该Bitmap所占用的内存，接着将bitmap置空，最后使用System.gc()调用一下系统的垃圾回收器进行回收，调用System.gc()并不能保证立即开始进行回收过程，而只是为了加快回收的到来。

#### 3、一张Bitmap所占内存以及内存占用的计算
> + 参考回答：
>   + Bitamp 所占内存大小 = 宽度像素 x （inTargetDensity / inDensity） x 高度像素 x （inTargetDensity / inDensity）x 一个像素所占的内存字节大小
>     + 注：这里inDensity表示目标图片的dpi（放在哪个资源文件夹下），inTargetDensity表示目标屏幕的dpi，所以你可以发现inDensity和inTargetDensity会对Bitmap的宽高进行拉伸，进而改变Bitmap占用内存的大小。
>   + 在Bitmap里有两个获取内存占用大小的方法。
>     + **getByteCount()**：API12 加入，代表存储 Bitmap 的像素需要的最少内存。 
>     + **getAllocationByteCount()**：API19 加入，代表在内存中为 Bitmap 分配的内存大小，代替了 getByteCount() 方法。
>     + 在**不复用 Bitmap** 时，getByteCount() 和 getAllocationByteCount 返回的结果是一样的。在通过**复用 Bitmap** 来解码图片时，那么 getByteCount() 表示新解码图片占用内存的大 小，getAllocationByteCount() 表示被复用 Bitmap 真实占用的内存大小

#### 4、Android中缓存更新策略 ？
> + 参考回答：
>   + Android的缓存更新策略**没有统一的标准**，一般来说，缓存策略主要包含**缓存的添加、获取和删除**这三类操作，但不管是内存缓存还是存储设备缓存，它们的缓存容量是有限制的，因此删除一些旧缓存并添加新缓存，如何定义缓存的新旧这就是一种策略，**不同的策略就对应着不同的缓存算法**
>   + 比如可以简单地根据文件的最后修改时间来定义缓存的新旧，当缓存满时就将最后修改时间较早的缓存移除，这就是一种缓存算法，但不算很完美

#### 5、LRU的原理 ？
> + 参考回答：
>   + 为减少流量消耗，可采用缓存策略。常用的缓存算法是LRU(Least Recently Used)：当缓存满时, 会优先淘汰那些近期最少使用的缓存对象。主要是两种方式：
>   + **LruCache(内存缓存)**：LruCache类是一个线程安全的泛型类：内部采用一个LinkedHashMap以强引用的方式存储外界的缓存对象，并提供get和put方法来完成缓存的获取和添加操作，当缓存满时会移除较早使用的缓存对象，再添加新的缓存对象。
>   + **DiskLruCache(磁盘缓存)**： 通过将缓存对象写入文件系统从而实现缓存效果

### 性能优化
#### 1、图片的三级缓存中,图片加载到内存中,如果内存快爆了,会发生什么？怎么处理？
> + 参考回答：
>   + 首先我们要清楚图片的三级缓存是如何的
![](https://user-gold-cdn.xitu.io/2019/3/19/169956a4db82e9f3?w=1319&h=529&f=png&s=9441)
如果内存足够时不回收。内存不够时就回收软引用对象

#### 2、内存中如果加载一张500*500的png高清图片.应该是占用多少的内存?
> + 参考回答：
>   + **不考虑屏幕比的话**：占用内存=500 * 500 * 4 = 1000000B ≈ 0.95MB
>   + **考虑屏幕比的的话**：占用内存= 宽度像素 x （inTargetDensity / inDensity） x 高度像素 x （inTargetDensity / inDensity）x 一个像素所占的内存字节大小
>   + inDensity表示目标图片的dpi（放在哪个资源文件夹下），inTargetDensity表示目标屏幕的dpi
![](https://user-gold-cdn.xitu.io/2019/3/19/169957db5956a922?w=239&h=277&f=png&s=11171)

#### 3、WebView的性能优化 ?
> + 参考回答：
>   + 一个加载网页的过程中，native、网络、后端处理、CPU都会参与，各自都有必要的工作和依赖关系；让他们相互并行处理而不是相互阻塞才可以让网页加载更快：
>     + **WebView初始化慢，可以在初始化同时先请求数据，让后端和网络不要闲着。**
>     + **常用 JS 本地化及延迟加载，使用第三方浏览内核**
>     + **后端处理慢，可以让服务器分trunk输出，在后端计算的同时前端也加载网络静态资源。**
>     + **脚本执行慢，就让脚本在最后运行，不阻塞页面解析。**
>     + **同时，合理的预加载、预缓存可以让加载速度的瓶颈更小。**
>     + **WebView初始化慢，就随时初始化好一个WebView待用。**
>     + **DNS和链接慢，想办法复用客户端使用的域名和链接。**
![WebView启动时间](https://user-gold-cdn.xitu.io/2019/3/20/16998e3a43726040?w=1534&h=504&f=png&s=28905)
> + 推荐文章：
>   + [WebView性能、体验分析与优化](https://tech.meituan.com/2017/06/09/webviewperf.html)

#### 4、Bitmap如何处理大图，如一张30M的大图，如何预防OOM？
> + 参考回答：避免OOM的问题就需要对大图片的加载进行管理，主要通过缩放来减小图片的内存占用。
>   + BitmapFactory提供的加载图片的四类方法（**decodeFile、decodeResource、decodeStream、decodeByteArray**）都支持BitmapFactory.Options参数，通过inSampleSize参数就可以很方便地对一个图片进行采样缩放
>   + 比如一张1024*1024的高清图片来说。那么它占有的内存为1024*1024*4，即4MB，如果inSampleSize为2，那么采样后的图片占用内存只有512*512*4,即1MB（**注意：根据最新的官方文档指出，inSampleSize的取值应该总是为2的指数，即1、2、4、8等等，如果外界输入不足为2的指数，系统也会默认选择最接近2的指数代替，比如2**）
>   + 综合考虑。通过采样率即可有效加载图片，流程如下
>       + **将BitmapFactory.Options的inJustDecodeBounds参数设为true并加载图片**
>       + **从BitmapFactory.Options中取出图片的原始宽高信息，它们对应outWidth和outHeight参数**
>       + **根据采样率的规则并结合目标View的所需大小计算出采样率inSampleSize**
>       + **将BitmapFactory.Options的inJustDecodeBounds参数设为false，重新加载图片**
![](https://user-gold-cdn.xitu.io/2019/3/20/1699929ec27be435?w=2048&h=1706&f=png&s=412340)
> + 推荐文章：
>   + [Android高效加载大图、多图解决方案，有效避免程序OOM](https://blog.csdn.net/guolin_blog/article/details/9316683)

#### 5、内存回收机制与GC算法(各种算法的优缺点以及应用场景)；GC原理时机以及GC对象
> + 参考回答：
>   + 内存判定对象可回收有两种机制：
>     + **引用计数算法**：给对象中添加一个引用计数器，每当有一个地方引用它时，计数器值就加1；当引用失效时，计数器值就减1；任何时刻计数器为0的对象就是不可能再被使用的。然而在主流的Java虚拟机里未选用引用计数算法来管理内存，主要原因是它难以解决对象之间**相互循环引用**的问题，所以出现了另一种对象存活判定算法。
>     + **可达性分析法**：通过一系列被称为『GCRoots』的对象作为起始点，从这些节点开始向下搜索，搜索所走过的路径称为***引用链**，当一个对象到GC Roots没有任何引用链相连时，则证明此对象是不可用的。其中可作为GC Roots的对象：虚拟机栈中引用的对象，主要是指栈帧中的**本地变量**、本地方法栈中**Native**方法引用的对象、方法区中**类静态**属性引用的对象、方法区中**常量**引用的对象
>   + GC回收算法有以下四种：
>     + **分代收集算法**：是当前商业虚拟机都采用的一种算法，根据对象存活周期的不同，将Java堆划分为新生代和老年代，并根据各个年代的特点采用最适当的收集算法。
>     + 新生代：大批对象死去，只有少量存活。使用『复制算法』，只需复制少量存活对象即可。
>       + **复制算法**：把可用内存按容量划分为大小相等的两块，每次只使用其中的一块。当这一块的内存用尽后，把还存活着的对象『复制』到另外一块上面，再将这一块内存空间一次清理掉。**实现简单，运行高效。在对象存活率较高时就要进行较多的复制操作，效率将会变低**
>     + 老年代：对象存活率高。使用『标记—清理算法』或者『标记—整理算法』，只需标记较少的回收对象即可。
>       + **标记-清除算法**：首先『标记』出所有需要回收的对象，然后统一『清除』所有被标记的对象。**标记和清除两个过程的效率都不高，清除之后会产生大量不连续的内存碎片，空间碎片太多可能会导致以后在程序运行过程中需要分配较大对象时，无法找到足够的连续内存而不得不提前触发另一次垃圾收集动作。**
>       + **标记-整理算法**：首先『标记』出所有需要回收的对象，然后进行『整理』，使得存活的对象都向一端移动，最后直接清理掉端边界以外的内存。**标记整理算法会将所有的存活对象移动到一端，并对不存活对象进行处理，因此其不会产生内存碎片**
>   + 推荐文章：[图解Java 垃圾回收机制](https://blog.csdn.net/justloveyou_/article/details/71216049) 

#### 6、内存泄露和内存溢出的区别 ？AS有什么工具可以检测内存泄露
> + 参考回答：
>   + **内存溢出(out of memory)**：是指程序在申请内存时，没有足够的内存空间供其使用，出现out of memory；比如申请了一个integer，但给它存了long才能存下的数，那就是内存溢出。
>   + **内存泄露(memory leak)**：是指程序在申请内存后，无法释放已申请的内存空间，一次内存泄露危害可以忽略，但内存泄露堆积后果很严重，无论多少内存,迟早会被占光。**memory leak会最终会导致out of memory！**
>   + 查找内存泄漏可以使用Android Studio 自带的**AndroidProfiler**工具或**MAT**

#### 7、性能优化,怎么保证应用启动不卡顿? 黑白屏怎么处理?
> + 参考回答：
>   + **应用启动速度**，取决于你在application里面时候做了什么事情，比如你集成了很多sdk，并且sdk的init操作都需要在主线程里实现所以会有卡顿的感觉。在非必要的情况下可以把加载延后或则开启子线程处理
>   + 另外，影响**界面卡顿**的两大因素，分别是**界面绘制和数据处理。**
>     + 布局优化(使用include，merge标签，复杂布局推荐使用ConstraintLayout等)
>     + onCreate() 中不执行耗时操作 把页面显示的 View 细分一下，放在 AsyncTask 里逐步显示，用 Handler 更好。这样用户的看到的就是有层次有步骤的一个个的 View 的展示，不会是先看到一个黑屏，然后一下显示所有 View。最好做成动画，效果更自然。
>     + 利用多线程的目的就是尽可能的减少 onCreate() 和 onReume() 的时间，使得用户能尽快看到页面，操作页面。
>     + 减少主线程阻塞时间。
>     + 提高 Adapter 和 AdapterView 的效率。
>   + 推荐文章：[Android 性能优化之内存检测、卡顿优化、耗电优化、APK瘦身](https://blog.csdn.net/csdn_aiyang/article/details/74989318) 
>   + **黑白屏产生原因**：当我们在启动一个应用时，系统会去检查是否已经存在这样一个进程，如果不存在，系统的服务会先检查startActivity中的intent的信息，然后在去创建进程，最后启动Acitivy，即冷启动。而启动出现白黑屏的问题，就是在这段时间内产生的。系统在绘制页面加载布局之前，首先会初始化窗口（Window），而在进行这一步操作时，系统会根据我们设置的Theme来指定它的Theme 主题颜色，我们在Style中的设置就决定了显示的是白屏还是黑屏。
>     + windowIsTranslucent和windowNoTitle，将这两个属性都设置成true (会有明显的卡顿体验，不推荐)
>     + 如果启动页只是是一张图片，那么为启动页专一设置一个新的主题，设置主题的android:windowBackground属性为启动页背景图即可
>     + 使用layer-list制作一张图片launcher_layer.xml，将其设置为启动页专一主题的背景，并将其设置为启动页布局的背景。
> + 推荐文章：
>   + [Android启动页解决攻略](https://blog.csdn.net/zivensonice/article/details/51691136)

#### 8、强引用置为null，会不会被回收？
> + 参考回答：
>   + **不会立即释放对象占用的内存。** 如果对象的引用被置为null，只是断开了当前线程栈帧中对该对象的引用关系，而 垃圾收集器是运行在后台的线程，只有当用户线程运行到安全点(safe point)或者安全区域才会扫描对象引用关系，扫描到对象没有被引用则会标记对象，这时候仍然不会立即释放该对象内存，因为有些对象是可恢复的（在 finalize方法中恢复引用 ）。只有确定了对象无法恢复引用的时候才会清除对象内存。

#### 9、ListView跟RecyclerView的区别
> + 参考回答：
>   + 动画区别：
>     + 在**RecyclerView**中，内置有许多动画API，例如：notifyItemChanged(), notifyDataInserted(), notifyItemMoved()等等；如果需要自定义动画效果，可以通过实现（RecyclerView.ItemAnimator类）完成自定义动画效果，然后调用RecyclerView.setItemAnimator()；
>     + 但是**ListView**并没有实现动画效果，但我们可以在Adapter自己实现item的动画效果；
>   + 刷新区别：
>     + ListView中通常刷新数据是用全局刷新notifyDataSetChanged()，这样一来就会非常消耗资源；**本身无法实现局部刷新**，但是如果要在ListView实现**局部刷新**，依然是可以实现的，当一个item数据刷新时，我们可以在Adapter中，实现一个onItemChanged()方法，在方法里面获取到这个item的position（可以通过getFirstVisiblePosition()），然后调用getView()方法来刷新这个item的数据；
>     + RecyclerView中可以实现局部刷新，例如：notifyItemChanged()；
>   + 缓存区别：
>     + RecyclerView比ListView多两级缓存，支持多个离ItemView缓存，支持开发者自定义缓存处理逻辑，支持所有RecyclerView共用同一个RecyclerViewPool(缓存池)。
>     + ListView和RecyclerView缓存机制基本一致，但缓存使用不同
> + 推荐文章：
>   + [【腾讯Bugly干货分享】Android ListView 与 RecyclerView 对比浅析—缓存机制](https://zhuanlan.zhihu.com/p/23339185)
>   + [ListView 与 RecyclerView 简单对比](https://blog.csdn.net/shu_lance/article/details/79566189)
>   + [Android开发：ListView、AdapterView、RecyclerView全面解析](https://www.jianshu.com/p/4e8e4fd13cf7)

#### 10、ListView的adapter是什么adapter
> + 参考回答：
![](https://user-gold-cdn.xitu.io/2019/3/20/1699a79b39f0c556?w=658&h=546&f=png&s=31636)
>   + **BaseAdapter**：抽象类，实际开发中我们会继承这个类并且重写相关方法，用得最多的一个适配器！
>   + **ArrayAdapter**：支持泛型操作，最简单的一个适配器，只能展现一行文字〜
>   + **SimpleAdapter**：同样具有良好扩展性的一个适配器，可以自定义多种效果！
>   + **SimpleCursorAdapter**：用于显示简单文本类型的listView，一般在数据库那里会用到，不过有点过时，不推荐使用！

#### 11、LinearLayout、FrameLayout、RelativeLayout性能对比，为什么？
> + 参考回答：
>   + RelativeLayout会让子View调用2次onMeasure，LinearLayout 在有weight时，也会调用子 View 2次onMeasure
>   + RelativeLayout的子View如果高度和RelativeLayout不同，则会引发效率问题，当子View很复杂时，这个问题会更加严重。如果可以，尽量使用padding代替margin。
>   + 在不影响层级深度的情况下,使用LinearLayout和FrameLayout而不是RelativeLayout。

### JNI

#### 1、对JNI是否了解
> + 参考回答：
>   + Java的优点是**跨平台**，但也因为其跨平台的的特性导致其**本地交互的能力不够强大**，一些和操作系统相关的的特性Java无法完成，于是**Java提供JNI专门用于和本地代码交互，通过JNI，用户可以调用C、C++编写的本地代码**
>   + NDK是Android所提供的一个工具集合，通过NDK可以在Android中更加方便地通过JNI访问本地代码，其优点在于
>     + 提高代码的安全性。由于so库反编译困难，因此NDK提高了Android程序的安全性
>     + 可以很方便地使用目前已有的C/C++开源库
>     + 便于平台的移植。通过C/C++实现的动态库可以很方便地在其它平台上使用
>     + 提高程序在某些特定情形下的执行效率，但是并不能明显提升Android程序的性能

#### 2、如何加载NDK库 ？如何在JNI中注册Native函数，有几种注册方法 ？
> + 参考回答：
```
    public class JniTest{
        //加载NDK库 
        static{
            System.loadLirary("jni-test");
        }
    }

```
>   + 注册JNI函数的两种方法
>     + **静态方法**
>     + **动态注册**
>   + 推荐文章：
>     + [注册JNI函数的两种方式](https://blog.csdn.net/wwj_748/article/details/52347341)
>     + [Android JNI 篇 - 从入门到放弃](https://www.jianshu.com/p/3dab1be3b9a4)

#### 3、你用JNI来实现过什么功能 ？ 怎么实现的 ？（加密处理、影音方面、图形图像处理)
>  + 参考回答：
>  + 推荐文章：
>    + [Android JNI 篇 - ffmpeg 获取音视频缩略图](https://www.jianshu.com/p/411761bd5f5b)

### 设计模式
#### 1、你所知道的设计模式有哪些？
> + 参考回答：
>    + **创建型模式，共五种**：工厂方法模式、抽象工厂模式、单例模式、建造者模式、原型模式。
>    + **结构型模式，共七种**：适配器模式、装饰器模式、代理模式、外观模式、桥接模式、组合模式、享元模式。
>    + **行为型模式，共十一种**：策略模式、模板方法模式、观察者模式、迭代子模式、责任链模式、命令模式、备忘录 
模式、状态模式、访问者模式、中介者模式、解释器模式。

#### 2、谈谈MVC、MVP和MVVM，好在哪里，不好在哪里 ？
> + 参考回答：
>   + **MVC**:
>     + 视图层(View) 对应于xml布局文件和java代码动态view部分
>     + 控制层(Controller) MVC中Android的控制层是由Activity来承担的，Activity本来主要是作为初始化页面，展示数据的操作，但是因为XML视图功能太弱，所以Activity既要负责视图的显示又要加入控制逻辑，承担的功能过多。
>     + 模型层(Model) 针对业务模型，建立数据结构和相关的类，它主要负责网络请求，数据库处理，I/O的操作。
>   + 总结
>     + 具有一定的分层，model彻底解耦，controller和view并没有解耦层与层之间的交互尽量使用回调或者去使用消息机制去完成，尽量避免直接持有 controller和view在android中无法做到彻底分离，但在代码逻辑层面一定要分清业务逻辑被放置在model层，能够更好的复用和修改增加业务。
>   + **MVP**
>     + 通过引入接口BaseView，让相应的视图组件如Activity，Fragment去实现BaseView，实现了视图层的独立，通过中间层Preseter实现了Model和View的完全解耦。MVP**彻底解决**了MVC中View和Controller傻傻分不清楚的问题，但是随着业务逻辑的增加，一个页面可能会非常复杂，UI的改变是非常多，会有非常多的case，这样就会造成View的接口会很庞大。
>   + **MVVM**
>     + MVP中我们说过随着业务逻辑的增加，UI的改变多的情况下，会有非常多的跟UI相关的case，这样就会造成View的接口会很庞大。而MVVM就解决了这个问题，通过双向绑定的机制，实现数据和UI内容，只要想改其中一方，另一方都能够及时更新的一种设计理念，这样就省去了很多在View层中写很多case的情况，只需要改变数据就行。
>   + 三者如何选择？
>     + 如果项目简单，没什么复杂性，未来改动也不大的话，那就不要用设计模式或者架构方法，只需要将每个模块封装好，方便调用即可，不要为了使用设计模式或架构方法而使用。
>     + 对于偏向展示型的app，绝大多数业务逻辑都在后端，app主要功能就是展示数据，交互等，建议使用mvvm。
>     + 对于工具类或者需要写很多业务逻辑app，使用mvp或者mvvm都可。
> + 推荐文章：
>   + [MVC、MVP、MVVM，我到底该怎么选？](https://juejin.im/post/5b3a3a44f265da630e27a7e6)

#### 3、封装p层之后.如果p层数据过大,如何解决?
> + 参考回答：
>   + 对于MVP模式来说，P层如果数据逻辑过于臃肿，建议引入**RxJava**或则**Dagger**，越是复杂的逻辑，越能体现RxJava的优越性
> + 推荐文章：
>   + [（仿有道精品课）RxJava+OkHttp+Retrofit+Dagger2+MVP框架(kotlin版本)](https://juejin.im/post/5c6e601cf265da2dc675b69e#comment)

#### 4、是否能从Android中举几个例子说说用到了什么设计模式 ？
> + 参考回答：
>   + **AlertDialog、Notification源码中使用了Builder（建造者）模式完成参数的初始化**
>   + **Okhttp内部使用了责任链模式来完成每个Interceptor拦截器的调用**
>   + **RxJava的观察者模式；单例模式；GridView的适配器模式；Intent的原型模式**
>   + **日常开发的BaseActivity抽象工厂模式**


#### 5、装饰模式和代理模式有哪些区别 ？
> + 参考回答：
>   + 装饰器模式与代理模式的区别就在于
>     + 两者都是对类的方法进行**扩展**，但**装饰器模式**强调的是增强自身,在被装饰之后你能够在被增强的类上使用增强后的功能。
>     + 而**代理模式**则强调要让别人帮你去做一些本身与你业务没有太多关系的职责（记录日志、设置缓存）代理模式是为了实现对象的控制，因为被代理的对象往往难以直接获得或者是其内部不想暴露出来。

#### 6、实现单例模式有几种方法 ？懒汉式中双层锁的目的是什么 ？两次判空的目的又是什么 ？
> + 参考回答：
>   + 单例模式实现方法有多种：**饿汉，懒汉(线程安全，线程非安全)，双重检查(DCL),内部类，以及枚举**
>   + 所谓**双层检验锁（在加锁前后对实例对象进行两次判空的检验）**：加锁是为了第一次对象实例化的线程同步，而锁内还要有第二层判空是因为可能会有多个线程进入第一层if判断内部，而在加锁代码块外排队等候，如果锁内不进行第二次检验，仍然会出现实例化多个对象的情况。
> + 推荐文章：
>   + [单例模式的总结](https://xxxblank.github.io/2017/09/14/singleTon/)

#### 7、用到的一些开源框架，介绍一个看过源码的，内部实现过程。
> + 参考回答：
>   + 面试常客：Okhttp，Retrofit，Glide，RxJava，GreenDao，Dagger等
> + 推荐文章：
>   + [Android OkHttp源码解析入门教程（一）](https://juejin.im/post/5c46822c6fb9a049ea394510)
>   + [Android OkHttp源码解析入门教程（二）](https://juejin.im/post/5c4682d2f265da6130752a1d)

#### 8、Fragment如果在Adapter中使用应该如何解耦？
> + 参考回答：
>   + 接口回调
>   + 广播

### Android进阶延伸点

#### 1、如何进行单元测试，如何保证App稳定 ？
> + 参考回答：
>   + 要测试Android应用程序，通常会创建以下类型自动单元测试
>     + **本地测试**：只在本地机器JVM上运行，以最小化执行时间，这种单元测试不依赖于Android框架，或者即使有依赖，也很方便使用模拟框架来模拟依赖，以达到隔离Android依赖的目的，模拟框架如Google推荐的Mockito；
>     + [**Android官网-建立本地单元测试**](https://developer.android.com/training/testing/unit-testing/local-unit-tests.html)
>     + **检测测试**：真机或模拟器上运行的单元测试，由于需要跑到设备上，比较慢，这些测试可以访问仪器（Android系统）信息，比如被测应用程序的上下文，一般地，依赖不太方便通过模拟框架模拟时采用这种方式；
>     + [**Android官网-建立仪表单元测试**](https://developer.android.com/training/testing/unit-testing/instrumented-unit-tests.html)
>   + 注意：单元测试不适合测试复杂的UI交互事件
>   + 推荐文章：[Android 单元测试只看这一篇就够了](https://juejin.im/post/5b57e3fbf265da0f47352618)
>   + App的稳定主要决定于整体的系统架构设计，同时也不可忽略代码编程的细节规范，正所谓“千里之堤，溃于蚁穴”，一旦考虑不周，看似无关紧要的代码片段可能会带来整体软件系统的崩溃，所以上线之前除了自己**本地化测试**之外还需要进行**Monkey压力测试**
>   + 少部分面试官可能会延伸，如Gradle自动化测试、机型适配测试等
    
#### 2、Android中如何查看一个对象的回收情况 ？
> + 参考回答：
>   + 首先要了解Java四种引用类型的场景和使用（强引用、软引用、弱引用、虛引用）
>   + 举个场景例子：**SoftReference**对象是用来保存软引用的，但它同时也是一个Java对象，所以当软引用对象被回收之后，虽然这个**SoftReference**对象的get方法返回null，但**SoftReference**对象本身并不是null，而此时这个**SoftReference**对象已经不再具有存在的价值，需要一个适当的清除机制，避免大量**SoftReference**对象带来的**内存泄露**
>   + 因此，Java提供**ReferenceQueue**来处理引用对象的回收情况。当**SoftReference**所引用的对象被GC后，**JVM**会先将**softReference**对象添加到**ReferenceQueue**这个队列中。当我们调用**ReferenceQueue的poll()方法**，如果这个队列中不是空队列，那么将返回并移除前面添加的那个Reference对象。
![](https://user-gold-cdn.xitu.io/2019/3/27/169bd0c28741e8e0?w=1950&h=1058&f=png&s=262083)
> + 推荐文章：
>   + [Java中的四种引用类型：强引用、软引用、弱引用和虚引用](https://segmentfault.com/a/1190000015282652#articleHeader3)

#### 3、Apk的大小如何压缩 ？
> + 参考回答：
>   + 一个完整APK包含以下目录（将APK文件拖到Android Studio）：
>     + **META-INF/**：包含**CERT.SF**和**CERT.RSA**签名文件以及**MANIFEST.MF** 清单文件。
>     + **assets/**：包含应用可以使用**AssetManager**对象检索的应用资源。
>     + **res/**：包含未编译到的资源 resources.arsc。
>     + **lib/**：包含特定于处理器软件层的编译代码。该目录包含了每种平台的子目录，像**armeabi，armeabi-v7a， arm64-v8a，x86，x86_64，和mips**。
>     + **resources.arsc**：包含已编译的资源。该文件包含**res/values/** 文件夹所有配置中的XML内容。打包工具提取此XML内容，将其编译为二进制格式，并将内容归档。此内容包括语言字符串和样式，以及直接包含在**resources.arsc*8文件中的内容路径 ，例如布局文件和图像。
>     + **classes.dex**：包含以**Dalvik / ART**虚拟机可理解的**DEX**文件格式编译的类。
>     + **AndroidManifest.xml**：包含核心Android清单文件。该文件列出应用程序的名称，版本，访问权限和引用的库文件。该文件使用Android的二进制XML格式。
![](https://user-gold-cdn.xitu.io/2019/3/27/169bd48e53be9928?w=1878&h=415&f=png&s=52339)
>     + lib、class.dex和res占用了超过90%的空间，所以这三块是优化Apk大小的重点（实际情况不唯一）
>   + **减少res，压缩图文文件**
>     + 图片文件压缩是针对jpg和png格式的图片。我们通常会放置多套不同分辨率的图片以适配不同的屏幕，这里可以进行适当的删减。在实际使用中，只保留一到两套就足够了（保留一套的话建议保留xxhdpi，两套的话就加上hdpi），然后再对剩余的图片进行压缩(jpg采用优图压缩，png尝试采用pngquant压缩)
>   + **减少dex文件大小**
>     + 添加资源混淆
![](https://user-gold-cdn.xitu.io/2019/3/27/169be5f6fe340e53?w=566&h=141&f=png&s=9307)
>     + shrinkResources为true表示移除未引用资源，和代码压缩协同工作。
>     + minifyEnabled为true表示通过ProGuard启用代码压缩，配合proguardFiles的配置对代码进行混淆并移除未使用的代码。
>     + 代码混淆在压缩apk的同时，也提升了安全性。
>     + 推荐文章：[Android混淆最佳实践](https://www.jianshu.com/p/cba8ca7fc36d)
>   + **减少lib文件大小**
>     + 由于引用了很多第三方库，lib文件夹占用的空间通常都很大，特别是有so库的情况下。很多so库会同时引入armeabi、armeabi-v7a和x86这几种类型，这里可以只保留armeabi或armeabi-v7a的其中一个就可以了，实际上微信等主流app都是这么做的。
>     + 只需在build.gradle直接配置即可，NDK配置同理
![](https://user-gold-cdn.xitu.io/2019/3/27/169be64df0148df3?w=219&h=95&f=png&s=2712)
> + 推荐文章：
>   + [APK瘦身](https://www.jianshu.com/p/5921e9561f5f)

#### 4、如何通过Gradle配置多渠道包？
> + 参考回答：
>   + 首先要了解设置多渠道的原因。在安装包中添加不同的标识，配合自动化埋点，应用在请求网络的时候携带渠道信息，方便后台做运营统计，比如说统计我们的应用在不同应用市场的下载量等信息
>   + 这里以友盟统计为例
>     + 首先在manifest.xml文件中设置动态渠道变量：
![](https://user-gold-cdn.xitu.io/2019/3/28/169c30d0cdbfb111?w=486&h=103&f=png&s=7268)
>     + 接着在app目录下的build.gradle中配置productFlavors，也就是配置打包的渠道：
![](https://user-gold-cdn.xitu.io/2019/3/28/169c31116ef61848?w=661&h=356&f=png&s=41701)
>     + 最后在编辑器下方的Teminal输出命令行
>       + **执行./gradlew assembleRelease ，将会打出所有渠道的release包；**
>       + **执行./gradlew assembleVIVO，将会打出VIVO渠道的release和debug版的包；**
>       + **执行./gradlew assembleVIVORelease将生成VIVO的release包。**
> + 推荐文章：
>   + [美团Android自动化之旅—Walle生成渠道包](https://github.com/Meituan-Dianping/walle)

#### 5、插件化原理分析
> + 参考回答：
>   + **插件化**是指将 APK 分为**宿主**和**插件**的部分。把需要实现的模块或功能当做一个独立的提取出来，在 APP 运行时，我们可以动态的**载入**或者**替换插件**部分，减少**宿主**的规模
>       + 宿主： 就是当前运行的APP。 
>       + 插件： 相对于插件化技术来说，就是要加载运行的apk类文件。
>   + 而**热修复**则是从修复bug的角度出发，强调的是在不需要二次安装应用的前提下修复已知的bug。能
![](https://user-gold-cdn.xitu.io/2019/4/7/169f6c21ae14fee0?w=1208&h=1134&f=jpeg&s=321789)
>   + **类加载机制**
>     + Android中常用的两种类加载器，**DexClassLoader**和**PathClassLoader**，它们都继承于**BaseDexClassLoader**，两者**区别**在于**PathClassLoader**只能加载**内部存储目录**的dex/jar/apk文件。**DexClassLoader**支持加载**指定目录**(不限于内部)的dex/jar/apk文件
>   + **插件通信**：通过给插件apk生成相应的DexClassLoader便可以访问其中的类，可分为单DexClassLoader和多DexClassLoader两种结构。
>     + 若使用**多ClassLoader机制**，主工程引用插件中类需要先通过插件的ClassLoader加载该类再通过**反射**调用其方法。插件化框架一般会通过统一的入口去管理对各个插件中类的访问，并且做一定的限制。
>     + 若使用**单ClassLoader机制**，主工程则可以**直接通过**类名去访问插件中的类。该方式有个弊端，若两个不同的插件工程引用了一个库的不同版本，则程序可能会出错。
>   + **资源加载**
>     + 原理在于通过反射将插件apk的路径加入AssetManager中并创建Resource对象加载资源，有两种处理方式：
>       + 合并式：addAssetPath时加入所有插件和主工程的路径；由于AssetManager中加入了所有插件和主工程的路径，因此生成的Resource可以同时访问插件和主工程的资源。但是由于主工程和各个插件都是独立编译的，生成的资源id会存在相同的情况，在访问时会产生资源冲突。
>       + 独立式：各个插件只添加自己apk路径，各个插件的资源是互相隔离的，不过如果想要实现资源的共享，必须拿到对应的Resource对象。
> + 推荐文章：
>   + [Android动态加载技术 简单易懂的介绍方式](https://segmentfault.com/a/1190000004062866#articleHeader1)
>   + [深入理解Android插件化技术](https://yq.aliyun.com/articles/361233?utm_content=m_40296)
>   + [为什么要做热更新](https://www.cnblogs.com/baiqiantao/p/9160806.html)

#### 6、组件化原理
> + 参考回答：
>   + **引入组件化的原因**：项目随着需求的增加规模变得越来越大，规模的增大导致了各种业务错中复杂的交织在一起, 每个业务模块之间，代码没有约束，带来了代码边界的模糊，代码冲突时有发生, 更改一个小问题可能引起一些新的问题, 牵一发而动全身，增加一个新需求，需要熟悉相关的代码逻辑，增加开发时间
>     + **避免重复造轮子，可以节省开发和维护的成本。**
>     + **可以通过组件和模块为业务基准合理地安排人力，提高开发效率。**
>     + **不同的项目可以共用一个组件或模块，确保整体技术方案的统一性。**
>     + **为未来插件化共用同一套底层模型做准备。**
>   + **组件化开发流程**就是把一个功能完整的App或模块拆分成**多个子模块（Module）**，每个子模块可以**独立编译运行**，也可以任意组合成另一个新的 App或模块，每个模块即不相互依赖但又可以相互交互，但是最终发布的时候是将这些组件合并统一成一个apk，遇到某些特殊情况甚至可以**升级**或者**降级**
>   + 举个简单的模型例子
![APP架构图](https://user-gold-cdn.xitu.io/2019/4/7/169f692c69f60c06?w=932&h=806&f=png&s=39959)
![APP代码结构图](https://user-gold-cdn.xitu.io/2019/4/7/169f690af8eea279?w=716&h=750&f=png&s=214708)
App是主application，ModuleA和ModuleB是两个业务模块（**相对独立，互不影响**），Library是基础模块，包含所有模块需要的依赖库，以及一些工具类：如网络访问、时间工具等
>    + **注意：提供给各业务模块的基础组件，需要根据具体情况拆分成 aar 或者 library，像登录，基础网络层这样较为稳定的组件，一般直接打包成 aar，减少编译耗时。而像自定义 View 组件，由于随着版本迭代会有较多变化，就直接以源码形式抽离成 Library**
> + 推荐文章：
>   + [干货 | 从智行 Android 项目看组件化架构实践](https://mp.weixin.qq.com/s?__biz=MjM5MDI3MjA5MQ==&mid=2697268363&idx=1&sn=3db2dce36a912936961c671dd1f71c78&scene=21#wechat_redirect)


#### 7、跨组件通信
> + 参考回答：
>   + **跨组件通信场景：**
>     + 第一种是组件之间的页面跳转 (Activity 到 Activity, Fragment 到 Fragment, Activity 到 Fragment, Fragment 到 Activity) 以及跳转时的数据传递 (基础数据类型和可序列化的自定义类类型)。
>     + 第二种是组件之间的自定义类和自定义方法的调用(组件向外提供服务)。
>   + **跨组件通信方案分析**：
>     + 第一种**组件之间的页面跳转**实现简单，跳转时想传递不同类型的数据提供有相应的 API即可。
>     + 第二种组件之间的自定义类和**自定义方法的调用**要稍微复杂点，需要 ARouter 配合架构中的 公共服务(CommonService) 实现：
>       + 提供服务的业务模块：
>       + 在公共服务(CommonService) 中声明 Service 接口 (含有需要被调用的自定义方法), 然后在自己的模块中实现这个 Service 接口, 再通过 ARouter API 暴露实现类。
>       + 使用服务的业务模块：
>         + 通过 ARouter 的 API 拿到这个 Service 接口(多态持有, 实际持有实现类), 即可调用 Service 接口中声明的自定义方法, 这样就可以达到模块之间的交互。
>       + 此外，可以使用 AndroidEventBus 其独有的 Tag, 可以在开发时更容易定位发送事件和接受事件的代码, 如果以组件名来作为 Tag 的前缀进行分组, 也可以更好的统一管理和查看每个组件的事件, 当然也不建议大家过多使用 EventBus。
>   + **如何管理过多的路由表？**
>     + RouterHub 存在于基础库, 可以被看作是所有组件都需要遵守的通讯协议, 里面不仅可以放路由地址常量, 还可以放跨组件传递数据时命名的各种 Key 值, 再配以适当注释, 任何组件开发人员不需要事先沟通只要依赖了这个协议, 就知道了各自该怎样协同工作, 既提高了效率又降低了出错风险, 约定的东西自然要比口头上说强。
>     + Tips: 如果您觉得把每个路由地址都写在基础库的 RouterHub 中, 太麻烦了, 也可以在每个组件内部建立一个私有 RouterHub, 将不需要跨组件的路由地址放入私有 RouterHub 中管理, 只将需要跨组件的路由地址放入基础库的公有 RouterHub 中管理, 如果您不需要集中管理所有路由地址的话, 这也是比较推荐的一种方式。
>   + **ARouter路由原理：**
>     + ARouter维护了一个路由表Warehouse，其中保存着全部的模块跳转关系，ARouter路由跳转实际上还是调用了startActivity的跳转，使用了原生的Framework机制，只是通过apt注解的形式制造出跳转规则，并人为地拦截跳转和设置跳转条件。
>  + 常见的组件化方案如下
![](https://user-gold-cdn.xitu.io/2019/4/7/169f6a3d472ef431?w=1828&h=945&f=png&s=615437)

#### 8、组件化中路由、埋点的实现
> + 参考回答：
>    + 因为在组件化中，各个业务模块之间是各自**独立**的, 并不会存在相互依赖的关系, 所以一个业务模块是访问不了其他业务模块的代码的, 如果想从 A 业务模块的 A 页面跳转到 B 业务模块的 B 页面, 光靠模块自身是不能实现的，这就需要一种跨组件通信方案—— **路由（Router）**
>    + **路由**主要有以下两种场景:
>      + 第一种是**组件之间的页面跳转** (Activity 到 Activity, Fragment 到 Fragment, Activity 到 Fragment, Fragment 到 Activity) 以及跳转时的数据传递 (基础数据类型和可序列化的自定义类类型)
>      + 第二种是**组件之间的自定义类**和**自定义方法的调用**(组件向外提供服务)
>    + 其**原理**在于将分布在不同组件module中的某些类按照一定规则生成映射表（数据结构通常是Map，Key为一个字符串，Value为类或对象），然后在需要用到的时候从映射表中根据字符串从映射表中取出类或对象，本质上是类的查找
>    + 埋点则是在应用中特定的流程收集一些信息，用来跟踪应用使用的状况
>      + **代码埋点**：在某个事件发生时调用SDK里面相应的接口发送埋点数据，百度统计、友盟、TalkingData、Sensors Analytics等第三方数据统计服务商大都采用这种方案
>      + **全埋点**：全埋点指的是将Web页面/App内产生的所有的、满足某个条件的行为，全部上报到后台服务器
>      + **可视化埋点**：通过可视化工具（例如Mixpanel）配置采集节点，在Android端自动解析配置并上报埋点数据，从而实现所谓的**自动埋点**
>      + **无埋点**：它并不是真正的不需要埋点，而是Android端自动采集全部事件并上报埋点数据，在后端数据计算时过滤出有用数据
> + 推荐文章：
>   + [安卓组件化开源方案实现](https://juejin.im/post/5a7ab8846fb9a0634514a2f5)

#### 9、Hook以及插桩技术
> + 参考回答：
>   + **Hook**是一种用于**改变API执行结果**的技术，能够将系统的API函数执行**重定向**（应用的**触发事件**和**后台逻辑处理**是根据事件流程一步步地向下执行。而**Hook**的意思，就是在事件传送到终点前截获并监控事件的传输，像个钩子钩上事件一样，并且能够在钩上事件时，处理一些自己特定的事件，例如逆向破解App）
![](https://user-gold-cdn.xitu.io/2019/4/17/16a2a2f8cf3e4448?w=625&h=211&f=png&s=14871)
>   +  Android 中的 Hook 机制，大致有两个方式：
>      +  要 root 权限，直接 Hook 系统，可以干掉所有的 App。
>      +  无 root 权限，但是只能 Hook 自身app，对系统其它 App 无能为力。
>   +  **插桩**是以静态的方式修改第三方的代码，也就是从编译阶段，对源代码（中间代码）进行编译，而后重新打包，是**静态的篡改**； 而**Hook**则不需要再编译阶段修改第三方的源码或中间代码，是在运行时通过反射的方式修改调用，是一种**动态的篡改**
> + 推荐文章：
>   + [Android插件化原理解析——Hook机制之动态代理](http://weishu.me/2016/01/28/understand-plugin-framework-proxy-hook/)
>   + [android 插桩基本概念](https://blog.csdn.net/fei20121106/article/details/51879047)
>   + [Android逆向之旅](http://www.520monkey.com/)

#### 10、Android的签名机制？
> + 参考回答：
>   + Android的签名机制包含有**消息摘要**、**数字签名**和**数字证书**
>     + **消息摘要**：在消息数据上，执行一个单向的 Hash 函数，生成一个固定长度的Hash值
>     + **数字签名**：一种以电子形式存储消息签名的方法，一个完整的数字签名方案应该由两部分组成：**签名算法和验证算法**
>     + **数字证书**：一个经证书授权（Certificate Authentication）中心数字签名的包含公钥拥有者信息以及公钥的文件
> + 推荐文章：
>   + [一篇文章看明白 Android v1 & v2 签名机制](https://blog.csdn.net/freekiteyu/article/details/84849651)

#### 11、v3签名key和v2还有v1有什么区别
> + 参考回答：
>   + 在**v1版本**的签名中，签名以文件的形式存在于apk包中，这个版本的apk包就是一个标准的zip包，**V2**和**V1**的差别是**V2**是对整个zip包进行签名，而且在zip包中增加了一个**apk signature block**，里面保存签名信息。
![](https://user-gold-cdn.xitu.io/2019/4/1/169d7c3ea2437de3?w=1692&h=520&f=png&s=77618)
>   + **v2版本**签名块（APK Signing Block）本身又主要分成三部分:
>     + **SignerData**（签名者数据）：主要包括签名者的证书，整个APK完整性校验hash，以及一些必要信息
>     + **Signature**（签名）：开发者对SignerData部分数据的签名数据
>     + **PublicKey**（公钥）：用于验签的公钥数据
>   + **v3版本**签名块也分成同样的三部分，与v2不同的是在SignerData部分，v3新增了attr块，其中是由更小的level块组成。每个level块中可以存储一个证书信息。前一个level块证书验证下一个level证书，以此类推。最后一个level块的证书，要符合SignerData中本身的证书，即用来签名整个APK的公钥所属于的证书
![](https://user-gold-cdn.xitu.io/2019/4/1/169d7c5908d3c159?w=1643&h=1790&f=png&s=101056)
> + 推荐文章：
>   + [APK 签名方案 v3](https://source.android.google.cn/security/apksigning/v3)
>   + [Android P v3签名新特性](https://xuanxuanblingbling.github.io/ctf/android/2018/12/30/signature/)

#### 12、Android5.0~10.0之间大的变化
> + 参考回答：
>   + **Android5.0新特性**
>     + **MaterialDesign设计风格**
>     + **支持64位ART虚拟机**（5.0推出的ART虚拟机，在5.0之前都是Dalvik。他们的区别是：Dalvik,每次运行,字节码都需要通过即时编译器转换成机器码(JIT)。 ART,第一次安装应用的时候,字节码就会预先编译成机器码(AOT)）
>     + 通知详情可以用户自己设计
>   + **Android6.0新特性**
>     + **动态权限管理**
>     + 支持快速充电的切换
>     + 支持文件夹拖拽应用
>     + 相机新增专业模式
>   + **Android7.0新特性**
>     + **多窗口支持**
>     + **V2签名**
>     + 增强的Java8语言模式
>     + 夜间模式
>   + **Android8.0（O）新特性**
>     + **优化通知**：通知渠道 (Notification Channel) 通知标志 休眠 通知超时 通知设置 通知清除
>     + **画中画模式**：清单中Activity设置android:supportsPictureInPicture
>     + **后台限制**
>     + 自动填充框架
>     + 系统优化
>     + 等等优化很多
>   + **Android9.0（P）新特性**
>     + **室内WIFI定位**
>     + **“刘海”屏幕支持**
>     + 安全增强
>     + 等等优化很多
>   + **Android10.0（Q）新特性**
>     + **夜间模式**：包括手机上的所有应用都可以为其设置暗黑模式。
>     + **桌面模式**：提供类似于PC的体验，但是远远不能代替PC。
>     + **屏幕录制**：通过长按“电源”菜单中的"屏幕快照"来开启。
> + 推荐文章：
>   + [Android Developers 官方文档](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html#ApiLevels)

#### 13、说下Measurepec这个类
> + 参考回答：
>   + 作用：通过宽测量值**widthMeasureSpec**和高测量值**heightMeasureSpec**决定View的大小
>   + 组成：一个32位int值，高2位代表**SpecMode**(测量模式)，低30位代表**SpecSize**( 某种测量模式下的规格大小)。
>   + 三种模式：
>     + **UNSPECIFIED**：父容器不对View有任何限制，要多大有多大。常用于系统内部。
>     + **EXACTLY**(精确模式)：父视图为子视图指定一个确切的尺寸SpecSize。对应LyaoutParams中的match_parent或具体数值。
>     + **AT_MOST**(最大模式)：父容器为子视图指定一个最大尺寸SpecSize，View的大小不能大于这个值。对应LayoutParams中的wrap_content。
>   + 决定因素：值由**子View的布局参数LayoutParams**和父容器的**MeasureSpec**值共同决定。具体规则见下图：![](https://user-gold-cdn.xitu.io/2019/4/1/169d7a649cc67de5?w=700&h=230&f=png&s=60337)

#### 14、请例举Android中常用布局类型，并简述其用法以及排版效率
> + 参考回答：
>   + Android中常用布局分为**传统布局**和**新型布局**
>     + 传统布局（编写XML代码、代码生成）：
>       + **框架布局（FrameLayout）**：
>       + **线性布局（LinearLayout）**：
>       + **绝对布局（AbsoluteLayout）**：
>       + **相对布局（RelativeLayout）**：
>       + **表格布局（TableLayout）**：
>     + 新型布局（**可视化拖拽控件**、编写XML代码、代码生成）：
>       + **约束布局（ConstrainLayout）**：
![](https://user-gold-cdn.xitu.io/2019/4/18/16a2f8e1327c53b4?w=1220&h=950&f=png&s=183074)
>       + 注：图片出自Carson_Ho的[Android：常用布局介绍&属性设置大全](https://blog.csdn.net/carson_ho/article/details/51719519)
>     + 对于嵌套多层View而言，其排版效率：**LinearLayout = FrameLayout >> RelativeLayout**

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
