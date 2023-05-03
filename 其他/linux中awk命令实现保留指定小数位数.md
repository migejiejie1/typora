linux中awk命令实现保留指定小数位数

```shell
root@PC1:/home/test2# ls
a.txt
root@PC1:/home/test2# cat a.txt                     ## 测试数据
3.3724  34.348  988.383
0.0001  34.837  381.439
3.2534  32.732  358.346
root@PC1:/home/test2# awk '{for(i = 1; i <= NF; i++) {printf("%.1f\t", $i)} {printf("\n")}}' a.txt
3.4     34.3    988.4                               ## 一位小数
0.0     34.8    381.4
3.3     32.7    358.3
root@PC1:/home/test2# awk '{for(i = 1; i <= NF; i++) {printf("%.2f\t", $i)} {printf("\n")}}' a.txt
3.37    34.35   988.38                               ## 两位小数
0.00    34.84   381.44
3.25    32.73   358.35
root@PC1:/home/test2# awk '{for(i = 1; i <= NF; i++) {printf("%.6f\t", $i)} {printf("\n")}}' a.txt
3.372400        34.348000       988.383000           ## 六位小数
0.000100        34.837000       381.439000
3.253400        32.732000       358.346000
```