# Maintainer: Allen Zhong <moeallenz@gmail.com>
# Maintainer: Yardena Cohen <yardenack at gmail dot com>
# tracks: https://projects.archlinux.org/svntogit/packages.git/log/trunk?h=packages/linux

pkgname=linux-linode
_basekernel=3.14
_kernelname=${pkgname#linux}
_srcname=linux-${_basekernel}
pkgver=${_basekernel}.4
pkgrel=3
arch=('i686' 'x86_64')
url="https://github.com/AstroProfundis/linux-linode"
license=(GPL2)
makedepends=(xmlto docbook-xsl kmod inetutils bc 'gcc>=4.9.0')
options=('!strip')
_ckpatchversion=1
_ckpatchname="patch-3.14-ck${_ckpatchversion}"
_gcc_patch="enable_additional_cpu_optimizations_for_gcc.patch"
source=("https://www.kernel.org/pub/linux/kernel/v3.x/${_srcname}.tar.xz"
        "https://www.kernel.org/pub/linux/kernel/v3.x/patch-${pkgver}.xz"
	"http://ck.kolivas.org/patches/3.0/3.14/3.14-ck${_ckpatchversion}/${_ckpatchname}.bz2"
	"http://repo-ck.com/source/gcc_patch/${_gcc_patch}.gz"
        'config'
        'config.x86_64'
        'menu.lst'
        "preset"
        'fsid-0.patch::https://projects.archlinux.org/svntogit/packages.git/plain/linux/trunk/0004-fs-Don-t-return-0-from-get_anon_bdev.patch'
	'https://projects.archlinux.org/svntogit/packages.git/plain/linux/trunk/0013-net-Start-with-correct-mac_len-in-skb_network_protocol.patch'
        'https://projects.archlinux.org/svntogit/packages.git/plain/linux/trunk/change-default-console-loglevel.patch')
sha512sums=('5730d83a7a81134c1e77c0bf89e42dee4f8251ad56c1ac2be20c59e26fdfaa7bea55f277e7af156b637f22e1584914a46089af85039177cb43485089c74ac26e'
            '61eca26d57f7d7caa78d157582d4b98fbba1c85af73f1773fb51eab3db4381de53f4fbfbc202083e45297c0b4487bc58880a518e7ee9c0d616cddf0b3909b303'
            'd745370376e660245e0a5cc4512f0c584a4c782ddb0747637d6ec60021d95afa09d5728f44756c48843b398ba3072823bea99b1713c0833c941a522da0b6f305'
            'a149a6c62c6654b5efe76308037d37b9421114e6257343a22eae84f4489e210927d1db16e5742fd04bb4eea5c8a9c1e541d9bbf82d495fffaae9d645a5fd5d3f'
            '4fee1ddd6f3ccff06ce4c6fb55534214973de51a89d97721a7f290f2982e6617025c622f5c4b0ee16611b4ef0926cf41014b2d30b19e405039c3dd2894a31dec'
            '0e1b844c2c8bfc887d8826937d2b3fc798a7658c625574494a4ca48de08797879428c932eb164177a724516ac3355e0cdddd4dfdbb84cd43d556e48309f97512'
            'f4191d63f71920a4c366c6c291205a780b7ddca952b4420dfb52b9e6d33c818b431830afe990df3ef3601458959a1b39b88c084b416a814cb7a957187670b733'
            'a0a78831075336edef0a8faa34fa550986c3c4d89a89f4f39d798da0211129dc90257d162bec2cdefabef2eb5886a710e70c72074b2f3016788861d05d1e2a1f'
            '61addb73b2811a369b72ea097e310c63853f219d1384ea0e2cd2bc7b8389163e2e5679a9a198fe0977017658c18a90be0f73aaf72e9b829cc4a802a4fe7cfba0'
            '064e679dc2df2f33cbfe3f719622976f827770e594d5ad28d5cf138482860ca1b9abf21614f9da070274297ea579bda32e2ac3a26e590b213a41116b4bf09d69'
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
  patch -Np1 -i "${srcdir}/fsid-0.patch"

  # fix tun/openvpn performance
  # https://bugs.archlinux.org/task/40089
  # https://bugzilla.kernel.org/show_bug.cgi?id=74051
  patch -Np1 -i "${srcdir}/0013-net-Start-with-correct-mac_len-in-skb_network_protocol.patch"

  # patch source with ck patchset
  # fix double name in EXTRAVERSION
  sed -i -re "s/^(.EXTRAVERSION).*$/\1 = /" "${srcdir}/${_ckpatchname}"
  msg "Patching source with ck1 including BFS v0.447"
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
