# spring-cloud-store

## store-config配置

### Windows安装nacos

**1.下载nacos**

```
https://github.com/alibaba/nacos/releases/tag/1.1.0
```

**2.修改application.properties文件**

```
#*************** Config Module Related Configurations ***************#
### If use MySQL as datasource:
spring.datasource.platform=mysql
### Count of DB:
db.num=1
### Connect URL of DB:
db.url.0=jdbc:mysql://127.0.0.1:3306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone=UTC
db.user=root
db.password=123456
```

**3.单节点配置cluster.conf**

```
#2021-05-22T10:03:42.045
127.0.0.1:8847
192.168.36.1:8848
```

**4.双击startup.cmd启动**

### 新建module

**1.创建store-config**

**2.新建bootstrap.yml**

```
spring:
  application:
    name: spring-cloud-store
  cloud:
    nacos:
      config:
        server-addr: 127.0.0.1:8848
        file-extension: yaml
        prefix: spring-cloud-store
        group: spring-cloud-store
  profiles:
    active: dev
```

**3.启动StoreConfigApplication**



