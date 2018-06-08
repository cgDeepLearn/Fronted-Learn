# 路由

## 为什么需要路由

在应用程序中定义路由非常有用，因为我们可以：

- 将应用程序划分为多个分区；
- 维护应用程序的状态；
- 基于某些规则保护应用分区

如果为所有页面使用同样的URL，会有什么后果呢？

- 刷新页面后，无法保留你在应用中的位置。
- 不能为页面添加书签，方便以后返回相同的页面。
- 无法与他人分享当前页面的URL。

反过来看，使用路由能让我们定义URL字符串，指定用户在应用中的位置

## 客户端路由的工作原理

服务器端路由: 它接收一个请求，并路由到一个控制器。该控制器根据路径和参数执行特定的任务。

客户端路由在概念上很相似，但是实施方法不同。在客户端路由的情况下，每次URL发生变化时，不一定会向服务器发送请求。我们把Angular应用叫作单页应用程序（single page app， SPA），因为服务器只提供一个页面，负责渲染各种页面的是JavaScript。

### 初级阶段: 使用锚标记

可能你已经知道，锚标记的传统作用是直接链接到所在网页的其他位置，并让浏览器滚动到定义该锚标记元素所在的位置。
当访问`http://something/#about`这个URL时，浏览器将直接跳到这个定义about锚标记的H1标签。
SPA应用客户端框架使用的方式是：将锚标记作为路径来格式化，用它们代表应用程序的路由。

例如， SPA应用的about路由可能是`http://something/#/about`。这就是所谓的基于锚点标记的路由（hash-based routing）。这个方法巧妙的地方在于，它看起来像一个“普通”的URL，因为它以锚标记和斜杠开头（/about）

### 进化: HTML5客户端路由

随着HTML5的引入，浏览器获得了新的能力：在不需要新请求的情况下，允许在代码中创建新的浏览器记录项并显示适当的URL。
这是利用`history.pushState`方法来实现的，该方法允许JavaScript控制浏览器的导航历史。因此，现代框架可以不依赖锚记方法来进行路由导航，而是依赖pushState在无需重新加载的情况下控制浏览器历史。

在Angular中， HTML5路由是默认的模式。

## 编写第一个路由配置

在Angular中，我们通过将路径映射到处理它们的组件来配置路由。

我们来创建一个有多种路由的小型应用程序。在这个例子应用程序中，我们将有三种路由：

- 主页，使用/#/home路径；
- 关于页面，使用/#/about路径；
- 联系我们页面，使用/#/contact路径；

最后，当用户访问根路径（/#/）时，重定向到主页路径。

## Angular 路由的组成部分

我们使用三种主要部件来配置Angular路由。

- `Routes`：描述了应用程序支持的路由配置。
- `RouterOutlet`：这是一个“占位符”组件，用于告诉Angular要把每个路由的内容放在哪里。
- `RouterLink`指令：用于创建各种路由链接。

### 导入

为了使用Angular的路由器，首先从@angular/router库中导入一些常量。

```ts code/routes/basic/app/ts/app.ts
import {
  RouterModule,
  Routes
} from '@angular/router';
```

### 路由配置

为了定义应用的路由配置，首先创建一个Routes配置，然后使用RouterModule.forRoot(routes)来为应用程序提供使用路由器必需的依赖:

```ts code/routes/basic/app/ts/app.ts
const routes: Routes = [
  { path: '', redirectTo: 'home', pathMatch: 'full' },
  { path: 'home', component: HomeComponent },
  { path: 'about', component: AboutComponent },
  { path: 'contact', component: ContactComponent },
  { path: 'contactus', redirectTo: 'contact' },
];
```

注意关于路由配置的以下事项。

- path：指定了该路由要处理的URL路径。
- component：用于连接当前路由路径与处理该路由的组件。
- redirectTo：一个可选选项，用于将当前路径重定向到另一个已知路由。

### 安装路由配置

现在有了路由配置routes，我们需要安装它。为了在应用中使用路由配置，首先要对NgModule进行两项修改：

- (1) 导入RouterModule；
- (2) 在NgModule中的imports数组里使用RouterModule.forRoot(routes)来安装路由配置。

```ts
class RoutesDemoApp {
}

const routes: Routes = [
  { path: '', redirectTo: 'home', pathMatch: 'full' },
  { path: 'home', component: HomeComponent },
  { path: 'about', component: AboutComponent },
  { path: 'contact', component: ContactComponent },
  { path: 'contactus', redirectTo: 'contact' },
];

@NgModule({
  declarations: [
    RoutesDemoApp,
    HomeComponent,
    AboutComponent,
    ContactComponent
  ],
  imports: [
    BrowserModule,
    RouterModule.forRoot(routes) // <-- routes
  ],
  bootstrap: [ RoutesDemoApp ],
  providers: [
    { provide: LocationStrategy, useClass: HashLocationStrategy }
  ]
})
class RoutesDemoAppModule {}

platformBrowserDynamic().bootstrapModule(RoutesDemoAppModule)
  .catch((err: any) => console.error(err));
```

### 使用`<router-outlet>`调用RouterOutlet指令

当路由发生变化时，我们希望保留外部“布局”模板，只用路由的组件替换页面的“内部”。

为了指定Angular在页面的什么地方渲染各种路由的内容，我们使用RouterOutlet指令。

组件的模板中指定了一些div结构、导航部分和一个名为router-outlet的指令。我们可以在模板中使用router-outlet指令，因为已经在NgModule中导入了RouterModule。

下面是应用中用于承载导航的组件及其模板:

```ts
@Component({
  selector: 'router-app',
  template: `
  <div>
    <nav>
      <a>Navigation:</a>
      <ul>
        <li><a [routerLink]="['home']">Home</a></li>
        <li><a [routerLink]="['about']">About</a></li>
        <li><a [routerLink]="['contact']">Contact Us</a></li>
      </ul>
    </nav>

    <router-outlet></router-outlet>
  </div>
  `
})
class RoutesDemoApp {
}
```

仔细查看上面模板的内容，你将发现router-outlet元素在导航目录的正下方。 当访问/home时，这里便是HomeComponent模板被渲染的地方。其他组件的渲染位置也是一样的。

### 使用`[routerLink]`调用routeLink指令

我们可以尝试使用纯HTML，像这样直接链接到路由：`<a href="/#/home">Home</a>`.
但是如果这样做，点击这个链接将触发页面重载，而这是开发单页应用时要杜绝的。
要解决这个问题， Angular提供了一个方案，可以在不重载页面的情况下链接路由：使用routerLink指令

```html
<a>Navigation:</a>
<ul>
  <li><a [routerLink]="['home']">Home</a></li>
  <li><a [routerLink]="['about']">About</a></li>
  <li><a [routerLink]="['contact']">Contact Us</a></li>
</ul>
```

## 整合

```html
<!doctype html>
<html>
  <head>
    <base href="/">
    <title>ng-book 2: Angular 2 Router</title>

    {% for (var css in o.htmlWebpackPlugin.files.css) { %}
      <link href="{%=o.htmlWebpackPlugin.files.css[css] %}" rel="stylesheet">
    {% } %}
  </head>
  <body>
    <router-app></router-app>
    <script src="/core.js"></script>
    <script src="/vendor.js"></script>
    <script src="/bundle.js"></script>
  </body>
</html>
```

> 描述htmlWebpackPlugin的部分来自于webpack模块捆绑器。我们在本章中使用了webpack，它是一个帮你捆绑资源的工具.

`<base href="/">`这行声明了HTML标签base。传统上，该标签的作用是使用相对路径来告知浏览器去哪里查找图片和其他资源。

Angular的路由器也依赖这个标签来确定如何构建它的路由信息。例如，如果一个路由的路径为`/hello`， base元素声明是`href="/app"`，那么应用程序将使用`/app/#/hello`作为实际路径。

有时候， Angular应用开发者对应用中HTML的head部分没有访问权。比如在重用已有大型应用的页头和页脚时。
幸运的是，我们有方法处理这种情况。你可以在配置NgModule时，像这样使用`APP_BASE_HREF`提供者，用代码来声明应用程序的基准路径：

```ts
@NgModule({
  declarations: [ RoutesDemoApp ],
  imports: [
    BrowserModule,
    RouterModule.forRoot(routes) // <-- routes
  ],
  bootstrap: [ RoutesDemoApp ],
  providers: [
    { provide: LocationStrategy, useClass: HashLocationStrategy },
    { provide: APP_BASE_HREF, useValue: '/' } // <--- this right here
 ]
})
```

**将`{ provide: APP_BASE_HREF, useValue: '/' }`放到providers中，等同于在应用的HTML页头里使用`<base href="/">`。**

### 创建组件

#### HomeCompoennt

```ts
import {Component} from '@angular/core';

@Component({
  selector: 'home',
  template: `<h1>Welcome!</h1>`
})
export class HomeComponent {
}
```

#### AboutComponent

```ts
import {Component} from '@angular/core';

@Component({
  selector: 'about',
  template: `<h1>About</h1>`
})
export class AboutComponent {
}
```

#### ContactComponent

```ts
import {Component} from '@angular/core';

@Component({
  selector: 'contact',
  template: `<h1>Contact Us</h1>`
})
export class ContactComponent {
}
```

### 应用程序组件

我们先从core和router库导入需要的模块。

```ts
/*
 * Angular Imports
 */
import {
  NgModule,
  Component
} from '@angular/core';
import {BrowserModule} from '@angular/platform-browser';
import {platformBrowserDynamic} from '@angular/platform-browser-dynamic';
import {
  RouterModule,
  Routes
} from '@angular/router';
import {LocationStrategy, HashLocationStrategy} from '@angular/common';
```

然后导入上面创建的三个组件：

```ts
import {HomeComponent} from 'components/HomeComponent';
import {AboutComponent} from 'components/AboutComponent';
import {ContactComponent} from 'components/ContactComponent';
```

声明选择器和模板:

```ts
@Component({
  selector: 'router-app',
  template: `
  <div>
    <nav>
      <a>Navigation:</a>
      <ul>
        <li><a [routerLink]="['home']">Home</a></li>
        <li><a [routerLink]="['about']">About</a></li>
        <li><a [routerLink]="['contact']">Contact Us</a></li>
      </ul>
    </nav>

    <router-outlet></router-outlet>
  </div>
  `
})
class RoutesDemoApp {
}
```

我们将为这个组件使用两个路由指令： RouterOutlet和RouterLink。这两个指令和其他公共路由指令一起，在我们将RouterModule放置到NgModule的imports数组中时被导入进来。

### 配置路由

接下来，我们创建一组类型为Routes的对象数组，并用它来声明路由配置。

```ts
const routes: Routes = [
  { path: '', redirectTo: 'home', pathMatch: 'full' },
  { path: 'home', component: HomeComponent },
  { path: 'about', component: AboutComponent },
  { path: 'contact', component: ContactComponent },
  { path: 'contactus', redirectTo: 'contact' },
];
```

在app.ts文件的最后，我们这样引导应用。

```ts
@NgModule({
  declarations: [
    RoutesDemoApp,
    HomeComponent,
    AboutComponent,
    ContactComponent
  ],
  imports: [
    BrowserModule,
    RouterModule.forRoot(routes) // <-- routes
  ],
  bootstrap: [ RoutesDemoApp ],
  providers: [
    { provide: LocationStrategy, useClass: HashLocationStrategy }
  ]
})
class RoutesDemoAppModule {}

platformBrowserDynamic().bootstrapModule(RoutesDemoAppModule)
  .catch((err: any) => console.error(err));
```

我们引导应用并指定RoutesDemoApp为根组件。
注意，我们将所有必需的组件放到declarations里。如果要路由到一个组件，那么必须在某个NgModule（当前模块或者导入的模块）里面声明它。

## 路由策略

定位策略（location strategy）是Angular应用从路由定义进行解析和创建路径的方式。

Angular的默认策略为PathLocationStrategy，也就是HTML5路由。在使用这个策略时，路由的路径是常规路径，例如/home或者/contact。

通过将LocationStrategy类绑定到新的策略类实例，我们可以改变应用的定位策略。我们可以不使用默认的PathLocationStrategy，而是使用HashLocationStrategy

我们使用锚点标记策略作为默认策略，因为如果使用HTML5路由，那么URL将成为普通的路径（而非使用锚点标记或者锚标签）

该默认策略适用于基于锚点标记的路径，例如/#/home或者/#/contact。

- 如何在产品中使用HTML5模式呢？

要使用HTML5模式路由，你必须配置服务器来将所有“不存在”的路由重定向到根URL。

在routes/basic项目中，我们包含了一个脚本，可在webpack-dev-server环境下开发，并使用HTML5路径。
要使用它，需要cd routes/basic并运行node html5-dev-server.js。

最后，为了让示例应用适合这个新的策略，必须首先导入LocationStrategy和HashLocationStrategy。

```ts code/routes/basic/app/ts/app.ts
import {LocationStrategy, HashLocationStrategy} from '@angular/common';
```

然后将定位策略添加到NgModule的providers。

```ts
providers: [
  { provide: LocationStrategy, useClass: HashLocationStrategy }
]
```

## 路径定位策略

在示例应用的目录中，有一个名为app/ts/app.html5.ts的文件。
如果你想试试默认的PathLocationStrategy，那么将这个文件的内容复制到app/ts/app.ts中，然后重新加载应用即可。

## 运行应用程序

routes/basic下运行`npm run go`

## 路由参数

为了添加参数到路由配置，我们这样指定路由路径。

```ts
const routes: Routes = [
  { path: '', redirectTo: 'search', pathMatch: 'full' },
  { path: 'search', component: SearchComponent },
  { path: 'artists/:id', component: ArtistComponent },
  { path: 'tracks/:id', component: TrackComponent },
  { path: 'albums/:id', component: AlbumComponent },
];
```

当我们访问路由/artist/123时， 123部分是被传到路由的id路由参数。

### ActivatedRoute

为了使用路由参数，我们首先需要导入ActivatedRoute：

```ts
import { ActivatedRoute } from '@angular/router';
```

接下来，将ActivatedRoute注入组件的构造函数中

```ts
export class ArticleComponent {
  id: string;
  constructor(private route: ActivatedRoute) {
    route.params.subscribe(params => { this.id = params['id']; });
  }
}
```

注意， `route.params`是一个可观察对象。我们可以使用`.subscribe`将参数值提取到固定值。
在这种情况下，我们将params['id']赋值给组件实例的变量id。

## 音乐搜索应用

我们将构建一个音乐搜索应用（如图7-4所示），它具有以下特性：

- (1) 按照提供的关键词搜索曲目；
- (2) 在数据表格中显示匹配曲目；
- (3) 点击歌手名字时，显示歌手介绍；
- (4) 点击专辑名字时，显示专辑信息和曲目列表；
- (5) 点击歌曲名字时，显示曲目信息并允许用户试听