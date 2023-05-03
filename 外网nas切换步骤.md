```elm
一、nas卷拷贝数据

1. 创建目录
   mkdir -p -m 775 /nfs45/DZSQ
   mkdir -p -m 775 /nfs45/cases
   mkdir -p -m 775 /nfs45/dzsqyj
   mkdir -p -m 775 /nfs45/dzsqyj/RAGE2
   mkdir -p -m 775 /nfs45/dzsqyj/RAGE2/dzsqyj
   mkdir -p -m 775 /nfs45/dzsqyj/RAGE2/dzsqyj/DZSQYJ
   mkdir -p -m 775 /nfs45/dzsqyj/RAGE2/dzsqyj/DZSQYJ/APPWW
   mkdir -p -m 775 /nfs45/dzsqyj/DZSQYJ
   mkdir -p -m 775 /nfs45/dzsqyj/DZSQYJ/HZ
   mkdir -p -m 775 /nfs45/dzsqyj/DZSQYJ/APPWW
   mkdir -p -m 775 /nfs45/jwcx
   mkdir -p -m 775 /nfs45/JHS
   mkdir -p -m 775 /nfs45/DZSQ/CA
   mkdir -p -m 775 /nfs45/DZSQ/APPXSD
   mkdir -p -m 775 /nfs45/DZSQ/cfca
   mkdir -p -m 775 /nfs45/DZSQ/TOOLS
   mkdir -p -m 775 /nfs45/DZSQ/UNIONPAY
   mkdir -p -m 775 /nfs45/DZSQ/UPDATE
   mkdir -p -m 775 /nfs45/DZSQ/DZSQ_APPWW_UNZIP
   mkdir -p -m 775 /nfs45/DZSQ/DZSQ_APPWW_UNZIP/cases
   mkdir -p -m 775 /nfs45/DZSQ/DZSQ_APPWW_UNZIP/cases/inventions
   mkdir -p -m 775 /nfs45/DZSQ/DZSQ_APPWW_UNZIP/cases/utility_models
   mkdir -p -m 775 /nfs45/DZSQ/DZSQ_APPWW_UNZIP/cases/Invalidation
   mkdir -p -m 775 /nfs45/DZSQ/DZSQ_APPWW_UNZIP/cases/PCT_inventions
   mkdir -p -m 775 /nfs45/DZSQ/DZSQ_APPWW_UNZIP/cases/designs
   mkdir -p -m 775 /nfs45/DZSQ/APPWW
   mkdir -p -m 775 /nfs45/DZSQ/APPWW/test
   mkdir -p -m 775 /nfs45/DZSQ/APPWW/test3
   mkdir -p -m 775 /nfs45/DZSQ/APPTEMPFILE
   mkdir -p -m 775 /nfs45/DZSQ/FWBWW
   mkdir -p -m 775 /nfs45/DZSQ/HZ
   mkdir -p -m 775 /nfs45/DZSQ/HZTEMP
   mkdir -p -m 775 /nfs45/DZSQ/SQWJTIFF
   mkdir -p -m 775 /nfs45/DZSQ/ZJZDZSQ
   mkdir -p -m 775 /nfs45/DZSQ/ZXZF
   mkdir -p -m 775 /nfs45/DZSQ/ZXZF/GNMB
   mkdir -p -m 775 /nfs45/DZSQ/ZXZF/PCTSC
   mkdir -p -m 775 /nfs45/DZSQ/cases
   mkdir -p -m 775 /nfs45/DZSQ/ZXZF/GNMB/2022/
   mkdir -p -m 775 /nfs45/DZSQ/ZXZF/PCTSC/2022/
    mkdir -p -m 775 /nfs45/DZSQ/ZXZF/GNMB/2022/12/
    mkdir -p -m 775 /nfs45/DZSQ/ZXZF/PCTSC/2022/12/
    mkdir -p -m 775 /nfs45/DZSQ/APPWW/202212/
    mkdir -p -m 775 /nfs45/DZSQ/APPTEMPFILE/202212/
    mkdir -p -m 775 /nfs45/DZSQ/FWBWW/202212/
    mkdir -p -m 775 /nfs45/DZSQ/HZ/202212/
    mkdir -p -m 775 /nfs45/DZSQ/HZTEMP/202212/
    mkdir -p -m 775 /nfs45/DZSQ/SQWJTIFF/202212/
    mkdir -p -m 775 /nfs45/DZSQ/ZJZDZSQ/202212/
   mkdir -p -m 775 /nfs45/DZSQ/ZXZF/GNMB/2022/12/
   mkdir -p -m 775 /nfs45/DZSQ/ZXZF/PCTSC/2022/12/
   mkdir -p -m 775 /nfs45/DZSQ/DZSQ_APPWW_UNZIP/202212/

2. 修改目录属主属组
   chown -R weblogic:bea /nfs45/
   chmod 777 /nfs45/

3. 拷贝模板和数据
   模板文件拷贝:
   nohup cp -pr /nfs44/DZSQ/CA/ /nfs45/DZSQ/ &
   nohup cp -pr /nfs44/DZSQ/APPXSD/ /nfs45/DZSQ/ &
   nohup cp -pr /nfs44/DZSQ/cfca/ /nfs45/DZSQ/ &
   nohup cp -pr /nfs44/DZSQ/TOOLS/ /nfs45/DZSQ/ &
   nohup cp -pr /nfs44/DZSQ/UNIONPAY/ /nfs45/DZSQ/ &
   nohup cp -pr /nfs44/DZSQ/UPDATE/ /nfs45/DZSQ/ &
   nohup cp -pr /nfs44/DZSQ/700000000000001-SIGN.pfx /nfs45/DZSQ/ &
   nohup cp -pr /nfs44/cases/login.jsp /nfs45/cases/ &

   数据文件拷贝(当月)
   nohup cp -pr /nfs44/DZSQ/DZSQ_APPWW_UNZIP/202212/{26..31} /nfs45/DZSQ/DZSQ_APPWW_UNZIP/202212/ &	

   nohup cp -pr /nfs44/DZSQ/ZXZF/GNMB/2022/12/{26..31} /nfs45/DZSQ/ZXZF/GNMB/2022/12/ &
   nohup cp -pr /nfs44/DZSQ/ZXZF/PCTSC/2022/12/{26..31} /nfs45/DZSQ/ZXZF/PCTSC/2022/12/ &


   数据文件拷贝(当天)
   nohup cp -pr /nfs44/DZSQ/DZSQ_APPWW_UNZIP/202212/31/ /nfs45/DZSQ/DZSQ_APPWW_UNZIP/202212/ &	
   nohup cp -pr /nfs44/DZSQ/ZXZF/GNMB/2022/12/31/ /nfs45/DZSQ/ZXZF/GNMB/2022/12/ &
   nohup cp -pr /nfs44/DZSQ/ZXZF/PCTSC/2022/12/31/ /nfs45/DZSQ/ZXZF/PCTSC/2022/12/ &	

   nohup cp -pr /nfs44/DZSQ/APPWW/202212/31/ /nfs45/DZSQ/APPWW/202212/ &	
   nohup cp -pr /nfs44/DZSQ/APPTEMPFILE/202212/31/ /nfs45/DZSQ/APPTEMPFILE/202212/ &	
   nohup cp -pr /nfs44/DZSQ/FWBWW/202212/31/ /nfs45/DZSQ/FWBWW/202212/ &	
   nohup cp -pr /nfs44/DZSQ/HZ/202212/31/ /nfs45/DZSQ/HZ/202212/ &	
   nohup cp -pr /nfs44/DZSQ/HZTEMP/202212/31/ /nfs45/DZSQ/HZTEMP/202212/ &	
   nohup cp -pr /nfs44/DZSQ/SQWJTIFF/202212/31/ /nfs45/DZSQ/SQWJTIFF/202212/ &	
   nohup cp -pr /nfs44/DZSQ/ZJZDZSQ/202212/31/ /nfs45/DZSQ/ZJZDZSQ/202212/ &	



新卷挂载命令
bash
mkdir /nfs45
mount  192.168.16.35:/vx/wwnas45 /nfs45
echo 'mount  192.168.6.11:/vol/wwnas44 /nfs45' >> /etc/rc.local   #AIX
tail -n 1 /etc/rc.local
df -g /nfs45

mkdir /nfs45
mount  192.168.16.35:/vx/wwnas45 /nfs45
echo 'mount  192.168.6.11:/vol/wwnas44 /nfs45' >> /etc/rc.d/rc.local   #Centos
tail -n 1 /etc/rc.d/rc.local
df -h /nfs45

10.160.59.214,
10.160.59.213,
10.160.59.212,
10.160.59.211, 

10.50.167.110

192.168.7.32 JHS不需要修改配置
192.168.7.31 JHS不需要修改配置

192.168.6.56 DWFW_1，DWFW_2
192.168.6.55 DWFW_1，DWFW_2
192.168.6.54 DWFW_1，DWFW_2
192.168.6.53 DWFW_1，DWFW_2


192.168.7.129 不需要修改配置
192.168.7.125 scxz，JHS不需要修改配置
192.168.7.127 scxz，JHS不需要修改配置
192.168.7.126 scxz，JHS不需要修改配置
192.168.7.125 scxz，JHS不需要修改配置
192.168.7.124 scxz，JHS不需要修改配置
192.168.7.123 scxz，JHS不需要修改配置
192.168.7.122 scxz，JHS不需要修改配置
192.168.7.121 scxz，JHS不需要修改配置




二、修改PathConfig.properties文件

bash
cp -pr /app/bea/SCXZ_1/WEB-INF/classes/PathConfig.properties /app/bea/SCXZ_1/WEB-INF/classes/PathConfig.properties.20221231

ls /app/bea/SCXZ_1/WEB-INF/classes/PathConfig.properties.20221231

perl -p -i -e "s/nfs44/nfs45/g"  /app/bea/SCXZ_1/WEB-INF/classes/PathConfig.properties
grep nfs45 /app/bea/SCXZ_1/WEB-INF/classes/PathConfig.properties



bash
cp -pr /app/bea/DWFW_1/WEB-INF/classes/PathConfig.properties /app/bea/DWFW_1/WEB-INF/classes/PathConfig.properties.20221231
cp -pr /app/bea/DWFW_2/WEB-INF/classes/PathConfig.properties /app/bea/DWFW_2/WEB-INF/classes/PathConfig.properties.20221231

ls /app/bea/DWFW_1/WEB-INF/classes/PathConfig.properties.20221231
ls /app/bea/DWFW_2/WEB-INF/classes/PathConfig.properties.20221231

perl -p -i -e "s/nfs44/nfs45/g" /app/bea/DWFW_1/WEB-INF/classes/PathConfig.properties
perl -p -i -e "s/nfs44/nfs45/g" /app/bea/DWFW_2/WEB-INF/classes/PathConfig.properties

grep nfs45 /app/bea/DWFW_1/WEB-INF/classes/PathConfig.properties
grep nfs45 /app/bea/DWFW_2/WEB-INF/classes/PathConfig.properties
```

