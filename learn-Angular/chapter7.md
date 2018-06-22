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

### 导入需要的类

```ts
/*
 * Angular Imports
 */
import {
  Component
} from '@angular/core';
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
import { HttpModule } from '@angular/http';
import { FormsModule } from '@angular/forms';
import {
  RouterModule,
  Routes
} from '@angular/router';
import {
  LocationStrategy,
  HashLocationStrategy,
  APP_BASE_HREF
} from '@angular/common';

/*
 * Components
 */
```

现在我们有了所有导入声明，接下来考虑每个路由的组件。

- Search路由：新建SearchComponent。该组件将连接Spotify API并执行搜索功能，然后在数据表格中显示搜索结果。
- Artists路由：新建ArtistComponent，显示艺术家信息。
- Albums路由：新建AlbumComponent，显示专辑的曲目列表。
- Tracks路由：新建TrackComponent，显示曲目并允许试听。

因为新组件需要与Spotify API交互，所以我们需要创建一个服务，它使用http模块来调用API服务器。
应用的一切都依赖这些数据，所以我们首先创建SpotifyService。

### SpotifyService

SearchByTrack：

```sql
class SpotifyService {
  constructor(public http: Http) {
}
  searchByTrack(query: string) {
    let params: string = [
      `q=${query}`,
      `type=track`
    ].join("&");
    let queryURL: string = `https://api.spotify.com/v1/search?${params}`;
    return this.http.request(queryURL).map(res => res.json());
  }
}
```

该http调用返回一个Observable。我们将进一步使用RxJS函数map转换搜索结果（一个http模块的Response对象）并将它解析为JSON，最终获得一个对象。

### SearchComponent

导入声明

```ts
import {Component, OnInit} from '@angular/core';
import {
  Router,
  ActivatedRoute,
} from '@angular/router';

/*
 * Services
 */
import {SpotifyService} from 'services/SpotifyService';
```

现在开始开发组件。我们用search作为选择器，并使用下面的模板。

```ts
@Component({
  selector: 'search',
  template: `
  <h1>Search</h1>

  <p>
    <input type="text" #newquery
      [value]="query"
      (keydown.enter)="submit(newquery.value)">
    <button (click)="submit(newquery.value)">Search</button>
  </p>

  <div *ngIf="results">
    <div *ngIf="!results.length">
      No tracks were found with the term '{{ query }}'
    </div>

    <div *ngIf="results.length">
      <h1>Results</h1>

      <div class="row">
        <div class="col-sm-6 col-md-4" *ngFor="let t of results">
          <div class="thumbnail">
            <div class="content">
              <img src="{{ t.album.images[0].url }}" class="img-responsive">
              <div class="caption">
                <h3>
                  <a [routerLink]="['/artists', t.artists[0].id]">
                    {{ t.artists[0].name }}
                  </a>
                </h3>
                <br>
                <p>
                  <a [routerLink]="['/tracks', t.id]">
                    {{ t.name }}
                  </a>
                </p>
              </div>
              <div class="attribution">
                <h4>
                  <a [routerLink]="['/albums', t.album.id]">
                    {{ t.album.name }}
                  </a>
                </h4>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
  `
})
```

1. 搜索框

```html
<p>
    <input type="text" #newquery
      [value]="query"
      (keydown.enter)="submit(newquery.value)">
    <button (click)="submit(newquery.value)">Search</button>
  </p>
```

这里，我们插入了输入框，并将其DOM元素的value属性绑定到组件的query属性。
我们还给这个元素赋予了一个模板变量，名为#newquery。这样我们就可以在模板中通过newquery.value来直接访问该输入框的值。
按钮将触发组件的submit方法，将输入框的值当作参数传入.我们还希望在用户按下回车键以后触发submit事件，所以将keydown.enter事件绑定到输入框.

2. 搜索结果和链接

我们依靠ngFor指令来迭代返回对象中的每条曲目：

```html
<div class="row">
        <div class="col-sm-6 col-md-4" *ngFor="let t of results">
          <div class="thumbnail">
```

我们为每条曲目显示其艺术家的名字:

```html
<h3>
  <a [routerLink]="['/artists', t.artists[0].id]">
    {{ t.artists[0].name }}
  </a>
</h3>
```

注意我们是如何使用RouterLink指令来重定向到['/artists', t.artists[0].id]的。
这是为特定路由设置路由参数的方法。假设有一个id为abc123的艺术家，当这个链接被点击时，本应用将导航到/artist/abc123（abc123是:id参数）。

下面将展示如何在该路由对应的组件中获取这个参数。
现在，我们这样显示曲目：

```html
<p>
  <a [routerLink]="['/tracks', t.id]">
    {{ t.name }}
  </a>
</p>
```

这样显示专辑：

```html
<h4>
  <a [routerLink]="['/albums', t.album.id]">
    {{ t.album.name }}
  </a>
</h4>
```

3. SearchComponent类

先看看它的构造函数。

```ts searchComponent
export class SearchComponent implements OnInit {
  query: string;
  results: Object;

  constructor(private spotify: SpotifyService,
              private router: Router,
              private route: ActivatedRoute) {
    this.route
      .queryParams
      .subscribe(params => { this.query = params['query'] || ''; });
  }
```

在构造函数的参数中，我们注入了（之前创建的） SpotifyService、 Router和ActivatedRoute，并将它们设置为类属性。
在构造函数中，我们用subscribe订阅到queryParams属性。通过它，我们可以访问查询参数，比如搜索关键词（params['query']）。
在一个像http://localhost/#/search?query=cats&order=ascending这样的URL中， queryParams以对象的形式为我们提供路由参数。这就是说，我们可以从params['order']中访问order（在本例中为ascending）。

另外，注意queryParams与route.params有所不同。 route.params在路由配置中匹配参数，而queryParams在查询字符串中匹配参数。

- search方法

```ts search
search(): void {
  console.log('this.query', this.query);
  if (!this.query) {
    return;
}

this.spotify
  .searchTrack(this.query)
  .subscribe((res: any) => this.renderResults(res));
}
```

search函数通过当前this.query属性的值来得知应该搜索什么。因为我们在构造函数中订阅了queryParams，所以可以确认this.query总是有最新的搜索关键词。
然后，我们订阅到searchTrack可观察对象。这样，只要有新搜索结果到达，我们就调用renderResults。

```ts renderResults
renderResults(res: any): void {
  this.results = null;
  if (res && res.tracks && res.tracks.items) {
    this.results = res.tracks.items;
  }
}
```

我们声明了组件属性results。只要它的值有变化， Angular就会自动更新视图。

- 在页面加载时进行搜索

正如上面指出的，我们希望在URL包含搜索查询参数时，能够直接自动获取搜索结果。
为了达到这个目标，我们将实现一个Angular路由器提供的钩子，在组件初始化的时候运行它.

```ts ngOnInit
ngOnInit(): void {
  this.search();
}
```

为了使用ngOnInit，我们导入OnInit接口，并声明组件类implements OnInit。
正如你所看到的，我们在这里仅仅执行了搜索。因为我们的搜索关键词来自于URL，所以这没有问题。

- 提交表单

```ts submit
submit(query: string): void {
  this.router.navigate(['search'], { queryParams: { query: query } })
    .then(_ => this.search() );
}
```

我们手动告诉路由器导航到搜索路由，并提供了query参数，然后执行搜索功能。这样做为我们带来了很大的好处：如果刷新浏览器，我们将会看到一样的搜索结果。可以说，我们将搜索关键词保存到URL了。

### 尝试搜索

`ng serve`

### TrackComponent

我们用TrackComponent来处理曲目路由。它显示曲目名字和专辑封面图片，并允许用户使用HTML5的audio标签来进行试听。

```ts trackcomponent
@Component({
  selector: 'theTrack',
  template: `
  <div *ngIf="track">
    <h1>{{ track.name }}</h1>

    <p>
      <img src="{{ track.album.images[1].url }}">
    </p>

    <p>
      <audio controls src="{{ track.preview_url }}"></audio>
    </p>

    <p><a href (click)="back()">Back</a></p>
  </div>
  `
})
```

searchTrack方法

```ts SpotifyService
  searchTrack(query: string): Observable<any[]> {
    return this.search(query, 'track');
  }
```

然后创建一个方法，让我们正在开发的组件可以根据曲目的id来获取曲目信息:

```ts
getTrack(id: string): Observable<any[]> {
  return this.query(`/tracks/${id}`);
}
```

最后，在TrackComponent的新ngOnInit方法中调用getTrack。

```ts
  ngOnInit(): void {
    this.spotify
      .getTrack(this.id)
      .subscribe((res: any) => this.renderTrack(res));
  }
```

其他组件的工作原理很相似，它们都使用SpotifyService中的get*方法来根据id获取艺术家或曲目信息。

### 音乐搜索应用小结

## 路由器钩子

在变换路由前，我们可能想要触发一些行为。典型的例子是用户认证。假设我们有登录路由和被保护的路由。
我们希望只有在登录页面中提供了正确的用户名和密码的时候，才允许应用导航到被保护的路由。
为了实现这个功能，我们需要连接到路由的生命周期钩子，并在激活被保护的路由时获得通知。然后调用一个认证服务，查询用户是否提供了正确的凭证。

要检查一个组件是否可以被激活，我们添加了一个守卫类到路由器配置的`canActivate`数组.

### AuthService

我们来创建一个十分简单的最小化服务，负责认证和授权资源。

```ts
import { Injectable } from '@angular/core';

@Injectable()
export class AuthService {
  login(user: string, password: string): boolean {
    if (user === 'user' && password === 'password') {
      localStorage.setItem('username', user);
      return true;
    }

    return false;
  }
```

login方法将在提供的用户名和密码为'user'和'password'时返回true。此外，在它们匹配时，使用localStorage来保存用户名。它标志着应用程序是否有一个仍然活跃的已登录用户。

>如果你不熟悉，这里解释一下： localStorage是HTML5提供的键值对，用来在浏览器中保存信息。

logout方法清除了username值:

```ts
logout(): any {
  localStorage.removeItem('username');
}
```

最后两个方法是：

- getUser，返回用户名或者null；
- isLoggedIn，使用getUser()并在有用户时返回true。

```ts
getUser(): any {
  return localStorage.getItem('username');
}

isLoggedIn(): boolean {
  return this.getUser() !== null;
}
```

最后一件要做的事是导出一个`AUTH_PROVIDERS`，这样可以将其注入到应用中:

```ts
export var AUTH_PROVIDERS: Array<any> = [
  { provide: AuthService, useClass: AuthService }
];

```

至此，我们有了用于注入到组件的AuthService服务，可以实现用户登录、检查当前登录用户和用户登出等。

随后，我们还将在路由器中使用它来保护ProtectedComponent。不过我们首先创建用于登录的组件。

### LoginComponent

这个组件将在没有登录用户的时候显示登录表单，或者显示一条包含了用户信息和登录链接的小横幅。

```ts loginComponent
export class LoginComponent {
  message: string;

  constructor(private authService: AuthService) {
    this.message = '';
  }

  login(username: string, password: string): boolean {
    this.message = '';
    if (!this.authService.login(username, password)) {
      this.message = 'Incorrect credentials.';
      setTimeout(function() {
        this.message = '';
      }.bind(this), 2500);
    }
    return false;
  }

  logout(): boolean {
    this.authService.logout();
    return false;
  }
}

```

在服务验证用户凭证后，我们就登入用户。
根据用户的登录状态，组件模板中有两段代码片段分别被显示出来。
第一段是登录表单，受到*ngIf="!authService.getUser()"保护。

```ts 
<form class="form-inline" *ngIf="!authService.getUser()">
  <div class="form-group">
    <label for="username">User:</label>
    <input class="form-control" name="username" #username>
  </div>

  <div class="form-group">
    <label for="password">Password:</label>
    <input class="form-control" type="password" name="password" #password>
  </div>

  <a class="btn btn-default" (click)="login(username.value, password.value)">
    Submit
  </a>
</form>
```

第二段是信息横幅，包含了登出链接，受到相反的*ngIf="authService.getUser()"保护。

```ts
<div class="well" *ngIf="authService.getUser()">
  Logged in as <b>{{ authService.getUser() }}</b>
  <a href (click)="logout()">Log out</a>
</div>
```

另外，在出现验证错误时，会显示一段代码片段。

```ts
<div class="alert alert-danger" role="alert" *ngIf="message">
  {{ message }}
</div>
```

### ProtectedComponent 组件和路由守卫

1. ProtectedComponent

要保护组件，必先有组件。 ProtectedComponent组件很简明。

```ts
import {Component} from '@angular/core';

@Component({
  selector: 'protected',
  template: `<h1>Protected content</h1>`
})
export class ProtectedComponent {
}
```

我们希望只有登录的用户可以访问这个组件。但是如何才能做到呢？
答案是使用路由器钩子`canActivate`，连接到一个实现CanActivate接口的守卫类。

2. LoggedInGuard守卫

新建一个名为guards的目录，然后新建loggedIn.guard.ts文件

```ts
import { Injectable } from '@angular/core';
import { CanActivate } from '@angular/router';
import { AuthService } from 'services/AuthService';

@Injectable()
export class LoggedInGuard implements CanActivate {
  constructor(private authService: AuthService) {}

  canActivate(): boolean {
    return this.authService.isLoggedIn();
  }
}

```

该守卫声明了它实现CanActivate接口。可以通过实现canActivate方法满足这个声明。
我们注入AuthService到这个类的构造函数，并将其保存到私有变量authService。
在canActivate函数中，我们通过this.AuthService来检查用户的登录状态isLoggedIn。

3. 配置路由器

为了使用这个守卫，我们需要这样配置路由器：

- 导入LoggedInGuard；
- 在路由配置中使用LoggedInGuard；
- 添加LoggedInGuard到提供者列表中（这样它就可以被注入了）。

我们在app.ts中实现以上步骤。首先导入LoggedInGuard。

```ts
import {AUTH_PROVIDERS} from 'services/AuthService';
import {LoggedInGuard} from 'guards/loggedIn.guard';

const routes: Routes = [
  { path: '',          redirectTo: 'home', pathMatch: 'full' },
  { path: 'home',      component: HomeComponent },
  { path: 'about',     component: AboutComponent },
  { path: 'contact',   component: ContactComponent },
  { path: 'protected', component: ProtectedComponent,
    canActivate: [LoggedInGuard]}
];
```

最后将LoggedInGuard添加到提供者列表中。

```ts
providers: [
    AUTH_PROVIDERS,
    LoggedInGuard,
    { provide: LocationStrategy, useClass: HashLocationStrategy },
  ]
```

4. 用户登录

导入后添加：

- (1) 一个新链接，指向被保护的路由；
- (2) <login>标签到模板中，用来渲染新组件。

```ts
import {LoginComponent} from 'components/LoginComponent';

@Component({
  selector: 'router-app',
  template: `
  <div class="page-header">
    <div class="container">
      <h1>Router Sample</h1>
      <div class="navLinks">
        <a [routerLink]="['/home']">Home</a>
        <a [routerLink]="['/about']">About</a>
        <a [routerLink]="['/contact']">Contact Us</a>
        <a [routerLink]="['/protected']">Protected</a>
      </div>
    </div>
  </div>

  <div id="content">
    <div class="container">

      <login></login>

      <hr>

      <router-outlet></router-outlet>
    </div>
  </div>
  `
})
class RoutesDemoApp {
  constructor(private router: Router) {
  }
}
```

## 嵌套路由

嵌套路由是在一些路由中包含其他路由。利用嵌套路由，我们可以封装父级路由的功能，并在它的子级路由中使用这些功能。

你可以有多重嵌套的router-outlet。这样，应用的每个区域都可以有自己的子组件，这些组件也可以有自己的router-outlet。

### 配置路由

```ts
const routes: Routes = [
  { path: '', redirectTo: 'home', pathMatch: 'full' },
  { path: 'home', component: HomeComponent },
  { path: 'products', component: ProductsComponent, children: childRoutes }
];
```

注意products有个children参数。它是从哪儿来的？我们在定义ProductsComponent时定义了childRoutes。

### ProductsComponent 组件

这个组件有自己的路由配置。

```ts
export const routes: Routes = [
  { path: '', redirectTo: 'main', pathMatch: 'full' },
  { path: 'main', component: MainComponent },
  { path: ':id', component: ByIdComponent },
  { path: 'interest', component: InterestComponent },
  { path: 'sportify', component: SportifyComponent },
];

```

然后在组件的路由器中为每种静态子路由添加一个链接。

```html
<a [routerLink]="['./main']">Main</a> |
<a [routerLink]="['./interest']">Interest</a> |
<a [routerLink]="['./sportify']">Sportify</a> |
```

可以看到路由链接的格式都是['./main']，前面有./。它表明了导航到main路由是相对于当前路由上下文的。你也可以用['products', 'main']的形式声明路由。这么做的坏处是，子路由知晓父路由；如果想要移动或者复用该组件，可能需要重新编写路由链接。

```ts
@Component({
  selector: 'products',
  template: `
  <h2>Products</h2>

  <div class="navLinks">
    <a [routerLink]="['./main']">Main</a> |
    <a [routerLink]="['./interest']">Interest</a> |
    <a [routerLink]="['./sportify']">Sportify</a> |
    Enter id: <input #id size="6">
    <button (click)="goToProduct(id.value)">Go</button>
  </div>

  <div class="products-area">
    <router-outlet></router-outlet>
  </div>
  `
})

export class ProductsComponent {
  constructor(private router: Router, private route: ActivatedRoute) {
  }

  goToProduct(id:string): void {
    this.router.navigate(['./', id], {relativeTo: this.route});
  }
}
```