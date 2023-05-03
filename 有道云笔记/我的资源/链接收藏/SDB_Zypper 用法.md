[**Zypper**](https://zh.opensuse.org/Portal:Zypper)

**用法** - [疑难解答](https://zh.opensuse.org/SDB:Zypper_疑难解答) - [版本](http://en.opensuse.org/openSUSE:Zypper_versions) - [功能](https://zh.opensuse.org/openSUSE:Zypper_功能) - [开发](https://zh.opensuse.org/index.php?title=openSUSE:Zypper_开发&action=edit&redlink=1) - [路线图](http://en.opensuse.org/openSUSE:Zypper_roadmap) - [Bug 领养](http://en.opensuse.org/openSUSE:Zypper_bugs_for_adoption) - [团队](http://en.opensuse.org/openSUSE:Zypper_team)

版本：11.3 本文适用于 [openSUSE 11.3](https://zh.opensuse.org/Portal:11.3) 的 1.4.5 版的 Zypper 。

本文对 [Zypper](https://zh.opensuse.org/Category:Zypper) 用法的介绍可以视为对 Zypper 手册页 (man zypper) 的补充。

**目录**

- [1 快速参考](https://zh.opensuse.org/SDB:Zypper_用法#.E5.BF.AB.E9.80.9F.E5.8F.82.E8.80.83)

- - [1.1 备忘单](https://zh.opensuse.org/SDB:Zypper_用法#.E5.A4.87.E5.BF.98.E5.8D.95)

- [2 常规用法](https://zh.opensuse.org/SDB:Zypper_用法#.E5.B8.B8.E8.A7.84.E7.94.A8.E6.B3.95)

- - [2.1 答复提示](https://zh.opensuse.org/SDB:Zypper_用法#.E7.AD.94.E5.A4.8D.E6.8F.90.E7.A4.BA)

- [3 词汇表](https://zh.opensuse.org/SDB:Zypper_用法#.E8.AF.8D.E6.B1.87.E8.A1.A8)
- [4 命令](https://zh.opensuse.org/SDB:Zypper_用法#.E5.91.BD.E4.BB.A4)

- - [4.1 帮助信息](https://zh.opensuse.org/SDB:Zypper_用法#.E5.B8.AE.E5.8A.A9.E4.BF.A1.E6.81.AF)
  - [4.2 供应源管理](https://zh.opensuse.org/SDB:Zypper_用法#.E4.BE.9B.E5.BA.94.E6.BA.90.E7.AE.A1.E7.90.86)

- - - [4.2.1 列出设定的供应源](https://zh.opensuse.org/SDB:Zypper_用法#.E5.88.97.E5.87.BA.E8.AE.BE.E5.AE.9A.E7.9A.84.E4.BE.9B.E5.BA.94.E6.BA.90)
    - [4.2.2 添加供应源](https://zh.opensuse.org/SDB:Zypper_用法#.E6.B7.BB.E5.8A.A0.E4.BE.9B.E5.BA.94.E6.BA.90)
    - [4.2.3 刷新供应源](https://zh.opensuse.org/SDB:Zypper_用法#.E5.88.B7.E6.96.B0.E4.BE.9B.E5.BA.94.E6.BA.90)
    - [4.2.4 移除供应源](https://zh.opensuse.org/SDB:Zypper_用法#.E7.A7.BB.E9.99.A4.E4.BE.9B.E5.BA.94.E6.BA.90)
    - [4.2.5 调整供应源](https://zh.opensuse.org/SDB:Zypper_用法#.E8.B0.83.E6.95.B4.E4.BE.9B.E5.BA.94.E6.BA.90)
    - [4.2.6 重命名供应源](https://zh.opensuse.org/SDB:Zypper_用法#.E9.87.8D.E5.91.BD.E5.90.8D.E4.BE.9B.E5.BA.94.E6.BA.90)
    - [4.2.7 导出导入供应源](https://zh.opensuse.org/SDB:Zypper_用法#.E5.AF.BC.E5.87.BA.E5.AF.BC.E5.85.A5.E4.BE.9B.E5.BA.94.E6.BA.90)

- - [4.3 服务管理](https://zh.opensuse.org/SDB:Zypper_用法#.E6.9C.8D.E5.8A.A1.E7.AE.A1.E7.90.86)
  - [4.4 软件包管理](https://zh.opensuse.org/SDB:Zypper_用法#.E8.BD.AF.E4.BB.B6.E5.8C.85.E7.AE.A1.E7.90.86)

- - - [4.4.1 选择软件包](https://zh.opensuse.org/SDB:Zypper_用法#.E9.80.89.E6.8B.A9.E8.BD.AF.E4.BB.B6.E5.8C.85)
    - [4.4.2 安装软件包](https://zh.opensuse.org/SDB:Zypper_用法#.E5.AE.89.E8.A3.85.E8.BD.AF.E4.BB.B6.E5.8C.85)
    - [4.4.3 移除软件包](https://zh.opensuse.org/SDB:Zypper_用法#.E7.A7.BB.E9.99.A4.E8.BD.AF.E4.BB.B6.E5.8C.85)
    - [4.4.4 源码包和构建依赖](https://zh.opensuse.org/SDB:Zypper_用法#.E6.BA.90.E7.A0.81.E5.8C.85.E5.92.8C.E6.9E.84.E5.BB.BA.E4.BE.9D.E8.B5.96)
    - [4.4.5 更新软件包](https://zh.opensuse.org/SDB:Zypper_用法#.E6.9B.B4.E6.96.B0.E8.BD.AF.E4.BB.B6.E5.8C.85)

- - [4.5 查询](https://zh.opensuse.org/SDB:Zypper_用法#.E6.9F.A5.E8.AF.A2)

- - - [4.5.1 搜寻软件包](https://zh.opensuse.org/SDB:Zypper_用法#.E6.90.9C.E5.AF.BB.E8.BD.AF.E4.BB.B6.E5.8C.85)
    - [4.5.2 获取软件包的信息](https://zh.opensuse.org/SDB:Zypper_用法#.E8.8E.B7.E5.8F.96.E8.BD.AF.E4.BB.B6.E5.8C.85.E7.9A.84.E4.BF.A1.E6.81.AF)
    - [4.5.3 依赖关系](https://zh.opensuse.org/SDB:Zypper_用法#.E4.BE.9D.E8.B5.96.E5.85.B3.E7.B3.BB)
    - [4.5.4 其他查询](https://zh.opensuse.org/SDB:Zypper_用法#.E5.85.B6.E4.BB.96.E6.9F.A5.E8.AF.A2)

- - [4.6 软件包锁定](https://zh.opensuse.org/SDB:Zypper_用法#.E8.BD.AF.E4.BB.B6.E5.8C.85.E9.94.81.E5.AE.9A)
  - [4.7 工具](https://zh.opensuse.org/SDB:Zypper_用法#.E5.B7.A5.E5.85.B7)

- - - [4.7.1 验证依赖关系](https://zh.opensuse.org/SDB:Zypper_用法#.E9.AA.8C.E8.AF.81.E4.BE.9D.E8.B5.96.E5.85.B3.E7.B3.BB)
    - [4.7.2 安装推荐的新的软件包](https://zh.opensuse.org/SDB:Zypper_用法#.E5.AE.89.E8.A3.85.E6.8E.A8.E8.8D.90.E7.9A.84.E6.96.B0.E7.9A.84.E8.BD.AF.E4.BB.B6.E5.8C.85)
    - [4.7.3 检查进程](https://zh.opensuse.org/SDB:Zypper_用法#.E6.A3.80.E6.9F.A5.E8.BF.9B.E7.A8.8B)

- - [4.8 更新管理](https://zh.opensuse.org/SDB:Zypper_用法#.E6.9B.B4.E6.96.B0.E7.AE.A1.E7.90.86)

- - - [4.8.1 列出所需的补丁](https://zh.opensuse.org/SDB:Zypper_用法#.E5.88.97.E5.87.BA.E6.89.80.E9.9C.80.E7.9A.84.E8.A1.A5.E4.B8.81)
    - [4.8.2 安装补丁](https://zh.opensuse.org/SDB:Zypper_用法#.E5.AE.89.E8.A3.85.E8.A1.A5.E4.B8.81)
    - [4.8.3 列出全部补丁](https://zh.opensuse.org/SDB:Zypper_用法#.E5.88.97.E5.87.BA.E5.85.A8.E9.83.A8.E8.A1.A5.E4.B8.81)
    - [4.8.4 检查补丁](https://zh.opensuse.org/SDB:Zypper_用法#.E6.A3.80.E6.9F.A5.E8.A1.A5.E4.B8.81)
    - [4.8.5 获取补丁信息](https://zh.opensuse.org/SDB:Zypper_用法#.E8.8E.B7.E5.8F.96.E8.A1.A5.E4.B8.81.E4.BF.A1.E6.81.AF)
    - [4.8.6 软件包更新](https://zh.opensuse.org/SDB:Zypper_用法#.E8.BD.AF.E4.BB.B6.E5.8C.85.E6.9B.B4.E6.96.B0)
    - [4.8.7 发行版升级](https://zh.opensuse.org/SDB:Zypper_用法#.E5.8F.91.E8.A1.8C.E7.89.88.E5.8D.87.E7.BA.A7)

- [5 在脚本和程序里使用 zypper](https://zh.opensuse.org/SDB:Zypper_用法#.E5.9C.A8.E8.84.9A.E6.9C.AC.E5.92.8C.E7.A8.8B.E5.BA.8F.E9.87.8C.E4.BD.BF.E7.94.A8_zypper)

- - [5.1 无交互模式](https://zh.opensuse.org/SDB:Zypper_用法#.E6.97.A0.E4.BA.A4.E4.BA.92.E6.A8.A1.E5.BC.8F)
  - [5.2 无 GPG 检查模式](https://zh.opensuse.org/SDB:Zypper_用法#.E6.97.A0_GPG_.E6.A3.80.E6.9F.A5.E6.A8.A1.E5.BC.8F)
  - [5.3 自动接受许可](https://zh.opensuse.org/SDB:Zypper_用法#.E8.87.AA.E5.8A.A8.E6.8E.A5.E5.8F.97.E8.AE.B8.E5.8F.AF)
  - [5.4 安静输出](https://zh.opensuse.org/SDB:Zypper_用法#.E5.AE.89.E9.9D.99.E8.BE.93.E5.87.BA)
  - [5.5 XML 输出](https://zh.opensuse.org/SDB:Zypper_用法#XML_.E8.BE.93.E5.87.BA)
  - [5.6 提示](https://zh.opensuse.org/SDB:Zypper_用法#.E6.8F.90.E7.A4.BA)

- - - [5.6.1 GPG 相关提示](https://zh.opensuse.org/SDB:Zypper_用法#GPG_.E7.9B.B8.E5.85.B3.E6.8F.90.E7.A4.BA)
    - [5.6.2 其他提示](https://zh.opensuse.org/SDB:Zypper_用法#.E5.85.B6.E4.BB.96.E6.8F.90.E7.A4.BA)

- [6 与 Rug 的兼容](https://zh.opensuse.org/SDB:Zypper_用法#.E4.B8.8E_Rug_.E7.9A.84.E5.85.BC.E5.AE.B9)
- [7 参阅](https://zh.opensuse.org/SDB:Zypper_用法#.E5.8F.82.E9.98.85)

**快速参考**

下列是些常用的命令：

zypper			# 列出可用的全局选项和命令 zypper help search	# 列出 search 命令的帮助 zypper lp		# 列出需要的补丁更新 zypper patch		# 安装所需的补丁 zypper se sqlite	# 搜索 sqlite 软件 zypper rm sqlite2	# 删除 sqlite2 软件 zypper in sqlite3	# 安装 sqlite3 软件 zypper in yast*		# 安装所有符合 yast* 名称的软件 zypper up		# 更新所有软件包到可用的新版本

**备忘单**

这里是本页内容的备忘单，也包含了一些手册页里的内容。建议您了解本页内容之后再使用。

| 第一页（A4 大小）     |
| --------------------- |
| 第二页（A4 大小）     |
| 第一页（letter 大小） |
| 第二页（letter 大小） |

------

**常规用法**

zypper 常规语法：

\# zypper [全局选项] 命令 [命令选项] [参数] ...

方括号部分并非必需，因此最简单的形式就是 zypper 后跟一个[命令](https://zh.opensuse.org/SDB:Zypper_用法#.E5.91.BD.E4.BB.A4)(有时也称为子命令，因为zypper是一个集合了多种功能的一个综合统筹程序，所以运行时常需要提供子命令来指定具体执行哪种行为)。例如，想要安装所需的补丁可执行并指定使用patch子命令：

\# zypper patch

您也可以选择一个或多个[全局选项](https://zh.opensuse.org/SDB:Zypper_用法#.E5.85.A8.E5.B1.80.E9.80.89.E9.A1.B9)（有时也称为开关，常以"-"或“--”开头，用于调整执行程序的行为），[全局选项](https://zh.opensuse.org/SDB:Zypper_用法#.E5.85.A8.E5.B1.80.E9.80.89.E9.A1.B9)是在[命令](https://zh.opensuse.org/SDB:Zypper_用法#.E5.91.BD.E4.BB.A4)之前输入，如（执行各种命令都不用询问用户，此处以patch命令做演示）：

\# zypper --non-interactive patch

命令选项跟在命令后面是针对特定命令使用的选项，如（安装补丁命令并自动同意所有的协议）：

\# zypper patch --auto-agree-with-licenses

一些命令需要一或多个参数，如（安装或更新 mplayer）：

\# zypper install mplayer

一些选项也需要参数，如（搜索匹配项 pattern）：

\# zypper search -t pattern

综合以上所列，如（列出过程的详细信息 的 安装从 Factory 供应源提供的 mplayer 和 amarok，）：

\# zypper -v install --repo factory mplayer amarok

**答复提示**

当 zypper 遇到提示询问信息需等待用户答复时，会于提示符旁在括号里列出默认备择项。直接按 Enter 选择默认答复（默认答复选中显示，非 ASCII 字符则不然）。一些提示询问信息也包含帮助信息，这种时候就会有一个问号“?”。如欲让 zypper 直接使用默认答复不非询问用户，使用 [--non-interactive](https://zh.opensuse.org/SDB:Zypper_用法#.E6.97.A0.E4.BA.A4.E4.BA.92.E6.A8.A1.E5.BC.8F) 全局选项。

------

**词汇表**

- **repository** 供应源 - 包含软件包和各种软件包信息（元数据 metadata）的本地或远端目录。供应源以前称为安装源、软件源、YaST 源或源。
- **alias** 别名 - 供应源别名是供应源名字的简短代号，用于与供应源相关的命令和选项中，如 removerepo 或 --repo。
- **package** 软件包 - 如 RPM 软件包、源码包等。补丁 (patch)、模式 (pattern) 和成品 (product) 通常也认为是别种类型的软件包。
- **patch** 补丁 - 用于安装更新（通常是修复缺陷）的一个或一组软件包。
- **pattern** 模式 - 同一模式的一组软件包。例如 *Http Server* 模式界定了依赖关系，当安装该模式时，运行或管理一个 http 服务所需的全部的软件包都将一起安装。
- **product** 成品 - 一整个成品，如 openSUSE 11.1。

------

**命令**

zypper 提供的一系列命令可以归类成以下类别：

- **供应源管理**

refresh, repos, addrepo, removerepo, modifyrepo, namerepo

refresh-services, services, addservice, removeservice, modifyservice

- **软件包管理**

install, remove, source-install

- **更新管理**

patch, list-patches, patch-check, patches, update, list-updates, dist-upgrade

- **查询**

search, info, what-provides, list-updates, patch-check, patches, packages, patterns, products

- **锁定**

locks, addlock, removelock, cleanlocks

- **工具**

verify, install-new-recommends

- **其他**

help, licenses, versioncmp, targetos

**帮助信息**

首先，应该先了解如何获取帮助信息。如欲查看基本的帮助信息（命令和全局选项列表）只需不带任何选项或参数输入 zypper。如欲查看特定的命令，输入：

\# zypper help [command]

获取特定命令的具体帮助信息，输入：

\# zypper [command] --help

可以使用 -h 替代 --help。

**供应源管理**

您可以以 zypper lr 里的序号、别名或 URI（Uniform Resource Identifier，统一资源标志符）代指相应的供应源。使用序号时，须先 zypper lr 确认一番，因为若变更了供应源这些序号也会变更。

**列出设定的供应源**

**repos** 或 **lr**

示例：

$ zypper lr

\# | Alias                 | Name                  | Enabled | Refresh --+-----------------------+-----------------------+---------+-------- 1 | packman               | Packman 11.1          | Yes     | No 2 | fate                  | fate                  | No      | No 3 | openSUSE-11.1-Updates | Updates for 11.1      | Yes     | Yes 4 | repo-oss              | openSUSE-11.1-Oss     | Yes     | No 5 | repo-non-oss          | openSUSE-11.1-Non-Oss | Yes     | No 6 | repo-debug            | openSUSE-11.1-Debug   | No      | No

$ zypper lr 5 4

Alias          : openSUSE-11.3-Non-Oss Name           : openSUSE-11.3-Non-Oss URI            : http://download.opensuse.org/distribution/11.3/repo/non-oss/ Enabled        : Yes Priority       : 99 Auto-refresh   : Off Keep Packages  : Off Type           : yast2                                                        GPG Check      : On GPG Key URI    :  Path Prefix    : / Parent Service :  MD Cache Path  : /var/cache/zypp/raw/openSUSE-11.3-Non-Oss Alias          : openSUSE-11.3-Oss Name           : openSUSE-11.3-Oss URI            : http://download.opensuse.org/distribution/11.3/repo/oss/ Enabled        : Yes Priority       : 99 Auto-refresh   : Off Keep Packages  : Off  Type           : yast2 GPG Check      : On GPG Key URI    :    Path Prefix    : / Parent Service : MD Cache Path  : /var/cache/zypp/raw/openSUSE-11.3-Oss

其他例子：

zypper lr -u       # 列出供应源 URI zypper lr -d       # 列出供应源的其他数项属性 zypper lr -P       # 列出供应源优先级并依此排序 zypper lr -e my    # 导出全部的供应源设定信息至文件 my.repo

**添加供应源**

**addrepo** 或 **ar**

安装软件包之前至少得有一个供应源。可以使用 addrepo 命令添加供应源：

示例：

\# zypper ar http://download.videolan.org/pub/vlc/SuSE/11.1 vlc

Adding repository 'vlc' [done] Repository 'vlc' successfully added Enabled: Yes Autorefresh: No URI: http://download.videolan.org/pub/vlc/SuSE/11.1

其他例子：

zypper ar http://download.opensuse.org/repositories/X11:/XGL/openSUSE_11.1/X11:XGL.repo  # 通过 .repo 文件 zypper ar -c ftp://some.download.site myalias   # 添加之前试探供应源 zypper ar my/dir/with/rpms local                # 添加含 RPM 文件的本地目录为供应源

参见 [Libzypp](http://en.opensuse.org/Portal:Libzypp) 了解支持的媒介与 URI。

**刷新供应源**

**refresh** 或 **ref**

添加了供应源之后或供应源不新之时，就需要刷新供应源。即下载软件包的元数据 (metadata)，并将数据预处理为 .solv 缓存以便快速读取。

\# zypper refresh

Downloading repository 'Packman 11.1' metadata [done] Building repository 'Packman 11.1' cache [done] Downloading repository 'Updates for 11.1' metadata [done] Building repository 'Updates for 11.1' cache [done] Repository 'openSUSE-11.1-Oss' is up to date. All repositories have been refreshed.

若是供应源已启用自动刷新，您就不需操心了，当需要的时候他会自动进行。然而，有的用户偏好控制刷新的进行（此举可以避免当您只想看看 'zypper info krusader' 时却须等待刷新的完成），故而禁用了自动刷新。更多细节参阅 man zypper。

其他例子：

zypper ref packman main  # 您也可以只指定特定的供应源刷新 zypper ref -f upd        # 强制 upd 供应源刷新

**移除供应源**

**removerepo** 或 **rr**

\# zypper rr vlc 1 23 foo

Repository 23 not found by alias, number or URI. Repository foo not found by alias, number or URI. Removing repository 'repo-debug' [done] Repository 'repo-debug' has been removed. Removing repository 'vlc' [done] Repository 'vlc' has been removed.

**调整供应源**

**modifyrepo** 或 **mr**

禁用序号为 6 的供应源：

\# zypper mr -d 6

Repository 'repo-non-oss' has been sucessfully disabled.

启用 packman 供应源的自动刷新并缓存 RPM 文件，再设置其优先级为 70:

\# zypper mr -rk -p 70 packman

Autorefresh has been enabled for repository 'packman'. RPM files caching has been enabled for repository 'packman'. Repository 'packman' priority has been set to 70.

禁用所有供应源的 RPM 文件缓存：

\# zypper mr -Ka

Nothing to change for repository 'local'. RPM files caching has been disabled for repository 'packman'. Nothing to change for repository 'fate'. Nothing to change for repository 'upd'. Nothing to change for repository 'repo-oss'. Nothing to change for repository 'repo-non-oss'.

启用所有供应源的 RPM 文件缓存：

\# zypper mr -ka

RPM files caching has been enabled for repository 'repo-non-oss'. RPM files caching has been enabled for repository 'Main Repository (OSS)'. RPM files caching has been enabled for repository 'Main Repository (NON-OSS)'. RPM files caching has been enabled for repository 'openSUSE-11.1-Updates'.

**重命名供应源**

**renamerepo** 或 **nr**

\# zypper nr 3 upd

Repository 'openSUSE-11.1-Updates' renamed to 'upd'.

当前该命令只能更改供应源的别名 (alias)，若您想更改显示的名称，请参阅 mr 命令。

取一个简短的别名可以方便地用于命令参数或 --repo 选项中。使用别名较之序号安全，序号可能变化而使您出错，较之 URI 简单，URI 太长复制黏贴不便。

**导出导入供应源**

**repos --export** 或 **lr -e**

您可以导出您的供应源列表为一个文件，并于稍后或另一计算机上导入。

\# zypper lr --export backups/repos/foo.repo

\# zypper ar backups/repos/foo.repo

**服务管理**

<待撰>

**软件包管理**

**选择软件包**

有数种方式选择要安装或移除的软件包。

- 根据名称

zypper in eclipse

zypper in qt

- 根据名称、构架或版本

zypper in 'zypper<0.12.10'

zypper in zypper.i586=0.12.11

- 根据确切的软件包名称 (--name)

zypper in -n ftp

- 根据确切的软件包名称和供应源 (implies --name)

zypper in factory:zypper

- 根据含通配符的软件包名称

zypper in yast*ftp*

- 根据指定一个 .rpm 软件包文件来安装

**安装软件包**

**install** 或 **in**

您可以根据名称来安装软件包：

\# zypper install git

Reading installed packages... The following NEW packages are going to be installed:  subversion-perl sqlite3 perl-DBD-SQLite git-svn git-cvs git  Overall download size: 1.1 M. After the operation, additional 4.6 M will be used. Continue? [YES/no]: Downloading package subversion-perl-1.5.0-96.1.x86_64 (1/6), 950.0 K (4.1 M unpacked) Downloading: subversion-perl-1.5.0-96.1.x86_64.rpm [done] Installing: subversion-perl-1.5.0-96.1 [done] Downloading package sqlite3-3.5.7-17.1.x86_64 (2/6), 30.0 K (40.0 K unpacked) Downloading: sqlite3-3.5.7-17.1.x86_64.rpm [done] Installing: sqlite3-3.5.7-17.1 [done] Downloading package perl-DBD-SQLite-1.14-41.1.x86_64 (3/6), 44.0 K (103.0 K unpacked) Downloading: perl-DBD-SQLite-1.14-41.1.x86_64.rpm [done] Installing: perl-DBD-SQLite-1.14-41.1 [done] Downloading package git-svn-1.5.4.5-26.1.x86_64 (4/6), 66.0 K (195.0 K unpacked) Downloading: git-svn-1.5.4.5-26.1.x86_64.rpm [done] Installing: git-svn-1.5.4.5-26.1 [done] Downloading package git-cvs-1.5.4.5-26.1.x86_64 (5/6), 63.0 K (205.0 K unpacked) Downloading: git-cvs-1.5.4.5-26.1.x86_64.rpm [done] Installing: git-cvs-1.5.4.5-26.1 [done] Downloading package git-1.5.4.5-26.1.x86_64 (6/6), 10.0 K (3.0 K unpacked) Downloading: git-1.5.4.5-26.1.x86_64.rpm [done] Installing: git-1.5.4.5-26.1 [done]

或据其所提供的功能：

\# zypper in MozillaFirefox \< 3

Reading installed packages... 'MozillaFirefox' providing 'MozillaFirefox<3' is already installed. Nothing to do.

$ zypper in MozillaFirefox \>= 3

Reading installed packages... The following packages are going to be upgraded:  mozilla-xulrunner190-translations MozillaFirefox mozilla-xulrunner190-gnomevfs mozilla-xulrunner190 MozillaFirefox-translations  The following package is going to be REMOVED:  mozilla-xulrunner190-lang  Overall download size: 11.0 M. After the operation, 12.9 M will be freed. Continue? [Y/n/p/?]:

$ zypper in 'libqtiff.so()(64bit)'

Reading installed packages... 'libqt4-x11' providing 'libqtiff.so()(64bit)' is already installed. Nothing to do.

其他例子：

zypper in yast*                     # 安装全部 yast 模块 zypper in -t pattern lamp_server    # 安装 lamp_server 模式（LAMP server 所需的软件包） zypper in vim -emacs                # 安装 vim 并移除 emacs zypper in amarok packman:libxine1   # 安装 packman 供应源的 libxine1 和任何供应源的 amarok zypper in bitchx-1.1-81.x86_64.rpm  # 安装当前目录的 bitchx RPM 软件包 zypper in -f subversion             # 强制重新安装 subversion

**移除软件包**

**remove** 或 **rm**

remove 命令与 install 命令相似，除了其相反的作用。

\# zypper remove sqlite

Reading installed packages... The following packages are going to be REMOVED:  sqlite3 perl-DBD-SQLite git-cvs git  After the operation, 351.0 K will be freed. Continue? [YES/no]: n

**源码包和构建依赖**

**source-install** 或 **si**

\# zypper si zypper

Reading installed packages... The following NEW packages are going to be installed:  libzypp-devel libsatsolver-devel  The following source package is going to be installed:  zypper  Overall download size: 1.5 M. After the operation, additional 6.7 M will be used. Continue? [YES/no]:

您也可以只安装源码包或构建依赖：

zypper si -D zypper    # 只安装源码包 zypper si -d zypper    # 只安装构建依赖

**更新软件包**

**update** 或 **up**

以下命令更新软件包至新版本。参阅[更新管理](https://zh.opensuse.org/SDB:Zypper_用法#.E6.9B.B4.E6.96.B0.E7.AE.A1.E7.90.86)了解详情。

zypper up                           # 更新全部已安装的软件包至尽可能新的版本 zypper up libzypp zypper            # 更新 libzypp 和 zypper zypper in sqlite3                   # 安装或更新 sqlite3

**查询**

**搜寻软件包**

**search** 或 **se**

search 命令默认搜寻匹配的任何类型、状态或供应源的软件包（大小写不敏感）：

\# zypper se sqlite

Reading installed packages... S | Name                     | Summary                                                        | Type --+--------------------------+----------------------------------------------------------------+--------  | libapr-util1-dbd-sqlite3 | DBD driver for SQLite 3                                        | package i | libgda-3_0-sqlite        | Sqlite Provider for GNU Data Access (GDA)                      | package  | libqt4-sql-sqlite        | Qt 4 sqlite plugin                                             | package i | libsqlite3-0             | Shared libraries for the Embeddable SQL Database Engine        | package  | libsqlite3-0-32bit       | Shared libraries for the Embeddable SQL Database Engine        | package  | mediatomb-sqlite         | UPnP AV MediaServer                                            | package i | mono-data-sqlite         | Database connectivity for Mono                                 | package  | pdns-backend-sqlite2     | SQLite 2 backend for pdns                                      | package  | pdns-backend-sqlite3     | SQLite 3 backend for pdns                                      | package i | perl-DBD-SQLite          | The DBD::SQLite is a self contained RDBMS in a DBI driver      | package i | php5-sqlite              | PHP5 Extension Module                                          | package  | python-sqlite2           | Python bindings for sqlite 2                                   | package  | qt3-sqlite               | SQLite Database Plug-In for Qt                                 | package  | rekall-sqlite            | Rekall sqlite Database Backend                                 | package  | rubygem-sqlite3          | A Ruby interface for the SQLite3 database engine               | package i | sqlite2                  | Embeddable SQL Database Engine                                 | package  | sqlite2-32bit            | Embeddable SQL Database Engine                                 | package  | sqlite2-devel            | Embeddable SQL Database Engine                                 | package i | sqlite3                  | Embeddable SQL Database Engine                                 | package  | sqlite3-devel            | Embeddable SQL Database Engine                                 | package  | sqlite3-tcl              | Tcl binding for SQLite                                         | package  | tntdb1-sqlite            | Tntdb is a c++-class-library for easy database-access - sqlite | package  | ulogd-sqlite             | SQLite output plugin for ulogd                                 | package

首栏的 i 标示已经安装的软件包。如欲查看匹配软件包的全部可选版本，使用 --details 或 -s 选项：

\# zypper search -s --match-exact virtualbox-ose

Reading installed packages... S | Name           | Type    | Version    | Arch   | Repository --+----------------+---------+------------+--------+------------------------------------ v | virtualbox-ose | package | 1.6.2-2.1  | x86_64 | VirtualBox OSE i | virtualbox-ose | package | 1.5.6-33.1 | x86_64 | openSUSE-11.1-Oss v | virtualbox-ose | package | 1.5.6-20.5 | x86_64 | VirtualBox OSE ( v | virtualbox-ose | package | 1.6.2-2.1  | i586   | VirtualBox OSE v | virtualbox-ose | package | 1.5.6-33.1 | i586   | openSUSE-11.1-Oss v | virtualbox-ose | package | 1.5.6-20.3 | i586   | VirtualBox OSE

此处首栏的 v 标示已安装了此软件包的其他版本。

其他例子：

zypper se -dC --match-words RSI   # 搜寻包括摘要和描述中的匹配 RSI 缩写的项 zypper se 'yast*'                 # 搜寻所有含 yast 字符的软件包（注意 shell 的表达，不确定就加引号） zypper se -r packman              # 列出所有 packman 供应源的软件包 zypper se -i sqlite               # 列出所有已安装的其名字包含 sqlite 的软件包 zypper se -t pattern -r repo-oss  # 列出所有 repo-oss 供应源的模式 (pattern) zypper se -t product              # 列出所有可选的成品 (product)

**获取软件包的信息**

**info** 或 **if**

显示名为 amarok 的软件包的具体信息：

\# zypper info amarok

Reading installed packages...  Information for package amarok: Repository: Packman 11.1 Name: amarok Version: 1.4.9.1-103.pm.1 Arch: x86_64 Vendor: packman.links2linux.de Installed: Yes Status: up-to-date Installed Size: 12.1 M Summary: Media Player for KDE Description: Amarok is a media player for all kinds of media, supported by aRts, GStreamer or Xine (depending on the packages you install). This includes MP3, Ogg Vorbis, audio CDs and streams. It also supports audio effects of all kinds that are provided by aRts. Playlists can be stored in .m3u or .pls files. Amarok also provides audio file collection management, by using either an embedded sqlite3, a MySQL or a PostgreSQL database.

其他例子：

zypper info -t patch MozillaFirefox    # 显示 MozillaFirefox 补丁的信息 zypper patch-info MozillaFirefox       # 同上 zypper info -t pattern lamp_server     # 显示 lamp_server 模式的信息 zypper info -t product openSUSE-FTP    # 显示特定成品的信息

**依赖关系**

**what-provides** 或 **wp**

列出所有指定软件的供应方：

\# zypper wp firefox

Reading installed packages... S | Name           | Type    | Version     | Arch   | Repository --+----------------+---------+-------------+--------+------------------ i | MozillaFirefox | package | 3.0-0.1     | x86_64 | Updates for 11.1 v | MozillaFirefox | package | 2.9.95-25.1 | x86_64 | openSUSE-11.1-Oss v | MozillaFirefox | package | 3.0-0.1     | i586   | Updates for 11.1 v | MozillaFirefox | package | 2.9.95-25.1 | i586   | openSUSE-11.1-Oss

此命令与 rpm -q --whatprovides firefox 相似，但 rpm 只能查询 RPM 数据库（只包含已装软件的信息）。而 zypper 能提供其他供应源的信息，并不仅仅只是已安装的。

**其他查询**

命令 **patches**、**packages**、**patterns** 和 **products** 相似于 **search -s -t [patch,package,pattern,product]**，但这些命令还显示相应类型的软件包的其他信息。例如 patches 还显示了补丁的状态（需求、安全性、不适用情况）。

命令 **list-updates** 和 **patch-check** 参见[更新管理](https://zh.opensuse.org/SDB:Zypper_用法#.E6.9B.B4.E6.96.B0.E7.AE.A1.E7.90.86)。

**软件包锁定**

**locks** 或 **ll** **addlock** 或 **al** **removelock** 或 **rl** **cleanlocks** 或 **cl**

软件包锁定可以防止软件包的变更。应用了有效的锁定的软件包无法变更其安装状态，即已安装的软件包无法移除或升级，未安装的软件包无法安装。

如欲锁定所有以 yast2 开首的软件包，执行：

\# zypper al 'yast2*'

Reading installed packages... Specified lock has been successfully added.

再次提醒 shell 的表达，若 yast2* 有可能匹配当前目录（工作目录）的文件或目录时，请使用引号。

列出当前有效的锁定：

\# zypper ll

\# | Name             | Type    | Repository --+------------------+---------+----------- 1 | libpoppler3      | package | (any) 2 | libpoppler-glib3 | package | (any) 3 | yast*            | package | (any)

移除锁定：

\# zypper rl yast2-packager

Reading installed packages... The following query locks some of the objects you want to unlock: type: package match_type: glob case_sensitive: on solvable_name: yast2* Do you want remove this lock? [YES/no]: y Lock count has been succesfully decreased by: 1

其他例子：

zypper al zypper                   # 锁定 zypper 软件包（精确匹配） zypper al -r repo-oss virtualbox*  # 限制 repo-oss 供应源（允许安装其他供应源的软件包） zypper rl 3                        # 移除对应序号的锁定

您也可以直接编辑 [locks file](http://en.opensuse.org/openSUSE:Libzypp_locksfile) 设置锁定。

**工具**

**验证依赖关系**

**verify** 或 **ve**

或许您会因软件包依赖关系将系统搞得一团糟。若是一些应用程序提示缺少某些东西而无法启动，这些东西正是 zypper 可以检查的：

$ rpm -e --nodeps mozilla-xulrunner190

$ firefox

Could not find compatible GRE between version 1.9.0 and 1.9.0.

\# zypper ve

Reading installed packages... Some of the dependencies of installed packages are broken. In order to fix these dependencies, the following actions need to be taken: The following NEW package is going to be installed:  mozilla-xulrunner190  Overall download size: 6.5 M. After the operation, additional 23.5 M will be used. Continue? [YES/no]: y

**安装推荐的新的软件包**

**install-new-recommends** 或 **inr**

此命令查找并安装已安装的软件包的推荐的新添加的软件包。这是一种简单的方式来获取软件的新语言包或是新添加的硬件的驱动。

\# zypper inr

Reading installed packages... The following NEW packages are going to be installed:  kdebase4-openSUSE-lang bundle-lang-common-cs  Overall download size: 534.0 K. After the operation, additional 1.9 M will be used. Continue? [YES/no]:

**检查进程**

**ps**

此命令显示使用被最近的更新或移除操作所删除的文件的进程。

有些正运行的程序使用被最近的更新所删除的文件，您不妨重启其中的一些。执行 zypper ps 列出这些程序。

\# zypper ps

The following running processes use deleted files: PID   | PPID  | UID  | Login | Command       | Service | Files ------+-------+------+-------+---------------+---------+-------------------------------- 10604 | 10603 | 1000 | geeko | chrome        |         | /usr/share/mime/mime.cache      |       |      |       |               |         | /usr/share/mime/mime.cache 15304 | 3195  | 1000 | geeko | kio_thumbnail |         | /var/tmp/kdecache-geeko/ksycoca4      |       |      |       |               |         | /var/tmp/kdecache-geeko/ksycoca4 You may wish to restart these processes. See 'man zypper' for information about the meaning of values in the above table.

**更新管理**

有两种方式更新您的系统，一种是补丁方式，一种是软件包方式。

补丁方式尤其适合使用稳定发行版的用户，通过在线的 update 供应源发布的补丁更新系统。安装或升级系统时 update 供应源就已默认添加了，您也可以通过 **YaST 控制中心**的**在线更新设置**添加，或使用 [zypper](https://zh.opensuse.org/SDB:Zypper_用法#.E6.B7.BB.E5.8A.A0.E4.BE.9B.E5.BA.94.E6.BA.90) 手动添加。这是 [openSUSE update 供应源](http://download.opensuse.org/update/)列表。

YaST 中的相应部分是[在线更新](http://en.opensuse.org/YaST_Online_Update)模块。

软件包方式将在[软件包更新](https://zh.opensuse.org/SDB:Zypper_用法#.E8.BD.AF.E4.BB.B6.E5.8C.85.E6.9B.B4.E6.96.B0)中描述，这种方式以任何供应源中的新版本提供一般的软件包更新服务。

**列出所需的补丁**

**list-patches** 或 **lp**

列出所有所需的补丁，执行：

\# zypper lp

Reading installed packages... Patches Repository       | Name               | Version | Category    | Status -----------------+--------------------+---------+-------------+------- Updates for 11.1 | KDE4-fixes         | 38      | recommended | Needed Updates for 11.1 | MozillaFirefox     | 50      | recommended | Needed Updates for 11.1 | NetworkManager-kde | 49      | recommended | Needed

有时仅列出影响软件包管理的更新，这是由于这些应首先更新，更新后，其余的更新才会列出。

此命令等效于旧版 zypper 的 zypper up -t patch。列出全部的更新，使用：

\# zypper lu

**安装补丁**

**patch**

安装所需的补丁，执行：

\# zypper patch

Reading installed packages... The following packages are going to be upgraded:  NetworkManager-kde mozilla-nss mozilla-nspr kde4-korganizer  The following NEW patches are going to be installed:  NetworkManager-kde MozillaFirefox KDE4-fixes  Overall download size: 2.9 M. After the operation, additional 283.0 K will be used. Continue? [YES/no]:

**列出全部补丁**

**patches**

list-updates 命令仅列出所需的补丁，如欲列出全部的补丁，使用：

\# zypper patches

Reading installed packages... Catalog          | Name               | Version | Category    | Status -----------------+--------------------+---------+-------------+--------------- Updates for 11.1 | KDE4-fixes         | 38      | recommended | Installed Updates for 11.1 | MozillaFirefox     | 50      | recommended | Installed Updates for 11.1 | NetworkManager-kde | 49      | recommended | Installed Updates for 11.1 | autoyast2          | 37      | recommended | Installed Updates for 11.1 | courier-authlib    | 42      | security    | Not Applicable Updates for 11.1 | insserv            | 47      | recommended | Installed Updates for 11.1 | opera              | 43      | security    | Installed

**检查补丁**

**patch-check**

此命令检查是否有可用的重要的补丁，并反馈补丁数：

\# zypper pchk

Reading installed packages... 0 patches needed (0 security patches)

**获取补丁信息**

**patch-info**

**info -t patch**

\# zypper info -t patch MozillaFirefox

Reading installed packages...  Information for patch MozillaFirefox: Name: MozillaFirefox Version: 50 Arch: noarch Vendor: maint-coord@suse.de Status: Installed Category: recommended Created On: Thu 01 Jan 1970 01:00:00 AM CET Reboot Required: No Package Manager Restart Required: No Interactive: No Summary: Mozilla Firefox 3.0 Description: This patch updates Mozilla Firefox to the final 3.0 version. The dependend libraries mozilla-xulrunner190, mozilla-nspr and mozilla-nss were also brought to their release version.

**软件包更新**

**list-updates** 或 **lu**

**update** 或 **up**

如欲简单地以新版本更新所安装的软件包，执行：

\# zypper up

您可以以此获取可用的更新列表：

\# zypper lu

以上命令仅列出或更新其更新无依赖问题的软件包。如欲获取原始的所有的更新列表，而非仅仅已安装的，执行：

\# zypper lu -a

这将列出所有的候选更新，无论是否可装，无论是否需要用户介入解决一些问题。

**发行版升级**

**dist-upgrade** 或 **dup**

此命令使用发行版升级算法，处理软件包分裂 (package splits)、无维护软件包以及类似的其他软件包。使用此命令可以升级到新的发行版。

\# zypper dup

建议在发行版升级过程中仅启用您所欲安装的发行版的主要的供应源和一些您所用的重要的供应源（若其版本对应于主要的供应源则更佳）。您也可以先禁用旧的供应源 zypper mr -da，添加新的供应源 zypper ar，再 zypper dup 升级。您可以用 --repo 选项指定所使用的供应源 zypper dup -r repo1 -r repo2 ...。

问：是否 zypper up 仅更新在其供应源中有新版本的软件包，而 zypper dup 将升级一切，无论其软件包的来源。

答：zypper up 会更新软件包到新版本，但不会变更其供应源（注，当前的整个构建服务具有相同的供应源）。zypper dup 尝试将您当前的软件包同步为您所启用的（所有）供应源中的版本，这意味着可能将比供应源里新的软件包降级下来。

04：好像回答的与实际相反。

------

**在脚本和程序里使用 zypper**

zypper 支持多个全局选项故而适合在自动化流程如脚本里使用。并且，在自动化流程里使用 zypper 时，可以检查列在 zypper 手册页的多个不同的退出代码。

**无交互模式**

**--non-interactive**

此模式下 zypper 不会询问用户，而以默认答复代之。使用此选项可以保证 zypper 不会停滞在等待用户输入的环节，或陷入死循环。

例如，不需人工确认自动更新系统：

\# zypper --non-interactive update

此命令执行更新而无需用户确认，略过全部需要额外确认的交互性补丁，并自动答复任何提示。

**无 GPG 检查模式**

**--no-gpg-checks**

若使用此选项，zypper 将总是选择继续，即使 GPG (GNU Privacy Guard) 检查不通过，如本该有签名却没有签名的源文件、有签名但没能通过 GPG 检查的文件等等。

**自动接受许可**

**--auto-agree-with-licenses**

这是安装、移除和更新命令的特殊选项。使用此选项，即声明接受许可协议，而 zypper 遇到许可确认时自动接受答复。这对于在多台机器上（以自动化流程）安装相同的软件包的已阅协议的用户很有用。

**安静输出**

**--quiet**

避免输出过多的信息，诸如程序信息之类的，只输出操作结果和错误信息。

**XML 输出**

**--xmlout**

此选项使 zypper 以 XML（eXtensible Markup Language 可扩展标记语言）输出信息。这允许使用 zypper 的脚本、图形化前端或其他类型的程序以良好定义的标准的方式解析 zypper 输出。zypper XML 输出的 RNC 架构可于[此](http://svn.opensuse.org/svn/zypp/trunk/zypper/src/output/xmlout.rnc)找到，其位于 /usr/share/zypper/xml/xmlout.rnc。

当前并非所有的输出都是 XML 格式（但大部分是），但目标是让所有的输出统一为 XML 格式。

**提示**

以下是一份完整的列表，在此情况下 zypper 需要用在无交互模式下的用户交互答复。这儿提到的所有的附加选项都较 --non-interactive 有高的优先级，所以若是使用，将自动选用默认答复，即使没有使用 --non-interactive。（04：不知所云）

**GPG 相关提示**

若使用了 --no-gpg-checks，将显示信息或向标准错误 (stderr) 写入警告并记录。

| 提示信息                                                     | 默认答复 | 使用 --no-gpgp-checks 选项 | 备注                             |
| ------------------------------------------------------------ | -------- | -------------------------- | -------------------------------- |
| 是否接受未签名文件                                           | N        | Y                          |                                  |
| 是否接受新钥密（reject 拒绝、trust temporarily 临时接受、trust always 接受） | R        | R                          | 只能在交互模式下接受或导入新钥密 |
| 是否接受未知的钥密                                           | N        | Y                          |                                  |
| 签名文件验证失败，是否继续                                   | N        | Y                          |                                  |
| 文件无 digest，是否继续                                      | N        | Y                          |                                  |
| 是否接受未知 digest                                          | N        | Y                          |                                  |

**其他提示**

| 提示信息                                              | 默认答复 | 其他答复                                 | 备注                                                         |
| ----------------------------------------------------- | -------- | ---------------------------------------- | ------------------------------------------------------------ |
| 执行安装/移除/更新                                    | Y        |                                          | --no-confirm 选项可用于安装/移除/更新命令，即使没有全局的 --non-interactive 选项 |
| 是否接受第三方许可协议                                | N        | Y 若使用 --auto-agree-with-licenses 选项 | 对于 zypper 更新命令 --skip-interactive option 选项可以用于排除安装列表中的交互性补丁（rug 命令的遗传） |
| 是否确认补丁信息                                      | Y        |                                          |                                                              |
| 安装/移除过程出错，Abort 放弃/Retry 重试/Ignore 忽略  | ABORT    |                                          | 这很棘手，今后将改进                                         |
| 下载软件包过程出错，Abort 放弃/Retry 重试/Ignore 忽略 | ABORT    |                                          | 这同样棘手，今后将某些程度上改进                             |
| 依赖冲突，#/s/r/c（解决方案序号/跳过/重试/取消）      | c        |                                          | 总是取消，需要用户介入解决                                   |
| 媒介变更请求                                          | ABORT    |                                          |                                                              |
| 是否移除有问题的锁定                                  | Y        |                                          |                                                              |

在 XML 输出中，提示 (prompt) 以  标签标记，包含 **id** 属性。[prompt.h](http://svn.opensuse.org/svn/zypp/trunk/zypper/src/output/prompt.h) 有所有已知的 id 细目，包括了 zypper 软件包里的文件 (/usr/include/zypper/prompt.h)。

------

**与 Rug 的兼容**

Zypper 的语法与 Rug 很接近，但命令和选项集就如输出和行为一样，与 Rug 开始分歧了。然而，Zypper 还是可以以兼容于 Rug 的模式来运行，也支持大多数 Rug 的命令。详细信息可以参阅 Zypper 手册页的 COMPATIBILITY WITH RUG 章节。

------

**参阅**

- [软件包管理](https://zh.opensuse.org/软件包管理)