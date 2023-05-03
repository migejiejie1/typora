```shell
https://www.cnblogs.com/lldsn/p/10479493.html

#vim /mirror/script/centos_yum_update.sh

#!/bin/bash

echo 'Updating Aliyum Source'

DATETIME=`date +%F_%T`

exec > /var/log/aliyumrepo_$DATETIME.log

     reposync -np /mirror

if [ $? -eq 0 ];then

      createrepo --update /mirror/base

      createrepo --update /mirror/extras

      createrepo --update /mirror/updates

      createrepo --update /mirror/epel

    echo "SUCESS: $DATETIME aliyum_yum update successful"

  else

    echo "ERROR: $DATETIME aliyum_yum update failed"

fi

 

将脚本加入到定时任务中

# crontab -e

# Updating Aliyum Source

00 13 * * 6 [ $(date +%d) -eq $(cal | awk 'NR==3{print $NF}') ] && /bin/bash /mirror/script/centos_yum_update.sh

每月第一个周六的13点更新阿里云yum源
```

