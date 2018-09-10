---
title: android-tips
date: 2016-05-31 21:53:23
tags: android
---



**解决Kotlin项目Parameter specified as non-null is null: method kotlin.jvm.internal.Intrinsics.checkParameterIsNotNull, parameter savedInstanceState**

当我们自定义application并实现ActivityLifecycleCallbacks方法，这时候我们会重写onActivityCreated这个方法

使用kotlin转换java代码，会是下面这样

```kotlin
override fun onActivityCreated(activity: Activity, savedInstanceState: Bundle) {

}
```

这个时候我们需要把savedInstanceState: Bundle改为savedInstanceState: Bundle?，就是在Bundle后面加个?号

```kotlin
override fun onActivityCreated(activity: Activity, savedInstanceState: Bundle?) {
  
}
```

OK，又可以继续玩耍了

### Android allowBackup敏感信息泄露的一些解决方案

- 直接在你的Android清单文件中设置`android:allowBackup=”false”`即可，如下：

  ```java
  <application
    	android:name=".app.App"
      android:allowBackup="true">
  ```

- 不在你的Android清单文件中设置`android:allowBackup=”false”`，允许执行备份，但是在你应用启动页进行逻辑判断是否进行重新登陆等，譬如查看设备唯一识别设备编号和备份前是否一致，不一致则直接跳转登陆页面的同时清空当前应用数据及缓存。

### 设置背景颜色偶现无效

最近在开发中碰到了一个问题，在Android5.0以上的手机，对布局设置背景颜色

```java
 android:background="@color/c_4897fa"
```

会出现背景颜色偶现无效的问题，使用下面的方法解决

```java
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">
    <!-- 角度 -->
    <corners android:radius="1dp" />
    <!-- 填充色 -->
    <solid android:color="@color/c_4897fa" />
</shape>
```

```java
android:background="@drawable/home_nearby_bg"
```

先画一个shape，再设置给backgroud

### 清除Android WebView 缓存

```java
public static void clearWebCache(Context context) {
  try {
    CookieSyncManager.createInstance(context);
    CookieManager cm = CookieManager.getInstance();
    cm.removeSessionCookie();
    cm.removeAllCookie();
    CookieSyncManager.getInstance().sync();

    WebStorage.getInstance().deleteAllData();
  } catch (Exception e) {
    e.printStackTrace();
  }
}
```
### 捕获全局异常

使用`Thread.UncaughtExceptionHandler`捕获全局异常

### 启动LaunchIntent

```java
Intent i = getBaseContext().getPackageManager()
.getLaunchIntentForPackage(getBaseContext().getPackageName());
i.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
startActivity(i);
```

### Annotations

```java
@CallSuper
@StringRes
@DrawableRes
@CheckResult
@NonNull
@Nullable
@AnimatorRes
@AnimRes
@AnyRes
@AnyThread
@ArrayRes
@AttrRes
@BinderThread
@ColorRes
@ColorInt
@BoolRes
@DimenRes
@Dimension
@IdRes
@FloatRange
@FractionRes
@IntegerRes
@XmlRes
```
**关于Android7.0系统使用webview遇到的一个问题(二级跳转后界面空白**

https://github.com/panyz/Blogs/blob/master/Android实战经验/关于Android7.0系统使用webview遇到的一个问题(二级跳转后界面空白).md

**RecyclerView去掉滚动条和滑动到边界阴影**

```java
<android.support.v7.widget.RecyclerView
    android:id="@+id/rv_search_one"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:overScrollMode="never"
    android:scrollbars="none" />
```
**设置屏幕常亮**

```java
@Override
public void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);
  getWindow().addFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);
}
@Override
protected void onDestroy() {
  super.onDestroy();
  getWindow().clearFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);
}
```

**使用PhotoView+Viewpager崩溃问题**

java.lang.IllegalArgumentException: pointerIndex out of range

```java
// 自定义一个Viewpager，重写onInterceptTouchEvent函数，在里面捕获IllegalArgumentException
public class PhotoViewPager extends android.support.v4.view.ViewPager {
    public PhotoViewPager(Context context) {
        super(context);
    }

    public PhotoViewPager(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    @Override
    public boolean onTouchEvent(MotionEvent ev) {
        try {
            return super.onTouchEvent(ev);
        } catch (IllegalArgumentException ex) {
            ex.printStackTrace();
        }
        return false;
    }

    @Override
    public boolean onInterceptTouchEvent(MotionEvent ev) {
        try {
            return super.onInterceptTouchEvent(ev);
        } catch (IllegalArgumentException ex) {
            ex.printStackTrace();
        }
        return false;
    }
}
```
**WebView自适应高度**

```java
private void setupWebView() {
    webView.getSettings().setJavaScriptEnabled(true);
    webView.setWebViewClient(new WebViewClient() {
        @Override
        public void onPageFinished(WebView view, String url) {
            		webView.loadUrl("javascript:MyApp.resize(document.body.getBoundingClientRect().height)");
            super.onPageFinished(view, url);
        }
    });
    webView.addJavascriptInterface(this, "MyApp");
}

@JavascriptInterface
public void resize(final float height) {
    MyActivity.this.runOnUiThread(new Runnable() {
        @Override
        public void run() {
            webView.setLayoutParams(new LinearLayout.LayoutParams(getResources().getDisplayMetrics().widthPixels, (int) (height * getResources().getDisplayMetrics().density)));
        }
    });
}
```
http://www.bubuko.com/infodetail-1612451.html

**通过resId获取属性名**

例：R.id.tv_hello = 0x7f080007

当我们调用view的getId()时，我们能获取到0x7f080007，但有时我们需要获取到tv_hello这个属性名，可以使用下面的方法

```
String name = getResources().getResourceEntryName(id);
```

**使用AspectJ配置**

使用网上的配置，在gradle版本为2.2.3时，不会出现问题，当切换到2.3.3时，会报

No such property: project for class: com.android.build.gradle.LibraryPlugin，

修复办法：不使用 LibraryPlugin 直接使用 project

```java
android.libraryVariants.all { variant ->
//    LibraryPlugin plugin = project.plugins.getPlugin(LibraryPlugin)
    JavaCompile javaCompile = variant.javaCompile
    javaCompile.doLast {
        String[] args = ["-showWeaveInfo",
                         "-1.5",
                         "-inpath", javaCompile.destinationDir.toString(),
                         "-aspectpath", javaCompile.classpath.asPath,
                         "-d", javaCompile.destinationDir.toString(),
                         "-classpath", javaCompile.classpath.asPath,
                         "-bootclasspath", project.android.bootClasspath.join(
                File.pathSeparator)]

        MessageHandler handler = new MessageHandler(true);
        new Main().run(args, handler)

        def log = project.logger
        for (IMessage message : handler.getMessages(null, true)) {
            switch (message.getKind()) {
                case IMessage.ABORT:
                case IMessage.ERROR:
                case IMessage.FAIL:
                    log.error message.message, message.thrown
                    break;
                case IMessage.WARNING:
                case IMessage.INFO:
                    log.info message.message, message.thrown
                    break;
                case IMessage.DEBUG:
                    log.debug message.message, message.thrown
                    break;
            }
        }
    }
}
```

**解决支持库版本兼容问题：**

(all com.android.support libraries must use the exact same version specification)

去改第三方库所用的支持库版本比较麻烦，如果用的库很多的话工作量很大。这个时候我们可以考虑强制让所有模块都用相同的支持库版本。

在app build.gradle中添加：

```java
configurations.all {
    resolutionStrategy.eachDependency { DependencyResolveDetails details ->
        def requested = details.requested
        if (requested.group == 'com.android.support') {
            if (!requested.name.startsWith("multidex")) {
                details.useVersion '25.3.1'
            }
        }
    }
}
```

其中，25.3.1就是你要使用的支持库版本号，你可以根据需要改成其它的。



[error: style attribute '@android:attr/windowEnterAnimation' not found.](http://blog.csdn.net/u012730980/article/details/78390428)

在Project/gradle.properties中添加 android.enableAapt2=false

**解决Google Pixel Download Manager不在通知栏显示进度的BUG**

```java
DownloadManager.Request request = new DownloadManager.Request(Download_Uri);
// 添加下面这行
request.setAllowedOverRoaming(false);
```

**解决Error:Some file crunching failed, see logs for details**

```java
android {

    compileSdkVersion 25
    buildToolsVersion '25.0.3'
    useLibrary 'org.apache.http.legacy'

    aaptOptions {
        cruncherEnabled = false
        useNewCruncher = false
    }
}
```

添加aaptOptions一栏

**解决viewpager滑动不流畅的问题**

```java
public class customViewpagerView extends ViewPager {  
  
    private int preX=0;  
    public customViewpagerView(Context context, AttributeSet attrs) {  
        super(context, attrs);  
          
    }  
    public customViewpagerView(Context context) {  
        super(context);  
          
    }  
      
    @Override  
    public boolean onInterceptTouchEvent(MotionEvent even) {  
          
        if(even.getAction()==MotionEvent.ACTION_DOWN)  
        {  
            preX=(int) even.getX();  
        }else  
        {  
            if(Math.abs((int)even.getX()-preX)>10)  
            {  
                return true;  
            }else  
            {  
                preX=(int) even.getX();  
            }  
        }  
        return super.onInterceptTouchEvent(even);  
    }  
}  
```

# 解决设置控件Alpha透明引发的问题

```
findViewById(R.id.login_whitebg)).getBackground().mutate().setAlpha(204);
// https://www.jianshu.com/p/4d89fe683664
```

