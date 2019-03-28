cat <<EOT  >> /etc/sysconfig/network-scripts/ifcfg-eth0
DEVICE=eth0               ## Nom de l'interface
BOOTPROTO=static          ## Passer en mode static et non DHCP
BROADCAST=192.168.168.255   ## Adresse de broadcast
HWADDR=AA:???????????????  ## MAC ADRESSE de la carte reseau
IPADDR=192.168.168.169       ## Adresse IP de la machine
NETMASK=255.255.255.0     ## Masque sous-reseau
NETWORK=192.168.168.0       ## Adresse reseau
ONBOOT=yes                ## Monter l'interface au boot
NM_CONTROLLED="no"        ## Pas de contrôl via NetworkManager
EOT
  
cat <<EOT > /etc/sysconfig/network
NETORKING=yes
NETORKING_IPV6=no
HOSTNAME=zimbra2.test.fr
GATEWAY=192.168.168.1
EOT
  
echo "nameserver 8.8.8.8" > /etc/resolv.conf
touch /etc/sysconfig/network-scripts/route-eth0
#echo "0.0.0.0/0 via 192.168.168.1 dev eth0"

##########################################################################
cat <<EOT > /etc/yum.repos.d/CentOS-Base.repo
# CentOS-Base.repo
#
# The mirror system uses the connecting IP address of the client and the
# update status of each mirror to pick mirrors that are updated to and
# geographically close to the client.  You should use this for CentOS updates
# unless you are manually picking other mirrors.
#
# If the mirrorlist= does not work for you, as a fall back you can try the
# remarked out baseurl= line instead.
#
#

[base]
name=CentOS-$releasever - Base
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os
baseurl=http://vault.centos.org/5.11/os/x86_64/
#baseurl=http://mirror.centos.org/centos/$releasever/os/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5

#released updates
[updates]
name=CentOS-$releasever - Updates
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates
baseurl=http://vault.centos.org/5.11/updates/x86_64/
#baseurl=http://mirror.centos.org/centos/$releasever/updates/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5

#additional packages that may be useful
[extras]
name=CentOS-$releasever - Extras
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras
baseurl=http://vault.centos.org/5.11/extras/x86_64/
#baseurl=http://mirror.centos.org/centos/$releasever/extras/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5
EOT

#########################################################

reboot

yum -y install unzip net-tools sysstat openssh-clients perl-core libaio nmap-ncat libstdc++.so.6
setenforce 0
sed -i 's/SELINUXTYPE=targeted/SELINUXTYPE=disabled/g' /etc/selinux/config 
yum install wget
echo "192.168.168.169 zimbra2.test.fr zimbra2" >> /etc/hosts
mkdir /zimbra
cd /zimbra
wget https://files2.zimbra.com/downloads/7.2.6_GA/zcs-7.2.6_GA_2926.RHEL5_64.20131203115905.tgz --no-check-certificate
tar xzf zcs-7.2.6_GA_2926.RHEL5_64.20131203115905.tgz
cd zcs-7.2.6_GA_2926.RHEL5_64.20131203115905
sleep 5
 /etc/init.d/named
 chkconfig named on

yum -y install bind bind-utils caching-nameserver 
cd /etc/
wget https://github.com/madrugre/zimbra-install/blob/master/etc/named.conf
/etc/named.caching-nameserver.conf /etc/named.conf


Voir https://wiki.zimbra.com/wiki/Split_DNS


./install --platform-override





