# Maintainer: Allen Zhong <moeallenz@gmail.com>
# Maintainer: Yardena Cohen <yardenack at gmail dot com>
# tracks: https://projects.archlinux.org/svntogit/packages.git/log/trunk?h=packages/linux

pkgname=linux-linode
_basekernel=4.3
_kernelname=${pkgname#linux}
_srcname=linux-${_basekernel}
pkgver=${_basekernel}.3
pkgrel=1
arch=('x86_64')
url="https://github.com/AstroProfundis/linux-linode"
license=(GPL2)
makedepends=(xmlto docbook-xsl kmod inetutils bc 'gcc>=4.9.0')
options=('!strip')
_ckpatchversion=3
_ckpatchname="patch-${_basekernel}-ck${_ckpatchversion}"
_bfqver="v7r8"
_bfqpath="http://algo.ing.unimo.it/people/paolo/disk_sched/patches/${_basekernel}.0-${_bfqver}"
_gcc_patch="enable_additional_cpu_optimizations_for_gcc_v4.9+_kernel_v3.15+.patch"
source=("https://www.kernel.org/pub/linux/kernel/v4.x/${_srcname}.tar."{xz,sign}
        "https://www.kernel.org/pub/linux/kernel/v4.x/patch-${pkgver}."{xz,sign}
        "${_bfqpath}/0001-block-cgroups-kconfig-build-bits-for-BFQ-${_bfqver}-${_basekernel}.patch"
        "${_bfqpath}/0002-block-introduce-the-BFQ-${_bfqver}-I-O-sched-for-${_basekernel}.patch"
        "${_bfqpath}/0003-block-bfq-add-Early-Queue-Merge-EQM-to-BFQ-${_bfqver}-for-${_basekernel}.0.patch"
        "http://ck.kolivas.org/patches/4.0/${_basekernel}/${_basekernel}-ck${_ckpatchversion}/${_ckpatchname}.xz"
        "http://repo-ck.com/source/gcc_patch/${_gcc_patch}.gz"
        'config'
        'menu.lst'
        'preset')
sha512sums=('d25812043850530fdcfdb48523523ee980747f3c2c1266149330844dae2cba0d056d4ddd9c0f129f570f5d1f6df5c20385aec5f6a2e0755edc1e2f5f93e2c6bc'
            'SKIP'
            '1b9cc343a589a7cdaa66284b3d7129be9869195f5b839dac0222050af42dc7a7451d4e2bf176c1a9939d6572a44a9b6f4534b0c7a79032881a9bc8f8f56c9c69'
            'SKIP'
            'e31394f8addbfa7d46eaa6ebf4b848a0f13cbfc8d41f3741d2512c7728db839fa67f1a138cc0b5b88a85f0b7c285522e166ad2e349bebd9f4c3f4a187235b6e3'
            '593c8217c83a46faffa2b0639b3b8a17fd2cd68c160d716e9a931c6e114300384f68f6e98bb50a99d17bf3343ff694ce3edca627505813bb08a37f67ba525eee'
            '76e63b7c983d695a420fbe58a5d29063722a413a540160e63789d4dc4d6ac59c8cd15a6583ee4c8bcef965ab9512d4e69a4c1dac207bbe954b49f7356c90bdf4'
            'f13dd22d44ec51b03f9213ea7e75262197c9e9057d1acc7c2648ad802321a59b93dc2be75e402978d972dd2fa450699076cd51387ada928db93c4693ca9f960d'
            '62fdd5c0a060a051b64093d71fbb028781061ccb7a28c5b06739a0b24dac0945740d9b73ff170784f60005a589774bcc14f56523ec51557eb3a677f726ec34cf'
            '1d9bf4cbdff6c1b9e9a12654dc8c457762d64efacaab7b611525e59916b40d49fd93426f798d86d7d7eacef7744019380789b384ccc83b6df97919f031b29ecf'
            '810e1e1cd67b172f29db6c1a3caed8050bb3ff86db1d49abcebd76b5ea70622deefb587cc914d1819df78525ce433564f71533822244a286373fc0f7605e6dde'
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

  cat "${srcdir}/config" > ./.config
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
