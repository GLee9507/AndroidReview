# <center> 四方园Android开发规范手册</center>

- [Changelog](#changelog)
- [1 Android资源文件](#1-android%E8%B5%84%E6%BA%90%E6%96%87%E4%BB%B6)
    - [1.1 layout文件命名](#11-layout%E6%96%87%E4%BB%B6%E5%91%BD%E5%90%8D)
    - [1.2 shape文件命名](#12-shape%E6%96%87%E4%BB%B6%E5%91%BD%E5%90%8D)
    - [1.3 selector文件命名](#13-selector%E6%96%87%E4%BB%B6%E5%91%BD%E5%90%8D)
    - [1.4 color文件命名](#14-color%E6%96%87%E4%BB%B6%E5%91%BD%E5%90%8D)
    - [1.5 style文件命名](#15-style%E6%96%87%E4%BB%B6%E5%91%BD%E5%90%8D)
    - [1.6 anim文件命名](#16-anim%E6%96%87%E4%BB%B6%E5%91%BD%E5%90%8D)
    - [1.7 dimen资源命名](#17-dimen%E8%B5%84%E6%BA%90%E5%91%BD%E5%90%8D)
    - [1.7 控件ID资源命名](#17-%E6%8E%A7%E4%BB%B6id%E8%B5%84%E6%BA%90%E5%91%BD%E5%90%8D)
- [2 Android基础组件](#2-android%E5%9F%BA%E7%A1%80%E7%BB%84%E4%BB%B6)
- [3 布局](#3-%E5%B8%83%E5%B1%80)
- [4 进程、线程通信](#4-%E8%BF%9B%E7%A8%8B%E3%80%81%E7%BA%BF%E7%A8%8B%E9%80%9A%E4%BF%A1)
- [5 文件、数据库](#5-%E6%96%87%E4%BB%B6%E3%80%81%E6%95%B0%E6%8D%AE%E5%BA%93)
- [6 Bitmap、Drawable与动画](#6-bitmap%E3%80%81drawable%E4%B8%8E%E5%8A%A8%E7%94%BB)
- [6 安全](#6-%E5%AE%89%E5%85%A8)
- [7 内存泄露](#7-%E5%86%85%E5%AD%98%E6%B3%84%E9%9C%B2)
- [8 其他建议](#8-android%E5%BC%80%E5%8F%91%E5%BB%BA%E8%AE%AE)

## Changelog

| 版本号 | 制定人 | 更新日期  | 备注 |
| :----: | :----: | :-------: | :--- |
| 0.0.1  | 李季   | 2018.3.13 | 无   |

## 1 Android资源文件
### 1.1 layout文件命名

格式：` 模块名_布局所属_业务功能`

**exp:**

    Activity：module_activity_main
    Fragment：module_fragment_home
    include：module_include_login
    recycler item：module_recycler_item_msg
    Dialog：module_dialog_logout

### 1.2 shape文件命名

格式：`模块名_shape_业务功能_控件_控件状态`

**exp:**

    module_ shape_cancel_btn_normal
    module_ shape_cancel_btn_pressd

### 1.3 selector文件命名

格式：`模块名_selector_业务功能`

**exp:**

    module_ selector_cancel_btnl

### 1.4 color文件命名
color使用#AARRGGBB格式，统一写入module_colors.xml中

格式：`模块名_逻辑名_颜色`

**exp:**

    <color name="module_login_btn_red">#ffff0000</color>

### 1.5 style文件命名

 格式：`父名称.子名称`

**exp:**

    <style name="ParentTheme.MyTheme">
        …
    </style>

### 1.6 anim文件命名
color使用#AARRGGBB格式，统一写入module_colors.xml中

格式：`模块名_逻辑名_方向||序号`

**exp:**

    module_login_push_in
    module_login_push_out

    帧动画
    module_app_start_01
    module_app_start_02
    module_app_start_03

### 1.7 dimen资源命名
dimen使用#AARRGGBB格式，统一写入module_dimen.xml中

格式：`模块名_描述`

**exp:**

    <dimen name="module_horizontal_login_height">100dp</dimen>

### 1.7 控件ID资源命名
id以驼峰法命名，并采用控件缩写

**exp:**

    Button btnLogin;    //登录按钮

**缩写表**

| 控件             | 缩写  |
| :--------------: | :---: |
| Button           | btn   |
| EditText         | et    |
| ListView         | lv    |
| TextView         | tv    |
| CheckBox         | cb    |
| ScrollView       | sv    |
| ImageView        | iv    |
| RaduiButton      | rb    |
| RecyclerView     | rv    |
| LinearLayout     | ll    |
| ReletiveLayout   | rl    |
| ConstraintLayout | cl    |





## 2 Android基础组件

- Activity间通过`隐式Intent`的跳转，在发出Intent之前必须通过`resolveActivity`检查，避免找不到合适的调用组件，造成ActivityNotFoundException的异常。
 
        public void viewUrl(String url, String mimeType) {
                Intent intent = new Intent(Intent.ACTION_VIEW);
                intent.setDataAndType(Uri.parse(url), mimeType);
                if (getPackageManager().resolveActivity(intent, PackageManager.MATCH_DEFAULT_ONLY) != null) {
                        startActivity(intent);
                }else {
                        // 找不到指定的 Activity
                }
        }
- 禁止在`Service`中执行耗时操作，可改用`IntentService`或采用其他异步机制完成
- 禁止在`BroadcastReceiver#onReceive()`中执行耗时操作，也不可以开启子线程。可该用`IntentService`完成
- 添加`Fragment`时，确保`FragmentTransaction#commit()`在`Activity#onPostResume()`或者 `FragmentActivity#onResumeFragments()`内调用。不要随意使用`FragmentTransaction#commitAllowingStateLoss()`来代替，任何`commitAllowingStateLoss()`的使用必须经过`code review`，确保无负面影响
- 不要在`Activity#onDestroy()`内执行释放资源的工作，例如一些工作线程的销毁和停止，因为`onDestroy()`执行的时机可能较晚。可在`Activity#onPause()/onStop()`中结合`isFinishing()`的判断来执行。
- 对于只用于应用内的广播，优先使用`LocalBroadcastManager`来进行注册和发送，`LocalBroadcastManager`安全性更好，同时拥有更高的运行效率
- 当前` Activity`的`onPause`方法执行结束后才会`onCreate`或`onRestart`别的Activity，所以在`onPause`方法中不适合做耗时较长的工作，这会影响到页面之间的跳转效率
- 在`Activity`中显示对话框或弹出浮层时，尽量使用`DialogFragment`，而非`Dialog/AlertDialog`，便
于管理生命周期
- 启动页使用Activity`冷启动`
- 当项目体量较大，内存占用较多时，应为内存占用较大的Activity`设置新的进程`。如`WebView的Activity`或`图片浏览Activity`等
- 不要在Android的`Application`对象中缓存数据。基础组件之间的数据共享请使用`Intent 等机制`，也可使用 `SharedPreferences`等数据持久化机制。
- 禁止在`Activity`没有完全显示时显示`PopupWindow或Dialog`
- 在`Activity#onPause()或Activity#onStop()`回调中，关闭当前`activity正在执行的的动画`
## 3 布局

- 大多数情况布局嵌套不超过3层
- 墙裂推荐约束布局`ConstraintLayout`，可扁平化大多数复杂布局
- `文本大小`使用单位`dp`，`View`大小使用单位`dp`。对于`TextView`，如果在文字大小确定的情况下推荐使用 `wrap_content`布局避免出现文字显示不全的适配问题。
- 禁止在设计布局时多次为`子View`和`父View` 设置同样背景进而造成页面`过度绘制`
- 将不需要显示的布局进行及时隐藏
- 使用`merge、ViewStub`来优化布局
- 在需要时刻刷新某一区域的组件时，建议通过以下方式避免引发全局`layout`刷新:
    - 设置固定的`View`大小的宽高，如倒计时组件等
    - 调用`View的 layout`方法修改位置，如弹幕组件等
    - 通过修改`Canvas`位置并且调用`invalidate(int l, int t, int r, int b)`等方式限定刷新区域
    - 通过设置一个是否允许`requestLayout`的变量，然后重写控件的`requestlayout `、`onSizeChanged `方法，判断控件的大小没有改变的情况下，当进入`requestLayout`的时候，直接返回而不调用`super的requestLayout`方法。


- 尽量不要使用`AnimationDrawable`，它在初始化的时候就将所有图片加载到内存中
- 尽量不使用 ScrollView 包裹`ListView/GridView/ExpandableListVIew`。因为这样会把`ListView`的所有`Item`都加载到内存中，如数据量小可以使用`RecyclerView+NestedScrollView`

## 4 进程、线程通信
- 禁止使用`Intent`在`Android 基础组件`之间传递大数据（binder transaction缓存为 1MB），可能导致 OOM
- 当项目含有多进程时，`自定义Application`中`初始化SDK`的操作应区分主次进程
 
        public class MyApplication extends Application {
                @Override
                public void onCreate() {
                        //在所有进程中初始化
                        ....
                if (mainProcess) {
                        //仅在主进程中初始化
                        ...
                }
                if (bgProcess) {
                        //仅在子进程中初始化
                        ...
                }
            }
        }
        
- 新建线程时，必须通过线程池提供（`Rxjava自带`或者`ThreadPoolExecutor`等），不允许在应用中自行显式创建线程

## 5 文件、数据库

- 禁止使用文件的`绝对路径`，使用`Android文件系统API`访问

- 当使用`外部存储`时，必须检查外部存储的`可用性`
- 应用间共享文件时应使用`FileProvider`
- `多线程`操作写入数据库时，需要使用`事务`，以免出现同步问题
- `大量数据`写入时，使用事物操作
- 执行`SQL语句`时，应使用`SQLiteDatabase#insert()、update()、delete()`，不要使用 `SQLiteDatabase#execSQL()`，以免`SQL注入`风险

## 6 Bitmap、Drawable与动画
- `png图片`使用`TinyPNG`或者类似插件压缩处理，减少包体积

- 根据实际需要显示图片，如`ImageView`大小为100*100，则把`Bitmap降低采样率`后再显示
- 使用完毕的图片，必须应该及时`回收`
- 非特殊情况下，使用`RGB_565代替RGB_888`
- 当`View Animation`执行结束时，调用`View.clearAnimation()`释放相关资源。

##  6 安全

- 必须将`android:allowbackup`属性设置为`false`

- `必须使用 V2 签名`
- 不要把敏感信息打印到`log`中
- 本地加密秘钥不能硬编码在代码中，更不能使用`SharedPreferences`等本地持久化机制存储。应选择`Android自身的秘钥库（KeyStore）`机制或者其他安全性更高的安全解决方案保存
- 在`HTTPS通信`中，验证策略需要改成`严格模式`

## 7 内存泄露
- 凡是有`Activity Context`引用的对象不得为`static`静态的，如View及子类、Activity、Fragment等

- `Handler`使用时需要同步生命周期，可结合`弱引用`或者当其容器组件销毁时应移除Handler所有消息
- 凡是含有`注册(register)`功能的框架应及时`注销`，如`广播接收器`和`EventBus`等
- 子线程耗时任务应同步生命周期。如`网络请求在Activity中`时，当Activity`销毁`时应`取消`其网络请求
- 数据库`Cursor`必须确保使用完后关闭

## 8 其他建议
 
- 尽量避免使用`EventBus`等事件总线框架。`EventBus`会造成耦合度过低，代码阅读性极差

- 如开发耗时差别不大时，能用原生不用三方
- 提升JDK环境为1.8，并使用`lambda表达式`简化代码
- 如果项目体量较大建议使用`模块化`开发
- 网络请求建议`Retrofit2+Rxjava2`
- 使用`BRVAH`适配器
