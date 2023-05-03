**外网nas切换步骤**

一、nas卷拷贝数据

1. 新卷挂载命令

```shell
主机:
10.160.59.211-214 # 云外nas访问服务器01-04
10.160.58.157-158 # 数据迁移服务器，不需要挂载
10.50.167.114 # 没有密码，连接不上
10.50.167.110 # e系统
192.168.7.31-32 # 复审JHS, 不需要修改配置
192.168.6.53-56 # DWFW_1，DWFW_2
192.168.7.129 # 不需要修改配置
192.168.7.121-128 # scxz，JHS不需要修改配置

bash
mkdir /nfs43
mount 192.168.6.11:/vol/wwnas43 /nfs43
echo 'mount 192.168.6.10:/vol/wwnas43 /nfs43' >> /etc/rc.local   #AIX
tail -n 1 /etc/rc.local
df -g /nfs43

mkdir /nfs43
mount 192.168.6.11:/vol/wwnas43 /nfs43
echo 'mount 192.168.6.10:/vol/wwnas43 /nfs43' >> /etc/rc.d/rc.local   #Centos
tail -n 1 /etc/rc.d/rc.local
df -h /nfs43
```

2. 创建目录

```shell
mkdir -p -m 775 /nfs43/DZSQ
mkdir -p -m 775 /nfs43/cases
mkdir -p -m 775 /nfs43/dzsqyj
mkdir -p -m 775 /nfs43/dzsqyj/RAGE2
mkdir -p -m 775 /nfs43/dzsqyj/RAGE2/dzsqyj
mkdir -p -m 775 /nfs43/dzsqyj/RAGE2/dzsqyj/DZSQYJ
mkdir -p -m 775 /nfs43/dzsqyj/RAGE2/dzsqyj/DZSQYJ/APPWW
mkdir -p -m 775 /nfs43/dzsqyj/DZSQYJ
mkdir -p -m 775 /nfs43/dzsqyj/DZSQYJ/HZ
mkdir -p -m 775 /nfs43/dzsqyj/DZSQYJ/APPWW
mkdir -p -m 775 /nfs43/jwcx
mkdir -p -m 775 /nfs43/JHS
mkdir -p -m 775 /nfs43/DZSQ/CA
mkdir -p -m 775 /nfs43/DZSQ/APPXSD
mkdir -p -m 775 /nfs43/DZSQ/cfca
mkdir -p -m 775 /nfs43/DZSQ/TOOLS
mkdir -p -m 775 /nfs43/DZSQ/UNIONPAY
mkdir -p -m 775 /nfs43/DZSQ/UPDATE
mkdir -p -m 775 /nfs43/DZSQ/DZSQ_APPWW_UNZIP
mkdir -p -m 775 /nfs43/DZSQ/DZSQ_APPWW_UNZIP/cases
mkdir -p -m 775 /nfs43/DZSQ/DZSQ_APPWW_UNZIP/cases/inventions
mkdir -p -m 775 /nfs43/DZSQ/DZSQ_APPWW_UNZIP/cases/utility_models
mkdir -p -m 775 /nfs43/DZSQ/DZSQ_APPWW_UNZIP/cases/Invalidation
mkdir -p -m 775 /nfs43/DZSQ/DZSQ_APPWW_UNZIP/cases/PCT_inventions
mkdir -p -m 775 /nfs43/DZSQ/DZSQ_APPWW_UNZIP/cases/designs
mkdir -p -m 775 /nfs43/DZSQ/APPWW
mkdir -p -m 775 /nfs43/DZSQ/APPWW/test
mkdir -p -m 775 /nfs43/DZSQ/APPWW/test3
mkdir -p -m 775 /nfs43/DZSQ/APPTEMPFILE
mkdir -p -m 775 /nfs43/DZSQ/FWBWW
mkdir -p -m 775 /nfs43/DZSQ/HZ
mkdir -p -m 775 /nfs43/DZSQ/HZTEMP
mkdir -p -m 775 /nfs43/DZSQ/SQWJTIFF
mkdir -p -m 775 /nfs43/DZSQ/ZJZDZSQ
mkdir -p -m 775 /nfs43/DZSQ/ZXZF
mkdir -p -m 775 /nfs43/DZSQ/ZXZF/GNMB
mkdir -p -m 775 /nfs43/DZSQ/ZXZF/PCTSC
mkdir -p -m 775 /nfs43/DZSQ/cases

mkdir -p -m 775 /nfs43/DZSQ/ZXZF/GNMB/2022/
mkdir -p -m 775 /nfs43/DZSQ/ZXZF/PCTSC/2022/
mkdir -p -m 775 /nfs43/DZSQ/APPWW/202211/
mkdir -p -m 775 /nfs43/DZSQ/APPTEMPFILE/202211/
mkdir -p -m 775 /nfs43/DZSQ/FWBWW/202211/
mkdir -p -m 775 /nfs43/DZSQ/HZ/202211/
mkdir -p -m 775 /nfs43/DZSQ/HZTEMP/202211/
mkdir -p -m 775 /nfs43/DZSQ/SQWJTIFF/202211/
mkdir -p -m 775 /nfs43/DZSQ/ZJZDZSQ/202211/
mkdir -p -m 775 /nfs43/DZSQ/DZSQ_APPWW_UNZIP/202211/
mkdir -p -m 775 /nfs43/DZSQ/ZXZF/GNMB/2022/10/
mkdir -p -m 775 /nfs43/DZSQ/ZXZF/PCTSC/2022/10/
```

3. 修改目录属主属组

```shell
chown -R weblogic:bea /nfs43/
chmod 777 /nfs43/
```

4. 拷贝模板和数据

```shell
模板文件拷贝:
nohup cp -pr /nfs42/DZSQ/CA/ /nfs43/DZSQ/ &
nohup cp -pr /nfs42/DZSQ/APPXSD/ /nfs43/DZSQ/ &
nohup cp -pr /nfs42/DZSQ/cfca/ /nfs43/DZSQ/ &
nohup cp -pr /nfs42/DZSQ/TOOLS/ /nfs43/DZSQ/ &
nohup cp -pr /nfs42/DZSQ/UNIONPAY/ /nfs43/DZSQ/ &
nohup cp -pr /nfs42/DZSQ/UPDATE/ /nfs43/DZSQ/ &
nohup cp -pr /nfs42/DZSQ/700000000000001-SIGN.pfx /nfs43/DZSQ/ &
nohup cp -pr /nfs42/cases/login.jsp /nfs43/cases/ &

数据文件拷贝(当月)
nohup cp -pr /nfs42/DZSQ/DZSQ_APPWW_UNZIP/202211/{1..10} /nfs43/DZSQ/DZSQ_APPWW_UNZIP/202211/ &
nohup cp -pr /nfs42/DZSQ/ZXZF/GNMB/2022/11/{1..10} /nfs43/DZSQ/ZXZF/GNMB/2022/11/ &
nohup cp -pr /nfs42/DZSQ/ZXZF/PCTSC/2022/11/{1..10} /nfs43/DZSQ/ZXZF/PCTSC/2022/11/ &

数据文件拷贝(当天)
nohup cp -pr /nfs42/DZSQ/DZSQ_APPWW_UNZIP/202211/11 /nfs43/DZSQ/DZSQ_APPWW_UNZIP/202211/ &
nohup cp -pr /nfs42/DZSQ/ZXZF/GNMB/2022/11/11 /nfs43/DZSQ/ZXZF/GNMB/2022/11/ &
nohup cp -pr /nfs42/DZSQ/ZXZF/PCTSC/2022/11/11 /nfs43/DZSQ/ZXZF/PCTSC/2022/11/ &

nohup cp -pr /nfs42/DZSQ/APPWW/202211/11/ /nfs43/DZSQ/APPWW/202211/ &	
nohup cp -pr /nfs42/DZSQ/APPTEMPFILE/202211/11/ /nfs43/DZSQ/APPTEMPFILE/202211/ &	
nohup cp -pr /nfs42/DZSQ/FWBWW/202211/11/ /nfs43/DZSQ/FWBWW/202211/ &	
nohup cp -pr /nfs42/DZSQ/HZ/202211/11/ /nfs43/DZSQ/HZ/202211/ &	
nohup cp -pr /nfs42/DZSQ/HZTEMP/202211/11/ /nfs43/DZSQ/HZTEMP/202211/ &	
nohup cp -pr /nfs42/DZSQ/SQWJTIFF/202211/11/ /nfs43/DZSQ/SQWJTIFF/202211/ &	
nohup cp -pr /nfs42/DZSQ/ZJZDZSQ/202211/11/ /nfs43/DZSQ/ZJZDZSQ/202211/ &	
```

二、修改PathConfig.properties文件

```shell
对外服务
bash
cp -pr /app/bea/DWFW_1/WEB-INF/classes/PathConfig.properties /app/bea/DWFW_1/WEB-INF/classes/PathConfig.properties.20221121
cp -pr /app/bea/DWFW_2/WEB-INF/classes/PathConfig.properties /app/bea/DWFW_2/WEB-INF/classes/PathConfig.properties.20221121

ls /app/bea/DWFW_1/WEB-INF/classes/PathConfig.properties.20221121
ls /app/bea/DWFW_2/WEB-INF/classes/PathConfig.properties.20221121

perl -p -i -e "s/nfs42/nfs43/g" /app/bea/DWFW_1/WEB-INF/classes/PathConfig.properties
perl -p -i -e "s/nfs42/nfs43/g" /app/bea/DWFW_2/WEB-INF/classes/PathConfig.properties

grep nfs43 /app/bea/DWFW_1/WEB-INF/classes/PathConfig.properties
grep nfs43 /app/bea/DWFW_2/WEB-INF/classes/PathConfig.properties

上传下载
bash
cp -pr /app/bea/SCXZ_1/WEB-INF/classes/PathConfig.properties /app/bea/SCXZ_1/WEB-INF/classes/PathConfig.properties.20221130

ls /app/bea/SCXZ_1/WEB-INF/classes/PathConfig.properties.20221130

perl -p -i -e "s/nfs42/nfs43/g"  /app/bea/SCXZ_1/WEB-INF/classes/PathConfig.properties

grep nfs43 /app/bea/SCXZ_1/WEB-INF/classes/PathConfig.properties
```

