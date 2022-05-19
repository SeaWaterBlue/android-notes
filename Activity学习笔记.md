# Activity笔记

## 基础概念

具有单一用户界面的单一屏幕,

![生命周期](../android-notes/Activity学习笔记-资源/activity.jpg)

- onCreate()

    创建 Activity 时触发，需要在此处执行 setContentView 方法来绑定布局文件。此方法在整个生命周期只会执行一次。  
    此方法会接收 savedInstanceState 参数，后者是包含 Activity 先前保存状态的 Bundle 对象。如果 Activity 此前未曾存在，Bundle 对象的值为 null。

- onStart()

    Activity 进入“已启动”状态，并且对用户可见，执行 Activity 与用户交互前最后的准备工作。

- onResume()

    同样在 Activity 与用户交互前执行，此时 Activity 处于 Activity 堆栈的栈顶，应用层的核心功能可以放到此处实现，此方法会一直执行，直到 onPause() 执行。

- onPause()

    Activity 处于"已停止"或者“已恢复”状态，这一状态的 Activity 不在处于前台，仍可以执行部分操作。

- onStop()

    Activity 处于不可见状态，接下来可能会调用 onRestart() 或者 onDestroy().

- onRestart()

    可以恢复已经 onStop() 的 Activity，接下来会执行 onStart().

- onDestroy()

    销毁 Activity ，并释放所有资源

