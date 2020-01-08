# JUnit

JUnit 测试类只是普通的 Java 类，包含一个或多个测试方法，以及0个或多个 setup 和 teardown 方法。

声明测试方法

```java
// JUnit 测试类
public class ExampleTest {
    @Test
    public void thisIsATestMethod() {
        assertEquals(5, 2+3);
    }

    @Test
    public void AnotherTestMethod() {
        // code
    }
}
```

Junit 发现以下几种方法，就会忽略它。

- 一个非 public 方法
- 带有参数的方法
- 返回值为 void 的方法
- 声明为static的方法
- 没有使用 @Test 注解的方法

即，方法只能是 public void 的，且不带参数。

JUnit 测试的生命周期

JUnit 扫描各个方法，对满足的方法

1. 实例化测试类的一个实例
2. 调用测试类实例的 setup 方法
3. 调用测试类
4. 调用测试类实例的 teardown 方法

setup 和 teardown

```java
public class ExampleTest {
    private Locale originalLocal;
    // 对每个方法执行前运行一次。可以有多个，但不保证执行顺序
    @Before
    public void setLocaleForTests(){
        this.originalLocal = Locale.getDefault();
        Locale.setDefault(Locale.US);
    }

    @After
    public void restoreOriginalLocale(){
        Locale.setDefault(originalLocal);
    }
}
```

JUnit 断言

声明预期的异常

```java
// 没有抛出预期的异常，则测试失败
@Test(expected = IllegalArgumentException.class)
public void throwProperException(){
    FaxMachine fax = new FaxMachine();
    fax.connect("xxxxxx"); // should throw an exception
}
// 想要测试抛出异常中的提示信息
@Test
public void throwProperException(){
    String invalidPhoneNumber = "xxxxxx";
    FaxMachine fax = new FaxMachine();
    try {
        fax.connect(invalidPhoneNumber);
        fail("should've raised an exception by now"); // 没有抛出异常，就测试失败
    } catch (IllegalArgumentException e) {
        assertThat(e.getMessage(), containsString(invalidPhoneNumber)); // 进一步对异常进行断言
    }
}
```

assertThat() 和 Hamcrest 匹配器

assertThat() 允许程序员自行扩展基本的断言。

语法： `assertThat(someObj, [匹配器]);`

匹配器用来执行实际的断言工作，是 Hamcrest 匹配器。

[Hamcrest](https://github.com/hamcrest/JavaHamcrest)






