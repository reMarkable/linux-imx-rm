# Linux kernel for reMarkable Paper Pro

This repository provides buildable kernel source tree snapshots based on the reMarkable software version.
The source code is stored using git lfs. You might need to run `git lfs install` depending on your system.

# Build and deploy instructions (example)
```shell
git clone git@github.com:reMarkable/linux-imx-rm.git && cd linux-imx-rm
git checkout rmpp_6.1.55_v3.14.x

tar xzvf linux-imx-rel-4.0-jv-3.14.1.10-68a6c61fc0.tar.gz
cd linux-imx-rel-4.0-jv-3.14.1.10-68a6c61fc0

export make=make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu-
make ferrari_defconfig
make -j$(nproc)
make INSTALL_MOD_STRIP=1 INSTALL_MOD_PATH=./output modules_install
mkimage -f ferrari.its fitImage

# deploy to target
ssh root@10.11.99.1 mount -o remount, rw /
rsync --archive --recursive --verbose fitImage root@10.11.99.1:/boot
rsync --archive --recursive --verbose output/lib root@10.11.99.1:/
```
