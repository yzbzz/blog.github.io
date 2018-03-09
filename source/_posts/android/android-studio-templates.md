---
title: 使用Android Studio模板节省开发时间
date: 2018-03-05 17:42:53
tags: android
---

### 前言

使用模板能大大减少写重复代码的时间。Android Studio内置三种样式的模板

| 模板                    | 适用范围     |
| ----------------------- | ------------ |
| Live Templates          | 代码块       |
| File and Code Templates | 创建单个文件 |
| Plugin Templates        | 整个工程     |

Plugin Templates比较复杂，本文主要讲解前二个：Live Templates、File and Code Templates

<!--more-->

### Live Templates

**什么是`Live Templates`呢**

`Live Templates`就是把一些常用的代码块封装成几个快捷的字母，比如`abc`，当我们输入`abc`时，会自动生成封装好的代码。

以Toast为例，平时我们会这样写

```java
Toast.makeText(context, "", Toast.LENGTH_SHORT).show();
```

每当要显示Toast时，都要敲上面的代码，很麻烦。细心的同学会发现，当输入Toast时，会在提示栏显示一些快捷的操作

![android-studio-live-templates-toast](/img/android/android-studio-live-templates-toast.jpg)

我们点击第二项，会自动生成下面的代码

```java
 Toast.makeText(mContext, "", Toast.LENGTH_SHORT).show();
```

第二项中的Toast就是AndroidStudio内置的`Live Templates`

以`mac` 为例，可以通过下面的方式进入到`Live Templates`

Android Studio -> Preferences -> Editor -> Live Templates

![android-studio-live-templates](/img/android/android-studio-live-templates.jpg)

从图中，我们可以看到AndroidStudio已经内置了一些模板，点开`Android`栏，会看到一些常用的`Live Templates`，其中包含我们上面提到的`Toast`。

当然，我们也可以定义自己的`Live Templates`，下面我们来封装一个`AlertDialog`

![android-studio-live-templates-add](/img/android/android-studio-live-templates-add.jpg)

点击右边的`+`号，选择`Template Group…`新建一个组，组名我们这里填写`UI`，完成后会在左侧列表显示我们刚刚新建的`UI`组，选中`UI`，再次点击右侧的`+`号，这里我们选择`Live Templates`

![android-studio-live-templates-add-detail](/img/android/android-studio-live-templates-add-detail.jpg)

`Abbreviation`栏填写你要设置的快捷字母，这里我们填写`showDialog`，`Description`填写一个描述，这里我们填`显示AlertDialog`。

`Template Text`栏填写你要封装的代码块

```java
AlertDialog.Builder builder = new AlertDialog.Builder($context$);
builder.setTitle("");
builder.setMessage("");
builder.setNegativeButton("", new DialogInterface.OnClickListener() {
    @Override
    public void onClick(DialogInterface dialog, int which) {

    }
});
builder.setPositiveButton("", new DialogInterface.OnClickListener() {
    @Override
    public void onClick(DialogInterface dialog, int which) {

    }
});
builder.create().show();
```

下面蓝色的`Define`，点开选择`Java`，如下图：

![android-studio-live-templates-add-complete](/img/android/android-studio-live-templates-add-complete.jpg)

点击完成，我们的`Live Templates`就生成好啦，我们来试试在代码中输入`showDialog`

![android-studio-live-templates-add-show](/img/android/android-studio-live-templates-add-show.jpg)

点击提示栏的第一个，就会自动生成我们封装好的`AlertDialog`代码，是不是很方便呢^^

### File and Code Templates

我们点击`File -> New`的时候，会在右侧显示一些快捷项，供我们快速的创建文件

![android-stuido-file-templates](/img/android/android-stuido-file-templates.jpg)

有时候我们希望可以定义自己的`Templates`，用于快速生成相应文件，比如`RecycleView`的`Adapter`文件，很多都是差不多的格式。

以`mac` 为例，可以通过下面的方式进入到`File and Code Templates`

Android Studio -> Preferences -> Editor ->File and Code Templates

File -> New -> Edit File Templates

![android-studio-file-templates-add](/img/android/androd-studio-file-templates-add.jpg)

选中`Files`标签页，点击`+`号，`Name`栏输入`RVAdapter`，下面的输入框输入以下代码

```java
#if (${PACKAGE_NAME} && ${PACKAGE_NAME} != "")package ${PACKAGE_NAME};#end

import android.content.Context;
import android.support.v7.widget.RecyclerView;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import java.util.List;

#parse("File Header.java")
public class ${NAME} extends RecyclerView.Adapter<${VIEWHOLDER_CLASS}> {
    private final Context context;
    private List<${ITEM_CLASS}> items;
   
    public ${NAME}(List<${ITEM_CLASS}> items, Context context) {
        this.items = items;
        this.context = context;
    }

    @Override
    public ${VIEWHOLDER_CLASS} onCreateViewHolder(ViewGroup parent,
                                             int viewType) {
        View v = LayoutInflater.from(parent.getContext())
                .inflate(R.layout.${LAYOUT_RES_ID}, parent, false);
        return new ${VIEWHOLDER_CLASS}(v);
    }

    @Override
    public void onBindViewHolder(${VIEWHOLDER_CLASS} holder, int position) {
        ${ITEM_CLASS} item = items.get(position);
        //TODO Fill in your logic for binding the view.
    }

    @Override
    public int getItemCount() {
        if (items == null){
            return 0;
        }
        return items.size();
    }
    
     public class ${VIEWHOLDER_CLASS} extends RecyclerView.ViewHolder {

        public MyViewHolder(View itemView) {
            super(itemView);
        }
    }
}
```

点击`OK`，我们的`Templates`就创建好。

接下来，我们点击`File -> New`，就会出现我们创建好的`File Templates`项

![android-studio-file-tempates-add-complete](/img/android/android-studio-file-tempates-add-complete.jpg)

点击它，会出现下面的对话框

![android-studio-file-templates-add-show](/img/android/android-studio-file-templates-add-show.jpg)

- File name：填写你要创建的Adapter名称，例：TestRvAdapter
- VIEWHOLDER CLASS：填写你要创建的ViewHolder，例：MyViewHolder
- ITEM CLASS：填写你数据的类型，例: String
- LAYOUT RES ID：填写你的布局文件名称，例：acitivity_main

填写完成，我们的文件就生成好啦 

![android-studio-file-templates-show](/img/android/android-studio-file-templates-show.jpg)


OK，大功造成 ，是不是会节约很多时间呢^^


> 参考文章： [How to create your own file template in Android Studio](https://riggaroo.co.za/custom-file-templates-android-studio/)

