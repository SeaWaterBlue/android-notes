# Broadcast学习笔记

## 基本描述、原理

- `Android` 内部的一种消息通知机制，可以再不同进程间传递数据。每个应用都可以自由的发送和接收广播数据。

- 广播采用观察者模式：基于消息的发布 / 订阅事件模型 将广播的发送和接收进行解耦。并且广播的发送和接收是一种完全异步的行为。

- 基本原理：
  
    1. 广播接收者通过 `Binder` 机制在 `AMS`（ `Activity Manager Service` ） 注册广播接收器（`BroadcastReceiver`）；
   
    2. 广播发送者通过 `Binder` 机制向 AMS 发送广播；
   
    3. AMS 根据广播发送者要求，在已注册列表中，寻找合适的 `BroadcastReceiver` （ 寻找依据：`IntentFilter` / `Permission` ）；
   
    4. `AMS` 将广播发送到 `BroadcastReceiver` 相应的消息循环队列中；
   
    5. 广播接收者通过消息循环拿到此广播，并回调广播接收器的 `onReceive()` 方法。

## 分类以及具体实现

- 基础实现

    广播接收器的注册方式分为两种（`静态注册`和`动态注册`）：

    ```Java
    //发送广播
    Intent intent= new Intent();
    sendBroadcast(intent);
    //广播接收器（动态注册）
    BroadcastReceiver broadcast = new BroadcastReceiver() {
    @Override
    public void onReceive(Context context, Intent intent) {
            //TODO
        }
    };
    IntentFilter intentFilter = new IntentFilter();
    intentFilter.addAction("xxx");
    registerReceiver(broadcast,intentFilter);
    //广播接收器（静态注册 AndroidManifest.xml）
    <application>
        <receiver android:name="com.xxx.receiver">
            <intent-filter>
                <action android:name="xxx"/>
            </intent-filter>
        </receiver>
    </application>

    ```

    - `Android 8 `之后，大多数广播的静态注册都被取消。

- 按照是否有序分类

    1. 无序广播（普通广播）

        广播发出后，所有广播接收器同一时间收到该广播，没有先后顺序。

        具体实现如`基础实现`所示


    2. 有序广播

        广播发出后，同一时间只有一个广播接收器可以收到该广播，优先级高的广播接收器会优先接收到该广播。当收到信息的广播接收器运行结束后，下一个广播接收器才会收到广播。并且接收器可以对收到的广播信息进行`修改`甚至于`截停`。

        ```Java
        //发送广播
        Intent intent = new Intent();
        //设置优先级
        intentFilter.setPriority(100);
        intent.setAction(Contants.orderBroadcast);
        Bundle bundle = new Bundle();
        bundle.putString("message","有序广播发送的第一站");
        //根据参数的不同使用不同的方法发送广播
        sendOrderedBroadcast(intent,null,null,null,null, Activity.RESULT_OK,null,bundle);

        //广播接收器
        private BroadcastReceiver broadcast = new BroadcastReceiver() {
                @Override
                public void onReceive(Context context, Intent intent) {
                    Bundle resultExtras = getResultExtras(true);
                    String message = resultExtras.getCharSequence("message").toString();
                    resultExtras.putCharSequence("content","有序广播发送的第二站");
                    //处理后的数据发送到下一站
                    setResultExtras(resultExtras);

                }
            };

        ```
 - 本地广播

    本地广播只可以被本应用的广播接收器所接收。
   
   ```Java
   Intent intent = new Intent();
   LocalBroadcastManager.getInstance(this).sendBroadcast(intent);
   ```

- 系统广播

    确认好对应的广播 `Action`,并且需要请求对应的 `perssion`权限才可以正常接收。

## 补充



