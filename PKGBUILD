# Maintainer: Allen Zhong <moeallenz at gmail dot com>
# Maintainer: Yardena Cohen <yardenack at gmail dot com>
# tracks: https://projects.archlinux.org/svntogit/packages.git/log/trunk?h=packages/linux

pkgbase=linux-linode
_basekernel=5.4
pkgrel=1
_kernelname=${pkgname#linux}
_srcname=linux-${_basekernel}
pkgver=${_basekernel}.46
arch=('x86_64')
url="https://github.com/yardenac/linux-linode"
license=(GPL2)
makedepends=(xmlto docbook-xsl kmod inetutils bc libelf)
options=('!strip')
_uksm_path="https://raw.githubusercontent.com/dolohow/uksm/master/v5.x"
_uksm_patch="uksm-${_basekernel}.patch"
source=("https://mirrors.tuna.tsinghua.edu.cn/kernel/v5.x/${_srcname}.tar."{xz,sign}
        "https://mirrors.tuna.tsinghua.edu.cn/kernel/v5.x/patch-${pkgver}.xz"
        "${_uksm_path}/${_uksm_patch}"
        'config'
        '08_linux_linode'
        '99-grub-ll.hook'
        'menu.lst'
        'preset')
md5sums=('ce9b2d974d27408a61c53a30d3f98fb9'
         'SKIP'
         'b36e63e054dbc1b1c61a41be4ad773d7'
         '150751b7db9f7bd579841a7f0dfc291f'
         'fcba7b20550d0f2a3560938ddb361f1a'
         'b24775bcf1d7a04c1d489af73c6e74b0'
         '625481f015365febcd65aa136ee555f9'
         '901c03c685c65119b50666cb31a080be'
         '3d6427cc412dbc226b5d058c82ba7c66')
sha256sums=('bf338980b1670bca287f9994b7441c2361907635879169c64ae78364efc5f491'
            'SKIP'
            '4f85e3d59aadf31bea6170439b16153fd499f86718d214c1c66065c64f762336'
            '422a7a32f45dc01bcf90707bbdf870a5a8428eeae5b98453cae42138f0d7bbdf'
            '616103b983536bbaf401c1daa1fbd4ec7aadb42719e2a0b1a8b99c3312a44a7b'
            'a2295cee5ae8c8d9c3efb0483e5e842f6bd9753f851c7433465b242264738546'
            '368fb58e7aa465f597e9a72da4b6eea4183c1a85242173412d54ad18d10d8fb3'
            'df9f20f818bf6c11296e5ac58c5fdd664ba17f3d80cf301eef9309ddb528741d'
            '33d9f913e4aed9f41bf263ff784921ab7d69597732079cbd06ff05b9adb32f44')
sha512sums=('9f60f77e8ab972b9438ac648bed17551c8491d6585a5e85f694b2eaa4c623fbc61eb18419b2656b6795eac5deec0edaa04547fc6723fbda52256bd7f3486898f'
            'SKIP'
            '98d81824e36551c74c4411372476be374c52f13bf7b8e733bcc7dbc3a08c467f2bd26a337c0edb0f1366c93df6d1e412b02c1b519cccd0379d32de6184c6a957'
            'bacc2a6aba0316ea5cbe121850d57ab15a914d8fd4e9087390a26accc4916255b2b77ae14339e94029910538d7095a8567a2929aad7d4e5db54adc62b1758462'
            '178410f4fd158b0cccc622efb325f2df2643f9bb1febcb92cc19a819f10fa3b388db4b840a209b8a82973de5eb4cac8f09e832ab609ba56a4a65d08c6885dc55'
            '1e901b8894743e9dcb04046a5fa58e14b19095b3295abae679dcbbf309bd79ddf1716dcd07ae8a71e7cdc9361216c0c9da12a76edb45e9388c512b07df7759e7'
            'db9080b2548e4dcd61eaaf20cd7d37cbbc8c204ce85a2e3408d0671f6b26010f77a61affd2c77e809768714eca29d3afb64765a3f2099317a2c928eff3feb4cf'
            '1a17f83747ebd2dbe8d57996a1234f9e72de0754f8907c984477d761c2d99753490b72d80e2c801b85ded705818d530401f6377e3312937d72d1e4052007ce30'
            'a0a78831075336edef0a8faa34fa550986c3c4d89a89f4f39d798da0211129dc90257d162bec2cdefabef2eb5886a710e70c72074b2f3016788861d05d1e2a1f')
validpgpkeys=(
              'ABAF11C65A2970B130ABE3C479BE3E4300411886' # Linux Torvalds
              '647F28654894E3BD457199BE38DBBDC86092693E' # Greg Kroah-Hartman
              'E240B57E2C4630BA768E2F26FC1B547C8D8172C8' # Levente Polyak
)

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

  cp "${srcdir}/config" .config
  make oldconfig
  make -s kernelrelease > version
}

build() {
  cd "${srcdir}/${_srcname}"
  [[ "$MAKEFLAGS" =~ -j[0-9]* ]] || MAKEFLAGS+=" -j$(nproc)"
  ionice -c 3 nice -n 16 make ${MAKEFLAGS} bzImage modules
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
  install -Dm644 "$(make -s image_name)" "$modulesdir/vmlinuz"

  # Used by mkinitcpio to name the kernel
  echo "$pkgbase" | install -Dm644 /dev/stdin "$modulesdir/pkgbase"

  echo "Installing modules..."
  make INSTALL_MOD_PATH="$pkgdir/usr" modules_install

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

