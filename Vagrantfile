Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.provision :shell, :inline => $BOOTSTRAP_SCRIPT # see below
end

$BOOTSTRAP_SCRIPT = <<EOF
  set -e # Stop on any error

  export DEBIAN_FRONTEND=noninteractive

  # APT packages
  sudo apt-get install -y zip libc6-i386 lib32gcc1

  # NACL SDK
  VERSION=pepper_37
  wget http://storage.googleapis.com/nativeclient-mirror/nacl/nacl_sdk/nacl_sdk.zip
  unzip nacl_sdk.zip
  cd nacl_sdk
  ./naclsdk list
  ./naclsdk update $VERSION
  echo 'export NACL_SDK_ROOT='`pwd`"/$VERSION" | sudo tee -a ~vagrant/.profile
  sudo chown -R vagrant .
  # NACL cleanup
  rm -rf ./nacl_sdk/sdk_cache ./nacl_sdk/pepper_37/ports ./nacl_sdk/pepper_37/toolchain/linux_arm_newlib \
    ./nacl_sdk/pepper_37/toolchain/linux_pnacl ./nacl_sdk/pepper_37/lib/glibc_x86_32 ./nacl_sdk/pepper_37/lib/glibc_x86_64 \
    ./nacl_sdk/pepper_37/lib/linux_host ./nacl_sdk/pepper_37/lib/pnacl ./nacl_sdk/pepper_37/lib/newlib_arm \
    ./nacl_sdk/pepper_37/examples ./nacl_sdk/pepper_37/toolchain/linux_x86_glibc

  ########## CLEANUP AND SHRINK SPACE
  # Remove APT files
  find /var/lib/apt -type f | xargs rm -f
  # Remove documentation files
  find /var/lib/doc -type f | xargs rm -f
  # Remove Virtualbox specific files
  rm -rf /usr/src/vboxguest* /usr/src/virtualbox-ose-guest*
  apt-get clean -y
  apt-get autoclean -y
  # Zero free space to aid VM compression
  dd if=/dev/zero of=/EMPTY bs=1M
  rm -f /EMPTY
  echo 'Cleanup log files'
  find /var/log -type f | while read f; do echo -ne '' > $f; done;

  echo VAGRANT IS READY.
EOF
