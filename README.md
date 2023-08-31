# openwrt编译个人纪录

参考lean的说明，做好前期准备https://github.com/coolsnowwolf/lede/blob/master/README_EN.md

### 修改默认ip
```shell
sed -i 's/192.168.1.1/192.168.10.2/g' package/base-files/files/bin/config_generate
```

### 替换终端为bash
```shell
sed -i 's/\/bin\/ash/\/bin\/bash/' package/base-files/files/etc/passwd
```
#今天发现在编译mipsel架构时，替换为bash则无法连接ssh，提示bash不存在，只能用ash

### 删除默认密码
```shell
sed -i "/CYXluq4wUazHjmCDBCqXF/d" package/lean/default-settings/files/zzz-default-settings
```

### 添加常用软件包
```shell
sed -i '$a src-git kenzo https://github.com/kenzok8/openwrt-packages' feeds.conf.default #kenzo的软件源
sed -i '$a src-git small https://github.com/kenzok8/small' feeds.conf.default #small科学依赖
#sed -i '$a src-git opentopd  https://github.com/sirpdboy/sirpdboy-package' feeds.conf.default #sirpdboy的软件源，和kenzo重复
```

### neobird主题
```shell
git clone https://github.com/thinktip/luci-theme-neobird.git  ~/lede/package/lean/luci-theme-neobird
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

### 若要更换编译架构，需要清除旧的编译产物
```shell
make dirclean #在源码有大规模更新或者内核更新后执行，以保证编译质量。此操作会删除 /bin和 /build_dir目录中的文件
make dirclean #更换架构编译前必须执行。此操作会删除 /bin和 /build_dir目录的中的文件( make clean)以及 /staging_dir、 /toolchain、 /tmp和 /logs中的文件
```

****
感谢包括lean在内各位开发者！
