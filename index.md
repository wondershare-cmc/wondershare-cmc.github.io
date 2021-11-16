## 欢迎来到我的个人空间


### 2021-11-12  周五



### 2021-11-13  周六

* 重点：QSystemTrayIcon是Qt提供的托盘
* singleShot()的特殊用法，如果第一个参数，也就是等待时间为0，当Qt回到消息循环的时候，通常就是当前处理的事件处理完成之后就立即处理槽函数。
* 点击通知栏的处理需要多调用setWindowState(Qt::WindowNotState);将窗口的状态恢复为正常的状态。如果不调用此函数，就会出现再次显示界面，点击
界面可能出现无法响应的问题。（新show的界面，点击后可能出现接收不到鼠标事件，多次点击后标题栏的最小化按钮后，响应了hideEvent事件窗口直接隐藏了）


#### 参考资料
[Qt实现系统托盘](https://blog.csdn.net/u011417605/article/details/51322997)
[Qt下窗口最小化到托盘](https://www.geek-share.com/detail/2557485470.html)


### 2021-11-15 周一 

#### 如何过滤windows系统的任务栏的“关闭窗口”消息
* 思路：重写nativeEvent的事件

#### 参考资料
[QT 禁止通过 任务栏的“关闭窗口”来关闭程序](https://blog.csdn.net/hellokandy/article/details/115346854)
