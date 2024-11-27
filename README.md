# ISS-UCR-Micro
How to fix the damn thing


So... You ended up having to reset one of these things...

Ok, after the factory reset you need the root password, the only way i got one was to go to http://192.168.2.2:44380 and complete the wizard. The root password is pretty much the only thing that will work.

You have SSH access now? Great, uninstall mariadb and remove /var/lib/mysql. Yes. Just do it.

You need internet access now, /etc/sysconfig/network, have it looking something like this:

  NETWORKING=yes
  GATEWAY=<gw>
  GATEWAYDEV=eth0.200 (for lan port -or eth0.100 for wan port-) 

/etc/sysconfig/network-scripts/ifcfg-eth0.200
  
  DEVICE=eth0.200
  BOOTPROTO=static
  ONBOOT=yes
  IPADDR=<lan ip>
  NETMASK=255.255.255.0
  USERCTL=no
  NETWORK=<lan network>
  VLAN=yes
  NOZEROCONF=yes
  BROADCAST=<broadcast>
  DNS1=1.1.1.1


ok, you have internet, good. Now you need to correct your repo sources...

/etc/yum.repos.d/CentOS-Base.repo
  
  [base]
  name=CentOS-$releasever - Base
  baseurl=http://ftp.iij.ad.jp/pub/linux/centos-vault/altarch/7/os/aarch64/
  gpgcheck=1
  gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
  exclude=redhat-logos php53* kernel*
  
  #released updates
  [updates]
  name=CentOS-$releasever - Updates
  baseurl=http://ftp.iij.ad.jp/pub/linux/centos-vault/altarch/7/updates/aarch64/
  gpgcheck=1
  gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
  exclude=redhat-logos php53* kernel*
  
  #additional packages that may be useful
  [extras]
  name=CentOS-$releasever - Extras
  baseurl=http://ftp.iij.ad.jp/pub/linux/centos-vault/altarch/7/extras/aarch64/
  gpgcheck=1
  gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
  exclude=redhat-logos php53* kernel*
  
  #additional packages that extend functionality of existing packages
  [centosplus]
  name=CentOS-$releasever - Plus
  baseurl=http://ftp.iij.ad.jp/pub/linux/centos-vault/altarch/7/centosplus/aarch64/
  gpgcheck=1
  enabled=1
  gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
  exclude=redhat-logos php53* kernel*

/etc/yum.repos.d/Issabel.repo

  [issabel-base]
  name=Base RPM Repository for Issabel
  mirrorlist=http://mirror.issabel.org/?release=4&arch=$basearch&repo=base
  #baseurl=http://repo.issabel.org/issabel/4/base/$basearch/
  gpgcheck=1
  enabled=1
  gpgkey=http://repo.issabel.org/issabel/RPM-GPG-KEY-Issabel
  
  [issabel-updates]
  name=Updates RPM Repository for Issabel
  mirrorlist=http://mirror.issabel.org/?release=4&arch=$basearch&repo=updates
  #baseurl=http://repo.issabel.org/issabel/4/updates/$basearch/
  gpgcheck=1
  enabled=1
  gpgkey=http://repo.issabel.org/issabel/RPM-GPG-KEY-Issabel
  
  [issabel-beta]
  name=Beta RPM Repository for Issabel
  mirrorlist=http://mirror.issabel.org/?release=4&arch=$basearch&repo=beta
  #baseurl=http://repo.issabel.org/issabel/4/beta/$basearch/
  #gpgcheck=1
  enabled=0
  gpgkey=http://repo.issabel.org/issabel/RPM-GPG-KEY-Issabel
  
  [issabel-extras]
  name=Extras RPM Repository for Issabel
  mirrorlist=http://mirror.issabel.org/?release=4&arch=$basearch&repo=extras
  #baseurl=http://repo.issabel.org/issabel/4/extras/$basearch/
  gpgcheck=1
  enabled=1
  gpgkey=http://repo.issabel.org/issabel/RPM-GPG-KEY-Issabel


with this done:

  yum clean all ; yum makecache
  yum update
  yum upgrade
  
  curl http://repo.issabel.org/issabel4-netinstall.sh | bash

  say YES to beta repo.

  pray
  
