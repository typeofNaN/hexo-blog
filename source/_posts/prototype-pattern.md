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

# 前言

最近在工作室课上在讲ASP.NET的设计模式，恰巧看到设计模式中的原型模式与JavaScript中的继承机制——原型链有异曲同工之妙，便深入研究了一下。

在实际的程序开发中，使用一个最适合的设计模式往往能够起到事半功倍的效果。原型模式的使用场景为：

* 资源优化场景：类初始化需要消化非常多的资源，这个资源包括数据、硬件资源等。
* 性能和安全要求的场景：通过new产生一个对象需要非常繁琐的数据准备或访问权限，则可以使用原型模式。
* 一个对象多个修改者的场景：一个对象需要提供给其他对象访问，而且各个调用者可能都需要修改其值时，可以考虑使用原型模式拷贝多个对象供调用者使用。

在实际项目中，原型模式很少单独出现，一般是和工厂方法模式一起出现，通过clone的方法创建一个对象，然后由工厂方法提供给调用者。

现特将本人学习心得分享与此以方便大家更好地掌握学习原型模式。

<!-- more -->

# 问题引入

当运行以下代码时，会产生什么样的结果呢？

``` c#
Person a = new Person("Jack",20);
Person b = a;
b.SetInfo("John",21);
a.Display();    // a显示的信息是什么？
```

答案是：（看下面！！！）

```
John 21
```

# 为什么

通过以下图片我们可以看到对象的值的传递情况

![对象的值的传递](https://github.com/DM2N/personal-img/raw/master/blog/prototype-pattern/p1.png)

![对象的值的传递](https://github.com/DM2N/personal-img/raw/master/blog/prototype-pattern/p2.png)

![对象的值的传递](https://github.com/DM2N/personal-img/raw/master/blog/prototype-pattern/p3.png)

![对象的值的传递](https://github.com/DM2N/personal-img/raw/master/blog/prototype-pattern/p4.png)

但它们是如何做到对象的值的传递？请学习原型模式！！！

# 原型模式

## 原型模式介绍

维基百科：原型模式（Prototype Pattern）是创建型模式的一种，其特点在于通过「复制」一个已经存在的实例来返回新的实例，而不是新建实例。被复制的实例就是我们所称的「原型」（Prototype），这个原型是可定制的。

## 原型模式的UML类图

![原型模式的UML类图](https://github.com/DM2N/personal-img/raw/master/blog/prototype-pattern/p5.png)

## 原型模式的简单实现

申明类：

``` c#
public interface Prototype
{
    Prototype Clone();
}

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

主程序调用：

``` c#
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

程序运行结果：

```
ConcreteProtype1 Cloned!
ConcreteProtype2 Cloned!
```

## 实现ICloneable接口

.NET在System命名空间中提供了ICloneable接口，其中只包含一个Clone()方法，实现了这个接口就是完成了原型模式。

![实现ICloneable接口](https://github.com/DM2N/personal-img/raw/master/blog/prototype-pattern/p6.png)

## 深拷贝与浅拷贝

### 浅拷贝（Shallow Copy）

* 只复制对象的值类型字段，引用类型只复制引用不复制引用的对象（即复制地址）
* MemberwiseClone() 是浅拷贝（MSDN）

![浅拷贝](https://github.com/DM2N/personal-img/raw/master/blog/prototype-pattern/p7.png)

### 深拷贝（Deep Copy）

* 不仅复制值类型字段，而且复制引用的对象

![深拷贝](https://github.com/DM2N/personal-img/raw/master/blog/prototype-pattern/p8.png)

#### 实现深拷贝

![实现深拷贝](https://github.com/DM2N/personal-img/raw/master/blog/prototype-pattern/p9.png)

简历和工作经历类：

``` c#
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

主程序调用：

``` c#
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

程序运行结果：

```
Jack worked in XX公司 from 2012-2015
Jack worked in YY公司 from 2015-2018
```

## 原型模式的应用

### JavaScript继承机制——原型链

![JavaScript继承机制——原型链](https://github.com/DM2N/personal-img/raw/master/blog/prototype-pattern/p10.png)

参考文章：[阮一峰《Javascript 继承机制的设计思想》](http://www.ruanyifeng.com/blog/2011/06/designing_ideas_of_inheritance_mechanism_in_javascript.html)

### 数据模型缓存

![数据模型缓存](https://github.com/DM2N/personal-img/raw/master/blog/prototype-pattern/p11.png)

实现示例：创建一个抽象类 CloneableModel，并让类 User、Product 来扩展它；然后定义 ModelCache 类，该类把 CloneableModel 对象存储在 HashTable 中，并在请求的时候返回对应类型的克隆对象。

![数据模型缓存](https://github.com/DM2N/personal-img/raw/master/blog/prototype-pattern/p12.png)

CloneableModel类定义及扩展：

``` c#
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

ModelICache类定义：

``` c#
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

主程序调用：

``` c#
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

程序运行结果：

``` c#
Db Models Cache Loading ... Down!
ID: #1001 Name: John Doe
ID: #2001 Name: Illustrated C# 2012 Price: ￥89.00
```