```shell
/opt/monitor_wgjsService.sh
#!/bin/bash
# check wgjs server status
wgjs1="./sw1.sh"
wgjs2="./sw2.sh"
scriptsPath="/app/bea/scripts/"

wgjs1ps=$(ps -ef |grep newwgjs1 |grep -v grep |wc -l)
wgjs2ps=$(ps -ef |grep newwgjs2 |grep -v grep |wc -l)

if [ $wgjs1ps -eq 0 ];then
    cd $scriptsPath
    $wgjs1
    sleep 5
    wgjs1ps=$(ps -ef |grep newwgjs1 |grep -v grep |wc -l)
    if [ $wgjs1ps -eq 0 ];then
       echo "wgjs1 Service exception !"|tee -a /var/log/messages
    fi
fi

if [ $wgjs2ps -eq 0 ];then
    cd $scriptsPath
    $wgjs2
    sleep 5
    wgjs2ps=$(ps -ef |grep newwgjs2 |grep -v grep |wc -l)
    if [ $wgjs2ps -eq 0 ];then
       echo "wgjs2 Service exception !"|tee -a /var/log/messages
    fi
fi

/opt/run_wgjs.sh
#!/bin/bash
while :
do
    /opt/monitor_wgjsService.sh
    sleep 5
done

chown wguser:bea /opt/monitor_wgjsService.sh
chown wguser:bea /opt/run_wgjs.sh
chmod +x /opt/monitor_wgjsService.sh /opt/run_wgjs.sh

vim ~/.bashrc
nohup /opt/run_wgjs.sh &

```

