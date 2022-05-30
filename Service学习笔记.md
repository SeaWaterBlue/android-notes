# Service 学习笔记

## 概述

`Service` 是一种可以在后台长时间执行运行操作而不需要界面的应用组件。服务可以由其他应用启动，同时，其他应用也可以通过 `bindService` 的方法可以调用服务中的方法以及进行数据的交互。

## 服务类型

- 前台

    `前台服务` 执行一些用户能注意到的操作。例如，音频应用会使用前台服务来播放音频曲目。前台服务必须`显示通知`。即使用户停止与应用的交互，前台服务仍会继续运行。  
    通过 `startForegroundService()` 启动服务，并且服务中要调用 `startForeground()` 方法显示通知。

- 后台

    后台服务执行用户不会直接注意到的操作。`Jetpack`中的 `WorkMnanger` 可以作为代替。  
    通过 `startService()` ，并且需要应用在前台运行，否则会受到 `Android` 系统的限制。

- 绑定
    当应用组件通过调用 `bindService()` 绑定到服务时，服务即处于绑定状态。绑定服务会提供 `客户端-服务器` 接口，以便组件与服务进行交互、发送请求、接收结果，甚至是利用进程间通信 (IPC) 跨进程执行这些操作。仅当与另一个应用组件绑定时，绑定服务才会运行。多个组件可同时绑定到该服务，但全部取消绑定后，该服务即会被销毁。此情况下会通过 `AIDL` 文件使得其他应用可以调用 `Service` 的方法。

## 生命周期以及构成

- 生命周期

    `Service` 的生命周期由一下几部分组成：

    - `onCreate()`

        启动 `Service` 时，只会执行一次此方法，一般在此处进行初始化操作。

    - `onStartCommand()`

        在 `onCreate()` 之后执行，并且每次调用 `startService()` 或 `startForegroundService()` 都会执行。

    - `onBind()`

        当另一个组件想要与服务绑定（例如执行 RPC）时，系统会通过调用 `bindService()` 来调用此方法。在此方法的实现中，您必须通过返回 `IBinder` 提供一个接口，以供客户端用来与服务进行通信。请务必实现此方法；但是，如果您并不希望允许绑定，则应返回 null。

    - `onDestroy()`

        服务被销毁前会调用。

## 扩展 IntentService 类

- 默认情况下，`Service` 还是运行在主线程上的，所以不适合在之中直接执行耗时操作。`IntentService` 在 `onCreate`（函数中通过 `HandlerThread` 单独开启一个线程来处理所有Intent 请求对象所对应的任务，这样以免请求处理阻塞主线程。执行完一个 `Intent` 请求对象所对应的工作之后，如果没有新的 `Intent` 请求到达，就自动停止`Service`;否则执行下一个Intent 请求所对应的任务，直至最后执行完队列的所有命令，服务也随即停止并被销毁。
  
        

## AIDL 详解

- `Android` 接口定义语言 (`AIDL`) 与您可能使用过的其他接口语言 (`IDL`) 类似。您可以利用它定义客户端与服务均认可的编程接口，以便二者使用进程间通信 (`IPC`) 进行相互通信。

- `AIDL` 用于在不同应用之间 `bindService()` 时，便于其他应用调用该服务的方法。具体使用如下:

    - 新建`AIDL`文件

        `AIDL` 文件之间可以互相调用，适用于自定义回调作为函数参数

        默认情况下，`AIDL` 支持下列数据类型：

        `Java` 编程语言中的所有原语类型（如 `int`、`long`、`char`、`boolean` 等）  

        `String`  

        `CharSequence`  

        `List` 

        `List` 中的所有元素必须是以上列表中支持的数据类型，或者您所声明的由 `AIDL` 生成的其他接口或 `Parcelable` 类型。您可选择将 `List` 用作“泛型”类（例如，`List<String>`）。尽管生成的方法旨在使用 `List` 接口，但另一方实际接收的具体类始终是 `ArrayList`。  

        `Map`  

        `Map` 中的所有元素必须是以上列表中支持的数据类型，或者您所声明的由 `AIDL` 生成的其他接口或 `Parcelable` 类型。不支持泛型 Map（如 `Map<String,Integer>` 形式的 `Map`）。尽管生成的方法旨在使用 Map 接口，但另一方实际接收的具体类始终是 `HashMap`。

    ```Java
    //Service部分
        public class RemoteService extends Service {
        @Override
        public void onCreate() {
            super.onCreate();
        }

        @Override
        public IBinder onBind(Intent intent) {
            // Return the interface
            return binder;
        }
        //Service实现AIDL接口类并作为变量
        private final IRemoteService.Stub binder = new IRemoteService.Stub() {
            public int getPid(){
                return Process.myPid();
            }
            public void basicTypes(int anInt, long aLong, boolean aBoolean,
                float aFloat, double aDouble, String aString) {
                // Does nothing
            }
        };
    }

    //其他进程调用
        private ServiceConnection mConnection = new ServiceConnection() {
        // Called when the connection with the service is established
        public void onServiceConnected(ComponentName className, IBinder service) {
          
            //此处进行获取ibinder的转化
            iRemoteService = IRemoteService.Stub.asInterface(service);
        }

        // Called when the connection with the service disconnects unexpectedly
        public void onServiceDisconnected(ComponentName className) {
            Log.e(TAG, "Service has unexpectedly disconnected");
            iRemoteService = null;
        }
    };
    context.bindService(intent,mConnection,Service.BIND_AUTO_CREATE)

    ```


