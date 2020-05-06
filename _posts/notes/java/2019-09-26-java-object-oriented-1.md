---
layout: post
title:  "面向对象（上）"
date:   2019-09-26 19:18:15 +0800
categories: notes java base
tags: java 基础 继承 extends super 重构 final Object getClass toString equals 转型 instanceof 重载 不定长参数 多态 抽象 abstract 接口 interface implements 
excerpt: "继承转型多态抽象与接口"
---

## 继承

### &emsp;1. extends和super关键字

类的继承在于基于某个父类，指定一个新子类，子类拥有父类原有的属性和方法，并有自己独有的属性与方法，甚至可以重写父类的属性方法。  

`extends`关键字说明继承，`super`关键字调用父类。

```java
class Test{
    public Test(){ //构造方法，没有返回值且方法名和类名一致
    }
    protected void doSometing(){ //成员方法
    }
    protected Test doIt(){ //方法返回值为Test类型
        return new Test();
    }
}
class Test2 extends Test{ //定义Test2继承Test类
    public Test2(){ //定义Test构造方法
        super(); //super是父类，super()调用父类
        super.doSomething(); //调用父类成员方法
    }
    public void doSomethingnew(){ //定义子类新方法
    }
    public void doSomething(){ //重写父类方法
    }
    protect Test2 doIt(){ //重写父类方法，返回值为Test2
        return new Test2();
    }
}
```

首先定义了两个类Test和Test2，其中Test2继承了Test类，Test类是Test2的父类。

子类初始化可以连同初始化父类构造方法，既可以在子类中调用`super()`来调用父类构造方法，也可以在子类中使用`super`关键字来调用父类成员。但是子类无法调用`private`权限的成员。  

如果子类和父类的成员方法返回值，方法名称，参数类型与个数都完全相同，不同的仅仅是实现内容，那这种构造方法就是<span style="color:yellowgreen">重构</span>。  

当重写父类方法时，修改方法的权限只能从小权限向大权限改，如如果父类成员方法权限为`protected`，那么子类重写方法只能改为`protected`或者`public`。

当子类重写父类的方法还可以修改返回值类型，但是重写的返回值类型只能时父类同一方法返回值的子类，如返回Test类型值的方法修改后返回类型为Test2就可以，而一般的Int类型就不行，因为不是其原来返回值类型的子类。

Java中一切都以对象的方式进行处理，在继承中，创建一个子类对象，将包含一个父类子对象，这个对象与单用父类构造函数创建的对象是一样的。两者的区别是后者是来自外部，而前者来自子类对象内部。也就是说当实例化子类对象的时候父类对象也同样会被实例化，即实例化子类对象时，Java编译器会在子类的构造方法中自动调用父类的无参构造方法。

如：

```java
class Parent{
    Parent(){
    System.out.println("父类构造方法");
    }
}
class Child extends Parent{
    Child(){
        System.out.println("子类构造方法");
    }
}
public class GrandChild extends Child{
    GrandChild(){
        System.out.println("孙子类构造方法");
    }
    public static void main(String[] args){
        GrandChlid g = new GrandChild();
    }
}
>>>>>
父类构造方法
子类构造方法
孙子类构造方法
```

所以可知当实例化一个子类对象，会首先依次实例化其父类对象。  

在实例化子类对象时，父类无参构造方法会被自动调用，但是有参构造函数不会被自动调用，只能依赖`super`关键字显示地被调用父类构造方法，并在括号中传入参数。且super语句需要放到子类构造器的第一行，不然会出错。  

如果使用`finalize()`方法对对象进行显示清理，需要确保子类的`finalize()`方法的最后一个动作是调用父类的`finalize()`方法，以保证父类的垃圾也能被回收。

### &emsp;2. final方法与类

#### &emsp;&emsp;2.1final参数

如果声明一个final参数，就代表其参数不能被改变。也就是说传入的参数只能被读取，不能被修改。

#### &emsp;&emsp;2.2final方法

父类中的`final`方法可以被子类继承，但是不能被子类重写。同时`final`方法执行等级高于非`final`方法。

声明`final`方法的主要目的是防止该方法的内容被修改。

已知如果一个父类的一个方法被设置为`private`权限，那么子类无法访问该方法。既然无法访问，那就更无法修改，所以一个定义为`private`的方法隐式地指定为`final`权限。

如下所示，使用`final`修饰符声明方法。

```java
public class Test{
    public final void changeName(){
       // 方法体
    }
}
```

#### &emsp;&emsp;2.3final属性

这个属性可以被继承，但是不可以被子类重写。

#### &emsp;&emsp;2.4final类

final类不能被继承，没有类能够继承final类的任何特性，不可以有子类，只可以被使用。

```java
public final class Test {
   // 类体
}
```

final类的属性可以是final的也可以是其他的权限，也就是说对于非final权限的属性是可以被更改值的，而方法是默认final的。

&emsp;

## Object类

使用`class`关键字定义类时，就开始使用继承，因为在Java中所有的类都继承了`java.lang.Object`类。`Object`类比较特殊，是所有类的父类，是Java类层的最高层。

当创建一个类除非是显示继承其他类，否则就是由`java.lang.Object`类继承而来的，如`String`类等，自定义的类也是继承`Object`类，由于全部的类都是`Object`类的子类，所以省略`extends Object`关键字。

由于所有的类都是`Object`类的子类，所以所有类都可以重写`Object`类中的方法。  

但是`getClass()`方法、`notify()`方法、`notifyAll()`方法、`wait()`方法等方法不可被重写，因为其为`final`类型，即用`final`关键字修饰，不可修改与被继承。

### &emsp;1. getClass()

是`Object`类的方法，返回对象执行时的`Class`实例，然后使用此实例调用`getName()`方法可以获取改类的名称。  

<span style="color:aqua">格式：</span>`getClass().getName();`

### &emsp;2. toString()

将一个对象返回为字符串的形式，将返回一个`String`实例，实际使用时会重写`toString`方法，为对象提供一个特定的输出模式，当这个类转换为字符串或字符串连接时会自动调用重写的方法。  
如：  

```java
public class Newclass{
    public String toString(){
        return "在"+getClass().getName()+"类中重写了toString()方法";
    }
    public static void main(String[] args){
        System.out.println(new Newclass());
    }
}
```

在主函数中打印创建的`Newclass`对象，应该自动调用该对象的`toString()`方法，因为在这个对象中重写了`toString`方法，所以返回的是一串自定义字符串，并写上了当前实例化的类的类名。

### &emsp;3. equals()

在已经定义过的类，如`String`类、`Number`类中`equals()`方法不同于==运算符比较两边的引用，而是比较两边的内容，实际上`equals()`方法在Object类中默认的功能就是比较其引用是否相同，而在已经包装好的`String`类等中重写了该方法变为比较内容，但是在自定义的类中，没有定义过这种功能，所以在自定义类中需要同样地重写该函数。

&emsp;

## 转型

对象类型地转换十分常见，分为向上转型和向下转型，也就是抽象和具体的转换问题。

### &emsp;1. 向上转型

```java
class Father{
    public static void `function1`(Father f){
    }
}
public class Son extends Father{
    public static void main(String args[]){
        Son s = new Son();
        function1(s);
    }
}
```

在这里定义了两个类`Father`和`Son`类，`Son`类继承`Father`类，`Father`类中定义了`function1`的方法，其传递的参数是`Father`类型的。由于`Son`类继承`Father`类，所以也可以在`Son`类中调用其`function1`的方法。但是值得注意的是在`Son`类中调用`function1`的方法传入的参数不是其定义时的`Father`类型的参数，而是`Son`类型的。  

这种把子类对象赋值给父类类型的变量的技术就是向上转型。  

为什么可以实现向上转型，这就是抽象和具体的转换，抽象就是一类事物所共有的特征，具体就是在共有的特征外还有自己独有的特征。如果一个对象是一个类的子类，那就满足一个类的所有共有特征，还有自己独有的特征，所以是符合特征的，因为抽象的范围比具体的范围更广。由于具体向抽象转型，所以其总是安全的。  

这就是多态的思想，在父类上定义所有子类所共有的特征，然后传入子类对象仍能使用，因为子类符合父类标准。但是如果你向上转型，那么你独有的属性和方法都会失效，而只会保存继承而来的属性和方法。对于向上转型更多是为了改进原来的父类。相当于按照父类模板来建造的子类对象，是子类的简化。  

上转型后的对象不能访问子类新增的成员的，但是可以访问子类继承或者屏蔽的类成员。同样上转型对象可以使用子类继承的方法和重写的方法，作用等价于子类去调用。

同时我们可以将子类向上转型变成父类对象，同时也可以向下转型重新变为子类对象，但是父类建立的对象的引用不能指向子类，因为父类没办法扩充没有的方法或者属性。

如果子类重写了父类的静态方法，则子类重写后也必须是静态的，如果子类上转型对象调用静态方法，则调用的不是子类重写的静态方法，而是父类的静态方法。

### &emsp;2. 向下转型

向下转型就是由抽象到具体，但是这种往往会出现问题，如鸽子是鸟，但是鸟不满足鸽子的独有特征。所以可以说子类对象总是父类的实例，但是不能说父类对象总是子类的实例。  
如果想向下转型，就必须使用显示类型转换：

<span style="color:aqua">格式：</span>`(目标类名)变量名;`  

但是这种向下转型往往有不安全的问题。所以就出现了Java的泛型编程。  

为什么需要向下转型？就是因为在实践中虽然将一堆不同类型的类都归于一组，但是获得了抽象性，也丢失了可扩展性。所以向下转型就可以强行获取到原来子类独有的方法。  

&emsp;

## instanceof关键字

当进行向下转型时如果父类不是子类对象的实例，就会发生ClassCastException异常，所以向下转型需要判断父类对象是否为子类对象的实例。  

<span style="color:aqua">格式：</span>`对象的引用变量 instanceof 目标类;`  

返回`true`值就是是实例对象，`false`就是不是。

且null不属于任何类的子类，包括它自己。

&emsp;

## 重载与重写

重载和重写也是体现多态的方法。

重载就是在同一个类中允许同时存在一个以上的同名方法，只需要参数个数或者参数类型不同即可。  

编译器是根据方法名、参数类型和参数个数以及参数顺序来判断类中的方法是否唯一。（返回值类型不足以判断两个方法的重载）  
如：  

```java
public class Add{
    public static int add(int a,int b){
        return a+b;
    }
    public static double add(double a,double b){
        return a+b;
    }
    public static int add(int a){
        return a;
    }
    public static int add(int a,double b){
        return 1;
    }
    public static int add(double a,int b){
        return 1;
    }
    public static void main(String[] args){
        System.out.println("add(int,int)"+add(1,2));
        System.out.println("add(double,double)"+add(1.3,2.7));
        System.out.println("add(int)"+add(1));
    }
}
```

不同的参数传入函数会实现不同的实现，所以重载的意义就是实现不同参数的适应。

如果我们要重写方法，也就是假如父类有默认的处理方法，而子类继承父类而需要改写父类处理方法，那么就需要重写方法。因为重载的要求，所以我们重写时必须要求与父类的方法名，参数类型，参数数量，参数顺序保持一致，否则就是重载而不是重写。我们也可以在子类中命名与父类中同名的属性来覆盖父类的属性。

```java
class Father{
    public void show(){
        System.out.println("Father.show()");
    }
}
class Son extends Father{
    //重写父类方法
    public void show(){
        System.out.println("Son.show()");
    }
}
public class Override{
    public static void main(String[] args){
        Son s = new Son();
        //调用子类重写方法
        s.show();
    }
}
```

假如我们要使用被父类重写的属性和方法，那么我们可以使用super.属性名/方法名来使用被覆盖掉的方法或者属性。如上面这个例子如果要调用被覆盖的方法只用`super.show()`就可以了。

同时子类重写父类方法是不可以降低父类方法的权限的，也就是说权限必须更开放，如果父类一个方法是protected，那么子类一定是protected或者public以及无权限，而不能是private。

&emsp;

## 不定长参数

<span style="color:aqua">格式：</span>`参数数据类型...参数名称`  等价于 `参数数据类型 参数名称[]`  

如int...a等价于int a[];  

```java
public static int add(int...a){
int s=0;
for(int i=0,i<a.length；i++)
s+=a[i];
return s;
}
```

定义不定长参数仍然是可以作为方法的重载。且实用方法类似数组。

&emsp;

## 抽象（abstract关键字）

如四边形都是拥有四边，等腰三角形拥有两条相等的三角形，这些描述都是十分合理的，但是对于图形，这样的类就十分抽象，是最顶级的抽的象类，所以就很难用具体的语言进行描述，所以这就是抽象类。抽象类的最主要目的就是继承扩展。

一般父类被定义为抽象类，需要使用该抽象类进行继承和多态处理。由继承机制可以得知，一般越上面的父类就越抽象，在多态机制中不需要实例化抽象类的父类，只需要子类对象，所以在Java中也不能实例化抽象类对象，所以就比如图像类不能抽象出一个具体图形，但是其子类就可以。且子类如果要实例化就必须实现抽象类中的所有方法。

最典型的例子就是在Java中没有构造Object类的构造函数，就是因为多态机制它作为抽象类不可实例化。（在JavaScript中就含有，但是它可以被实例化，所以JavaScript就不是面向对象的语言，而是基于对象的语言）

<span style="color:aqua">格式：</span>

```java
public abstract class 抽象类名{  
    abstract 权限修饰符 抽象方法名();  
}  
```

`abstract`关键字就是定义抽象类和抽象方法的关键字。

使用`abstract`关键字定义的类就是抽象类，定义的方法就是抽象方法。

抽象方法没有方法体，它本身没有任何意义，也没有任何作用，除非它在子类被重写，而承载这个抽象方法的抽象类必须被继承（抽象类的存在意义就是被继承）。

抽象方法不能被声明成`final`和`static`。

也就是说如果声明一个抽象的方法，那么承载它的类必须是抽象类，只要类中有一个抽象方法，那么它就是抽象类。  

当然抽象类中也可以含有非抽象的具体实现的方法。也就是说抽象类可以没有抽象方法。它们双方并不是充分必要条件。当然抽象类被继承后需要实现其中所有的抽象方法，也就是说要保证相同的方法名称，参数列表和相同返回值类型创建出非抽象方法，也可以是抽象方法。  

当继承抽象类的所有子类都需要将抽象类中的抽象方法都覆盖，即重写（因为抽象方法没有意义）。

因为抽象类是无法通过new来创建实例的，所以如果你要使用抽象类来创建实例，那么是需要首先使用一个子类来实现抽象类，然后再将这个子类实例上转型。

&emsp;

## 接口（interface和implements关键字）

接口是抽象类的延伸，可以当作纯粹的抽象类，接口中所有的方法都没有方法体。为什么会出现接口的概念？就是当继承的时候父类的有些方法可能对继承父类的部分子类有效而不是对所有的子类都有效，如多边形的面积求法就不同于三角形和四边形。如果把这部分功能放在父类中被继承，不需要这功能的类仍然需要重写该方法，所以为了让子类继承父类并继承部分类需要的功能，且子类不能同时继承多个父类，所以就出现了接口。让子类继承父类并实现接口。

### &emsp;1. 定义和实现

接口定义<span style="color:aqua">格式：</span>

```java
public interface 接口名{  
    接口内方法，接口中的方法都是抽象方法，不能具体实现功能。  
}
```  

实现接口<span style="color:aqua">格式：</span>

```java
public class 类名 implements 接口名{  
}  
或者  
public class 类名 extends 父类名 implements 接口名{  
}  
```

在接口中定义的方法必须被声明为`public`或者`abstract`形式，其他权限修饰符都不认可，即使不声明也是默认该形式。  

且在接口中定义的任何字段都是`static`和`final`的。

接口中亦可以有属性，不过这些都是会改变的属性，在子类会重新被重写。且相同接口的使用不代表这些类有继承的关系。

### &emsp;2. 多继承

Java不允许多继承，但是使用接口可以实现多继承，因为一个类可以同时实现多个接口，但是很可能会带来更多的代码量，因为继承一个接口就要实现其所有的方法。  

<span style="color:aqua">格式：</span>`class 类名 implements 接口1,接口2...`

同时接口可以继承其他接口<span style="color:aqua">格式：</span>

```java
interface 接口1 extends 接口2 {  
}
```

### &emsp;3. 接口回调

如果我们使用了一个类来继承一个接口，但是我们也可以实例化一个接口类的实例，这就是接口回调：

```java
public class C implements A{
    public void run(){}
};
A a = new C();
a.run();
```

这个接口变量就可以调用被类重写和定义的接口方法。所以不同的子类可以重写同一个接口的实现方式，这也是多态的呈现方式。

### &emsp;4. 总结

接口并不是类，编写接口的方式和类很相似，但是它们属于不同的概念。类描述对象的属性和方法。接口则包含类要实现的方法。  

除非实现接口的类是抽象类，否则该类要定义接口中的所有方法。  

接口无法被实例化，但是可以被实现。一个实现接口的类，必须实现接口内所描述的所有方法，否则就必须声明为抽象类。另外，在Java中，接口类型可用来声明一个变量，他们可以成为一个空指针，或是被绑定在一个以此接口实现的对象。  

接口与类相似点：

+ 一个接口可以有多个方法。  
+ 接口文件保存在 .java 结尾的文件中，文件名使用接口名。  
+ 接口的字节码文件保存在 .class 结尾的文件中。  
+ 接口相应的字节码文件必须在与包名称相匹配的目录结构中。  

接口与类的区别：  

+ 接口不能用于实例化对象。  
+ 接口没有构造方法。  
+ 接口中所有的方法必须是抽象方法。抽象类可以有变量和非抽象方法。  
+ 接口不能包含成员变量，除了`static`和`final`变量。即只能是常量。  
+ 接口不是被类继承了，而是要被类实现。  
+ 接口支持多继承。

接口特性：

+ 接口中每一个方法也是隐式抽象的,接口中的方法会被隐式的指定为`public abstract`（只能是`public abstract`，其他修饰符都会报错）。  
+ 接口中可以含有变量，但是接口中的变量会被隐式的指定为`public static final`变量（并且只能是`public`，用`private`修饰会报编译错误）。  
+ 接口中的方法是不能在接口中实现的，只能由实现接口的类来实现接口中的方法。

抽象类和接口的区别：  

1. 抽象类中的方法可以有方法体，就是能实现方法的具体功能，但是接口中的方法不行。
2. 抽象类中的成员变量可以是各种类型的，而接口中的成员变量只能是 public static final 类型的。
3. 接口中不能含有静态代码块以及静态方法(用 static 修饰的方法)，而抽象类是可以有静态代码块和静态方法。
4. 一个类只能继承一个抽象类，而一个类却可以实现多个接口。

### &emsp;4. 默认方法

Java 8 对接口做了进一步的增强。  
a. 在接口中可以添加使用 default 关键字修饰的非抽象方法。即：默认方法（或扩展方法）  
b. 接口里可以声明静态方法，并且可以实现。  

默认方法（或扩展方法）  
Java 8 允许给接口添加一个非抽象的方法实现，只需要使用`default`关键字即可，这个特征又叫做扩展方法（也称为默认方法或虚拟扩展方法或防护方法）。在实现该接口时，该默认扩展方法在子类上可以直接使用，它的使用方式类似于抽象类中非抽象成员方法。  
扩展方法不能够重写（也称复写或覆盖）Object中的方法，却可以重载Object中的方法。
`toString`、`equals`、 `hashCode`不能在接口中被覆盖，却可以被重载。  
默认方法允许我们在接口里添加新的方法，而不会破坏实现这个接口的已有类的兼容性，也就是说不会强迫实现接口的类实现默认方法。

默认方法和抽象方法的区别是抽象方法必须要被实现，默认方法不是。作为替代方式，接口可以提供一个默认的方法实现，所有这个接口的实现类都会通过继承得到这个方法（如果有需要也可以重写这个方法）

如：

```java
interface Defaulable {
    //使用default关键字声明了一个默认方法
     @SuppressLint("NewApi")
     default String myDefalutMethod() {
        return "Default implementation";
    }
}
class DefaultableImpl implements Defaulable {
    //DefaultableImpl实现了Defaulable接口，没有对默认方法做任何修改
}
class OverridableImpl implements Defaulable {
        //OverridableImpl实现了Defaulable接口重写接口的默认实现，提供了自己的实现方法。
        @Override
        public String myDefalutMethod() {
            return "Overridden implementation";
        }
}
```

JVM平台的接口的默认方法实现是很高效的，并且方法调用的字节码指令支持默认方法。默认方法使已经存在的接口可以修改而不会影响编译的过程。`java.util.Collection`中添加的额外方法就是最好的例子：`stream()`, `parallelStream()`, `forEach()`, `removeIf()`

虽然默认方法很强大，但是使用之前一定要仔细考虑是不是真的需要使用默认方法，因为在层级很复杂的情况下很容易引起模糊不清甚至变异错误。