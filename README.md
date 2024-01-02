# openwrt编译个人纪录

参考lean的说明，做好前期准备https://github.com/coolsnowwolf/lede/blob/master/README_EN.md

### 编译命令

1. 首先装好 Linux 系统，推荐 Debian 11 或 Ubuntu LTS

2. 安装编译依赖

   ```bash
   sudo apt update -y
   sudo apt full-upgrade -y
   sudo apt install -y ack antlr3 asciidoc autoconf automake autopoint binutils bison build-essential \
   bzip2 ccache cmake cpio curl device-tree-compiler fastjar flex gawk gettext gcc-multilib g++-multilib \
   git gperf haveged help2man intltool libc6-dev-i386 libelf-dev libfuse-dev libglib2.0-dev libgmp3-dev \
   libltdl-dev libmpc-dev libmpfr-dev libncurses5-dev libncursesw5-dev libpython3-dev libreadline-dev \
   libssl-dev libtool lrzsz mkisofs msmtp ninja-build p7zip p7zip-full patch pkgconf python2.7 python3 \
   python3-pyelftools python3-setuptools qemu-utils rsync scons squashfs-tools subversion swig texinfo \
   uglifyjs upx-ucl unzip vim wget xmlto xxd zlib1g-dev
   ```

3. 下载源代码，更新 feeds 并选择配置

   ```bash
   git clone https://github.com/coolsnowwolf/lede
   cd lede
   ```

## 以下按照个人自定义修改

### 修改默认ip
```shell
sed -i 's/192.168.1.1/192.168.10.2/g' package/base-files/files/bin/config_generate
```

### 替换终端为bash
```shell
sed -i 's/\/bin\/ash/\/bin\/bash/' package/base-files/files/etc/passwd
```
#今天发现在编译mipsel架构时，替换为bash则无法连接ssh，提示bash不存在，只能用ash -- 2023.08.31

### 删除默认密码
```shell
sed -i "/CYXluq4wUazHjmCDBCqXF/d" package/lean/default-settings/files/zzz-default-settings
```

### neobird主题
```shell
git clone https://github.com/thinktip/luci-theme-neobird.git  ~/lede/package/lean/luci-theme-neobird
```


### 添加常用软件包
```shell
sed -i '$a src-git kenzo https://github.com/kenzok8/openwrt-packages' feeds.conf.default #kenzo的软件源
sed -i '$a src-git small https://github.com/kenzok8/small' feeds.conf.default #small科学依赖
#sed -i '$a src-git opentopd  https://github.com/sirpdboy/sirpdboy-package' feeds.conf.default #sirpdboy的软件源，和kenzo重复
git pull
./scripts/feeds update -a
./scripts/feeds install -a
make menuconfig
```


### 编译固件名字开头增加时间戳
```shell
sed -i 's/IMG_PREFIX:=/IMG_PREFIX:=[$(shell date +%Y%m%d)]/g' include/image.mk
```

### 旁路由模式设置防火墙
```shell
nano package/network/config/firewall/files/firewall.config
```
找到lan，追加masq和mtu_fix的值为1
即
```shell
config zone
	option name		lan
	list   network		'lan'
	option input		ACCEPT
	option output		ACCEPT
	option forward		ACCEPT
	option masq		1
	option mtu_fix		1
```

### 二次编译：
```bash
cd lede
git pull
./scripts/feeds update -a
./scripts/feeds install -a
make defconfig
make download -j8
make V=s -j$(nproc)
```




### 若要更换编译架构，需要清除旧的编译产物
```shell
make clean #仅清理编译结果（bin目录）
make distclean #在源码有大规模更新或者内核更新后执行，以保证编译质量。此操作会删除 /bin和 /build_dir目录中的文件
make dirclean #更换架构编译前必须执行。此操作会删除 /bin和 /build_dir目录的中的文件( make clean)以及 /staging_dir、 /toolchain、 /tmp和 /logs中的文件（除了.config、dl文件夹、feeds文件夹以外都清理）
```

****
感谢包括lean在内各位开发者！
