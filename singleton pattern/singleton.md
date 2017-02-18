懒汉（线程不安全）
```java
public class Singleton{
    private Singleton(){}
    private static Singleton instance;
    public static Singleton getInstance(){
        if(instance==null)
            instance=new Singleton();
        return instance;
    }
}
```
懒汉（线程安全）
```java
public class Singleton{
    private Singleton(){}
    private static volatile Singleton instance;
    public static Singleton getInstance(){
        if(instance==null){
            synthronized(Singleton.class){
                if(instance==null)
                    instance=new Singleton();
            }
        }
        return instance;
    }
}
```
饿汉
```java
public class Singleton{
    private Singletion(){}
    private static final Singleton instance=new Singleton();
    public static Singleton getInstance(){
        return instance;
    }
}
```
静态内部类
```java
public class Singleton{
    private Singleton(){}
    private static class Holder{
        private static final Singleton instance=new Singleton();
    }
    public static Singleton getInstance(){
        return Holder.instance;
    }
}
```
枚举
```java
public enum Singleton{
    INSTANCE;
}
```
