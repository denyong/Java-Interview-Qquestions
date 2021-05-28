# Docker部署ELK

## 环境准备

```
centos7.4
elasticsearch:7.8.0
kibana:7.0.1
logstash:7.8.1
```

## elasticsearch部署

**获取镜像**

```
docker pull elasticsearch:7.8.0
```

**创建数据目录**

```
mkdir -p /home/software/data/elasticsearch/{config,data,plugins}
```

**创建配置脚本**

```
echo "http.host: 0.0.0.0" > /home/software/data/elasticsearch/config/elasticsearch.yml
```

**分配文件夹权限，不给权限启动会报错**

```
chmod -R 775 /home/software/data/elasticsearch/
```

**启动elasticsearch**

```
docker run -d\
--restart=always \
--name elasticsearch \
-p 9200:9200 \
-p 9300:9300 \
-e "discovery.type=single-node" \
-e ES_JAVA_OPTS="-Xms64m -Xmx128m" \
-v /home/software/data/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml \
-v /home/software/data/elasticsearch/data:/usr/share/elasticsearch/data \
-v /home/software/data/elasticsearch/plugins:/usr/share/elasticsearch/plugins \
elasticsearch:7.8.0
```



## kibana部署

**获取镜像**

```
docker pull kibana:7.0.1
```

**在kibana.yml配置elasticsearch访问地址**

```
server.name: kibana
server.host: "0.0.0.0"
elasticsearch.hosts: [ "http://172.16.11.248:9200" ] 
xpack.monitoring.ui.container.elasticsearch.enabled: true
```

**启动kibana**

```
docker run -d \
--restart=always \
--name kibana \
-p 5601:5601 \
-v /home/software/data/kibana/kibana.yml:/usr/share/kibana/config/kibana.yml \
kibana:7.0.1
```

## logstash部署

**下载tar包**

```
wget https://artifacts.elastic.co/downloads/logstash/logstash-7.8.0.tar.gz
```

**解压tar包**

```
tar -xvf [logstash-7.8.0.tar.gz
```

**启动logstash**

```
bin/logstash -e ""
```

**输入hello world**

```
hello world
```

**输出内容**

```
{
          "host" => "localhost.localdomain",
    "@timestamp" => 2021-05-07T08:23:31.291Z,
      "@version" => "1",
       "message" => "hello world",
          "type" => "stdin"
}
```

**logstash配置读取日志输出到elasticsearch**

