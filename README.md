# Actions-OpenWrt-Xiaomi-R4A

[![LICENSE](https://img.shields.io/github/license/mashape/apistatus.svg?style=flat-square&label=LICENSE)](https://github.com/P3TERX/Actions-OpenWrt/blob/master/LICENSE)
![GitHub Stars](https://img.shields.io/github/stars/unkaer/Actions-OpenWrt-Xiaomi-R4A.svg?style=flat-square&label=Stars&logo=github)
![GitHub Forks](https://img.shields.io/github/forks/unkaer/Actions-OpenWrt-Xiaomi-R4A.svg?style=flat-square&label=Forks&logo=github)

使用 GitHub Actions 编译 OpenWrt 固件

[Read the details in my blog (in Chinese) | 中文教程](https://p3terx.com/archives/build-openwrt-with-github-actions.html)

# 修改说明

魔改成了
使用 `Lean` 大佬的 https://github.com/coolsnowwolf/lede openwrt源码编译 `小米R4A千兆版` 的 `breed直刷版`

1. .comfig 默认配置选中小米R4A千兆版 （其余默认）

2. 修改diy-part1.sh

添加一些插件快乐上网插件

更新 argon 主题

添加脚本自动修改 openwrt 源码成 breed 直刷 （参考[pidge](https://www.right.com.cn/FORUM/space-uid-221258.html) 提供的方法 [分享小米R4A千兆版编译OPENWRT(Breed直刷版)](https://www.right.com.cn/FORUM/thread-4052254-1-1.html) 修改，编译生成的固件就可以用breed直刷了）

3. 修改diy-part2.sh

修改 `diy-part2.sh` 参考自 [281677160/build-openwrt](https://github.com/281677160/build-openwrt) 

```sh
# 修改openwrt登陆地址,把下面的192.168.5.1修改成你想要的就可以了
sed -i 's/192.168.1.1/192.168.5.1/g' package/base-files/files/bin/config_generate

# 修改主机名字，把Xiaomi-R4A修改你喜欢的就行（不能纯数字或者使用中文）
sed -i '/uci commit system/i\uci set system.@system[0].hostname='Xiaomi-R4A'' package/lean/default-settings/files/zzz-default-settings

# 版本号里显示一个自己的名字（ababwnq build $(TZ=UTC-8 date "+%Y.%m.%d") @ 这些都是后增加的）
sed -i "s/OpenWrt /ababwnq build $(TZ=UTC-8 date "+%Y.%m.%d") @ OpenWrt /g" package/lean/default-settings/files/zzz-default-settings

# 修改 argon 为默认主题,可根据你喜欢的修改成其他的（不选择那些会自动改变为默认主题的主题才有效果）
sed -i 's/luci-theme-bootstrap/luci-theme-argon/g' feeds/luci/collections/luci/Makefile

# 设置密码为空（安装固件时无需密码登陆，然后自己修改想要的密码）
sed -i 's@.*CYXluq4wUazHjmCDBCqXF*@#&@g' package/lean/default-settings/files/zzz-default-settings
```
# 使用

## 1.Fork

右上角，Fork本项目到你的仓库

## 2.Actions

同意工作流，然后开整。

![](https://gitee.com/Unkaer/blog/raw/master/images/material/20210307205947.webp)

等到 ssh连接 界面
![](https://gitee.com/Unkaer/blog/raw/master/images/material/20210307210916.webp)

单击 `url` 进行访问;
![](https://gitee.com/Unkaer/blog/raw/master/images/material/20210307210937.webp)

黑屏 按 `Ctrl`+`C`变为命令行模式 ;
输入 `cd openwrt/ && make menuconfig` 进入菜单![](https://gitee.com/Unkaer/blog/raw/master/images/material/20210307211012.webp)
![](https://gitee.com/Unkaer/blog/raw/master/images/material/20210307211148.webp)

### 2.1设置插件
插件对照参考 [OpenWrt 编译 LuCI -> Applications 添加插件应用说明-L大](https://www.right.com.cn/forum/thread-3682029-1-1.html)
`Y` 确定选中 `N`取消选中

#### 2.1.1 机型选择
前三个是设置机型，默认已经选好了 小米R4A千兆版
![机型](https://user-images.githubusercontent.com/45261780/128300236-881f51d1-6475-4621-83f4-61775e01030e.png)

#### 2.1.2 主题选择
在 `LuCI` --> `Themes` 中进行设置
![LuCI](https://user-images.githubusercontent.com/45261780/128300627-a3af1f69-2c2f-49fa-86ce-8da6b3a0d0d4.png)

#### 2.1.3 插件选择
在 `LCTY` --> `Applications` 中进行设置
![Applications](https://user-images.githubusercontent.com/45261780/128300725-26799ad1-1bbb-4035-8ff0-aeaba1635dd3.png)

#### 2.1.4 保存设置
方向键移动选中 `Save` 回车确认
![1](https://user-images.githubusercontent.com/45261780/128300983-93ee554e-e72d-4082-8550-265ff087971e.png)
保存为 默认的文件名
![2](https://user-images.githubusercontent.com/45261780/128301040-705307f5-2b0b-42d0-b52c-5608807ebcd5.png)

或者 连按两下 `ESC` 返回至退出菜单界面，
会弹出确认是否保存菜单，确认即可
![3](https://user-images.githubusercontent.com/45261780/128301176-8f163e5e-84f3-4700-ba38-7732f4fe16f4.png)


### 2.2下载本次修改的配置文件（可选）
下次升级就可以直接用，不必再 ssh 选择插件了

```
# 若在调整OpenWrt系统组件的过程有多次保存操作，则建议先删除.config.old文件再继续操作
rm -f .config.old

# 根据编译环境生成默认配置
make defconfig

# 对比默认配置的差异部分生成配置文件（可以理解为增量）
./scripts/diffconfig.sh > seed.config

# 输出seed.config
cat seed.config

# 自己复制保存到合适的位置
# 在手动修改成项目的 .config 文件
```

### 2.3退出 ssh
在命令行界面 `ctrl`+`D`  退出 ssh
![批注 2021-08-05 115225](https://user-images.githubusercontent.com/45261780/128301252-f054fa0a-6544-4770-8e99-217946f9b692.png)
![批注 2021-08-05 115238](https://user-images.githubusercontent.com/45261780/128301319-5b6969b1-94e5-43f7-97c7-6f69acbd92ec.png)

## 3.6 下载固件
一般两个小时左右编译完成
![](https://gitee.com/Unkaer/blog/raw/master/images/material/20210307211649.webp)

## 3.7 刷入体验
第一次要断电，按住`reset`键，再插入电源，等待5秒，浏览器访问 192.168.1.1 ，就进入 breed界面，进行升级
之后的更新升级，都可以在 `系统`->`备份/升级`里面刷入新的固件
附·一些图片

![](https://gitee.com/Unkaer/blog/raw/master/images/material/20210314184159.webp)

![](https://gitee.com/Unkaer/blog/raw/master/images/material/20210314184218.webp)


[MIT](https://github.com/P3TERX/Actions-OpenWrt/blob/main/LICENSE) © P3TERX
