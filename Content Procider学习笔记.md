# Content Procider 学习笔记

## Content Procider作用

- 进程间进行数据交互&共享，即跨进程通信。可以使用暴露的连接来获取以及修改 `Uri` 提供者的对应应数据。`Content Procider`使用的是 `Acticity` 的 `Binder` 机制。

## 实现流程

- 外界进程通过`URI`找到对应的`Content Provider`&其中的数据，再进行数据操作，

    - `Uri` :统一资源标识符

            自定义 Uri = content://com.zhq.test/user/1
            主题（Schema）: Content Provider的URI前缀。
            授权信息(Authority): Content Provider的唯一标识符，不同的应用一般不同
            表名(Path): `User`为表名，指向的是对应应用数据库中对一个表的名称
            记录(ID): 表中的某个记录，若无则返回全部记录

    - ContentProvider类
        Contentprovider主要以表格的形式组织数据  

        同时也支持文件数据，只是表格的数据比较多。  

        主要方法，进程间共享数据的本质是：添加、删除、获取和修改数据。  
    
        ```Java
        <-- 4个核心方法 -->
        public Uri insert(Uri uri, ContentValues values)
        // 外部进程向 ContentProvider 中添加数据
        public int delete(Uri uri, String selection, String[] selectio
        nArgs)
        // 外部进程 删除 ContentProvider 中的数据
        public int update(Uri uri, ContentValues values, String select
        ion, String[] selectionArgs)
        // 外部进程更新 ContentProvider 中的数据
        public Cursor query(Uri uri, String[] projection, String selec
        tion, String[] selectionArgs, String sortOrder)
        // 外部应用 获取 ContentProvider 中的数据
        // 注：
        // 1. 上述4个方法由外部进程回调， 并运行在ContentProvider进程的Binder
        线程池中（ 不是主线程）
        // 2. 存在多线程并发访问， 需要实现线程同步
        // a. 若ContentProvider的数据存储方式是使用SQLite & 一个， 则不需要， 因为SQLite内部实现好了线程同步，
        // 若是多个SQLite则需要， 因为SQL对象之间无法进行线程同步
        // b. 若ContentProvider的数据存储方式是内存， 则需要自己实现线程同步
        <-- 2个其他方法 -->
        public boolean onCreate()
        // ContentProvider创建后 或 打开系统后其它进程第一次访问该ContentProvi
        der时 由系统进行调用
        // 注： 运行在ContentProvider进程的主线程， 故不能做耗时操作
        public String getType(Uri uri)
        // 得到数据类型， 即返回当前 Url 所代表数据的MIME类型
        ```

        也可以扩展 `Content Procider`来自定义需要的类。提供特定的数据处理方法，注意要在 `AndroidManifest.xml`中注册该procider并声明相应的 `Authority`等属性。

    - ContentObserver 类
  
        内容观察者，观察Uri引起contentprovider中的数据变化并通知外界。

        具体使用：

        ```Java
        // 步骤1： 注册内容观察者ContentObserver
        getContentResolver().registerContentObserver(uri);
        // 通过ContentResolver类进行注册， 并指定需要观察的URI
        // 步骤2： 当该URI的ContentProvider数据发生变化时，
        // 通知外界（ 即访问该ContentProvider数据的访问者）
        public class UserContentProvider extends ContentProvider {
        public Uri insert(Uri uri, ContentValues values) {
            db.insert("user", "userid", values);
            getContext().getContentResolver().notifyChange(uri, null);
            // 通知访问者
        }
        } 
        // 步骤3： 解除观察者
        getContentResolver().unregisterContentObserver（ uri） ；
        // 同样需要通过ContentResolver类进行解除
        ```

    
