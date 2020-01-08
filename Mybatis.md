# Mybatis

[mybatis官方说明文档](http://www.mybatis.org/mybatis-3/zh/getting-started.html)

是一个基于 Java 的持久层框架

## 我的理解

核心功能有2个

1. 将使用SQL语句，从数据库中查询出的数据，映射到Java类型、原始类型上。（输出映射）
2. 将Java类型、原始类型，映射到SQL语句中的参数上。（输入映射）

而且通过简单的配置，就可以自动实现上面2个功能。

## 入门程序

- 安装

将 mybatis-x.x.x.jar 文件置于 classpath 中。

Maven 的话，依赖

```xml
<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis</artifactId>
  <version>x.x.x</version>
</dependency>
```

文件结构(仅参考)

```
- myProject/
| - src/
| | - myPackage/(包)
| | | - MyBatisTest.java
| | - mapper/(包)
| | | - DemoMapper.xml
| | | - DemoMapper.java
| | | - mybatis-config.xml
| | - pojo/(包)
| | | - Student.java
| | - properties/
| | |  - jdbc.properties
| - lib/
| | - mybatis-x.x.x.jar
```

要素

1. DemoMapper.xml映射文件：配置sql语句，从数据库中读取数据，并映射到java类型上
2. DemoMapper.java：映射器类，是一个接口，方法和DemoMapper.xml中的sql语句一一对应
3. mybatis-config.xml：配置mybatis的相关信息
4. MyBatisTest.java：一个用来读取数据库数据的程序
5. Student.java：从数据库中读取的数据类型

- MyBatisTest.java

作用：查询数据库，读出结果

```java
public class MyBatisTest{
	@Test
	public void test1(){
		// 加载mybatis配置文件，文件名随意
		String resource = "mybatis-config.xml";
		// Resources 类用来从类路径下、文件系统或一个 web URL 中加载资源文件。
		InputStream in = Resources.getResourceAsStream(resource);
		// 创建SqlSessionFactory
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(in);
		// 创建SqlSession
		SqlSession sqlSession = sqlSessionFactory.openSession();
		// 执行sql语句。mapper.DemoMapper.selectStu 定位到一个SQL语句，1是输入参数
    // 方式1
		Student stu = sqlSession.selectOne("mapper.DemoMapper.selectStu", 1);
    // 方式2
    DemoMapper mapper = session.getMapper(DemoMapper.class);
    Student stu = mapper.selectStu(101);
		System.out.println(stu);
	}
}
```

- mybatis-config.xml

作用：配置数据链接池、事务等

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<!-- 引入配置文件 -->
	<properties resource="jdbc.properties" />
	<!-- 配置环境 -->
	<environments default="development">
		<environment id="development">
			<!-- 事务管理 -->
			<transactionManager type="JDBC" />
			<!-- 连接池 -->
			<dataSource type="POOLED">
				<property name="driver" value="${driver}" />
				<property name="url" value="${url}" />
				<property name="username" value="${username}" />
				<property name="password" value="${password}" />
			</dataSource>
		</environment>
	</environments>
	<!-- 引入sql语句 -->
	<mappers>
		<mapper resource="mapper/DemoMapper.xml" />
	</mappers>
</configuration>
```

- DemoMapper.xml

作用：配置需要的sql语句

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- 命名空间用来区分各个表 -->
<mapper namespace="mapper.DemoMapper">
  <!-- resultType是返回类型 -->
  <select id="selectStu" resultType="pojo.Student">
    select * from student where sid = #{sid}
  </select>
</mapper>
```

- DemoMapper.java

映射器类，和DemoMapper.xml中的sql语句一一对映。

```java
public interface DemoMapper {
  public Strudent selectStu (int sid);
}
```

补充说明

使用java配置mybatis

```java
DataSource dataSource = BlogDataSourceFactory.getBlogDataSource();
TransactionFactory transactionFactory = new JdbcTransactionFactory();
Environment environment = new Environment("development", transactionFactory, dataSource);
Configuration configuration = new Configuration(environment);
configuration.addMapper(BlogMapper.class);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(configuration);
```

获取 SqlSession

```java
// 旧版
SqlSession session = sqlSessionFactory.openSession();
try {
  Blog blog = (Blog) session.selectOne("org.mybatis.example.BlogMapper.selectBlog", 101);
} finally {
  session.close();
}

// 新版
SqlSession session = sqlSessionFactory.openSession();
try {
  BlogMapper mapper = session.getMapper(BlogMapper.class);
  Blog blog = mapper.selectBlog(101);
} finally {
  session.close();
}
```

通过注解映射

```java
public interface BlogMapper {
  @Select("SELECT * FROM blog WHERE id = #{id}")
  Blog selectBlog(int id);
}
```

作用域和生命周期

- SqlSessionFactoryBuilder : 方法作用域。一旦创建 SqlSessionFactory 后，就不再需要了
- SqlSessionFactory : 应用作用域。在应用运行期间应该一直存在
- SqlSession : 请求或方法作用域。不是线程安全的，每个线程应该都有一个。
- 映射器实例(Mapper Instances) : 方法作用域。

## XML 映射配置文件

相当于 mybaits-config.xml

文档的顶层结构如下：

- configuration 配置
  - properties 属性
  - settings 设置
  - typeAliases 类型别名
  - typeHandlers 类型处理器
  - objectFactory 对象工厂
  - plugins 插件
  - environments 环境
    - environment 环境变量
      - transactionManager 事务管理器
      - dataSource 数据源
  - databaseIdProvider 数据库厂商标识
  - mappers 映射器

### properties

可以在 Java 属性文件中配置，也可通过 properties 元素的子元素来传递

```xml
<properties resource="org/mybatis/example/config.properties">
  <property name="username" value="dev_user"/>
  <property name="password" value="F2Fa3!33TYyg"/>
  <!-- 指定默认值 3.4.2+ -->
  <property name="org.apache.ibatis.parsing.PropertyParser.enable-default-value" value="true"/> <!-- Enable this feature -->
  <property name="username" value="${username:ut_user}"/> 
</properties>

<!-- 使用 -->
${username}
```

在java方法中传递

```java
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, props);
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, environment, props);
```

如果属性在不只一个地方进行了配置，那么 MyBatis 将按照下面的顺序来加载：

1. 在 properties 元素体内指定的属性首先被读取。
2. 根据 properties 元素中的 resource 属性读取类路径下属性文件，根据 url 属性指定的路径读取属性文件，并覆盖已读取的同名属性。
3. 读取作为方法参数传递的属性，并覆盖已读取的同名属性。

### settings

属性见官方文档

### typeAliases

类型别名是为 Java 类型设置一个短的名字。它只和 XML 配置有关，存在的意义仅在于用来减少类完全限定名的冗余。

```xml
<typeAliases>
  <typeAlias alias="Author" type="domain.blog.Author"/>
  <!-- 为整个包下的类创建别名，别名为首字母小写的类名，或注解名 -->
  <package name="domain.blog"/>
</typeAliases>
```

```java
@Alias("author")
public class Author {...}
```

常见的 Java 类型内建了相应的类型别名。基本规律是

- `基本类型` --- `_基本类型名`， 如 `byte` --- `_byte`
- 包装类型or引用类型 --- 首字母小写的类名，如 Integer --- integer

### typeHandlers

类型处理器将获取的值以合适的方式转换成 Java 类型。

使用 typeHandlers 的场景

- MyBatis 在预处理语句(PreparedStatement)中设置一个参数时
- 从结果集中取出一个值时

默认的类型处理器见官方参考文档

自定义类型处理器

- 实现 org.apache.ibatis.type.TypeHandler 接口
- 继承 org.apache.ibatis.type.BaseTypeHandler 类

```java
// @MappedTypes(String)
@MappedJdbcTypes(JdbcType.VARCHAR)
public class ExampleTypeHandler extends BaseTypeHandler<String> {...}
```

```xml
<!-- mybatis-config.xml -->
<typeHandlers>
  <typeHandler handler="org.mybatis.example.ExampleTypeHandler"/>
  <!-- <typeHandler handler="org.mybatis.example.ExampleTypeHandler" javaType="String" jdbcType="VARCHAR"/> -->
</typeHandlers>

<!-- 自动查找，只适用通过注解方式指定jdbc类型 -->
<typeHandlers>
  <package name="org.mybatis.example"/>
</typeHandlers>
```

### 处理枚举类型

将枚举类型映射成数字

```xml
<!-- mybatis-config.xml -->
<typeHandlers>
  <typeHandler handler="org.apache.ibatis.type.EnumOrdinalTypeHandler" javaType="java.math.RoundingMode"/>
</typeHandlers>
```

### 对象工厂(objectFactory)

MyBatis 每次创建结果对象的新实例时，它都会使用一个对象工厂(ObjectFactory)实例来完成。默认的对象工厂需要做的仅仅是实例化目标类，要么通过默认构造方法，要么在参数映射存在的时候通过参数构造方法来实例化。如果想覆盖对象工厂的默认行为，则可以通过创建自己的对象工厂来实现。

```java
public class ExampleObjectFactory extends DefaultObjectFactory {...}
```

```xml
<!-- mybatis-config.xml -->
<objectFactory type="org.mybatis.example.ExampleObjectFactory">
  <property name="someProperty" value="100"/>
</objectFactory>
```

### 映射器

告诉 MyBatis 到哪里去找 SQL 映射语句

```xml
<mappers>
  <!-- 1. 使用相对路径 -->
  <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
  <!-- 2. 使用本地路径 -->
  <mapper url="file:///var/mappers/AuthorMapper.xml"/>
  <!-- 3. 使用完全限定名，需要映射器类，且mapper.xml在同一目录下 -->
  <mapper class="org.mybatis.builder.AuthorMapper"/>
  <!-- 4. 使用包名，需要映射器类，且mapper.xml在同一目录下 -->
  <package name="org.mybatis.builder"/>
</mappers>
```

## Mapper XML 文件

相当于入门程序中的 DemoMapper.xml

SQL 映射文件只有很少的几个顶级元素（按照应被定义的顺序列出）：

- cache – 对给定命名空间的缓存配置。
- cache-ref – 对其他命名空间缓存配置的引用。
- resultMap – 是最复杂也是最强大的元素，用来描述如何从数据库结果集中来加载对象。
- parameterMap – 已被废弃！老式风格的参数映射。更好的办法是使用内联参数，此元素可能在将来被移除。文档中不会介绍此元素。
- sql – 可被其他语句引用的可重用语句块。
- insert – 映射插入语句
- update – 映射更新语句
- delete – 映射删除语句
- select – 映射查询语句

### select

```xml
<select id="findUserById" parameterType="Integer" resultType="User">
	<!-- #{} 表示占位符，告诉 MyBatis 创建一个预处理语句（PreparedStatement）参数。相当于sql中的"?"占位符。中间的参考可以任意命名-->
	select * from user where id = #{v}
</select>

<select id="findUserByUsername" parameterType="String" resultType="User">
	<!-- ${} 代表字符串拼接 中间参数只能用value -->
	select * from user where username like '%${value}%'
</select>

<select id="findUserByWrapper" parameterType="UserWrapper" resultType="User">
	<!-- user是包装类中的字段 -->
	select * from user where username like "%"#{user.username}"%"
</select>
```

select 元素的属性

|属性|描述|
|---|---|
|id|在命名空间中唯一的标识符，可以被用来引用这条语句。|
|parameterType|将会传入这条语句的参数类的完全限定名或别名。这个属性是可选的，因为 MyBatis 可以通过类型处理器（TypeHandler） 推断出具体传入语句的参数，默认值为未设置（unset）。|
|parameterMap|已被废弃。请使用内联参数映射和 parameterType 属性。|
|resultType|从这条语句中返回的期望类型的类的完全限定名或别名。注意如果返回的是集合，那应该设置为集合包含的类型，而不是集合本身。可以使用 resultType 或 resultMap，但不能同时使用。|
|resultMap|外部 resultMap 的命名引用。可以使用 resultMap 或 resultType，但不能同时使用。|
|flushCache|将其设置为 true 后，只要语句被调用，都会导致本地缓存和二级缓存被清空，默认值：false。|
|useCache|将其设置为 true 后，将会导致本条语句的结果被二级缓存缓存起来，默认值：对 select 元素为 true。|
|timeout|这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数。默认值为未设置（unset）（依赖驱动）。|
|fetchSize|这是一个给驱动的提示，尝试让驱动程序每次批量返回的结果行数和这个设置值相等。 默认值为未设置（unset）（依赖驱动）。|
|statementType|STATEMENT，PREPARED 或 CALLABLE 中的一个。这会让 MyBatis 分别使用 Statement，PreparedStatement 或 CallableStatement，默认值：PREPARED。|
|resultSetType|FORWARD_ONLY，SCROLL_SENSITIVE, SCROLL_INSENSITIVE 或 DEFAULT（等价于 unset） 中的一个，默认值为 unset （依赖驱动）。|
|databaseId|如果配置了数据库厂商标识（databaseIdProvider），MyBatis 会加载所有的不带 databaseId 或匹配当前 databaseId 的语句；如果带或者不带的语句都有，则不带的会被忽略。|
|resultOrdered|这个设置仅针对嵌套结果 select 语句适用：如果为 true，就是假设包含了嵌套结果集或是分组，这样的话当返回一个主结果行的时候，就不会发生有对前面结果集的引用的情况。 这就使得在获取嵌套的结果集的时候不至于导致内存不够用。默认值：false。|
|resultSets|这个设置仅对多结果集的情况适用。它将列出语句执行后返回的结果集并给每个结果集一个名称，名称是逗号分隔的。|

### insert, update 和 delete

```xml
<insert id="insertUser" parameterType="User">
	<!-- 将insert的数据的主键返回，直接拿到新增数据的主键。对象的id属性由keyProperty指定 -->
	<selectKey keyProperty="id" resultType="Integer" order="AFTER">
		select LAST_INSERT_ID()
	</selectKey>
	<!-- 可以通过#{}自动获取对象中的字段值 -->
	insert into user(username, password) values（#{username}, #{password})
</insert>

<update id="updateUserById" parameterType="User">
	update user set username = #{username} where id = #{id}
</update>

<delete id="deleteUserById" parameterType="Integer">
	delete from user where id = #{v}
</delete>
```

如果数据库支持自动生成主键的字段，则可以使用以下配置自动生成主键

```xml
<!-- 批量插入数据，并自动生成主键 -->
<insert id="insertAuthor" useGeneratedKeys="true"
    keyProperty="id">
  insert into Author (username, password, email, bio) values
  <foreach item="item" collection="list" separator=",">
    (#{item.username}, #{item.password}, #{item.email}, #{item.bio})
  </foreach>
</insert>
```

详细属性等，见mybatis官方参考文档

### sql

用来定义可重用的 SQL 代码段，这些 SQL 代码可以被包含在其他语句中

```xml
<sql id="userColumns"> ${alias}.id,${alias}.username,${alias}.password </sql>

<select id="selectUsers" resultType="map">
  select
    <include refid="userColumns"><property name="alias" value="t1"/></include>,
    <include refid="userColumns"><property name="alias" value="t2"/></include>
  from some_table t1
    cross join some_table t2
</select>
```

### 参数

```xml
<insert id="insertUser" parameterType="User">
  insert into users (id, username, password)
  values (#{id}, #{username}, #{password})
</insert>
```

如果 User 类型的参数对象传递到了语句中，id、username 和 password 属性将会被查找，然后将它们的值传入预处理语句的参数中。 

参数也可以指定一个特殊的数据类型。

```xml
<!-- 指定一个特殊的数据类型 -->
#{property,javaType=int,jdbcType=NUMERIC}
<!-- 指定自定义的类型转换器 -->
#{age,javaType=int,jdbcType=NUMERIC,typeHandler=MyTypeHandler}
<!-- 指定小数点后保留的位数 -->
#{height,javaType=double,jdbcType=NUMERIC,numericScale=2}
<!-- jdbcType=CURSOR，必须使用resultMap；mode参数为out或者inout，参数对象属性的真实值将被改变 -->
#{department, mode=OUT, jdbcType=CURSOR, javaType=ResultSet, resultMap=departmentResultMap}
```

JDBC 要求，如果一个列允许 null 值，并且会传递值 null 的参数，就必须要指定 jdbcType。

#### 传递多个参数

1. 利用map进行传参

将多个参数设置到map里，用#{键值}来取

```java
public User findUser(String loginname, String loginpassword) {
    Map<String,String> map=new HashMap<String, String>();
    map.put("loginname", loginname);
    map.put("loginpassword", loginpassword);
    return (User) sqlSessionTemplate.selectOne("loginUser", map);
}
```

```xml
<select id="loginUser" parameterType="map" resultType="User">
    select * from user where loginname=#{loginname} and loginpassword=#{loginpassword}
</select>
```

2. 利用注解方式

使用@Param（"参数名"）注解的方式，在sql语句中直接用 `#{参数名}` 取出

```java
public User findUser2(@Param("loginname")String loginname, @Param("password")String password);
```

```xml
<select id="findUser2" parameterType="string" resultType="User">
	select * from user where loginname=#{loginname} and password=#{password}
</select>
```

3. 利用 `#{0.1.2....}` 来取

`#{0}` 表示第一个参数，`#{1}` 表示第二个参数，以此类推

```java
public User findUser2(String loginname,String password);
```

```xml
<select id="findUser2" parameterType="string" resultType="User">
    select * from user where loginname=#{0} and password=#{1}
</select>
```

### 字符串替换

有时想直接在 SQL 语句中插入一个不转义的字符串。 比如，像 ORDER BY + 某列列名，你可以这样来使用：

```xml
<!-- columnName 不会加双引号 -->
ORDER BY ${columnName}
```

这里 MyBatis 不会修改或转义字符串。

`#{}` 表示占位符，会在字符串两端添加引号后，加入sql语句。

### 结果映射 resultMap

将结果直接映射到 POJO 类User中

原理：MyBatis 会在幕后自动创建一个 ResultMap，再基于属性名来映射列到 JavaBean 的属性上。

```xml
<!-- mybatis-config.xml 中 -->
<typeAlias type="com.someapp.model.User" alias="User"/>

<!-- SQL 映射 XML 中 -->
<select id="selectUsers" resultType="User">
  select 
  <!-- 如果列名和属性名没有精确匹配，可以在 SELECT 语句中对列使用别名 -->
  user_id as id, 
  username, hashedPassword
  from some_table
  where id = #{id}
</select>

<!-- 解决列名不匹配的另外一种方式 -->
<resultMap id="userResultMap" type="User">
  <id property="id" column="user_id" />
  <result property="username" column="user_name"/>
  <result property="password" column="hashed_password"/>
</resultMap>

<select id="selectUsers" resultMap="userResultMap">
  ...
</select>
```

resultMap 元素的子元素们。

- resultMap
	- constructor : 用于在实例化类时，注入结果到构造方法中
	    - idArg : ID 参数；标记出作为 ID 的结果可以帮助提高整体性能
	    - arg : 将被注入到构造方法的一个普通结果
	- id : 一个 ID 结果；标记出作为 ID 的结果可以帮助提高整体性能
	- result : 注入到字段或 JavaBean 属性的普通结果
	- association : 一个复杂类型的关联；许多结果将包装成这种类型
	    嵌套结果映射 : 关联本身可以是一个 resultMap 元素，或者从别处引用一个
	- collection : 一个复杂类型的集合
	    嵌套结果映射 : 集合本身可以是一个 resultMap 元素，或者从别处引用一个
	- discriminator : 使用结果值来决定使用哪个 resultMap
	    - case : 基于某些值的结果映射
	        - 嵌套结果映射 : case 本身可以是一个 resultMap 元素，因此可以具有相同的结构和元素，或者从别处引用一个

id & result

```xml
<resultMap id="..." type="...">
  <id property="id" column="post_id"/>
  <result property="subject" column="post_subject"/>
</resultMap>
```

id 和 result 元素都将一个列的值映射到一个简单数据类型（String, int, double, Date 等）的属性或字段。这两者之间的唯一不同是，id 元素表示的结果将是对象的标识属性

构造方法注入

将数据库中的内容，通过类的构造器，注入到实例中。（resultMap用的是setter方法）

```xml
<constructor>
   <idArg column="id" javaType="int"/>
   <arg column="username" javaType="String"/>
   <arg column="age" javaType="_int"/>
</constructor>
```

#### 关联

某个java对象的属性，引用了另一个java对象的情况，就是关联。

```xml
<!-- 从BLOG表中查询了一条信息，再使用select查询加载author属性的详细信息。（性能不佳） -->
<resultMap id="blogResult" type="Blog">
  <association property="author" column="author_id" javaType="Author" select="selectAuthor"/>
</resultMap>

<select id="selectBlog" resultMap="blogResult">
  SELECT * FROM BLOG WHERE ID = #{id}
</select>

<select id="selectAuthor" resultType="Author">
  SELECT * FROM AUTHOR WHERE ID = #{id}
</select>

<!-- 另一种关联方式，适用于多表查询 -->
<select id="selectBlog" resultMap="blogResult">
  select
    B.id as blog_id,
    B.title as blog_title,
    B.author_id as blog_author_id,
    A.id as author_id,
    A.username as author_username,
    A.password as author_password,
    A.email as author_email,
    A.bio as author_bio
  from Blog B left outer join Author A on B.author_id = A.id
  where B.id = #{id}
</select>

<resultMap id="blogResult" type="Blog">
  <id property="id" column="blog_id" />
  <result property="title" column="blog_title"/>
  <association property="author" column="blog_author_id" javaType="Author" resultMap="authorResult"/>
</resultMap>

<resultMap id="authorResult" type="Author">
  <id property="id" column="author_id"/>
  <result property="username" column="author_username"/>
  <result property="password" column="author_password"/>
  <result property="email" column="author_email"/>
  <result property="bio" column="author_bio"/>
</resultMap>
```

#### 集合

处理多表查询中，一对多映射关系。如一个博客有很多文章。

对应于一个java对象的某个属性，是另一个java对象的集合。

```xml
<resultMap id="blogResult" type="Blog">
  <!-- 对应java对象的属性：
    private List<Post> posts;
   -->
  <collection property="posts" column="id" javaType="ArrayList" ofType="Post" select="selectPostsForBlog"/>
</resultMap>

<select id="selectBlog" resultMap="blogResult">
  SELECT * FROM BLOG WHERE ID = #{id}
</select>

<select id="selectPostsForBlog" resultType="Post">
  SELECT * FROM POST WHERE BLOG_ID = #{id}
</select>

<!-- 另一个种查询方式 -->
<select id="selectBlog" resultMap="blogResult">
  select
    B.id as blog_id,
    B.title as blog_title,
    B.author_id as blog_author_id,
    P.id as post_id,
    P.subject as post_subject,
    P.body as post_body,
  from Blog B left outer join Post P on B.id = P.blog_id
  where B.id = #{id}
</select>
<resultMap id="blogResult" type="Blog">
  <id property="id" column="blog_id" />
  <result property="title" column="blog_title"/>
  <collection property="posts" ofType="Post">
    <id property="id" column="post_id"/>
    <result property="subject" column="post_subject"/>
    <result property="body" column="post_body"/>
  </collection>
</resultMap>
```

### 鉴别器

鉴别器表现的很像 Java 语言中的 switch 语句

```xml
<resultMap id="vehicleResult" type="Vehicle">
  <id property="id" column="id" />
  <discriminator javaType="int" column="vehicle_type">
    <case value="1" resultMap="carResult">
      <result property="doorCount" column="door_count" />
    </case>
    <case value="2" resultMap="truckResult"/>
    <case value="3" resultMap="vanResult"/>
    <case value="4" resultMap="suvResult"/>
  </discriminator>
</resultMap>
```

MyBatis 会从结果集中得到每条记录，然后比较它的 vehicle_type 的值。

1. 匹配任何一个鉴别器的实例，那么就使用这个实例指定的结果映射。
2. 都不匹配，则使用外层resultMap的Vehicle类映射结果。

### 自动映射

当自动映射查询结果时，MyBatis 会获取结果中返回的列名并在 Java 类中查找相同名字的属性（忽略大小写）。

通常数据库列使用大写字母组成的单词命名，单词间用下划线分隔；而 Java 属性一般遵循驼峰命名法约定。为了在这两种命名方式之间启用自动映射，需要将 mapUnderscoreToCamelCase 设置为 true。 

在 resultMap 中，没有配置的列，会自动映射。

有三种自动映射等级

- NONE : 禁用自动映射。仅设置手动映射属性。
- PARTIAL : 将自动映射结果除了那些有内部定义内嵌结果映射的(joins)（即，有association的resultMap不自动映射）。默认值
- FULL : 自动映射所有。

```xml
<!-- autoMapping 启用或者禁用自动映射 -->
<resultMap id="userResultMap" type="User" autoMapping="false">
  <result property="password" column="hashed_password"/>
</resultMap>
```

### 缓存

默认情况下是没有开启缓存的

```xml
<!-- 开启缓存 -->
<cache/>

<!-- 修改缓存属性 -->
<cache
  eviction="FIFO"
  flushInterval="60000"
  size="512"
  readOnly="true"/>
```

效果

- 映射语句文件中的所有 select 语句将会被缓存。
- 映射语句文件中的所有 insert, update 和 delete 语句会刷新缓存。
- 缓存会使用 Least Recently Used(LRU，最近最少使用的)算法来收回。
- 根据时间表(比如 no Flush Interval,没有刷新间隔)，缓存不会以任何时间顺序来刷新。
- 缓存会存储列表集合或对象(无论查询方法返回什么)的 1024 个引用。
- 缓存会被视为是 read/write(可读/可写)的缓存，意味着对象检索不是共享的，而且可以安全地被调用者修改，而不干扰其他调用者或线程所做的潜在修改。

可用的收回策略有:

– LRU 最近最少使用的 : 移除最长时间不被使用的对象。(默认)
– FIFO 先进先出 : 按对象进入缓存的顺序来移除它们。
– SOFT 软引用 : 移除基于垃圾回收器状态和软引用规则的对象。
– WEAK 弱引用 : 更积极地移除基于垃圾收集器状态和弱引用规则的对象。

属性

- flushInterval(刷新间隔)，单位毫秒。默认仅调用语句时刷新
- size(引用数目)，默认1024
- readOnly(只读)，默认false

使用自定义缓存

```xml
<!-- MyCustomCache 必需实现 org.mybatis.cache.Cache 接口 -->
<cache type="com.domain.something.MyCustomCache"/>
```

略，见官方参考手册

## 动态SQL

动态SQL元素

- if
- choose (when, otherwise)
- trim (where, set)
- foreach

### if

根据条件包含 where 子句的一部分

```xml
<select id="findActiveBlogWithTitleLike"
     resultType="Blog">
  SELECT * FROM BLOG
  WHERE state = ‘ACTIVE’
  <if test="title != null and username != null">
    AND title like #{title}
  </if>
</select>
```

### choose, when, otherwise

choose 元素有点像 Java 中的 switch 语句。

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <choose>
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND author_name like #{author.name}
    </when>
    <otherwise>
      AND featured = 1
    </otherwise>
  </choose>
</select>
```

### trim, where, set

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG
  <where>
    <if test="state != null">
         state = #{state}
    </if>
    <if test="title != null">
        AND title like #{title}
    </if>
    <if test="author != null and author.name != null">
        AND author_name like #{author.name}
    </if>
  </where>
</select>

<!-- 和where元素等价。所有在 prefixOverrides 属性中指定的内容将被移除，并且插入 prefix 属性中指定的内容 -->
<trim prefix="WHERE" prefixOverrides="AND |OR ">
  ...
</trim>
```

where 元素只会在至少有一个子元素的条件返回 SQL 子句的情况下才去插入“WHERE”子句。而且，若语句的开头为“AND”或“OR”，where 元素也会将它们去除。

```xml
<update id="updateAuthorIfNecessary">
  update Author
    <set>
      <if test="username != null">username=#{username},</if>
      <if test="password != null">password=#{password},</if>
      <if test="email != null">email=#{email},</if>
      <if test="bio != null">bio=#{bio}</if>
    </set>
  where id=#{id}
</update>
```

set 元素会动态前置 SET 关键字，同时也会删掉无关的逗号.

### foreach

对一个集合进行遍历，通常是在构建 IN 条件语句的时候

```xml
<select id="selectPostIn" resultType="domain.blog.Post">
  SELECT *
  FROM POST P
  WHERE ID in
  <!-- index 是当前迭代的次数，item 的值是本次迭代获取的元素 -->
  <foreach item="item" index="index" collection="list"
      open="(" separator="," close=")">
        #{item}
  </foreach>
</select>


<!--
	item：List中的每个值
	separator： 分隔符
	open： 前缀
	close： 后缀
-->
<!-- 参数是List -->
<foreach collection="list" item="id" separator="," open="id in (" close=")">
	#{id}
</foreach>
<!-- 参数是数组 -->
<foreach collection="array" item="id" separator="," open="id in (" close=")">
	#{id}
</foreach>
```

foreach 元素允许指定一个集合，声明可以在元素体内使用的集合项（item）和索引（index）变量。它也允许指定开头与结尾的字符串以及在迭代结果之间放置分隔符。

可以将任何可迭代对象（如 List、Set 等）、Map 对象或者数组对象传递给 foreach 作为集合参数。

- 当使用可迭代对象或者数组时，index 是当前迭代的次数，item 的值是本次迭代获取的元素。
- 当使用 Map 对象（或者 Map.Entry 对象的集合）时，index 是键，item 是值。

### bind

bind 元素可以从 OGNL 表达式中创建一个变量并将其绑定到上下文。

```xml
<select id="selectBlogsLike" resultType="Blog">
  <bind name="pattern" value="'%' + _parameter.getTitle() + '%'" />
  SELECT * FROM BLOG
  WHERE title LIKE #{pattern}
</select>
```

### 动态 SQL 中可插拔的脚本语言

实现接口，以插入语言

```java
public interface LanguageDriver {
  ParameterHandler createParameterHandler(MappedStatement mappedStatement, Object parameterObject, BoundSql boundSql);
  SqlSource createSqlSource(Configuration configuration, XNode script, Class<?> parameterType);
  SqlSource createSqlSource(Configuration configuration, String script, Class<?> parameterType);
}
```

```xml
<!-- mybatis-config.xml -->
<typeAliases>
  <typeAlias type="org.sample.MyLanguageDriver" alias="myLanguage"/>
</typeAliases>
<settings>
  <setting name="defaultScriptingLanguage" value="myLanguage"/>
</settings>
```

```xml
<!-- someMapper.xml -->
<!-- 只用在特定语句 -->
<select id="selectBlog" lang="myLanguage">
  SELECT * FROM BLOG
</select>
```

```java
public interface Mapper {
  @Lang(MyLanguageDriver.class)
  @Select("SELECT * FROM BLOG")
  List<Blog> selectBlog();
}
```

细节请参考 MyBatis-Velocity 项目

## Java API

### SqlSessionFactoryBuilder

```java
SqlSessionFactory build(InputStream inputStream)
SqlSessionFactory build(InputStream inputStream, String environment)
SqlSessionFactory build(InputStream inputStream, Properties properties)
SqlSessionFactory build(InputStream inputStream, String env, Properties props)
SqlSessionFactory build(Configuration config)
```

### SqlSessionFactory

SqlSessionFactory 有六个方法可以用来创建 SqlSession 实例

```java
SqlSession openSession()
SqlSession openSession(boolean autoCommit)
SqlSession openSession(Connection connection)
SqlSession openSession(TransactionIsolationLevel level)
SqlSession openSession(ExecutorType execType,TransactionIsolationLevel level)
SqlSession openSession(ExecutorType execType)
SqlSession openSession(ExecutorType execType, boolean autoCommit)
SqlSession openSession(ExecutorType execType, Connection connection)
Configuration getConfiguration();
```

默认的 openSession()方法没有参数,它会创建有如下特性的 SqlSession:

- 会开启一个事务(也就是不自动提交)
- 连接对象会从由活动环境配置的数据源实例中得到。
- 事务隔离级别将会使用驱动或数据源的默认设置。
- 预处理语句不会被复用,也不会批量处理更新。

ExecutorType 这个枚举类型定义了 3 个值:

- ExecutorType.SIMPLE : 这个执行器类型不做特殊的事情。它为每个语句的执行创建一个新的预处理语句。
- ExecutorType.REUSE : 这个执行器类型会复用预处理语句。
- ExecutorType.BATCH : 这个执行器会批量执行所有更新语句,如果 SELECT 在它们中间执行还会标定它们是必须的,来保证一个简单并易于理解的行为。

### SqlSession

```java
// 语句执行方法（其他方法见官方说明文档）
<T> T selectOne(String statement, Object parameter)
<E> List<E> selectList(String statement, Object parameter)
<K,V> Map<K,V> selectMap(String statement, Object parameter, String mapKey)
int insert(String statement, Object parameter)
int update(String statement, Object parameter)
int delete(String statement, Object parameter)
// 批量立即更新方法
List<BatchResult> flushStatements()
// 事务控制方法
void commit()
void commit(boolean force)
void rollback()
void rollback(boolean force)
// 清理 Session 级的缓存
void clearCache()
// 确保 SqlSession 被关闭
void close()
// 使用映射器
<T> T getMapper(Class<T> type)
// 映射器注解（见官方说明文档）
```

### 映射器类

只需要写 Dao 层接口和 Mapper.xml，可以自动生成 Dao 层接口的实现类

接口的四个原则

1. 接口的类路径与 Mapper.xml 的命名空间名相同
2. 接口方法名 == Mapper.xml 中的 id 名
2. 返回值类型与 Mapper.xml 中的返回值类型要一致
3. 方法的入参类型与 Mapper.xml 中的入参类型要一致

接口

```java
public interface DemoMapper {
  Student queryById(int id);
}
```

使用示例

```java
// 加载核心配置文件
String resource = "SqlMapConfig.xml";
InputStream in = Resources.getResourceAsStream(resource);
// 创建SqlSessionFactory
SqlSessionFactory ssf = new SqlSessionFactoryBuilder().build(in);
// 创建SqlSession
SqlSession ss = ssf.openSession();
// SqlSession自动生成(Dao层的UserMapper接口的)实现类
DemoMapper mapper = ss.getMapper(DemoMapper.class);
// 调用对象的方法
Student stu = mapper.queryById(10);
```

## SQL 语句构建器类

SQL 类

```java
public String insertPersonSql() {
  String sql = new SQL()
    .INSERT_INTO("PERSON")
    .VALUES("ID, FIRST_NAME", "#{id}, #{firstName}")
    .VALUES("LAST_NAME", "#{lastName}")
    .toString();
  return sql;
}
```

## Logging

指定日志框架

```xml
<configuration>
  <settings>
    ...
    <setting name="logImpl" value="LOG4J"/>
    ...
  </settings>
</configuration>
```

## 与Spring整合

[官方参考文档](http://www.mybatis.org/spring/zh/getting-started.html)

### 快速入门

需要导入 mybatis-spring 包

文档结构(仅供参考)

```
- myProject/
| - src/
| | - myPackage/(包)
| | | - MyBatisTest.java
| | - mapper/(包)
| | | - DemoMapper.java
| | | - DemoMapper.xml
| | - pojo/(包)
| | | - Student.java
| | - jdbc.properties
| | - mybatis-config.xml
| | - spring-config.xml
```

MyBatisTest.java

从数据库获取数据

```java
@Test
public void test2() throws IOException {
	ApplicationContext container = new ClassPathXmlApplicationContext("spring-config.xml");
	DemoMapper demoMapper = container.getBean(DemoMapper.class);
	Student stu = demoMapper.queryById(1);
	System.out.println(stu);
}
```

spring-config.xml

配置mybatis的SqlSessionFactory、映射器类等

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">

	<!-- 引入配置文件 -->
	<context:property-placeholder
		location="classpath:jdbc.properties" />

	<!-- 配置数据源(DBCP) -->
	<bean id="dataSource"
		class="org.apache.commons.dbcp2.BasicDataSource"
		destroy-method="close">
		<property name="driverClassName" value="${driver}" />
		<property name="url" value="${url}" />
		<property name="username" value="${username}" />
		<property name="password" value="${password}" />
	</bean>

	<!-- Mybatis: SqlSessionFactory -->
	<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="dataSource" />
    <!-- 核心配置文件的位置 -->
    <property name="configLocation" value="classpath:mybatis-config.xml" />
	</bean>

	<!-- 手动注册映射器 -->
	<!-- <bean id="demoMapper" class="org.mybatis.spring.mapper.MapperFactoryBean">
		<property name="mapperInterface" value="mapper.DemoMapper" />
		<property name="sqlSessionFactory" ref="sqlSessionFactory" />
	</bean> -->
  <!-- 或者：自动扫描发现映射器 -->
  <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <property name="basePackage" value="mapper" />
  </bean>

</beans>
```

在 MyBatis-Spring 中，使用 SqlSessionFactoryBean 来创建 SqlSessionFactory。

mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<!-- <mappers>
		<mapper resource="mapper/DemoMapper.xml" />
	</mappers> -->
</configuration>
```

如果映射器接口 DemoMapper 在相同的类路径下有对应的 MyBatis XML 映射器配置文件，将会被 MapperFactoryBean 自动解析。不需要在 MyBatis 配置文件中显式配置映射器。

MyBatis-Spring 借助了 Spring 中的 DataSourceTransactionManager 来实现事务管理。 

### 发现映射器

不需要一个个地注册你的所有映射器。你可以让 MyBatis-Spring 对类路径进行扫描来发现它们。

有几种办法来发现映射器：

- 使用 <mybatis:scan/> 元素
- 使用 @MapperScan 注解
- 在经典 Spring XML 配置文件中注册一个 MapperScannerConfigurer

```xml
<mybatis:scan base-package="org.mybatis.spring.sample.mapper" />

<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
  <property name="basePackage" value="org.mybatis.spring.sample.mapper" />
</bean>
```

## 逆向工程

[逆向工程项目地址，可下载核心包](https://github.com/mybatis/generator/releases)

[逆向工程使用说明](https://blog.csdn.net/qq_39056805/article/details/80585941)

generator

```java
package generator;
import java.io.File;
import java.util.*;
import org.mybatis.generator.api.MyBatisGenerator;
import org.mybatis.generator.config.Configuration;
import org.mybatis.generator.config.xml.ConfigurationParser;
import org.mybatis.generator.internal.DefaultShellCallback;
 
public class GeneratorSqlmap {
 
  public void generator() throws Exception {
    List<String> warnings = new ArrayList<String>();
    boolean overwrite = true;
    // 指定配置文件
    File configFile = new File("./config/generatorConfig.xml");
    ConfigurationParser cp = new ConfigurationParser(warnings);
    Configuration config = cp.parseConfiguration(configFile);
    DefaultShellCallback callback = new DefaultShellCallback(overwrite);
    MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config, callback, warnings);
    myBatisGenerator.generate(null);
  }
 
  // 执行main方法以生成代码
  public static void main(String[] args) {
    System.out.println("running...");
    try {
      GeneratorSqlmap generatorSqlmap = new GeneratorSqlmap();
      generatorSqlmap.generator();
    } catch (Exception e) {
      e.printStackTrace();
    }
  }
}
```

generatorConfig

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN" "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
 
<generatorConfiguration>
  <context id="DB2Tables" targetRuntime="MyBatis3">
    <commentGenerator>
      <!-- 是否去除自动生成的注释 -->
      <property name="suppressAllComments" value="true"/>
    </commentGenerator>
    <!-- Mysql数据库连接的信息：驱动类、连接地址、用户名、密码 -->
    <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
        connectionURL="jdbc:mysql://localhost/mall"
        userId="root"
        password="bejavaer">
    </jdbcConnection>
    <!-- Oracle数据库
      <jdbcConnection driverClass="oracle.jdbc.OracleDriver"
          connectionURL="jdbc:oracle:thin:@127.0.0.1:1521:yycg"
          userId="yycg"
          password="yycg">
      </jdbcConnection> 
    -->
  
  <!-- 默认为false，把JDBC DECIMAL 和NUMERIC类型解析为Integer，为true时
  把JDBC DECIMAL 和NUMERIC类型解析为java.math.BigDecimal -->
    <javaTypeResolver >
    <property name="forceBigDecimals" value="false" />
    </javaTypeResolver>
  
  <!-- targetProject：生成POJO类的位置 -->
    <javaModelGenerator targetPackage="top.rainyrun.mall.dao.pojo" targetProject="./src">
    <!-- enableSubPackages:是否让schema作为包的后缀 -->
    <property name="enableSubPackages" value="false" />
    <!-- 从数据库返回的值被清理前后的空格 -->
    <property name="trimStrings" value="true" />
    </javaModelGenerator>
    
  <!-- targetProject：mapper映射文件生成的位置 -->
    <sqlMapGenerator targetPackage="top.rainyrun.mall.dao.mapper"  targetProject="./src">
    <!-- enableSubPackages:是否让schema作为包的后缀 -->
    <property name="enableSubPackages" value="false" />
    </sqlMapGenerator>
    
  <!-- targetProject：mapper接口生成的的位置 -->
  <javaClientGenerator type="XMLMAPPER" targetPackage="top.rainyrun.mall.dao.mapper"  targetProject="./src">
    <!-- enableSubPackages:是否让schema作为包的后缀 -->
    <property name="enableSubPackages" value="false" />
    </javaClientGenerator>
    
  <!-- 指定数据表 -->
  <table schema="" tableName="mall_item"></table>
  <!-- <table schema="" tableName="mall_content"></table>
  <table schema="" tableName="mall_content_cat"></table>
  <table schema="" tableName="mall_item"></table>
  <table schema="" tableName="mall_item_cat"></table>
  <table schema="" tableName="mall_item_desc"></table>
  <table schema="" tableName="mall_item_param"></table>
  <table schema="" tableName="mall_item_param_item"></table>
  <table schema="" tableName="mall_order_shipping"></table>
  <table schema="" tableName="mall_order"></table>
  <table schema="" tableName="mall_order_item"></table>
  <table schema="" tableName="mall_user"></table> -->
    
    <!-- 有些表的字段需要指定java类型 
    <table schema="DB2ADMIN" tableName="ALLTYPES" domainObjectName="Customer" >
      <property name="useActualColumnNames" value="true"/>
      <generatedKey column="ID" sqlStatement="DB2" identity="true" />
      <columnOverride column="DATE_FIELD" property="startDate" />
      <ignoreColumn column="FRED" />
      <columnOverride column="LONG_VARCHAR_FIELD" jdbcType="VARCHAR" />
    </table> -->
 
  </context>
</generatorConfiguration>
```

使用方法

设置排序顺序

```java
// 设置按时间逆序
MallOrderExample example = new MallOrderExample();
example.setOrderByClause("created DESC");
```

## 相关资料

[mybatis参考文档](http://www.mybatis.org/mybatis-3/zh/getting-started.html)

