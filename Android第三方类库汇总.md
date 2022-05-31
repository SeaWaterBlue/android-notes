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



## ButterKnife

自动生成绑定id控件，减少 `findviewbyId`

https://github.com/JakeWharton/butterknife

    implementation 'com.jakewharton:butterknife:10.2.3'
    annotationProcessor 'com.jakewharton:butterknife-compiler:10.2.3' 