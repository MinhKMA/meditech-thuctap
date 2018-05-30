
- Viết một dockerfile để cài đặt influxdb 

    `vim dockerfile`

- Có nội dung như sau:

```sh
FROM centos
MAINTAINER minhkma
RUN echo $'[influxdb] \n\
name = InfluxData Repository - RHEL \$releasever \n\
baseurl = https://repos.influxdata.com/rhel/\$releasever/\$basearch/stable \n\
enabled = 1 \n\
gpgcheck = 1 \n\
gpgkey = https://repos.influxdata.com/influxdb.key' > /etc/yum.repos.d/influxdata.repo

RUN yum install influxdb -y
EXPOSE 8086
CMD ["/usr/bin/influxd", "-config", "/etc/influxdb/influxdb.conf"]1
```

- Sau đó build mội image từ dockerfile đã tạo 

    `docker build -t influxdb_minhkma .`

- Kiểm tra images vừa tạo 

```sh
[root@HA1-minhnv ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
influxdb            latest              263f3c551252        32 minutes ago      342MB
centos              latest              e934aafc2206        7 weeks ago         199MB
```

- Xem thông tin về image vừa tạo 

```sh
[root@HA1-minhnv ~]# docker image inspect influxdb
```

- Run container với image vừa tạo:

```sh 
docker run -it -p 8086:8086 --name influxdb influxdb
```

- Kiểm tra container vừa tạo 

```sh
[root@HA1-minhnv ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
e660f111e9b2        influxdb            "/usr/bin/influxd -c…"   16 minutes ago      Up 16 minutes       0.0.0.0:8086->8086/tcp   influxdb
```

- Truy cập vào trong container 

```sh
[root@HA1-minhnv ~]# docker exec -it influxdb /bin/bash
[root@e660f111e9b2 /]# influx
Connected to http://localhost:8086 version 1.5.2
InfluxDB shell version: 1.5.2
> 

```

- Đứng từ bên ngoài thực hiện lệnh `crul` và `ss` để kiểm tra port :

```sh
[root@HA1-minhnv ~]# curl -I http://192.168.30.65:8086/ping
HTTP/1.1 204 No Content
Content-Type: application/json
Request-Id: 7273c442-63b7-11e8-8004-000000000000
X-Influxdb-Build: OSS
X-Influxdb-Version: 1.5.2
X-Request-Id: 7273c442-63b7-11e8-8004-000000000000
Date: Wed, 30 May 2018 03:13:38 GMT
```

```sh
[root@HA1-minhnv ~]# ss -lan | grep 8086
tcp    LISTEN     0      128      :::8086                 :::*   
```
- Xem log của contaiber: 

```sh 
docker logs e660f111e9b2
[httpd] 127.0.0.1 - - [30/May/2018:03:12:24 +0000] "GET /ping HTTP/1.1" 204 0 "-" "InfluxDBShell/1.5.2" 462105fc-63b7-11e8-8001-000000000000 214
[httpd] 192.168.30.65 - - [30/May/2018:03:13:26 +0000] "GET /ping HTTP/1.1" 204 0 "-" "curl/7.29.0" 6b0492a6-63b7-11e8-8002-000000000000 27
[httpd] 192.168.30.65 - - [30/May/2018:03:13:33 +0000] "GET /ping HTTP/1.1" 204 0 "-" "curl/7.29.0" 6f3605da-63b7-11e8-8003-000000000000 33
[httpd] 192.168.30.65 - - [30/May/2018:03:13:38 +0000] "HEAD /ping HTTP/1.1" 204 0 "-" "curl/7.29.0" 7273c442-63b7-11e8-8004-000000000000 43
```



