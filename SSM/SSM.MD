# 架构模式与框架

（1）界面层----SpringMVC

（2）业务层----Spring

（3）持久层----MyBatis

## 一、MyBastis 入门

第一个项目：

mysql 有个学生表

步骤：新建maven->quickstart

修改pom.xl

2、创建实体类，定义属性，属性名，列名保持一致

3、创建dao，操作数据库方法

4、创建mapper.xml 写sql

5、mapper 定义在dao接口同一目录，一个表对应一个mapper.xml

6、创建mybastis的主配置文件，放进resource目录下，定义创建连接实例的数据源（DataSources）对象，指定mapper文件的位置

7、创建main测试mybatis数据库，可以用junit访问数据库



### 概念：

mybatis 设置日志，控制台输出mybatis.xml

```java
<settings>
    <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>
```

自动提交，当你的sql语句执行完毕，提交事务，数据库更新操作之间保存数据

手动提交事务，在你需要提交事务的位置，执行方法，提交或者回滚事务

![image-20210721222736361](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210721222736361.png)

```java
		String config = "mybatis.xml";
        //2、读取主配置文件，使用Resource类操作
        InputStream resourceAsStream = Resources.getResourceAsStream(config);

        //3、创建SqlSessionFactory对象，使用SqlSessionFactoryBuilder
        SqlSessionFactory sqlSessionFactory = new 			 SqlSessionFactoryBuilder().build(resourceAsStream);

        //4、获取SqlSession对象
        SqlSession sqlsession  = sqlSessionFactory.openSession();

        //5、指定要执行sql的id
        //sqlid = namespace+'.'+select|update|insert标签的id的属性值
        String sqlid = "com.powernode.dao.StudentDao"+"."+"selectStudentByID";

        //6、通过SqlSession的方法，执行SQL
        //查询单条
        Student student = sqlsession.selectOne(sqlid,2);
        //查询所有学生fsd

		sqlsession.close();
```

SqlSessionFactory  重要对象：创建对象需要资源和时间，重量级对象

sqlSessionFactory接口sqlsession 工厂，创建

sqlsession 实现类

selectOne 最多得到一条数据

selectList  得到多条数据

selectMap 得到map

commit  rollback 提交  回滚

sqlsession 对象不是线程安全，所以分三步骤，获取对象，调用sql语句，关闭sqlsession对象





#### 使用工具类和模板

mapper文件模板和mybatis主配置文件模板

![image-20210722221343605](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210722221343605.png)

![image-20210722221549804](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210722221549804.png)

![image-20210722221724012](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210722221724012.png)





#### dao代理

mybatis提供代理，mybatis创建dao接口实现类对象，完成对SQL语句的执行。

mybatis创建一个对象代替你的dao实现类对象

##### 要求

1）namespace 一定是dao接口的全限定名称

2）id为dao接口方法名称

##### 代理实现方式

```java
SqlSession session = MyBatisUtils.getSqlSession();
Student dao = session.getMapper(StduentDao.class);
Student student = dao.selectOne(1);

```

#### 理解参数

各种查询各种参数

parameterType:形参数据类型

![image-20210723100305432](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210723100305432.png)

多个参数，使用命名参数

```java
List<Student> selectByNameorAge(@Param("myName") String name,@Param("myAge") Integer age)
```

${}表示连接 ，也就是Java的+

#{}参数占位

”   ‘ JM  ’  “ 

<typeAliases>





#### 动态sql

同一dao方法，根据不同条件可以表示不同sql语句，主要是where变化，使用mybais提供的标签实现动态sql

主要if、where、foreach sql

使用动态sql的时候，dao方法的形参使用Java对象

```xml
<select id="selectStudents" resultType="com.powernode.domain.Student">
        select * from tbl_student;
    <if test = '条件'>
    	sql语句
    </if>
     <if test = '条件'>
    	sql语句
    </if>
     <if test = '条件'>
    	sql语句
    </if>
</select>
```





#### 使用PageHelper

查询分页，select * from student 后面加入limit 语句

使用步骤：

1、pom.xml

```xml
 <!--引入mybatis的分页插件的依赖-->
    <dependency>
      <groupId>com.github.pagehelper</groupId>
      <artifactId>pagehelper</artifactId>
      <version>4.2.1</version>
    </dependency>
    <dependency>
```

2、mybatis.xml（在environments前面添加,插件声明）

```xml
<plugins>
        <plugin interceptor="com.github.pagehelper.PageHelper">
            <property name="dialect" value="mysql"/>
            <property name="offsetAsPageNum" value="false"/>
            <property name="rowBoundsWithCount" value="false"/>
            <property name="pageSizeZero" value="true"/>
            <property name="reasonable" value="true"/>
            <property name="supportMethodsArguments" value="false"/>
            <property name="returnPageInfo" value="none"/>
        </plugin>
    </plugins>

    <environments default="development">
```

3、在select语句前，调用PageHelper.startPage(页码，每页大小)







## 二、Spring

spring容器，核心IOC控制反转，AOP面向切面

### 2 IOC控制反转

#### 2.1、IOC控制反转

Inversion of control 指导思想，指导开发人员如何使用对象，管理对象

把对象的创建，属性赋值，对象的声明，生命周期交给代码之外的容器管理

##### 1）IOC 分为控制和反转

控制：对象创建，属性赋值，对象声明，周期管理

反转：把开发人员管理对象的权限转移交给容器实现，由容器完成对象的管理。

正转：开发人员在代码中，使用new 构造方法创建对象，开发人员掌握对象的创建，属性渎职，对象从开始到销毁的全部过程。

通过容器，可以使用容器的对象（容器已经创建对象，属性赋值，对象也组装好了）

##### 2）IOC的技术实现

DI（依赖注入）：dependency injection,IOC的一种技术实现，需要提供对象名称就可以了，对象如何创建，如何从容器中查找，获取，都是由容器内部自己实现

依赖名词：依赖jar包，或者比如说，ClassA类使用到B类的属性和方法，这就叫A依赖B

```java
public class classB{
    public void CreatOrder(){};
}
public class classA{
	private classB b = new classB();
    public void buy(){
		b.CreatOrder();
    }
}
执行classA的buy方法，
    classA a = new classA();
	a.buy();
```

3)Spring框架使用的DI实现IOC

通过Spring框架，只需要提供要使用的对象名词就可以了，从容器中获取名称对应的对象。

Spring底层使用的反射机制，通过反射创建对象给属性。

### 2.2 Spring配置文件

beans.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

</beans>
```

beans 约束文件

### 2.3 Spring容器创建对象

在bean标签创建

```xml
<!--声明bean标签用来声明对象-->
<!--id,自定义唯一值，class,类的全限定名称，Spring根据id,class创建对象，吧对象放入
到SPring 的一个map对象-->
<bean id="someService" class="com.powernode.service.impl.SomeServiceImpl"></bean>
<bean id="someService1" class="com.powernode.service.impl.SomeServiceImpl"></bean>

<!--创建非自定义对象-->
<bean id="mydate" class="java.util.Date"></bean>
```

### 2.4 给属性赋值

两种方式给属性赋值：1、xml文件的标签和属性；2、使用注解

DI（注入）分类：1、set注入，别名设值注入；2、构造注入

#### 2.4.1 基于xml的注入

1、set注入，别名设值注入

概念：Spring调用类中的set方法，在set方法完成属性赋值（推荐使用）

```xml
<!--1、set注入，赋值-->
<bean id="myStudent" class="com.powernode.entity.Student">
    <property name="id" value="1"/><!--执行了setid-->
    <property name="name" value="ccs"/><!--执行了setname-->
</bean>
```

```xml
<!--声明bean-->
<!--1、set注入，赋值-->
<bean id="myStudent" class="com.powernode.entity.Student">
    <property name="id" value="1"/><!--执行了setid-->
    <property name="name" value="ccs"/><!--执行了setname-->
    <property name="school" ref="mySchool"/>
</bean>
<!--引用类型赋值-->
<bean id="mySchool" class="com.powernode.entity.School">
    <property name="name" value="北京大学"/><!--执行了setid-->
    <property name="address" value="北京市海淀区"/><!--执行了setname-->
</bean>
```

2、构造注入，Spring 调用类中的有参构造该方法，在创建对象的同时，给属性赋值

```java
public Student(String name, int id, School myschool) {
    System.out.println("有参构造");
    this.name = name;
    this.id = id;
    this.school = myschool;
}
```

```xml
<bean id="myStudent" class="com.powernode.entity.Student">
    <constructor-arg name="id" value="1"/>
    <constructor-arg name="name" value="JM"/>
   <constructor-arg name="myschool" ref="mySchool"/>
</bean>
<!--引用类型赋值-->
<bean id="mySchool" class="com.powernode.entity.School">
    <property name="name" value="北京大学"/><!--执行了setid-->
    <property name="address" value="北京市海淀区"/><!--执行了setname-->
</bean>
```

```xml
<!--声明file 对象-->
<bean id="myFile" class="java.io.File">
    <constructor-arg name="parent" value="E:\\2kSports"/>
    <constructor-arg name="child" value="test.txt"/>
</bean>
```

3)引用类型的自动注入

概念：Spring可以根据某些规则给引用类型完成赋值

1、byName(按名称注入)：Java 类中引用类型属性名称和Spring容器中的bean中id名称一样。

2、byType(按类型注入)：Java 类中引用类型属性名称与bean中class值同源

```xml
<bean id="myStudent" class="com.powernode.entity.Student" autowire="byName" >
    <constructor-arg name="id" value="1"/>
    <constructor-arg name="name" value="JM"/>
    <constructor-arg name="myschool" ref="mySchool"/>
</bean>
```

```
<bean id="myStudent" class="com.powernode.entity.Student" autowire="byType" >
    <constructor-arg name="id" value="1"/>
    <constructor-arg name="name" value="JM"/>
    <constructor-arg name="myschool" ref="mySchool"/>
</bean>
```



##### 2.4.1.1项目中使用多个Spring配置文件

按模块分，按类的功能分；Spring配置多个文件，常用是包含关系，项目中有一个总文件，里面import标签包含其他的多个配置文件

![image-20210724232554685](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210724232554685.png)

applicationContext.xml

语法：

```xml
<beans>
	<import resource = "classpath:Spring-school.xml"/>
	<import resource = "classpath:Spring-Student.xml"/>
</beans>
//第二种包含关系，使用通配符* 表示任意字符
<import resource = "classpath:Spring-*.xml"/>
```

#### 2.4.2 基于注解的注入，（简单方便，但是有点乱，快）

@override

@Component:指定bean对象，格式在Student.java

```java
@Component(value = "myStudent")
//上面这句话相等于mapper里面<bean id="myStudent" class ="com.powernode.entity.Student">
public class Student{
    private String name;
    private int age;
}
```

@Repository :用于对dao实现类注解,持久层对象，访问数据库

@Service用于对Service实现类注解，业务层对象，有事务的功能

@Controller用于对Controller实现类进行注解，用于表现层，控制器对象能接受请求，把请求的处理结果显示给用户。



使用注解必须在applicationContext.xml文件声明扫描器

<context:component-scan base-package="com.powernode.entity;com.powernode.entity;(扫描多个包)">

value赋值

![image-20210724234719128](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210724234719128.png)

读取外部属性的配置文件myconfig.properties,在applicationContext.xml文件加入下句

```xml
<context:property-placeholder location="classspath:/myconfig.properties"/>
```

在Student.java中

```java
//简单类型赋值
@Value("${myname}")
```

读取到myname 是ccs

```xml
//myconfig.properties
myname="ccs"
myage = 25
```

```java
@Autpwired(required = false)   //加了required = false，Qualifier里面的mySchool写错了，程序也能走下去
@Qualifier("mySchool")
private School school;
//引用类型注解
```

```xml
<bean id = "mySchool" class ="com.powernode.entity.School">
	<property name = "name" value="北京大学"></property>
    <property name = "address" value="北京"></property>
</bean>
```

@Resource，给引用类型赋值，支持byName,byType

```xml
@Resource注入依赖
<dependency>
      <groupId>javax.annotation</groupId>
      <artifactId>javax.annotation-api</artifactId>
      <version>1.3.2</version>
    </dependency>
```

```xml
@Resource(name="mySchool")//使用byName赋值
@Autowired 默认bytype
@Autowired @Qualifier 使用byname
```

#### 2.5 IOC总结：

IOC：管理对象，把对象放在容器中，创建、赋值、管理依赖关系

IOC：通过管理对象，实现解耦合，IOC解决处理业务逻辑对象之间的耦合关系，也就是service和dao之间的解耦。

spring作为容器适合管理什么对象？

- service对象，dao对象
- 工具类对象

不适合交给spring的对象？

- 实体类
- servlet，listener，filter等web对象，是由tomcat管理

### 3 AOP面向切面

减少重复，专注业务

什么是AOP（Aspect Orient Programming）？面向切面编程；

Aspect:增加功能叫切面,切面一般都是非业务功能，而且切面功能一般都是可以复用，例如日志功能，事务功能，权限检查，参数检查，统计信息。

Orient :面向

Programming：编程

**怎么理解AOP？以切面为核心开发**

1. 设计项目，找出切面功能
2. 安排切面执行时间和位置。

**AOP作用？**

1. 让切面功能复用
2. 专注业务逻辑
3. 实现业务功能和其他非业务功能解耦合
4. 给存在的业务方法，增加功能，不用修改原来的代码

**AOP术语**

Aspect切面：给业务方法增加的功能

JoinPoint连接点：连接切面的业务方法。在这个业务方法执行时，会同时执行切面方法。

PointCut切入点：是一个或多个连接点集合。表示这些方法执行时，都能增加切面的功能

target目标对象：给那个对象增加切面功能，这个对象就是目标对象

advice：通知（增强），表示切面的执行时间，在目标方法之前执行切面，还是目标方法之后执行切面

**重要的是三个要素**

APsect，pointcut，advice，在advice的时间，在pointcut的位置，执行aspect

AOP想等于动态代理，程序运行期间，创建代理，使用代理执行方法时，增加切面的功能，这个代理对象存在内存中。

**什么时候使用AOP**

- 给业务方法增加非业务功能
- 给某些方法增加相同的功能

**AOP技术思想的实现**

使用框架实现AOP

- Spring：实现AOP思想的部分功能，比较繁琐
- Aspectj：独立的框架，专门AOP，属于Eclipse

### 使用AspectJ框架实现AOP

AspectJ框架可以使用注解和xml配置文件两种方式实现AOP

**通知注解**

- @before：前置通知
- @AfterReturning：后置通知
- Around：环绕通知
- AfterThrowing：异常通知
- After：最终通知

**Pointcut位置**

- aspectJ专门定义表达式用于指定切入点：execution (访问修饰符，返回值 方法名（参数）)
- public * * （··） 任意方法
- execution（void dosome（String，Integer））

使用aspectj框架的注解，实现前置通知

实现步骤：

1. 新建maven项目
2. 修改pom.xml  ，加入依赖 spring-context，spring-aspects junit
3. 创建业务接口和实现类
4. 创建一个切面类，是一个普通类
   1. 在类的上面加入@Aspect
   2. 在类中定义方法，方法表示切面的功能，在方法的上面加入Aspect注解，例如@Before（vaLue="切入点表达式"）
5. 创建spring配置文件
   1. 声明目标对象
   2. 声明切面类对象
   3. 声明自动代理生成器
6. 创建测试类，测试目标方法执行时，增加切面的功能

## 三、SpringMVC入门



## 四、Redis 入门

![image-20210726002640033](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210726002640033.png)

![image-20210821165701801](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210821165701801.png)

**![image-20210821165720319](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210821165720319.png)**

## 五、整合SSM

1、创建maven web项目

2、修改pom.xml 加入依赖 Spring SpringMVC mybatis,mybatis-spring druid,jackson，mysql驱动

3、写web.xml：声明容器对象

​	1）声明Spring的监听器contextLoaderListener:创建Spring的容器对象，创建service，dao对象

​	2）声明SpringMVC的中央调度器DispatchServlet：创建springMVC容器对象，创建controller对象

​	3）声明字符集的过滤器CharacterEncodingFilter，解决post请求乱码的问题

4、创建程序中的包，dao，service，controller，entity

5、写Spring、springMVC，mybatis配置文件

6、写java代码，实体类，dao接口，mapper文件，service类，controller类，使用注解声明对象和赋值

7、创建视图文件，各种jsp

