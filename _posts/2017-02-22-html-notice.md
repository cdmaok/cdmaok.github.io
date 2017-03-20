---
layout: post
title: html notice
category: html
---

Some practice on html learner

1. 保持标签闭合，所有标签都有闭合标签
2. 声明正确的文档类型，说明是html还是xhtml
3. 不要用内联样式。

```
<p style="color: red;">I'm going to make this text red so that it really stands out and makes people take notice! </p>
```

4. 将所有外部css文件放入head标签内，可以使网页加载速度，因为这样可以使页面逐步渲染。

```
<head>  
<title>My Favorites Kinds of Corn</title>  
<link rel="stylesheet" type="text/css" media="screen" href="path/to/file.css" />  
<link rel="stylesheet" type="text/css" media="screen" href="path/to/anotherFile.css" />  
</head>
```

5. javascript文件放在底部

```
<p>And now you know my favorite kinds of corn. </p>  
<script type="text/javascript" src="path/to/file.js"></script>  
<script type="text/javascript" src="path/to/anotherFile.js"></script>  
</body>  
</html>
```

6. 不要在源代码里面直接写js代码
7. 一边写，一遍验证。
8. 使用firebug
9. 保持标签名小写
10. 使用h1-h6标签，请将h1留给文章标题
11. 使用无序列表（UL）包裹导航菜单，可以避免使用过多的div
12. 给所有图片加上“alt”属性
