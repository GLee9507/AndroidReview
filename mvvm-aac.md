# 基于 Android Architecture Components 的 Model-View-ViewModel 架构

本文阐述了通过 [Android Architecture Components](https://developer.android.google.cn/topic/libraries/architecture)（下文简称AAC） 实现的 MVVM 架构（下简称 MVVM-AAC）的设计思想、开发规范、和常用 Library 的使用方式


## 为什么要用 MVVM
传统 Android MVC 架构在迭代开发过程中会逐渐暴露出了许多缺陷，而基于 ACC 的 MVVM 架构可有效避免这些问题
|     | 问题                                           | 传统 MVC                                                                                                                                                                                                                   | MVVM-AAC                                                                                                                                                                  |
| --- | ---------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | Activity/Fragment 代码臃肿，高耦合度，难以维护 | 传统 Android MVC 架构把 Activity 或者 Fragment 作为 Controller + View 的集合体，所有数据逻辑、业务逻辑和 UI 逻辑的代码全部在 Activity 或者 Fragment 中，耦合度高。当业务复杂时单个文件代码行数无法控制，项目可维护性低下。 | MVVM 架构会把业务逻辑和数据逻辑抽取到ViewModel中，如ViewModel臃肿甚至可以把ViewModel中的逻辑再次抽取到Presenter中，松散的耦合。代码逻辑比较清晰，易于多人分工合作与维护。 |
| 2   | 内存泄漏风险                                   | 传统 MVC 发生内存泄漏的风险较大，通常为数据或业务逻辑的生命周期未与 Activity/Fragment 的生命周期同步                                                                                                                       | MVVM-AAC 架构中数据或业务逻辑在ViewModel中，而ViewModel 不持有 Activity/Fragment，故不会阻碍 Activity/Fragment 被 GC 回收                                                 |
| 3   | 团队协作性                                     | 传统 MVC 架构团队协作性较差，View 层与 Controller 层严重耦合，大多数情况下团队中一个开发者包揽模块的 UI 开发与业务开发                                                                                                     | MVVM-AAC 因为使用了 DataBinding Library ，并且 ViewModel 层和 View 层是松耦合的。所以完全可以多人分工来做，一部分人做 UI（XML+DataBinding）一部分人写 ViewModel，效率更高 |
| 4   | 可复用/迁移性                                  | 传统 MVC 代码耦合度高，业务逻辑与 UI 分离难度较高。如需fork新项目改动较多并且难度高                                                                                                                                        | MVVM 架构业务逻辑与 UI 是完全分离的，fork新项目如果大体逻辑不变，只需修改 UI（XML+DataBinding）和少许的 ViewModel 中逻辑                                                  |
| 5   | 可测性                                         | 传统 MVC 代码耦合性严重，单元测试难度极大                                                                                                                                                                                  | MVVM-ACC 架构中 ViewModel 负责业务逻辑与数据逻辑，ViewModel 中的 LiveData 为 View 层 UI 的映射。所以单元测试只需测试 ViewModel 中的逻辑，并且校验 LiveData 正确性即可     |
## 开始
在此之前，你应该了解或熟悉以下内容

-  [MVVM](https://zh.wikipedia.org/wiki/MVVM)
-  [Android Architecture Components](https://developer.android.google.cn/topic/libraries/architecture)
   -  [LiveData](https://developer.android.google.cn/topic/libraries/architecture/livedata) 
        
        熟悉使用 MediatorLiveData、Transformations#map 和 Transformations#switchMap，了解 LiveData 原理
   -  [ViewModel](https://developer.android.google.cn/topic/libraries/architecture/viewmodel)

        熟悉 ViewModel 构造方式，了解 ViewModel 生命周期以及如何避免 ViewModel 泄漏
   -  [Lifecycle](https://developer.android.google.cn/topic/libraries/architecture/lifecycle)

        熟悉 Activity 和 Fragment 生命周期
   -  [DataBinding](https://developer.android.google.cn/topic/libraries/data-binding)

        熟悉使用 DataBindingV2，使用 DataBinding 观察 LiveData，XML 中调用 ViewModel 的方法



## [Android Architecture Components](https://developer.android.google.cn/topic/libraries/architecture)
Android Architecture Components 是一组库，可以构建健壮性、可测试性、可维护性比较高的应用程序
###  [Lifecycle](https://developer.android.google.cn/topic/libraries/architecture/lifecycle)
轻松管理应用程序的生命周期。
新的生命周期感知组件可帮助您管理活动和碎片生命周期。
生存配置更改，避免内存泄漏并轻松将数据加载到UI中。
### [ViewModel](https://developer.android.google.cn/topic/libraries/architecture/viewmodel)
ViewModel存储在应用程序轮换中未销毁的UI相关数据。
### [LiveData](https://developer.android.google.cn/topic/libraries/architecture/livedata)
使用LiveData构建数据对象，以便在基础数据库更改时通知视图。
### [DataBinding](https://developer.android.google.cn/topic/libraries/data-binding)
数据绑定库是一个支持库，允许您使用声明性格式而不是以编程方式将布局中的UI组件绑定到应用程序中的数据源。

DataBinding 在 XML 中可以使用以下运算符和关键字
- 算数运算 + - / * %
- 字符串连接 +
- 逻辑运算 && ||
- 二进制运算 & | ^
- 一元运算 + - ! ~
- 移位运算 >> >>> <<
- 关系运算 == > < >= <= (Note that < needs to be escaped as &lt;)
- instanceof
- 括号 ()
- 字符 ''、字符串""、数字和 null
- 强转
- 方法调用
- 访问属性
- 访问数组 []
- 三元运算 ?:

## MVVM-AAC
MVVM-AAC 是通过 Android Architecture Components 中的 ViewModel、Lifecycle、LiveData和DataBinding 实现的 MVVM 架构，是 Google 推荐的开发架构

![](https://raw.githubusercontent.com/GLee9507/Note/master/mvvm-aac.jpg)

### View
在 Android MVVM 开发中普遍把 Activity 和 Fragment 定义为 View 层，View 层的主要职责如下
- 把数据映射到 UI 

    UI 的更新由 DataBinding Library 实现，在 XML 布局文件中 绑定 ViewModel 中的 LiveData，当 LiveData 数据发生变化后 UI 做出响应的更新

- 响应 UI 事件

    UI 事件的响应也由 DataBinding Library 实现，XML 布局文件中持有对应 ViewModel 的引用，所以当发生 UI 事件时直接在 XML 中调用 ViewModel 中的逻辑处理方法


### ViewModel
ViewModel 层主要负责程序的业务逻辑和数据逻辑处理，处理后的数据提供给其内部持有的 LiveData，此时绑定此 LiveData 的控件会自动更新。故 ViewModel 不会做任何 UI 相关的事

### Model
Model 层最大的特点是被赋予了数据获取和数据修改的职责，与我们平常 Model 层只定义实体对象的行为截然不同，数据的获取、存储、数据状态变化都是 Model 层的任务。Model 包括数据模型（Bean）、网络数据接口，本地数据库接口，数据变化监听等。Model 提供数据获取接口供 ViewModel 调用，经数据转换和操作并最终映射绑定到 View 层某个 UI 元素的属性上。


```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        ActivityMainBinding viewDataBinding = DataBindingUtil.setContentView(this, R.layout.activity_main);
        //构造 MainViewModel
        MainViewModel mainViewModel = ViewModelProviders.of(this).get(MainViewModel.class);
        //设置 ActivityMainBinding 中的 viewModel 属性，对应 XML 中的 viewModel
        viewDataBinding.setViewModel(mainViewModel);
        //赋予 ActivityMainBinding 生命周期所有者 
        viewDataBinding.setLifecycleOwner(this);
    }
}
```

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout>

    <data>

        <variable
            name="viewModel"
            type="com.glee.mvvmaac.MainViewModel" />
    </data>

    <FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context=".MainActivity">
        
        <!-- 当 MainViewModel 中的 LiveData 数据发生变化时，TextView 的 text 属性随着变化 -->
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text='@{viewModel.liveData.userName ?? "用户名为空"}' />

        <!-- 当 Button 被点击时调用 MainViewModel 中的 getUserInfo()方法 -->
        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="@{(view)-> viewModel.getUserInfo()}" />

    </FrameLayout>
</layout>
````

```java
public class MainViewModel extends ViewModel {

    //View 层 XML 中的 TextView 通过 DataBinding 绑定此 LiveData
    private MutableLiveData<UserInfo> liveData = new MutableLiveData<>();

    //LocalDBModel 即为 Model 层，负责数据提供
    private LocalDBModel localDBModel = LocalDBModel.getInstance();

    public LiveData<UserInfo> getLiveData() {
        return liveData;
    }

    /**
     * getUserInfo 方法即为业务逻辑处理方法
     * 通过本地数据库获取 UserInfo 并且更新LiveData，使 UI 更新
     */
    public void getUserInfo() {
        localDBModel.getUserInfo(userInfo -> {
            //因为数据回调在子线程所以调用 postValue 更新 LiveData 
            liveData.postValue(userInfo);
        });
    }
}
```

<!-- ## Android Architecture Components 规范

### ViewModel
为了更好的统一管理 ViewModel  -->

## Presenter
如果某个页面业务逻辑过于庞杂，专注于处理业务逻辑的 ViewModel 也会显得力不从心。此时可以把业务逻辑提取分割为一个或多个 Presenter，由 Presenter 来处理

Presenter 的命名需与 ViewModel 对应，如定义一个为 PlayerViewModel 处理 Player 页面初始化业务逻辑的 Presenter

`PlayerViewModel`

`PlayerInitPresenter`

<!-- ### DataBinding Library
DataBinding 可使 UI 组件的绑定形式从编码的形式改变为 XML 声明的形式。DataBinding 在 XML 中可以使用以下运算符和关键字
- 算数运算 + - / * %
- 字符串连接 +
- 逻辑运算 && ||
- 二进制运算 & | ^
- 一元运算 + - ! ~
- 移位运算 >> >>> <<
- 关系运算 == > < >= <= (Note that < needs to be escaped as &lt;)
- instanceof
- 括号 ()
- 字符 ''、字符串""、数字和 null
- 强转
- 方法调用
- 访问属性  
- 访问数组 []
- 三元运算 ?:

虽然


--- lib -->
## RecyclerView
为方便在 MVVM 中使用 RecyclerView，封装了 AutoRecyclerView，具有以下功能
- 符合 MVVM 思想
- 高性能
- 简易

定义 item 的XML
```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:app="http://schemas.android.com/apk/res-auto">

    <data>

        <variable
            name="userInfo"
            type="com.tencent.wecarmusic.sdk.music.adapter.qqmusic.bean.UserInfo" />
    </data>

    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{userInfo.nickname}" />

        <ImageView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            app:imageUri="@{userInfo.headimgurl}" />
<android.support.v7.widget.RecyclerView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:bind="@{vm.autoBinding}"
            app:layoutManager="android.support.v7.widget.LinearLayoutManager" />
    </LinearLayout>
</layout>
```
定义 RecyclerView XML
```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:app="http://schemas.android.com/apk/res-auto">

    <data>

        <variable
            name="vm"
            type="com.mx.mvvm.TestViewModel" />
    </data>

    <FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <android.support.v7.widget.RecyclerView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:bind="@{vm.autoBinding}"
            android:orientation="horizontal"
            app:layoutManager="android.support.v7.widget.LinearLayoutManager" />
    </LinearLayout>
</layout>
```
定义 ViewModel 中的 AutoBinding
```java
public class PlayerViewModel extends ViewModel {

    /**
    * Build 构造方法
    * @param layoutId item 布局的资源文件
    * @param brId DataBinding XML 中 variable 标签的 name 属性
    */
    AutoBinding<UserInfo> autoBinding = new AutoBinding.Builder<UserInfo>(R.layout.item_test_databinding, BR.userInfo)
            //设置 item 的点击事件
            .setOnItemClickListener((position, userInfo) -> {
                //do something
            })
        
            /**
            * 添加 Header
            * @param key Header 的唯一标识
            * @param layoutId Header 的布局资源
            * @param brId DataBinding XML 中 variable 标签的 name 属性 
            * @param liveData Header 映射的 LiveData，用于数据更新
            * @param onClickListener Header 的点击事件
            */
            .addHeader("header", R.layout.header_test, BR.data, headerLiveData,
                    () -> {
                        //do something
                    })
            .addFooter("footer", R.layout.footer_test, BR.data, footerLiveData,
                    () -> {
                        //do something
                    }
            );
}
```
更新 RecyclerView 列表数据

```java
//更新指定索引的item
autoBinding.getData().update(4, var1 -> {
    var1.setNickname("测试");
    return var1;
});
//追加item
autoBinding.getData().addAll()
//删除指定索引的item
autoBinding.getData().remove()
//添加item        
autoBinding.getData().add()
//重新设置整个列表数据        
autoBinding.getData().setData();
```
## StateLayout

## SingleLiveEvent
SingleLiveEvent 基于 MutableLiveData，但和 MutableLiveData 的区别是只会响应一次 LiveData 事件。因此可以应用于页面的路由跳转、Toast、Dialog、PopupWindow 控制。

```java
public class SingleLiveEvent<T> extends MutableLiveData<T> {

    private static final String TAG = "SingleLiveEvent";

    private final AtomicBoolean mPending = new AtomicBoolean(false);

    @Override
    @MainThread
    public void observe(LifecycleOwner owner, final Observer<T> observer) {

        if (hasActiveObservers()) {
            Log.w(TAG, "Multiple observers registered but only one will be notified of changes.");
        }

        // Observe the internal MutableLiveData
        super.observe(owner, t -> {
            if (mPending.compareAndSet(true, false)) {
                observer.onChanged(t);
            }
        });
    }

    @Override
    @MainThread
    public void setValue(@Nullable T t) {
        mPending.set(true);
        super.setValue(t);
    }

    /**
     * Used for cases where T is Void, to make calls cleaner.
     */
    @MainThread
    public void call() {
        setValue(null);
    }
}
```

## LazyLoad
懒加载（按需加载）合理的使用会一定程度上提高提供程序的性能，为了代码整洁性封装了 LazyLoad 工具。需要注意的是使用 LazyLoad 工具会创建两个多余的对象，所以建议使用在耗时对象的构建，比如 View 的构建、Android Resource 的加载等
```java
public class LazyLoad<T> {
    /**
     * 期望构造的对象
     */
    private T t;
    /**
     * 具有线程安全的，期望构造的对象
     */
    private volatile T threadSafeT;
    /**
     * 传入的构造函数
     */
    private InitFunc<T> initFun;
    /**
     * 是否线程安全
     */
    private final boolean threadSafe;

    /**
     * @param initFun 构造所需对象的函数
     */
    public LazyLoad(InitFunc<T> initFun) {
        this(false, initFun);
    }

    /**
     * @param threadSafe 线程安全的构建
     * @param initFun    构造所需对象的函数
     */
    public LazyLoad(boolean threadSafe, InitFunc<T> initFun) {
        this.initFun = initFun;
        this.threadSafe = threadSafe;
    }

    /**
     * 获取对象
     *
     * @return t
     */
    public T get() {
        //如果是线程安全的构建，则会经过 double check
        if (threadSafe) {
            if (threadSafeT == null) {
                synchronized (this) {
                    if (threadSafeT == null) {
                        threadSafeT = initFun.init();
                        initFun = null;
                    }
                }
            }
            return threadSafeT;
        }

        if (t == null) {
            t = initFun.init();
            initFun = null;
        }
        return t;
    }

    public interface InitFunc<T> {
        T init();
    }
}
```

```java
public class PlayerViewModel extends AndroidViewModel {
    private final LazyLoad<String[]> testArray 
    = new LazyLoad<>(() -> getApplication().getResources().getStringArray(R.array.test));

    public void start(){
        //首次调用get时构造
        String[] array = testArray.get();
    }
}
```
## 单元测试与 LiveData 测试

## 开发要点

1. ViewModel 不可持有任何关于 android.view.View、Lifecycle、Activity 的 Context 的引用
2. View 层通常不可修改 ViewModel 中的数据
3. 使用 SingleLiveEvent 控制页面的跳转和 Toast、Dialog、PopupWindow 的弹出
4. 合理使用 LazyLoad 懒加载初始化变量，但不要过度使用
5. View 层原则上不可以使用 LiveData#ObserveForever
6. 当 ViewModel 所承载的业务逻辑过多时，考虑分割业务逻辑给 Presenter
7. 尽可能的精简 XML 中的 DataBinding 代码
8.  
