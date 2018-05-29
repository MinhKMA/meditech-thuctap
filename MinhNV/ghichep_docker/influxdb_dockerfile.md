
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
influx_minhkma      latest              190bbd4645cc        5 hours ago         342MB
centos              latest              e934aafc2206        7 weeks ago         199MB
```

- Xem thông tin về image vừa tạo 

```sh
[root@HA1-minhnv ~]# docker image inspect 190bbd4645cc
[
    {
        "Id": "sha256:190bbd4645cc8cdc91b25abddbc1732f4124c2774e3d70b3b138d4c331904553",
        "RepoTags": [
            "influx_minhkma:latest"
        ],
        "RepoDigests": [],
        "Parent": "sha256:061ab8cb77be5a96486e647ebd68c369170c255550391273b1f65bf80b5434bf",
        "Comment": "",
        "Created": "2018-05-29T06:22:22.767698497Z",
        "Container": "9c4e9cd4ff26ba8092d571594b06a316a8d268e73add9a733eed7cb2fbcbb117",
        "ContainerConfig": {
            "Hostname": "9c4e9cd4ff26",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "8086/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"/usr/bin/influxd\" \"-config\" \"/etc/influxdb/influxdb.conf\"]"
            ],
            "ArgsEscaped": true,
            "Image": "sha256:061ab8cb77be5a96486e647ebd68c369170c255550391273b1f65bf80b5434bf",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.schema-version": "= 1.0     org.label-schema.name=CentOS Base Image     org.label-schema.vendor=CentOS     org.label-schema.license=GPLv2     org.label-schema.build-date=20180402"
            }
        },
        "DockerVersion": "18.05.0-ce",
        "Author": "minhkma",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "8086/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/usr/bin/influxd",
                "-config",
                "/etc/influxdb/influxdb.conf"
            ],
            "ArgsEscaped": true,
            "Image": "sha256:061ab8cb77be5a96486e647ebd68c369170c255550391273b1f65bf80b5434bf",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.schema-version": "= 1.0     org.label-schema.name=CentOS Base Image     org.label-schema.vendor=CentOS     org.label-schema.license=GPLv2     org.label-schema.build-date=20180402"
            }
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 341797426,
        "VirtualSize": 341797426,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/0a2a49b29f188d4a33785334edc9afcb01649752ee3f50086cb485cb3e6cd23c/diff:/var/lib/docker/overlay2/b90f6a29f18e3339939fd51f2716eab6e22710ed26512320ae156f7967ecd445/diff",
                "MergedDir": "/var/lib/docker/overlay2/b0d93c835a4592c16cab2626cdb9c1f316c81d4fbb7398c96d2c64f10eee0c7b/merged",
                "UpperDir": "/var/lib/docker/overlay2/b0d93c835a4592c16cab2626cdb9c1f316c81d4fbb7398c96d2c64f10eee0c7b/diff",
                "WorkDir": "/var/lib/docker/overlay2/b0d93c835a4592c16cab2626cdb9c1f316c81d4fbb7398c96d2c64f10eee0c7b/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:43e653f84b79ba52711b0f726ff5a7fd1162ae9df4be76ca1de8370b8bbf9bb0",
                "sha256:b94d3af974156e7fe25e173eac5634a5f910abc19bf33b78178bd1263a2394c3",
                "sha256:5570bac4b6ce650c444df6572e2d3808ad1485c6dab9439bad518d632a6d9734"
            ]
        },
        "Metadata": {
            "LastTagTime": "2018-05-29T02:22:22.960372375-04:00"
        }
    }
]
```

- Run container với image vừa tạo:

```sh 
docker run -it --name influxdb 190bbd4645cc
```

- Kiểm tra container vừa tạo 

```sh
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
cbdc7ed4a205        190bbd4645cc        "/usr/bin/influxd -c…"   4 hours ago         Up 4 hours          8086/tcp            influxdb
```

- Truy cập vào trong container 

```sh
[root@HA1-minhnv ~]# docker exec -it cbdc7ed4a205 /bin/bash
[root@cbdc7ed4a205 /]# influx
Connected to http://localhost:8086 version 1.5.2
InfluxDB shell version: 1.5.2
> 
```
