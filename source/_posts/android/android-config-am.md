---
title: Android配置文件详解-AndroidManifest
date: 2017-02-22 11:51:47
tags: android
---

# AndroidManifest

> 每个应用的根目录中都必须包含一个AndroidManifest.xml文件(且文件名精确无误)。AndroidManifest向Android系统提供应用的必要信息，系统必须具备有这些信息方可运行应用的任何代码。
> 此外，AndroidManifest还可执行以下操作

- 为应用的Java软件包命名。软件包名称充当应用的唯一标识符。
- 描述应用的各个组件，包括构成应用的 Activity、服务、广播接收器和内容提供程序。它还为实现每个组件的类命名并发布其功能，例如它们可以处理的 [Intent](https://developer.android.com/reference/android/content/Intent.html) 消息。这些声明向 Android 系统告知有关组件以及可以启动这些组件的条件的信息。
- 确定托管应用组件的进程。
- 声明应用必须具备哪些权限才能访问 API 中受保护的部分并与其他应用交互。还声明其他应用与该应用组件交互所需具备的权限。
- 列出 [Instrumentation](https://developer.android.com/reference/android/app/Instrumentation.html) 类，这些类可在应用运行时提供分析和其他信息。这些声明只会在应用处于开发阶段时出现在清单中，在应用发布之前将移除。
- 声明应用所需的最低 Android API 级别
- 列出应用必须链接到的库

注：准备要在 Chromebook 上运行的 Android 应用时，要考虑一些重要的硬件和软件功能限制。如需了解详细信息，请参阅 [Chromebook 的应用清单兼容性](https://developer.android.com/topic/arc/manifest.html)文档。

<!--more-->

## AndroidManifest文件结构

下面是AndroidManifest文件的示例：

```java
<?xml version="1.0" encoding="utf-8"?>

<manifest>

    <uses-permission />
    <permission />
    <permission-tree />
    <permission-group />
    <instrumentation />
    <uses-sdk />
    <uses-configuration />  
    <uses-feature />  
    <supports-screens />  
    <compatible-screens />  
    <supports-gl-texture />  

    <application>
  		<meta-data />
        <activity>
            <intent-filter>
                <action />
                <category />
                <data />
            </intent-filter>
            <meta-data />
        </activity>

        <activity-alias>
            <intent-filter> . . . </intent-filter>
            <meta-data />
        </activity-alias>

        <service>
            <intent-filter> . . . </intent-filter>
            <meta-data/>
        </service>

        <receiver>
            <intent-filter> . . . </intent-filter>
            <meta-data />
        </receiver>

        <provider>
            <grant-uri-permission />
            <meta-data />
            <path-permission />
        </provider>

        <uses-library />

    </application>

</manifest>
```

### AndroidManifest属性

##### Application

```java
<application  
  	android:allowBackup=["true" | "false"]
	android:allowClearUserData=["true" | "false"]
    android:allowTaskReparenting=["true" | "false"]
    android:backupAgent="string"
    android:backupInForeground=["true" | "false"]
    android:banner="drawable resource" 
    android:debuggable=["true" | "false"]
    android:description="string resource"
    android:enabled=["true" | "false"]
    android:extractNativeLibs=["true" | "false"]
    android:fullBackupContent="string"
    android:fullBackupOnly=["true" | "false"]
    android:hasCode=["true" | "false"]
    android:hardwareAccelerated=["true" | "false"]
    android:isGame=["true" | "false"]
    android:icon="drawable resource"
    android:killAfterRestore=["true" | "false"]
    android:label="string resource"
    android:largeHeap=["true" | "false"]
    android:logo="drawable resource"
    android:manageSpaceActivity="string"
    android:name="string"
    android:permission="string"
    android:persistent=["true" | "false"]
    android:process="string"
    android:restoreAnyVersion=["true" | "false"]
    android:requiredAccountType="string"
    android:resizeableActivity=["true" | "false"]
    android:restrictedAccountType="string"
    android:supportsRtl=["true" | "false"]
    android:taskAffinity="string"
    android:testOnly=["true" | "false"]
    android:theme="resource or theme" 
    android:uiOptions=["none" | "splitActionBarWhenNarrow"]
    android:usesCleartextTraffic=["true" | "false"]
    android:vmSafeMode=["true" | "false"]>
</application>
```

**android:allowBackup**
开启或关闭应用程序数据的备份和恢复功能，注意该属性值默认为true，如果你不需要你的应用被恢复导致隐私数据暴露（如果值为true，甚至可以直接通过adb命令获取该应用中的数据），必须手动设置此属性
```bash
// 备份
adb backup -nosystem -noshared -apk -f d:/com.test.bak.ab com.test.bak

-nosystem: 不备份系统应用
-noshared: 不备份应用存储在SD中的数据
-apk: 备份应用APK安装包
-f d:/com.test.bak.ab: 备份文件在PC上的路径
com.test.bak: 要备份的包名

// 恢复
adb restore d:/com.test.bak.ab
```


**android:allowClearUserData**
默认为`true`。用户是否能选择自行清除数据，程序管理器包含一个选择允许用户清除数据。当为true时，用户可自己清理用户数据，反之亦然

**android:allowTaskReparenting(‘true’ or ‘false’)**
默认值为`false`。是否允许activity`更换从属的任务。当该`Task`下一次被带到前面时，应用程序定义的`Activity`是否可以从启动它们的`Task`移动到有相同`affinity`的Task，比如从短信息任务切换到浏览器任务。Activity可以设置自己的`allowTaskReparenting`属性来覆盖Application节点下的这个属性。

**android:backupAgent**
此属性没有默认值。实现应用backup代理的类名，它是BackupAgent的一个子类。这个属性值应该是一个完整的类名，如com.project.BackupAgent.。如果类名的第一个字母是"."，它会附加到`mainfest`元素指定的包名之后。

**android:backupInForeground**
默认为`false`。一个应用即使在前台状态下也可以执行自动备份。当应用正在自动备份时，系统可能会停止应用，因此要谨慎的使用该属性。该属性设置为true会影响到处于激活状态下的应用的行为。

**android:banner**
没有默认值。一个drawable资源，用来提供一个扩展的图形banner。使用`application`标签为应用所有的Activity提供一个默认的banner，或者使用`activity`标签为一个指定的Activity设置banner。在`Android TV`的home页，系统使用banner来表示一个应用。由于banner仅仅在home页显示，当一个应用中有处理`CATEGORY_LEANBACK_LAUNCHER`这个intent的Activity时，才能指定该属性。这个属性必须设置一个包含图片的drawable资源的引用(例如"@drawable/banner")。

**android:debuggable**
默认为`false`。这个从字面上就可以看出是什么作用的，当设置为true时，表明该APP在手机上可以被调试。在false的情况下调试该APP，就会报以下错误：
Device XXX requires that applications explicitely declare themselves as debuggable in their manifest.
Application XXX does not have the attribute ‘debuggable’ set to TRUE in its manifest and cannot be debugged.

**android:description**
关于应用的用户可读的文字，比label属性更长、更具描述性。这个属性必须设置为一个字符串资源的引用。不像label属性，它不能是一个raw字符串。没有默认值

**android:enabled**
默认为`true`。Android系统是否能够实例化该应用程序的组件-true表示可以，false表示不可以。如果为true，每个组件的enabled属性决定那个组件是否可以被 enabled。如果为false，它覆盖组件指定的值，即所有组件都是disabled。

**android:extractNativeLibs**
默认值为true。包安装器能否从apk中抽取本地库到文件系统。如果设置为false，你的本地库必须页对其并且在apk中以未压缩的方式存储。链接器在运行时从apk中直接加载库不会导致代码改动。

**android:fullBackupContent**
该属性指向一个xml文件，这个文件包含了自动备份时全部的备份规则。这些规则决定了哪些文件会被备份。该属性为可选的。如果没设置，默认情况下，自动备份会备份应用的大部分的文件。

**android:fullBackupOnly=["true" | "false"]**
默认值是`false`。当设备可用时是否使用自动备份。如果设置为true，当应用安装到运行Android 6.0及其更高版本的的设备上时，会运行自动备份，在低版本的设备上，应用将忽略这个属性并执行key/value方式的备份。

**android:hasCode(‘true’ or ‘false’)**
默认为`true`。表示此APP是否包含任何的代码。若为false，则系统在运行组件时，不会去尝试加载任何的APP代码
一个应用程序自身不会含有任何的代码，除非内置组件类，比如Activity类，此类使用了AliasActivity类，当然这是个罕见的现象
(在Android2.3可以用标准C来开发应用程序，可在androidManifest.xml中将此属性设置为false,因为这个APP本身已经不含有任何的JAVA代码了)

**android:hardwareAccelerated=["true" | "false"]**
如果你设置了minSdkVersion或targetSdkVersion 为14或更高，默认是启用；否则默认不启用。应用中所有的Activity和View是否启用硬件加速渲染-true表示启用，false表示不启用。
从 Android 3.0 (API level 11)开始，应用可使用支持硬件加速的OpenGL 渲染，用来提升许多通用的2D图形操作性能。当硬件加速渲染打开时，Canvas, Paint, Xfermode, ColorFilter, Shader, 和 Camera中的大部分操作都会被加速。这会使得动画更流畅、滑动更流畅，提升整体的响应性，甚至应用不必显式的使用framework的 OpenGL库。
需要注意的是，不是全部的OpenGL 2D操作都会被加速。如果你打开了硬件加速渲染，请测试你的应用以确保使用渲染的过程中不会出错。

**android:icon**
没有默认图标。一个作为整体应用的图标，就是声明整个APP的图标(在桌面的启动图标,图片一般都放在drawable文件夹下)，并且是应用的每个组件的默认图标。 < activity>, < activity-alias>, < service>, < receiver>和 < provider>都可以有各自的图标。这个属性必须被设置为一个包含图片的drawable资源文件的引用（比如"@drawable/icon"）。

**android:isGame=["true" | "false"]**
应用是否为游戏。系统可能把应用根据游戏分组或把它们与其他的应用分开来显示。默认为false。

**android:killAfterRestore**
`默认值为true`。当应用在全系统层面的恢复操作时，应用的设置被恢复后，应用应当终止运行。单个包的恢复操作不会导致应用关闭。全系统的恢复操作一般来说只会发生一次，即在手机第一次启动时。第三方的应用一般没必要使用这个属性。意味着当应用在全系统的恢复操作中处理完自己的数据后，将会停止运行。

**android:largeHeap=["true" | "false"]**
你的应用进程是否通过一个largeDalvik heap 被创建。这个属性适用于所有的为应用创建的进程。它仅适用于被加载到进程的第一个应用；如果你使用shared user id来允许多个应用在同一个进程中，它们都必需一致的使用这个选项，否则可能出现不可预知的结果。
大部分app都不应使用这个属性，而应该把焦点放在如何减少整体的内存使用来提示性能。启用此功能也不能保证可用内存的固定增加，因为一些设备被全部可用内存限制了。
可使用ActivityManager.getMemoryClass() or ActivityManager.getLargeMemoryClass()在运行时获取可用的内存大小。

**android:label**
应用的标签名(应用名称)。把应用作为整体的用户可读标签，也是应用中各个组件的默认标签。 < activity>, < activity-alias>, < service>, < receiver>, < provider> 都可以设置各自的标签。标签应设置为一个字符串资源的引用，以便可以像用户界面中的其他字符串一样进行本地化。为了方便，在开发应用时可以设置为原始字符串。

**android:logo="drawable resource"**
应用作为一个整体的logo，也是所有Activity的默认logo。这个属性必须被设置为一个包含图片的drawable资源的引用（比如@drawable/logo）。没有默认logo。默认情况下，使用在ActionBar中使用icon，如果同时也设置了logo属性，ActionBar上将使用logo。logo应比icon远，但不应包括不必要的文本。 只有在以用户认可的传统格式表示您的品牌时才应使用logo。 logo代表预期的使用者品牌，而应用程序的的icon是符合启动器图示方形要求的修改版本。

**android:manageSpaceActivity**
一个Activity子类的全限定名，系统可以启动它来让用户管理设备上应用的内存使用情况。这个Activity也必须使用< activity> 元素来声明。

**android:name**
为应用程序所实现的Application子类的全名。当应用程序进程开始时，该类在所有应用程序组件之前被实例化。
若该类(比方androidMain类)是在声明的package下，则可以直接声明android:name=”androidMain”,但此类是在package下面的子包的话，就必须声明为全路径或android:name=”package名称.子包名成.androidMain”

**android:permission**
为了跟应用交互，客户端必须拥有的权限。这个属性是适用于应用中所有组件设置权限的一个方便的方法。每个组件可设置各自的permission属性来覆盖application中的这个属性。

**android:presistent**
默认值是false。该应用程序是否应该在任何时候都保持运行状态,默认为false。因为应用程序通常不应该设置本标识，持续模式仅仅应该设置给某些系统应用程序才是有意义的。

**android:process**
应用程序运行的进程名，它的默认值为元素里设置的包名，当然每个组件都可以通过设置该属性来覆盖默认值。如果你想两个应用程序共用一个进程的话，你可以设置他们的android:process相同，但前提条件是他们共享一个用户ID及被赋予了相同证书的时候

**android:restoreAnyVersion**
此属性的默认值为`false`。用来表明应用是否准备尝试恢复所有的备份，即使该备份是由比当前在设备上安装的应用程序的更新版本存储的。 将此属性设置为true将允许备份管理器尝试恢复，即使版本不匹配表明数据不兼容。 使用时要小心！。

**android:requiredAccountType="string"**
指定应用程序为了运行所需的帐户类型。 如果您的应用需要帐户，则此属性的值必须与您的应用使用的帐户验证器类型（由AuthenticatorDescription定义）（如“com.google”）相对应。默认值为null，表示应用程序可以在没有任何帐户的情况下工作。
由于受限个人资料目前无法添加帐户，因此指定此属性会使您的应用无法从受限个人资料中获取，除非您也声明了android：restrictedAccountType具有相同的值。
警告：如果帐户数据可能显示个人身份信息，请务必声明此属性并将android：restrictedAccountType设置为null，以便受限配置文件无法使用您的应用访问属于所有者用户的个人信息。
此属性在API级别18中添加。

**android:resizeableActivity=["true" | "false"]**
指定应用是否支持多窗口显示。你可以在 < activity> 或 < application>元素中设置该属性。
如果设置为true，用户可在分屏和freeform 模式下启动Activity。如果设置为false，Activity将不支持多窗口模式。如果这个值为false，并且用户尝试在多窗口模式下启动Activity，Activity将会全屏显示。
如果你的应用的 targets API 是24或者更高，如果你不指定这个属性，默认值是true。这个属性是在 API level 24新增的。

**android:restrictedAccountType="string"**
指定此应用程序所需的帐户类型，并指示允许受限制的配置文件访问属于所有者用户的此类帐户。 如果您的应用需要帐户，且受限个人资料可以访问主要用户的帐户，则此属性的值必须与您的应用所使用的帐户验证器类型（由AuthenticatorDescription定义）（如“com.google”）相对应。默认值为null，表示应用程序可以在没有任何帐户的情况下工作。
警告：指定此属性允许受限配置文件使用属于所有者用户的帐户使用您的应用程序，这可能会泄露个人身份信息。 如果帐户可能显示个人详细信息，您不应使用此属性，而应声明android：requiredAccountType属性，使您的应用程序不能使用限制的配置文件。
此属性在API级别18中添加。

**android:supportsRtl=["true" | "false"]**
默认值是`false`。声明你的应用是否支持从右向左布局。
如果设置为true并且targetSdkVersion 大于等于17，多个RTL相关的API将被激活，被系统使用，从而使得你的app能够显示从右向左的布局。
如果设置为false或targetSdkVersion小于17，RTL相关的API将无效或者被忽略，你的app将无视布局方向，始终是从左向右的方向。
在API level 17中引入的这个属性。

**android:taskAffinity**
拥有相同的affinity的Activity理论上属于相同的Task，应用程序默认的affinity的名字是元素中设定的package名（manifest中通过 < manifest>元素设置的包名）。除了那些设置了不同affinity属性的Activity

**android:testOnly=["true" | "false"]**
表示一个应用是否仅仅用于测试。例如，它可能暴露它自己的功能或外部数据，这些可能导致一个安全漏洞，但对调试来说有用。这类应用仅仅可以通过adb命令安装。

**android:theme**
是一个资源的风格，它定义了一个默认的主题风格给所有的activity，可以后续在activity标签层单独覆盖此Theme。

**android:uiOptions=["none" | "splitActionBarWhenNarrow"]**
Activity UI的额外选项，必须是下列值中的一个。
* "none"： 没有额外的UI选项，这是默认值。
* "splitActionBarWhenNarrow"：在屏幕底部添加一个条形，以在水平空间受限时（例如在手机上的纵向模式下）在应用栏（也称为操作栏）中显示操作项目。 而不是显示在屏幕顶部的应用栏中的少量操作项，应用栏分割为顶部导航部分和操作项的底部栏。 这确保了不仅为操作项目而且在顶部的导航和标题元素提供合理的空间量。 菜单项不分割在两个条上; 他们总是出现在一起。
  该属性是在 API level 14中新增的。

**android:usesCleartextTraffic=["true" | "false"]**
默认值为`true`。指示应用程序是否要使用明文网络流量，例如明文HTTP。当属性设置为“false”时，平台组件（例如，HTTP和FTP堆栈，DownloadManager，MediaPlayer）将拒绝应用程序使用明文流量的请求。强烈鼓励第三方图书馆也遵守此设置。避免明文流量的关键原因是缺乏保密性，真实性和防止篡改的保护：网络攻击者可以窃听传输的数据，并且还可以在不检测的情况下对其进行修改。
这个标志是最好的努力的基础上，因为它是不可能防止来自Android应用程序的所有明文流量给定的访问级别提供给他们。例如，没有期望Socket API将尊重此标志，因为它不能确定其流量是否为明文。然而，来自应用程序的大多数网络流量由更高级网络堆栈/组件处理，可以通过从ApplicationInfo.flags或NetworkSecurityPolicy.isCleartextTrafficPermitted（）读取该标志来处理该标志。
注意：WebView不遵守此标志。
在应用程序开发期间，StrictMode可用于标识来自应用程序的任何明文流量：请参阅StrictMode.VmPolicy.Builder.detectCleartextNetwork（）。
此属性是在API级别23中添加的。
如果存在Android网络安全配置，此标记在Android 7.0（API级别24）及以上版本中将被忽略。

**android:vmSafeMode=["true" | "false"]**
表示应用让虚拟机运行在安全模式。默认值为false。这个属性在 API level 8 中引入，true表示禁用DalvikJIT编译器。在API level 22 中有改动，true表示禁用ART AOT编译器
