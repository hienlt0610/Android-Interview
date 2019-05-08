# Android-Interview
## 引言
2017年初Android市场饱和的传言一度甚嚣尘上。2018年经济寒潮下，众多大厂和曾经风口上的互联网企业也不得不裁员自保，通过小程序、前端渲染以达到原生的实现。

面对外界的纷繁复杂和技术栈的日新月异，我们更应该清楚认识到自身技术的短板来进行知识巩固。目前移动端Android初中级人才大量涌入，正所谓僧多粥少，但楼主始终认为 **苦心人，天不负，只要自身有过硬的知识广度和深度储备，在寒冬之下，同样也能站稳脚跟。**

楼主年前走了一波社招试试水，一番厮杀后最终拿到多家offer，回味之余，不得不感叹现在的985、211出身的技术人才真的强（楼主只是普通本科），为了践行社会主义核心价值观，于是总结自己的面试经历，结合参考其他社招面试总结整理出这一份面试解答，承蒙大家不弃，文中知识点如有描述错误，还望提出探讨纠正。

再此感谢以下博主，文章部分知识点有引用，他们的文章使我得到很多启发

[郭霖](https://blog.csdn.net/guolin_blog) 

[鸿洋](https://blog.csdn.net/lmj623565791) 

[任玉刚](https://blog.csdn.net/singwhatiwanna) 

[Android开发官方文档](https://developer.android.com/) 

[Carson_Ho](https://juejin.im/user/58d4d9781b69e6006ba65edc)

>注：**答案在最下面**，因为实际开发与参考答案会有所不同，再者怕误导大家的理解，所以这些面试题答案还是自己去理解！面试官会针对简历中提到的知识点由浅入深提问，所以不要背答案，多理解。

## Android篇

### Activity
> + 说下Activity生命周期 ？
> + Activity A 启动另一个Activity B 会调用哪些方法？如果B是透明主题的又或则是个DialogActivity呢 ？
> + 说下onSaveInstanceState()方法的作用 ? 何时会被调用？
> + 说下 Activity的四种启动模式、应用场景 ？
> + 了解哪些Activity常用的标记位Flags？
> + 说下 Activity跟window，view之间的关系？
> + 横竖屏切换的Activity生命周期变化？
> + 如何启动其他应用的Activity？
> + Activity的启动过程？（重点）

### Fragment
> + 谈一谈Fragment的生命周期 ？与Activity生命周期的不同 ？
> + 谈谈Activity和Fragment的区别？
> + Fragment中add与replace的区别（Fragment重叠）
> + getFragmentManager、getSupportFragmentManager 、getChildFragmentManager之间的区别？
> + FragmentPagerAdapter与FragmentStatePagerAdapter的区别与使用场景

### Service
> + 谈一谈Service的生命周期？
> + Service的两种启动方式？区别在哪？
> + 如何保证Service不被杀死 ？
> + 能否在Service开启耗时操作 ？ 怎么做 ？
> + 用过哪些系统Service ？
> + 了解ActivityManagerService吗？发挥什么作用（重点）

### Broadcast Receiver
> + 广播有几种形式 ? 都有什么特点 ？
> + 广播的两种注册方式 ？
> + 广播发送和接收的原理了解吗 ？（Binder机制、AMS）

### ContentProvider
> + ContentProvider了解多少？
> + ContentProvider的权限管理？
> + 说说ContentProvider、ContentResolver、ContentObserver 之间的关系？

### 数据存储
> + 描述一下Android数据持久存储方式？
> + SharedPreferences的应用场景？注意事项？
> + SharedPrefrences的apply和commit有什么区别？
> + 了解SQLite中的事务操作吗？是如何做的
> + 使用SQLite做批量操作有什么好的方法吗？
> + 如何删除SQLite中表的个别字段？
> + 使用SQLite时会有哪些优化操作？

### IPC（重点）
> + Android中进程和线程的关系？ 区别？
> + 如何开启多进程 ？ 应用是否可以开启N个进程 ？ 
> + 为何需要IPC？多进程通信可能会出现的问题？
> + Android中IPC方式、各种方式优缺点，为什么选择Binder？
> + Binder机制的作用和原理？
> + Binder框架中ServiceManager的作用？
> + Bundle传递对象为什么需要序列化？Serialzable和Parcelable的区别？
> + 讲讲AIDL？原理是什么？如何优化多模块都使用AIDL的情况？

### View
> + 讲下View的绘制流程？
> + MotionEvent是什么？包含几种事件？什么条件下会产生？
> + 描述一下View事件传递分发机制？
> + 如何解决View的事件冲突 ？ 举个开发中遇到的例子 ？
> + scrollTo()和scollBy()的区别？
> + Scroller是怎么实现View的弹性滑动？
> + invalidate()和postInvalidate()的区别 ？
> + SurfaceView和View的区别？
> + 自定义View如何考虑机型适配 ?


### Handler
> + 谈谈消息机制Handler ? 作用 ？有哪些要素 ？流程是怎样的 ？
> + 一个线程能否创建多个Handler，Handler跟Looper之间的对应关系 ？ 
> + 软引用跟弱引用的区别
> + Handler 引起的内存泄露原因以及最佳解决方案
> + 为什么系统不建议在子线程访问UI
> + Looper死循环为什么不会导致应用卡死
> + 使用Handler的postDealy后消息队列会有什么变化 ？
> + 可以在子线程直接new一个Handler吗 ？怎么做 ？ 
> + Message可以如何创建 ？ 哪种效果更好 ？ 为什么 ？

### 线程（重点）
> + 线程池的好处？ 线程池的几个参数的理解，四种线程池的使用场景
> + Android中还了解哪些方便线程切换的类？
> + 讲讲AsyncTask的原理
> + IntentService有什么用 ？
> + 直接在Activity中创建一个thread跟在service中创建一个thread之间的区别
> + ThreadPoolExecutor的工作策略 ？
> + Handler、Thread和HandlerThread的差别？
> + ThreadLocal的原理
> + 多线程是否一定会高效（优缺点）
> + 多线程中,让你做一个单例,你会怎么做
> + 除了notify还有什么方式可以唤醒线程
> + 什么是ANR ? 什么情况会出现ANR ？如何避免 ？ 在不看代码的情况下如何快速定位出现ANR问题所在 ？

### Bitmap
> + Bitmap使用需要注意哪些问题 ？
> + Bitmap.recycle()会立即回收么？什么时候会回收？如果没有地方使用这个Bitmap，为什么垃圾回收不会直接回收？
> + 一张Bitmap所占内存以及内存占用的计算
> + Android中缓存更新策略 ？
> + LRU的原理 ？ 

### 性能优化（重点）
> + 图片的三级缓存中,图片加载到内存中,如果内存快爆了,会发生什么？怎么处理？
> + 内存中如果加载一张500*500的png高清图片.应该是占用多少的内存?
> + WebView的性能优化 ?
> + Bitmap如何处理大图，如一张30M的大图，如何预防OOM
> + 内存回收机制与GC算法(各种算法的优缺点以及应用场景)；GC原理时机以及GC对象
> + 内存泄露和内存溢出的区别 ？AS有什么工具可以检测内存泄露
> + 性能优化,怎么保证应用启动不卡顿? 黑白屏怎么处理?
> + 强引用置为null，会不会被回收？
> + ListView跟RecyclerView的区别
> + ListView的adapter是什么adapter ？
> + LinearLayout、FrameLayout、RelativeLayout性能对比，为什么？

### JNI
> + 对JNI是否了解
> + 如何加载NDK库 ？如何在JNI中注册Native函数，有几种注册方法 ？
> + 你用JNI来实现过什么功能 ？ 怎么实现的 ？（加密处理、影音方面、图形图像处理）

### 设计模式
> + 你所知道的设计模式有哪些？
> +	谈谈MVC、MVP和MVVM，好在哪里，不好在哪里 ？ 
> + 封装p层之后.如果p层数据过大,如何解决
> +	是否能从Android中举几个例子说说用到了什么设计模式 ？
> +	装饰模式和代理模式有哪些区别 ？
> +	实现单例模式有几种方法 ？懒汉式中双层锁的目的是什么 ？两次判空的目的又是什么？
> + 用到的一些开源框架，介绍一个看过源码的，内部实现过程。
> + Fragment如果在Adapter中使用应该如何解耦？

### Android进阶延伸点
> + 如何进行单元测试，如何保证App稳定
> + Android中如何查看一个对象的回收情况
> + APK的大小如何压缩 ？ 多渠道包 ？
> + 插件化原理分析
> + 组建化原理，组件化中路由、埋点的实现
> + Hook以及插桩技术
> + Android的签名机制，APK包含哪些东西 ？
> + v3签名key和v2还有v1有什么区别
> + 热修复流派、原理，如何进行dex替换的 ？
> + Android5.0~10.0之间大的变化 ？
> + 说下Measurepec这个类
> + WebView相关（内存泄露、JS交互）
> + 请例举Android中常用布局类型，并简述其用法以及排版效率
> + 区别Animation和Animator的用法，概述其原理
> + 如何实现一个推送，极光推送原理
> + 是否使用过DataBinding ？ ButterKnife是怎么做到布局绑定的 ？ 
> + 使用过什么图片加载库 ？Glide的源码设计哪里很微妙 ？
> + 做过屏幕适配吗 ？你的处理方案有哪些 ？
> + 做过主题切换吗？你的处理方案有哪些？
> + 做过权限适配吗 ？动态权限适配方案、权限组的概念
> + 用过哪些网络加载库 ？OkHttp、Retrofit实现原理 ？ 
> + 对于应用更新这块是如何做的 ？ （灰度，强制更新、分区域更新）
> + 了解GPS、GIS吗 ？
> + 会用Kotlin、Fultter吗 ？ 谈谈你的理解

## 解答篇

* [Android解答篇](https://github.com/Ye-Miao/Android-Interview/blob/master/Android%E8%A7%A3%E7%AD%94%E7%AF%87.md)

