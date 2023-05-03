```shell
#!/bin/bash

if [ $# -lt 6 ];then

         echo "Usage: $0 <bond*> <eth*> <eth*> <ipaddress> <netmask> <gateway>"

         echo "eg:    $0 bond0 eth0 eth1 10.0.0.1 255.255.255.0 10.0.0.254"

         exit 1

fi

 

#modify ifcfg-bond* file

echo "DEVICE=$1

IPADDR=$4

NETMASK=$5

GATEWAY=$6

ONBOOT=yes

BOOTPROTO=none

USERCTL=no" >/tmp/ifcfg-$1

mv -f /tmp/ifcfg-$1 /etc/sysconfig/network-scripts/

 

#modify ifcfg-eth0 file

echo "DEVICE=$2

USERCTL=no

ONBOOT=yes

MASTER=$1

SLAVE=yes

BOOTPROTO=none" >/tmp/ifcfg-$2

mv -f /tmp/ifcfg-$2 /etc/sysconfig/network-scripts/

 

#modify ifcfg-eth1 file

echo "DEVICE=$3

USERCTL=no

ONBOOT=yes

MASTER=$1

SLAVE=yes

BOOTPROTO=none" >/tmp/ifcfg-$3

mv -f /tmp/ifcfg-$3 /etc/sysconfig/network-scripts/

 

##modify network file

#sed /GATEWAY/d /etc/sysconfig/network >/tmp/network

#echo "GATEWAY=\"$6\"">>/tmp/network

#mv -f /tmp/network /etc/sysconfig/

 

#modify modules.cof/modprobe.cof

MODCONF=/NULL

TEMPFILE1=/tmp/mod1.$$

TEMPFILE2=/tmp/mod2.$$

BAKFILE=/etc/.modconf

 

echo "Please Select Your Bond Mode:(balance-rr/active-backup)or(0/1)?"

read MODE

 

if [ -f /etc/modprobe.conf ]; then

         MODCONF=/etc/modprobe.conf

else

         MODCONF=/etc/modules.conf

fi

 

cp $MODCONF $BAKFILE

 

sed '/alias[ \t]*'$1'[ \t]*bonding/d;/options[ \t]*'$1'[ \t]*/d;/install.*'$1'/d' $MODCONF > $TEMPFILE1

cp $TEMPFILE1 $TEMPFILE2

 

if [ "$(grep "alias.*bonding" $TEMPFILE1)" != "" ]; then

         bondcount=$(grep "alias.*bonding" $TEMPFILE1 | wc -l)

elif [ "$(grep "install.*bond.*" $TEMPFILE1)" != "" ]; then

         bondcount=$(grep "install.*bond.*" $TEMPFILE1 | wc -l)

else

         bondcount=0

fi

 

if [ "$bondcount" -ge 1 ]; then

         sed '/alias.*bonding/d;s/\(options[ \t]*\)\(bond[0-9]*\)/install\ \2\ \/sbin\/modprobe\ --ignore-install\ bonding\ -o\ \2/' $TEMPFILE1 > $TEMPFILE2

 

         echo "install $1 /sbin/modprobe --ignore-install bonding -o $1 miimon=100 mode=$MODE" >> $TEMPFILE2

else

 

         echo "alias $1 bonding" >> $TEMPFILE2

         echo "options $1 miimon=100 mode=$MODE" >> $TEMPFILE2

fi      

         mv -f $TEMPFILE2 $MODCONF

 

#restart network

echo "System will restart network continue(y/n)?"

read bb

if [ "$bb" = "y" ] || [ "$bb" = "yes" ] || [ "$bb" = "Y" ];then

         for tempmod in $(lsmod | grep -i bond | awk '{print $1}')

         do

         modprobe -r bonding -o "$tempmod"

         done

 

         /etc/init.d/network restart

fi

echo "OK!"

exit 0
```

