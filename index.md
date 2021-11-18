## 欢迎来到我的个人空间

###  2021-11-11 周四
#### 绘制一个带边框的填充的圆角矩形

* 关键QPainterPath 

#### 参考资料
[绘制带边框的填充的圆角矩形](https://www.coder.work/article/111792)


### 2021-11-12  周五
#### 问题：新show的界面，点击后可能出现接收不到鼠标事件，多次点击后标题栏的最小化按钮后，响应了hideEvent事件窗口直接隐藏了
#### 排查思路
* 开始以为是由于进度条的刷新影响了事件循环，后面改用定时器每隔1s刷新仍然存在此情况，然后干脆屏蔽了进度条的刷新发现程序依然这样。
* 通过断点发现程序多次点击有时候无法进入hideEvent事件去，最后依靠日志记录，发现界面在多次点击无响应的情况下后面触发调用了2次hideEvent事件。
* 继续分析代码发现程序响应了miniMized和hide两个事件，当我将hide事件屏蔽之后界面可以正常的隐藏和显示响应点击。
* 开始以为是miniMized和hide事件相互响应导致界面再次显示出了问题，而问题的实质则是窗口的状态发生了改变，必须调用setWindowState再次标记窗口的状态。

#### 知识点
* Qt将窗体变成顶层窗体(activeWindow和raise())

#### 参考资料
[raise的使用](https://www.cnblogs.com/findumars/p/5599447.html)


### 2021-11-13  周六

* 重点：QSystemTrayIcon是Qt提供的托盘
* singleShot()的特殊用法，如果第一个参数，也就是等待时间为0，当Qt回到消息循环的时候，通常就是当前处理的事件处理完成之后就立即处理槽函数。
* 点击通知栏的处理需要多调用setWindowState(Qt::WindowNotState);将窗口的状态恢复为正常的状态。如果不调用此函数，就会出现再次显示界面，点击
界面可能出现无法响应的问题。原因是界面最小化后，Qt::WA_Mapped属性会丢失，再次show界面的时候会导致界面刷新出现问题，所以必须再次设置Qt::WA_Mapped的属性。
另外一种方式是重写changeEvent(QEvent * event)事件，在changeEvent中判断当!isMinimized()的状态下setAttribute(Qt::WA_Mapped);

```
After minimizing window attribute Qt::WA_Mapped was removed from QMainWindow (you can set breakpoint to setAttribute_internal in qwidget.cpp to check it). But this attribute was not set again after showing window. This caused that condition if (discardSyncRequest(tlw, tlwExtra)) in QWidgetBackingStore::sync was not met and it caused dirtyWidgets are not cleared. In other part of Qt updating system this caused that no other rendering was made.
Workaround I did: Subclassed QMainWindow and set attribute Qt::WA_Mapped manually when window was restored (handling changeEvent):
```


#### 参考资料
[Qt实现系统托盘](https://blog.csdn.net/u011417605/article/details/51322997)

[Qt下窗口最小化到托盘](https://www.geek-share.com/detail/2557485470.html)

[Qt实现app界面程序出现卡着不动的解决](https://blog.csdn.net/huangyifei_1111/article/details/80194221)

[Qt界面最小化后再次show出现界面冻结的问题](https://stackoverflow.com/questions/14554903/widgets-freezing-after-minimise-window)

### 2021-11-15 周一 

#### 如何过滤windows系统的任务栏的“关闭窗口”消息
* 思路：重写nativeEvent的事件

#### 参考资料
[QT 禁止通过 任务栏的“关闭窗口”来关闭程序](https://blog.csdn.net/hellokandy/article/details/115346854)


### 2021-11-16 周二
#### VS中的Resharp的快捷键Ctrl+t没有生效的原因，没有配置Keyboard scheme选中Visual Studio
![image](https://user-images.githubusercontent.com/94329757/141891860-1f2eec9e-193a-4f8b-b0d2-5067a2c4bf06.png)

### 2021-11-17 周三
#### moveToThread实现多线程
* 创建QObject派生类对象不能带有父类

[moveToThead的使用](https://www.cnblogs.com/lcgbk/p/13950284.html)




