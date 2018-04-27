

# 个人简历
## 个人信息
- 李季/男/1995.07.04
- 联系方式:17600669507 / glee9507@outlook.com
- Android 开发工程师/1年

- 本科/大连科技学院/物联网工程
- 学习能力强
***
## 技能清单
- 具备 Android 应用的**独立开发**能力
- 了解 Android 开发中的**性能优化**
    - 内存优化：内存泄露/内存溢出/图片优化
    - 绘制优化：熟练使用 **ConstraintLayout** 布局，**ViewStub** 控件和 **merge/include** 标签
- 熟悉**模块化/组件化开发**流程，使用 ARouter/CC等框架进行模块化开发
- 具备 **Kotlin** Android 应用开发能力
- 熟悉 Android 中 **MVP**/**MVVM** 开发架构
- 理解 **HTTP/HTTPS** 协议原理，了解 **MQTT 协议**，封装使用 Android 中常用网络请求框架，如 **OkHttp3/Retrofit2** ，自定义OkHttp**拦截器**
- 熟练使用 **Rxjava2** 及其常用操作符
- 对 Android FrameWork 层有一定了解
    - **Binder** 驱动原理
    - **ActivityManagerService/ActivityThread** 等常见系统服务的工作原理
- 熟悉 **JNI/NDK** 开发
- 对 **Linux** 操作系统较深理解，熟练使用 **Arch Linux** 等常见**发行版**作为工作环境
- 熟悉 **Git/SVN** 版本控制

***
## 沈阳×××医疗科技有限公司
- 辽宁沈阳
- Android 开发工程师
- 薪资：7000


### ×××患者端 / 医生端
一款综合医疗平台APP，主要涉及如下业务：
- 诊断：线上诊断/约诊、在线阅片、健康自查
- 医生查询：按科室、集团级别等查询医生并浏览医生详细信息
- 费用查询：根据病症和医院等级预估所需费用
- 医疗资讯：包括医疗健康方面的文章和各种疾病百科
- 病例管理：增删改除病例
- 提醒：用药提醒、复查提醒、回访记录等
- 地图：显示附近医院或药房，并显示详细信息

**主要技术**
- 使用CC框架模块化并行开发，模块解耦，模块可独立运行，代码责任制
- 高度封装BaseActivity/BaseFragment/BaseLazyFragment，实现多状态布局
- 参考RxLifeCycler实现Rxjava生命周期绑定
- Rxjava+Retrofit 封装，自定义OkHttp拦截器，实现sign自动生成
- 自定义Rxjava的Observer，实现网络请求状态码统一处理
- 部分模块使用Kotlin开发
- 自定义 View 实现刻度表盘和游标刻度尺


***




## 辽宁网药电子商务有限公司
- 辽宁沈阳
- Android 开发工程师 
- 薪资：5000

### 网药送
一款主营医药用品的电商APP，支持指定范围内的骑手配送和全国范围的快递配送。

**主要技术**
- Rxjava2+Retrofit2网络请求封装
- 使用RxLifeCycler解决内存泄露
- 通过系统日历实现精准用药提醒
- 实现无邀请码推广渠道追踪
- SDK集成，包括百度地图/网易云信/科大讯飞/友盟/个推/支付宝/微信/zxing等
- 商品详情中图片浏览器实现

### 网药送配送员
网药送的骑手端，类似美团外卖饿了么骑手端

**主要技术**
- Rxjava2+Retrofit2网络请求封装
- RxLifeCycler解决内存泄露
- 规范和文档的编写，根据最新需求不断修改和完善
- SDK集成，百度导航/骑行导航/定位/个推/zxing
- 子进程Service中使用SoundPool轮循语音提示/推送语音提示，通过AIDL通信
