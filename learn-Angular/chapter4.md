# 内置指令

## 简介

内置指令是已经导入过的，你的组件可以直接使用它们。因此，不用像你自己的组件一样把它们作为指令导入进来。

## ngIf

如果你希望根据一个条件来决定显示或隐藏一个元素，可以使用ngIf指令。这个条件是由你传给指令的表达式的结果决定的。
如果表达式的结果返回的是一个假值，那么元素会从DOM上被移除。

```html
<div *ngIf="false"></div> <!-- never displayed -->
<div *ngIf="a > b"></div> <!-- displayed if a is more than b -->
<div *ngIf="str == 'yes'"></div> <!-- displayed if str holds the string "yes" -->
<div *ngIf="myFunc()"></div> <!-- displayed if myFunc returns a true value -->
```

## ngSwitch

有时候你需要根据一个给定的条件来渲染不同的元素。对表达式进行一次求值，然后根据其结果来决定如何显示指令内的嵌套元素。(其实我们真正想表达的只是一个else而已。)

- 使用ngSwitchCase指令描述已知结果；
- 使用ngSwitchDefault指令处理所有其他未知情况。

```html
<div class="container" [ngSwitch]="myVar">
  <div *ngSwitchCase="'A'">Var is A</div>
  <div *ngSwitchCase="'B'">Var is B</div>
  <div *ngSwitchCase="'C'">Var is C</div>
  <div *ngSwitchDefault>Var is something else</div>
</div>
```

ngSwitchDefault元素是可选的。如果我们不用它，那么当myVar没有匹配到任何期望的值时就不会渲染任何东西

你也可以为不同的元素声明同样的*ngSwitchCase值，这样就可以多次匹配同一个值了。例子如下:

```html
<h4 class="ui horizontal divider header">
      Current choice is {{ choice }}
    </h4>

    <div class="ui raised segment">
      <ul [ngSwitch]="choice">
        <li *ngSwitchCase="1">First choice</li>
        <li *ngSwitchCase="2">Second choice</li>
        <li *ngSwitchCase="3">Third choice</li>
        <li *ngSwitchCase="4">Fourth choice</li>
        <li *ngSwitchCase="2">Second choice, again</li>
        <li *ngSwitchDefault>Default choice</li>
      </ul>
    </div>

    <div style="margin-top: 20px;">
      <button class="ui primary button" (click)="nextChoice()">
        Next choice
      </button>
    </div>
```

当choice的值是2的时候，第2个和第5个li都会被渲染。

## ngStyle

使用ngStyle指令，可以通过Angular表达式给特定的DOM元素设定CSS属性

该指令最简单的用法就是`[style.<cssproperty>]="value"`的形式:

```html
<div [style.background-color]="'yellow'">
  Uses fixed yellow background
</div>
```

另一种设置固定值的方式就是使用ngStyle属性，使用键值对来设置每个属性:

```html
<div [ngStyle]="{color: 'white', 'background-color': 'blue'}">
  Uses fixed white text on blue background
</div>
```

- Note

> 注意，在ngStyle的说明中，我们对background-color使用了单引号，但却没有对color使用。这是为什么呢？因为ngStyle的参数是一个JavaScript对象，而color是一个合法的键，不需要引号。但是在background-color中，连字符是不允许出现在对象的键名当中的，除非它是一个字符串，因此使用了引号。通常情况下，我尽量不会对对象的键使用引号，除非不得不用.

ngStyle指令真正的能力在于使用动态值:

```html
<div class="ui input">
  <input type="text" name="color" value="{{color}}" #colorinput>
</div>

<div class="ui input">
  <input type="text" name="fontSize" value="{{fontSize}}" #fontinput>
</div>

<button class="ui primary button" (click)="apply(colorinput.value, fontinput.value)">
  Apply settings
</button>

```

在这个例子中，我们定义了两个输入框。然后使用它们的值来设置三个元素的CSS属性。在第一个元素中，我们基于输入框的值来设定字体大小:

```html
<div>
  <span [ngStyle]="{color: 'red'}" [style.font-size.px]="fontSize">
     red text
  </span>
</div>
```

注意，我们在某些情况下必须指定单位。例如，把font-size设置为12不是合法的CSS，必须指定一个单位，比如12px或者1.2em。 Angular提供了一个便捷语法用来指定单位：这里我们使用的格式是`[style.fontSize.px]`。

另外两个元素使用#colorinput的值来设置文字颜色和背景颜色:

```html
<h4 class="ui horizontal divider header">
      ngStyle with object property from variable
    </h4>

    <div>
      <span [ngStyle]="{color: color}">
        {{ color }} text
      </span>
    </div>

    <h4 class="ui horizontal divider header">
      style from variable
    </h4>

    <div [style.background-color]="color"
         style="color: white;">
      {{ color }} background
    </div>
```

这样，当我们点击Apply settings按钮时，就会调用方法来设置新的值:

```ts
apply(color: string, fontSize: number) {
  this.color = color;
  this.fontSize = fontSize;
}
```

## ngClass

ngClass指令在HTML模板中用ngClass属性来表示，让你能动态设置和改变一个给定DOM元素的CSS类:

使用这个指令的第一种方式是传入一个对象字面量。该对象希望以类名作为键，而值应该是一个用来表明是否应该应用该类的真/假值.假设我们有一个叫作bordered的CSS类，用来给元素添加一个黑色虚线边框:

```css
.bordered {
  border: 1px dashed black;
  background-color: #eee;
}
```

我们来添加两个div元素：一个一直都有bordered类（因此一直有边框），而另一个永远都不会有:

```html
<div [ngClass]="{bordered: false}">This is never bordered</div>
<div [ngClass]="{bordered: true}">This is always bordered</div>
```

当然，使用ngClass指令来动态分配类会有用得多。为了动态使用它，我们添加了一个变量作为对象的值:

```html
<div [ngClass]="{bordered: isBordered}">
  Using object literal. Border {{ isBordered ? "ON" : "OFF" }}
</div>
```

或者在组件中定义该对象：

```ts
export class NgClassSampleApp {
  isBordered: boolean;
  classesObj: Object;
  classList: string[];
```

```html
<div [ngClass]="classesObj">
  Using object var. Border {{ classesObj.bordered ? "ON" : "OFF" }}
</div>
```

我们也可以使用一个类名列表来指定哪些类名会被添加到元素上。为此，我们可以传入一个数组型字面量:

```html
<div class="base" [ngClass]="['blue', 'round']">
  This will always have a blue background and
  round corners
</div>
```

或者在组件中声明一个数组对象:

```ts
this.classList = ['blue', 'round'];
```

```html
<div class="base" [ngClass]="classList">
  This is {{ classList.indexOf('blue') > -1 ? "" : "NOT" }} blue
  and {{ classList.indexOf('round') > -1 ? "" : "NOT" }} round
</div>
```

最后添加到元素的类总会是HTML属性class中的类和[ngClass]指令求值结果得到的类的集合,上例中就是"base blue round"

## ngFor

这个指令的任务是重复一个给定的DOM元素（或一组DOM元素），每次重复都会从数组中取一个不同的值。这个指令是AngularJS中ng-repeat的继任者。

它的语法是`*ngFor="let item of items"`。

- `let item`语法指定一个用来接收items数组中每个元素的（模板）变量。
- `items`是来自组件控制器的一组项的集合。

迭代普通数组、对象数组、嵌套数组

### 获取索引

我们可以在ngFor指令的值中插入语法`let idx = index`并用分号分隔开，这样就可以获取索引了。

## ngNonBindable

当我们想告诉Angular不要编译或者绑定页面中的某个特殊部分时，要使用ngNodBindable指令.

```html
<div class='ngNonBindableDemo'>
  <span class="bordered">{{ content }}</span>
  <span class="pre" ngNonBindable>
    &larr; This is what {{ content }} rendered
  </span>
</div>
```

有了ngNonBindable属性， Angular不会编译第二个span里的内容，而是原封不动地将其显示出来