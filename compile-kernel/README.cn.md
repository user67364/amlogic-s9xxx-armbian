# 内核编译和使用说明

查看英文说明 | [View English description](README.md)

此内核可用于 `Armbian` 和 `OpenWrt` 系统。例如 [amlogic-s9xxx-armbian](https://github.com/ophub/amlogic-s9xxx-armbian), [amlogic-s9xxx-openwrt](https://github.com/ophub/amlogic-s9xxx-openwrt), [flippy-openwrt-actions](https://github.com/ophub/flippy-openwrt-actions) 和 [unifreq/openwrt_packit](https://github.com/unifreq/openwrt_packit) 等项目。可以在编译固件时集成，也可以安装到已有的系统中使用。

## 内核编译说明

你可以根据需要对内核的配置进行调整，如添加驱动和补丁。也可以根据心情编译具有特殊意义的个性化签名内核，如 `5.10.95-happy-new-year`, `5.10.96-beijing-winter-olympics`, `5.10.99-valentines-day` 等等。

| 参数 | 含义 | 说明 |
| ---- | ---- | ---- |
| -d | Defaults | 使用默认配置 |
| -k | Kernel | 指定 [kernel](https://cdn.kernel.org/pub/linux/kernel/v5.x/) 名称，如 `-k 5.4.180` . 多个内核使用 `_` 进行连接，如 `-k 5.10.100_5.4.180` |
| -a | AutoKernel | 设置是否自动采用同系列最新版本内核。当为 `true` 时，将自动查找在 `-k` 中指定的内核如 `5.4.180` 的 `5.4` 同系列是否有更新的版本，如有 `5.4.180` 之后的最新版本时，将自动更换为最新版。设置为 `false` 时将编译指定版本内核。默认值：`true` |
| -n | CustomName | 设置内核自定义签名。默认值为 `-meson64-dev` ，生成的内核名称为 `5.4.180-meson64-dev` 。设置自定义签名时请勿包含空格。 |
| -r | Repo | 指定编译内核的源代码仓库。默认为 `unifreq` 。可选择 `kernel.org` 的源码和 `github.com` 的内核源代码仓库。例如 `-r kernel.org` 或 `-r unifreq` 等。当使用 `github.com` 的内核源代码仓库时，可设置参数格式为 `owner/repo@branch` 三项组合，参数中的所有者名称 `owner` 为必选参数，内核源代码仓库名称 `/repo` 和 仓库的分支名称 `@branch` 为可选参数。当仅指定所有者名称 `owner` 参数时，将自动匹配所有者的名称为 `linux-5.x.y` 格式且分支为 `main` 的内核源代码仓库。如果仓库名称或分支名称不同，请使用组合方式指定，如 `owner@branch` 或 `owner/repo` 或 `owner/repo@branch` |

- `sudo ./recompile -d` : 使用默认配置编译内核。
- `sudo ./recompile -d -k 5.4.180` : 使用默认配置，并通过 `-k` 进行指定需要编译的内核版本，多个版本同时编译时使用 `_` 进行连接。
- `sudo ./recompile -d -k 5.4.180 -a true` : 使用默认配置，并通过 `-a` 参数设置编译内核时，是否自动升级到同系列最新内核。
- `sudo ./recompile -d -k 5.4.180 -n -good-luck` : 使用默认配置，并通过 `-n` 参数设置内核自定义签名。
- `sudo ./recompile -d -k 5.4.180 -r kernel.org` : 使用默认配置，并通过 `-r` 参数设置内核源代码仓库。
- `sudo ./recompile -d -k 5.10.100_5.4.180 -a true -n -good-luck -r kernel.org` : 使用默认配置，并通过多个参数进行设置。

💡提示：推荐使用 `unifreq` 的 [5.4](https://github.com/unifreq/linux-5.4.y), [5.10](https://github.com/unifreq/linux-5.10.y), [5.15](https://github.com/unifreq/linux-5.15.y) 等仓库的内核源代码进行编译，他针对相关盒子添加了驱动和补丁。推荐使用 [tools/config](tools/config) 中的模板，已经根据相关盒子进行了预配置，可以在此基础上进行个性化定制。也可以使用 [kernel.org](https://cdn.kernel.org/pub/linux/kernel/v5.x/) 等其他内核源代码进行编译。

- ### 本地编译

1. 安装必要的软件包（脚本仅在 Ubuntu-20.04-x86_64 下做过测试）

```yaml
sudo apt-get update -y
sudo apt-get full-upgrade -y
sudo apt-get install -y $(curl -fsSL git.io/ubuntu-2004-server)
```

2. 克隆仓库到本地 `git clone --depth 1 https://github.com/ophub/amlogic-s9xxx-armbian.git`

3. 进入 `~/amlogic-s9xxx-armbian` 根目录，然后运行 `sudo ./recompile -d -k 5.4.180` 等指定参数命令即可编译内核。脚本会自动下载安装编译环境和内核源码并做好全部设置。打包好的内核文件保存在 `compile-kernel/output` 目录里。可以将这些内核文件上传到 `Armbian` 系统任意目录，如 `/opt/5.4.180` 目录下，并在此内核目录内执行 `armbian-update` 命令进行内核安装。

- ### 使用 GitHub Action 进行编译

1. 关于 Workflows 文件的配置在 [.yml](https://github.com/ophub/amlogic-s9xxx-armbian/tree/main/.github/workflows) 文件里。

2. 在 [Action](https://github.com/ophub/amlogic-s9xxx-armbian/actions) 页面里选择 ***`Compile the armbian kernel`*** ，点击 ***`Run workflow`*** 按钮即可编译。

- ### 仅单独引入 GitHub Action 编译内核

可以在你的 github.com 仓库中通过 Actions 调用本仓库的内核编译脚本，制作个性化内核。详见使用模板 [compile-kernel.yml](https://github.com/ophub/amlogic-s9xxx-openwrt/blob/main/.github/workflows/compile-kernel.yml) 。代码如下:

```yaml
- name: Compile the kernel for Amlogic s9xxx
  uses: ophub/amlogic-s9xxx-armbian@main
  with:
    build_target: kernel
    kernel_version: 5.10.100_5.4.180
    kernel_auto: true
    kernel_sign: -good-luck
```

- GitHub Action 输入参数说明

相关参数与`本地编译命令`相对应，请参考上面的说明。

| 参数               | 默认值            | 说明                                                      |
|-------------------|------------------|-----------------------------------------------------------|
| build_target      | kernel           | 固定参数 `kernel`，设置编译目标为内核。                        |
| kernel_repo       | unifreq          | 指定编译内核的源代码仓库。默认值为 `unifreq` 。功能参考 `-r`      |
| kernel_version    | 5.10.100_5.4.180 | 指定 [kernel](https://cdn.kernel.org/pub/linux/kernel/v5.x/) 名称，如 `5.4.180`。功能参考 `-k` |
| kernel_auto       | true             | 设置是否自动采用同系列最新版本内核。默认值为 `true`。功能参考 `-a`  |
| kernel_sign       | -meson64-dev     | 设置内核自定义签名。默认值为 `-meson64-dev`。功能参考 `-n`       |
| kernel_config     | 无               | 默认使用 [tools/config](tools/config) 目录下的配置模板。你可以设置编译内核的配置文件在你仓库中的存放目录，如 `kernel/config_path` 。在此目录下存放的各系列的内核配置模板都必须以 `config-5.x` 的名称为开头，例如编译 `5.4` 系列内核的模板可命名为以 `config-5.4` 开头的各种名字，如 `config-5.4` 、 `config-5.4.174` 或者 `config-5.4.174-good-luck` 等，有多个以 `config-5.4` 为开头的文件时，将使用版本号最大的文件。 |

- GitHub Action 输出变量说明

| 参数                               | 默认值                    | 说明                       |
|-----------------------------------|--------------------------|----------------------------|
| ${{ env.PACKAGED_OUTPUTTAGS }}    | 5.10.100_5.4.180         | 编译好的内核的名称            |
| ${{ env.PACKAGED_OUTPUTPATH }}    | compile-kernel/output    | 编译完成的内核所在文件夹的路径  |
| ${{ env.PACKAGED_OUTPUTDATE }}    | 2021.04.13.1058          | 编译日期                    |
| ${{ env.PACKAGED_STATUS }}        | success                  | 编译状态：success / failure  |

## 内核使用说明

此内核可用于 `Armbian` 和 `OpenWrt` 系统。以 ophub 的项目进行举例说明。

- ### 在 Armbian 系统中使用

下面分别对在编译 Armbian 固件时集成，以及如何安装到已有的系统中使用进行说明。

- #### 使用内核编译 Armbian 固件

编译 Armbian 固件支持本地化操作，也支持使用 github.com 的 Actions 在线编译。其中本地化编译时的使用方法详见：[本地化打包](../README.cn.md#本地化打包), 使用 Actions 在线编译的方法详见：[Actions 编译](../README.cn.md#使用-github-actions-进行编译)

- #### 将内核安装到已有的 Armbian 系统

可以使用 `armbian-update` 命令将编译好的内核安装到已有的 Armbian 系统中，具体操作方法详见：[更新 Armbian 内核](../README.cn.md#更新-armbian-内核)


- ### 在 OpenWrt 系统中使用

下面分别对在 OpenWrt 系统中编译固件时集成和安装到已有的系统中使用进行介绍。

- #### 使用内核编译 OpenWrt 固件

编译 OpenWrt 固件支持本地化操作，也支持使用 github.com 的 Actions 在线编译。其中本地化编译时的使用方法详见：[本地化打包](https://github.com/ophub/amlogic-s9xxx-openwrt/blob/main/README.cn.md#本地化打包), 使用 Actions 在线编译的方法详见：[Actions 编译](https://github.com/ophub/amlogic-s9xxx-openwrt/blob/main/README.cn.md#githubcom-一站式编译和打包)

- #### 将内核安装到已有的 OpenWrt 系统

可以使用 [luci-app-amlogic](https://github.com/ophub/luci-app-amlogic/blob/main/README.cn.md) 插件将编译好的内核安装到已有的 OpenWrt 系统中，具体操作方法详见：[升级 OpenWrt](https://github.com/ophub/amlogic-s9xxx-openwrt/blob/main/README.cn.md#升级-openwrt)

