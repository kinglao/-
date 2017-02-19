抽象工厂模式（abstract factory pattern）

所要生产的对象

```java
public interface XPath {...}
public class XPathImpl implements javax.xml.xpath.XPath {...}
```

`XpathImpl`即`com.sun.org.apache.xpath.internal.jaxp.XPathImpl`



工厂

```java
public abstract class XPathFactory {
  	protected XPathFactory() {
    }
  	public static XPathFactory newInstance() {
        try {
             return newInstance(DEFAULT_OBJECT_MODEL_URI);
        } catch (XPathFactoryConfigurationException xpathFactoryConfigurationException) {
        }
    }
  	public static XPathFactory newInstance(final String uri)
        throws XPathFactoryConfigurationException {
		ClassLoader classLoader = ss.getContextClassLoader();
        XPathFactory xpathFactory = new XPathFactoryFinder(classLoader).newFactory(uri);
        return xpathFactory;
    }
  	public static XPathFactory newInstance(String uri, String factoryClassName, ClassLoader classLoader)
        throws XPathFactoryConfigurationException{
        ClassLoader cl = classLoader;
		XPathFactory f = new XPathFactoryFinder(cl).createInstance(factoryClassName);
    }
  	public abstract XPath newXPath();
}

public  class XPathFactoryImpl extends XPathFactory {
  	public javax.xml.xpath.XPath newXPath() {
            return new com.sun.org.apache.xpath.internal.jaxp.XPathImpl(
                    xPathVariableResolver, xPathFunctionResolver,
                    !_isNotSecureProcessing, _useServicesMechanism,
                    _featureManager );
    }
}
```

`com.sun.org.apache.xpath.internal.jaxp.XPathImpl`就是所要生产的对象。注意在XPathFactory#newInstance中，通过XPathFactoryFinder#newFactory或者#createInstance生成了XPathPactory。



创建工厂(FactoryFinder or FactoryBuilder)

```java
class XPathFactoryFinder  {
  	public XPathFactory newFactory(String uri) throws XPathFactoryConfigurationException {...}
  	XPathFactory createInstance( String className ) throws XPathFactoryConfigurationException{...}
}
```

使用：

```java
XPathFactory xpf = XPathFactory.newInstance();
XPath xpath = xpf.newXPath();
```



对应UML图：

![](./images/XPath.png)



上面的例子只有一个生产对象XPath，再举一个例子。

![](./images/Transformer.png)

生产的对象

```java
public abstract class Transformer {}
public final class TransformerImpl extends Transformer
    implements DOMCache, ErrorListener{}
public class EfficientStreamingTransformer extends Transformer {}
```

```java
public interface Templates {}
public final class TemplatesImpl implements Templates, Serializable {}
```



工厂

```java
public abstract class TransformerFactory {
  	protected TransformerFactory() { }
  	public static TransformerFactory newInstance() throws TransformerFactoryConfigurationError {
        return FactoryFinder.find(
            /* The default property name according to the JAXP spec */
            TransformerFactory.class,
            /* The fallback implementation class name, XSLTC */
            "com.sun.org.apache.xalan.internal.xsltc.trax.TransformerFactoryImpl");
    }
  	public static TransformerFactory newInstance(String factoryClassName, ClassLoader classLoader) throws TransformerFactoryConfigurationError{
        return  FactoryFinder.newInstance(TransformerFactory.class,
                    factoryClassName, classLoader, false, false);
    }
  	public abstract Templates newTemplates(Source source) throws TransformerConfigurationException;
  public abstract Transformer newTransformer(Source source) throws TransformerConfigurationException;
  public abstract Transformer newTransformer() throws TransformerConfigurationException;
  
}
```

子类`TransformerFactoryImpl`和`SmartTransformerFactoryImpl`分别实现了abstract方法。`SAXTransformerFactory`也是抽象工厂。

在TransformerFactory#newInstance中使用javax.xml.transform.FactoryFinder#find或者#newInstance得到工厂

```java
class FactoryFinder {
  	static <T> T find(Class<T> type, String fallbackClassName) throws TransformerFactoryConfigurationError{}
  	static <T> T newInstance(Class<T> type, String className, ClassLoader cl, boolean doFallback, boolean useServicesMechanism) throws TransformerFactoryConfigurationError{}
}
```



使用：

```java
TransformerFactory tfactory;
tfactory = TransformerFactory.newInstance();
Templates stylesheet=tfactory.newTemplates(new DOMSource(xslDOM,xslFileName));
SAXTransformerFactory stf = (SAXTransformerFactory) tfactory;
Transformer serializer = stf.newTransformer();
```





优点

1. 抽象工厂模式隔离了具体类的生成，使得客户并不需要知道什么被创建。由于这种隔离，更换一个具体工厂就变得相对容易。所有的具体工厂都实现了抽象工厂中定义的那些公共接口，因此只需改变具体工厂的实例，就可以在某种程度上改变整个软件系统的行为。另外，应用抽象工厂模式可以实现高内聚低耦合的设计目的，因此抽象工厂模式得到了广泛的应用。
2. 当一个产品族中的多个对象被设计成一起工作时，它能够保证客户端始终只使用同一个产品族中的对象。这对一些需要根据当前环境来决定其行为的软件系统来说，是一种非常实用的设计模式。
3. 增加新的具体工厂和产品族很方便，无须修改已有系统，符合“开闭原则”。

缺点

1. 在添加新的产品对象时，难以扩展抽象工厂来生产新种类的产品，这是因为在抽象工厂角色中规定了所有可能被创建的产品集合，要支持新种类的产品就意味着要对该接口进行扩展，而这将涉及到对抽象工厂角色及其所有子类的修改，显然会带来较大的不便。
2. 开闭原则的倾斜性（增加新的工厂和产品族容易，增加新的产品等级结构麻烦）。

适用环境
在以下情况下可以使用抽象工厂模式：

1. 一个系统不应当依赖于产品类实例如何被创建、组合和表达的细节，这对于所有类型的工厂模式都是重要的。
2. 系统中有多于一个的产品族，而每次只使用其中某一产品族。
3. 属于同一个产品族的产品将在一起使用，这一约束必须在系统的设计中体现出来。
4. 系统提供一个产品类的库，所有的产品以同样的接口出现，从而使客户端不依赖于具体实现。
