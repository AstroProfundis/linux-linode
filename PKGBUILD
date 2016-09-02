# Maintainer: Allen Zhong <moeallenz@gmail.com>
# Maintainer: Yardena Cohen <yardenack at gmail dot com>
# tracks: https://projects.archlinux.org/svntogit/packages.git/log/trunk?h=packages/linux

pkgname=linux-linode
_basekernel=4.7
_kernelname=${pkgname#linux}
_srcname=linux-${_basekernel}
pkgver=${_basekernel}.2
pkgrel=1
arch=('x86_64')
url="https://github.com/AstroProfundis/linux-linode"
license=(GPL2)
makedepends=(xmlto docbook-xsl kmod inetutils bc 'gcc>=4.9.0')
options=('!strip')
_ckpatchversion=1
_ckpatchname="patch-${_basekernel}-ck${_ckpatchversion}"
_bfqver_legacy="v7r11"
_bfqver="v8r2"
_bfqpath="http://algo.ing.unimo.it/people/paolo/disk_sched/patches/${_basekernel}.0-${_bfqver}"
_gcc_patch="enable_additional_cpu_optimizations_for_gcc_v4.9+_kernel_v3.15+.patch"
source=("https://www.kernel.org/pub/linux/kernel/v4.x/${_srcname}.tar."{xz,sign}
        "https://www.kernel.org/pub/linux/kernel/v4.x/patch-${pkgver}."{xz,sign}
        "${_bfqpath}/0001-block-cgroups-kconfig-build-bits-for-BFQ-${_bfqver_legacy}-${_basekernel}.0.patch"
        "${_bfqpath}/0002-block-introduce-the-BFQ-${_bfqver_legacy}-I-O-sched-for-${_basekernel}.0.patch"
        "${_bfqpath}/0003-block-bfq-add-Early-Queue-Merge-EQM-to-BFQ-${_bfqver_legacy}-for.patch"
        "${_bfqpath}/0004-block-bfq-turn-BFQ-${_bfqver_legacy}-for-${_basekernel}.0-into-BFQ-${_bfqver}-for.patch"
        "http://ck.kolivas.org/patches/4.0/${_basekernel}/${_basekernel}-ck${_ckpatchversion}/${_ckpatchname}.xz"
        "http://repo-ck.com/source/gcc_patch/${_gcc_patch}.gz"
        'config.x86_64'
        'menu.lst'
        'preset')
sha512sums=('e8c02583e17e4fc4214fef694825fcb78c898266f1624deb1cdf56ab5c5fdfa669c5221122a7cf0d502ed6f921ff3797634acd9d294d29e98e3faa8a21920185'
            'SKIP'
            'e05ef99e047b6874ef3978355c46cf38b7cb9e601af07be4ac12459a58152ebd6232e51f7f1263b95513ecac8aa1ed8129c09f2e298dd833195584ed9584b25b'
            'SKIP'
            '0759a2f48377d38fd331f243c9d49ca63059b2388cfcd46e2f88194d5a97741d2546e044807fe983065de2dcdefdf66197fa6e2630bb6afd0cc8abc3f76c5f01'
            '53fb9471f477e02e132ad4b61fb2381c4ca3268ac43d268a5a40b2ddc6df26ccadf322cd696a80ec51073a6fb1c763a3dd4dbad516dda9cea6c7e6b1da21914c'
            '02c5d82d1129ecc5f6322161e96e4db160fbcfe0c5ce1675ae9e828edfb39033404fc66f2a74fba4a30808291a25d3d0ff761c64957f41bafa8560b50dac1fa3'
            'e0f8e699a16c21fa4c03f3a16f81b1adc4e2da30123d5a06c938ffdd6e837cc9cb76c28ba8417432d7f7c2551c3fcd3a1ecca863da36ab399f3e9cb1eeaeb2e1'
            '9413970338e29cd508e73a7193f37e091b4246529c37f03ef7aa72f7c4c85f559f3509e2e43d27bd7992921fc952e5072951a6b18d4d64fd1fadc9aaea40d30b'
            '62fdd5c0a060a051b64093d71fbb028781061ccb7a28c5b06739a0b24dac0945740d9b73ff170784f60005a589774bcc14f56523ec51557eb3a677f726ec34cf'
            'da105ee3dbd0a31d76e191a91b58cc2e211e51313bf31e40302efe7fc4c83f72da94545d46ad6d12e14b441ca4402d5267c9fd655f4aa5379da99bc97c831a8f'
            '9104c51ff60d5a3865c2e8966d2447e4b225a9f4ac2b1c7bfee1552d59a85ca88325482810abcc8d92bf381ec60daa4518c074a139d07a91a954ee14da20c359'
            'a0a78831075336edef0a8faa34fa550986c3c4d89a89f4f39d798da0211129dc90257d162bec2cdefabef2eb5886a710e70c72074b2f3016788861d05d1e2a1f')
validpgpkeys=(
              'ABAF11C65A2970B130ABE3C479BE3E4300411886' # Linux Torvalds
              '647F28654894E3BD457199BE38DBBDC86092693E' # Greg Kroah-Hartman
             )
pkgdesc="Kernel for Linode servers, with ck patchset"
depends=('coreutils' 'linux-firmware' 'kmod' 'mkinitcpio>=0.7')
provides=(linux)
conflicts=(grub grub-legacy)
backup=(etc/mkinitcpio.d/${pkgname}.preset boot/grub/menu.lst)
install=install

prepare() {
  cd "${srcdir}/${_srcname}"
  patch -p1 -i "${srcdir}/patch-${pkgver}"

  # patch source with BFQ patches
  msg "Patching source with BFQ patches"
  for p in "${srcdir}"/000{1,2,3}-block*.patch; do
    patch -Np1 -i "$p"
  done

  # patch source with ck patchset
  # fix double name in EXTRAVERSION
  sed -i -re "s/^(.EXTRAVERSION).*$/\1 = /" "${srcdir}/${_ckpatchname}"
  msg "Patching source with ck1 including BFS"
  patch -Np1 -i "${srcdir}/${_ckpatchname}"

  # Patch source to enable more gcc CPU optimizatons via the make nconfig
  patch -Np1 -i "${srcdir}/${_gcc_patch}"

  # Clean tree and copy ARCH config over
  msg "Running make mrproper to clean source tree"
  make mrproper

  cat "${srcdir}/config.x86_64" > ./.config
  sed -i '2iexit 0' scripts/depmod.sh
  sed -i "s|CONFIG_LOCALVERSION=.*|CONFIG_LOCALVERSION=\"${_kernelname}\"|g" ./.config
  sed -i "s|CONFIG_LOCALVERSION_AUTO=.*|CONFIG_LOCALVERSION_AUTO=n|g" ./.config
  sed -ri "s|^(EXTRAVERSION =).*|\1 -${pkgrel}|" Makefile
  make prepare
}

build() {
  cd "${srcdir}/${_srcname}"
  CFLAGS=${CFLAGS}" -march=corei7 -mtune=corei7 -mcpu=corei7 "
  CXXFLAGS=${CXXFLAGS}" -march=corei7 -mtune=corei7 -mcpu=corei7 "
  [[ "$MAKEFLAGS" =~ -j[0-9]* ]] || MAKEFLAGS+=" -j$(nproc)"
  ionice -c 3 nice -n 16 make ${MAKEFLAGS} LOCALVERSION= bzImage modules
}

package_linux-linode() {
  KARCH=x86
  cd "${srcdir}/${_srcname}"
  _kernver="$(make LOCALVERSION= kernelrelease)"
  mkdir -p "${pkgdir}"/{lib/{modules,firmware},boot}
  make LOCALVERSION= INSTALL_MOD_PATH="${pkgdir}" modules_install
  rm -rf "${pkgdir}"/lib/{firmware,modules/${_kernver}/{source,build}}
  cp arch/$KARCH/boot/bzImage "${pkgdir}/boot/vmlinuz-${pkgname}"
  install -D -m644 vmlinux "${pkgdir}/lib/modules/${_kernver}/build/vmlinux"
  install -D -m644 "${srcdir}/preset" "${pkgdir}/etc/mkinitcpio.d/${pkgname}.preset"
  sed \
    -e  "s/KERNEL_NAME=.*/KERNEL_NAME=${_kernelname}/" \
    -i "${startdir}/install"
  sed \
    -e "s|ALL_kver=.*|ALL_kver=\"/boot/vmlinuz-${pkgname}\"|" \
    -e "s|default_image=.*|default_image=\"/boot/initramfs-${pkgname}.img\"|" \
    -e "s|fallback_image=.*|fallback_image=\"/boot/initramfs-${pkgname}-fallback.img\"|" \
    -i "${pkgdir}/etc/mkinitcpio.d/${pkgname}.preset"

  emdir="extramodules-${_basekernel}${_kernelname:--ARCH}"
  mkdir -p "${pkgdir}/lib/modules/${emdir}"
  ln -s "../${emdir}" "${pkgdir}/lib/modules/${_kernver}/extramodules"
  echo "${_kernver}" >| "${pkgdir}/lib/modules/${emdir}/version"
  depmod -b "${pkgdir}" -F System.map "${_kernver}"
  mkdir -p "${pkgdir}/usr"
  mv "${pkgdir}/"{lib,usr/}

  mkdir -p ${pkgdir}/boot/grub
  sed "s/%VER%/${pkgver}-${pkgrel}/ig" ${srcdir}/menu.lst > ${pkgdir}/boot/grub/menu.lst
}
