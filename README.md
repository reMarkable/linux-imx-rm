# Linux kernel for reMarkable Paper Pro

This repository provides buildable kernel source tree snapshots based on the reMarkable software version.
The source code is stored using git lfs. You might need to run `git lfs install` depending on your system.

# Build and deploy instructions Paper Pro (example)
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

# Build and deploy instructions Paper Pro Move (example)

```shell
# fetch SDK
wget https://storage.googleapis.com/remarkable-codex-toolchain/3.22.0.65/chiappa/remarkable-production-image-5.2.96-chiappa-public-x86_64-toolchain.sh
chmod +x remarkable-production-image-5.2.96-chiappa-public-x86_64-toolchain.sh

# install sdk
./remarkable-production-image-5.2.96-chiappa-public-x86_64-toolchain.sh

# source sdk, assuming default path
. /opt/codex/chiappa/5.2.96-dirty/environment-setup-cortexa55-remarkable-linux

git clone git@github.com:reMarkable/linux-imx-rm.git && cd linux-imx-rm
git checkout rmpp_6.6.52_v3.22.x
tar xzvf linux-imx-rel-5.2-rm-3.22.0.64-cc26ce6266b2.tar.gz
cd linux-imx-rel-5.2-rm-3.22.0.64-cc26ce6266b2

make chiappa_defconfig
make -j$(nproc)
mkimage -f chiappa.its fitImage
mkimage_imx8 -soc IMX9 -c -data fitImage a35 0x90000000 -out fitImage.ahab -images_hash sha256
make INSTALL_MOD_STRIP=1 INSTALL_MOD_PATH=./output modules_install

# deploy to target
ssh root@10.11.99.1 mount -o remount, rw /
rsync --archive --recursive --verbose fitImage.ahab root@10.11.99.1:/boot
rsync --archive --recursive --verbose output/lib root@10.11.99.1:/usr/

```
