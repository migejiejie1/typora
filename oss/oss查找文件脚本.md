**oss查找文件**

查询脚本

```shell
#!/bin/bash 

file=$1

for i in $(cat buckets); do
  #echo $file
  #echo oss://${i}/${file}
  echo "正在搜索的Bucker是: $i"
  num=$(/root/oss/ossutil64 ls oss://${i}/${file} | grep 'Object Number' |awk '{print $NF}')
  if [ ! $num -eq 0 ]; then
    echo "Bucket: ${i} 存在该文件."
  fi
done
```

脚本原理

```shell
# ./ossutil64 ls oss://changruan-duiwai02/archived/2022/07/07/jpg/14965a6f358b7df6cec6a95421407d80.jpg
LastModifiedTime                   Size(B)  StorageClass   ETAG                                  ObjectName
2022-07-07 14:55:14 +0800 CST        48334      Standard   AB7331DBDEFFF7BCD046E5E0C0526D70      oss://changruan-duiwai02/archived/2022/07/07/jpg/14965a6f358b7df6cec6a95421407d80.jpg
Object Number is: 1

0.011877(s) elapsed

# 利用 ossutil64 工具的 ls 选项, 查询的文件存在时, Object Number 的值是 非0 , 文件不存在时, Object Number 的值为 0 。
```

ossutil64配置文件

```shell
# cat .ossutilconfig
[Credentials]
language=CH
endpoint=http://oss-cn-beijing-gzj1-d01-a.cnipaig2.cloud
accessKeyID=wPg4tEANXJWJZHZ3
accessKeySecret=4VdhplC3N8qSxD4UbwNXXOWXrJVc4L
```

buckets

```css
[root@manage ~]# cat buckets 
ads-trans
assert
bucket-containerfile
bucket-dmh-zip
bucket-document-migration
bucket-pct-dmh-unzip
bucket-pg-bak
bucket-wgqt-picture
changruan-duiwai01
changruan-duiwai02
db-backup
edoc
famingxinxingfenlei
fenlei
fushen
gongzhong
gwssi-dmh
gzj20210929
jingxiangdaochu
jssj
jssj-app
jssj-app-logs
jssj-pubsearch
jssjfenlei
jssjfsjs
jssjfwl
jssjgzcx
jssjjijian
jssjsjfullimg
jssjsjimg
jssjsjtrans
jssjsjtxt
paltform-mirror
paltform-ubuntu-mirros
platform-mirror-centos7
prod-administrative-review
prod-ajfl-doc
prod-appdoc-pdf
prod-appdoc-zip
prod-attach-file
prod-bucket-resources
prod-certificate-pdf
prod-dmh-gzyq-unzip
prod-dmh-unzip
prod-dmh-xml
prod-document-migration-pdf
prod-dsajfj-file
prod-dzqz-pdf
prod-epct-file
prod-fixed-report
prod-fmss-file
prod-fswx-file
prod-fw-file
prod-g-basic
prod-gjywzx-hzb
prod-gjywzx-ysb
prod-hysc-doc-image
prod-hysc-pdf
prod-information-file
prod-information-picture
prod-js-log
prod-log
prod-notice-affix-pdf
prod-notice-html
prod-notice-pdf
prod-notice-template
prod-notice-xml
prod-notice-zip
prod-other-data
prod-other-file
prod-paper-file
prod-pct-file-pdf
prod-pct-file-xml
prod-pct-filedata
prod-pct-gzyq-file-xml
prod-pct-notice-affix-pdf
prod-pct-notice-docx
prod-pct-notice-pdf
prod-pct-notice-xml
prod-pctcs-file
prod-pdf-zip
prod-publish-pdf
prod-return-notice
prod-signed-notice
prod-sqzlgl-file
prod-voucher-data
prod-wgfl-doc
prod-wghy-cswj
prod-wgqt-picture
prod-ww-dmh-file
prod-ww-doc-pdf
prod-zljffw-file
prod-zlswfw-file
prod-zlwx-doc
resources
shenchaxinxijiansuo
test-singed-notice
uat-oss-bucket
uat-resources
waiguanfenlei
```

