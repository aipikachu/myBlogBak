---
title: 在Hexo的Next主题中支持Latex
tags:
  - Hexo
  - Next
  - Latex
mathjax: true
abbrlink: 2631
date: 2019-06-07 14:44:09
modified: 2019-08-08 21:54:09
---

**声明**：本文参考如下文章：

* [Hexo 的 Next 主题中渲染 MathJax 数学公式](https://blog.csdn.net/wgshun616/article/details/81019687)
* [如何在 hexo 中支持 Mathjax？](https://blog.csdn.net/u014630987/article/details/78670258)
* [使用LaTex添加公式到Hexo博客里](https://blog.csdn.net/Aoman_Hao/article/details/81381507)


在Hexo中的Next主题中默认是不支持 `Latex` 的，但是可以通过安装第三方库来解决这一问题。具体的方法如下。

<!-- more -->

### 第一步：替换Marked渲染引擎

Hexo默认的渲染引擎是marked，但marked渲染引擎不支持mathjax公式输出。而在marked的基础上进行修改得到的kramed渲染引擎则是支持mathjax公式输出的。更换方法如下：

```node
npm uninstall hexo-renderer-marked --save
npm install hexo-renderer-kramed --save
```

同时，需要对博客根目录下的kramed的配置文件做一些修改，方法是打开`/node_modules/hexo-renderer-kramed/lib/renderer.js`（注：此为Linux系统下文件路径的格式，在Windows系统下文件路径分隔符为`\`而不是`/`）文件，将其中的如下代码块

```js
// Change inline math rule
function formatText(text) {
  // Fit kramed's rule: $$ + \1 + $$
  return text.replace(/`\$(.*?)\$`/g, '$$$$$1$$$$');
}
```

修改为：

```js
// Change inline math rule
function formatText(text) {
  // Fit kramed's rule: $$ + \1 + $$
  return text
}
```


### 第二步：安装mathjax

如果已经安装`hexo-math`，需要先卸载，命令如下：

```node
npm uninstall hexo-math --save
```

然后，再安装`hexo-renderer-mathjax`包，命令如下：

```node
npm install hexo-renderer-mathjax --save
```

接下来，则需要更新`Mathjax`中的**CDN**链接。方法是在博客根目录下，打开`/node_modules/hexo-renderer-mathjax/mathjax.html`文件，将其中的代码：

```html
<script src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>
```

修改为

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.1/MathJax.js?config=TeX-MML-AM_CHTML"></script>
```

### 第三步：解决语义冲突

需要指出的是，LaTeX与markdown的语法有语义上的冲突，在hexo中默认的转义规则会将一些字符进行转义。比如在markdown语法中，下划线`_`代表斜体，会被渲染引擎处理为`<em>`标签。而Latex格式书写的数学公式下划线`_`表示下标，有特殊的含义，如果被强制转换为`<em>`标签，那么MathJax引擎在渲染数学公式的时候就会出错。类似的语义冲突的符号还包括`*`，`{`，`}`，`\\`等。

语义冲突的方法如下，在博客的根目录下，打开`/node_modules/kramed/lib/rules/inline.js`文件，首先找到`escape`字段（一般在第11行），将其中的内容由如下形式：

```js
escape: /^\\([\\`*{}\[\]()#$+\-.!_>])/,
```

修改为：

```js
escape: /^\\([`*\[\]()#$+\-.!_>])/,
```

这一步是在原基础上取消了对`\`，`{`，`}`的转义。另外，还要修改该文件中的`em`字段（一般在第20行），将其中的内容由如下形式：

```js
em: /^\b_((?:__|[\s\S])+?)_\b|^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
```

修改为：

```js
em: /^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
```

重新启动hexo（先clean再generate），应该就可以了。



### 第四步：开启Mathjax支持

在Next主题的`_config.yml`中开启Mathjax。打开`_config.yml`文件，找到找到`mathjax`字段，将默认的`false`修改为`true`。并更新其中**cdn**的**url**。更新后的内容如下：

```yml
# MathJax Support
mathjax:
  enable: true
  per_page: true
  # cdn: //cdn.bootcss.com/mathjax/2.7.1/latest.js?config=TeX-AMS-MML_HTMLorMML
  cdn: //cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.1/MathJax.js?config=TeX-AMS-MML_HTMLorMML
```

至此，即可在Hexo中开启Latex支持。另外，可在每次需要用LaTeX渲染的博文中，在文章的Front-matter里打开mathjax开关，具体示例如下：

```md
---
title: Testing Mathjax with Hexo
date: 2019-06-07 14:44:09
category: xxx
mathjax: true
---
```

做一个测试，Latex语句如下：

```latex
\begin{equation}
\left\{
\begin{array}{lr}
x=\dfrac{3\pi}{2}(1+2t)\cos(\dfrac{3\pi}{2}(1+2t)), & \\
y=s, & 0 \leq s \leq L,|t| \leq1. \\
z=\dfrac{3\pi}{2}(1+2t)\sin(\dfrac{3\pi}{2}(1+2t)), &  
\end{array}
\right.
\end{equation}
```

渲染效果如下：

\begin{equation}
\left\{
\begin{array}{lr}
x=\dfrac{3\pi}{2}(1+2t)\cos(\dfrac{3\pi}{2}(1+2t)), & \\
y=s, & 0 \leq s \leq L,|t| \leq1. \\
z=\dfrac{3\pi}{2}(1+2t)\sin(\dfrac{3\pi}{2}(1+2t)), &  
\end{array}
\right.
\end{equation}
