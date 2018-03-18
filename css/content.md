# 参考:
>  《css世界》第4章

# 替换元素
通过修改某个属性值，所呈现的内容可以替换的元素就成为"替换元素"，比如`<img>、<object>、<video>、<iframe>、<textarea>、<input>、<select>`等

# 替换元素应用-首屏优化
为了提高性能与节约流量，首屏以下的图片会通过滚动的方式异步加载，然后为了异步加载的布局稳健、体验良好，可以用一张透明的图片做占位
比如:
```html
    <img src="transparent.jpg">
```
等价于:
```html
    <img>
```
```css
    img {
        visibility: hidden;
    }
    img[src] {
        visibility: visible;
    }
```
这里的`img`不能加`src=""`