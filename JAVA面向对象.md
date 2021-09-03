### 1、面向对象

面向对象：谁来做

面向过程：怎么做

![image-20210623212536094](E:\ccs的架构师之路\picture\image-20210623212536094.png)

面向对象方式便于我们从宏观把握事物之间的复杂关系，方便我们分析整个系统具体到微观操作，仍然使用面向过程的方式处理问题

面向过程：编年体

面向对象：纪传体

面向对象的三个阶段

（1）面向对象分析OOA

对象：张三王五，李四

抽出一个类：人类

类里面有什么？动词：动态特性也就是方法

名词：静态特性也就是属性

（2）面向对象设计OOD

类：人类 Person

对象：zhangsan,lisi zhaoliu

面向对象编程OOP

```java
public class person{
    //名词
    int age;
    String name;
    double height;
    double weight;
    
    //动词
    public void eat(){
        System.out.println("吃饭")
    }
    public void sleep(String address){
        System.out.println("在"+address+"睡觉")
    }
    public String introduce(){
        return "我的名字是："+name+",我的年龄为："+age;
    }
}
```



### 2、类和对象

万物皆对象

对象：具体的事务，具体的实体，具体的实例

类：对对象向上抽取出像（公共）的部分，形成类，类是抽象的，是一个模板

写代码的时候，先写类，然后根据类创建对应的对象

类加载创建多个对象，类加载也只会加载一次；

##### 1)创建对象的过程：

1、new 对象名；；；；；；进行类的加载（仅一次）

2、创建对象，为这个对象在堆中开辟空间

3、为对象进行属性的初始化

##### 2)局部变量和成员变量

局部变量：在方法中，或者代码块中，无默认值，作用范围当前方法中，一定需要初始化，后续使用赋值；位置在栈内存；生命周期：当前方法从开始到执行完毕

成员变量：定义在类中，方法外，有默认值，当前类中的所有方法，一定不需要，不然直接使用报错。在堆内存；生命周期:当前对象从创建到销毁

### 3、构造器

new person();

new 关键字实际是i调用一个方法，这个 方法叫构造方法（器）

构造器跟类名相同

##### 无参构造器

public person(){}

一般不会在空构造器进行初始化操作，那样的话每个都对象属性都一样。

##### 有参构造器

 一般重载构造器进行赋值操作。     

### 4、内存分析

代码1

![image-20210623224922816](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210623224922816.png)

代码2

![image-20210623225429530](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210623225429530.png)

![image-20210623225619642](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210623225619642.png)

代码3

 ![image-20210623231100359](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210623231100359.png)

### 5、this

![image-20210623231401254](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210623231401254.png)

this:指的是正在创建的对象，正在调用方法的对象

同一个类中，方法可以互相调用，this可以省略不写，

this可以修饰构造器

### 6、Static

可以修饰属性，方法，代码块，内部类

####  1）static修饰属性

![image-20210623233212462](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210623233212462.png)

静态属性在方法区静态域，加载一次；（类加载的时候会将静态内容也加载到方法区的静态域中，静态的内容先于对象存在，这个静态内容被所有该类的对象共享）

static 修饰属性的应用：

![image-20210623233627394](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210623233627394.png)

某些特定的数据在内存中共享

属性：

静态属性：类变量

非静态属性：实例变量

#### 2）static修饰方法

先于对象被加载

静态方法中不能访问非静态属性

静态方法不能使用this

非静态方法可以使用对象名，方法名去调用

静态方法可以用方法名和对象名调用，也可以用类名.方法名调用（推荐）

### 7、代码块

类的组成：属性，方法，构造器，代码块，内部类

代码块：普通块，构造快，静态块，同步块（多线程）

总结：

代码块执行顺序：

最先执行静态块，只有类加载的时候执行一次，一般写项目，创建工厂，数据库初始化信息都放在静态块中；一般执行一些全局性的初始化操作。

再执行构造快

在执行构造器，再执行方法中的普通块

### 8、包、import

### 9、三大特性（封装、继承、多态）

#### 1）封装

过程和数据包装起来

程序设计追求高内聚，低耦合

高内聚：类的内部数据操作细节自己完成，不允许外部干涉

低耦合：仅对外暴露少量的方法用于使用

隐藏对象的内部的复杂性，对外公开接口，便于调用，从而提高系统的可拓展性，可维护性。

封装的好处：提高代码的安全性



private 外界对他的访问收受到限制，所以通过定义方法限制条件的添加

##### 进行封装

1）将属性私有化，private，权限修饰符

2）提高public 修饰的方法让别人来访问

3）即使外界可以通过方法来访问属性，但不能随意访问，因为我们再方法中加入限制条件

实际开发中alt+insert->getter and setter

#### 2）继承

![image-20210624111535525](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210624111535525.png)

类是对对象的抽象

##### 2.1继承是对类的抽象

extends 继承

继承的好处：提高代码的复用性

注意：父类private 修饰的内容，子类实际上也可以继承，只是因为封装的特性阻碍了直接调用，但是提供了间接调用方式，可以间接调用；

总结：继承关系；父类子类

父类private修饰的内容，子类也继承过来

![image-20210624112210590](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210624112210590.png)

##### 2.2权限修饰符

![image-20210624112926561](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210624112926561.png)

private 同一类中可以访问，不同类中不可以访问

default 缺省修饰符  同一类，同一包都可以访问

protected 不同包子类都可以访问

一般属性用private 进行封装，用public修饰方法

##### 2.3方法的重写

![image-20210624114020121](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210624114020121.png)

重写：不同类中，子类对父类的方法不满意，要对父类进行重写

重载：同一类中，当方法名相同，形参列表不同的时候多个方法构成重载

##### 2.4super

super：父类的

可以修饰属性和方法

##### 2.5tostring

以文本方式表示此对象字符串

getclass().getName()+"@"+Integr.toHexString(hashcode());

##### 2.6 equals 

这个方法提供了对对象的内容是否相等的一个比较方式，对象的内容指的是属性，object比较==地址，我们一般不会使用父类提供的方法，而是在子类中对这个方法进行重写

##### 2.7类和类的关系



#### 3）多态

向上转型

向下转型-获取子类中特有的内容

### 10、项目练习

![image-20210627134802848](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210627134802848.png)

### 

