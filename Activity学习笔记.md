# Activity笔记

## 基础概念

具有单一用户界面的单一屏幕,

![生命周期](../android-notes/Activity学习笔记-资源/activity.jpg)

- onCreate()

    创建 Activity 时触发，需要在此处执行 setContentView 方法来绑定布局文件。

- onStart()

    Activity 进入“已启动”状态，并且对用户可见，执行 Activity 与用户交互前最后的准备工作。

- onResume()

    同样在 Activity 与用户交互前执行，此时 Activity 处于 Activity 堆栈的栈顶，应用层的核心功能可以放到此处实现。

- onPause()

    Activity 处于"已停止"或者“已恢复”状态，但是这一状态的 Activity 还处于可见状态，仍可以执行部分操作。

- onStop()

    