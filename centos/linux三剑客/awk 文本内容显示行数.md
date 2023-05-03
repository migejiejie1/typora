显示行号

```shell
[root@env-config ~]# awk '{if($0 != ""){print NR,$0} else {print $0}}' s.txt
1 ecloud-app/ecloud-app-ajfl:master.00001333
2 ecloud-app/ecloud-app-ayps-fmss:master.00000269
3 ecloud-app/ecloud-app-ayps-fs:master.00000174
4 ecloud-app/ecloud-app-ayps-pctgjjshcs:master.00000153
5 ecloud-app/ecloud-app-ayps-processor:master.00000061
6 ecloud-app/ecloud-app-ayps-syxx:master.00000223
7 ecloud-app/ecloud-app-ayps-wgsj:master.00000232
8 ecloud-app/ecloud-app-ayps-wgsjfl:master.00000106
```

以下方法也可显示行号

```bash
cat -n ./a.txt # 空白行也显示行号

nl ./a.txt # 空白行不显示行号
```

去重多余得空行

```shell
[root@node-31 ~]# awk '{if($0 != ""){print NR,$0} else {print $0}}' c.sh
1 sit-ecloud.txt:ecloud-front-app/ecloud-app-xzfy-ui:master.00000304

3 sit-ecloud.txt:ecloud-front-app/ecloud-app-zjgl-ui:release.00000001
4 pre-ecloud.txt:ecloud-front-app/ecloud-app-zjgl-ui:master.00000096

6 sit-ecloud.txt:ecloud-front-app/ecloud-app-zjsl-pct-ui:master.00000172





12 sit-ecloud.txt:ecloud-front-app/ecloud-app-zlspjk-ui:master.00000018
```

```shell
[root@node-31 ~]# awk '{if($0 != ""){print NR,$0} else {print $0}}' c.sh|uniq
1 sit-ecloud.txt:ecloud-front-app/ecloud-app-xzfy-ui:master.00000304

3 sit-ecloud.txt:ecloud-front-app/ecloud-app-zjgl-ui:release.00000001
4 pre-ecloud.txt:ecloud-front-app/ecloud-app-zjgl-ui:master.00000096

6 sit-ecloud.txt:ecloud-front-app/ecloud-app-zjsl-pct-ui:master.00000172
```

