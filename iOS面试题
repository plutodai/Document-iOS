#iOS面试题目及答案
--

###1.什么是ARC? (虽然现在基本不用MRC，不过知道总是好的)

首先解释ARC: Automatic Reference Counting自动引用计数。 
ARC几个要点： 
在对象被创建时 retain count +1，在对象被release时 retain count - 1.当retain count 为0 时，销毁对象。 
程序中加入autoreleasepool的对象会由系统自动加上autorelease方法，如果该对象引用计数为0，则销毁。 
那么ARC是为了解决什么问题诞生的呢？这个得追溯到MRC手动内存管理时代说起。 
MRC下内存管理的缺点： 
1.当我们要释放一个堆内存时，首先要确定指向这个堆空间的指针都被release了。（避免提前释放） 
2.释放指针指向的堆空间，首先要确定哪些指针指向同一个堆，这些指针只能释放一次。（MRC下即谁创建，谁释放，避免重复释放） 
3.模块化操作时，对象可能被多个模块创建和使用，不能确定最后由谁去释放。 
4.多线程操作时，不确定哪个线程最后使用完毕

###2.UIView和CALayer是啥关系？

UIView是iOS系统中界面元素的基础，所有的界面元素都继承自它。它本身完全是由CoreAnimation来实现的 （Mac下似乎不是这样）。它真正的绘图部分，是由一个叫CALayer（Core Animation Layer）的类来管理。 UIView本身，更像是一个CALayer的管理器，访问它的跟绘图和跟坐标有关的属性，例如frame，bounds等 等，实际上内部都是在访问它所包含的CALayer的相关属性。

###3. 使用drawRect有什么影响？（这个可深可浅，你至少得用过。。）

drawRect方法依赖Core Graphics框架来进行自定义的绘制，但这种方法主要的缺点就是它处理touch事件的方式：每次按钮被点击后，都会用`setNeedsDisplay`进行强制重绘；而且不止一次，每次单点事件触发两次执行。这样的话从性能的角度来说，对CPU和内存来说都是欠佳的。特别是如果在我们的界面上有多个这样的UIButton实例。

###4. loadView是干嘛用的？

当你访问一个ViewController的view属性时，如果此时view的值是nil，那么，ViewController就会自动调用loadView这个方法。这个方法就会加载或者创建一个view对象，赋值给view属性。 
loadView默认做的事情是：如果此ViewController存在一个对应的nib文件，那么就加载这个nib。否则，就创建一个UIView对象。

如果你用Interface Builder来创建界面，那么不应该重载这个方法。

如果你想自己创建view对象，那么可以重载这个方法。此时你需要自己给view属性赋值。你自定义的方法不应该调用super。如果你需要对view做一些其他的定制操作，在viewDidLoad里面去做。

###5.有没有使用过block，使用block时需要注意什么？

注意避免循环引用，

block中的循环引用：一个viewController

~~~
@property (nonatomic,strong)HttpRequestHandler * handler;
@property (nonatomic,strong)NSData *data;
_handler = [httpRequestHandler sharedManager];
[ downloadData:^(id responseData){
    _data = responseData;
}];    
~~~
self 拥有_handler, _handler 拥有block, block拥有self（因为使用了self的_data属性，block会copy 一份self） 
解决方法：

    __weak typedof(self)weakSelf = self
    [ downloadData:^(id responseData){
        weakSelf.data = responseData;
    }];

###6.对于iPhone6和iPhone6 Plus的屏幕适配问题，具体的解决方案

AutoLayout，xib或者代码（NSLayoutConstraint 或者第三方 如Masonry等）

###7.请解释以下keywords的区别： assign vs weak, __block vs __weak

assign适用于基本数据类型，weak是适用于NSObject对象，并且是一个弱引用。 
assign其实也可以用来修饰对象，那么我们为什么不用它呢？因为被assign修饰的对象在释放之后，指针的地址还是存在的，也就是说指针并没有被置为nil。如果在后续的内存分配中，刚好分到了这块地址，程序就会崩溃掉。 
而weak修饰的对象在释放之后，指针地址会被置为nil。所以现在一般弱引用就是用weak。 
首先__block是用来修饰一个变量，这个变量就可以在block中被修改（参考block实现原理） 
__block：使用__block修饰的变量在block代码快中会被retain（ARC下，MRC下不会retain） 
__weak：使用__weak修饰的变量不会在block代码块中被retain 
同时，在ARC下，要避免block出现循环引用 __weak typedof(self)weakSelf = self;

###8.使用nonatomic和atomic的区别

* atomic原子操作，系统会为setter方法加锁。 具体使用`@synchronized(self){//code }`
nonatomic不会为setter方法加锁。 
* atomic：线程安全，需要消耗大量系统资源来为属性加锁 
nonatomic：非线程安全，适合内存较小的移动设备

###9. GCD里面有哪几种Queue？(有没有用过多线程，用过哪几种？GCD、NSThread、NSOperation， 主要了解GCD)

* 1.主队列`dispatch_main_queue();`串行 ，更新UI
* 2.全局队列`dispatch_global_queue();`并行，四个优先级：background，low，default，high 
* 3.自定义队列`dispatch_queue_t queue;`可以自定义是并行：`DISPATCH_QUEUE_CONCURRENT`或者串行`DISPATCH_QUEUE_SERIAL`

###10. http的post和get啥区别？

1.GET请求的数据会附在URL之后（就是把数据放置在HTTP协议头中），以?分割URL和传输数据，参数之间以&相连，如：login.action?name=hyddd&password=idontknow&verify=%E4%BD%A0%E5%A5%BD。如果数据是英文字母/数字，原样发送，如果是空格，转换为+，如果是中文/其他字符，则直接把字符串用BASE64加密，得出如：%E4%BD%A0%E5%A5%BD，其中％XX中的XX为该符号以16进制表示的ASCII。 POST把提交的数据则放置在是HTTP包的包体中。

2.POST的安全性要比GET的安全性高。注意：这里所说的安全性和上面GET提到的“安全”不是同个概念。上面“安全”的含义仅仅是不作数据修改，而这里安全的含义是真正的Security的含义，比如：通过GET提交数据，用户名和密码将明文出现在URL上，因为(1)登录页面有可能被浏览器缓存，(2)其他人查看浏览器的历史纪录，那么别人就可以拿到你的账号和密码了，除此之外，使用GET提交数据还可能会造成Cross-site request forgery攻击。

总结一下，Get是向服务器发索取数据的一种请求，而Post是向服务器提交数据的一种请求，在FORM（表单）中，Method默认为”GET”，实质上，GET和POST只是发送机制不同，并不是一个取一个发。
