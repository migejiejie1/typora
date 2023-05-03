**在GPU计算型实例中安装GPU驱动（Linux）**

## 操作步骤

1. 访问[NVIDIA驱动下载](https://www.nvidia.com/Download/Find.aspx?spm=a2c4g.11186623.0.0.2d456e79TqbJ1l&lang=cn)。

2. 设置搜索条件后，单击**搜索**查找适用的驱动程序。

   <img src="../Image/image-20220908110407143.png" alt="image-20220908110407143" style="zoom:50%;" />

3. 在搜索到的驱动程序列表下，选择需要下载的驱动版本，单击对应的驱动名称。

4. 在打开**驱动程序下载**页面，单击**下载**，然后在**NVIDIA驱动程序下载**页面，右键单击**下载**并选择**复制链接地址**。

   <img src="../Image/image-20220908110418367.png" alt="image-20220908110418367" style="zoom:50%;" />

5. 使用`wget`命令，并粘贴您在[步骤4](https://help.aliyun.com/document_detail/163824.html#step-9m4-hhm-y4c)中复制的驱动下载链接，执行命令下载安装包。命令示例如下所示：

   ```ruby
   wget https://cn.download.nvidia.com/tesla/460.73.01/NVIDIA-Linux-x86_64-460.73.01.run
   ```

6. 安装GPU驱动。

   1. 如果您的操作系统为CentOS，请执行以下命令，查询实例中是否安装kernel-devel和kernel-headers包。对于ubuntu等其他操作系统，已经在其中预装了kernel-devel和kernel-headers包，请跳过此步骤。

      ```javascript
      rpm  -qa | grep $(uname -r)
      ```

      - 如果回显类似如下信息，即包含了kernel-devel和kernel-headers包的版本信息，表示已安装。

        ```undefined
        kernel-3.10.0-1062.18.1.el7.x86_64
        kernel-devel-3.10.0-1062.18.1.el7.x86_64
        kernel-headers-3.10.0-1062.18.1.el7.x86_64
        ```

      - 如果在回显信息中，您没有找到 kernel-devel-* 和 kernel-headers-* 内容，您需要自行下载并安装kernel对应版本的 [kernel-devel](https://pkgs.org/download/kernel-devel) 和 [kernel-headers](http://www.rpmfind.net/linux/rpm2html/search.php?query=kernel-headers)包。

        **说明** kernel-devel和kernel版本不一致会导致在安装driver rpm过程中driver编译出错。因此，请您确认回显信息中kernel-*的版本号后，再下载对应版本的kernel-devel。在示例回显信息中，kernel的版本号为3.10.0-1062.18.1.el7.x86_64。

   2. 安装GPU驱动。

      以操作系统是Linux 64-bit的驱动为例，您下载的GPU驱动为.run格式，例如：NVIDIA-Linux-x86_64-xxxx.run。分别执行以下命令，授权并安装GPU驱动。

      ```bash
      chmod +x NVIDIA-Linux-x86_64-xxxx.run
      sh NVIDIA-Linux-x86_64-xxxx.run
      ```

      安装完成后，执行如下命令，查看是否安装成功。

      ```undefined
      nvidia-smi
      ```

      回显信息类似如下所示，表示GPU驱动安装成功。

      <img src="../Image/image-20220908110433594.png" alt="image-20220908110433594" style="zoom:50%;" />