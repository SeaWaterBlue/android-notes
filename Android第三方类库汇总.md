# Android 第三方类库汇总

## XXPermissions

权限请求框架，适配 `Android12`

https://github.com/getActivity/XXPermissions

    maven { url 'https://jitpack.io' }
    implementation 'com.github.getActivity:XXPermissions:13.6'

## XPopup

各式各样的弹窗

https://github.com/li-xiaojun/XPopup

    maven { url 'https://jitpack.io' }

    implementation 'com.github.li-xiaojun:XPopup:2.8.1'
    //需要的依赖库
    implementation 'androidx.appcompat:appcompat:1.3.1'
    implementation 'com.google.android.material:material:1.4.0'
    implementation 'androidx.recyclerview:recyclerview:1.2.1'

## okdownload

文件下载框架，自带断点续传

https://github.com/lingochamp/okdownload

    maven { url "https://oss.sonatype.org/content/repositories/snapshots/" }

    implementation com.liulishuo.okdownload:okdownload:1.0.7-SNAPSHOT
    // provide sqlite to store breakpoints
    implementation com.liulishuo.okdownload:sqlite:1.0.7-SNAPSHOT
    // provide okhttp to connect to backend
    implementation com.liulishuo.okdownload:okhttp:1.0.7-SNAPSHOT



## ButterKnife

自动生成绑定id控件，减少 `findviewbyId`(可以被`ViewBinding`代替)

https://github.com/JakeWharton/butterknife

    implementation 'com.jakewharton:butterknife:10.2.3'
    annotationProcessor 'com.jakewharton:butterknife-compiler:10.2.3' 


## Zxing

摄像头识别条码框架，需集成代码使用

https://github.com/zxing/zxing

## Okhttp

网络请求框架

    implementation(platform("com.squareup.okhttp3:okhttp-bom:4.9.3"))

    // define any required OkHttp artifacts without version
    implementation("com.squareup.okhttp3:okhttp")
    implementation("com.squareup.okhttp3:logging-interceptor")

# EventBus

EventBus是一个轻量级的事件发布/订阅的库，可以替代Android传统的Intent,Handler,Broadcast或接口函数,在Fragment,Activity,Service线程之间传递数据，

    implementation("org.greenrobot:eventbus:3.3.1")