# JSON

严格的json格式，key和字符串必须用双引号扩起来。

## JSON格式

- JSON对象

```
{
	"username":"zhangsan",
	"password":"hello"
}
```

- JSON数组

json对象的数组

```
[
    {}, {}, {}
]
```

## json与java对象互相转换

### JackSon

JackSon 是解析 JSON 和 XML 的一个框架，优点是简单易用，性能较高。

JackSon提供了三种JSON的处理方式

1. 数据绑定
2. 树模型
3. 流式API

简单示例

```java
private static final ObjectMapper MAPPER = new ObjectMapper();
// 将对象转换成json字符串
String objectJson = MAPPER.writeValueAsString(objectData);
// 将json结果集转化为对象
SomeType t = MAPPER.readValue(someTypeJson, SomeType.class);
// 将json数据转换成SomeType对象的list
JavaType javaType = MAPPER.getTypeFactory().constructParametricType(List.class, SomeType.class);
List<SomeType> list = MAPPER.readValue(someTypeJson, javaType);
````

#### 数据绑定

数据绑定用于 JSON 转化，可以将 JSON 与 POJO 对象进行转化。数据绑定有两种，简单数据绑定和完整数据绑定。

完整数据绑定

```java
import com.fasterxml.jackson.databind.ObjectMapper;
import java.io.IOException;

/**
 * 将json字符串映射为对象，或者是将对象转化为json字符串。这是完整数据绑定。
 缺点:这种方法十分方便，但是扩展性不强，增加一个字段便要修改POJO对象，这个操作有一定风险性。并且解析的时候，如果json缺少POJO中的某字段，映射出的对象对应值默认为null，直接使用有一定风险。如果json对象多了某一字段，解析过程中会抛出UnrecognizedPropertyException异常。并且如果json较为复杂的话，POJO对象会显得特别臃肿。
 */
public class CompleteDataBind {
    public static void main(String[] args) throws IOException {
        String s = "{\"id\": 1,\"name\": \"小明\",\"array\": [\"1\", \"2\"]}";
        ObjectMapper mapper = new ObjectMapper();
        //Json映射为对象
        Student student = mapper.readValue(s, Student.class);
        //对象转化为Json
        String json = mapper.writeValueAsString(student);
        System.out.println(json);
        System.out.println(student.toString());
    }
}
```

简单数据绑定

简单数据绑定就是将json字符串映射为java核心的数据类型。

|json类型|Java类型|
|----|----|
|object|LinkedHashMap|
|array|ArrayList|
|string|String|
|number|Integer,Long,Double|
|true, false|Boolean|
|null|null|

下面演示一个例子，将json转化为一个Map。通过Map来读取。

```java
import com.fasterxml.jackson.databind.ObjectMapper;
import java.io.IOException;
import java.util.*;

/**
 * 简单数据绑定的示例，不用POJO对象，直接映射为一个Map，然后从Map中获取。
 */
public class SimpleDataBind {
    public static void main(String[] args) throws IOException {
        Map<String, Object> map = new HashMap<>(16);
        String s = "{\"id\": 1,\"name\": \"小明\",\"array\": [\"1\", \"2\"]," +
                "\"test\":\"I'm test\",\"base\": {\"major\": \"物联网\",\"class\": \"3\"}}";
        ObjectMapper mapper = new ObjectMapper();
        map = mapper.readValue(s, map.getClass());
        //获取id
        Integer studentId = (Integer) map.get("id");
        System.out.println(studentId);
        //获取数据
        ArrayList list = (ArrayList) map.get("array");
        System.out.println(Arrays.toString(list.toArray()));
        //新增加的字段可以很方便的处理
        String test = (String) map.get("test");
        System.out.println(test);
        //不存在的返回null
        String notExist = (String) map.get("notExist");
        System.out.println(notExist);
        //嵌套的对象获取
        Map base = (Map) map.get("base");
        String major = (String) base.get("major");
        System.out.println(major);
    }
}
```

#### 树模型

将json串解析成各类节点

```java
import com.fasterxml.jackson.core.JsonPointer;
import com.fasterxml.jackson.databind.*;
import com.fasterxml.jackson.databind.node.*;
import java.io.IOException;

/**
 * JackSon树模型结构，可以通过get，JsonPointer等进行操作，适合用来获取大Json中的字段，比较灵活。缺点是如果需要获取的内容较多，
 * 会显得比较繁琐。
 */
public class TreeModel {
    public static void main(String[] args) throws IOException {
        ObjectMapper mapper = new ObjectMapper();
        //以下是对象转化为Json
        JsonNode root = mapper.createObjectNode();
        ((ObjectNode) root).putArray("array");
        ArrayNode arrayNode = (ArrayNode) root.get("array");
        ((ArrayNode) arrayNode).add("args1");
        ((ArrayNode) arrayNode).add("args2");
        ((ObjectNode) root).put("name", "小红");
        String json = mapper.writeValueAsString(root);
        System.out.println("使用树型模型构建的json:"+json);
        //以下是树模型的解析Json
        String s = "{\"id\": 1,\"name\": \"小明\",\"array\": [\"1\", \"2\"]," +
                "\"test\":\"I'm test\",\"nullNode\":null,\"base\": {\"major\": \"物联网\",\"class\": \"3\"}}";
        //读取rootNode
        JsonNode rootNode = mapper.readTree(s);
        //通过path获取
        System.out.println("通过path获取值：" + rootNode.path("name").asText());
        //通过JsonPointer可以直接按照路径获取
        JsonPointer pointer = JsonPointer.valueOf("/base/major");
        JsonNode node = rootNode.at(pointer);
        System.out.println("通过at获取值:" + node.asText());
        //通过get可以取对应的value
        JsonNode classNode = rootNode.get("base");
        System.out.println("通过get获取值：" + classNode.get("major").asText());

        //获取数组的值
        System.out.print("获取数组的值：");
        JsonNode arrayNode2 = rootNode.get("array");
        for (int i = 0; i < arrayNode2.size(); i++) {
            System.out.print(arrayNode2.get(i).asText()+" ");
        }
        System.out.println();
        //path和get方法看起来很相似，其实他们的细节不同，get方法取不存在的值的时候，会返回null。而path方法会
        //返回一个"missing node"，该"missing node"的isMissingNode方法返回值为true，如果调用该node的asText方法的话，
        // 结果是一个空字符串。
        System.out.println("get方法取不存在的节点，返回null:" + (rootNode.get("notExist") == null));
        JsonNode notExistNode = rootNode.path("notExist");
        System.out.println("notExistNode的value：" + notExistNode.asText());
        System.out.println("isMissingNode方法返回true:" + notExistNode.isMissingNode());
    
        //当key存在，而value为null的时候，get和path都会返回一个NullNode节点。
        System.out.println(rootNode.get("nullNode") instanceof NullNode);
        System.out.println(rootNode.path("nullNode") instanceof NullNode);
    }
}
```

#### 流式API

流式API是一套比较底层的API，速度快，但是使用起来特别麻烦。它主要是有两个核心类：

- JsonGenerator，用来生成json
- JsonParser，用来读取json内容

```java
import com.fasterxml.jackson.core.*;
import java.io.*;

/**
 *  JsonParser和Generator的优点是速度快，缺点是写起来真的很复杂。
 */
public class StreamApi {
    public static void main(String[] args) throws IOException {
        JsonFactory factory = new JsonFactory();
        String s = "{\"id\": 1,\"name\": \"小明\",\"array\": [\"1\", \"2\"]," +
                "\"test\":\"I'm test\",\"nullNode\":null,\"base\": {\"major\": \"物联网\",\"class\": \"3\"}}";

        //这里就举一个比较简单的例子，Generator的用法就是一个一个write即可。
        File file = new File("/json.txt");
        JsonGenerator jsonGenerator = factory.createGenerator(file, JsonEncoding.UTF8);
        //对象开始
        jsonGenerator.writeStartObject();
        //写入一个键值对
        jsonGenerator.writeStringField("name", "小光");
        //对象结束
        jsonGenerator.writeEndObject();
        //关闭jsonGenerator
        jsonGenerator.close();
        //读取刚刚写入的json
        FileInputStream inputStream = new FileInputStream(file);
        int i = 0;
        final int SIZE = 1024;
        byte[] buf = new byte[SIZE];
        StringBuilder sb = new StringBuilder();
        while ((i = inputStream.read(buf)) != -1) {
            System.out.println(new String(buf,0,i));
        }
        inputStream.close();

        //JsonParser解析的时候，思路是把json字符串根据边界符分割为若干个JsonToken，这个JsonToken是一个枚举类型。
        //下面这个小例子，可以看出JsonToken是如何划分类型的。
        JsonParser parser = factory.createParser(s);
        while (!parser.isClosed()){
            JsonToken token = parser.currentToken();
            System.out.println(token);
            parser.nextToken();
        }
    
        JsonParser jsonParser = factory.createParser(s);
        //下面是一个解析的实例
        while (!jsonParser.isClosed()) {
            JsonToken token  = jsonParser.nextToken();
            if (JsonToken.FIELD_NAME.equals(token)) {
                String currentName = jsonParser.currentName();
                token = jsonParser.nextToken();
                if ("id".equals(currentName)) {
                    System.out.println("id:" + jsonParser.getValueAsInt());
                } else if ("name".equals(currentName)) {
                    System.out.println("name:" + jsonParser.getValueAsString());
                } else if ("array".equals(currentName)) {
                    token = jsonParser.nextToken();
                    while (!JsonToken.END_ARRAY.equals(token)) {
                        System.out.println("array:" + jsonParser.getValueAsString());
                        token = jsonParser.nextToken();
                    }
                }
            }
        }
    }

}
```

JackSon的常用注解

JackSon提供了一些的注解，可以用在类上或者是在字段上。通常是数据绑定的时候使用。下面几个是最常用的几个

@JsonInclude(Include.NON_EMPTY)

仅在属性不为空时序列化此字段，对于字符串，即null或空字符串

@JsonIgnore

序列化时忽略此字段

@JsonProperty(value = "user_name")

指定序列化时的字段名，默认使用属性名

#### 设置转换的日期格式

默认显示的是一串数字代表的时间戳。

1. 普通的方式

默认是转成timestamps形式的，通过下面方式可以取消timestamps。

```java
ObjectMapper objectMapper = new ObjectMapper();
// objectMapper.configure(SerializationConfig.Feature.WRITE_DATES_AS_TIMESTAMPS, false);
// 或自定义输出格式
SimpleDateFormat myDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
objectMapper.setDateFormat(myDateFormat);
// 输出类似如下格式的时间: "1970-01-01T00:00:00.000+0000"
```

2. annotaion的注释方式

Java代码
```java
import java.io.IOException;
import java.text.SimpleDateFormat;
import java.util.Date;
import org.codehaus.jackson.JsonGenerator;
import org.codehaus.jackson.JsonProcessingException;
import org.codehaus.jackson.map.JsonSerializer;
import org.codehaus.jackson.map.SerializerProvider;
/** 
java日期对象经过Jackson库转换成JSON日期格式化自定义类
*/
public class CustomDateSerializer extends JsonSerializer {
    @Override
    public void serialize(Date value, JsonGenerator jgen, SerializerProvider provider) throws IOException, JsonProcessingException {
            SimpleDateFormat formatter = new SimpleDateFormat("yyyy-MM-dd");
            String formattedDate = formatter.format(value);
            jgen.writeString(formattedDate);
        }
}
```

然后在你的POJO上找到日期的get方法

```java
@JsonSerialize(using = CustomDateSerializer.class)
public Date getCreateAt() {
    return createAt;
}
```

### fastjson

阿里巴巴提供的json和java对象转换工具。优点是不需要依赖包，转化速度快。

```java
String text = JSON.toJSONString(obj); //序列化
VO vo = JSON.parseObject("{...}", VO.class); //反序列化
```

前端，js使用json串

```js
$(function(){
	$("#province").change(function(){
		var pid = $(this).val();
		$.post("CityServlet02", {pid:pid}, function(data, status){
			// data是一个json对象数组
			$(data).each(function(index, element){
				var id = element.id;
				var cname = element.cname;
				// 将代码加入到页面
				$("#city").append("<option value='" + id +"' />" + cname);
			});
		}, "json");
	});
});
```

### jsonlib(已过时？)

使用json-lib等jar包

```java
// 变成json对象
JSONObject js = JSONObject.fromObject(Object);
// 将list转成json数组
JSONArray jsonArray = JSONArray.fromObject(list);
String json = jsonArray.toString();

// 设置响应格式
// 告诉浏览器本次响应的数据是JSON格式的字符串
response.setContentType("application/json;charset=utf-8");
response.getWriter().write(json);
```

## 参考资料

http://blog.lifw.org/post/63088058v 

https://www.yiibai.com/jackson/

[日期转换参考文档](https://blog.csdn.net/cckevincyh/article/details/81228312)


