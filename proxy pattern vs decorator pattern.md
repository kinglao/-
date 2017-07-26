# 代理模式 vs 装饰器模式
代理的使用者一般不会关心被代理的对象的存在的，通常构造Proxy的时候，不用把被代理的对象传给它。Decorator正好相反，使用者需要清楚要装饰的是什么东西，Java IO包里的很多类就是装饰者。

代理模式和装饰器模式的区别看维基百科的例子：</br>
https://en.wikipedia.org/wiki/Proxy_pattern#Java   </br>
https://en.wikipedia.org/wiki/Decorator_pattern#Java   </br>
可以看出来，JDK的FutureTask使用了代理模式

参考：
http://www.cnblogs.com/jaredlam/archive/2011/11/08/2241089.html </br>
https://powerdream5.wordpress.com/2007/11/17/the-differences-between-decorator-pattern-and-proxy-pattern/
