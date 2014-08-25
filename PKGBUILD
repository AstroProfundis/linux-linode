# Maintainer: Allen Zhong <moeallenz@gmail.com>
# Maintainer: Yardena Cohen <yardenack at gmail dot com>
# tracks: https://projects.archlinux.org/svntogit/packages.git/log/trunk?h=packages/linux

pkgname=linux-linode
_basekernel=3.16
_kernelname=${pkgname#linux}
_srcname=linux-${_basekernel}
pkgver=${_basekernel}.1
pkgrel=2
arch=('x86_64')
url="https://github.com/AstroProfundis/linux-linode"
license=(GPL2)
makedepends=(xmlto docbook-xsl kmod inetutils bc 'gcc>=4.9.0')
options=('!strip')
_ckpatchversion=1
_ckpatchname="patch-${_basekernel}-ck${_ckpatchversion}"
_bfqpath="http://algo.ing.unimo.it/people/paolo/disk_sched/patches/3.16.0-v7r5"
_gcc_patch="enable_additional_cpu_optimizations_for_gcc_v4.9+_kernel_v3.15+.patch"
source=("https://www.kernel.org/pub/linux/kernel/v3.x/${_srcname}.tar.xz"
        "https://www.kernel.org/pub/linux/kernel/v3.x/patch-${pkgver}.xz"
        "${_bfqpath}/0001-block-cgroups-kconfig-build-bits-for-BFQ-v7r5-3.16.patch"
        "${_bfqpath}/0002-block-introduce-the-BFQ-v7r5-I-O-sched-for-3.16.patch"
        "${_bfqpath}/0003-block-bfq-add-Early-Queue-Merge-EQM-to-BFQ-v7r5-for-3.16.0.patch"
        "http://ck.kolivas.org/patches/3.0/${_basekernel}/${_basekernel}-ck${_ckpatchversion}/${_ckpatchname}.bz2"
        "http://repo-ck.com/source/gcc_patch/${_gcc_patch}.gz"
        'config'
        'menu.lst'
        "preset"
        'https://projects.archlinux.org/svntogit/packages.git/plain/linux/trunk/change-default-console-loglevel.patch')
sha512sums=('64033e741d3f42ed06010e63e69d66e1d3d9414ef20f76834508878d4218b72619541a18ef4fd377204af2859e52198310998109b20ed9b67722586b28c9a231'
            '01715f6c276611184c2a41d0736c4b91b155daa5c3b67e0f3646e5a795ec22b9ff8cd4db8508eac71b0eb6a3c194fbf02b7bdeeb09626931bf34ebc03d224026'
            'fd484e55bbff30f37e8d4b4c652bfce8d1e9a1bd2221e2d965ce18bd3eb929a3109fc02910535735b4af88f2f4700980f167dd7429b89a0fefdc24e778522473'
            '63db43b6ce1d43f9cf4bc1736378eae3a53b396d684eb40502889e272f291208bacbcfcc2bdbab84ece472133a3b35ee4cdb11bc0bb0986d7f9fbad0b40d8ba1'
            'b645b483623b2a5fa2806991e2f73df83faf4ecd5a331624e38c671ed462bf124b884caad2fc76c8eaa326e3645d2e3efe2d2c06da90410db4ff6798e3305326'
            'ee7afadb6fe4c97bbd233fda704d424e556d9e4f68a216acc8c37460926746184a628c65093b81243d9ea9367a798bc8fd7bcf80a11ee30e13d04d3e4e0d9b35'
            '7a0da65c7db087a489d1aabeebffed4ff26a37b2c544727216d208c857d65b190af81b8786af93ebaa55a48f55be5efd05ea8035b8aaaba8186e4fe37814e011'
            '30562a11556eecc49befd8499d8dd15dc39267bc56b91e09b0a434cd9a760c6ff4a1ff31c01a170cacc3f922f2ed496314a6fdab3b1a4a1e3cb30aa08887abc7'
            'f4191d63f71920a4c366c6c291205a780b7ddca952b4420dfb52b9e6d33c818b431830afe990df3ef3601458959a1b39b88c084b416a814cb7a957187670b733'
            'a0a78831075336edef0a8faa34fa550986c3c4d89a89f4f39d798da0211129dc90257d162bec2cdefabef2eb5886a710e70c72074b2f3016788861d05d1e2a1f'
            'd9d28e02e964704ea96645a5107f8b65cae5f4fb4f537e224e5e3d087fd296cb770c29ac76e0ce95d173bc420ea87fb8f187d616672a60a0cae618b0ef15b8c8')
pkgdesc="Kernel for Linode servers, with ck patchset"
depends=('coreutils' 'linux-firmware' 'kmod' 'mkinitcpio>=0.7')
provides=(linux)
conflicts=(grub grub-legacy)
backup=(etc/mkinitcpio.d/${pkgname}.preset boot/grub/menu.lst)
install=install

prepare() {
  cd "${srcdir}/${_srcname}"
  patch -p1 -i "${srcdir}/patch-${pkgver}"
  patch -p1 -i "${srcdir}/change-default-console-loglevel.patch"

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
