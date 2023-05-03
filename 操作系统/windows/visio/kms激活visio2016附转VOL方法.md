```basic
kms激活visio2016附转VOL方法

概述
现在大家的电脑基本上都装了杀毒软件，而一般的激活软件(kms、小马)都会被杀毒软件报毒，而且网上下载的激活程序不知道有没有被修改过。所以使用kms激活是比较方便、安全的方法了。
如果觉得使用别人的kms激活不安全的，也可以考虑自己搭建一个kms服务器。docker安装kms服务
由于kms激活需要VOL版本，而在I Tell You 上没找到VOL版本的Visio 2016。所以要先将零售版转换为VOL版本，再用kms方式激活。如果觉得麻烦可以直接去下载批处理命令脚本，用管理员权限运行，根据提示选择相应的序号进行版本转换。下载链接：https://pan.baidu.com/s/1HazC0FouVhSZ5MOqE5poDA 提取码：4h5k
下面是转换版本和激活的详细步骤，实际上office也是可以用这种方式激活的。

转换Visio为VOL版本
首先去I Tell You 上下载你想要的Visio，安装后将下方代码复制到文本文件中，并重命名文件为.bat结尾的文件。如果中文显示为乱码，以ANSI 格式保存可以解决。

========================================================

@ECHO OFF&PUSHD %~DP0
 
setlocal EnableDelayedExpansion&color 3e & cd /d "%~dp0"
title office2016 retail转换vol版
 
%1 %2
mshta vbscript:createobject("shell.application").shellexecute("%~s0","goto :runas","","runas",1)(window.close)&goto :eof
:runas
 
if exist "%ProgramFiles%\Microsoft Office\Office16\ospp.vbs" cd /d "%ProgramFiles%\Microsoft Office\Office16"
if exist "%ProgramFiles(x86)%\Microsoft Office\Office16\ospp.vbs" cd /d "%ProgramFiles(x86)%\Microsoft Office\Office16"
 
:WH
cls
echo.
echo                         选择需要转化的office版本序号
echo.
echo --------------------------------------------------------------------------------                                                         
echo                 1. 零售版 Office Pro Plus 2016 转化为VOL版
echo.
echo                 2. 零售版 Office Visio Pro 2016 转化为VOL版
echo.
echo                 3. 零售版 Office Project Pro 2016 转化为VOL版
echo.
echo. --------------------------------------------------------------------------------
                                                        
set /p tsk="请输入需要转化的office版本序号【回车】确认（1-3）: "
if not defined tsk goto:err
if %tsk%==1 goto:1
if %tsk%==2 goto:2
if %tsk%==3 goto:3
 
:err
goto:WH
 
:1
cls
 
echo 正在重置Office2016零售激活...
cscript ospp.vbs /rearm
 
echo 正在安装 KMS 许可证...
for /f %%x in ('dir /b ..\root\Licenses16\proplusvl_kms*.xrm-ms') do cscript ospp.vbs /inslic:"..\root\Licenses16\%%x" >nul
 
echo 正在安装 MAK 许可证...
for /f %%x in ('dir /b ..\root\Licenses16\proplusvl_mak*.xrm-ms') do cscript ospp.vbs /inslic:"..\root\Licenses16\%%x" >nul
 
echo 正在安装 KMS 密钥...
cscript ospp.vbs /inpkey:XQNVK-8JYDB-WJ9W3-YJ8YR-WFG99
 
goto :e
 
:2
cls
 
echo 正在重置Visio2016零售激活...
cscript ospp.vbs /rearm
 
echo 正在安装 KMS 许可证...
for /f %%x in ('dir /b ..\root\Licenses16\visio???vl_kms*.xrm-ms') do cscript ospp.vbs /inslic:"..\root\Licenses16\%%x" >nul
 
echo 正在安装 MAK 许可证...
for /f %%x in ('dir /b ..\root\Licenses16\visio???vl_mak*.xrm-ms') do cscript ospp.vbs /inslic:"..\root\Licenses16\%%x" >nul
 
echo 正在安装 KMS 密钥...
cscript ospp.vbs /inpkey:PD3PC-RHNGV-FXJ29-8JK7D-RJRJK
 
goto :e
 
:3
cls
 
echo 正在重置Project2016零售激活...
cscript ospp.vbs /rearm
 
echo 正在安装 KMS 许可证...
for /f %%x in ('dir /b ..\root\Licenses16\project???vl_kms*.xrm-ms') do cscript ospp.vbs /inslic:"..\root\Licenses16\%%x" >nul
 
echo 正在安装 MAK 许可证...
for /f %%x in ('dir /b ..\root\Licenses16\project???vl_mak*.xrm-ms') do cscript ospp.vbs /inslic:"..\root\Licenses16\%%x" >nul
 
echo 正在安装 KMS 密钥...
cscript ospp.vbs /inpkey:YG9NW-3K39V-2T3HJ-93F3Q-G83KT
 
goto :e
 
:e
echo.
echo 转化完成，按任意键退出！
pause >nul
exit

========================================================

设置kms服务器地址并激活
以管理员权限运行CMD，进入office激活脚本所在的目录。
64位默认路径为C:\Program Files\Microsoft Office\Office16，32位默认路径为C:\Program Files (x86)\Microsoft Office\Office16
我安装的是64位的，在CMD中运行。

cd "C:\Program Files\Microsoft Office\Office16"
设置kms服务器地址
用的比较多的kms服务器有以下三个，如果命令行中的不行，可以尝试其它两个。
kms.03k.org
kms.luody.info
kms.idina.cn
cscript ospp.vbs /sethst:kms.idina.cn
激活

cscript ospp.vbs /act

作者：whisshe
链接：https://www.jianshu.com/p/501a98b5d0cc
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

