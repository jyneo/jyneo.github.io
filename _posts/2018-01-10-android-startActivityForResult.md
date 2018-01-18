---
layout: post
title: startActivityForResult 用法分析
key: 20180110
tags: android
category: blog
date: 10-01-2018
modify_date: 18-01-2018
---

## 使用场景

在一个主 Activity 通过意图跳转到多个不同子 Activity，当子 Activity 的代码执行完毕后再次返回主 Activity 时，如果需要将子 Activity 中返回的数据交给主 Activity 处理，这种带数据的意图跳转需要使用 startActivityForResult() 方法。

- startActivityForResult(Intent intent, int requestCode);

    - 第一个参数：一个 Intent 对象，用于携带将跳转至下一个界面中使用的数据，使用putExtra() 方法，可以把想要传递的数据暂存在 Intent 中。

    - 第二个参数：请求码，用于在之后的回调中判断数据来源于哪个 Activity。
<!--more-->

- onActivityResult(int requestCode, int resultCode, Intent data)

    - 第一个参数：requestCode 用于与 startActivityForResult() 中的 requestCode 值进行比较，以此来判断返回的数据是从哪个 Activity 返回的。

    - 第二个参数：resultCode 是由子 Activity 通过 setResult() 方法返回，用于多个Activity 都返回数据时，来标识到底是哪一个 Activity 返回的值。

    - 第三个参数：一个Intent对象，带有返回的数据，可以通过 data.getXxxExtra() 方法来获取指定数据类型的数据。

- setResult(int resultCode, Intent data)

    在目的 Activity 中调用这个方法把 Activity 想要返回的数据返回到主 Activity中。

    - 第一个参数：当子 Activity 结束时，将回调 onActivityResult()，resultCode 一般为RESULT_CANCELED, RESULT_OK默认为 -1。

    - 第二个参数：一个 Intent 对象，返回给主 Activity 的数据。

至于 requestCode 和 resultCode 的分析，没有比 [Ruthless](http://www.cnblogs.com/linjiqin/archive/2011/06/03/2071956.html) 的这篇博客分析得更清楚的了。

## 请求码 requestCode

使用 startActivityForResult(Intent intent, int requestCode) 方法打开新的 Activity，我们需要为 startActivityForResult() 方法传入一个请求码(第二个参数)。请求码的值是根据业务需要由自已设定，用于标识请求来源。

使用场景分析：一个 Activity 有两个按钮，点击这两个按钮都会打开同一个 Activity，不管是哪个按钮打开新 Activity，当这个新 Activity 关闭后，系统都会调用前面 Activity 的onActivityResult(int requestCode, int resultCode, Intent data) 方法。在 onActivityResult() 方法如果需要知道新 Activity 是由哪个按钮打开的，并且要做出相应的业务处理时，可以这样做：
```
@Override
public void onCreate(Bundle savedInstanceState) {
    ....
    button1.setOnClickListener(new View.OnClickListener() {
        public void onClick(View v) {
            startActivityForResult (new Intent(MainActivity.this, NewActivity.class), 1);
        }
    });

    button2.setOnClickListener(new View.OnClickListener() {
        public void onClick(View v) {
            startActivityForResult (new Intent(MainActivity.this, NewActivity.class), 2);
        }
    });
}

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    switch(requestCode) {
        case 1:
            // 来自按钮1的请求，作相应业务处理
        case 2:
            // 来自按钮2的请求，作相应业务处理
    }
}
```

## 结果码 resultCode

使用场景分析：在一个 Activity 中，可能会使用 startActivityForResult() 方法打开多个不同的 Activity 处理不同的业务，当这些新 Activity 关闭后，系统都会调用前面 Activity 的 onActivityResult(int requestCode, int resultCode, Intent data) 方法。为了知道返回的数据来自于哪个新 Activity，在 onActivityResult() 方法中可以这样做:
```
public class NewActivity1 extends Activity {
    .....
    NewActivity1.this.setResult(1, intent);
    NewActivity1.this.finish();
}

public class NewActivity2 extends Activity {
    ......
    NewActivity2.this.setResult(2, intent);
    NewActivity2.this.finish();
}

public class MainActivity extends Activity {
    // 在该 Activity 会打开 NewActivity1 和 NewActivity2
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data {
        switch(resultCode) {
            case 1:
                // NewActivity1 的返回数据
            case 2:
                // NewActivity2 的返回数据
        }
    }
}
```