## CSS学习笔记01

**替换元素(img)和非替换元素(p、div、h2等)**

在HTML中块级标签不可以放入到行内标签中

```html
<span><p>hdhdhdh</p></span>
```

这个是不合法的（至少在XHTML中）。但是

**css中块级元素和行内元素可以随意嵌套**

```css
<p><span>hdhdhdh</span></p>
p {
  display:inline;
}
span {
  display:block;
}
```

**@import**

```css
@import url('example.css)
```

@import要出现在CSS文档的开头，如果穿插在其他规则之后，会被忽略

**CSS注释/* */，但是不能嵌套**

