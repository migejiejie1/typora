

压测脚本

```shell
[root@node-31 csal]# pwd
/docker/example/csal
[root@node-31 csal]# ls
01-gateway       07-发明初审              13-在线电子申请及受理  18-缴费-bak  24-PCT流程           
02-公开公告      08-法律                  14-PCT受理客户端       19-移动端    25-实审              
03-外观审计审查  09-国家申请纸件受理采集  15-复审                20-审查管理  26-电子申请受理审查 
04-保密          10-PPH                   16-公共                21-纸件      27-PCT受理纸件                
05-新型          11-PCT初审               17-发文                22-专利事务  28-PCT受理在钱      
06-收费          12-分类                  18-缴费                23-PCT实审   29-专利业务办理      
a.sh             b.sh                     name   
# a.sh 压测的脚本  b.sh 批量执行压测脚本   name 接口名称

```

a.sh 压测的脚本

```shell
[root@node-31 csal]# cat a.sh 
#!/bin/bash

# 第一种配置方法
#serviceName='$1'
#service='ecloud-app-wgsj-sc'
#num_threads='30 100 300 500'
#num_threads='3'
#run_time='300'
#run_time='5'


# 第二种配置方法
pb=$1    # 传入 p 或者 b , 不同的参数对应不同的变量值
#sName=$2
sName='ecloud-app-wgsj-sc'   # 服务名

if [ ! -z $pb ] && [ ! -z $sName ] ; then   # 判断字符串是否为空, 为空时抛出使用办法 Usage: xxx
  service="${sName}"   # 设置服务名
  
  case ${pb} in
    pre|p)   # 测试脚本能否正常运行, 是否会报错
      num_threads='5'  # 设置压测线程数, 也表示并发用户数
      run_time='5'    # 设置压测时间为 * 秒
    ;;
    begin|b)    # 正式开始进行压测
      #num_threads='30 100 300 500'
      num_threads='100 300 500'  # 设置压测线程数, 也表示并发用户数
      run_time='180'   # 设置压测时间为 * 秒
    ;;
    #*)
    #  echo "Usage: $0 [pre/p | begin/b] [serviceName]"
    #;;
  esac
 
  #echo num_threads: $num_threads run_time: $run_time
  #echo service: $service
else
  echo "Usage: $0 [pre/p | begin/b] [service]"   # 抛出使用方法
  exit 
fi


# 设置压测报告存放的路径, 方便统一管理
report_path="/docker/report/${service}/$(date +%F)/$(date +%H-%M)"
# 设置修改后的压测报告存放的路径
report_edit_path="/docker/report-re/${service}/$(date +%F)/$(date +%H-%M)"

#/docker/example/start ${service}-${1}.jmx
#cp /docker/html/statistics.json /docker/report/${service}-${500}-${num_threads}.json


# 若压测报告存放目录不存在, 则进行创建
if [ ! -d ${report_path} ];then
  mkdir -p ${report_path} ${report_edit_path}
fi

# 根据上面case命令获取到的压测时间修改jmeter压测时间
echo "++++++++++++++++++++++++++++ 每个接口压测时间为 ${run_time} 秒! ++++++++++++++++++++++++++++" 
echo
sed -i "/duration/ s/[0-9]\+/${run_time}/" ${service}-*
#sed -n "/duration/p" ${service}-*

# 根据上面case命令获取到的压测线程数修改并发用户数 
for j in ${num_threads}
do
  echo "++++++++++++++++++++++++++++ 新一轮压测开始，并发用户 ${j} 个! ++++++++++++++++++++++++++++"
  echo
  sed -i "/num_threads/ s/[0-9]\+/${j}/" ${service}-*    #修改并发用户数 
  #sed -n "/num_threads/p" ${service}-*

  # 压测开始
  for i in `cat ./name`   # 循环读取接口名称
  do 
    echo ------- ${i} 开始压测了, 并发用户 ${j} 个！-------
    /docker/example/start ${service}-${i}.jmx    # 循环调取jmeter执行程序, 并传入 .jmx 文件进行压测
    cp /docker/html/statistics.json ${report_path}/${service}-${i}-${j}.json  # 将生成的压测报告拷贝到前面设置的压测报告存放的路径中, 方便统一管理
    echo
  done

done

echo '++++++++++++++++++++++++++++ 压测结束了~ ++++++++++++++++++++++++++++' 


#for i in `cat ./name`
# do 
#  echo -------${i} 开始压测了, 并发用户 ${num_threads} 个！-------
#  /docker/example/start ${service}-${i}.jmx
#  cp /docker/html/statistics.json /docker/report/${service}-${i}-${num_threads}-$(date +%s).json
#  echo
#done

# 处理压测报告, 默认生成的压测报告有一部分数据为重复数据, 这里只取需要的部分, 并将其存放到 修改后的压测报告的路径中
for k in `ls ${report_path}`
do 
  sed -n '20,28p' ${report_path}/${k} > ${report_edit_path}/${k}
done

# 压测报告默认为英文显示, 将其处理成中文
sed -i \
  -e 's/errorCount/错误数/' \
  -e 's/errorPct/错误率/' \
  -e 's/meanResTime/平均值(ms)/' \
  -e 's/minResTime/最小值(ms)/' \
  -e 's/maxResTime/最大值(ms)/' \
  -e 's/pct1ResTime/90%(ms)/' \
  -e 's/pct2ResTime/95%(ms)/' \
  -e 's/pct3ResTime/99%(ms)/' \
  -e 's/throughput/吞吐量\/s/' \
  ${report_edit_path}/*

# 压测报告中默认小数点后保留很多位, 这里只保留小数点后1位
for i in `ls ${report_edit_path}/*`
do
  awk 'BEGIN{FS=OFS=" "} {$3=sprintf("%.1f,",$3)} 1 ENG{print > "'${i}'"}' ${i}
done

# 增加空格，美化输出
sed -i 's/^"/    "/' ${report_edit_path}/*
#sed -n 's/^"/    "/p' ${report_edit_path}/*


```

name 接口名称

```shell
[root@node-31 03-外观审计审查]# cat name 
未结案件新案未入审
未结案件新案已入审
通知书查询
菜单树查询数量接口审查
菜单树查询数量接口评价报告
```

/docker/example/start  jmeter执行程序

```shell
[root@node-31 csal]# cd /docker/example/
[root@node-31 example]# ls
start
[root@node-31 example]# cat /docker/example/start 
#!/bin/bash

# 压测报告存放目录
DataDir="/docker/html/"

# 压测脚本文件
#JmxFile="ecloud-app-gateway.jmx"
JmxFile="$1" # 通过 a.sh 脚本进行传输

# 清除上一轮压测报告, 否则运行会报错
rm -rf $DataDir/*

# 开始压测 
# -r 表示使用jmeter集群进行压测, -l 指定结果文件存放路径(每条压测的结果,200,404等报错信息), -o 指定输出文件夹路径(压测报告), -t 指定jmx压测脚本文件
#jmeter -r -n -t $JmxFile -l $DataDir/base.jtl -e -o $DataDir

jmeter -n -t $JmxFile -l $DataDir/base.jtl -e -o $DataDir

#jmeter -n -t $JmxFile -l $DataDir/base.csv -e -o $DataDir

```

b.sh 批量执行压测脚本

```shell
[root@node-31 csal]# pwd
/docker/example/csal
[root@node-31 csal]# cat b.sh 
#!/bin/bash

cd /docker/example/csal/28-PCT受理在钱/ && ./a.sh b
cd /docker/example/csal/29-专利业务办理/ && ./a.sh b
cd /docker/example/csal/20-审查管理/ && ./a.sh b
cd /docker/example/csal/18-缴费/ && ./a.sh b
cd /docker/example/csal/14-PCT受理客户端/ && ./a.sh b
cd /docker/example/csal/08-法律/ && ./a.sh b

```

jmx压测脚本文件

```shell
[root@node-31 csal]# cd 03-外观审计审查/
[root@node-31 03-外观审计审查]# ls
ecloud-app-wgsj-sc-未结案件新案未入审.jmx          ecloud-app-wgsj-sc-通知书保存.jmx
ecloud-app-wgsj-sc-主屏文件信息.jmx        ecloud-app-wgsj-sc-组长待审批.jmx      ecloud-app-wgsj-sc-通知书发送.jmx
ecloud-app-wgsj-sc-主屏案件基本信息.jmx    ecloud-app-wgsj-sc-菜单树查询数量接口审查.jmx  
ecloud-app-wgsj-sc-参评员待审批.jmx        ecloud-app-wgsj-sc-菜单树查询数量接口评价报告.jmx  jmeter.log
ecloud-app-wgsj-sc-已结案件查询.jmx        ecloud-app-wgsj-sc-评价报告主评员待审批.jmx        name
ecloud-app-wgsj-sc-未结案件新案已入审.jmx  ecloud-app-wgsj-sc-辐屏图片.jmx                    a.sh
ecloud-app-wgsj-sc-通知书查询.jmx

```

