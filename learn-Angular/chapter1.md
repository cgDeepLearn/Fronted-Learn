# 第一章 编写你的第一个Angular Web应用

## 仿制reddit网站

我们将构建一个应用，它能让用户发表推荐文章（包括标题和URL）并对每篇文章投票。
首先，用户将提交一个新的链接。之后，其他用户可以对每篇文章投票：“顶”或“踩”。每个链接都有一个最终得票。

## 起步

### TypeScript

TypeScript是JavaScript ES6版的一个超集，增加了类型支持

安装`Node.js`,然后安装`TypeScript`:

```node
npm install -g typescript
```

### angular-cli

```
npm install -g @angular/cli
```

安装完在命令行可以用`ng`命令运行它

MAC和Linux还需安装`watchman`

### 示例项目

打开终端窗口并且运行ng new命令，快速创建一个新的项目：

```ng
ng new angular2-hello-world
cd angular2-hello-world
```

## 运行应用

在项目根目录:

```
ng serve
```

### 制作Component

Angular背后的指导思想之一就是组件化。

组件化背后的基本思想：我们要教浏览器认识一些拥有自定义功能的新标签.

要使用angular-cli来创建新组件，可以使用generate（生成）命令。
要生成hello-world组件，我们需要运行下列命令：`ng generate component hello-world`

那该怎么定义一个新组件呢？最基本的组件包括两部分：

- (1) Component注解
- (2) 组件定义类

```ts
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-hello-world',
  templateUrl: './hello-world.component.html',
  styleUrls: ['./hello-world.component.css']
})
export class HelloWorldComponent implements OnInit {

  constructor() { }

  ngOnInit() {
  }

}
```

### 导入依赖

`import`语句定义了我们写代码时要用到的那些模块。这里我们导入了两样东西：`Component`和`OnInit`.

注意这个import语句的结构是`import { things } from wherever`格式。我们把{ things }这部分的写法叫作解构。解构是由ES6和TypeScript提供的一项特性.

### Component注解

导入依赖后，我们还要声明该组件。

```ts
@Component({
  selector: 'app-hello-world',
  templateUrl: './hello-world.component.html',
  styleUrls: ['./hello-world.component.css']
})
```

我们可以把注解看作添加到代码上的元数据。当在HelloWorld类上使用@Component时，就把HelloWorld“装饰”（decorate）成了一个Component。

这个`<app-hello-world>`标签表示我们希望在HTML中使用该组件。要实现它，就得配置@Component并把selector指定为app-hello-world.

这里的selector属性用来指出该组件将使用哪个DOM元素。如果模板中有`<app-helloworld></app-hello-world>`标签，就用该Component类及其组件定义息对其进行编译。

### 用templateUrl添加模板

在这个组件中，我们把templateUrl指定为`./hello-world.component.html`。这意味着我们将从与该组件同目录的`hello-world.component.html`文件中加载模板。

### 添加template

我们有两种定义模板的方式：使用@Component对象中的template属性；指定templateUrl属性。

我们可以通过传入template选项来为@Component添加一个模板:

```ts
@Component({
  selector: 'app-hello-world',
  template: `
    <p>
      hello-world works inline!
    </p>
  `
})
```

注意，我们在反引号中定义了template字符串。这是ES6中的一个新特性（而且很棒），允许使用多行字符串。使用反引号定义多行字符串，可以让我们更轻松地把模板放到代码文件中.

### 用styleUrls添加CSS样式

```ts
styleUrls: ['./hello-world.component.css']
```

### 加载组件

因为我们为HelloWorldComponent配置了app-hello-world选择器，所以要在模板中使用`<app-hello-world></app-hello-world>`。

### 把数据添加到组件中

我们来创建一个新的组件，它将显示用户的名字。

```
ng generate component user-item
```

为了添加名字，我们要在UserItemComponent类上引入一个属性，来声明该组件有一个名叫name的局部变量。

```ts
export class UserItemComponent implements OnInit {
  name: string; // added name property
  constructor() {
    this.name = 'James'; // set the name
   }

  ngOnInit() {
  }

}
```

- name属性
- 构造函数
- 渲染模板

填好这个值之后，我们可以用模板语法（也就是双花括号语法`{{ }}`）在模板中显示该变量的值

```html
<p>
  Hello {{ name }}
</p>
```

注意，我们在template中引入了一个新的语法： {{ name }}。这些括号叫作“模板标签”（也叫“小胡子标签”）。模板标签中间的任何东西都会被当作一个表达式来展开。 这里， 因为template是绑定到组件上的，所以name将会被展开为this.name的值，

## 使用数组

```
ng generate component user-list
```

把app.component.html文件中的`<app-user-item>`替换为`<app-user-list>`,修改构造函数:

```ts
export class UserListComponent implements OnInit {
  names: string[];
  constructor() {
    this.names = ['Ari', 'Carlos', 'Felipe', 'Nate'];
   }

  ngOnInit() {
  }

}
```

现在就可以更新模板，渲染出这个名字列表了。这时我们要用到`*ngFor`，它会在一个列表上进行迭代，为列表中的每一个条目生成一个新标签。

```html
<ul>
  <li *ngFor="let name of names">Hello {{ name }}</li>
</ul>
```

names是我们在HelloWorld对象中定义的名字数组。 letname叫作引用。 "let name of names"的意思是，循环处理names中的每一个元素并将其逐个赋值给一个名叫name的局部变量。

## 使用 UserItemComponent 组件

### 渲染 UserItemComponent

UserItemComponent指定了选择器app-user-item，接下来要把这个标签添加到模板中。我们要做的就是把li标签替换为app-user-item标签。

```ts
<ul>
  <app-user-item
    *ngFor="let name of names">
  </app-user-item>
</ul>
```

它确实重复了，但有些不大对劲——每个用户名都是Felipe！我们需要某种方式来把数据传给子组件。
 Angular为此提供了一种方式：`@Input`注解

### 接收输入

现在，我们需要进行一些改动，让组件的name属性从外部接收值.这里要把`UserItemComponent`修改为：

```ts
import {
  Component,
  OnInit,
  Input // add Input
} from '@angular/core';

@Component({
  selector: 'app-user-item',
  templateUrl: './user-item.component.html',
  styleUrls: ['./user-item.component.css']
})
export class UserItemComponent implements OnInit {
  @Input() name: string; // added Input annotation
  constructor() {
    // removed setting name
   }

  ngOnInit() {
  }

}
```

### 传入Input值

为了把一个值传入组件，就要在模板中使用方括号[]语法:

```html
<ul>
  <app-user-item
    *ngFor="let name of names"
    [name]="name">
  </app-user-item>
</ul>
```

注意，我们在app-user-item标签上添加了新属性[name]="name"。 在Angular中，添加一个带方括号的属性（比如[foo]）意味着把一个值传给该组件上同名的输入属性（比如foo）。

- (1) 在names中迭代；
- (2) 为names中的每个元素创建一个新的UserItemComponent；
- (3) 把当前名字的值传给UserItemComponent上名叫name的Input属性

## “启动”速成班

`ng serve` 启动应用过程:

- angular-cli.json指定一个"main"文件，这里是main.ts；
- main.ts是应用的入口点，并且会引导（bootstrap）我们的应用；
- 引导过程会引导一个Angular模块——我们尚未讨论过模块，不过很快就会谈到；
- 我们使用AppModule来引导该应用，它是在src/app/app.module.ts中指定的；
- AppModule指定了将哪个组件用作顶层组件，这里是AppComponent；
- AppComponent的模板中有一个`<app-user-list>`标签，它会渲染出我们的用户列表。

Angular有一个强大的概念： 模块。当引导一个Angular应用时，并不是直接引导一个组件，而是创建了一个`NgModule`，它指向了你要加载的组件。
我们看一下代码(/src/app/app.module.ts):

```ts
@NgModule({
  declarations: [
    AppComponent,
    HelloWorldComponent,
    UserItemComponent,
    UserListComponent
  ],
  imports: [
    BrowserModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

我们首先看到的是@NgModule注解。像所有注解一样，这段@NgModule( ... )代码为紧随其
后的AppModule类添加了元数据。

@NgModule注解有三个属性： declarations、 imports和bootstrap。

- declarations指定了在该模块中定义的组件。你可能已经注意到了，当我们使用ng generate时，它会自动把生成的组件添加到这个列表里！**要想在模板中使用一个组件，你必须首先在NgModule中声明它**
- imports 描述了该模块有哪些依赖。我们正在创建一个浏览器应用，因此要导入BrowserModule。
- bootstrap告诉Angular，当使用该模块引导应用时，我们要把AppComponent加载为顶层组件。

## 扩展你的应用

仿制Reddit

```
ng new angular2-reddit
```

### 添加CSS

- src/index.html
- src/styles.css
- src/app/vendor
- src/assets/images

### 应用程序组件

(1) 存储我们的当前文章列表；
(2) 包含一个表单，用来提交新的文章。

- 修改模板

```html app.component.html
<form class="ui large form segment">
  <h3 class="ui header">Add a Link</h3>

  <div class="field">
    <label for="title">Title:</label>
    <input name="title" >  
  </div>
  <div class="field">
    <label for="link">Link:</label>
    <input name="link" > 
  </div>

</form>
```

我们要创建一个template，它定义了两个input标签：一个用于文章的标题（title），另一个用于文章的链接（link URL）。

### 添加互动

在表单中添加一个提交按钮，来添加一些交互。当提交该表单时，我们希望调用一个函数来创建并添加一个链接。可以往`<button />`元素上添加一个交互事件来实现这个功能.

把事件的名字包裹在圆括号()中就可以告诉Angular：我们要响应这个事件。比如，要想添加一个函来响应`<button />`的onClick事件，可以像这样把它传进去：

```html
  <!-- added this button -->
  <button (click)="addArticle()"  
          class="ui positive right floated button">
    Submit link
  </button>
```

这样，当点击这个按钮时，就会调用一个名叫addArticle()的函数； 我们要在AppComponent类中定义这个函数。

```ts app.component.ts
export class AppComponent {
  title = 'app';
  // add addArticle
  addArticle(title: HTMLInputElement, link: HTMLInputElement): boolean {
    console.log(`Adding article title: ${title.value} and link: ${link.value}`);
    return false;
  }
}
```

我们可以通过为表单中的input元素添加一个特殊的语法来取得模板变量。修改后的模板如下所示:

```html
<form class="ui large form segment">
  <h3 class="ui header">Add a Link</h3>

  <div class="field">
    <label for="title">Title:</label>
    <input name="title" #newtitle>  <!-- changed -->
  </div>
  <div class="field">
    <label for="link">Link:</label>
    <input name="link" #newlink> <!-- changed -->
  </div>

  <!-- added this button -->
  <button (click)="addArticle(newtitle, newlink)"  
          class="ui positive right floated button">  
    Submit link
  </button>

</form>

<div class="ui grid posts">
  <app-article
    *ngFor="let article of sortedArticles()"
    [article]="article">
  </app-article>
</div>
```

注意，我们在input标签上使用了`#（hash）`来要求Angular把该元素赋值给一个局部变量。通过把#title和#link添加到适当的`<input />`元素上，就可以把它们作为变量传给按钮上addArticle()函数！

总结一下，我们一共进行了四项修改：

- (1) 在模版中创建了一个button标签，向用户表明应该点击哪里；
- (2) 新建了一个名叫addArticle的函数，来定义按钮被点击时要做的事情；
- (3) 在button上添加了一个(click)属性，意思是“只要点击了这个按钮，就运行addArticle函数”；
- (4) 在两个`<input>`标签上分别添加了#newtitle和#newlink属性。

### 添加文章组件

```
ng generate component article
```

- (1) 创建ArticleComponent的template

```html
<div class="four wide column center aligned votes">
  <div class="ui statistic">
    <div class="value">
      {{ votes }}
    </div>
    <div class="label">
      Points
    </div>
  </div>
</div>
<div class="twelve wide column">
  <a class="ui large header" href="{{ link }}">
    {{ title }}
  </a>

  <ul class="ui big horizontal list voters">
    <li class="item">
      <a href (click)="voteUp()">
        <i class="arrow up icon"></i>
        upvote 
      </a>
    </li>
    <li class="item"> 
      <a href (click)="voteDown()">
        <i class="arrow down icon"></i>
        downvote
      </a>
    </li>
  </ul>
</div>
```

(1) 左侧是投票的数量；(2) 右侧是文章的信息。我们用模板展开字符串{{ votes }}和{{ title }}来展示votes和title。这些值来自ArticleComponent类中的votes和title属性.

在upvote和downvote的链接上，我们还有一个动作。只要分别将其按钮上的(click)绑定到voteUp()和voteDown()就可以了。当upvote按钮被按下时， ArticleComponent类上的voteUp()函数就会被调用；当downvote按钮被按下时， voteDown()函数会被调用。

- (2) 创建ArticleComponent

```ts article.component.ts
@Component({
  selector: 'app-article',
  templateUrl: './article.component.html',
  styleUrls: ['./article.component.css'],
  host: {
    class: 'row'
  }
})
```

首先，我们用@Component定义了一个新组件。 selector表示会用`<app-article>`标签将该组件放在页面中（也就是说，该选择器是一个标签名）.

我们希望每个app-article都独占一行。我们使用的是Semantic UI，它提供了一个用来表示行的CSS类 ，叫作row。`host: { class: 'row' }`它告诉Angular：我们要在宿主元素（app-article标签）上设置class属性，使其具有row类。

- (3) 创建组件定义类ArticleComponent

```ts
export class ArticleComponent implements OnInit {
  votes: number;
  title: string;
  link: string;

  constructor() {
    this.title = 'Angular 2';
    this.link = 'http://angular.io';
    this.votes = 10;
  }

  voteUp() {
    this.votes += 1;
  }

  voteDown() {
    this.votes -= 1;
  }

  ngOnInit() {
  }

}
```

此处我们在ArticleComponent上创建了以下三个属性。

(1) votes：一个数字，用来表示所有“赞”减去所有“踩”的数量之和。

(2) title：一个字符串，用来存放文章的标题。

(3) link：一个字符串，用来存放文章的URL。

- (4) 使用app-article组件

这个例子中，我们希望让AppComponent组件来渲染这个新组件。因此修改AppComponent的代码，把`<app-article>`标签添加到AppComponent的模板中，紧跟在`</form>`标签后面：

```html
<div class="ui grid posts">
  <app-article>
  </app-article>
</div>
```

为了把这个新的 ArticleComponent 组件引荐给 AppComponent ， 我们需要把 ArticleComponent添加到NgModule的declarations列表中。在app.module.ts中(新版Angular会自动添加)

在默认情况下， JavaScript会把click事件冒泡到所有父级组件中。因为click事件被冒泡到了父级，浏览器就会尝试导航到这个空白链接，于是浏览器就重新刷新了。要解决这个问题，我们得让click的事件处理器返回`false`。

## 渲染多行

### 创建 Article 类

创建article.model.ts来定义所需的Article类

```ts
export class Article {
  title: string;
  link: string;
  votes: number;

  constructor(title: string, link: string, votes?: number) {
    this.title = title;
    this.link = link;
    this.votes = votes || 0;
  }
}
```

此处，我们创建了一个新类，用来表示Article。注意，这是一个普通类而不是Angular组件。在MVC模式中，它被称为模型（model）。
每篇文章都有一个标题title、一个链接link和一个投票总数votes。当创建新文章时，我们需要title和link。 votes参数是可选的（用末尾的?标出来），并且默认为0。

我们来修改ArticleComponent的代码，让它使用新的Article类。以前是直接把这些属性存到ArticleComponent组件上，现在则把它改为存到Article类的一个实例上。

```ts
import { Article } from './article.model'; // 引入 Article

export class ArticleComponent implements OnInit {
  article: Article;

  constructor() {
    this.article = new Article(
      'Angular 2',
      'http://angular.io',
      10);
  }

  voteUp(): boolean {
    this.article.votes += 1;
    return false;
  }

  voteDown(): boolean {
    this.article.votes -= 1;
    return false;
  }

  ngOnInit() {
  }

}
```

修改了模型，那么我们的视图也需要修改，变成`{{ article.votes }}`等

**当前的voteUp和voteDown违反了`迪米特法则`。迪米特法则是指：一个对象对其他对象的结构或属性所作的假设应该越少越好。**

问题在于ArticleComponent组件了解太多Article类的内部知识了。要解决这一点，就要为Article类添加voteUp和voteDown方法。

```ts article.model.ts
export class Article {
  title: string;
  link: string;
  votes: number;

  constructor(title: string, link: string, votes?: number) {
    this.title = title;
    this.link = link;
    this.votes = votes || 0;
  }

  voteUp(): void {
    this.votes += 1;
  }

  voteDown(): void {
    this.votes -= 1;
  }

  domain(): string {
    try {
      const link: string = this.link.split('//')[1];
      return link.split('/')[0];
    } catch (err) {
      return null;
    }
  }
}
```

然后修改调用方法:

```ts article.component.ts
export class ArticleComponent implements OnInit {
  article: Article;

  constructor() {
    this.article = new Article(
      'Angular 2',
      'http://angular.io',
      10);
  }

  voteUp(): boolean {
    this.article.votes += 1;
    return false;
  }

  voteDown(): boolean {
    this.article.votes -= 1;
    return false;
  }

  ngOnInit() {
  }

}
```

- 为什么模型和组件中都有一个voteUp函数？

原因 在于 ， 这两 个函 数 所做 的事 情 略有 不同 。 ArticleComponent 上 的voteUp()函数是与组件的视图有关的，而Article模型上的voteUp()定义了模型上的变化。也就是说，当投票时， Article类可以对模型上的相应功能进行封装。在真实的应用中， Article模型的内部可能更加复杂，比如向Web服务器发起一个API调用，而你显然不希望这些本属于模型的代码出现在组件的控制器中。

同样，在ArticleComponent中，我们return false;从而“阻止事件冒泡”。这是属于视图的逻辑片段，我们不希望Article模型上的voteUp()函数懂得这些与视图有关的API。也就是说， Article模型应该让投票逻辑从特定的视图中分离出来。

### 存储多篇文章

让AppComponent拥有一份文章集合:

```ts
import { Component } from '@angular/core';
import { Article } from './article/article.model';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  articles: Article[];

  constructor() {
    this.articles = [
      new Article('Angular 2', 'http://angular.io', 3),
      new Article('Fullstack', 'http://fullstack.io', 2),
      new Article('Angular Homepage', 'http://angular.io', 1),
    ];
  }

  addArticle(title: HTMLInputElement, link: HTMLInputElement): boolean {
    console.log(`Adding article title: ${title.value} and link: ${link.value}`);
    this.articles.push(new Article(title.value, link.value, 0));
    title.value = '';
    link.value = '';
    return false;
  }
}
```

`Article[]`看起来可能有点陌生。这里的意思是articles是Article的数组。另一种写法是`Array<Article>`。这种模式被称为`泛型`。 Java、 C#和一些别的语言中都有这个概念，意思是你的集合（Array）是有类型的。也就是说， Array是一个集合，它只能存放Article类型的对象。

### 使用inputs配置ArticleComponent

我们真正想做的是配置要显示的Article。比如，假设我们有article1和article2两篇文章，那就要支持把一个Article型的“参数”传给组件来复用app-article组件，就像这样:

```html
<app-article [article]="article1"></app-article>
<app-article [article]="article2"></app-article>
```

Angular通过Component上的Input注解来支持我们这样做：

```ts
class ArticleComponent {
@Input() article: Article;
// ...
```

我们把输入属性的名字放入方括号中（[article]），而该属性的值就是我们要传给此输入属性的那个。

ArticleComponent组件使用@Input之后变成了下面这样:

```ts
import {
  Component,
  OnInit,
  Input
} from '@angular/core';
import { Article } from './article.model';

@Component({
  selector: 'app-article',
  templateUrl: './article.component.html',
  styleUrls: ['./article.component.css'],
  host: {
    class: 'row'
  }
})
export class ArticleComponent implements OnInit {
  @Input() article: Article;

  voteUp(): boolean {
    this.article.voteUp();
    return false;
  }

  voteDown(): boolean {
    this.article.voteDown();
    return false;
  }

  ngOnInit() {
  }

}

```

### 渲染文章列表

这次我们要配置AppComponent来渲染所有articles.要实现这个功能，就不能单独使用`<app-article>`标签了，而要用NgFor指令在articles数组上进行迭代，并为其中的每一个都渲染一份app-article。

```html
<div class="ui grid posts">
  <app-article
    *ngFor="let article of articles"
    [article]="article">
  </app-article>
</div>
```

*ngFor="let article of articles"语法会对articles列表进行迭代，并且为列表中的每一个条目创建一个局部变量article。
要为组件指定一个输入属性article，就要使用[inputName]="inputValue"表达式。

## 添加新文章

现在，我们需要修改addArticle以便在按下按钮时实际添加一篇新文章。修改addArticle方法，使其变成下面这样:

```ts app.component.ts
  addArticle(title: HTMLInputElement, link: HTMLInputElement): boolean {
    console.log(`Adding article title: ${title.value} and link: ${link.value}`);
    this.articles.push(new Article(title.value, link.value, 0));
    title.value = '';
    link.value = '';
    return false;
  }
```

- (1) 创建一个具有所提交标题和URL的Article新实例；
- (2) 把它加入Article数组；
- (3) 清除input字段的值。 (我们要如何清除input字段的值呢？回忆一下， title和link都是HTMLInputElement对象。这就意味着我们可以设置它们的属性。当我们修改value属性时，页面中的input标签也会跟着改变)

## 最后的修整

### 显示文章所属的域名

我们先为链接添加一个提示信息，以便在用户点击链接时显示将重定向到的域名。
把domain方法添加到Article类中。

```ts article.model.ts
  domain(): string {
    try {
      const link: string = this.link.split('//')[1];
      return link.split('/')[0];
    } catch (err) {
      return null;
    }
  }
```

把对该函数的调用添加到ArticleComponent的模板中：

```html article.component.html
<div class="meta">({{ article.domain() }})</div>
```

### 基于分数重新排序

希望让分数最高的条目显示在顶部，让低分条目沉到底部。

我们把articles存储在了AppComponent类中，但这个数组是无序的。处理这种情况的简单方式是在AppComponent上创建一个新方法`sortedArticles`。

```ts
sortedArticles(): Article[] {
    return this.articles.sort((a: Article, b: Article) => b.votes - a.votes);
}
```

这样，在ngFor中，我们就可以在sortedArticles()上而不是直接在articles上迭代了：

```html
<div class="ui grid posts">
  <app-article
    *ngFor="let article of sortedArticles()"
    [article]="article">
  </app-article>
</div>
```

## 总结

很多Angular程序的写法都和我们刚刚所做的类似：

- (1) 把应用拆分成组件；
- (2) 创建视图；
- (3) 定义模型；
- (4) 显示模型；
- (5) 添加交互。