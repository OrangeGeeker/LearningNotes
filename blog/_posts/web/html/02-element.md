# 常用元素

- [常用元素](#常用元素)
  - [a 元素](#a-元素)
    - [href 属性](#href-属性)
    - [target 属性](#target-属性)
  - [图片元素 img](#图片元素-img)
    - [和 a 元素一起使用](#和-a-元素一起使用)
    - [点击图片区域跳转 map 元素](#点击图片区域跳转-map-元素)
    - [figure 元素](#figure-元素)
  - [多媒体元素](#多媒体元素)
    - [有序列表 ol](#有序列表-ol)
  - [容器元素](#容器元素)
    - [语义化容器元素](#语义化容器元素)

## a 元素

超链接

### href 属性

hyper reference：通常表示跳转的地址

1. 跳转地址
2. 跳转锚点：`#id`，回到顶部 `#`
3. 功能链接：点击后触发某个功能

功能链接：

- JS 代码：`href="javascript:alert('hello')"`
- 发送邮件：`href="mailto:dsfsdf@qq.com"`
- 打电话：`href="tel:"`

### target 属性

跳转窗口的位置，默认当前窗口

- `_self`
- `_blank`：新窗口打开

## 图片元素 img

src 属性：指定图片路径

alt 属性：src 失效时，使用该属性文字替代图片

### 和 a 元素一起使用

点击图片跳转，将 a 元素包在 img 元素外

### 点击图片区域跳转 map 元素

```html
<map name="solarMap">
  <area shape="" , coords="" , hrefs="" , alt="" />
</map>

<img usemap="#solarMap" , src="xxx" />
```

### figure 元素

将图片、标题、描述包裹起来，这样标题、描述就会跟图片关联起来（语义）

`figcaption` 子元素，表示图片标题

```html
<figure>
  <img src="xxx">
  <figcation>
    <h2>xxx</h2>
  </figcaption>
  <p>asdlfjlaskjdf</p>
</figure>
```

## 多媒体元素

controls: 控制控件的显示，取值只能为 controls

布尔属性，类似 controls 这种属性，H5 中可以不用写属性值

autoPlay：布尔属性，自动播放

muted：布尔属性，静音播放

loop：布尔属性，循环播放

##　列表元素

### 有序列表 ol

属性 type: 1 用数字排序，i 用罗马数字排列，a 用字母排序，A 用大写字母排序

通常用 css 控制列表序号，list-style-type 属性

li 列表元素

###　无序列表 ul

常用于制作菜单、新闻列表

###　定义列表

通常用于一些术语的定义

dl: defination list

dt: defination title

dd: defination desc

## 容器元素

###　 div 元素

没有语义，过去唯一的方式

### 语义化容器元素

- header：头部
- footer：尾部
- article：文章
- section：章节
- aside：附加信息（侧边栏等）
