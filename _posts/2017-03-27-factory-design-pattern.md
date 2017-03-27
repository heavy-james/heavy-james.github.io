---
layout: post
title:  工厂模式
description: 简单工厂模式、工厂模式、抽象工厂模式的演进和比较总结
category: blog
---

假如我们需要一个产品类，并用它做些事，在使用的时候创建它：

```
ProductA pta = new ProductA();
pta.doSomething();

```
我们将这种直接指定产品类型的创建模式叫静态创建。  

## 简单工厂模式

假如由于需求变更，我们需要ProductA1来做这件事，我们就需要修改代码才能达到这个效果了。这就违反了闭则，所以我们对产品引入一个抽象层，指定具体类型的实例化过程封装到另一个类中。  

假设我们引入的抽象产品类型是ProductA,负责帮助我们创建产品的工具叫FactoryA。那么，现在的实现是：

``` 
interface ProductA {
    doSomething();
}

class FactoryA{
    public ProductA createProdoctA(String args){
        if(args = "type1"){
            return new ProductA1();
        }else if(args = "type2"){
            return new ProductA2();
        }
    }
}

class Main{
    public static void main(String args){
        ProductA pt = FactoryA.createProductA("type1");
        pt.doSomething();
    }
}

```

现在我们要更换产品类型，只要修改修改type值，相对修改对象类型，已经好很多了，但是还是需要代码，不符合闭则，我们可以保留type和类型这种对应关系，但是使用的type信息，由第三方托管。例如使用xml来保存类型信息，类似的做法还有数据库保存、服务器下发等。

假设我们用xml保存具体产品的type

```
<?xml version="1.0"?> 
<productA>  
    <type>type1</type>  
</productA>


```

使用工具类XMLUtil来读取：
```
public class XMLUtil {  
    //该方法用于从XML配置文件中提取产品类型，并返回类型名  
    public static String getNodeContent(String fileName, String targetTag) {  
        try {  
            //创建文档对象  
            DocumentBuilderFactory dFactory = DocumentBuilderFactory.newInstance();  
            DocumentBuilder builder = dFactory.newDocumentBuilder();  
            Document doc;                             
            doc = builder.parse(new File(fileName));   

            //获取包含图表类型的文本节点  
            NodeList nl = doc.getElementsByTagName(targetTag);  
            Node classNode = nl.item(0).getFirstChild();  
            String type = classNode.getNodeValue().trim();  
            return type;  
        }     
        catch(Exception e) {  
            e.printStackTrace();  
            return null;  
        }  
    }  
}
```

现在的Main类实现为：
```
class Main{
    public static void main(String args){
        String type = XMLUtil.getNodeContent("config.xml", "ProductA");
        ProductA pt = FactoryA.createProductA(type);
        pt.doSomething();
    }
}
```
现在我们如果要更换产品类型，只需要修改config.xml文件了。

这种引用抽象类型，将具体的实类放到运行时指定的方式，我们叫做动态创建。动态创建的机制是，由于继承的机制存在，允许我们在不知道具体类的情况下，直接使用类的抽象行为，而真正实现这个行为的对象类型，就可以在运行时指定。

有时候会对简单工厂模式做一个简化，可以将Factory的逻辑，Prodoct的基类中，这种做法在jdk中被广泛使用。

## 工厂模式

简单工厂有一个明显的不足，如果抽象产品类型ProductA有多个子类，Factory的create的逻辑就会变得拖沓，它的职能就会很重，一旦它不能正常工作，整个系统都收到严重的影响。我们对Factory也引入一个抽象，一个factory创建一个具体的产品，通过多态来达到创建不同产品的目的：

```
interface FactoryA{
    public ProductA createProductA();
}

class FactoryA1{
    public ProductA createProductA(){
        return new ProductA1();
    }
}

class FactoryA2{
    public ProductA createProductA(){
        return new ProductA2();
    }
}

```
此时的Main实现为:
```
class Main{
    public static void main(String args){
        FactoryA factoryA = new FactoryA1();
        ProductA productA = factoryA.createProductA();
        productA.doSometing();
    }
}
```
这里同样的遇到了静态创建对象的问题，此时修改产品类型，就需要修改FacotryA的具体类型。假如我们我们针对FactoryA也做一个托管，根据配置去创建具体类型，那么等于在简单工厂里的职能过重的现象又出现在了factory创建的过程，问题只是发生了转移而不是解决，所以我们采用反射方法来获取factroy类型， xml为：
```
<!— config.xml -->  
<?xml version="1.0"?>  
<config>  
    <className>FactoryA1</className>  
</config>
```
反射工具类为：
```
class BeanUtil{
    public static Object getBean(String className){
        try{
            Class c=Class.forName(cName);  
            Object obj=c.newInstance();  
            return obj; 
        }catch(Exception e){
            e.printStacktrace();
            return null;
        }
    }    
}


```
改造后Main的实现为：
```
class Main{
    
    public static void main(String args){
        String factoryName = XMLUtil.getNodeContent("config.xml", "factoryName");
        FactoryA factoryA = (FactoryA) BeanUtil.getBean(factoryName);
        ProductA productA = factoryA.createProductA();
        productA.doSometing();
    }
}
```
这样如果变更产品，只需要配置factory类型。但是为什么不直接用反射方法来确定产品类型呢，因为产品的创建可能需要比较复杂的初始化逻辑，如果把这个逻辑全部配置xml中，就演变成了依赖注入功能，例如hibernate框架实现的逻辑一样。对于实例化不是主要业务场景的需求来说，将创建工作封装在工厂中就可以很好地满足需求了。

## 抽象工厂模式

工厂模式已经解决了静态创建的问题，新建或修改产品类型，只需要修改配置文件即可，符合开闭原则。但是由于每新增一个产品，都需要新增一个工厂类，当产品过多的时候，就会导致factory数量非常多，增加了工作量和维护难度。

现在假如我们我们需要设计一个皮肤库，我们针对每一个控件类型都有一个外观产品：  

![](http://img.blog.csdn.net/20130713161528750?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTG92ZUxpb24=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

如果我们使用工厂方法来实现：

![](http://img.blog.csdn.net/20130713161546250?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTG92ZUxpb24=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

可以看见，这么做有两个明显的弊端：
* 每增加一个风格，就要增加三个产品类型和三个工厂类型，数量繁多。
* 这些控件都是同时显示的，如果某个具体的工厂配置错误，会导致界面风格混乱，虽然我们可以增加一些约束语句，但客户端代码和配置文件都较为复杂。

要解决这个问题，就要减少factory的数量，打破factory和product的一一对应关系，这让我们想起来之前的简单工厂模式中，factory是负责创建同一类产品的，我们先看一下这里的问题。

可以发现按钮、文本框、组合框等是成一个“套装”的，每个风格都是这个套装的不同样式，我们把这种总是一起使用的一些产品，叫产品族。每一个套装中都有一个按钮，这些按钮组成的划分，我们叫产品结构。

![](http://img.blog.csdn.net/20130713162941328?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTG92ZUxpb24=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

简单工厂方法里的factory其实就是针对产品结构的工厂方法，一个工厂只生产一个结构的产品，具体是那款，由运行时传入的外部信息决定。假如我们使用简单工厂方法来减少factory数量，那么由于一个产品族的产品需要同时被使用，我们仍然需要配置三个产品类型，并且简单工厂的工厂职能过重问题也依然存在。那么我们如果使用产品族的工厂方法呢，我们扩展工厂方法中的factory，使它能生产同一族的所有产品。
```
interface Factory{
    ProductA createProductA();
    ProductB createProductB();
    ProductC createProductC();
}

Factory1{
    ProductA createProductA(){
        return new ProductA1();
    }
    
    ProductB createProductB(){
        return new ProductB1();
    }
    ProductC createProductC(){
        return new ProductC1();
    }
}

<!— config.xml -->  
<?xml version="1.0"?>  
<config>  
    <className>Factory1</className>  
</config>

class Main{
    
    public static void main(String args){
        String factoryName = XMLUtil.getNodeContent("config.xml", "factoryName");
        Factory factory = (Factory) BeanUtil.getBean(factoryName);
        ProductA a = factory.createProductA();
        a.doSometing();
        ProductB b = factory.createProductB();
        b.doSometing();
        ProductC c = factory.createProductC();
        c.doSometing();
    }
}

```
我们发现针对产品族的工厂方法，和工厂方法一样，只需要配置一个工厂类型，并且没增加一族产品，只需要新增一个产品类，既符合开闭原则，也简便易用，这就是抽象工厂方法。

抽象工厂方法也有一个弊端，假如需要新增一个产品结构，需要修改Factory接口和每一个Factory具体类型，并且还需要修改客户端代码。但是在没有使用抽象工厂的情况下，新增产品结构也是需要改动产品的创建代码，使用代码。在使用简单工厂的情况下，需要新增产品的工厂，在使用工厂方法的情况下，需要新增一个等级结构的工厂类，相比较之下，抽象工厂方法还是最优方法，所以，只是说在设计产品族的时候，要尽量保证全面。

## 工厂方法的使用比较：

* 简单工厂方法  
简单工厂方法只能创建同一结构的产品，当结构中的差异产品较少时使用。

* 工厂方法  
工厂方法只推荐用来创建同一结构的产品，当结构中差异产品较多时使用。

* 抽象工厂方法  
抽象工厂方法推荐用来创建产品族，当产品成族被使用的时候使用，否则使用工厂方法更简单。

## 工厂方法的弊端

工厂模式，或者说模式的一个弊端是都会增加系统的类的数量，从而增加系统复杂性和实现难度。并且由于工厂模式利用了多态，同时也增加了代码直接理解难度（了解模式除外）。