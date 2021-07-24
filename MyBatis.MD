# MyBatis

### 1、介绍

MyBatis 是一款支持动态S QL 语句的持久层框架，支持目的是让开发人员将精力集中在SQL 语句上。My Batis 可以将SQL 语句配置在XML 文件中，这避免了JDBC 在Java 类中添加SQL 语句
的硬编码问题； 通过MyBatis 提供的输入参数映射方式，将参数自由灵活地配置在SQL 语句配
置文件中，解决了JDBC 中参数在Java 类中手工配置的问题：通过MyBatis 的输出映射机制，
将结果集的检索自动映射成相应的Java 对象，避免了JDBC 中对结果集的手工检索：同时
My Batis 还可以创建自己的数据库连接池，使用XML 配置文件的形式，对数据库连接数据进行
管理， 避免了JDBC 的数据库连接参数的硬编码问题。

#### 特点：

采用配置文件动态管理SQL 语句，并含有输入映射、输出映射机制以及数据库连接池配置的持久层框架。

#### 整体框架：

数据源配置文件、SQL映射配置文件，会话工厂、执行器以及底层封装对象组成。

（1）数据源配置文件

对于一个持久层框架，也就是负责连接数据库，并对数据进行操作的一套框架，连接数据
库是最重要的一步。MyB atis 框架对于数据库连接的配置信息， 采用了配置“数据库连接池”
的形式。所谓的“数据库连接池”（又可称作“数据源勺， 就是让数据库的配置信息从外部的某
种配置文件中读取，然后由一个独立处理数据库连接的程序来和数据库进行交互。这样一来，
应用程序本身不必关心数据库的配置信息，数据库的配置交由独立的模块管理和配置。

```Java
	<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
  PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
  "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
 
  <context id="DB2Tables" targetRuntime="MyBatis3">
  	<!-- 逆向工程生成的代码没有注释 -->
  	<commentGenerator>
  		<property name="suppressAllComments" value="true" />
	</commentGenerator>
  
 	 <!-- 配置数据库连接信息 -->
    <jdbcConnection driverClass="com.mysql.jdbc.Driver"
        connectionURL="jdbc:mysql://localhost:3306/ssm"
        userId="root"
        password="123456">
    </jdbcConnection>

    <javaTypeResolver >
      <property name="forceBigDecimals" value="false" />
    </javaTypeResolver>

	<!-- 指定mybatis相应javaBean生成的位置 -->
    <javaModelGenerator targetPackage="com.ccs.entity" targetProject=".\src\main\java">
      <property name="enableSubPackages" value="true" />
      <property name="trimStrings" value="true" />
    </javaModelGenerator>

	<!-- 指定mybatis相应XML映射文件生成的位置 -->
    <sqlMapGenerator targetPackage="mapper"  targetProject=".\src\main\resources">
      <property name="enableSubPackages" value="true" />
    </sqlMapGenerator>

	<!-- 指定mybatis mapper接口生成的位置 -->
    <javaClientGenerator type="XMLMAPPER" targetPackage="com.ccs.dao"  targetProject=".\src\main\java">
      <property name="enableSubPackages" value="true" />
    </javaClientGenerator>

	<!-- 指定每个表的生成策略 -->
	<!-- domainObjectName:生成java实例的文件名 -->
    <!-- <table tableName="grade" domainObjectName="Grade"></table>
    <table tableName="subject" domainObjectName="Subject"></table>
    <table tableName="knowledge_point" domainObjectName="Knowledge_point"></table>
    <table tableName="video" domainObjectName="Video"></table> -->
    <!-- <table tableName="user" domainObjectName="User"></table> -->
   <!--  <table tableName="role" domainObjectName="Role"></table>
    <table tableName="authority" domainObjectName="Authority"></table> -->
    <!-- <table tableName="video" domainObjectName="Video"></table> -->
	<!-- <table tableName="role" domainObjectName="Role"></table> -->
	
	<table tableName="cdv_library_details" domainObjectName="Library_details"></table>
  </context>
</generatorConfiguration>

```

（2）SQL映射配置文件

MyB atis 框架会根据配置文件中的参数配置， 将组装参数
的Java 对象或Map 对象中的相关字段与Mapper.刀口l 中的参数配置做匹配， 将相关数据绑定在
需要执行的SQL 语句上； 在查询语句输出结果时， 会根据Mapper.xml 中配置的结果集信息，
将从数据库取出的数据字段， 一一映射到相应的Java 对象或Map 对象中。也就是说， Mapper. xml
配置文件， 完成了对SQL 语句以及输入输出参数的映射配置。

```java
<mapper namespace=” test ” >
<select id=” findUserByld ” parameterType="int ” result Type=” cn . com . mybatis .
model.User ” >
SELECT * FROM USER WHERE 工d ＝＃｛工d }
</select>
</mapper>
```

（3）会话工厂

Sq!Sessi onFactorγ 类会根据Resources 资源信息加载对象，获取开发人员在项目中配置的数据库连接池配置文件Sq!MapConfig . xml 的信息，从而产生一种可以与数据库交互的会话实例类－一－Sq!Sess i on 。就好像一个卫星工厂一样，给厂商一个卫星的详细发射波长规格配置说明书，他们就能生产出能发射该种类型波长的卫星产品。Sq!Sess ionFactory 可以根据数据库配置信息产生出可以连接数据库并与其交互的SqIS巳ssion 会话实例类。前面提到过， SQL 映射配置文件Mapper.xml 的路径是配置在Sq!MapConfig . xml 配置文件中的，所以Sq!SessionFactory 类同时也加载了SQL 语句的配置信息。通过其产生的Sq!Sess ion会话实例类，可以依照Mapper 配置文件中的SQL 配置，对数据库执行增删改查的操作。

#### 运行流程

My Batis 的整个运行流程，也是紧紧围绕着数据库连接池配置文件S q!MapConfig.xml ，以
及SQL 映射配置文件Mapper.xml 而开展的。

![image-20210720220923945](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210720220923945.png)



2、搭建MyBatis共作环境