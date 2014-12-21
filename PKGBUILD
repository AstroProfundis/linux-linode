# Maintainer: Allen Zhong <moeallenz@gmail.com>
# Maintainer: Yardena Cohen <yardenack at gmail dot com>
# tracks: https://projects.archlinux.org/svntogit/packages.git/log/trunk?h=packages/linux

pkgname=linux-linode
_basekernel=3.18
_kernelname=${pkgname#linux}
_srcname=linux-${_basekernel}
pkgver=${_basekernel}.1
pkgrel=1
arch=('x86_64')
url="https://github.com/AstroProfundis/linux-linode"
license=(GPL2)
makedepends=(xmlto docbook-xsl kmod inetutils bc 'gcc>=4.9.0')
options=('!strip')
_ckpatchversion=1
_ckpatchname="patch-${_basekernel}-ck${_ckpatchversion}"
_bfqpath="http://algo.ing.unimo.it/people/paolo/disk_sched/patches/3.18.0-v7r6"
_gcc_patch="enable_additional_cpu_optimizations_for_gcc_v4.9+_kernel_v3.15+.patch"
source=("https://www.kernel.org/pub/linux/kernel/v3.x/${_srcname}.tar.xz"
        "https://www.kernel.org/pub/linux/kernel/v3.x/patch-${pkgver}.xz"
        "${_bfqpath}/0001-block-cgroups-kconfig-build-bits-for-BFQ-v7r6-3.18.patch"
        "${_bfqpath}/0002-block-introduce-the-BFQ-v7r6-I-O-sched-for-3.18.patch"
        "${_bfqpath}/0003-block-bfq-add-Early-Queue-Merge-EQM-to-BFQ-v7r6-for-3.18.0.patch"
        "http://ck.kolivas.org/patches/3.0/${_basekernel}/${_basekernel}-ck${_ckpatchversion}/${_ckpatchname}.bz2"
        "http://repo-ck.com/source/gcc_patch/${_gcc_patch}.gz"
        'config'
        'menu.lst'
        'preset')
sha512sums=('2f0b72466e9bc538a675738aa416573d41bbbd7e3e2ffd5b5b127afde609ebc278cec5a3c37e73479607e957c13f1b4ed9782a3795e0dcc2cf8e550228594009'
            '3c83cbf12a0c68607405ab253a85ef792d00bdca563a538069a5d4628d31dd0391ea4b4302cdd38cf5f5d6c7455df94ecd5d2ba77a5148e35d646c03bec3a31f'
            '58b7aa96279ac5989a74f5eb63bd6b2c827bb29a981452617cacc3b5fd5d84b22f65af07573fa38cd3bb7ca0e2c0965290f458317e2988744b90bd1e29a43f04'
            '2ddaa3c6ff9490016becedf241b8522edcbcadc80f7652478270d94a12ac8ee9dc8f47ad7fd19f7fcc3f21f70a492ad5aab548370f3994c402bdb3c1d79373df'
            '5a2c76366eefd263f18f484ab3921cbb140c62d4636d1703523e329a3b893d4c310dd7bf0f04b9953313bc7c83fd4b3e562b4edd9bd09797b7e57d87d69dc6cc'
            'c1ffbbc641cf9ef3c666a1fe5ddff1ab9ed02eb2d8d229d5ce4c11487609ef81beee456c211a658355c2061a108042929b9ad62914395a22077754ade4eb6a23'
            'bdf5fbd0f7b674be9e4c5004d54bd91c7cc82f80b0ad2c534b62bd39b52b1ad6a2694bd645a208dcd1eb1e03e64d60e71bf605e096c69e497cdeda00534cbcc7'
            'e3b4d54f0a0c0dddb15d1aac512887a823b05836b7267c476ce761cfb6216c9f3c72121443b0e4c41f7c0f45a9e32c83c5d834c62245e163e7b49e3752464a62'
            '0ddff435474213f6c6e62576b6b3042aeffd1df84c47ca6195714a92c2655c33c56ce211c14934f4268afdf246077e1ea04e0958efc263c71aabfb497481022c'
            'a0a78831075336edef0a8faa34fa550986c3c4d89a89f4f39d798da0211129dc90257d162bec2cdefabef2eb5886a710e70c72074b2f3016788861d05d1e2a1f')
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

  cat "${srcdir}/config" > ./.config
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
