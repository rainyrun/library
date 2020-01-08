# HttpClient

APIService

```java
package com.itheima.httpclient;

import java.net.URISyntaxException;
import java.util.ArrayList;
import java.util.List;
import java.util.Map;
import org.apache.http.NameValuePair;
import org.apache.http.client.entity.UrlEncodedFormEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.message.BasicNameValuePair;
import org.apache.http.util.EntityUtils;

public class APIService {
    // 1.不带参数的GET请求
    public HttpResult doget(String url) throws Exception {
        return doGet(url, null);
    }

    // 2.带参数的GET请求
    // 要返回http状态码以及响应的内容
    public HttpResult doGet(String url, Map<String, Object> map) throws Exception {
        // 1.创建httpclient对象
        CloseableHttpClient httpClient = HttpClients.createDefault();
        // 2.创建httget请求
        // 创建uribuilder 构建Url 及设置参数
        URIBuilder uriBuilder = new URIBuilder(url);
        // 循环遍历参数集合 设置参数
        // 判断如果map不为空
        if (map != null) {
            for (Map.Entry<String, Object> entry : map.entrySet()) {
                uriBuilder.setParameter(entry.getKey(), entry.getValue().toString());
            }
        }
        HttpGet httpGet = new HttpGet(uriBuilder.build());
        // 3.执行请求（发送请求）
        CloseableHttpResponse response = httpClient.execute(httpGet);
        // 4.接收响应内容，进行解析 封装到httpresult
        // 内容有的时候
        Integer code = response.getStatusLine().getStatusCode();// 响应的状态码
        if (response.getEntity() != null) {
            String body = EntityUtils.toString(response.getEntity(), "utf-8");// 获取响应的内容，转换成字符串
            HttpResult result = new HttpResult(code, body);
            return result;
        } else {
            HttpResult result = new HttpResult(code, null);
            return result;
        }
    }

    // 3.不带参数的POST请求
    public HttpResult doPost(String url) throws Exception{
        return doPost(url,null);
    }
    
    // 4.带参数的POST请求
    public HttpResult doPost(String url, Map<String, Object> map) throws Exception {

        // 1.创建httpclient
        CloseableHttpClient httpClient = HttpClients.createDefault();
        // 2.创建httppost请求
        HttpPost httpPost = new HttpPost(url);
        // 3.构建参数的列表
        //判断参数不为空的情况
        if (map != null) {
            List<NameValuePair> params = new ArrayList<>();
            // 遍历集合 构建参数列表
            for (Map.Entry<String, Object> entry : map.entrySet()) {
                params.add(new BasicNameValuePair(entry.getKey(), entry.getValue().toString()));
            }
            // 4.创建form表单传递参数的实体对象
            UrlEncodedFormEntity entity = new UrlEncodedFormEntity(params, "utf-8");

            // 设置httpost请求的实体对象
            httpPost.setEntity(entity);
        }
        // 5.执行请求
        CloseableHttpResponse response = httpClient.execute(httpPost);
        // 6.接收响应 封装到httpresult中
        // 内容有的时候
        Integer code = response.getStatusLine().getStatusCode();// 响应的状态码
        if (response.getEntity() != null) {
            String body = EntityUtils.toString(response.getEntity(), "utf-8");// 获取响应的内容，转换成字符串
            HttpResult result = new HttpResult(code, body);
            return result;
        } else {
            HttpResult result = new HttpResult(code, null);
            return result;
        }
    }

}

```

DoGet

```java
package com.itheima.httpclient;

import java.io.File;

import javax.swing.plaf.synth.SynthStyle;

import org.apache.commons.io.FileUtils;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

public class DoGET {

    public static void main(String[] args) throws Exception {

        // 创建Httpclient对象
        CloseableHttpClient httpclient = HttpClients.createDefault();

        // 创建http GET请求
        HttpGet httpGet = new HttpGet("http://localhost:8081/item/list?page=1&rows=10");

        CloseableHttpResponse response = null;
        try {
            // 执行请求
            response = httpclient.execute(httpGet);
            // 判断返回状态是否为200
            if (response.getStatusLine().getStatusCode() == 200) {
                String content = EntityUtils.toString(response.getEntity(), "UTF-8");
                System.out.println("内容长度：" + content.length());
                System.out.println(content);//响应的内容
            }
        } finally {
            if (response != null) {
                response.close();
            }
            httpclient.close();
        }

    }

}
```

DoGetParam

```java
package com.itheima.httpclient;

import java.net.URI;

import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

public class DoGETParam {

    public static void main(String[] args) throws Exception {

        // 创建Httpclient对象
        CloseableHttpClient httpclient = HttpClients.createDefault();

        // 定义请求的参数
        URI uri = new URIBuilder("http://localhost:8081/item/list").setParameter("page", "1").setParameter("rows", "10")
                .build();

        System.out.println(uri);

        // 创建http GET请求
        HttpGet httpGet = new HttpGet(uri);

        CloseableHttpResponse response = null;
        try {
            // 执行请求
            response = httpclient.execute(httpGet);
            // 判断返回状态是否为200
            if (response.getStatusLine().getStatusCode() == 200) {
                String content = EntityUtils.toString(response.getEntity(), "UTF-8");
                System.out.println(content);
            }
        } finally {
            if (response != null) {
                response.close();
            }
            httpclient.close();
        }

    }

}
```

DoPost

```java
package com.itheima.httpclient;

import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

public class DoPOST {

    public static void main(String[] args) throws Exception {

        // 创建Httpclient对象
        CloseableHttpClient httpclient = HttpClients.createDefault();

        // 创建http POST请求
        HttpPost httpPost = new HttpPost("http://www.oschina.net/");

        httpPost.setHeader("User-Agent", "");
        CloseableHttpResponse response = null;
        try {
            // 执行请求
            response = httpclient.execute(httpPost);
            // 判断返回状态是否为200
            if (response.getStatusLine().getStatusCode() == 200) {
                String content = EntityUtils.toString(response.getEntity(), "UTF-8");
                System.out.println(content);
            }
        } finally {
            if (response != null) {
                response.close();
            }
            httpclient.close();
        }

    }

}
```

DoPostParam

```java
package com.itheima.httpclient;

import java.util.ArrayList;
import java.util.List;

import org.apache.http.NameValuePair;
import org.apache.http.client.entity.UrlEncodedFormEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.message.BasicNameValuePair;
import org.apache.http.util.EntityUtils;

public class DoPOSTParam {

    public static void main(String[] args) throws Exception {

        // 创建Httpclient对象
        CloseableHttpClient httpclient = HttpClients.createDefault();

        // 创建http POST请求
        
        //  添加商品的urL   :/item/save
        HttpPost httpPost = new HttpPost("http://localhost:8081/item/save");

        //cid=560&title=httpclient&sellPoint=asfdafaf
        
        // 设置2个post参数，一个是scope、一个是q
        List<NameValuePair> parameters = new ArrayList<NameValuePair>(0);
        parameters.add(new BasicNameValuePair("cid", "560"));
        parameters.add(new BasicNameValuePair("title", "httpclient"));
        parameters.add(new BasicNameValuePair("sellPoint", "asfdafaf"));
        parameters.add(new BasicNameValuePair("price", "1200"));
        parameters.add(new BasicNameValuePair("num", "213"));
        // 构造一个form表单式的实体
        UrlEncodedFormEntity formEntity = new UrlEncodedFormEntity(parameters);
        // 将请求实体设置到httpPost对象中
        httpPost.setEntity(formEntity);

        CloseableHttpResponse response = null;
        try {
            // 执行请求
            response = httpclient.execute(httpPost);
            // 判断返回状态是否为200
            if (response.getStatusLine().getStatusCode() == 200) {
                String content = EntityUtils.toString(response.getEntity(), "UTF-8");
                System.out.println(content);
            }
        } finally {
            if (response != null) {
                response.close();
            }
            httpclient.close();
        }

    }

}
```

HttpRequest

```java
package com.itheima.httpclient;

public class HttpResult {
    private Integer code;//响应状态码
    private String body;//响应的内容

    public HttpResult(Integer code, String body) {
        super();
        this.code = code;
        this.body = body;
    }

    public HttpResult() {
        super();
    }

    public Integer getCode() {
        return code;
    }

    public void setCode(Integer code) {
        this.code = code;
    }

    public String getBody() {
        return body;
    }

    public void setBody(String body) {
        this.body = body;
    }   
}
```

TestAPIService

```java
package com.itheima.httpclient;

import java.util.HashMap;
import java.util.Map;

import org.apache.http.message.BasicNameValuePair;
import org.junit.Test;

public class TestAPIServcie {
    @Test
    public void testdogetParam() throws Exception{
            APIService service = new APIService();
            Map<String , Object> map =new  HashMap<String, Object>();
            map.put("page", "1");
            map.put("rows", "20");
            
            HttpResult doGet = service.doGet("http://localhost:8081/item/list", map);
            System.out.println(doGet.getCode());
            System.out.println(doGet.getBody());
    }
    
    @Test
    public void testdoPostParam() throws Exception{
        APIService service = new APIService();
        Map<String , Object> map =new  HashMap<String, Object>();
        
        /**
         *  parameters.add(new BasicNameValuePair("cid", "560"));
        parameters.add(new BasicNameValuePair("title", "httpclient"));
        parameters.add(new BasicNameValuePair("sellPoint", "asfdafaf"));
        parameters.add(new BasicNameValuePair("price", "1200"));
        parameters.add(new BasicNameValuePair("num", "213"));
         */
        
        map.put("cid", "560");
        map.put("title", "apiservicehttpclienttest");
        map.put("sellPoint", "asfdafaf");
        map.put("price", "1200");
        map.put("num", "213");
        
        HttpResult doGet = service.doGet("http://localhost:8081/item/save", map);
        System.out.println(doGet.getCode());
        System.out.println(doGet.getBody());
    }
}
```



```java
// 创建HttpClient对象（打开浏览器）
CloseableHttpClient httpclient = HttpClients.createDefault();
// 创建http GET请求（在浏览器中输入url）
HttpGet httpGet = new HttpGet("http://www.mytao.com/");

CloseableHttpResponse response = null;
try{
	// 执行请求（按回车，发出请求）
	response = httpclient.execute(httpGet);
	// 判断返回状态是否为200（获得响应）
	if(response.getStatusLine().getStatusCode() == 200){
		String content = EntityUtils.toString(response.getEntity(), "UTF-8");
		FileUtils.writeStringToFile(new File("/usr/local/mytao.html"), content);
	}
} finally {
	if(response != null){
		response.close();
	}
	// 关闭资源（关闭浏览器）
	httpclient.close();
}
```

```java
public class DoGET {

    public static void main(String[] args) throws Exception {

        // 创建Httpclient对象
        CloseableHttpClient httpclient = HttpClients.createDefault();

        // 创建http GET请求
        HttpGet httpGet = new HttpGet("http://localhost:8081/item/list?page=1&rows=10");

        CloseableHttpResponse response = null;
        try {
            // 执行请求
            response = httpclient.execute(httpGet);
            // 判断返回状态是否为200
            if (response.getStatusLine().getStatusCode() == 200) {
                String content = EntityUtils.toString(response.getEntity(), "UTF-8");
                System.out.println("内容长度：" + content.length());
                System.out.println(content);//响应的内容
            }
        } finally {
            if (response != null) {
                response.close();
            }
            httpclient.close();
        }
    }
}


public class DoGETParam {

	public static void main(String[] args) throws Exception {

		// 创建Httpclient对象
		CloseableHttpClient httpclient = HttpClients.createDefault();

		// 定义请求的参数
		URI uri = new URIBuilder("http://localhost:8081/item/list").setParameter("page", "1").setParameter("rows", "10")
				.build();

		System.out.println(uri);

		// 创建http GET请求
		HttpGet httpGet = new HttpGet(uri);

		CloseableHttpResponse response = null;
		try {
			// 执行请求
			response = httpclient.execute(httpGet);
			// 判断返回状态是否为200
			if (response.getStatusLine().getStatusCode() == 200) {
				String content = EntityUtils.toString(response.getEntity(), "UTF-8");
				System.out.println(content);
			}
		} finally {
			if (response != null) {
				response.close();
			}
			httpclient.close();
		}

	}

}
```




