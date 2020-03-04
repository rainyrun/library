# JUnit

JUnit 测试类只是普通的 Java 类，包含一个或多个测试方法，以及0个或多个 setup 和 teardown 方法。

声明测试方法

```java
// JUnit 测试类，通常以Test结尾
public class ExampleTest {
    @Test
    // 测试方法，通常以test开头
    public void testSomeMethod() {
        assertEquals(5, 2+3);
    }

    @Test
    public void testAnotherMethod() {
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

JUnit 断言方法

```java
assertArrayEquals("message", A, B);
assertEquals("message", A, B); // 使用 equals
assertSame("message", A, B); // 检查 AB 是否是同一个对象，使用 ==
assertTrue("message", A);
assertNotNull("message", A);
```

JUnit 框架的核心

- 测试类：含有多个测试方法的类
- 测试集(Suite 对象)：一组测试，将多个测试归入一组。
- 测试运行器(Runner 对象)：执行测试集的程序

### 运行参数化测试

参数化(Parameterized)的测试运行器，允许使用不同的参数多次运行同一个测试代码

```java
@RunWith(value=Parameterized.class) // 表示用 Parameterized 的测试运行器
public class ParameterizedTest {
    // 声明测试中使用的实例变量
    private double expected;
    private double valueOne;
    private double valueTwo;

    @Parameters
    public static Collection<Integer[]> getTestParameters() {
        return Arrrays.asList(new Integer[][] {
            {2, 1, 1},
            {3, 2, 1},
            {4, 3, 1},
        });
    }

    // 构造函数
    public ParameterizedTest(double expected, double valueOne, double valueTwo) {
        this.expected = expected;
        this.valueOne = valueOne;
        this.valueTwo = valueTwo;
    }

    // 测试方法
    @Test
    public void sum() {
        Calculator calc = new Calculator();
        assertEquals(expected, calc.add(valueOne, valueTwo), 0);
    }
}
```

### 测试运行器

使用 @RunWith 来制定测试运行器类。

```java
@RunWith(value=org.junit.internal.runners.JUnit38ClassRunner.class)
```

### 组合测试

Suite 是一个容器，用来把几个测试归在一起，并把他们作为一个集合一起运行。如果没有提供 Suite，会自动创建一个，将 @Test 注解标注的方法，加入集合。

```java
@RunWith(value=org.junit.runners.Suite.class)
@SuiteClasses(value={FolderConfigurationTest.class, FileConfigurationTest.class}) // 指定测试类
public class FileSystemConfigurationTestSuite {
}
```

## 掌握 JUnit

### 测试 Controller

```java
public class TestDefaultController {
    private DefaultController controller;

    @Before // 在测试方法前运行，不论测试通过还是失败。@After在测试方法执行后运行。
    public void instantiate() throws Exception {
        controller = new DefaultController();
    }
    @BeforeClass // 在所有@Test方法前执行一次。@AfterClass 在所有@Test方法后执行一次
    public static void someMethod(){...}

    @Test
    public void testMethod() {
        throw new RuntimeException("implement me"); // 未完成的测试方法抛出异常，使测试不通过，提醒要完成该方法。
    }
}
```

要创建一个单元测试，需要两种类型的对象

- 要测试的领域对象(Domain Object)
- 与被测试对象交互的测试对象(Test Object)

原则：一次只测试一个对象

