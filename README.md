# openwrt编译个人纪录

https://github.com/coolsnowwolf/lede/blob/master/README_EN.md

### 修改默认ip
```shell
sed -i 's/192.168.1.1/192.168.10.2/g' package/base-files/files/bin/config_generate
```

### 替换终端为bash
```shell
sed -i 's/\/bin\/ash/\/bin\/bash/' package/base-files/files/etc/passwd
```

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

感谢包括lean在内各位开发者！
