---
title: Android中的线程和线程池
date: 2016-03-20 22:13:22
tags: android thread executor
---

>在操作系统中，线程是操作系统调度的最小单元，同时线程又是一种受限的系统资源，即线程不可能无限制地产生，并且线程的创建和销毁都会有相应的开销。当系统中存在大量的线程时，系统会通过时间片轮转的方式调度每个线程，因此线程不可能做到绝对的并行，除非线程数量小于等于CPU的核心数。本文源码基于Andoird5.1.1分析。


## 线程


线程在Android上是一个很重要的概念，从用途上来讲分为主线程和子线程，主线程主要处理和界面相关的操作，而子线程则通常用来处理耗时操作。由于Android的主线程需要用来处理界面上的操作，这就导致主线程不能执行耗时操作，否则会造成界面上的卡顿，体验自然就下去了。

除了Java自带的Thread外，在Android中可以扮演线程的角色还有很多，比如AsyncTask、IntentService和HandlerThread。虽然上述三种线程的表现形式都有别于传统的线程，但是它们的本质还是Thread。AsyncTask的底层用了线程池，IntentService和HandlerThread则直接使用了Thread。下边通过Android源码来分析下上述三种Android的线程。

#### AsyncTask

AsyncTask是一种轻量级的异步任务类，它可以在线程池中处理后台任务，然后把处理结果传递给主线程，主线程通过结果进行UI方面的相关操作。AsyncTask的设计主要是对Thread和Handler的封装处理，**但是AsyncTask不能用于处理长时间的耗时任务**，耗时任务应控制在几秒内,如果需要处理耗时任务的话，应使用其它方式处理，比如Executor, ThreadPoolExecutor和FutureTask。Android官方文档描述如下：

>AsyncTask is designed to be a helper class around Thread and Handler and does not constitute a generic threading framework. AsyncTasks should ideally be used for short operations (a few seconds at the most.) If you need to keep threads running for long periods of time, it is highly recommended you use the various APIs provided by the java.util.concurrent package such as Executor, ThreadPoolExecutor and FutureTask.

##### AsyncTask用法

AsyncTask是一个抽象泛型类，必须被继承才能使用。它提供了三个泛型参数（Params、Progress、Result）。

**Params** 参数的类型  
**Progress** 后台任务的执行进度的类型  
**Result** 任务的返回结果的类型  

如果AsyncTask不需要参数传递具体的参数的话，这三个泛型参数可以用Void代替。

AsyncTask提供了四个常用的方法  
``onPreExecute()`` 在主线程中执行，在任务开始之前被调用，常用语处理一些准备操作。  
``doInBackground(Params...)`` 在线程池中执行，此方法用于执行耗时任务。
