---
layout: post
title: JavaScript模块模式(翻译）
author: 聪记
header-img: img/post_header_universe.jpg
tags: 
    - JavaScript
    - Markdown
---

## Mastering the Module Pattern

翻译文章[Mastering the Module Pattern](https://toddmotto.com/mastering-the-module-pattern/)，Module Pattern是一种非常常见的JS代码模式，尤其是各种类库中被广泛应用。  

再次推荐原文地址：[Mastering the Module Pattern](https://toddmotto.com/mastering-the-module-pattern/)  


## 掌握模块模式

我是JavaScript模块模式(Module Pattern)的狂热粉丝，我想与大家分享一些模块模式的用例以及特点，并解释它们的重要性。模板模式是一种“[设计模式](http://code.tutsplus.com/tutorials/understanding-design-patterns-in-javascript--net-25930)”，从各种方面看它都非常有用。模板模式(以及它的变种Revealing Module Pattern)最吸引我的一点是它可以轻而易举的划分程序的作用域并且不会使程序设计变得过于复杂。  

它还能使程序非常简单、易读易用，并且非常优雅的使用对象，不会使你的程序充满了重复的`this`和`prototype`声明。下面我将分享一些模块模式的优点、如何利用该模式，以及它的变种和特性。  

### 创建模块

为了理解模块的概念，首先应该理解下面的`function`代码：

```
function () {
  // code
})();
```

这段代码声明了一个函数，然后立即调用自身。这也被称为[Immediately-Invoked-Function-Expressions](http://benalman.com/news/2010/11/immediately-invoked-function-expression)，在这里`function`创建了一个新的作用域(`scope`)以及私有空间(`privacy`)。Javascript并没有私有访问权限，但当创建了一个包含函数逻辑的作用域时，我们模拟了私有作用域的概念。也就是只暴露我们需要的部分，而其他代码对全局隐藏。  

创建新的作用域后，我们需要给代码赋予命名空间(namespace)，以便后续可以访问我们返回的方法。下面就为我们的匿名模块增加命名空间：

```
var Module = (function () {
  // code
})();
```

在全局声明`Module`变量，也就是说接下来可以在任何地方调用Module，甚至可以将Module传递给其他模块。

### 私有方法

接下来你会看到很多Javascript的`private`方法。严格说来Javascript并没有`private`方法，但我们可以创建等同的结果。  


> What _are_ private methods you might be asking? Private methods are anything you don't want users/devs/hackers to be able to see/call outside the scope they're in. We might be making server calls and posting sensitive data, we _don't_ want to expose those functions publicly, they could post anything back then and take advantage of our code. 

所以我们可以创建闭包（Javascript中最好的方式）来更好的保护我们的代码。做这些**并不都是为了**保护代码，还有命名冲突问题。我敢打赌在你刚开始写Jquery/Javascript时，你肯定在一个文件里扔进了大量的`function, function, function`。然而很少有人意识到这些方法的作用域都是全局的，也许在将来某个点你会遭到影响。如果是这样，你将会了解为什么，以及如何去改变它。  

接下来我们使用新创建的`Module`作用域来使我们的方法在作用域外不可访问。对于模块模式的初学者来说，这个例子将会帮助你了解如何来定义一个私有方法:  

```
var Module = (function () {
  
  var privateMethod = function () {
    // do something
  };

})();
```

上面的例子声明了方法`privateMethod`，它是在新的作用域内本地声明的。如果试图在模块外部调用它将会抛出Error。我们并不想让其他人调用这个方法，尤其是那些可以操作数据并且与服务器交互的地方。  

### 理解"return"

通常典型的模块都会使用`return`并且返回一个`Object`，通过这个模块的命名空间来访问的方法都绑定在这个Object上。  

下面是一个带有`function`成员的返回`Object`的例子:

```
var Module = (function () {
  
  return {
    publicMethod: function () {
      // code
    }
  };

})();
```

我们可以像对待一般的Object一样来调用Module：

```
Module.publicMethod();
```

相比较而言，一个标准的对象看起来像这样：

```
var myObjLiteral = {
  defaults: { name: 'Todd' },
  someMethod: function () {
    console.log(this.defaults);
  }
};

// console.log: Object { name: 'Todd' }
myObjLiteral.someMethod();
```

但这样的问题是这种模式容易被滥用。那些本该是私有的方法也会被调用者使用。这就是模块(Module)出现的原因，模块允许我们局部定义私有方法并且只返回"有用的部分"。

下面来看看对象字面量(Object Literal)语法，以及一个非常好的模板模式的例子，并且理解`return`关键字的作用。通常一个模块会返回一个对象，但是如何定义和构造这个对象完全取决于你。根据项目以及代码的作用，以下几种都可能被用到。

#### 匿名对象字面量(Anonymous Object Literal)

就像上面的例子那样，这是最简单的一种方式，对象没有本地命名，我们只是返回一个对象:

```
var Module = (function () {

  var privateMethod = function () {};
  
  return {
    publicMethodOne: function () {
      // I can call `privateMethod()` you know...
    },
    publicMethodTwo: function () {

    },
    publicMethodThree: function () {

    }
  };

})();
```

#### 局部对象字面量(Locally scoped Object Literal)

局部作用域(Local scope)指的是在一个作用域内声明的变量或者方法。在[Conditionizr](//conditionizr.com)项目中，我们使用了一个超过100行的局部命名空间，这能很方便的看出public和private方法而不必检查`return`语句。在这种情况下，因为public方法都有本地变量定义，所以很容易辨别。

```
var Module = (function () {

  // locally scoped Object
  var myObject = {};

  // declared with `var`, must be "private"
  var privateMethod = function () {};

  myObject.someMethod = function () {
    // take it away Mr. Public Method
  };
  
  return myObject;

})();
```

模块最后一行返回`myObject`。我们的全局`Module`并不关心本地的局部`Object`是否有名字，我们只拿到返回的对象而不是命名。这使得代码可以更好的管理。

#### Stacked locally scoped Object Literal

这与前面的例子非常相似，但这里使用了一个更“传统”地对象字面量表达式：

```
var Module = (function () {

  var privateMethod = function () {};

  var myObject = {
    someMethod:  function () {

    },
    anotherMethod:  function () {
      
    }
  };
  
  return myObject;

})();
```

我个人更喜欢第二种方式。因为在这里我们必须在使用它们之前定义其他方法（你应该这么做，因为使用`function myFunction () {}`这样的语法可能会[提升](https://developer.mozilla.org/zh-CN/docs/Glossary/Hoisting)一个方法，如果使用不当可能会引入很多问题）。使用`var myFunction = function () {};`这样的语法可以不必担心这种情况，因为我们在使用它们之前定义所有方法，这也让调试更简单，因为JavaScript解释器可以按照声明的顺序加载代码。这种方式也有不好的一面，因为"嵌套"方法使代码看起来很冗余，同时也没有显而易见的本地变量`Object namespace`来绑定公用方法。

#### Revealing Module Pattern

我们已经研究过模块，它确实包含一个简洁的的变量可以让我们通过在模块内向它绑定方法来公开这些方法。同样的，我们可以用更好的代码组织方式来清楚的看到模块中的哪些方法是公共的:

```
var Module = (function () {

  var privateMethod = function () {
    // private
  };

  var someMethod = function () {
    // public
  };

  var anotherMethod = function () {
    // public
  };
  
  return {
    someMethod: someMethod,
    anotherMethod: anotherMethod
  };

})();
```

我非常喜欢上面的语法，它非常的有说明性。对于一些较大的JavaScript模块，这种方式非常有用，使用标准的模板模式可能因为语法及组织代码的方式造成代码失控。

#### 访问“私有”方法

你也许会想，“我该怎么访问私有方法？”。这就是JavaScript变得更棒的地方，我们可以通过调用公用方法来实际调用私有方法。观察如下代码:

```
var Module = (function () {

  var privateMethod = function (message) {
    console.log(message);
  };

  var publicMethod = function (text) {
    privateMethod(text);
  };
  
  return {
    publicMethod: publicMethod
  };

})();

// Example of passing data into a private method
// the private method will then `console.log()` 'Hello!'
Module.publicMethod('Hello!');
```

并不只局限于方法，还可以访问对象、数组，任何东西：

```
var Module = (function () {

  var privateArray = [];

  var publicMethod = function (somethingOfInterest) {
    privateArray.push(somethingOfInterest);
  };
  
  return {
    publicMethod: publicMethod
  };

})();
```

### 增强模块(Augmenting Modules)

到目前为止我们已经创建了一个很好的模块，并且返回一个对象。但是如果我们想扩展我们的模块，并且引入另一个较小的模块来扩展我们的原始模块呢？  
考虑下面代码：

```
var Module = (function () {

  var privateMethod = function () {
    // private
  };

  var someMethod = function () {
    // public
  };

  var anotherMethod = function () {
    // public
  };
  
  return {
    someMethod: someMethod,
    anotherMethod: anotherMethod
  };

})();
```

如果这是我们应用的一部分，但是设计时我们已经决定将某些部分排除在应用核心之外，所以我们可以把它作为独立的模块引入，实现扩展。  
目前为止我们的`Module`对象看起来是这样：

```
Object {someMethod: function, anotherMethod: function}
```

但是如果我们想扩展我们的模块，增加其他的公共方法，也许代码编程这样：  

```
Object {someMethod: function, anotherMethod: function, extension: function}
```

我们如何管理它呢？我们给它个恰当的命名`ModuleTwo`，并且传入我们的`Module`，它使我们能够扩展Object:

```
var ModuleTwo = (function (Module) {
    
    // access to `Module`
    
})(Module);
```

接下来我们可以在这个模块里创建`another`方法, 同时还能充分利用已有的私有、功能特性。我的伪代码也许看起来是这样：  

```
var ModuleTwo = (function (Module) {
    
    Module.extension = function () {
        // another method!
    };
    
    return Module;
    
})(Module || {});
```

`Module`被传入`ModuleTwo`，增加一个扩展方法并再次返回`Module`。我们的对象就被跑出了，这正是JavaScript的灵活性:D。  
现在可以通过Chome开发者工具看到最初的Module现在已经有了第三个属性：  

```
// Object {someMethod: function, anotherMethod: function, extension: function}
console.log(Module);
```

这里有条额外的提示，你也许注意到了我传入`Module || {}`到第二个`ModuleTwo`，这是为了防止`Module`是`undefined`，现在就不会引起异常了。这就是初始化了一个新的对象，并且绑定我们的`extension`方法，再return它。  


### 私有命名习惯(Private Naming Conventions)

我个人喜欢Revealing Module Pattern，这就会在代码里出现很多相同的声明。那我如何区分私有的变量或方法呢？使用**_**符号！你也许在网上已经看到很多这样的符号，现在你知道为什么我们这样用了：  

```
var Module = (function () {

  var _privateMethod = function () {
    // private stuff
  };

  var publicMethod = function () {
    _privateMethod();
  };
  
  return {
    publicMethod: publicMethod
  };

})();
```
