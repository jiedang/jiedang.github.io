---
layout: post
title: 关于activity堆栈管理控制
categories: []
tags: []
description: lauch mode和intent flag动态控制activity堆栈状态.
---
## 启动模式 lauchmode


在开启activity后 由activityXML中配置的默认模式 决定其所在的堆栈状态 和 退栈顺序


基本模式4种

 **standard** 默认 每次都新建一个新的activity
 
 **singleTop** 位于栈顶 复用堆栈中原有的 否则新建一个 常用于存储一些状态值啥的
 
 
 **singleTask** 堆栈中只能有一个实例 跳到SINGLE TASK的activity 会把其之上的界面踢出栈 主要通过这个属性 清理一些中间过渡界面
 
 
 **singleInstance** 堆栈中只能有一个ACTIVITY 就是它自身 主要给予外部调用 返回就是回到桌面 
 
示例：

1. 全是默认模式 A B C D ，D跳转到A，堆栈状态 A B C D A，退栈 ADCBA。

2. A C D 都是默认  B singleTask，堆栈状态 A B C D，D跳转到B，堆栈状态 A B，退栈顺序 BA 

  常见的用户注册登陆，中间界面销毁就用这个方式

3. A C D 都是默认  B singleInstance，堆栈状态 ACD，D跳转到B，堆栈A状态 ACD，堆栈B 状态B，然后B跳转到D，堆栈A状态 ACDD，堆栈B状态B，退栈顺序DDCAB 

例子不多 主要是因为 字面意思很简单，使用经验比较重要，随口出几个lauch方式组合 清楚退栈顺序就比较难了  

github上有个简单的 [DEMO](https://github.com/mokelab/Android-LauchMode-Demo) 没事可以点点理解下 

github上记得还有个更好的 带有当前堆栈状态显示 用不同颜色的条来标示 谁知道求分享

## 和启动相关的XML属性
- android:alwaysRetainTaskState task根ACTIVITY 属性 设置堆栈是否由系统保留状态  默认是false 
  
  例： 用户长时间没有访问一个保留的TASK 系统回删除 位于根ACTIVITY 以上的所有, 但是浏览器这些APP 需要长期保留多个跳转过得界面，可以设置主界面属性为TRUE。

- android:clearTaskOnLaunch 

  每次启动都清除自身堆栈之上的其他ACTIVITY
  例：没用过 可能那个界面首页 需要吧

-   android:finishOnTaskLaunch 

    强制要求每次启动时候 重新创建一个新的实例

## 手动控制堆栈状态 Intent Flag

- FLAG CTIVITY CLEAR TOP 类似于lauch mode 中得singleTask  清理启动界面以后的其他界面 

- FLAG ACTIVITY SINGLE TOP 和 lacuch mode 中得 singleTop 一样 如果被启动界面位于task 顶端运行时，不再启动一个新的 否则使用一个新的

   也可以和 FLAG CTIVITY CLEAR TOP 一起使用 则被启动界面不会被重新初始化 而是调用  onNewIntent方法

- FLAG ACTIVITY NEW TASK 新开一个新的TASK 调用 其他进程界面必须添加这个
  经常都和 FLAG CTIVITY CLEAR TOP 一起使用 用于外部方位APP 单个界面 通知启动一个界面啥的
- FLAG ACTIVITY CLEAR WHEN TASK RESET
- FLAG ACTIVITY RESET TASK IF NEEDED
  成对使用  

- FLAG ACTIVITY CLEAR WHEN TASK RESET 设置清理点 
   当带有 FLAG ACTIVITY RESET TASK IF NEEDED标示的 进入清理标记之上的所有界面
   
   就是APP 从A 界面打开了 很多其他功能 你喜欢用户从桌面进入的时候 不是去从新初始化一个A 还 是显示原来的A 使用这对标记


- FLAG ACTIVITY EXCLUDE FROM RECENTS
如果调出的Activtivity只是一个功能片段，并没有实际的意义，也没有必要出现在长按Home键调出最近使用过的程序类表中 使用这个表示标记

下面几个 没咋用过 抄录下网上 做个记录 以后用到了 在写下自己的理解

- FLAG ACTIVITY NO ANIMATION

   如果在Intent中设置，并传递给Context.startActivity()的话，这个标志将阻止系统进入下一个Activity时应用Acitivity迁移动画。这并不意味着动画将永不运行——如果另一个Activity在启动显示之前，没有指定这个标志，那么，动画将被应用。这个标志可以很好的用于执行一连串的操作，而动画被看作是更高一级的事件的驱动。

- FLAG ACTIVITY NO HISTORY

   如果设置，新的Activity将不再历史stack中保留。用户一离开它，这个Activity就关闭了。
 这也可以通过设置noHistory特性。

- FLAG ACTIVITY NO USER ACTION 

   如果设置，作为新启动的Activity进入前台时，这个标志将在Activity暂停之前阻止从最前方的 Activity回调的onUserLeaveHint()。 
       典型的，一个Activity可以依赖这个回调指明显式的用户动作引起的Activity移出后台。这个回调在Activity的生命周期中标记一个合适的点，并关闭一些Notification。 
       如果一个Activity通过非用户驱动的事件，如来电或闹钟，启动的，这个标志也应该传递给Context.startActivity，保证暂停的Activity不认为用户已经知晓其Notification。 

- FLAG ACTIVITY REORDER TO_FRONT

  如果在Intent中设置，并传递给Context.startActivity()，这个标志将引发已经运行的Activity移动到历史stack的顶端。 
       例如，假设一个Task由四个Activity组成：A,B,C,D。如果D调用startActivity()来启动Activity B，那么，B会移动到历史stack的顶端，现在的次序变成A,C,D,B。如果FLAG_ACTIVITY_CLEAR_TOP标志也设置的话，那么这个标志将被忽略。 









 



