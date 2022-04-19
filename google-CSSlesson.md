滚动条在盒模型的padding那个空间里。



##### 8 layout

多列布局

```css
.items {
	column-count: 2;
	column-gap: 1em;
}
```

用`column-width`定义最小宽度，而不指定列数，这样的布局是响应式的。
```css
.items {
	width: 100%;
	column-width: 260px;
	column-gap: 1em;
}
```

