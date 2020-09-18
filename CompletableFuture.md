CompletableFuture JDK1.8

静态类的几个方法使用：

```java
public static CompletableFuture<Void>   runAsync(Runnable runnable)
public static CompletableFuture<Void>   runAsync(Runnable runnable, Executor executor)
public static <U> CompletableFuture<U>  supplyAsync(Supplier<U> supplier)
public static <U> CompletableFuture<U>  supplyAsync(Supplier<U> supplier, Executor executor)
```

带Async后缀代表该方法是异步执行，不会影响主线程的运行

不含有Executor executor的方法将默认使用`ForkJoinPool.commonPool()` (全局的，在 JDK8 中介绍的通用池）

runAsync与supplyAsync的区别就在于是否有返回值

```java
CompletableFuture<Void> c1 =	 CompletableFuture.runAsync(() -> {
						System.out.println(111);
						try {
							Thread.sleep(1000);
						} catch (InterruptedException e1) {
							e1.printStackTrace();
						}
					}
					); 

CompletableFuture<Void> c2 =	 CompletableFuture.runAsync(() -> {
						System.out.println(222);
					}
					); 
//					c1.get();
//					c2.get();
```

输出结果：

```java
111
222
```

直接运行，不会触发Thread.sleep(1000);调用get()方法才会触发；

若用自己创建的线程池：

```java
		ExecutorService e = Executors.newFixedThreadPool(3);
			try {
                	//这里用supplyAsync是要调用返回值
					 CompletableFuture.supplyAsync(() -> {
						System.out.println(111);
						try {
							Thread.sleep(1000);
							System.out.println("卡了1秒");
						} catch (Exception e1) {
							e1.printStackTrace();
						}
//                        int a = 10/0;为了触发异常
						return "2";
					},e
					).exceptionally((e)->{System.out.println(123);return "23";}); 
```

输出结果：

```java
111
卡了1秒

当int a = 10/0;
输出：
111
卡了1秒
123
```

