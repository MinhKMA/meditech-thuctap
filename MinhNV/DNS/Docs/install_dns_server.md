# Hướng dẫn cài đặt DNS server trên CentOS7

## Mô hình 

- Primary (Master) DNS Server:
    + OS: CentOS7
    + IP: 192.168.100.46
    + Tắt firewalld và selinux 
- Client:
    + OS: CentOS7
    + IP: 192.168.100.113

## Cài đặt DNS server 

- Install bind9

    ``yum install bind bind-utils -y``

- Chỉnh sửa file conf

    ``vi /etc/named.conf``

- Sửa theo file cấu hình này:

```sh
//
// named.conf
//
// Provided by Red Hat bind package to configure the ISC BIND named(8) DNS
// server as a caching only nameserver (as a localhost DNS resolver only).
//
// See /usr/share/doc/bind*/sample/ for example named configuration files.
//

options {
    listen-on port 53 { 127.0.0.1; 192.168.100.46;}; ### Master DNS IP ###
#    listen-on-v6 port 53 { ::1; };
    directory     "/var/named";
    dump-file     "/var/named/data/cache_dump.db";
    statistics-file "/var/named/data/named_stats.txt";
    memstatistics-file "/var/named/data/named_mem_stats.txt";
    allow-query     { localhost; 192.168.100.0/24;}; ### IP Range ###


    /* 
     - If you are building an AUTHORITATIVE DNS server, do NOT enable recursion.
     - If you are building a RECURSIVE (caching) DNS server, you need to enable 
       recursion. 
     - If your recursive DNS server has a public IP address, you MUST enable access 
       control to limit queries to your legitimate users. Failing to do so will
       cause your server to become part of large scale DNS amplification 
       attacks. Implementing BCP38 within your network would greatly
       reduce such attack surface 
    */
    recursion yes;

    dnssec-enable yes;
    dnssec-validation yes;
    dnssec-lookaside auto;

    /* Path to ISC DLV key */
    bindkeys-file "/etc/named.iscdlv.key";

    managed-keys-directory "/var/named/dynamic";

    pid-file "/run/named/named.pid";
    session-keyfile "/run/named/session.key";
};

logging {
        channel default_debug {
                file "data/named.run";
                severity dynamic;
        };
};

zone "." IN {
    type hint;
    file "named.ca";
};

zone "minhkma.com" IN {
type master;
file "forward.minhkma";
allow-update { none; };
};
zone "100.168.192.in-addr.arpa" IN {
type master;
file "reverse.minhkma";
allow-update { none; };
};

include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";

```

- Tạo cở sở dữ liệu cho các zone 

``vi /var/named/forward.minhkma``

Thêm các dòng sau:

```sh 
$TTL 86400
@   IN  SOA     masterdns.minhkma.com. root.minhkma.com. (
        2011071001  ;Serial
        3600        ;Refresh
        1800        ;Retry
        604800      ;Expire
        86400       ;Minimum TTL
)
@       IN  NS          masterdns.minhkma.com.
@       IN  A           192.168.100.46
@       IN  A           192.168.100.113
masterdns       IN  A   192.168.100.46
client          IN  A   192.168.100.113
```

``vi /var/named/reverse.minhkma``

Thêm các dòng sau:

```sh
$TTL 86400
@   IN  SOA     masterdns.minhkma.com. root.minhkma.com. (
        2011071001  ;Serial
        3600        ;Refresh
        1800        ;Retry
        604800      ;Expire
        86400       ;Minimum TTL
)
@       IN  NS          masterdns.minhkma.com.
@       IN  PTR         minhkma.com.
masterdns       IN  A   192.168.100.46
client          IN  A   192.168.100.113
46     IN  PTR         masterdns.minhkma.com.
113     IN  PTR         client.minhkma.com.
```

- Khởi động service DNS

```sh
systemctl enable named
systemctl start named
```

- Tắt firewalld và selinux 

```sh
sed -i 's/\(^SELINUX=\).*/\SELINUX=disabled/' /etc/sysconfig/selinux
sed -i 's/\(^SELINUX=\).*/\SELINUX=disabled/' /etc/selinux/config
setenforce 0

systemctl disable firewalld
systemctl stop firewalld
```

- Trỏ DNS của client về DNS server

``vi /etc/sysconfig/network-scripts/ifcfg-eth0``

sửa dòng `DNS1=192.168.100.46`

OKE =))))

