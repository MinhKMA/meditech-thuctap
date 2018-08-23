```sh
yum -y install epel-release deltarpm chronyd wget 
yum makecache
yum -y update
yum install libvirt-python libvirt libvirt-daemon-config-network qemu-kvm python-ethtool sos \
          python-ipaddr nfs-utils iscsi-initiator-utils pyparted python-libguestfs libguestfs-tools novnc \
          spice-html5 python-configobj python-magic python-paramiko python-pillow virt-top

systemctl enable chronyd
systemctl restart chronyd
# firefox https://github.com/kimchi-project/kimchi/releases/latest

yum -y install http://kimchi-project.github.io/gingerbase/downloads/latest/ginger-base.el7.centos.noarch.rpm \
          http://kimchi-project.github.io/ginger/downloads/latest/ginger.el7.centos.noarch.rpm \
          https://github.com/kimchi-project/wok/releases/download/2.5.0/wok-2.5.0-0.el7.centos.noarch.rpm \
          https://github.com/kimchi-project/kimchi/releases/download/2.5.0/kimchi-2.5.0-0.el7.centos.noarch.rpm

firewall-cmd --add-port 8001/tcp --permanent

systemctl enable wokd nginx
systemctl restart wokd nginx firewalld
```
