# Ionic导航和核心组件

- 管理用户状态并实现应用内导航
- 用图标、列表和卡片来组织内容
- 从外部加载数据并显示载入效果
- 使用无限滚动来不断加载数据
- 使用幻灯片展示纽件来显示应用介绍

## 配置项目

### 创建一个新应用并手动添加代码

```
$ ionic start chapter4 https://github.com/ionic-in-action/starter
$ cd chapter4
$ ionic serve
```

### 或克隆完整版应用

```
$ git clone https://github.com/ionic-in-action/chapter4.git
$ cd chapter4
$ git checkout -f stepl
$ ionic serve
```

## 配置应用导航

`导航`和`路由`是两个有关联但是不相同的概念 。 术语导航指的是用户在应用内部移动 的动作（用户单击一个按钮，然后跳到另一个界面)，术语路由是应用内部的－个过程 ， 用于控制用户导航时具体的行为 （当按钮被按下时应用决定要切换到哪里）。换句话说，**导航是用户的行为，路由是应用响应用 输入的逻辑** 。

- 为什么Ionic 使用 ui-router 而不是 ngRoute？

Angular 有一个官方路由（`ngRoute`），但是 Ionic 没有使用 。 主要的原因是有些 `ui-router` 的重要功能并没有被 ngRoute 支持，比如命名视图、嵌套视图和平行视图 。 一个例子就是在选项卡界面中嵌套多个视图 。 Ionic 内直了这些特性，对应的指令是 ionNavView 。 Ionic 只支持 ui-route ，所以如果要使用 ngRoute 会出问题。

### 设计良好的应用导航

浏览器可以用地址栏、刷新和返回按钮来导航 。 应用没有按钮，所以开发者必须实现导航选项。

### 使用状态管理器来声明应用视图

- ionNavView和ionNavBar

`ionNavView` 和 `ionNavBar` 是 Ionic 的基础组件，用于导航。 ionNavView 就像一个占位符，用于把不同的视图内容载入应用 ， ionNavBar 是标题栏 ， 在用户跳转视图时自动更新。这两个组件是一同工作 的，不过如果你不想要顶部 的导航栏，也可以单独使用 ionNavView 。

![nav](pics/ionic-4.1.png)

- 声明应用状态

![state](pics/ionic-4.2.png)

- 主视图模板

![home](pics/ionic-4.3.png)

## 构建主视图

在清单 4.3 中有一个非常基础的空白视图，需要向其中添加内容。可加入`ionContent` ，这是 一个通用的内容封装器，有许多暂时还用不到的特性。接着会创建一个列表，包含每个视图的导航链接。最后会添加一些图标，让链接看起来更漂亮。

![主视图](pics/ionic-4.4.png)

### 创建内容容器

ionContent特性：

- 根据设备调整内容区域尺寸 一一它会根据设备来设置内容容器的高度 。
- 和头部底部协同合作一一它知道是否有头部和底部 ， 可以据此调节尺寸和位置。
- 管理滚动 一一有许多配置项来管理滚动 。举个例子，你可能希望只允许一个方向的滚动（水平），或者彻底禁止滚动。

![ionContent](pics/ionic-4.5.png)

### 使用 css 组件并添加一个简单的链接列表

![list](pics/ionic-4.6.png)

### 给列表元素添加图标

![icon](pics/ionic-4.7.png)

## 使用控制器和模型来开发预订视图

- 预定视图的控制器

![reservation](pics/ionic-4.8.png)

- 预定视图模板

![view](pics/ionic-4.9.png)

- 声明预定状态

![state](pics/ionic-4.10.png)

## 把数据加载到天气视图中

你首先需要创建一个模板文件，然后添加控制器和数据加载代码，最后实现加载组件。

![weather](pics/ionic-4.11.png)

### 给天气视图添加模板

![weather-template](pics/ionic-4.12.png)

### 创建天气控制器加载外部数据

需要使用 Angular 提供的 `$http`服务来从一个 URL 加载数据。把 $http 服务注入控制器，访问一个 URL，然后处理 HTTP请求 的成功或者失败情况 。 创建一个新控制器文件 www/views/weather/weather.js

![view](pics/ionic-4.13.png)

- 声明状态

```js
    .state('weather', {
      url: '/weather',
      controller: 'WeatherController',
      templateUrl: 'views/weather/weather.html'
    });
```

### 给天气视图添加一个载入指示器

加载组件有两个方法： show()和 hide()。你需要告诉加载组件什么时候使用hide()，因为它不会自动在加载完成时隐藏.

![load](pics/ionic-4.14.png)

## 在餐馆视图中使用卡片和无限滚动

- 视图模板

给列表中的元素添加 card 类，你就可以使用卡片效果。每个卡片都是单独的一个列表，使用 ngRepeat 来为每个餐馆创建一个新卡片。这里的重点是 `ioninfiniteScroll` 组件。

![roll](pics/ionic-4.15.png)

- 控制器

![controller](pics/ionic-4.16.png)

- 添加状态

```js
    .state('restaurants', {
      url: '/restaurants',
      controller: 'RestaurantsController',
      templateUrl: 'views/restaurants/restaurants.html'
    });
```

## 使用幻灯片组件来实现应用介绍

希望用户在第一次使用应用的时候能看到一个快速入门 。有许多种实现方法，不过这里我们会使用`ionSlideBox` 组件来显示一个简单的幻灯片.`$ionSlideBoxDelegate` 服务可以用来在程序中控制幻灯片.

![tourBox](pics/ionic-4.18.png)

- 引导视图模板

![tour](pics/ionic-4.17.png)

- tour.css

![css](pics/ionic-4.19.png)

- 添加新状态

```js
    .state('tour', {
      url: '/tour',
      templateUrl: 'views/tour/tour.html'
    });

$urlRouterProvider.otherwise('/tour'); // 默认路由是tour
```

## 挑战

- 添加新状态
- 改进天气视图
- 实现风向过滤器
- 缓存天气数据
- 创建天气服务

## 总结

- Ionic 应用基于状态构建 。状态是使用`$stateProvider` 的`config()`方法声明的。
- Ionic 会在状态发生改变时把你的模板载入 `ionNavView` 组件。
- `ionNavBar` 选项卡会基于当前视图自动更新导航栏的标题 。
- 列表和卡片组件可以用移动端友好的形式展示一组内容。
- 可以使用 `$http` 服务把数据加载到控制器中，并使用`$ionicLoading` 服务在加载时显示加载指示器。
- `ionSlideBox` 是一个非常强大的移动端幻灯片组件，你可以用它来实现应用使用介绍。