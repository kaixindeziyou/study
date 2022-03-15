# MyBatis

**原始jdbc操作：**

插入操作：

![image-20210409202649887](../img/image-20210409202649887.png)

查询操作：

![image-20210409202732626](../img/image-20210409202732626.png)

**原始jdbc操作的分析**

![image-20210409202525319](../img/image-20210409202525319.png)

**什么时MyBatis**

![image-20210409202800403](../img/image-20210409202800403.png)

## MyBatis开发步骤

![image-20210409203441005](../img/image-20210409203441005.png)

1.添加mybatis坐标

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.37</version>
</dependency>
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.6</version>
</dependency>
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

2.创建user数据表

3.编写User实体类

4.编写映射文件userMapper.xml

约束头：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
```

```xml
<mapper namespace="userMapper">
    <select id="findAll" resultType="com.zrulin.bean.User">
        select * from user
    </select>
</mapper>
```

5.编写核心文件sqlMapConfig.xml

约束头：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
```

```xml
<configuration>
    <!--数据源环境-->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/jdbctest?characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
<!--    加载映射文件-->
    <mappers>
        <mapper resource="com.zrulin.mapper/UserMapper.xml"/>
    </mappers>
</configuration>
```



6.编写测试类

```xml
@Test
public void test() throws IOException {
    //加载核心配置文件
    InputStream resourceAsStream = Resources.getResourceAsStream("sqlMappingConfig.xml");
    //获得sqlSession工厂对象
    SqlSessionFactory build = new SqlSessionFactoryBuilder().build(resourceAsStream);
    //获得sqlSession对象
    SqlSession sqlSession = build.openSession();
    //执行sql语句
    List<Object> userList = sqlSession.selectList("userMapper.findAll");
    //打印结果
    System.out.println(userList);
    //释放资源
    sqlSession.close();
}
```



## MyBatis的映射文件概述

![image-20210410093917681](../img/image-20210410093917681.png)

## MyBatis增删改查

**插入操作：**

![image-20210410094501993](../img/image-20210410094501993.png)

mybatis事务是默认不提交的 

mybatis执行更新操作  要自己提交事务

```java
@Test
public void test3() throws IOException {
    User user = new User();
    user.setName("tom");
    user.setPassword("osngiowegw");

    //加载核心配置文件
    InputStream resourceAsStream = Resources.getResourceAsStream("sqlMappingConfig.xml");
    //获得sqlSession工厂对象
    SqlSessionFactory build = new SqlSessionFactoryBuilder().build(resourceAsStream);
    //获得sqlSession对象
    SqlSession sqlSession = build.openSession();
    //执行sql语句
    sqlSession.insert("userMapper.insert",user);
    //事务提交
    sqlSession.commit();
    //释放资源
    sqlSession.close();
}
```

==注意事项==

![image-20210410094929085](../img/image-20210410094929085.png)

查询不需要事务提交

**修改操作：**



==注意事项==

![image-20210410095334852](../img/image-20210410095334852.png)



**删除操作：**



==注意事项==

![image-20210410095604943](../img/image-20210410095604943.png)

传递单个参数，中间可以用![image-20210410095644609](../img/image-20210410095644609.png)

传实体属性，中间![image-20210410095729444](../img/image-20210410095729444.png)

==知识小结==

![image-20210410101640705](../img/image-20210410101640705.png)

## MyBatis核心文件的概述

**Mybatis核心文件配置层级关系**

![image-20210410102744263](../img/image-20210410102744263.png)

## Mybatis常用配置解析

**1.environments标签**

![image-20210410103000027](../img/image-20210410103000027.png)

![image-20210410103034045](../img/image-20210410103034045.png)

**2.mapper标签**

![image-20210410103737831](../img/image-20210410103737831.png)

**3.Properties标签**

![image-20210410103839797](../img/image-20210410103839797.png)

**typeAliases标签**

![image-20210410105518377](../img/image-20210410105518377.png)

![image-20210410105608107](../img/image-20210410105608107.png)

==知识小结==

![image-20210410110347084](../img/image-20210410110347084.png)

![image-20210410110507709](../img/image-20210410110507709.png)

## MyBatis相应API

**1.SqlSession工厂构建器SqlSessionFactoryBuilder**

![image-20210410132509861](../img/image-20210410132509861.png)

![image-20210410133141848](../img/image-20210410133141848.png)

**2.SqlSession会话对象**

![image-20210410133244158](../img/image-20210410133244158.png)

## Dao层实现

**传统方式**

![image-20210410133901472](../img/image-20210410133901472.png)

**代理开发方式**

![image-20210410134210610](../img/image-20210410134210610.png)

![image-20210410135224517](../img/image-20210410135224517.png)	

**测试代理方式**

![image-20210410135402025](../img/image-20210410135402025.png)	

## MyBatis映射文件的深入

**动态sql语句**

概述：

![image-20210410141853017](../img/image-20210410141853017.png)

**动态sql  之< if>**

![image-20210410143734906](../img/image-20210410143734906.png)

![image-20210410143312398](../img/image-20210410143312398.png)

![image-20210410143411870](../img/image-20210410143411870.png)

**动态sql  之< foreach>**

![image-20210410143908157](../img/image-20210410143908157.png)

查询语句

![image-20210410144022028](../img/image-20210410144022028.png)

相当于：

![image-20210410144043488](../img/image-20210410144043488.png)

在mybatis中：

![image-20210410144951418](../img/image-20210410144951418.png)

**SQL片段抽取**

![image-20210410145251166](../img/image-20210410145251166.png)

==知识小结==

![image-20210410145831383](../img/image-20210410145831383.png)

## MyBatis核心配置文件深入

**1.typeHandlers标签**

![image-20210410150111571](../img/image-20210410150111571.png)

主要是进行类型处理器定义。

jdbc数据类型和java数据类型不一样

![image-20210410150419108](../img/image-20210410150419108.png)

1.![image-20210410151325916](../img/image-20210410151325916.png)

2.

![image-20210410151934971](../img/image-20210410151934971.png)

![image-20210410151819742](../img/image-20210410151819742.png)

![image-20210410151740431](../img/image-20210410151740431.png)

![image-20210410151730061](../img/image-20210410151730061.png)

3.在核心文件中注册

![image-20210410152143249](../img/image-20210410152143249.png)

## plugins标签

![image-20210410153827167](../img/image-20210410153827167.png)

1.导坐标

![image-20210410153909334](../img/image-20210410153909334.png)

2.在核心文件中配置插件

![image-20210410154040214](../img/image-20210410154040214.png)

![image-20210410154343860](../img/image-20210410154343860.png)

![image-20210410154658864](../img/image-20210410154658864.png)

==知识小结==

![image-20210410155149368](../img/image-20210410155149368.png)

## Mybatis的多表操作

![image-20210410160233651](../img/image-20210410160233651.png)

面向对象的思想，不是直接用int类型的Uid，而是引用user中的id

表示出数据库中主键和外键的关系

![](../img/image-20210410161311916.png)

![image-20210410162131029](../img/image-20210410162131029.png)

![image-20210410162409980](../img/image-20210410162409980.png)

**一对多：**

![image-20210410162821272](../img/image-20210410162821272.png)

**多对多查询：**

![image-20210410162953180](../img/image-20210410162953180.png)

java层面的代码的话，user实体中有role实体组成的list集合。

![image-20210410163406319](../img/image-20210410163406319.png)

![image-20210410163705938](../img/image-20210410163705938.png)

==知识小结==

![image-20210410163853069](../img/image-20210410163853069.png)

## MyBatis注解开发

**1.MyBatis的常用注解** 

![image-20210410170231689](../img/image-20210410170231689.png)

![image-20210410170336099](../img/image-20210410170336099.png)

![image-20210410170452721](../img/image-20210410170452721.png)

**MyBatis的注解实现复杂映射开发**

![image-20210410170555952](../img/image-20210410170555952.png)

**一对一：**

![image-20210410170945411](../img/image-20210410170945411.png)

或者：

![image-20210410171417646](../img/image-20210410171417646.png)



**一对多：**

![image-20210410171915105](../img/image-20210410171915105.png)

**多对多：**

![image-20210410173002175](../img/image-20210410173002175.png)

![image-20210410173019263](../img/image-20210410173019263.png)

# Mybatis-Plus

ge：>=
gt：>
le：<=
lt：<



```java
	//创建一个QueryWrapper的对象
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    //通过QueryWrapper设置条件
    //ge gt le lt
    //查询age>=30的记录
    //第一个参数是字段的名称 ， 第二个参数是设置的值
    wrapper.ge("age" , 30);
    List<User> users = userMapper.selectList(wrapper);
    System.out.println(users);
```


eq：=
ne：！=



		wrapper.eq("name" , "Marray");
	    List<User> users = userMapper.selectList(wrapper);
	    System.out.println(users);
between：范围之间 ， 需要三个参数 ， 第一个是表字段名 ， 第二个是起始 ， 第三个是结束

		wrapper.between("age" , 20 , 30);
	    List<User> users = userMapper.selectList(wrapper);
	    System.out.println(users);
like：模糊查询

orderByDesc：降序排列
orderByAsc：升序

		wrapper.orderByDesc("id");
	    List<User> users = userMapper.selectList(wrapper);
	    System.out.println(users);
last：拼接语句

		wrapper.last("limit 1");
	    List<User> users = userMapper.selectList(wrapper);
	    System.out.println(users);

等同于：
        
SELECT
 *
FROM
user 
WHERE
deleted=0 limit 1



select：查询指定列

		wrapper.select("id" , "name");
	    List<User> users = userMapper.selectList(wrapper);
	    System.out.println(users);