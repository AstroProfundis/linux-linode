# Maintainer: Allen Zhong <moeallenz@gmail.com>
# Maintainer: Yardena Cohen <yardenack at gmail dot com>
# tracks: https://projects.archlinux.org/svntogit/packages.git/log/trunk?h=packages/linux

pkgbase=linux-linode
_basekernel=4.15
_kernelname=${pkgname#linux}
_srcname=linux-${_basekernel}
pkgver=${_basekernel}.9
pkgrel=1
arch=('x86_64')
url="https://github.com/AstroProfundis/linux-linode"
license=(GPL2)
makedepends=('xmlto' 'kmod' 'inetutils' 'bc' 'libelf' 'git')
options=('!strip')
_ckpatchversion=1
_ckpatchname="patch-${_basekernel}-ck${_ckpatchversion}"
_gcc_more_v='20180310'
_clearver=${_basekernel}.7-536
source=("https://www.kernel.org/pub/linux/kernel/v4.x/${_srcname}.tar."{xz,sign}
        "https://www.kernel.org/pub/linux/kernel/v4.x/patch-${pkgver}."{xz,sign}
	"http://ck.kolivas.org/patches/4.0/${_basekernel}/${_basekernel}-ck${_ckpatchversion}/${_ckpatchname}.xz"
	"enable_additional_cpu_optimizations-$_gcc_more_v.tar.gz::https://github.com/graysky2/kernel_gcc_patch/archive/$_gcc_more_v.tar.gz" # enable_additional_cpu_optimizations_for_gcc
	"https://raw.githubusercontent.com/dolohow/uksm/master/uksm-${_basekernel}.patch"
	"clearlinux::git+https://github.com/clearlinux-pkgs/linux.git#tag=${_clearver}"
        'config'
        '08_linux_linode'
        '98-linux-linode.hook'
        '99-grub-ll.hook'
        'menu.lst'
        'preset'
	"0001-add-sysctl-to-disallow-unprivileged-CLONE_NEWUSER-by.patch"
	"0002-drm-i915-edp-Only-use-the-alternate-fixed-mode-if-it.patch"
	)
sha512sums=('c00d92659df815a53dcac7dde145b742b1f20867d380c07cb09ddb3295d6ff10f8931b21ef0b09d7156923a3957b39d74d87c883300173b2e20690d2b4ec35ea'
            'SKIP'
            '60d24d79c19ab44520e4b583c74ca30045dc72bebd426a802c84d62c369fbda5bd7016aee1f5fa3931937cd31f17d6c0867080eb26949dedbd2d9522ee13143d'
            'SKIP'
            'ade9fb1e712acd6a0d0b993a22119707d95ac08a55ad9b7474c7d0ee4b8b2bcf07ad9dda92914abae5469e8c569f49a873eb3fffe7e821affb671e1a61d433fa'
            '079e34ec7bf3ef36438c648116e24c51e00ea8608a1d8b5776164478522d6a96dcab5fe0431e8e9a6282c11a1edd177e1b68fc971a81717b297e199efc101963'
            '337b220e5c5f240bf195fcf174974c03b127598723fc4ea5813e5c32154048ac4193737418b21e720e9034ad53589b59b898d0e648925db7e2db2ad57acd7fe7'
            'SKIP'
            '00db9715b6a3a2813c7d2e3f9c18507515d2c97d40a80d7fd3d15c5e9c5c829bf658ddcf26b4ca82aef164a46bbb2ea1148d959aa1d768f226b93eb80e9e0e89'
            '7a80f858c32a9dd62f43aba0b7119a1196869216117164bcde24ab46022e8a1bbe27821faa26ca690a1633a5a9fe324e98e5cdf14f37591d569cbc71f542482d'
            'c57a6c8d9978cb6a1034bed33ba5e06bef9b134f22113761798d4fa46e8091e7b0bd26f3a14d79122ba780b2f7a93ca26850f4da6a654f81b34cc79c242f683f'
            'db9080b2548e4dcd61eaaf20cd7d37cbbc8c204ce85a2e3408d0671f6b26010f77a61affd2c77e809768714eca29d3afb64765a3f2099317a2c928eff3feb4cf'
            '73cb4c064d8942fddaac48158b7e77d19afc1cb61f83936f21832ba7d7266ccfd3021114252edd5cec5542096204f48cf30544fd6bffff79bc94d96fabe74f52'
            '62870a08f000abfe8eb1f50271afdf04686af108554f7629dc5e1d7610ad14bdc9cd14d2609270b83f9edb745a520b81fa7bfb92ebcc28a146df040c895b549b'
            '0b758342126881383ade60a01d05a880868cd58497f4e6dcf8c9ba58e3267c3827cdbe66eeab792621c34fc77c86286d1594e2d2e1dc4ac6aae0f4b4dd059bfe'
            '39df5af1aad9891bf228582ca71a5038c134fd18ecb21d4cab1ba2cbe0eabb260712d449a57006ffe31fa65c939b92a50f1bb3c72cbbabd040571a2899cbee11')
validpgpkeys=(
              'ABAF11C65A2970B130ABE3C479BE3E4300411886' # Linux Torvalds
              '647F28654894E3BD457199BE38DBBDC86092693E' # Greg Kroah-Hartman
)
pkgdesc="The kernel for Linode servers, with some custom patchsets"
depends=('coreutils' 'linux-firmware' 'kmod' 'mkinitcpio>=0.7' 'grub')
provides=("linux=${pkgver}" "linux-ck=${pkgver}")
conflicts=(grub-legacy)
backup=(etc/mkinitcpio.d/${pkgname}.preset boot/grub/menu.lst)
install=install

prepare() {
  cd "${srcdir}/${_srcname}"

  # add upstream patch
  msg "Applying Linux patches..."
  patch -p1 -i "${srcdir}/patch-${pkgver}"

  # add latest fixes from stable queue, if needed
  # http://git.kernel.org/?p=linux/kernel/git/stable/stable-queue.git

  msg "Applying Custom patches..."
  # disable USER_NS for non-root users by default
  patch -Np1 -i ../0001-add-sysctl-to-disallow-unprivileged-CLONE_NEWUSER-by.patch

  # https://bugs.archlinux.org/task/56711
  patch -Np1 -i ../0002-drm-i915-edp-Only-use-the-alternate-fixed-mode-if-it.patch

  # Apply clearlinux patches
  for i in $(grep "^Patch" ${srcdir}/clearlinux/linux.spec | sed -n 's/.*: //p'); do
    msg "Applying ${i}"
    patch -p1 -i "$srcdir/clearlinux/${i}"
  done

  # fix naming schema in EXTRAVERSION of ck patch set
  sed -i -re "s/^(.EXTRAVERSION).*$/\1 = /" ../${_ckpatchname}

  # Patch source with ck patchset
  msg "Applying ck patchset..."
  patch -Np1 -i ../${_ckpatchname}

  # Patch source with UKSM patchset
  msg "Applying UKSM patchset..."
  patch -Np1 -i ../uksm-${_basekernel}.patch

  # Patch source to unlock additional gcc CPU optimizatons
  # https://github.com/graysky2/kernel_gcc_patch
  msg "Enabling additional CPU optimizations..."
  patch -Np1 -i "../kernel_gcc_patch-$_gcc_more_v/enable_additional_cpu_optimizations_for_gcc_v4.9+_kernel_v4.13+.patch"

  make mrproper
  cat "${srcdir}/config" > ./.config

  sed -i "s|CONFIG_LOCALVERSION=.*|CONFIG_LOCALVERSION=\"${_kernelname}\"|g" ./.config
  sed -i "s|CONFIG_LOCALVERSION_AUTO=.*|CONFIG_LOCALVERSION_AUTO=n|" ./.config

  # set extraversion to pkgrel
  sed -ri "s|^(EXTRAVERSION =).*|\1 -${pkgrel}|" Makefile

  # don't run depmod on 'make install'. We'll do this ourselves in packaging
  sed -i '2iexit 0' scripts/depmod.sh

  make prepare

  # rewrite configuration
  yes "" | make config >/dev/null

  # save configuration for later reuse
  cat .config > "${startdir}/config.last"
}

build() {
  cd "${srcdir}/${_srcname}"
  [[ "$MAKEFLAGS" =~ -j[0-9]* ]] || MAKEFLAGS+=" -j$(nproc)"
  ionice -c 3 nice -n 16 make ${MAKEFLAGS} LOCALVERSION= bzImage modules
}

_package() {
  cd "${srcdir}/${_srcname}"
  _kernver="$(make kernelrelease)"
  emdir="extramodules-${_basekernel}${_kernelname}"
  mkdir -p "${pkgdir}"/{usr/lib/modules/"$emdir",boot/grub}
  make INSTALL_MOD_PATH="${pkgdir}/usr" modules_install
  rm -rf "${pkgdir}"/usr/lib/modules/${_kernver}/{source,build}
  cp arch/x86/boot/bzImage "${pkgdir}/boot/vmlinuzll-${pkgname}"
  install -D -m644 vmlinux "${pkgdir}/usr/lib/modules/${_kernver}/build/vmlinux"
  install -D -m644 "${srcdir}/preset" "${pkgdir}/etc/mkinitcpio.d/${pkgname}.preset"
  install -D -m644 "${srcdir}/98-linux-linode.hook" "${pkgdir}/usr/share/libalpm/hooks/98-linux-linode.hook"
  install -D -m644 "${srcdir}/99-grub-ll.hook"      "${pkgdir}/usr/share/libalpm/hooks/99-grub-ll.hook"
  install -D -m755 "${srcdir}/08_linux_linode" "${pkgdir}/etc/grub.d/08_linux_linode"
  sed \
    -e  "s/KERNEL_NAME=.*/KERNEL_NAME=${_kernelname}/" \
    -i "${startdir}/install"
  sed \
    -e "s|ALL_kver=.*|ALL_kver=\"/boot/vmlinuzll-${pkgname}\"|" \
    -e "s|default_image=.*|default_image=\"/boot/initramfs-${pkgname}.img\"|" \
    -e "s|fallback_image=.*|fallback_image=\"/boot/initramfs-${pkgname}-fallback.img\"|" \
    -i "${pkgdir}/etc/mkinitcpio.d/${pkgname}.preset"
  ln -s "../${emdir}" "${pkgdir}/usr/lib/modules/${_kernver}/extramodules"
  echo "${_kernver}" >| "${pkgdir}/usr/lib/modules/${emdir}/version"
  depmod -b "${pkgdir}/usr" -F System.map "${_kernver}"
  sed "s/%VER%/${pkgver}-${pkgrel}/ig" "${srcdir}/menu.lst" > "${pkgdir}/boot/grub/menu.lst"
}

_package-headers() {
  pkgdesc="Header files and scripts for building modules for ${pkgbase/linux/Linux} kernel"
  #_Hpkgdesc="Header files and scripts for building modules for ${pkgbase/linux/Linux} kernel"
  #pkgdesc="${_Hpkgdesc}"
  depends=('linux-linode') # added to keep kernel and headers packages matched
  provides=("linux-ck-headers=${pkgver}" "linux-headers=${pkgver}")
  #groups=('ck-generic')

  cd ${_srcname}
  local _builddir="${pkgdir}/usr/lib/modules/${_kernver}/build"

  install -Dt "${_builddir}" -m644 Makefile .config Module.symvers
  install -Dt "${_builddir}/kernel" -m644 kernel/Makefile

  mkdir "${_builddir}/.tmp_versions"

  cp -t "${_builddir}" -a include scripts

  install -Dt "${_builddir}/arch/x86" -m644 arch/x86/Makefile
  install -Dt "${_builddir}/arch/x86/kernel" -m644 arch/x86/kernel/asm-offsets.s

  cp -t "${_builddir}/arch/x86" -a arch/x86/include

  install -Dt "${_builddir}/drivers/md" -m644 drivers/md/*.h
  install -Dt "${_builddir}/net/mac80211" -m644 net/mac80211/*.h

  # http://bugs.archlinux.org/task/9912
  install -Dt "${_builddir}/drivers/media/dvb-core" -m644 drivers/media/dvb-core/*.h

  # http://bugs.archlinux.org/task/13146
  install -Dt "${_builddir}/drivers/media/i2c" -m644 drivers/media/i2c/msp3400-driver.h

  # http://bugs.archlinux.org/task/20402
  install -Dt "${_builddir}/drivers/media/usb/dvb-usb" -m644 drivers/media/usb/dvb-usb/*.h
  install -Dt "${_builddir}/drivers/media/dvb-frontends" -m644 drivers/media/dvb-frontends/*.h
  install -Dt "${_builddir}/drivers/media/tuners" -m644 drivers/media/tuners/*.h

  # add xfs and shmem for aufs building
  mkdir -p "${_builddir}"/{fs/xfs,mm}

  # copy in Kconfig files
  find . -name Kconfig\* -exec install -Dm644 {} "${_builddir}/{}" \;

  # add objtool for external module building and enabled VALIDATION_STACK option
  install -Dt "${_builddir}/tools/objtool" tools/objtool/objtool

  # remove unneeded architectures
  local _arch
  for _arch in "${_builddir}"/arch/*/; do
    [[ ${_arch} == */x86/ ]] && continue
    rm -r "${_arch}"
  done

  # remove files already in linux-docs package
  rm -r "${_builddir}/Documentation"

  # Fix permissions
  chmod -R u=rwX,go=rX "${_builddir}"

  # strip scripts directory
  local _binary _strip
  while read -rd '' _binary; do
    case "$(file -bi "${_binary}")" in
      *application/x-sharedlib*) _strip="${STRIP_SHARED}" ;; # Libraries (.so)
      *application/x-archive*) _strip="${STRIP_STATIC}" ;; # Libraries (.a)
      *application/x-executable*) _strip="${STRIP_BINARIES}" ;; # Binaries
      *) continue ;;
    esac
    /usr/bin/strip ${_strip} "${_binary}"
  done < <(find "${_builddir}/scripts" -type f -perm -u+w -print0 2>/dev/null)
}

pkgname=("${pkgbase}" "${pkgbase}-headers")
for _p in ${pkgname[@]}; do
  eval "package_${_p}() {
    $(declare -f "_package${_p#${pkgbase}}")
    _package${_p#${pkgbase}}
  }"
done
