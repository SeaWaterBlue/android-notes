#  Activity 笔记

## 基础概念

具有单一用户界面的单一屏幕,

## 生命周期

![生命周期](../android-notes/Activity学习笔记-资源/activity.jpg)

- `onCreate()`

    创建 `Activity` 时触发，需要在此处执行 setContentView 方法来绑定布局文件。此方法在整个生命周期只会执行一次。  
    此方法会接收 `savedInstanceState` 参数，后者是包含 `Activity` 先前保存状态的 `Bundle` 对象。如果 `Activity` 此前未曾存在，`Bundle` 对象的值为 `null`。

- `onStart()`

    `Activity` 进入“已启动”状态，并且对用户可见，执行 `Activity` 与用户交互前最后的准备工作。

- `onResume()`

    同样在 `Activity` 与用户交互前执行，此时 `Activity` 处于 `Activity` 堆栈的栈顶，应用层的核心功能可以放到此处实现，此方法会一直执行，直到 `onPause()` 执行。DaintyDainty

- `onPause()`

    `Activity` 处于"已停止"或者“已恢复”状态，这一状态的 `Activity` 不在处于前台，仍可以执行部分操作。

- `onStop()`

    `Activity` 处于不可见状态，接下来可能会调用 `onRestart()` 或者 `onDestroy()`.此方法中可以执行一些关闭操作。

- `onRestart()`

    可以恢复已经 onStop() 的 Activity，接下来会执行 onStart().

- `onDestroy()`

    销毁 `Activity` ，并释放所有资源

- `Activity` 中数据的保留以及重建
    
    通过 `onSaveInstanceState` 方法来保存 `Activity` 需要临时保存的数据；

    ```Java 
    static final String STATE_SCORE = "playerScore";
    static final String STATE_LEVEL = "playerLevel";
    // ...

    @Override
    public void onSaveInstanceState(Bundle savedInstanceState) {
        // Save the user's current game state
        savedInstanceState.putInt(STATE_SCORE, currentScore);
        savedInstanceState.putInt(STATE_LEVEL, currentLevel);

        // Always call the superclass so it can save the view hierarchy state
        super.onSaveInstanceState(savedInstanceState);
    }
    ```

    同时，在 `Activity` 重建时，取出 `savedInstanceState`的数据并对相应的界面赋值；

    ```Java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState); // Always call the superclass first

        // Check whether we're recreating a previously destroyed instance
        if (savedInstanceState != null) {
            // Restore value of members from saved state
            currentScore = savedInstanceState.getInt(STATE_SCORE);
            currentLevel = savedInstanceState.getInt(STATE_LEVEL);
        } else {
            // Probably initialize members with default values for a new instance
        }
        // ...
    }
    ```
## 页面跳转、任务与返回栈

- `Activity` 之间的跳转通过 `Intent` 实现
  
    ```Java
    Intent intent = new Intent(this,NewActivity.class);
    startActivity(intent);
    //单向跳转

    startActivityForResult(intent, 0x001);
    protected void onActivityResult(int requestCode, int resultCode,
                Intent data) {
            if (requestCode == PICK_CONTACT_REQUEST) {
                if (resultCode == RESULT_OK) {
                    //TODO
                }
            }
        }
    //带有返回的跳转（已弃用）
    
    ActivityResultLauncher<Intent> launcher;
    launcher = registerForActivityResult(new ActivityResultContracts.StartActivityForResult(), new ActivityResultCallback<ActivityResult>() {
            @Override
            public void onActivityResult(ActivityResult result) {
                if(result.getResultCode() == Activity.RESULT_OK){
                    //TODO
                }
            }
        });
    launcher.launch(intent);
    //带返回数据跳转的替代
    ```
- 返回堆栈
  
    返回堆栈中的 `Activity` 以固定顺序排列，且不会重新排列，新建 `Activity` 时会将此 `Activity` 置于栈顶，而 `返回` 则会销毁最顶层的 `Activity`。

- 管理任务

    Android 管理任务和返回堆栈的方式是将所有接连启动的 Activity 放到同一任务和一个 `后进先出` 堆栈中，可以通过设置 Activity 的属性来控制 Activity 的创建方式以及部分行为。 
    
    可以借助 `<activity>` 清单元素中的属性以及您传递给 `startActivity()` 的 `intent` 中的标记来实现上述目的。

    在这方面，您可以使用的主要 `<activity>` 属性包括：

    `taskAffinity`  

    `launchMode`  

    `allowTaskReparenting`  

    `clearTaskOnLaunch`  

    `alwaysRetainTaskState`  

    `finishOnTaskLaunch`  

    您可以使用的主要 intent 标记包括：

    `FLAG_ACTIVITY_NEW_TASK`  

    `FLAG_ACTIVITY_CLEAR_TOP`  

    `FLAG_ACTIVITY_SINGLE_TOP`  

1. `taskAffinity` 用于指定  `Activity` 的任务栈，也成为了 `Activity` 的亲和性，相同的`taskAffinity`会在同一个任务栈中新建实例，默认为包名。此概念于下面的 `launchMode` 会用到。

    - 当启动 `Activity` 的 `intent` 包含 `FLAG_ACTIVITY_NEW_TASK` 标记时。

        默认情况下，新 `Activity` 会被启动到调用 `startActivity()` 的任务中，并且会被推送到调用方 `Activity` 的返回堆栈中。在包含 `FLAG_ACTIVITY_NEW_TASK` 情况下，通常会新建一个任务来启动 `Activity` ，但是如果 新 `Activity` 存在 `taskAffinity` 属性，则会优先启动到相同的 `taskAffinity`（亲和性） 的任务中。
        
    - 当 `Activity` 的 `allowTaskReparenting` 属性设为 `true` 时。
        
        在这种情况下，一旦和 `Activity` 有亲和性的任务进入前台运行，`Activity` 就可从其启动的任务转移到该任务。

2. 清除返回堆栈
    
    如果用户离开任务较长时间，系统会清除任务中除根 `Activity` 以外的所有 `Activity`。当用户再次返回到该任务时，只有根 `Activity` 会恢复。系统之所以采取这种行为方式是因为，经过一段时间后，用户可能已经放弃了之前执行的操作，现在返回任务是为了开始某项新的操作。

    您可以使用一些 `Activity` 属性来修改此行为：

    - `alwaysRetainTaskState`

    如果在任务的根 `Activity` 中将该属性设为 `"true"`，则不会发生上述默认行为。即使经过很长一段时间后，任务仍会在其堆栈中保留所有 `Activity`。

    - `clearTaskOnLaunch`

    如果在任务的根 `Activity` 中将该属性设为 `"true"`，那么只要用户离开任务再返回，堆栈就会被清除到只剩根 `Activity`。也就是说，它与 `alwaysRetainTaskState` 正好相反。用户始终会返回到任务的初始状态，即便只是短暂离开任务也是如此。

    - `finishOnTaskLaunch`

    该属性与 `clearTaskOnLaunch` 类似，但它只会作用于单个 `Activity` 而非整个任务。它还可导致任何 `Activity` 消失，包括根 `Activity`。如果将该属性设为 `"true"`，则 `Activity` 仅在当前会话中归属于任务。如果用户离开任务再返回，则该任务将不再存在。

1. 使用清单文件设置 `Activity` 的 `launchMode`来改变其启动方式, `launchMode`属性可以是以下 4 种不同的启动模式：

    - `standrad`

        默认的启动方式，此模式下，`Activity` 可被多次实例化，每次启动都会新建 `Activity` 实例，每个实例可以属于相同的任务，也可以属于不同的任务。

    - `singleTop` 

        如果当前任务的返回栈栈顶存在已经实例化的 `Activity` ，当收到以此 `Activity` 为目标的 `intent` 时，系统不会再创建新的 `Activity` 的实例，而是通过 `onNewIntent()` 方法将收到的 `intent` 转送给栈顶的 `Activity` 实例。注意，只有目标 `Activity` 在当前返回栈栈顶时，才可以复用 `Activity` 实例，如果不在栈顶，则仍会创建新的 `Activity` 以供 `intent` 跳转。
        
    - `singleTask`

        此模式下，同一个任务栈中 `Activity` 只会有一个实例存在，已经创建的 `Activity` 实例可以跨应用进行转送，类似于 `任务栈内单例模式`。
    
    - `singleInstance`

        此模式下，该  `Activity` 会被启动到一个单独的任务栈中，并且此任务栈中只有该  `Activity` ，该 `Activity` 启动的任何其他 `Activity` 都不会和该 `Activity` 处于同一任务栈，而是会在其他任务栈中启动，真正的`全局单例模式`。  
        如果不声明 `Activity`的 `taskAffinity`，即 每一个`Activity` 都对应着单独的 `taskAffinity`（默认值：包名），其实 `singleInstance` 也就相当于`singleTask`。

2. 启动 `Activity` 时，您可以在传送给 `startActivity()` 的 `intent` 中添加相应的标记来修改 `Activity` 与其任务的默认关联。您可以使用以下标记来修改默认行为：

    - `FLAG_ACTIVITY_NEW_TASK`

        在新任务中启动 `Activity`。如果您现在启动的 `Activity` 已经有任务在运行，则系统会将该任务转到前台并恢复其最后的状态，而 `Activity` 将在 `onNewIntent()` 中收到新的 `intent`。  
        这与上一节中介绍的 `"singleTask" launchMode` 值产生的行为相同。
        
    - `FLAG_ACTIVITY_SINGLE_TOP`  
        如果要启动的 `Activity` 是当前 `Activity`（即位于返回堆栈顶部的 `Activity`），则现有实例会收到对 `onNewIntent()` 的调用，而不会创建 Activity 的新实例。  
        这与上一节中介绍的 `"singleTop" launchMode` 值产生的行为相同。

    - `FLAG_ACTIVITY_CLEAR_TOP`  
        如果要启动的 `Activity` 已经在当前任务中运行，则不会启动该 `Activity` 的新实例，而是会销毁位于它之上的所有其他 `Activity`，并通过 `onNewIntent()` 将此 intent 传送给它的已恢复实例（现在位于堆栈顶部）。  
        `launchMode` 属性没有可产生此行为的值。

FLAG_ACTIVITY_CLEAR_TOP 最常与 FLAG_ACTIVITY_NEW_TASK 结合使用。将这两个标记结合使用，可以查找其他任务中的现有 Activity，并将其置于能够响应 intent 的位置。   
  
## 其他

- 系统不会直接销毁 `Activity` ,而是直接销毁 `Activity` 所在的进程

## 其他补充知识



