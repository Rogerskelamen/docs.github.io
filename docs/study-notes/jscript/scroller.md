# 原生js实现页面滚动

1. ### [`Element`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element) 的`scrollTo()` 方法可以使界面滚动到给定元素的指定坐标位置。

   ## 语法

   ```js
   element.scrollTo(x-coord, y-coord)
   element.scrollTo(options)
   ```

   - `x-coord` 是期望滚动到位置水平轴上距元素左上角的像素。
   - `y-coord` 是期望滚动到位置竖直轴上距元素左上角的像素。

   \- or -

   - `options` 是一个[`ScrollToOptions`](https://developer.mozilla.org/zh-CN/docs/Web/API/ScrollToOptions)对象。

   for example:

   ```js
   element.scrollTo(0, 1000);
   // or
   element.scrollTo({
     top: 100,
     left: 100,
     behavior: 'smooth'
   });
   ```

   

   > ## 扩展一波

   - ### scrollToOptions是个啥？

     CSSOM View 规范的 **`ScrollToOptions`** 字典（dictionary）当中的属性用于指定一个元素应该滚动到哪里，以及滚动是否应该平滑。

     一个 `ScrollToOptions` 字典可以作为参数提供给下面的方法：

     - [`Window.scroll()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/scroll)
     - [`Window.scrollBy()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/scrollBy)
     - [`Window.scrollTo()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/scrollTo)
     - [`Element.scroll()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/scroll)
     - [`Element.scrollBy()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/scrollBy)
     - [`Element.scrollTo()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/scrollTo)

     ## 属性

     [`ScrollToOptions.top`](https://developer.mozilla.org/zh-CN/docs/Web/API/ScrollToOptions/top)

     指定 window 或元素 Y 轴方向滚动的像素数。

     [`ScrollToOptions.left`](https://developer.mozilla.org/zh-CN/docs/Web/API/ScrollToOptions/left)

     指定 window 或元素 X 轴方向滚动的像素数。

     [`ScrollToOptions.behavior`](https://developer.mozilla.org/zh-CN/docs/Web/API/ScrollToOptions/behavior)

     指定滚动是否应该平滑进行，还是立即跳到指定位置。该属性实际上定义在 `ScrollOptions` 字典上，它通过 `ScrollToOptions` 实现。
     它有两个值：

     - `smooth`: The scrolling animates smoothly.
     - `auto`: The scrolling happens in a single jump.

