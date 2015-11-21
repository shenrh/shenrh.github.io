---
title: Java Exception体系
---

Java Exception体系继承关系如下

![image](../blog_img/exception.jpg)

通常来讲，Java中的异常会被分为三种：

Error: 这种异常被设计成不被捕获，因为这种异常产生于JVM自身。

Runtime Exception: 运行时异常往往与环境有关，编译时无法检查，并且可能发生的情况太广泛，所以系统会去处理，程序不需要捕获。

普通异常: 非运行时异常。

这里的Java异常指直接继承java.lang.Throwable的异常类：

普通的Exception必须被捕获，RuntimeException则不用

	package com.test;
	
	public class Test {
	
		public static void main(String[] args) {
			test();
		}
	
		public static void test() {
			throwError(); // 运行时阻断程序
			
			throwRuntimeException(); // 运行时阻断程序
			throwsRuntimeException(); // 运行时阻断程序
			
			throwException(); // 运行时不会阻断程序，内部不捕获的话编译不能过
			try {
				throwsException(); // 运行时不会阻断程序，不捕获的话编译不能过
			} catch (Exception e) {
				e.printStackTrace();
			}
		}
	
		// 自己和调用方都不需要必须处理
		public static void throwError() {
			throw new Error();
		}
	
		// 自己和调用方都不需要必须处理
		public static void throwRuntimeException() {
			throw new RuntimeException();
		}
	
		// 自己和调用方都不需要必须处理
		public static void throwsRuntimeException() throws RuntimeException {
			throw new RuntimeException();
		}
	
		// 必须throws或着自己捕获,自己捕获外层不会获取到
		public static void throwException() {
			try {
				throw new Exception();
			} catch (Exception e) {
				e.printStackTrace();
			}
		}
	
		// 必须throws或着自己捕获,throws出去的话外部必须捕获或者继续向上层throws
		public static void throwsException() throws Exception {
			throw new Exception();
		}
	}
	