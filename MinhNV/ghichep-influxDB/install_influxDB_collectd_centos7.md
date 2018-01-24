## Hướng dẫn cài đặt influxDB kết hợp với collectd 


### Bước 1: Cài đặt influxDB
- Stop firewalld va selinux 

```sh
systemctl stop firewalld
systemctl disbale firewalld
sed -i 's/\(^SELINUX=\).*/\SELINUX=disabled/' /etc/sysconfig/selinux
sed -i 's/\(^SELINUX=\).*/\SELINUX=disabled/' /etc/selinux/config
setenforce 0
```

- Khai báo repo 

```sh
cat << EOF > /etc/yum.repos.d/influxdata.repo
[influxdb]
name = InfluxData Repository - RHEL $releasever
baseurl = https://repos.influxdata.com/rhel/$releasever/$basearch/stable
enabled = 1
gpgcheck = 1
gpgkey = https://repos.influxdata.com/influxdb.key
EOF
```
- Cài đặt 

``yum install influxdb -y``

- Khởi động influxDB 

``systemctl start influxdb``

### Bước 2: Truy cập vào CLI của influxDB và tạo DB tên là collectd

```sh
influx
CREATE DATABASE collectd
CREATE USER "minhkma" WITH PASSWORD 'minhkma' WITH ALL PRIVILEGES
exit 
```
- Sao lưu file /etc/influxdb/influxdb.conf

``cp /etc/influxdb/influxdb.conf /etc/influxdb/influxdb.conf.orig``

- Sửa file "/etc/influxdb/influxdb.conf", tìm tới section [[collectd]] và sửa như đoạn dưới.

``vi /etc/influxdb/influxdb.conf``

```sh
[[collectd]]
enabled = true
bind-address = ":25826" # the bind address
database = "collectd" # Name of the database that will be written to
retention-policy = ""
batch-size = 5000 # will flush if this many points get buffered
batch-pending = 10 # number of batches that may be pending in memory
batch-timeout = "10s"
read-buffer = 0 # UDP read buffer size, 0 means to use OS default
typesdb = "/usr/share/collectd/types.db"
security-level = "none" # "none", "sign", or "encrypt"

```

- Sau đó chỉnh sửa trong file cấu hình influxDB

Cấu hình để yêu cầu đăng nhập cho influxdb bằng cách sửa file  `/etc/influxdb/influxdb.conf`

``sed -i 's/# auth-enabled = false/auth-enabled = true/g'  /etc/influxdb/influxdb.conf``

- Khởi động lại influxDB 

``systemctl restart influxdb``

### Bước 3: Cài đặt collectd lên influx-server 

```sh
yum -y install epel-release
yum update
yum -y install collectd
```
- Cấu hình file conf của collectd

```sh
cp /etc/collectd.conf /etc/collectd.conf.orig
wget -O /etc/collectd.conf https://raw.githubusercontent.com/MinhKMA/meditech-thuctap/master/MinhNV/ghichep_graphite/file%20conf%20example/collectd_influxDB.conf
```
- Khởi động lại collectd 

``systemctl restart collectd``

## Hướng dẫn import một template trong grafana 

- Bước 1: Truy cập vào trang chủ của grafana và click vào phần dashbroad 

<img src ="https://i.imgur.com/Ca7dQx6.png">

- Bước 2: Filter dashboard theo yêu cầu của mình 

Ví dụ tôi muốn tìm một template dashboard cho infuluxdb và colectd :

<img src = "https://i.imgur.com/dORf6zq.png">

Ở phần bên phải sẽ hiển thị ra các dashboard theo yêu cầu của mình đã filter ở bên trái 

- Bước 3: Copy ID hoặc download file json

Ở đây mình copy id 

<img src = "https://i.imgur.com/xgKsCQX.png">

- Bước 4: Vào dashboard grafana của bạn chọn `dashboard` => `import`

<img src = "https://i.imgur.com/a63yzmM.png">

paste id bạn vừa copy và load 

<img src = "https://i.imgur.com/NshlSQy.png">

sau khi load bạn chọn tên và datasource cho dashboard

<img src = "https://i.imgur.com/uDKxrUo.png">

- Bước 5: Tùy chỉnh lại một số biến để hoàn thiện dashboard của mình 

<img src = "https://i.imgur.com/XoALRAq.png">

<img src = "https://i.imgur.com/NRJsyea.png">

## Một số câu lệnh query trong influxDB

- Đăng nhập vào influxDB 

    `influx -username 'username' -password 'pass'`

- Tạo một DATABASE 

    `CREATE DATABASE < tenDB > `

- Hiển thị database

    `SHOW DATABASES`

- Sử dụng database

    `USE <nameDB>`

- SHOW SERIES

    `SHOW SERIES`

- Hiển thị các giá trị trong một series 


    `SELECT * FROM <series_name>`

- Hiển thị các thông số trong 1 phút gần đây 

    `SELECT * FROM <series_name> WHERE time > (now() - 1m)`

- DROP một series 

    `DROP series PROM <series_name>`

- DROP giá trị của 1 phút gần nhất trong series

    `DELETE FROM "series_name" WHERE time > (now() - 1m)`


    
