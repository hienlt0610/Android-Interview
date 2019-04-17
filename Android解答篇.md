# Android-Interview
>注：因为实际开发与参考答案会有所不同，再者怕误导大家，所以这些面试题答案还是自己去理解！面试官会针对简历中提到的知识点由浅入深提问，所以不要背答案，多理解。
## Android篇

### Activity

#### 1、说下Activity生命周期 ？
> + 参考解答：在正常情况下，Activity的常用生命周期就只有如下7个
>   + **onCreate()**：表示Activity**正在被创建**，常用来**初始化工作**，比如调用setContentView加载界面布局资源，初始化Activity所需数据等；
>   + **onRestart()**：表示Activity**正在重新启动**，一般情况下，当前Acitivty从不可见重新变为可见时，OnRestart就会被调用；
>   + **onStart()**：表示Activity**正在被启动**，此时Activity**可见但不在前台**，还处于后台，无法与用户交互；
>   + **onResume()**：表示Activity**获得焦点**，此时Activity**可见且在前台**并开始活动，这是与onStart的区别所在；
>   + **onPause()**：表示Activity**正在停止**，此时可做一些**存储数据、停止动画**等工作，但是不能太耗时，因为这会影响到新Activity的显示，onPause必须先执行完，新Activity的onResume才会执行；
>   + **onStop()**：表示Activity**即将停止**，可以做一些稍微重量级的回收工作，比如注销广播接收器、关闭网络连接等，同样不能太耗时；
>   + **onDestroy()**：表示Activity**即将被销毁**，这是Activity生命周期中的最后一个回调，常做**回收工作、资源释放**；
> + 延伸：从**整个生命周期**来看，onCreate和onDestroy是配对的，分别标识着Activity的创建和销毁，并且只可能有**一次调用**；
从Activity**是否可见**来说，onStart和onStop是配对的，这两个方法可能被**调用多次**；
从Activity**是否在前台**来说，onResume和onPause是配对的，这两个方法可能被**调用多次**；
除了这种区别，在实际使用中没有其他明显区别；

#### 2、Activity A 启动另一个Activity B 会调用哪些方法？如果B是透明主题的又或则是个DialogActivity呢 ？
> + 参考解答：Activity A 启动另一个Activity B，回调如下
>   + Activity A 的onPause() → Activity B的onCreate() → onStart() → onResume() → Activity A的onStop()；
>   + 如果B是透明主题又或则是个DialogActivity，则不会回调A的onStop；

#### 3、说下onSaveInstanceState()方法的作用 ? 何时会被调用？
> + 参考解答：发生条件：异常情况下（**系统配置发生改变时导致Activity被杀死并重新创建、资源内存不足导致低优先级的Activity被杀死**）
>   + 系统会调用onSaveInstanceState来保存当前Activity的状态，此方法调用在onStop之前，与onPause没有既定的时序关系；
>   + 当Activity被重建后，系统会调用onRestoreInstanceState，并且把onSave(简称)方法所保存的Bundle对象**同时传参**给onRestore(简称)和onCreate()，因此可以通过这两个方法判断Activity**是否被重建**，调用在onStart之后；
![异常情况下Activity的重建过程](https://user-gold-cdn.xitu.io/2019/3/8/1695c1aaea26f833?w=583&h=381&f=png&s=10238)

#### 4、说下 Activity的四种启动模式、应用场景 ？
> + 参考回答：
>   + **standard标准模式**：每次启动一个Activity都会重新创建一个新的实例，不管这个实例是否已经存在，此模式的Activity默认会进入启动它的Activity所属的任务栈中；
>   + **singleTop栈顶复用模式**：如果新Activity已经位于任务栈的栈顶，那么此Activity不会被重新创建，同时会回调**onNewIntent**方法，如果新Activity实例已经存在但不在栈顶，那么Activity依然会被重新创建；
>   + **singleTask栈内复用模式**：只要Activity在一个任务栈中存在，那么多次启动此Activity都不会重新创建实例，并回调**onNewIntent**方法，此模式启动Activity A，系统首先会寻找是否存在A想要的任务栈，如果不存在，就会重新创建一个任务栈，然后把创建好A的实例放到栈中；
>   + **singleInstance单实例模式**：这是一种加强的singleTask模式，具有此种模式的Activity只能单独地位于一个任务栈中，且此任务栈中只有唯一一个实例；

#### 5、了解哪些Activity常用的标记位Flags？
> + **FLAG_ACTIVITY_NEW_TASK :** 对应singleTask启动模式，其效果和在XML中指定该启动模式相同；
> + **FLAG_ACTIVITY_SINGLE_TOP :** 对应singleTop启动模式，其效果和在XML中指定该启动模式相同；
> + **FLAG_ACTIVITY_CLEAR_TOP :** 具有此标记位的Activity，当它启动时，在同一个任务栈中所有位于它上面的Activity都要出栈。这个标记位一般会和singleTask模式一起出现，在这种情况下，被启动Activity的实例如果已经存在，那么系统就会回调onNewIntent。如果被启动的Activity采用standard模式启动，那么它以及连同它之上的Activity都要出栈，系统会创建新的Activity实例并放入栈中；
> + **FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS :** 具有这个标记的 Activity 不会出现在历史 Activity 列表中；

#### 6、说下 Activity跟window，view之间的关系？
> + 参考回答：
>   +  Activity 创建时通过attach()初始化了一个 Window 也就是 PhoneWindow，一个 PhoneWindow 持有一个 DecorView 的实例，DecorView 本身是一个 FrameLayout，继承于View，Activty通过setContentView将xml布局控件不断addView()添加到View中，最终显示到Window于我们交互；
>   + 推荐文章：
[Activity、View、Window的理解一篇文章就够了](https://blog.csdn.net/zane402075316/article/details/69822438)

#### 7、横竖屏切换的Activity生命周期变化？
> + 参考回答：
>   + 不设置Activity的android:configChanges时，切屏会销毁当前Activity，然后重新加载调用各个生命周期，切横屏时会执行一次，切竖屏时会执行两次；
onPause() →onStop()→onDestory()→onCreate()→onStart()→onResume()
>   + 设置Activity的android:configChanges="orientation"时，切屏还是会重新调用各个生命周期，切横、竖屏时只会执行一次；
>   + 设置Activity的android:configChanges="orientation|keyboardHidden|screenSize"时，切屏不会重新调用各个生命周期，只会执行onConfigurationChanged方法；
>   + 推荐文章：
[Android 横竖屏切换加载不同的布局](https://blog.csdn.net/u010365819/article/details/76618443)

#### 8、如何启动其他应用的Activity？
> + 参考回答：
>   + 在保证有权限访问的情况下，通过隐式Intent进行目标Activity的IntentFilter匹配，原则是：
>       + 一个intent只有同时匹配某个Activity的intent-filter中的action、category、data才算完全匹配，才能启动该Activity；
>       + 一个Activity可以有多个 intent-filter，一个 intent只要成功匹配任意一组 intent-filter，就可以启动该Activity；
>   + 推荐文章：
[action、category、data的具体匹配规则](https://blog.csdn.net/sunzhaojie613/article/details/77433994)

#### 9、Activity的启动过程？(重点)
> + 参考回答：
>   + **点击App图标后通过startActivity远程调用到AMS中，AMS中将新启动的activity以activityrecord的结构压入activity栈中，并通过远程binder回调到原进程，使得原进程进入pause状态，原进程pause后通知AMS我pause了**
>   + **此时AMS再根据栈中Activity的启动intent中的flag是否含有new_task的标签判断是否需要启动新进程，启动新进程通过startProcessXXX的函数**
>   + **启动新进程后通过反射调用ActivityThread的main函数，main函数中调用looper.prepar和lopper.loop启动消息队列循环机制。最后远程告知AMS我启动了。AMS回调handleLauncherAcitivyt加载activity。在handlerLauncherActivity中会通过反射调用Application的onCreate和activity的onCreate以及通过handleResumeActivity中反射调用Activity的onResume**
![](https://user-gold-cdn.xitu.io/2019/3/8/1695c1aaec5faf60?w=769&h=581&f=png&s=188283)
> + 推荐文章：
[Android四大组件启动机制之Activity启动过程](https://blog.csdn.net/qq_30379689/article/details/79611217)

### Fragment

#### 1、谈一谈Fragment的生命周期？
> + 参考回答：
>   + Fragment从创建到销毁整个生命周期中涉及到的方法依次为：onAttach()→onCreate()→ onCreateView()→onActivityCreated()→onStart()→onResume()→onPause()→onStop()→onDestroyView()→onDestroy()→onDetach()，其中和Activity有不少名称相同作用相似的方法，而不同的方法有:
>      + **onAttach()**：当Fragment和Activity建立关联时调用；
>      + **onCreateView()**：当fragment创建视图调用，在onCreate之后；
>      + **onActivityCreated()**：当与Fragment相关联的Activity完成onCreate()之后调用；
>      + **onDestroyView()**：在Fragment中的布局被移除时调用；
>      + **onDetach()**：当Fragment和Activity解除关联时调用；
> + 推荐文章：[Android之Fragment优点](https://www.cnblogs.com/shaweng/p/3918985.html)

#### 2、谈谈Activity和Fragment的区别？
> + 参考回答：
>   + 相似点：都可包含布局、可有自己的生命周期
>   + 不同点：
>      + Fragment相比较于Activity多出4个回调周期，在控制操作上更灵活；
>      + Fragment可以在XML文件中直接进行写入，也可以在Activity中动态添加；
>      + Fragment可以使用show()/hide()或者replace()随时对Fragment进行切换，并且切换的时候不会出现明显的效果，用户体验会好；Activity虽然也可以进行切换，但是Activity之间切换会有明显的翻页或者其他的效果，在小部分内容的切换上给用户的感觉不是很好；

#### 3、Fragment中add与replace的区别（Fragment重叠）
> + 参考回答：
>   + add不会重新初始化fragment，replace每次都会。所以如果在fragment生命周期内获取获取数据,使用replace会重复获取；
>   + 添加相同的fragment时，replace不会有任何变化，add会报IllegalStateException异常；
>   + replace先remove掉相同id的所有fragment，然后在add当前的这个fragment，而add是覆盖前一个fragment。所以如果使用add一般会伴随hide()和show()，避免布局重叠；
>   + 使用add，如果应用放在后台，或以其他方式被系统销毁，再打开时，hide()中引用的fragment会销毁，所以依然会出现布局重叠bug，可以使用replace或使用add时，添加一个tag参数；
![](https://user-gold-cdn.xitu.io/2019/3/14/1697b9862e1ce518?w=1800&h=878&f=png&s=215465)

#### 4、getFragmentManager、getSupportFragmentManager 、getChildFragmentManager之间的区别？
> + 参考回答：
>   + getFragmentManager()所得到的是所在fragment 的**父容器**的管理器，
getChildFragmentManager()所得到的是在fragment  里面**子容器**的管理器，
如果是fragment嵌套fragment，那么就需要利用getChildFragmentManager()；
>   + 因为Fragment是3.0 Android系统API版本才出现的组件，所以3.0以上系统可以直接调用getFragmentManager()来获取FragmentManager()对象，而3.0以下则需要调用getSupportFragmentManager() 来间接获取；

#### 5、FragmentPagerAdapter与FragmentStatePagerAdapter的区别与使用场景
>  + 参考回答：
>    + 相同点 ：二者都继承PagerAdapter
>    + 不同点 ：**FragmentPagerAdapter**的每个Fragment会持久的保存在FragmentManager中，只要用户可以返回到页面中，它都不会被销毁。因此适用于那些数据**相对静态**的页，Fragment**数量也比较少**的那种；
**FragmentStatePagerAdapter**只保留当前页面，当页面不可见时，该Fragment就会被消除，释放其资源。因此适用于那些**数据动态性**较大、**占用内存**较多，多Fragment的情况；

### Service

#### 1、谈一谈Service的生命周期？
> + 参考回答：Service的生命周期涉及到六大方法
>   + **onCreate()**：如果service没被创建过，调用startService()后会执行onCreate()回调；如果service已处于运行中，调用startService()不会执行onCreate()方法。也就是说，onCreate()只会在第一次创建service时候调用，多次执行startService()不会重复调用onCreate()，此方法适合完成一些初始化工作；
>   + **onStartComand()**：服务启动时调用，此方法适合完成一些数据加载工作，比如会在此处创建一个线程用于下载数据或播放音乐；
>   + **onBind()**：服务被绑定时调用；
>   + **onUnBind()**：服务被解绑时调用；
>   + **onDestroy()**：服务停止时调用；
> + 推荐文章：
[Android组件系列----Android Service组件深入解析](https://www.cnblogs.com/smyhvae/p/4070518.html)

#### 2、Service的两种启动方式？区别在哪？
> + 参考回答：Service的两种启动模式
>   + **startService()**：通过这种方式调用startService，onCreate()只会被调用一次，多次调用startSercie会多次执行onStartCommand()和onStart()方法。如果外部没有调用stopService()或stopSelf()方法，service会一直运行。
>   + **bindService()**：如果该服务之前**还没创建**，系统回调顺序为onCreate()→onBind()。如果调用bindService()方法前服务**已经被绑定**，多次调用bindService()方法不会多次创建服务及绑定。如果调用者希望与正在绑定的服务**解除绑定**，可以调用unbindService()方法，回调顺序为onUnbind()→onDestroy()；
![](https://user-gold-cdn.xitu.io/2019/3/8/1695c1aaec35fdba?w=407&h=520&f=png&s=58930)
> + 推荐文章：
[Android Service两种启动方式详解](https://www.jianshu.com/p/4c798c91a613)

#### 3、如何保证Service不被杀死 ？
> + 参考回答：
>   + **onStartCommand方式中，返回START_STICKY或则START_REDELIVER_INTENT**
>     + START_STICKY：如果返回START_STICKY，表示Service运行的进程被Android系统强制杀掉之后，Android系统会将该Service依然设置为started状态（即运行状态），但是不再保存onStartCommand方法传入的intent对象
>     + START_NOT_STICKY：如果返回START_NOT_STICKY，表示当Service运行的进程被Android系统强制杀掉之后，不会重新创建该Service
>     + START_REDELIVER_INTENT：如果返回START_REDELIVER_INTENT，其返回情况与START_STICKY类似，但不同的是系统会保留最后一次传入onStartCommand方法中的Intent再次保留下来并再次传入到重新创建后的Service的onStartCommand方法中
>   + **提高Service的优先级**
在AndroidManifest.xml文件中对于intent-filter可以通过android:priority = "1000"这个属性设置最高优先级，1000是最高值，如果数字越小则优先级越低，同时适用于广播；
>   + **在onDestroy方法里重启Service**
当service走到onDestroy()时，发送一个自定义广播，当收到广播时，重新启动service；
>   + **提升Service进程的优先级**
进程优先级由高到低：前台进程 一 可视进程 一 服务进程 一 后台进程 一 空进程
可以使用startForeground将service放到前台状态，这样低内存时，被杀死的概率会低一些；
>   + **系统广播监听Service状态**
>   + **将APK安装到/system/app，变身为系统级应用**
> + **注意**：以上机制都不能百分百保证Service不被杀死，除非做到系统白名单，与系统同生共死

#### 4、能否在Service开启耗时操作 ？ 怎么做 ？
> + 参考回答：
>   + Service默认并不会运行在子线程中，也不运行在一个独立的进程中，它同样执行在主线程中（UI线程）。换句话说，不要在Service里执行耗时操作，除非手动打开一个子线程，否则有可能出现主线程被阻塞（ANR）的情况；

#### 5、用过哪些系统Service ？
> + 参考回答：
![](https://user-gold-cdn.xitu.io/2019/3/8/1695c1aaecb6ae48?w=560&h=306&f=png&s=139877)

#### 6、了解ActivityManagerService吗？发挥什么作用
> + 参考回答：
ActivityManagerService是Android中最核心的服务 ， 主要负责系统中四大组件的启动、切换、调度及应用进程的管理和调度等工作，其职责与操作系统中的进程管理和调度模块类似；
> + 推荐文章：
[ActivityManagerService分析——AMS启动流程](https://blog.csdn.net/caohang103215/article/details/79597260)

### Broadcast Receiver

#### 1、广播有几种形式 ? 都有什么特点 ？
> + 参考回答：
>   + 普通广播：开发者自身定义 intent的广播（最常用），所有的广播接收器几乎会在同一时刻接受到此广播信息，**接受的先后顺序随机**；
>   + 有序广播：发送出去的广播被广播接收者**按照先后顺序接收**，同一时刻只会有一个广播接收器能够收到这条广播消息，当这个广播接收器中的逻辑执行完毕后，广播才会继续传递，且优先级（priority）高的广播接收器会先收到广播消息。有序广播可以被接收器截断使得后面的接收器无法收到它；
>   + 本地广播：仅在自己的应用内发送接收广播，也就是只有自己的应用能收到，数据更加安全，效率更高，但只能采用**动态注册**的方式；
>   + 粘性广播：这种广播会**一直滞留**，当有匹配该广播的接收器被注册后，该接收器就会收到此条广播；
> + 推荐文章：
[Android四大组件：BroadcastReceiver史上最全面解析](https://www.jianshu.com/p/ca3d87a4cdf3)

#### 2、广播的两种注册方式 ？
> + 参考回答：
![](https://user-gold-cdn.xitu.io/2019/3/8/1695c1aaec0b6b71?w=1025&h=240&f=png&s=54491)

#### 3、广播发送和接收的原理了解吗 ？（Binder机制、AMS）
> + 参考回答：
![](https://user-gold-cdn.xitu.io/2019/3/8/1695c1aaeac26f8d?w=576&h=386&f=png&s=12604)
> + 推荐文章：
[广播的底层实现原理](https://www.jianshu.com/p/02085150339c)

### ContentProvider

#### 1、ContentProvider了解多少？
> + 参考回答：
ContentProvider作为四大组件之一，其主要负责存储和共享数据。与文件存储、SharedPreferences存储、SQLite数据库存储这几种数据存储方法不同的是，后者保存下的数据只能被该应用程序使用，而前者可以让不同应用程序之间进行数据共享，它还可以选择只对哪一部分数据进行共享，从而保证程序中的隐私数据不会有泄漏风险。
>  + 推荐文章：
[Android：关于ContentProvider的知识都在这里了！](https://blog.csdn.net/carson_ho/article/details/76101093)

#### 2、ContentProvider的权限管理？
> + 参考回答：
>   + 读写分离
>   + 权限控制-精确到表级
>   + URL控制

#### 3、说说ContentProvider、ContentResolver、ContentObserver 之间的关系？
> + 参考回答：
>   + **ContentProvider**：管理数据，提供数据的增删改查操作，数据源可以是数据库、文件、XML、网络等，ContentProvider为这些数据的访问提供了统一的接口，可以用来做进程间数据共享。
>   + **ContentResolver**：ContentResolver可以为不同URI操作不同的ContentProvider中的数据，外部进程可以通过ContentResolver与ContentProvider进行交互。
>   + **ContentObserver**：观察ContentProvider中的数据变化，并将变化通知给外界。

### 数据存储

#### 1、描述一下Android数据持久存储方式？
> + 参考回答：Android平台实现数据持久存储的常见几种方式：
>     + SharedPreferences存储：一种轻型的数据存储方式，本质是基于XML文件存储的key-value键值对数据，通常用来存储一些简单的配置信息（如应用程序的各种配置信息）；
>     + SQLite数据库存储：一种轻量级嵌入式数据库引擎，它的运算速度非常快，占用资源很少，常用来存储大量复杂的关系数据；
>    + ContentProvider：四大组件之一，用于数据的存储和共享，不仅可以让不同应用程序之间进行数据共享，还可以选择只对哪一部分数据进行共享，可保证程序中的隐私数据不会有泄漏风险；
>    + File文件存储：写入和读取文件的方法和 Java中实现I/O的程序一样；
>    + 网络存储：主要在远程的服务器中存储相关数据，用户操作的相关数据可以同步到服务器上；

#### 2、SharedPreferences的应用场景？注意事项？
>  + 参考回答：
>      + SharedPreferences是一种轻型的数据存储方式，本质是基于XML文件存储的key-value键值对数据，通常用来存储一些简单的配置信息，如int，String，boolean、float和long；
>      + 注意事项：
>        + **勿存储大型复杂数据，这会引起内存GC、阻塞主线程使页面卡顿产生ANR**
>        + **勿在多进程模式下，操作Sp**
>        + **不要多次edit和apply，尽量批量修改一次提交**
>        + **建议apply，少用commit**

>  + 推荐文章：
>      + [史上最全面，清晰的SharedPreferences解析](https://blog.csdn.net/geekerhw/article/details/79713068)
>      + [SharedPreferences在多进程中的使用及注意事项](http://zmywly8866.github.io/2015/09/09/sharedpreferences-in-multiprocess.html)

#### 3、SharedPrefrences的apply和commit有什么区别？
>  + 参考回答：
>  + apply没有返回值而commit返回boolean表明修改是否提交成功。
>  + apply是将修改数据原子提交到内存, 而后异步真正提交到硬件磁盘, 而commit是同步的提交到硬件磁盘，因此，在多个并发的提交commit的时候，他们会等待正在处理的commit保存到磁盘后在操作，从而降低了效率。而apply只是原子的提交到内容，后面有调用apply的函数的将会直接覆盖前面的内存数据，这样从一定程度上提高了很多效率。
>  + apply方法不会提示任何失败的提示。 由于在一个进程中，sharedPreference是单实例，一般不会出现并发冲突，如果对提交的结果不关心的话，建议使用apply，当然需要确保提交成功且有后续操作的话，还是需要用commit的。

#### 4、了解SQLite中的事务操作吗？是如何做的
>  + 参考回答：
>    + SQLite在做CRDU操作时都默认开启了事务，然后把SQL语句翻译成对应的SQLiteStatement并调用其相应的CRUD方法，此时整个操作还是在rollback journal这个临时文件上进行，只有操作顺利完成才会更新db数据库，否则会被回滚；

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
[Android developer官方文档--进程和线程](https://developer.android.com/guide/components/processes-and-threads?hl=zh-cn)

#### 2、如何开启多进程 ？ 应用是否可以开启N个进程 ？ 
> + 参考回答：
>   + 在AndroidMenifest中给四大组件指定属性android:process开启多进程模式
>   + 在内存允许的条件下可以开启N个进程
> + 推荐讲解：
[如何开启多进程?应用是否可以开启N个进程？](https://github.com/qmsggg/qmsggg_BlogCollect/issues/158)

#### 3、为何需要IPC？多进程通信可能会出现的问题？
> + 参考回答：
>   + 所有运行在不同进程的四大组件（Activity、Service、Receiver、ContentProvider）共享数据都会失败，这是由于Android为每个应用分配了独立的虚拟机，不同的虚拟机在内存分配上有不同的地址空间，这会导致在不同的虚拟机中访问同一个类的对象会产生多份副本。比如常用例子（**通过开启多进程获取更大内存空间、两个或则多个应用之间共享数据、微信全家桶**）
>   + 一般来说，使用多进程通信会造成如下几方面的问题
>       + **静态成员和单例模式完全失效**：独立的虚拟机造成
>       + **线程同步机制完全实效**：独立的虚拟机造成
>       + **SharedPreferences的可靠性下降**：这是因为Sp不支持两个进程并发进行读写，有一定几率导致数据丢失
>       + **Application会多次创建**：Android系统在创建新的进程会分配独立的虚拟机，所以这个过程其实就是启动一个应用的过程，自然也会创建新的Application
> + 推荐文章：
[Android developer官方文档--进程和线程](https://developer.android.com/guide/components/processes-and-threads?hl=zh-cn)

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
[为什么 Android 要采用 Binder 作为 IPC 机制？](https://www.zhihu.com/question/39440766)

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
>      + [官方文档](https://developer.android.com/reference/android/view/View)
>      + [Android View的绘制流程](https://www.jianshu.com/p/5a71014e7b1b)
>      + [Android应用层View绘制流程与源码分析](https://blog.csdn.net/yanbober/article/details/46128379)

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
[View 的滑动原理和实现方式](https://www.jianshu.com/p/a177869b0382)

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
>   + 推荐文章：
[Java中的四种引用类型：强引用、软引用、弱引用和虚引用](https://segmentfault.com/a/1190000015282652)

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
> + 推荐文章：[Android异步消息处理机制完全解析，带你从源码的角度彻底理解](https://blog.csdn.net/guolin_blog/article/details/9991569)

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
> + 推荐文章：[java线程池解析和四种线程池的使用](https://blog.csdn.net/ztchun/article/details/57413255)

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
>   + 推荐文章：[单例模式的总结](https://xxxblank.github.io/2017/09/14/singleTon/)

#### 11、除了notify还有什么方式可以唤醒线程
> + 参考回答：
>   + 当一个拥有Object锁的线程调用 wait()方法时，就会使当前线程加入object.wait 等待队列中，并且释放当前占用的Object锁，这样其他线程就有机会获取这个Object锁，获得Object锁的线程调用notify()方法，就能在Object.wait 等待队列中随机唤醒一个线程（该唤醒是随机的与加入的顺序无关，优先级高的被唤醒概率会高）
>   + 如果调用notifyAll（）方法就唤醒全部的线程。**注意:调用notify()方法后并不会立即释放object锁，会等待该线程执行完毕后释放Object锁。**

#### 12、什么是ANR ? 什么情况会出现ANR ？如何避免 ？ 在不看代码的情况下如何快速定位出现ANR问题所在 ？
> + 参考回答：
>   + ANR（Application Not Responding，应用无响应）：当操作在一段时间内系统无法处理时，会在系统层面会弹出ANR对话框
>   + 产生ANR可能是因为5s内无响应用户输入事件、10s内未结束BroadcastReceiver、20s内未结束Service
>   + 想要避免ANR就不要在主线程做耗时操作，而是通过开子线程，方法比如继承Thread或实现Runnable接口、使用AsyncTask、IntentService、HandlerThread等
>   + 推荐文章：[如何快速分析定位ANR](https://www.jianshu.com/p/cfa9ed42e379)

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
>   + 推荐文章：[WebView性能、体验分析与优化](https://tech.meituan.com/2017/06/09/webviewperf.html)

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
>   + 推荐文章：[Android高效加载大图、多图解决方案，有效避免程序OOM](https://blog.csdn.net/guolin_blog/article/details/9316683)

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
>   + 推荐文章：[Android启动页解决攻略](https://blog.csdn.net/zivensonice/article/details/51691136)

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
>   + 推荐文章：
>     + [【腾讯Bugly干货分享】Android ListView 与 RecyclerView 对比浅析—缓存机制](https://zhuanlan.zhihu.com/p/23339185)
>     + [ListView 与 RecyclerView 简单对比](https://blog.csdn.net/shu_lance/article/details/79566189)
>     + [Android开发：ListView、AdapterView、RecyclerView全面解析](https://www.jianshu.com/p/4e8e4fd13cf7)

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
>    + 推荐文章：[Android JNI 篇 - ffmpeg 获取音视频缩略图](https://www.jianshu.com/p/411761bd5f5b)

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
> + 推荐文章：[MVC、MVP、MVVM，我到底该怎么选？](https://juejin.im/post/5b3a3a44f265da630e27a7e6)

#### 3、封装p层之后.如果p层数据过大,如何解决?
> + 参考回答：
>   + 对于MVP模式来说，P层如果数据逻辑过于臃肿，建议引入**RxJava**或则**Dagger**，越是复杂的逻辑，越能体现RxJava的优越性
>   + 推荐文章：[（仿有道精品课）RxJava+OkHttp+Retrofit+Dagger2+MVP框架(kotlin版本)](https://juejin.im/post/5c6e601cf265da2dc675b69e#comment)

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
>   + 推荐文章：[单例模式的总结](https://xxxblank.github.io/2017/09/14/singleTon/)

#### 7、用到的一些开源框架，介绍一个看过源码的，内部实现过程。
> + 参考回答：
>   + 面试常客：Okhttp，Retrofit，Glide，RxJava，GreenDao，Dagger等
>   + 推荐文章：
>     + [Android OkHttp源码解析入门教程（一）](https://juejin.im/post/5c46822c6fb9a049ea394510)
>     + [Android OkHttp源码解析入门教程（二）](https://juejin.im/post/5c4682d2f265da6130752a1d)

#### 8、Fragment如果在Adapter中使用应该如何解耦？
> + 参考回答：
>   + 接口回调
>   + 广播
