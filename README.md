# swift-error
swift遇到错误收集

## [Function错误]定义两个同名的方法
Compiler error: Method with Objective-C selector conflicts with previous declaration with the same Objective-C selector

####问题描述

在定义一个同样的方法名时出现了如下错误

![Problem1](https://github.com/yyllove123/swift-error/blob/master/problem1.jpg)

	class ViewController: UIViewController {
    
	    func perform(operation : Double -> Double)
	    {
	        
	    }
	    
	    func perform(operation : NSData)
	    {
	        
	    }
	}
	
####问题分析

首先，swift是支持重载(overloading)的，上面方法名不一样但是参数类型不一样，按照重载的理解，上面的代码不应该有问题。但是问题就是有了。下面看看解答。

解答：虽然swift是支持重载，但是OC不支持，上面的ViewController是继承的UIViewController，就是说OC和Swift都可以使用这个类。编译器会将此类用OC的编译方式检测，所以会报错。

扩展：
但是，如果上面代码改为

	class ViewController: UIViewController {
    
	    func perform(operation : Double -> Double)
	    {
	        
	    }
	    
	    func perform(operation some : NSData)
	    {
	        
	    }
	}
就不会报错，不过，如果写成这样又会报错，

	class ViewController: UIViewController {
    
	    func perform(#operation : Double -> Double)
	    {
	        
	    }
	    
	    func perform(operation some : NSData)
	    {
	        
	    }
	}
这样又会有问题。这里就是要说一下Swift转OC的方式。

如果一个swift类在OC中使用的时候，编译器会将swift类转换成OC的类进行处理。

	func perform(operation : Double -> Double) {}
这个方法会被转换为

	-(void)perform:(Double (^)(Double))block {}

这个方法

	func perform(operation: NSData) {}

会被转换为

	-(void)perform:(NSData *)operation {}
	
这两个方法在OC里面是不行的，所以会报错。

不过如果我们把方法改为下面这个

	func perform(operation some : NSData) {}
或者

	func perform(#operation : Double -> Double) {}
会相应的转换为

	-(void)performWithOperation:(Double (^)(Double))block {}
	-(void)performWithOperation:(NSData *)operation {}

这样就能理解了。
