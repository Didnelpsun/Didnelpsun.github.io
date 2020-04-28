---
layout: post
title:  "面向对象（下）"
date:   2019-09-26 23:54:40 +0800
categories: notes java base
tags: java 基础 内部类
excerpt: "内部类"
---

## 内部类

内部类就是在一个类内部定义的类。内部类可以分为成员内部类，局部内部类，静态内部类，匿名内部类。这就是封装的概念。通过类进行封装，保护内部的实现。

总的来说Java内部类的用法：

1. 内部类可以用多个实例，每个实例都有自己的状态信息，并且与其他外围对象的信息相互独立。
2. 在单个外围类中，可以让多个内部类以不同的方式实现同一个接口，或者继承同一个类。
3. 创建内部类对象的时刻并不依赖于外围类对象的创建。
4. 内部类并没有令人迷惑的"is-a"关系，他就是一个独立的实体。
5. 内部类提供了更好的封装，除了该外围类，其他类都不能访问。

### &emsp;1. 成员内部类

在一个类中使用内部类，可以在内部类中直接存取其所在类的私有变量成员。和成员变量是一样的，属于类的全局成员。  

<span style="color:aqua">格式：</span>

```java
权限修饰符 class 外部类名{  
    权限修饰符 class 内部类名{  
        ...
    }  
}
```

#### &emsp;&emsp;1.1说明

成员内部类也是最普通的内部类，它是外围类的一个成员，所以他是可以无限制的访问外围类的所有成员属性和方法，尽管是`private`的，但是外围类要访问内部类的成员属性和方法则需要通过内部类实例来访问。  

且注意内部类不能定义为public权限，因为同一个文件只能有一个public。

在成员内部类中要注意两点，第一：成员内部类中不能存在任何`static`的变量和方法；第二：成员内部类是依附于外围类的，所以只有先创建了外围类才能够创建内部类。  

内部类的实例一定要绑定在外部类的实例上，如果从外部类中初始化一个内部类对象，那么内部类对象就会被绑定到外部类对象上。内部类初始化方法与其他类初始化方法相同，都是使用`new`。也就是说需要使用内部类方法必须先实例化外部类，且类使用<span style="color:aqua">格式：</span>外部类名.内部类名。

如:

```java
public class OuterClass{
    innerClass in = new innerClass(); //在外部类实例化内部类对象引用
    public void ouf(){
        in.inf(); //在外部类方法中调用内部类方法
    }
    class innerClass{
        innerClass(){ //内部类构造方法
        }
        public void inf(){ //内部类成员方法
        }
        int y = 0; //定义内部类成员变量
    }
    public innerClass doit(){ //外部类方法，返回值为内部类引用
    //y = 4; //外部类不可以直接访问内部类成员变量
        in.y = 4;
        return new innerClass();
    }
    public static void main(String[] args){
        OuterClass out = new OuterClass();
//内部类的对象实例化操作必须在外部类或者外部类的静态方法中实现
        OuterClass.innerClass in = out.doit();
        OuterClass.innerClass in2 = out.new innerClass();
    }
}
```

外部类创建内部类实例与其他类创建对象引用时相同。内部可以访问其外部类成员，但是内部类成员只有内部类内才可以使用，外部类不能直接使用。  

如果在外部类和非静态方法之外实例化内部类对象，需要使用外部类，内部类的形式指定该对象的类型。内部类依赖外部类的存在而存在。

内部类是个编译时的概念，一旦编译成功后，它就与外围类属于两个完全不同的类（当然他们之间还是有联系的）。对于一个名为OuterClass的外围类和一个名为InnerClass的内部类，在编译成功后，会出现这样两个class文件：OuterClass.class和OuterClass$InnerClass.class。所以可以把还没有定义的内部类写在前面。

#### &emsp;&emsp;1.2内部类向上转型为接口

如果将一个权限修饰符为`private`的内部类向上转型为父类对象，或者直接向上转型为一个接口，那么就可以隐藏内部类具体实现过程。可以在外部提供一个接口，在接口中声明一个方法。如果在实现该接口的内部类中实现该接口的方法，，那么就可以定义多个内部类以不同的方式实现一个接口的同一方法，而一般类是无法多次实现接口中同一方法的。（类似于重载）

```java
interface OutInterface{ //定义一个接口
    public void f();
}
public class InterfaceInner{
    public static void main(String[] args){
        OuterClass out = new OuterClass(); //实例化一个OuterClass2对象
        OutInterface outinter = out.doit(); //调用doit()方法，返回一个OutInterface接口
        outinter.f(); //调用f()方法
    }
}
class OuterClass{ //定义一个内部类实现OutInterface接口
    private class InnerClass implements OutInterface{
        InnerClass(String s){ //内部类构造方法
            System.out.println(s);
        }
        public void f(){
            System.out.println("访问内部类中的f()方法");
        }
    }
    public OutInterface doit(){ //定义一个方法，返回类型为OutInterface接口
        return new InnerClass("访问内部类构造方法");
    }
}
```

OuterClass类中定义了一个修饰权限为private的内部类，这个内部类实现了OutInterface接口，后修改doit()方法，使该方法返回一个OutInterface接口。由于内部类InnerClass的修饰权限为private，所以只有其父类，即OuterClass类可以访问该内部类，可以访问doit()方法。由于该方法返回的是一个外部接口类型，这个接口可以作为外部使用的接口，其包含一个f()方法。在继承此方法的内部类中实现了此方法，如果某个类继承了外部类，由于内部的权限不能向下转型为内部类InnerClass，同时也不能访问f()方法，但是可以访问接口中的f()方法。如InterfaceInner类中的最后一条语句，接口调用f()方法，可以看出其执行的是内部类的f()方法，也就是对继承该类的子类隐藏了实现方法，隐藏了f()方法的具体实现，仅留下接口和外部类。这也是内部类的基础使用方法。  

非内部类不能被声明为`private`或者`protected`访问类型。

#### &emsp;&emsp;1.3使用this关键字获取内部类与外部类的引用

如果在外部类中定义的成员变量与内部类的成员变量名称相同，可以使用`this`关键字。

<span style="color:aqua">格式：</span>`内部类当前对象的变量：this.变量名` / `当前对象外部类的变量：外部类名.this.变量名`  

```java
public class TheSameName{
    private int x;
    private class Inner{
        public void doit(int x){ //传入的是形参x
            x++; //调用形参x
            this.x++； //调用内部类的变量x
            TheSameName.this.x++;
        }
    }
}
```

### &emsp;2. 局部内部类

内部类不仅可以在类中定义，也可以在类的局部区域定义，如类的方法，选择和循环语句中等。局部内部类只在局部区域中有效。  

对于这个类的使用主要是应用与解决比较复杂的问题，想创建一个类来辅助我们的解决方案，到那时又不希望这个类是公共可用的，所以就产生了局部内部类。  

```java
interface OutInterface{ //定义一个接口
}
class OuterClass{
    public OutInterface doit(final String x){
    //doit方法参数设置为final类型，也就是无法修改参数
    //定义一个内部类
        class InnerClass implements OutInterface{
            InnerClass(String s){
                s = x;
                System.out.println(s);
            }
        return new InnerClass("doit");
    }
}
```

内部类被定义在doit()方法内部，但是内部类InnerClass是doit()方法的一部分，而不是OuterClass的一部分，所以doit方法外部无法访问内部类额，但是该内部类可以访问外部类和方法的成员。  

由于参数x被设置为`final`类型，所以是无法更改其值，因为其生命周期超越方法的生命周期，被认为是一个常量。

同样局部内部类也可以使用外部类中所有的成员方法和成员变量，因为是内部的。

### &emsp;3. 静态内部类

同成员内部类不同，它可以直接创建使用，而不用依赖外部类的实现，使用static关键字来实现。即其实是内部类的一种特殊情况。

<span style="color:aqua">格式：</span>

```java
权限修饰符 class 外部类名{  
    static class 内部类名{  
        ...
    }  
}
```

### &emsp;3. 匿名内部类

即没有名称的内部类。因为匿名类没有类名，所以不能直接定义对象，需要借助父类构造器定义对象。

使用匿名类是因为我们不想在别的地方被使用而仅在这里使用，只被执行一次，能简介代码。

<span style="color:aqua">格式：</span>

```java
new 父类(){
    //匿名类的类体
    成员变量;
    成员方法;
}
```

```java
class A{
    void show(){
        System.out.println("A class");
    }
}
public class Test{
    public static void main(String[] args){
        A a = new A();
        a.show();
        A aa = new A(){
            void show(){
                System.out.println("A noname class");
            }
        }
        aa.show();
    }
}
```

请注意，如果你要编译这个文件，那么你会得到三个class文件，一个是A.class，一个是Test.class，一个是Test$1.class，最后一个文件即匿名类编译后的类文件。$1就代表第一个匿名类，依次排序。

#### &emsp;&emsp使用匿名类实现接口

我们之前实现接口，需要类继承接口再实例化，并把实例赋值给接口。这样是十分麻烦的。而如果我们使用了匿名类，那么我们可以直接在接口后使用匿名类实现接口。

```java
//首先定义一个接口
interface SpeakHello{
    void speak();
}
//定义一个类，传入接口类型的实例
class HelloTv{
    public void turnOn(SpeakHello hello){
        //调用实例方法
        hello.speak();
    }
}
public class Test{
    public static void main(String[] args){
        //创建一个实例
        HelloTv tv = new HelloTv();
        //传入接口类型的方法，并使用匿名类直接定义重写
        tv.turnOn(new SpeakHello(){
            public void speack(){
                System.out.println("Hello!Welcome!");
            }
        })
    }
}
```