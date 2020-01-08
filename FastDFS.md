# FastDFS

## 部署安装

[官方文档](https://github.com/happyfish100/fastdfs/wiki)

补充说明：测试时，记得先开放对应端口，启动tracker、storage、nginx等服务

## 常用命令

```sh
# tracker
/etc/init.d/fdfs_trackerd start #启动tracker服务
/etc/init.d/fdfs_trackerd restart #重启动tracker服务
/etc/init.d/fdfs_trackerd stop #停止tracker服务
chkconfig fdfs_trackerd on #自启动tracker服务

# storage
/etc/init.d/fdfs_storaged start #启动storage服务
/etc/init.d/fdfs_storaged restart #重动storage服务
/etc/init.d/fdfs_storaged stop #停止动storage服务
chkconfig fdfs_storaged on #自启动storage服务

# 防火墙
systemctl stop firewalld.service #关闭
systemctl restart firewalld.service #重启
```

## 入门程序

[FastDFS的使用](https://blog.csdn.net/kisscatforever/article/details/73229002)

Java程序

```java


public class FastdfsTest {
    @Test
    public void testUpload() throws Exception {
        // 加载配置文件
        ClientGlobal.init("配置文件路径");
        // 构建一个管理者客户端
        TrackerClient client = new TrackerClient();
        // 链接管理者服务端
        TrackerServer trackerServer = client.getConnection();
        // 声明存储服务端
        StorageServer storageServer = null;
        //  获取存储服务器的客户端对象
        StorageClient storageClient = new StorageClient(trackerServer, storageServer);
        // 上传文件，获得file_id
        String[] strings = storageClient.upload_file("文件名", "文件扩展名", "文件扩展信息");
    }
}
```

依赖

```xml
<dependency>
  <groupId>org.csource</groupId>
  <artifactId>fastdfs-client-java</artifactId>
  <version>...</version>
</dependency>
```

配置文件: fdfs.conf

```
# 连接tracker服务器超时时长
connect_timeout = 10
# socket连接超时时长
network_timeout = 30
# 文件内容编码
charset = UTF-8
# tracker服务器端口
http.tracker_http_port = 80
http.anti_steal_token = no
#密码
http.secret_key = 123456
# tracker服务器IP和端口（可以写多个）
tracker_server = 192.168.33.111:22122
```

[FastDFS原理](https://www.jianshu.com/p/3f24bb9bb742)

