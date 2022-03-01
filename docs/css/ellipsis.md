# css单、多行省略号

---

## 单行省略

```css
width: 100%;
white-space: nowrap;
overflow: hidden;
text-overflow: ellipsis;
```

## 多行省略

```css
width:100%    
overflow : hidden; // 超出的部分隐藏
text-overflow: ellipsis; // 超出的部分省略
display: -webkit-box; // 设置为是一个盒子
-webkit-line-clamp: 2; // 超出几行后会开始隐藏
-webkit-box-orient: vertical; // 子元素的排列顺序
```
