# icon-font

## 发展历程

**图片**  
大部分图标都是用 `img` ① 来实现的，结果是图片占了请求的大部分。所以为了优化请求，有了 `image sprite` ② 雪碧图，将多个图片合成一个图片，然后利用 css 的 `background-position` 定位显示不同的 icon 图标。但这个也有一个很大的痛点，维护困难、每新增一个图标，都需要改动原始图片，而且一修改雪碧图，图片缓存就失效了。

**font 库** ③  
如 `Font Awesome`，但  不方面查找和定制化。

**iconfont** ④  
阿里开源图库

## iconfont 三种使用方式

### unicode

**优势**

- 兼容性最好，支持 ie6+
- 支持按字体的方式去动态调整图标大小，颜色等等

**劣势**

- 不支持多色图标
- 在不同的设备浏览器字体的渲染会略有差别，在不同的浏览器或系统中对文字的渲染不同，其显示的位置和大小可能会受到 font-size、line-height、word-spacing 等 CSS 属性的影响，而且这种影响调整起来较为困难
- 书写不直观，语意不明确

**使用方法**

- 第一步：引入自定义字体 `font-face`

```css
@font-face {
  font-family: "iconfont";
  src: url("iconfont.eot"); /* IE9*/
  src: url("iconfont.eot#iefix") format("embedded-opentype"), /* IE6-IE8 */
       url("iconfont.woff") format("woff"), /* chrome, firefox */
       url("iconfont.ttf") format("truetype"), /* chrome, firefox, opera, Safari, Android, iOS 4.2+*/
       url("iconfont.svg#iconfont") format("svg"); /* iOS 4.1- */
}

.iconfont {
  font-family:"iconfont" !important;
  font-size:16px;
  font-style:normal;
  -webkit-font-smoothing: antialiased;
  -webkit-text-stroke-width: 0.2px;
  -moz-osx-font-smoothing: grayscale;
}
```

- 第二步：挑选相应图标并获取字体编码，应用于页面

```html
<i class="iconfont">&#xe604;</i>
```

**原理**  

> 通过 `@font-face` 引入自定义字体文件，它里面规定了 `unicode` 编码对应的形状。

---

## font-class

**优势**

- 兼容性良好，支持ie8+
- 相比于unicode语意明确，书写更直观


**使用方法**

- 第一步：引入自定义字体 `font-face`

```css
@font-face {
  font-family: "iconfont";
  src: url("iconfont.eot"); /* IE9*/
  src: url("iconfont.eot#iefix") format("embedded-opentype"), /* IE6-IE8 */
       url("iconfont.woff") format("woff"), /* chrome, firefox */
       url("iconfont.ttf") format("truetype"), /* chrome, firefox, opera, Safari, Android, iOS 4.2+*/
       url("iconfont.svg#iconfont") format("svg"); /* iOS 4.1- */
}

.iconfont {
  font-family:"iconfont" !important;
  font-size:16px;
  font-style:normal;
  -webkit-font-smoothing: antialiased;
  -webkit-text-stroke-width: 0.2px;
  -moz-osx-font-smoothing: grayscale;
}

/* 这里注意 ⚠️ */
.icon-adobe:before { content: "\ea01"; }
```

- 第二步：挑选相应图标并获取类名，应用于页面

```html
<i class="iconfont icon-adobe"></i>
```

**原理**  

> 其实是和 unicode 一样的，它只是 **多做了一步**，将原先 `&#xe604` 这种unicode写法换成了 `className`，它在每个 class 的 `before` 属性中写了unicode，省去了手动书写的麻烦。

----

## symbol

- 第一步：引入 `svg symbol`

```xml
<svg xmlns="http://www.w3.org/2000/svg" xlink="http://www.w3.org/1999/xlink" width="0" height="0" style="display:none;">
    <symbol viewBox="0 0 20 20" id="icon-adobe">
        <path fill="#555" fill-rule="evenodd" d="M12.6072222,1 L20,1 L20,19.0010671 L12.6072222,1 Z M-1.68220993e-12,1 L7.39861111,1 L-1.68220993e-12,19.0010671 L-1.68220993e-12,1 Z M6.77027778,15.5814318 L10.0027778,7.26488497 L14.7138889,19 L11.6269444,19 L10.2172222,15.5814318 L6.77027778,15.5814318 Z"/>
    </symbol>
</svg>
```

- 第二步：引入css代码

```css
.icon {
    width: 1em; height: 1em;
    vertical-align: -0.15em;
    fill: #FF0000;
    overflow: hidden;
}
```

- 第三步：挑选相应图标并获取id名

```html
<svg class="icon" aria-hidden="true">
    <use xlink:href="#icon-adobe"></use>
</svg>
```

⚠️ 注意

在 `第一步` 引入 `svg symbol` 当中，还可以通过 `第三步` 中的 `href` 属性加载

```html
<use xlink:href="svg-url#icon-adobe"></use>
```

**优势**

- 支持多色图标了
- 支持像字体那样通过font-size,color来调整样式
- 支持 ie9+
- 可利用CSS实现动画
- 减少HTTP请求。
- 矢量，缩放不失真
- 可以很精细的控制SVG图标的每一部分

----

## Unicode 编码区域

我们知道字体的 `Unicode` 是一个编码形式，那么这个编码是怎么生成的呢？  
需要知道，计算机里面每个字符都有一个unicode编码，通常会用 `u+` 然后紧接着一组十六进制的数字来表示这一个字符，例如 `U+4AE0`，unicode字符集里面，`E000 至 F8FF` 属于用户造字区，原本是空的，用户可以在字体文件里面随便定义这些字符的形状，如 `字体Unicode`。

## 如何使用SVG生成字体

首先我们知道的是，字体的常用使用方式有以上的三种，而前两种都需要生成 `unicode`，而编码是从 `0xe000`开始的，
- ① 因此我们字体编码就可以从 `0xe000`累加，即 `0xe000++`
- ② 然后获取字符编码 `String.fromCharCode(0xe000)`
```js
String.fromCharCode(0xe000)
""
```
- ③ 获取编码后，就可以生成对应的Unicode字体了

### unicode 方式使用

```js
<i class="icon"></i> 
```

⚠️ 定义使用的字体，很重要啦！！

```css
@font-face {
  font-family: "iconfont";
  src: url("iconfont.eot"); /* IE9*/
  src: url("iconfont.eot#iefix") format("embedded-opentype"), /* IE6-IE8 */
       url("iconfont.woff") format("woff"), /* chrome, firefox */
       url("iconfont.ttf") format("truetype"), /* chrome, firefox, opera, Safari, Android, iOS 4.2+*/
       url("iconfont.svg#iconfont") format("svg"); /* iOS 4.1- */
}

.icon {
  font-family:"iconfont" !important;
}
```

### font-class 使用方式

```js
<i class="icon icon-adobe"></i>
```

```css
@font-face {
  font-family: "iconfont";
  src: url("iconfont.eot"); /* IE9*/
  src: url("iconfont.eot#iefix") format("embedded-opentype"), /* IE6-IE8 */
       url("iconfont.woff") format("woff"), /* chrome, firefox */
       url("iconfont.ttf") format("truetype"), /* chrome, firefox, opera, Safari, Android, iOS 4.2+*/
       url("iconfont.svg#iconfont") format("svg"); /* iOS 4.1- */
}

.icon {
  font-family:"iconfont" !important;
}
.icon-adobe:before { content: "\e000"; }
```

- ④ 有了这些编码，就可以生成字体文件了

生成字体文件我们需要几个字体转换工具

```js
`svgicons2svgfont` -> `svg2ttf` -> `ttf2eot`  
                                -> `ttf2woff`  
                                -> `ttf2woff2`  
```

通过这几个工具我们就能轻松的将 `svg` 图转换为不同格式的字体文件

字体转换工具：[svgtofont](https://github.com/jaywcjlove/svgtofont) 👍

## 参考

- [手摸手，带你优雅的使用 icon](https://juejin.im/post/59bb864b5188257e7a427c09)
- [css中icon font 的每一个icon的编码是怎么来的](https://zhidao.baidu.com/question/204596322270131765.html)
- [怎么查某个汉字对应的 Unicode码？](https://zhidao.baidu.com/question/133757489.html)
- [Private Use Areas](https://en.wikipedia.org/wiki/Private_Use_Areas)
- [svgtofont](https://github.com/jaywcjlove/svgtofont)

