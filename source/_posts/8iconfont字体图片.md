---
title: iconfont图片使用技巧
date: 2017-04-15 
---
### font-class引用

------

font-class是unicode使用方式的一种变种，主要是解决unicode书写不直观，语意不明确的问题。

与unicode使用方式相比，具有如下特点：

##### 1.兼容性良好，支持ie8+，及所有现代浏览器。
##### 2.相比于unicode语意明确，书写更直观。可以很容易分辨这个icon是什么。
##### 3.因为使用class来定义图标，所以当要替换图标时，只需要修改class里面的unicode引用。
##### 4.不过因为本质上还是使用的字体，所以多色图标还是不支持的。

使用步骤如下：

#### 第一步：引入项目下面生成的fontclass代码：

```html
  <link rel="stylesheet" type="text/css" href="./iconfont.css">
```

#### 第二步：挑选相应图标并获取类名，应用于页面：

```html
  <i class="iconfont icon-nav-xxx"></i>
```

*"iconfont"是你项目下的font-family。可以通过编辑项目查看，默认是"iconfont"。*

## symbol引用

------

这是一种全新的使用方式，应该说这才是未来的主流，也是平台目前推荐的用法。相关介绍可以参考这篇文章 这种用法其实是做了一个svg的集合，与另外两种相比具有如下特点：

##### 1.支持多色图标了，不再受单色限制。
##### 2.通过一些技巧，支持像字体那样，通过font-size,color来调整样式。
##### 3.兼容性较差，支持 ie9+,及现代浏览器。
##### 4.浏览器渲染svg的性能一般，还不如png。

##### 使用步骤如下：

#### 第一步：引入项目下面生成的symbol代码：

```html
    <script src="./iconfont.js"></script>
```

#### 第二步：加入通用css代码（引入一次就行）：

```html
    <style type="text/css">
      .icon {
         width: 1em; height: 1em;
         vertical-align: -0.15em;
         fill: currentColor;
         overflow: hidden;
      }
    </style>
```
#### 第三步：挑选相应图标并获取类名，应用于页面：

```html
    <style type="text/css">
      <svg class="icon" aria-hidden="true">
        <use xlink:href="#icon-nav-xxx"></use>
      </svg>
    </style>
```

## unicode引用

------

unicode是字体在网页端最原始的应用方式，特点是：

##### 1.兼容性最好，支持ie6+，及所有现代浏览器。
##### 2.支持按字体的方式去动态调整图标大小，颜色等等。
##### 3.但是因为是字体，所以不支持多色。只能使用平台里单色的图标，就算项目里有多色图标也会自动去色。

*注意：新版iconfont支持多色图标，这些多色图标在unicode模式下将不能使用，如果有需求建议使用symbol的引用方式*

##### unicode使用步骤如下：

#### 第一步：拷贝项目下面生成的font-face

```html
    @font-face {
        font-family: 'iconfont';
        src: url('iconfont.eot');
        src: url('iconfont.eot?#iefix') format('embedded-opentype'),
        url('iconfont.woff') format('woff'),
        url('iconfont.ttf') format('truetype'),
        url('iconfont.svg#iconfont') format('svg');
    }
```

#### 第二步：定义使用iconfont的样式

```html
    .iconfont{
        font-family:"iconfont" !important;
        font-size:16px;font-style:normal;
        -webkit-font-smoothing: antialiased;
        -webkit-text-stroke-width: 0.2px;
        -moz-osx-font-smoothing: grayscale;
    }
```
#### 第三步：挑选相应图标并获取字体编码，应用于页面

```html
    <i class="iconfont">&#x33;</i>
```

*"iconfont"是你项目下的font-family。可以通过编辑项目查看，默认是"iconfont"。*