# Angular 中的表单

## 表单--既重要又复杂

- 表单输入意味着需要在页面和服务器端同时修改这份数据；
- 修改的内容通常要在页面的其他地方反映出来；
- 用户的输入可能存在很多问题，所以需要验证输入的内容；
- 用户界面需要清晰地显示出可能出现的预期结果和错误信息；
- 字段之间的依赖可能存在复杂的业务逻辑；
- 我们希望不依赖DOM选择器就能轻松测试表单

Angular已经给出了上述所有问题的解决方案:

- 表单控件(`FormControl`)封装了表单中的输入，并提供了一些可供操纵的对象。
- 验证器(`validator`)让我们能以自己喜欢的任何方式验证表单输入。
- 观察者(`observer`)让我们能够监听表单的变化，并作出相应的回应。

## FormControl和FormGroup

`FormControl`和`FormGroup`是Angular中两个最基础的表单对象。

### FormControl

FormControl代表单一的输入字段，它是Angular表单中的最小单元。FormControl封装了这些字段的值和状态，比如是否有效、是否脏（被修改过）或是否有错误等。

```ts
// create a new FormControl with the value "Nate"
let nameControl = new FormControl("Nate");
let name = nameControl.value; // -> Nate
// now we can query this control for certain values:
nameControl.errors // -> StringMap<string, any> of errors
nameControl.dirty // -> false
nameControl.valid // -> true
// etc.
```

为了构建表单，我们会创建几组FormControl对象，然后为它们附加元数据和逻辑.

```html
<!-- part of some bigger form -->
<input type="text" [formControl]="name" />
```

### FormGroup

大多数表单都拥有不止一个字段，因此我们需要某种方式来管理多个FormControl。FormGroup则可以为一组FormControl提供总包接口（wrapper interface），来解决这种问题。

```ts
let personInfo = new FormGroup({
    firstName: new FormControl("Nate"),
    lastName: new FormControl("Murray"),
    zip: new FormControl("90210")
})
```

FormGroup和FormControl都继承自同一个祖先AbstractControl.

注意，当我们试图从FormGroup中获取value时，会收到一个“键值对”结构的对象。它能让我们从表单中一次性获取全部的值而无需逐一遍历FormControl，使用起来相当顺手。

## 第一个表单

我们先把表单变为组件。你应该还记得，定义组件需要包含以下三个部分：

- 配置@Component()注解；
- 创建模板；
- 在组件定义类中实现自定义功能

### 加载FormsModule

Angular中有两种使用表单的方式，使用FormsModule以及使用ReactiveFormsModule。既然都要用到，那么这个模块就同时导入它们。因此需要在引用启动程序app.ts中这样写：

```ts
import {
  FormsModule,
  ReactiveFormsModule
} from '@angular/forms';
// farther down...

@NgModule({
  declarations: [
    FormsDemoApp,
    DemoFormSku,
// ... our declarations here
  ],
  imports: [
    BrowserModule,
    FormsModule, // <-- add this
    ReactiveFormsModule // <-- and this
  ],
  bootstrap: [ FormsDemoApp ]
})
class FormsDemoAppModule {}
```

FormsModule为我们提供了一些模板驱动的指令，例如：

- ngModel
- NgForm

ReactiveFormsModule则提供了下列指令：

- formControl
- ngFormGroup

### 简易SKU表单: @Component

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'demo-form-sku',
```

这里我们可以通过demo-form-sku标签来使用这个组件

### 简易SKU表单: template

```html
<div class="ui raised segment">
  <h2 class="ui header">Demo Form: Sku</h2>
  <form #f="ngForm"
        (ngSubmit)="onSubmit(f.value)"
        class="ui form">

    <div class="field">
      <label for="skuInput">SKU</label>
      <input type="text"
             id="skuInput"
             placeholder="SKU"
             name="sku" ngModel>
    </div>

    <button type="submit" class="ui button">Submit</button>
  </form>
</div>
```

1. form和NgForm

我们导入了FormsModule，因此可以在视图中使用NgForm了。记住，当这些指令在视图中可用时，它就会被附加到任何能匹配其selector的节点上。

NgForm做了一件便利但隐晦的工作：它的选择器包含form 标签（而不用显式添加ngForm属性）。这意味着当我们导入FormsModule时候， NgForm就会被自动附加到视图中所有的`<form>`标签上。

NgForm给我们提供了两个重要的功能：

- (1) 一个名叫ngForm的FormGroup对象；
- (2) 一个输出事件(ngSubmit)。

首先，我们使用了`#f="ngForm"`。 #v=thing语法的意思是，我们希望在当前视图中创建一个局部变量。

这里我们为视图中的ngForm创建了一个别名，并绑定到变量#f。这个ngForm来自哪里呢？它是由NgForm指令导出的。
ngForm是什么类型的对象呢？它是FormGroup类型的。这意味着我们可以在视图中把变量f当作FormGroup使用，而这也正是我们在输出事件(ngSubmit)中的使用方法.

我们在表单中绑定ngSubmit事件的语法是： `(ngSubmit)="onSubmit(f.value)"`。

- (ngSubmit)：来自NgForm指令。
- onSubmit()：将会在我们的组件类中进行定义（稍后）。
- f.value： f就是我们前面提到的FormGroup，而.value会以键值对的形式返回FormGroup中所有控件的值

总结起来，这行代码的意思是：“当我提交表单时，将会以该表单的值作为参数，调用组件实例上的onSubmit方法。”

2. input和NgModel

- class="ui form"和class="field"是两个可选的类。它们来自CSS框Semantic UI。它们并不属于Angular的范畴，在这里加上它们只是为了让本例子好看一些。
- label标签的for属性和input标签的id属性是一致的，这依据的是W3C标准。
- 我们设置SKU控件的placeholder属性，将其作为input值为空时给用户的提示。

NgModel指令指定的selector是ngModel。这意味着我们可以通过添加这个属性把它附加到input标签上： ngModel="whatever"。在这里我们指定了一个不带属性值的ngModel.

有两种不同的方法能在模板中指定ngModel，这里是第一种。当使用不带属性值的ngModel时，我们是要指定：

- (1) 单向数据绑定；
- (2) 希望在表单中创建一个名为sku的FormControl（这个sku来自input标签上的name属性）。

NgModel会创建一个新的FormControl对象， 把它自动添加到父FormGroup上（这里也就是form表单对象），并把这个FormControl对象绑定到一个DOM上。也就是说，它会在视图中的input标签和FormControl对象之间建立关联。这种关联是通过name属性建立的，在本例中是"sku"。

### 组件定义类

```ts
export class DemoFormSku {
  onSubmit(form: any): void {
    console.log('you submitted value:', form);
  }
}
```

## 使用FormBuilder

使用ngForm和ngControl隐式构建FormControl和FormGroup确实很方便，但无法为我们提供更多定制化选项。使用FormBuilder构建表单则是一种更为灵活和通用的方式。

FormBuilder是一个名副其实的表单构建助手。你应该还记得，表单是由FormControl和FormGroup构成的，而FormBuilder则可以帮助我们创建它们（你可以把它看作一个“工厂”对象）。

## 响应式表单FormBuilder

我们将使用formGroup和formControl指令来构建这个组件，这意味着我们需要导入相应的类。导入的代码如下所示。

```ts
import {
  FormBuilder,
  FormGroup
} from '@angular/forms';

@Component({
  selector: 'app-demo-form-sku-with-builder',
```

### 注入FormBuilder

通过在组件类上声明带参数的constructor，我们注入了一个FormBuilder：

```ts
export class DemoFormSkuBuilder {
  myForm: FormGroup;
  constructor(fb: FormBuilder) {
    this.myForm = fb.group({
    'sku': ['ABC123']
  });
  }
  onSubmit(value: string): void {
    console.log('you submitted value: ', value);
  }
}
```

在这期间，Angular将会注入一个从FormBuilder类创建的对象实例，并把它赋值给fb变量（来自构造函数）。

我们将会使用FormBuilder中的两个主要函数：

- control,用于创建一个新的FormControl
- group，用于创建一个新的FormGroup

### 在视图中使用myForm(FormBuilder)

Angular提供了另一个指令，能让我们使用现有的FormGroup。它叫作formGroup，可以这样使用:

```html
<h2 class="ui header">Demo Form: Sku with Builder</h2>
<form [formGroup]="myForm"
```

>我们说过，当使用FormsModule时， NgForm会自动应用于<form>元素上。但其实有一个例外： NgForm不会应用到带formGroup属性的<form>节点上。你也许不明白原因，这是因为NgForm的selector是：`form:not([ngNoForm]):not([formGroup]),ngForm,[ngForm]`

我们还需要把onSubmit中的f替换为myForm，因为现在的myForm变量中保存着表单的配置和值。

想让程序运行起来，还要做最后一件事：将我们的FormControl绑定到input标签上。记住，ngControl会创建一个新的FormControl对象，并附加到父FormGroup中.

```html
<div class="field">
      <label for="skuInput">SKU</label>
      <input type="text"
             id="skuInput"
             placeholder="SKU with builder"
             [formControl]="myForm.controls['sku']">
    </div>
```

在这里 ，我们将 input 标签上的 formControl 指令指向了 myForm.controls 上现有的FormControl控件sku

你需要记住以下两点。

如果想隐式创建新的FormGroup和FormControl，使用：

- ngForm
- ngModel

如果要绑定一个现有的FormGroup和FormControl，使用：

- formGroup
- formControl

## 添加验证

验证器由Validators模块提供。 Validators.required是最简单的验证，表明指定的字段是必填项，否则就认为这个FormControl是无效.想使用验证器，我们得做两件事：

- (1) 为FormControl对象指定一个验证器；
- (2) 在视图中检查验证器的状态，并据此采取行动。

要为 FormControl 对象分配一个验证器 ， 我们可以直接把它作为第二个参数传给FormControl的构造函数。

```ts
let control = new FormControl('sku', Validators.required)
```

也可以通过如下语法使用FormBuilder：

```ts
constructor(fb: FormBuilder) {
  this.myForm = fb.group({
    'sku': ['', Validators.required]
  });

  this.sku = this.myForm.controls['sku'];
}
```

现在要在视图中使用验证了。在视图中访问验证的值有以下两种方法。

- (1) 我们可以显式地把sku这个FormControl赋值给类的实例变量。这有点啰嗦，但便于我们在视图中访问这个FormControl 。
- (2) 我们也可以在myForm中查找sku这个FormControl。这样能简化组件类中的工作，但在视图中会稍微麻烦些。

### 显式地把sku设置为实例变量

在视图中，处理单个FormControls的最灵活的方式是将每个FormControl都定义在组件类上：

```ts
export class DemoFormWithValidationsExplicitComponent {
  myForm: FormGroup;
  sku: AbstractControl;

  constructor(fb: FormBuilder) {
    this.myForm = fb.group({
      'sku':  ['', Validators.required]
    });

    this.sku = this.myForm.controls['sku'];
  }

  onSubmit(value: string): void {
    console.log('you submitted value: ', value);
  }

}
```

- (1) 我们在类的顶部设置sku: AbstractControl；
- (2) 我们把用FormBuilder创建的myForm赋值给this.sku变量。

非常好，这意味着我们可以在组件视图中到处引用sku了。不过这样做有一个缺点：我们不得不为表单中的每个字段定义一个实例变量。对大型表单而言，这会显得相当啰嗦。

现在我们的sku可以得到验证了。我们要以四种不同的方式把它用在视图中：

- (1) 检查整个表单的有效性并显示一条错误信息；
- (2) 检查单个字段的有效性并显示一条错误信息；
- (3) 检查单个字段的有效性，当字段无效时将字段显示为红色；
- (4) 检查单个字段在特定规则下的有效性并显示一条错误信息

1. 表单信息

我们可以通过myForm.valid来检查整个表单的有效性。

```html
<div *ngIf="!sku.valid"
  class="ui error message">SKU is invalid</div>
```

2. 字段信息

当字段的FormControl无效时，我们也可以为该字段显示一条错误信息。

```html
<div *ngIf="sku.hasError('required')"
  class="ui error message">SKU is required</div>
```

3. 字段着色

当给`<div class= "field">`节点加上CSS类error时，这个输入框就会带有红色的边框.我们可以使用这种“属性语法”来有条件地设置这个CSS类。

```html
<div class="field"
    [class.error]="!sku.valid && sku.touched">
```

注意，这里我们为.error类设置了两个条件：检查!sku.valid和sku.touched。这是因为我们希望只有当用户修改过表单后（touched）才显示错误状态。

4. 特定验证

我们可以用hasError方法来检查特定的验证失败。

```html
<div *ngIf="sku.hasError('required')"
  class="ui error message">SKU is required</div>
```

注意， FormControl和FormGroup都定义了hasError方法。这意味着我们可以给它传入第二个参数path来在FormGroup中查询特定的字段。比如可以这样写：

```html
<div *ngIf="myForm.hasError('required', 'sku')"
  class="error">SKU is required</div>
```

5. 整合

```ts
import { Component } from '@angular/core';
import {
  FormBuilder,
  FormGroup,
  Validators,
  AbstractControl
} from '@angular/forms';

@Component({
  selector: 'app-demo-form-with-validations-explicit',
  templateUrl: './demo-form-with-validations-explicit.component.html',
  styles: []
})
export class DemoFormWithValidationsExplicitComponent {
  myForm: FormGroup;
  sku: AbstractControl;

  constructor(fb: FormBuilder) {
    this.myForm = fb.group({
      'sku':  ['', Validators.required]
    });

    this.sku = this.myForm.controls['sku'];
  }

  onSubmit(value: string): void {
    console.log('you submitted value: ', value);
  }

}
```

6. 移除sku实例变量

在上面的例子中，我们将sku: AbstractControl设置为一个实例变量。通常，我们不希望为每一个AbstractControl控件都创建一个实例变量。在没有实例变量的情况下，我们该如何在视图中引用FormControl呢？我们可以改用myForm.controls属性

```html
<input type="text"
    id="skuInput"
    placeholder="SKU"
    [formControl]="myForm.controls['sku']">
<div *ngIf="!myForm.controls['sku'].valid"
  class="ui error message">SKU is invalid</div>
<div *ngIf="myForm.controls['sku'].hasError('required')"
  class="ui error message">SKU is required</div>
```

### 自定义验证器

要明白如何实现自己的验证器，不妨看看Angular源代码中是如何实现Validators.required的：

```ts
export class Validators {
  static required(c: FormControl): StringMap<string, boolean> {
    return isBlank(c.value) || c.value == "" ? {"required": true} : null;
}
```

一个验证器：

- 接收一个FormControl作为输入；
- 当验证失败时，会返回一个`StringMap<string, boolean>`对象，它的键是“错误代码”，值是true。

1. 编写验证器

假设我们的sku有特殊的验证需求， 比如sku必须以123作为开始。我们写的验证器是这样的：

```ts
function skuValidator(control: FormControl): { [s: string]: boolean } {
  if (!control.value.match(/^123/)) {
    return {invalidSku: true};
  }
}
```

当输入值（控件的值control.value）不是以123作为开始时，验证器会返回错误代码invalidSku。

2. 给FormControl分配验证器

现在要为FormControl添加验证，但是有一个小问题： sku已经有一个验证器了，怎样才能在同一个字段上添加多个验证器呢？我们可以用`Validators.compose`来实现:

```ts
constructor(fb: FormBuilder) {
  this.myForm = fb.group({
    'sku': ['', Validators.compose([
      Validators.required, skuValidator])]
  });
```

Validators.compose把两个验证器包装在一起，我们可以将其赋值给FormControl。只有当两个验证都合法时， FormControl才是合法的。

现在就能在视图中使用这个新的验证器了:

```html
<div *ngIf="sku.hasError('invalidSku')"
  class="ui error message">SKU must begin with <span>123</span></div>
```

## 监听变化

FormGroup和FormControl都带有`EventEmitter`（事件发射器），我们可以通过它来观察变化。

想监听控件的变化，我们要：

- (1) 通过调用control.valueChanges访问到这个EventEmitter；
- (2) 然后使用.subscribe方法添加一个监听器。

```ts
constructor(fb: FormBuilder) {
    this.myForm = fb.group({
      'sku':  ['', Validators.required]
    });

    this.sku = this.myForm.controls['sku'];

    this.sku.valueChanges.subscribe(
      (value: string) => {
        console.log('sku changed to:', value);
      }
    );

    this.myForm.valueChanges.subscribe(
      (form: any) => {
        console.log('form changed to:', form);
      }
    );

  }
```

## ngModel

ngModel是一个特殊的指令，它将模型绑定到表单中。 ngModel的特殊之处在于它实现了 **双向绑定** 。相对于单向绑定来说，双向绑定更加复杂和难以推断。 Angular通常的数据流向是单向的：自顶向下。但对于表单来说，双向绑定有时会更容易。

下面对表单稍作修改：我们希望能输入产品名称productName。这次要用ngModel来保持组件实例和视图的同步:

```ts
export class DemoFormNgModelComponent {
  myForm: FormGroup;
  productName: string;

  constructor(fb: FormBuilder) {
    this.myForm = fb.group({
      'productName':  ['', Validators.required]
    });
  }

  onSubmit(value: string): void {
    console.log('you submitted value: ', value);
  }
}
```

注意，我们只是简单地将productName: string存成了实例变量。紧接着，我们在input标签上使用ngModel:

```html
<label for="productNameInput">Product Name</label>
      <input type="text"
             id="productNameInput"
             placeholder="Product Name"
             [formControl]="myForm.get('productName')"
             [(ngModel)]="productName">
```

注意，这里ngModel的语法很有意思：我们在ngModel属性上同时使用了()和[]。我们既使用了表示输入属性（@Input）的方括号[]，又使用了表示输出属性（@Output）的圆括号()，这就是双向绑定的标志.

另外还需要注意的是 ： 我们仍然用 formControl 指定此 input 应该绑定到表 上 的FormControl。这是因为ngModel只负责将input绑定到对象实例上，但FormControl的功能是与此独立的。由于我们还需要对这个值加以验证并把它作为表单的一部分提交上去，仍要保留formControl指令。

最后，我们把产品名称productName值显示在视图中:

```ts
<div class="ui info message">
  The product name is: {{productName}}
</div>
```

## 总结

只要我们掌握了如何使用FormGroup、FormControl和Validation，它就变得非常容易了！