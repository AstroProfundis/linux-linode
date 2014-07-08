# Maintainer: Allen Zhong <moeallenz@gmail.com>
# Maintainer: Yardena Cohen <yardenack at gmail dot com>
# tracks: https://projects.archlinux.org/svntogit/packages.git/log/trunk?h=packages/linux

pkgname=linux-linode
_basekernel=3.15
_kernelname=${pkgname#linux}
_srcname=linux-${_basekernel}
pkgver=${_basekernel}.4
pkgrel=1
arch=('i686' 'x86_64')
url="https://github.com/AstroProfundis/linux-linode"
license=(GPL2)
makedepends=(xmlto docbook-xsl kmod inetutils bc 'gcc>=4.9.0')
options=('!strip')
_ckpatchversion=1
_ckpatchname="patch-${_basekernel}-ck${_ckpatchversion}"
_bfqpath="http://algo.ing.unimo.it/people/paolo/disk_sched/patches/3.15.0-v7r5"
_gcc_patch="enable_additional_cpu_optimizations_for_gcc_v4.9+_kernel_v3.15+.patch"
source=("https://www.kernel.org/pub/linux/kernel/v3.x/${_srcname}.tar.xz"
        "https://www.kernel.org/pub/linux/kernel/v3.x/patch-${pkgver}.xz"
        "${_bfqpath}/0001-block-cgroups-kconfig-build-bits-for-BFQ-v7r5-3.15.patch"
        "${_bfqpath}/0002-block-introduce-the-BFQ-v7r5-I-O-sched-for-3.15.patch"
        "${_bfqpath}/0003-block-bfq-add-Early-Queue-Merge-EQM-to-BFQ-v7r5-for-3.15.0.patch"
        "http://ck.kolivas.org/patches/3.0/${_basekernel}/${_basekernel}-ck${_ckpatchversion}/${_ckpatchname}.bz2"
        "http://repo-ck.com/source/gcc_patch/${_gcc_patch}.gz"
        'config'
        'config.x86_64'
        'menu.lst'
        "preset"
        'https://projects.archlinux.org/svntogit/packages.git/plain/linux/trunk/change-default-console-loglevel.patch')
sha512sums=('d5dc477cad4584e56e2e2ef9e0950c2b22e76e5cf5090a896ba099cb7c5e5db1853a4aeb96b199189653dc66d461557e95198e37516a619f7ddc01ba6b308e02'
            '2e317f096bff6a01e7ee2a5ab72d228bbc174b3699a3c4c8c569519ddd0556c8a86711cb1f7b040c62ba2211e848f82e2802ca419c5969a1152e9f9d2f0dbb62'
            '009238524b9e45ba0b0e85b78c5e98c1a54276f5425ab3fc56850d3cd334d0bb4cf75e9d1749f0f0def59c8a1bcc0a12a64787698328e5fd51e0e8158d5c3dc4'
            'b499354d0244c024ff077aec7a6e9d2d05b4523ddc63fc2a0a688fcbf38ed58ffc30924ff4ba9116530afd9f5e39cfe4f12067d31d2f45a32fdd28f24c6ae07d'
            'cfaba4e000d89e8cb6722d06d75d26e5396cf799a3e4c6ea169de008d1f97e26449a1af0a1fd5389bea21de248c71ce653f805dd9dfcfc4c74a8f95521f1dd7f'
            'e018765a25c88d64a5b320030f252389e57fa04b736ad87675d86f1400534846dbf880912ca9efe395d9be56816d69b14411283da5c6dfe61b975caddd0beaa6'
            '7a0da65c7db087a489d1aabeebffed4ff26a37b2c544727216d208c857d65b190af81b8786af93ebaa55a48f55be5efd05ea8035b8aaaba8186e4fe37814e011'
            '2476edd764dabd1e592b0d2faa7a49392827d6e881c12ff38ddf96cc38c587d7742bae93dcb72d0d1f1388a20b2143348896c128d3e9b7413831b115e9959ef1'
            'd08e7179c83b7b3dd837f4fa9c757bef99dbedd1dd89ccc89f4257f5990b063cc312c9918aece8f3a922d199fbca6791c1609e95abe77ad5aa6f9233d67135f7'
            'f4191d63f71920a4c366c6c291205a780b7ddca952b4420dfb52b9e6d33c818b431830afe990df3ef3601458959a1b39b88c084b416a814cb7a957187670b733'
            'a0a78831075336edef0a8faa34fa550986c3c4d89a89f4f39d798da0211129dc90257d162bec2cdefabef2eb5886a710e70c72074b2f3016788861d05d1e2a1f'
            '502192b5ce94c8254205f5ddb85bf50c5f1e78c768817b10dca3a7716a8c59d5e093842631acb51e3805cbf85522e0a9200942656f11bbb4ea1b7d61e24ddd78')
pkgdesc="Kernel for Linode servers, with ck patchset"
depends=('coreutils' 'linux-firmware' 'kmod' 'mkinitcpio>=0.7')
provides=(linux)
conflicts=(grub grub-legacy)
backup=(etc/mkinitcpio.d/${pkgname}.preset boot/grub/menu.lst)
install=install

prepare() {
  cd "${srcdir}/${_srcname}"
  patch -p1 -i "${srcdir}/patch-${pkgver}"
  patch -Np1 -i "${srcdir}/change-default-console-loglevel.patch"

  # patch source with BFQ patches
  msg "Patching source with BFQ patches"
  for p in "${srcdir}"/000{1,2,3}-block*.patch; do
    patch -Np1 -i "$p"
  done

  # patch source with ck patchset
  # fix double name in EXTRAVERSION
  sed -i -re "s/^(.EXTRAVERSION).*$/\1 = /" "${srcdir}/${_ckpatchname}"
  msg "Patching source with ck1 including BFS v0.448"
  patch -Np1 -i "${srcdir}/${_ckpatchname}"

  # Patch source to enable more gcc CPU optimizatons via the make nconfig
  patch -Np1 -i "${srcdir}/${_gcc_patch}"

  # Clean tree and copy ARCH config over
  msg "Running make mrproper to clean source tree"
  make mrproper

  if [ "${CARCH}" = "x86_64" ]; then
    cat "${srcdir}/config.x86_64" > ./.config
  else
    cat "${srcdir}/config" > ./.config
  fi
  sed -i '2iexit 0' scripts/depmod.sh
  sed -i "s|CONFIG_LOCALVERSION=.*|CONFIG_LOCALVERSION=\"${_kernelname}-ck\"|g" ./.config
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
  find "${pkgdir}" -name '*.ko' -exec gzip -9 {} \;

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
