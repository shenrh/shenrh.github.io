---
title: 线程同步
---
Thread同步机制的比较

　　ThreadLocal和线程同步机制相比有什么优势呢？ThreadLocal和线程同步机制都是为了解决多线程中相同变量的访问冲突问题。
　　<br>
　　在同步机制中，通过对象的锁机制保证同一时间只有一个线程访问变量。这时该变量是多个线程共享的，使用同步机制要求程序慎密地分析什么时候对变量进行读写，什么时候需要锁定某个对象，什么时候释放对象锁等繁杂的问题，程序设计和编写难度相对较大。
　　<br>
　　而ThreadLocal则从另一个角度来解决多线程的并发访问。ThreadLocal会为每一个线程提供一个独立的变量副本，从而隔离了多个线程对数据的访问冲突。因为每一个线程都拥有自己的变量副本，从而也就没有必要对该变量进行同步了。ThreadLocal提供了线程安全的共享对象，在编写多线程代码时，可以把不安全的变量封装进ThreadLocal。
　　<br>
　　由于ThreadLocal中可以持有任何类型的对象，低版本JDK所提供的get()返回的是Object对象，需要强制类型转换。但JDK 5.0通过泛型很好的解决了这个问题，在一定程度地简化ThreadLocal的使用
　　<br>
　　概括起来说，对于多线程资源共享的问题，同步机制采用了“以时间换空间”的方式，而ThreadLocal采用了“以空间换时间”的方式。前者仅提供一份变量，让不同的线程排队访问，而后者为每一个线程都提供了一份变量，因此可以同时访问而互不影响。
　　<br>
　　ThreadLocal是解决线程安全问题一个很好的思路，它通过为每个线程提供一个独立的变量副本解决了变量并发访问的冲突问题。在很多情况下，ThreadLocal比直接使用synchronized同步机制解决线程安全问题更简单，更方便，且结果程序拥有更高的并发性。

Android的looper实现就使用了ThreadLocal存储looper，每个线程只有一个looper

	static final ThreadLocal<Looper> sThreadLocal = new ThreadLocal<Looper>();
	sThreadLocal是个全局的静态变量，那么所有的Looper类都共享同一个sThreadLocal
	
	sThreadLocal.set操作定义如下
	
	java.lang.ThreadLocal.java
	public void set(T value) {  
	    Thread currentThread = Thread.currentThread();  
	    Values values = values(currentThread);  
	    if (values == null) {  
	        values = initializeValues(currentThread);  
	    }  
	    values.put(this, value);  
	}  
	
	即从当前调用线程中取出values对象，然后往这个values对象存放这个Looper
	需要注意的是每个Thread中都有一个values对象，
	
	这个values对象再按照ThreadLocal<Looper> sThreadLocal对象在当前线程的values哈希表中找出对应的Looper
	
	那么这个Looper就对应为当前线程的Looper
	那么使用ThreadLocal有什么好处呢？
	
	好处是显而易见的，如果用全局的HashMap管理一个Thread对应一个Looper，
	
	那么增删改某个Looper对象时就需要进行同步操作，这大大增加了系统开销
	
	而如果有一个ThreadLocal.Values对象存放在Thread里，需要用到时就直接获取，不与其他线程的数据进行交互，
	那么就避免了同步带来的低效率问题，所以这个ThreadLocal正好被应用到了一个Thread对应一个Looper中
	
[ Android应用程序线程消息循环模型分析 ](http://blog.csdn.net/luoshengyang/article/details/6905587)