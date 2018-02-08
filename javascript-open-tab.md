### Javascript打开新标签（非窗口）

我们知道，在`<a>`标签中通过设置target="_blank"就可以实现打开新标签的效果。
但有时候我们需要通过Javascript来打开新标签，那么怎么实现呢？方法如下：
```javascript
window.open("http://www.test.com");
```
或者：
```javascript
window.open("http://www.test.com", "_blank"); //注意第二个参数
```
有人或许会觉得奇怪，window.open()不是用来打开新窗口的麽，怎么还可以打开新标签啊？

其实只有在window.open()中指定了第二个属性（即新窗口的特征）时浏览器才会打开新的窗口，
在没指定第三个属性时只会在当前窗口打开新的标签（在IE中，如果要打开的URL与当前页面URL不属于同一个主域名则打开新窗口；
在Chrome中，如果window.open()函数不是被鼠标键盘事件调用的，而是页面直接调用或通过定时器等调用的，则打开新窗口而非标签）。

此外，下面适用于`<a>`标签的target参数同样适用于window.open()的name参数：
![](http://img.ph.126.net/PtFxqDPKFWo6wFHQhs0QEw==/1926696215684487790.jpg)

注意事项:

1. 在IE中，如果要打开的域名和当前域名不属于同一个主域名，则会在新的窗口中打开（`<a>`标签也是这样）。
2. 在Chrome中，如果window.open()函数不是被鼠标键盘事件调用的，而是页面直接调用或通过定时器（包括鼠标键盘触发的定时器）等调用的，则打开新窗口而非标签。
3. 在新窗口或新标签中，window.open()的_parent和_top参数是无效的（只有在frame中时有效）。
4. framename参数可以设置为当前页面内的frame的name值、新窗口的name值，或者新标签的name值。

[window.open介绍](http://www.w3school.com.cn/jsref/met_win_open.asp)
