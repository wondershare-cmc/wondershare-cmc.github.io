## 欢迎来到我的个人空间

### 2022年4月7日
#### NULL对象指针调用成员函数时是否一定会崩溃？
答案是不一定

#### 结论
* 通过对象调用成员函数，对象的指针会被传入到函数中，指针名称为this
* NULL指针对象也可以调用成员函数，只要不访问此对象的成员变量，则程序正常运行，一旦访问此对象的成员变量，则程序崩溃。
* 虚函数的本质是通过类对象的的虚函数表进行访问的，而且类的成员函数除了虚函数，其它都不存储在类当中，因此类对象不存在的情况下，无法使用虚函数，其它函数可以正常访问（前提是这些函数都没有存储类对象的成员变量）



#### 参考资料
[牛客网笔试题](https://www.nowcoder.com/questionTerminal/b38bd4ba5fd043a8977ba43c29393ef5)




### 2022年2月12日 星期六
#### 智能指针shared_ptr的用法

#### 参考资料
[智能指针shared_ptr的用法](https://www.cnblogs.com/jiayayao/p/6128877.html)


### lambda表达式
```c++
// 指明返回类型
auto add = [](int a, int b) -> int { return a + b; };
// 自动推断返回类型
auto multiply = [](int a, int b) { return a * b; };
```
如果需要参数，那么就要像函数那样，放在圆括号里面，如果有返回值，返回类型要放在`->`后面，即拖尾返回类型，当然你也可以忽略返回类型，`lambda`会帮你自动推断出返回类型：这意味着`lambda`表达式无法修改通过复制形式捕捉的变量，因为函数调用运算符的重载方法是`const`属性的。有时候，要想高的那个传值方式的捕获的值，那么就要使用`mutable`，例如如下：
```c++
int main()
{
    int x = 10;
    
    auto add_x = [x](int a) mutable { x *= 2; return a + x; };  // 复制捕捉x
    
    cout << add_x(10) << endl; // 输出 30
    return 0;
}
```
因为你一旦将`lambda`表达式标记为`mutable`，那么实现的函数调用运算符是非const属性的。

* lambda表达式只能作为右值，也就是说，它是不能被赋值的
```C++
auto a=[]{ cout<<"A"<<endl; };
auto b=[]{ cout<<"B"<<endl; };
a = b;  // 非法，lambda表达式变量只能做右值
auto c = a; // 合法，生成一个副本
```
造成以上原因的是因为禁用了赋值运算符,但是没有禁用复制构造函数，所以仍然可以用是一个lambda表达式去初始化另一个(通过产生副本).
```
ClosureType& operator=(const ClosureType&) = delete;
```
##### 关于lambda的捕捉块，主要有以下用法
```
[]: 默认不捕捉变量
[=]: 默认以值捕捉所有变量（最好不要用)
[&]: 默认以引用捕捉所有变量（最好不用)
[x]: 仅以值捕捉x,其他变量不捕捉
[&x]:仅以引用捕捉x,其他变量不捕捉
[=,&x]:默认以值捕捉所有变量，但是x是例外，通过引用捕捉
[&,x]:默认以引用捕捉所有变量，但是x是例外，通过值捕捉
[this]:通过引用捕捉当前对象（其实是复制指针)
[*this]:通过传值方式捕捉当前对象
```


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




