---
title: ASP.NET中的设计模式——原型模式
date: 2018-04-24 12:32:12
categories: 技术文章
tags:
    - ASP.NET
    - C#
    - 设计模式
    - 原型模式
---

# **前言**

&emsp;&emsp;最近在工作室课上在讲ASP.NET的设计模式，恰巧看到设计模式中的原型模式与JavaScript中的继承机制——原型链有异曲同工之妙，便深入研究了一下。

&emsp;&emsp;在实际的程序开发中，使用一个最适合的设计模式往往能够起到事半功倍的效果。原型模式的使用场景为：

* 资源优化场景：类初始化需要消化非常多的资源，这个资源包括数据、硬件资源等。
* 性能和安全要求的场景：通过new产生一个对象需要非常繁琐的数据准备或访问权限，则可以使用原型模式。
* 一个对象多个修改者的场景：一个对象需要提供给其他对象访问，而且各个调用者可能都需要修改其值时，可以考虑使用原型模式拷贝多个对象供调用者使用。

&emsp;&emsp;在实际项目中，原型模式很少单独出现，一般是和工厂方法模式一起出现，通过clone的方法创建一个对象，然后由工厂方法提供给调用者。

&emsp;&emsp;现将本人学习心得分享与此以方便大家更好地学习掌握原型模式。

<!-- more -->

# **问题引入**

&emsp;&emsp;当运行以下代码时，会产生什么样的结果呢？

``` cs
Person a = new Person("Jack",20);
Person b = a;
b.SetInfo("John",21);
a.Display();    // a显示的信息是什么？
```

&emsp;&emsp;答案是：（看下面！！！）

```
John 21
```

# **为什么**

&emsp;&emsp;通过以下图片我们可以看到对象的值的传递情况

![对象的值的传递](https://github.com/DM2N/personal-img/raw/master/blog/prototype-pattern/p1.png)

![对象的值的传递](https://github.com/DM2N/personal-img/raw/master/blog/prototype-pattern/p2.png)

&emsp;&emsp;Person b = a后，即将a的值赋值给了b，此时a和b都同时指向同一个堆里，b.SetInfo("John",21)即改变了堆里的值，而a的值仍然是从堆里获取，所以a.Display()的值为John 21。

![对象的值的传递](https://github.com/DM2N/personal-img/raw/master/blog/prototype-pattern/p3.png)

![对象的值的传递](https://github.com/DM2N/personal-img/raw/master/blog/prototype-pattern/p4.png)

&emsp;&emsp;但它们是如何做到对象的值的传递？请学习原型模式！！！

# **原型模式**

## **原型模式介绍**

&emsp;&emsp;维基百科：原型模式（Prototype Pattern）是创建型模式的一种，其特点在于通过「复制」一个已经存在的实例来返回新的实例，而不是新建实例。被复制的实例就是我们所称的「原型」（Prototype），这个原型是可定制的。

## **原型模式的UML类图**

![原型模式的UML类图](https://github.com/DM2N/personal-img/raw/master/blog/prototype-pattern/p5.png)

## **原型模式的简单实现**

*申明类：*

``` cs
// 抽象原型类:声明克隆自身的接口
public interface Prototype
{
    Prototype Clone();
}

// 具体原型类:实现克隆的具体操作
public class ConcretePrototype1 : Prototype
{
    public Prototype Clone()
    {
        Console.WriteLine("ConcretePrototype1 Cloned!");
        return (Prototype)this.MemberwiseClone();
    }
}

public class ConcretePrototype2 : Prototype
{
    public Prototype Clone()
    {
        Console.WriteLine("ConcretePrototype2 Cloned!");
        return (Prototype)this.MemberwiseClone();
    }
}
```

*主程序调用：*

``` cs
// 客户类:让一个原型克隆自身，从而获得一个新的对象
public class Client
{
    static void Main(string[] args)
    {
        ConcretePrototype1 obj1 = new ConcretePrototype1();
        ConcretePrototype1 obj2 = new ConcretePrototype2();

        ConcretePrototype1 cloneObj1 = (ConcretePrototype1)obj1.Clone();
        ConcretePrototype1 cloneObj2 = (ConcretePrototype2)obj2.Clone();
    }
}
```

*程序运行结果：*

```
ConcreteProtype1 Cloned!
ConcreteProtype2 Cloned!
```

## **实现ICloneable接口**

&emsp;&emsp;.NET在System命名空间中提供了ICloneable接口，其中只包含一个Clone()方法，实现了这个接口就是完成了原型模式。

![实现ICloneable接口](https://github.com/DM2N/personal-img/raw/master/blog/prototype-pattern/p6.png)

## **浅拷贝与深拷贝**

### **浅拷贝（Shallow Copy）**

* 只复制对象的值类型字段，引用类型只复制引用不复制引用的对象（即复制地址）
* MemberwiseClone() 是浅拷贝（[MSDN关于MemberwiseClone()的介绍]()https://docs.microsoft.com/zh-cn/dotnet/api/system.object.memberwiseclone?view=netframework-4.7.1#System_Object_MemberwiseClone）

![浅拷贝](https://github.com/DM2N/personal-img/raw/master/blog/prototype-pattern/p7.png)

### **深拷贝（Deep Copy）**

* 不仅复制值类型字段，而且复制引用的对象

![深拷贝](https://github.com/DM2N/personal-img/raw/master/blog/prototype-pattern/p8.png)

#### **实现深拷贝**

![实现深拷贝](https://github.com/DM2N/personal-img/raw/master/blog/prototype-pattern/p9.png)

*简历和工作经历类：*

``` cs
// 简历
public class Resume : ICloneable
{
    public string Name { get; set; }
    private WorkExperience work;

    public void SetWorkExperience(string workDate, string company)
    {
        work.WorkDate = workDate;
        work.Company = company;
    }

    public void Display()
    {
        Console.WriteLine(
            $"{ Name } Worded in { work.Company } from { work.WorkDate }.");
    }

    public Resume(string name)
    {
        Name = name;
        work = new WorkExperience();
    }

    public object Clone()
    {
        Resume cloned = new Resume(this.Name);
        cloned.work = (WorkExperience)this.work.Clone();  // 克隆工作经历
        return cloned;
    }
}

// 工作经历
public class WorkExperience : ICloneable
{
    public String WorkDate { get; set; }
    public String Company { get; set; }

    public object Clone()
    {
        return this.MemberwiseClone();
    }
}
```

*主程序调用：*

``` cs
public class Program
    {
        static void Main(string[] args)
        {
            Resume resume = new Resume("Jack");
            resume.SetWorkExperience("2012-2015", "XX公司");

            Resume cloned = (Resume)resume.Clone();
            cloned.SetWorkExperience("2015-2018", "YY公司");

            resume.Display();
            cloned.Display();
        }
    }
```

*程序运行结果：*

```
Jack worked in XX公司 from 2012-2015
Jack worked in YY公司 from 2015-2018
```

## **原型模式的应用**

### **JavaScript继承机制——原型链**

参考文章：[阮一峰《Javascript 继承机制的设计思想》](http://www.ruanyifeng.com/blog/2011/06/designing_ideas_of_inheritance_mechanism_in_javascript.html)

![JavaScript继承机制——原型链](https://github.com/DM2N/personal-img/raw/master/blog/prototype-pattern/p10.png)

&emsp;&emsp;JavaScript的创始人Brendan Eich在开发JavaScript这个使得浏览器可以与网页互动的脚本易语言时，正是面向对象编程（object-oriented programming）最兴盛的时期，C++是当时最流行的语言，而Java语言的1.0版即将于第二年推出，Sun公司正在大肆造势。

&emsp;&emsp;Brendan Eich无疑受到了影响，Javascript里面所有的数据类型都是对象（object），这一点与Java非常相似。但是，他随即就遇到了一个难题，到底要不要设计"继承"机制呢？如果真的是一种简易的脚本语言，其实不需要有"继承"机制。但是，Javascript里面都是对象，必须有一种机制，将所有对象联系起来。所以，Brendan Eich最后还是设计了"继承"。但是，他不打算引入"类"（class）的概念，因为一旦有了"类"，Javascript就是一种完整的面向对象编程语言了，这好像有点太正式了，而且增加了初学者的入门难度。他考虑到，C++和Java语言都使用new命令，生成实例。因此，他就把new命令引入了Javascript，用来从原型对象生成一个实例对象。但是，Javascript没有"类"，怎么来表示原型对象呢？

&emsp;&emsp;这时，他想到C++和Java使用new命令时，都会调用"类"的构造函数（constructor）。他就做了一个简化的设计，在Javascript语言中，new命令后面跟的不是类，而是构造函数。

&emsp;&emsp;举例来说，现在有一个叫做DOG的构造函数，表示狗对象的原型。

``` js
function DOG(name){
    this.name = name;
}
```

&emsp;&emsp;对这个构造函数使用new，就会生成一个狗对象的实例。

``` js
var dogA = new DOG('大毛');
alert(dogA.name);   // 大毛
```

&emsp;&emsp;注意构造函数中的this关键字，它就代表了新创建的实例对象。

&emsp;&emsp;但是用构造函数生成实例对象，有一个缺点，那就是无法共享属性和方法。比如，在DOG对象的构造函数中，设置一个实例对象的共有属性species。然后，生成两个实例对象：

``` js
function DOG(name){
　　this.name = name;
　　this.species = '犬科';
}

var dogA = new DOG('大毛');
var dogB = new DOG('二毛');
```

&emsp;&emsp;这两个对象的species属性是独立的，修改其中一个，不会影响到另一个。

``` js
dogA.species = '猫科';
alert(dogB.species);   // 显示"犬科"，不受dogA的影响
```

&emsp;&emsp;考虑到这一点，Brendan Eich决定为构造函数设置一个prototype属性。

&emsp;&emsp;这个属性包含一个对象（以下简称"prototype对象"），所有实例对象需要共享的属性和方法，都放在这个对象里面；那些不需要共享的属性和方法，就放在构造函数里面。

&emsp;&emsp;实例对象一旦创建，将自动引用prototype对象的属性和方法。也就是说，实例对象的属性和方法，分成两种，一种是本地的，另一种是引用的。

&emsp;&emsp;还是以DOG构造函数为例，现在用prototype属性进行改写：

``` js
function DOG(name){
　　this.name = name;
}

DOG.prototype = { species : '犬科' };

var dogA = new DOG('大毛');
var dogB = new DOG('二毛');

alert(dogA.species);   // 犬科
alert(dogB.species);   // 犬科
```

&emsp;&emsp;现在，species属性放在prototype对象里，是两个实例对象共享的。只要修改了prototype对象，就会同时影响到两个实例对象。

``` js
DOG.prototype.species = '猫科';

alert(dogA.species);   // 猫科
alert(dogB.species);   // 猫科
```

### **数据模型缓存**

![数据模型缓存](https://github.com/DM2N/personal-img/raw/master/blog/prototype-pattern/p11.png)

&emsp;&emsp;实现示例：创建一个抽象类 CloneableModel，并让类 User、Product 来扩展它；然后定义 ModelCache 类，该类把 CloneableModel 对象存储在 HashTable 中，并在请求的时候返回对应类型的克隆对象。

![数据模型缓存](https://github.com/DM2N/personal-img/raw/master/blog/prototype-pattern/p12.png)

*CloneableModel类定义及扩展：*

``` cs
using System;

// 可克隆模型
// 实现ICloneable接口
public abstract class CloneableModel : ICloneable
{
    public object Clone()
    {
        return this.MemberWiseClone();
    }
}

// 用户
public class User : CloneableModel
{
    public string Id { get; set; }
    public string Name { get; set; }
}

// 商品
public class Product : CloneableModel
{
    public string Id { get; set; }
    public string Name { get; set; }
    public string Price { get; set; }
}
```

*ModelICache类定义：*

``` cs
using System;
using System.Collections;

public class ModelCache
{
    static Hashtable modelMap = new Hashtable();

    // 将模型类对象存储到Hashtable中
    // 实际开发中，模型对象从数据库中取出
    public static void Load()
    {
        User user = new User { 
            Id = "#1001", 
            Name = "John Doe" 
        };
        modelMap.Add(user.Id, user);

        Product product = new Product { 
            Id = "#2001", 
            Name = "Illustrated C# 2012", 
            Price = "￥89.00" 
        };
        modelMap.Add(product.Id, product);

        Console.WriteLine("Db Models Cache loading ... Down!");
    }

    public static T GetModel<T>(string id) where T : class, new()
    {
        CloneableModel cache = (CloneableModel)modelMap[id];

        return cache != null
            ? (T)cache.Clone()    // clone model from cache
            : null;
    }
}
```

*主程序调用：*

``` cs
class Program
{
    static void Main(string[] args)
    {
        ModelCache.Load();

        User userCloned = ModelCache.GetModel<User>("#1001");
        Product productCloned = ModelCache.GetModel<Product>("#2001");

        if (userCloned == null || productCloned == null)
            return;

        Console.WriteLine(
            $"ID: { userCloned.Id } Name: { userCloned.Name }");

        Console.WriteLine(
            $"ID: { productCloned.Id } Name: { productCloned.Name } Price: { productCloned.Price }");
    }
}
```

*程序运行结果：*

``` c#
Db Models Cache Loading ... Down!
ID: #1001 Name: John Doe
ID: #2001 Name: Illustrated C# 2012 Price: ￥89.00
```