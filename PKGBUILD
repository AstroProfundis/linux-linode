# Maintainer: Allen Zhong <moeallenz at gmail dot com>
# Maintainer: Yardena Cohen <yardenack at gmail dot com>
# tracks: https://projects.archlinux.org/svntogit/packages.git/log/trunk?h=packages/linux

pkgbase=linux-linode
_basekernel=5.10
pkgrel=2
_kernelname=${pkgname#linux}
_srcname=linux-${_basekernel}
pkgver=${_basekernel}.26
arch=('x86_64')
url="https://github.com/yardenac/linux-linode"
license=(GPL2)
makedepends=(xmlto docbook-xsl kmod inetutils bc libelf git clang llvm lld)
options=('!strip')
_gcc_more_v=20210309
# Clear Linux patches
_clr=${_basekernel}.19-1032
_uksm_path="https://gitlab.com/sirlucjan/kernel-patches/-/raw/master/${_basekernel}/uksm-patches"
_uksm_patch="0001-UKSM-for-${_basekernel}.patch"
source=("https://mirrors.tuna.tsinghua.edu.cn/kernel/v5.x/${_srcname}.tar."{xz,sign}
        "https://mirrors.tuna.tsinghua.edu.cn/kernel/v5.x/patch-${pkgver}.xz"
        "more-uarches-$_gcc_more_v.tar.gz::https://github.com/graysky2/kernel_gcc_patch/archive/$_gcc_more_v.tar.gz"
        "clearlinux::git+https://github.com/clearlinux-pkgs/linux.git#tag=${_clr}"
        "${_uksm_path}/${_uksm_patch}"
        'config'
        '08_linux_linode'
        '99-grub-ll.hook'
        'menu.lst'
        'preset')
sha512sums=('95bc137d0cf9148da6a9d1f1a878698dc27b40f68e22c597544010a6c591ce1b256f083489d3ff45ff77753289b535135590194d88ef9f007d0ddab3d74de70e'
            'SKIP'
            '0938834855a70ee5a77d7fc45f3f005983d0ad726f9d6367297970d885f19c433cf09c8592f74ba5c2861bf7eeb303455dd7ecebf4ff4c31baeff7c96357b9be'
            '53cf1f6f17840224fe7406d529968148f09b7f6c1a92bcb677816f19176cfc52eec194e9f8a02666815c1489abd03bdea36a1fb7233bf828dea618318fc76050'
            'SKIP'
            '003e33e214065a57df00458d4a88db5cc33eb00030a516264fc4b2e4de9b6649466451260a30cf86667f8981fc493103dea509217b3850773a65d3beb95e6441'
            '74c1364acf8c23cbb7d7ba4e5f2b3cb168754dd6add8d89923a38b84a9372834ceadd46cb45a29dfd86a49ba08a35df58c10da3b997f9b1ea65789c9ca8499c6'
            '1e901b8894743e9dcb04046a5fa58e14b19095b3295abae679dcbbf309bd79ddf1716dcd07ae8a71e7cdc9361216c0c9da12a76edb45e9388c512b07df7759e7'
            'db9080b2548e4dcd61eaaf20cd7d37cbbc8c204ce85a2e3408d0671f6b26010f77a61affd2c77e809768714eca29d3afb64765a3f2099317a2c928eff3feb4cf'
            '1a17f83747ebd2dbe8d57996a1234f9e72de0754f8907c984477d761c2d99753490b72d80e2c801b85ded705818d530401f6377e3312937d72d1e4052007ce30'
            'a0a78831075336edef0a8faa34fa550986c3c4d89a89f4f39d798da0211129dc90257d162bec2cdefabef2eb5886a710e70c72074b2f3016788861d05d1e2a1f')
validpgpkeys=(
              'ABAF11C65A2970B130ABE3C479BE3E4300411886' # Linux Torvalds
              '647F28654894E3BD457199BE38DBBDC86092693E' # Greg Kroah-Hartman
              'E240B57E2C4630BA768E2F26FC1B547C8D8172C8' # Levente Polyak
)

export KBUILD_BUILD_HOST=tethys
export KBUILD_BUILD_USER=builder
export KBUILD_BUILD_TIMESTAMP="$(date -Ru${SOURCE_DATE_EPOCH:+d @$SOURCE_DATE_EPOCH})"

pkgdesc="Kernel for Linode servers"
depends=(coreutils linux-firmware kmod mkinitcpio grub)
conflicts=(grub-legacy)
backup=(etc/mkinitcpio.d/${pkgname}.preset boot/grub/menu.lst)

prepare() {
  cd "${srcdir}/${_srcname}"
  scripts/setlocalversion --save-scmversion

  echo "Applying Linux patch..."
  patch -Np1 -i ../patch-${pkgver}

  local src
  for src in "${source[@]}"; do
    src="${src%%::*}"
    src="${src##*/}"
    [[ $src = *.patch ]] || continue
    echo "Applying patch $src..."
    patch -Np1 < "../$src"
  done

  ### Add Clearlinux patches
  for i in $(grep '^Patch' ${srcdir}/clearlinux/linux.spec |\
        sed -n 's/.*: //p'); do
    echo "Applying patch ${i}..."
    patch -Np1 -i "$srcdir/clearlinux/${i}"
  done

  # https://github.com/graysky2/kernel_gcc_patch
  # make sure to apply after olddefconfig to allow the next section
  echo "Patching to enable GCC optimization for other uarchs..."
  patch -Np1 -i "$srcdir/kernel_gcc_patch-$_gcc_more_v/more-uarches-for-gcc-v10-and-kernel-5.8+.patch"

  cp "${srcdir}/config" .config
  make LLVM=1 oldconfig
  make LLVM=1 -s kernelrelease > version
}

build() {
  cd "${srcdir}/${_srcname}"
  [[ "$MAKEFLAGS" =~ -j[0-9]* ]] || MAKEFLAGS+=" -j$(nproc)"
  ionice -c 3 nice -n 16 make LLVM=1 ${MAKEFLAGS} bzImage modules
}

_package() {
  pkgdesc="Kernel for Linode servers"
  depends=(coreutils linux-firmware kmod mkinitcpio grub)
  provides=(linux)
  conflicts=(grub-legacy)
  backup=(etc/mkinitcpio.d/${pkgname}.preset boot/grub/menu.lst)

  cd "${srcdir}/${_srcname}"
  local kernver="$(<version)"
  local modulesdir="$pkgdir/usr/lib/modules/$kernver"

  mkdir -p "$modulesdir" "${pkgdir}"/boot/grub

  echo "Installing boot image..."
  # systemd expects to find the kernel here to allow hibernation
  # https://github.com/systemd/systemd/commit/edda44605f06a41fb86b7ab8128dcf99161d2344
  install -Dm644 "$(make LLVM=1 -s image_name)" "$modulesdir/vmlinuz"

  # Used by mkinitcpio to name the kernel
  echo "$pkgbase" | install -Dm644 /dev/stdin "$modulesdir/pkgbase"

  echo "Installing modules..."
  make LLVM=1 INSTALL_MOD_PATH="$pkgdir/usr" modules_install

  # remove build and source links
  rm "$modulesdir"/{source,build}

  install -D -m644 "${srcdir}/preset"          "${pkgdir}/etc/mkinitcpio.d/${pkgname}.preset"
  install -D -m755 "${srcdir}/08_linux_linode" "${pkgdir}/etc/grub.d/08_linux_linode"
  install -D -m644 "${srcdir}/99-grub-ll.hook" "${pkgdir}/usr/share/libalpm/hooks/99-grub-ll.hook"
  sed "s/%VER%/${pkgver}-${pkgrel}/ig" "${srcdir}/menu.lst" > "${pkgdir}/boot/grub/menu.lst"

  echo "Fixing permissions..."
  chmod -Rc u=rwX,go=rX "$pkgdir"
}

_package-headers() {
  pkgdesc="Headers and scripts for building modules for the $pkgdesc kernel"

  cd $_srcname
  local builddir="$pkgdir/usr/lib/modules/$(<version)/build"

  echo "Installing build files..."
  install -Dt "$builddir" -m644 .config Makefile Module.symvers System.map \
    version vmlinux
  install -Dt "$builddir/kernel" -m644 kernel/Makefile
  install -Dt "$builddir/arch/x86" -m644 arch/x86/Makefile
  cp -t "$builddir" -a scripts

  # add objtool for external module building and enabled VALIDATION_STACK option
  install -Dt "$builddir/tools/objtool" tools/objtool/objtool

  # add xfs and shmem for aufs building
  mkdir -p "$builddir"/{fs/xfs,mm}

  echo "Installing headers..."
  cp -t "$builddir" -a include
  cp -t "$builddir/arch/x86" -a arch/x86/include
  install -Dt "$builddir/arch/x86/kernel" -m644 arch/x86/kernel/asm-offsets.s

  install -Dt "$builddir/drivers/md" -m644 drivers/md/*.h
  install -Dt "$builddir/net/mac80211" -m644 net/mac80211/*.h

  # http://bugs.archlinux.org/task/13146
  install -Dt "$builddir/drivers/media/i2c" -m644 drivers/media/i2c/msp3400-driver.h

  # http://bugs.archlinux.org/task/20402
  install -Dt "$builddir/drivers/media/usb/dvb-usb" -m644 drivers/media/usb/dvb-usb/*.h
  install -Dt "$builddir/drivers/media/dvb-frontends" -m644 drivers/media/dvb-frontends/*.h
  install -Dt "$builddir/drivers/media/tuners" -m644 drivers/media/tuners/*.h

  echo "Installing KConfig files..."
  find . -name 'Kconfig*' -exec install -Dm644 {} "$builddir/{}" \;

  echo "Removing unneeded architectures..."
  local arch
  for arch in "$builddir"/arch/*/; do
    [[ $arch = */x86/ ]] && continue
    echo "Removing $(basename "$arch")"
    rm -r "$arch"
  done

  echo "Removing documentation..."
  rm -r "$builddir/Documentation"

  echo "Removing broken symlinks..."
  find -L "$builddir" -type l -printf 'Removing %P\n' -delete

  echo "Removing loose objects..."
  find "$builddir" -type f -name '*.o' -printf 'Removing %P\n' -delete

  echo "Stripping build tools..."
  local file
  while read -rd '' file; do
    case "$(file -bi "$file")" in
      application/x-sharedlib\;*)      # Libraries (.so)
        strip -v $STRIP_SHARED "$file" ;;
      application/x-archive\;*)        # Libraries (.a)
        strip -v $STRIP_STATIC "$file" ;;
      application/x-executable\;*)     # Binaries
        strip -v $STRIP_BINARIES "$file" ;;
      application/x-pie-executable\;*) # Relocatable binaries
        strip -v $STRIP_SHARED "$file" ;;
    esac
  done < <(find "$builddir" -type f -perm -u+x ! -name vmlinux -print0)

  echo "Adding symlink..."
  mkdir -p "$pkgdir/usr/src"
  ln -sr "$builddir" "$pkgdir/usr/src/$pkgbase"

  echo "Fixing permissions..."
  chmod -Rc u=rwX,go=rX "$pkgdir"
}

pkgname=("$pkgbase" "$pkgbase-headers")
for _p in "${pkgname[@]}"; do
  eval "package_$_p() {
    $(declare -f "_package${_p#$pkgbase}")
    _package${_p#$pkgbase}
  }"
done

