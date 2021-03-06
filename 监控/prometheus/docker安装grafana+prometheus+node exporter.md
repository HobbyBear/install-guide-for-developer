# docker安装grafana+prometheus+node exporter

### 拉取各自镜像

```shell
    docker pull prom/node-exporter
    docker pull prom/prometheus
    ##查看镜像
    docker search grafana
    ## 根据镜像名称拉取镜像
    docker pull grafana/grafana
    docker pull prom/mysqld-exporter
    
```

### 启动docker-compose 
```shell
docker-compose up
```

### 导入几个面板

8919


