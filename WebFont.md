## 网络字体加载器
[项目地址](https://github.com/typekit/webfontloader)   
网络字体加载器让你在使用`@font-face`加载远程字体时拥有更多的控制性，它提供了一个通用接口来加载字体而不用你关注字体的资源文件，还添加了一些标准事件，你可能会用来控制字体的加载。它可以用来加载来自`Google Fonts`、`Typekit`、`Fonts.com`和`Fontdect`上的字体，也可以用来加载自己服务器上的字体，它由Google和Typekit合作开发。

### 快速开始
要使用网络字体加载器，只要把它引入你的页面中然后告诉它你要加载哪个字体。例如，你可以使用谷歌代码库托管网站上的托管的网络字体加载器，然后从`Google Fonts`上加载字体就像下面这样：

``` html
<script src="https://ajax.googleapis.com/ajax/libs/webfont/1.6.26/webfont.js"></script>
<script>
  WebFont.load({
    google: {
      families: ['Droid Sans', 'Droid Serif']
    }
  });
</script>
```

或者，你也可以通过`https://ajax.googleapis.com/ajax/libs/webfont/1/webfont.js`使用最新的`1.x`版本。注意这个url中的版本是不够明确的，它总会加载最新的`1.x`版本，不过它也会有短暂的缓存时间来确定你的页面被一种及时的方式更新。由于性能原因，我们推荐使用一个确切的版本号（比如1.6.26）在你的url里当你在产品中使用网络加载器时。你可以手动更新网络加载器的版本号在url里当你想要使用一个新版本时。

网络加载器在`jsDelivr`和`CDNJS`这些提供cdn加速的网站中也可以获得。

你可以用异步的方式来使用网络加载器。例如，异步加载`Typekit`上的字体，你可以使用下面的代码：

``` html
<script>
   WebFontConfig = {
      typekit: { id: 'xxxxxx' }
   };

   (function(d) {
      var wf = d.createElement('script'), s = d.scripts[0];
      wf.src = 'https://ajax.googleapis.com/ajax/libs/webfont/1.6.26/webfont.js';
      wf.async = true;
      s.parentNode.insertBefore(wf, s);
   })(document);
</script>
```

异步地使用网络加载器可以避免在加载Javascipt时阻塞你的页面。注意如果你的script是异步加载的，页面剩下的部分可能会在网络加载器被加载和执行前渲染，这可能导致一个`Flash of Unstyled Text(FOUT)`现象。

FOUT可以很容易地避免当你使用异步的方式加载网络加载器时，因为它会自动设置`wf-loading`class到HTML元素上当`WebFont.load`被调用时。浏览器在加载剩下的内容之前会等待script的加载，这样FOUT就被避免了。

网络加载器也可以在npm上作为一个CommonJS的模块获得，只要使用`npm install webfontloader`然后在你的代码中引用：

``` javascript
var WebFont = require('webfontloader');

WebFont.load({
  google: {
    families: ['Droid Sans', 'Droid Serif']
  }
});
```

### 配置
网络加载器的配置需要定义在一个叫`WebFontConfig`的全局变量中，或者直接传入到`WebFont.load`方法中。它定义了需要从每个字体提供商加载哪些字体，并且提供了一些选项用来设置事件的回调。当使用了异步的方式，你必须在加载网络字体加载器的代码之前定义全局变量`WebFontConfig`（和上面的例子一样）。

#### 事件
网络字体加载器提供了一个给开发者设置钩子的事件系统。它在CSS和Javascript加载字体时都会给你通知。
- `loading` - 这个事件会被触发当所有的字体被请求时。
- `active` - 这个事件会被触发当字体被渲染时。
- `inactive` - 这个事件会被触发当浏览器不支持关联的字体或者没有字体可以被加载时。
- `fontloading` - 这个事件会被触发一旦每个字体被加载。
- `fontactive` - 这个事件会被触发一旦每个字体被渲染。
- `fontinactive` - 这个事件会被触发如果字体不能被加载。

CSS事件通过html元素上的class来实现，以下class会被设置到html元素上：

```
.wf-loading
.wf-active
.wf-inactive
.wf-<familyname>-<fvd>-loading
.wf-<familyname>-<fvd>-active
.wf-<familyname>-<fvd>-inactive
```
其中`<familyname>`占位符会被替换为每个字体名字的一个安全字符串。空格和下划线会从名字中移除，并且所有的字母都没转换为小写。例如，`Droid Sans`变成`droidsans`。`<fvd>`占位符是一个字体变形描述。它是一个描述指定字体style和weight的简写。下面是一些例子：
``` css
/* n4 */
@font-face { font-style: normal; font-weight: normal; }

/* i7 */
@font-face { font-style: italic; font-weight: bold; }
```

需要时刻记住，`font-weight: normal`和`font-weibht: 400`一样，`font-weight: bold`和`font-weight: 700`一样。如果没有style/weight被指定，默认`n4`（`font-style: normal; font-weight: normal;`）会被使用。

如果一个字体在一个页面上被加载多次，css class会继续更新当前最新的状态在页面上。无论什么时候只要有字体被加载全局的`wf-loading`就会被添加（即使其他字体已经是active或inactive的状态）。`wf-inactive`只有当页面上一个字体都没渲染时才添加，否则`wf-active`将被添加（即使有些字体的状态是inactive）。

Javascript事件通过`WebFontConfig`配置对象上的回调函数来实现。

``` javascript
WebFontConfig = {
  loading: function() {},
  active: function() {},
  inactive: function() {},
  fontloading: function(familyName, fvd) {},
  fontactive: function(familyName, fvd) {},
  fontinactive: function(familyName, fvd) {}
};
```

`fontloading`，`fontactive`和`fontinactive`回调函数会被传入字体名字和字体变种描述。

你也可以禁止在html元素上设置class通过配置`classes`参数为`false`（默认为`true`）

``` javascript
WebFontConfig = {
  classes: false
};
```

你也可以禁止事件回调通过设置`events`参数为`false`（默认为`true`）
``` javascript
WebFontConfig = {
  events: false
};
```

如果事件和class都被禁止，那么网络字体加载器不会监听字体的加载，而是只做往文档里插入@font-face的代码。

### 超时
由于网络并不是100%可靠的，一个字体加载失败是有可能的。5秒之后如果字体渲染失败`fontinactive`事件会被触发。如果至少一个字体被成功渲染，`active`事件会被触发，其他情况`inactive`事件会被触发。

你可以更改超时的时间通过设置`WebFontConfig`对象上的`timeout`选项。

``` javascript
WebFontConfig = {
  google: {
    families: ['Droid Sans']
  },
  timeout: 2000 // Set the timeout to two seconds
};
```

超时的时间的单位是毫秒，默认3秒如果没有被提供。

### iframes
通常的，引入一份网络加载器的拷贝到每个需要字体的窗口里很简单，所以每个窗口管理它们自己的字体。然而，如果你需要有一个单独的窗口用来管理每个用Javascript构建的同源的子窗口或者iframes，网络字体加载器也能支持。只要使用可选的`context`配置选项，并且给它一个用来加载的对象窗口的引用：

``` javascript
WebFontConfig = {
  google: {
    families: ['Droid Sans']
  },
  context: frames['my-child']
};
```

这是一个大多数情况下不需要被使用的高级选项。

## 模块
网络字体加载器提供了一个模块系统所以任何网络字体提供商可以贡献代码，这样允许他们的字体被加载。你可以一次使用多个字体提供商。这个库当前支持的字体提供商将被记录在下面。

（不翻译了，反正我大天朝用不了...）

### 自定义
从任何外部样式表中加载字体，请使用`custom`模块。这里你需要指定那些你想加载的字体名字，和有选择的设置那些包含@font-face声明字体的样式表的url。

你可以指定一个具体的字体变种或者一些列的字体变种，添加在字体名字后面，和字体用冒号分隔，多个字体变种之间通过逗号分隔。字体变种使用FVD标记来指定。

``` javascript
WebFontConfig = {
  custom: {
    families: ['My Font', 'My Other Font:n4,i4,n7'],
    urls: ['/fonts.css']
  }
};
```

在这个例子中，`fonts.css`文件可能看起来像这样：

``` css
@font-face {
  font-family: 'My Font';
  src: ...;
}
@font-face {
  font-family: 'My Other Font';
  font-style: normal;
  font-weight: normal; /* or 400 */
  src: ...;
}
@font-face {
  font-family: 'My Other Font';
  font-style: italic;
  font-weight: normal; /* or 400 */
  src: ...;
}
@font-face {
  font-family: 'My Other Font';
  font-style: normal;
  font-weight: bold; /* or 700 */
  src: ...;
}
```

如果你的字体已经被另一个样式表包含，那么你可以省掉`urls`数组并且只指定字体名字来开始加载字体。只要名字和声明在`families`数组中的名字匹配，对应的加载class名字就会被应用到html元素上。

``` html
<script src="https://ajax.googleapis.com/ajax/libs/webfont/1.5.10/webfont.js"></script>
<script>
  WebFont.load({
    custom: {
      families: ['My Font']
    }
  });
</script>

<style type="text/css">
  @font-face {
    font-family:"My Font";
    src:url("assets/fonts/my_font.woff") format("woff");
  }
</style>
```

自定义模块也支持定制用来决定是否一个字体加载已经加载完成的测试字符串。这在用来加载带有自定义子集或者图形的字体来说很有用。

``` javascript
WebFontConfig = {
  custom: {
    families: ['My Font'],
    testStrings: {
      'My Font': '\uE003\uE005'
    }
  }
};
```

测试字符需要被指定在每一个字体上并且至少包含一个字符，如果不指定的话默认测试字符（BESbswy）将被使用。

## 浏览器支持
每一个浏览器对`@font-face`都有不同的支持程度。网络字体加载器通过浏览器的UA字符串来判断支持。UA字符可能声称支持一个网络字体但实际上并不支持，尤其在移动浏览器上使用桌面模式时值得注意，在linux上经常被认为是Chrome。在这种情况下字体提供商可能决定发送WOFF字体给设备因为真实的桌面Chrome支持它，但是移动浏览器不支持。网络字体加载器并不处理这些情况因为它相信UA字符串是不会骗它的。网络字体提供商可以单独地在网络字体加载器提供的基础功能之上来处理这些特殊情况。

如果网络字体加载器决定当前浏览器不支持`@font-face`，那么`inactive`事件会被触发。

当从多个提供商上加载字体时，每个提供商可能也可能没有提供给浏览器。如果网络字体加载器决定当前浏览器支持`@font-face`，并且至少有一个提供商可以提供字体，那么这个提供商的字体就会被加载。当完成是，`active`事件会被触发。

从支持的提供商上加载字体，`fontactive`事件会被触发。从不支持当前浏览器的提供商上加载字体，`fontinactive`事件会被触发。

例如：
``` javascript
WebFontConfig = {
  providerA: 'Family1',
  providerB: 'Family2'
};
```

如果`providerA`可以提供字体给浏览器，但`providerB`不能，`fontinactive`事件会被触发在`Family2`上。`fontactive`事件会被触发在`Family1`上一旦它加载完成，`active`事件也是如此。