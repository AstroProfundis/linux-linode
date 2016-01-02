# Maintainer: Allen Zhong <moeallenz@gmail.com>
# Maintainer: Yardena Cohen <yardenack at gmail dot com>
# tracks: https://projects.archlinux.org/svntogit/packages.git/log/trunk?h=packages/linux

pkgname=linux-linode
_basekernel=4.1
_kernelname=${pkgname#linux}
_srcname=linux-${_basekernel}
pkgver=${_basekernel}.6
pkgrel=1
arch=('x86_64')
url="https://github.com/AstroProfundis/linux-linode"
license=(GPL2)
makedepends=(xmlto docbook-xsl kmod inetutils bc 'gcc>=4.9.0')
options=('!strip')
_ckpatchversion=1
_ckpatchname="patch-${_basekernel}-ck${_ckpatchversion}"
_bfqver="v7r8"
_bfqpath="http://algo.ing.unimo.it/people/paolo/disk_sched/patches/${_basekernel}.0-${_bfqver}"
_gcc_patch="enable_additional_cpu_optimizations_for_gcc_v4.9+_kernel_v3.15+.patch"
source=("https://www.kernel.org/pub/linux/kernel/v4.x/${_srcname}.tar."{xz,sign}
        "https://www.kernel.org/pub/linux/kernel/v4.x/patch-${pkgver}."{xz,sign}
        "${_bfqpath}/0001-block-cgroups-kconfig-build-bits-for-BFQ-${_bfqver}-${_basekernel}.patch"
        "${_bfqpath}/0002-block-introduce-the-BFQ-${_bfqver}-I-O-sched-for-${_basekernel}.patch"
        "${_bfqpath}/0003-block-bfq-add-Early-Queue-Merge-EQM-to-BFQ-${_bfqver}-for-${_basekernel}.0.patch"
        "http://ck.kolivas.org/patches/4.0/${_basekernel}/${_basekernel}-ck${_ckpatchversion}/${_ckpatchname}.bz2"
        "http://ck.kolivas.org/patches/bfs/4.0/${_basekernel}/pending/bfs462-rtmn-fix.patch"
        "http://ck.kolivas.org/patches/bfs/4.0/${_basekernel}/pending/bfs462-update_inittask.patch"
        "http://repo-ck.com/source/gcc_patch/${_gcc_patch}.gz"
        'config'
        'menu.lst'
        'preset')
sha512sums=('168ef84a4e67619f9f53f3574e438542a5747f9b43443363cb83597fcdac9f40d201625c66e375a23226745eaada9176eb006ca023613cec089349e91751f3c0'
            'SKIP'
            '1b725c2051d7dd9129c927fdb90d8d7a2e13149a2dd1175c5efacfc516e664af3647bca83d847197f8c19ef140bf07ea856868e27bef4a1cad9607e40b527424'
            'SKIP'
            '383cd020ab882389731ef78abca727eccc8247ed82b95c89df93d7065bfde093b82e32190ad1fb29b37de35eb20b40339f2c02ad694a3978884255b193f5bc1a'
            'f7bcb50e7de166e0d89194a3cad1feae99c4a5a9918e8af691d7635ed8ef64762ff2af4702dc6ba0eef0fc01ad75173abddbf01ae89bc6e03ace5e54f4098b12'
            '1db70764577d3e8d5e65351bdef7f2cf61d2546138a6342c4bf4e5e6738b8e06b5291a9a0c12f9fc2c8cb620048006d05474cf75902cb26e7504038150cf0a44'
            '0679eab9ac50714beff50bb4551bdd01d29f3287e4438a1d88ce74ce11286dfb1ec8d2c8f07d0dd765ed3171488b52320e976e50eeb47c8da9b18ad8d7b3a233'
            '47e4134fa35a1596b390bdc7eadc59b75b88594e84b54a71a0fd7886bbef083c174ca5627bd2a899966eddd35b676df735b0304e737218040ab8edf72f333ef2'
            '623c4fc879b6bb17f1640caa268bf19cf65fbfa6c5910bbb5fdd44f1013913e5728d80fa037f33288a08ac56a23ee326951a15822a871dee2fb3d775ced9357e'
            '76bf6a9f22b023ab8f780884f595dac1801d150ecd94f88da229c5c9ea98d2c3ef8add01ff7e18e4cbbfa5e6e9c022c4042ee62c779a8485203c1b0e082b8ccc'
            'a5e480a7f5f7e6aedf84ece3701fda7bf0c3487dadd1695179a12e5818e153eae3d3515035a6e06eaacfb039b26b080f2134900e07d9b3a3639f6b23092a069a'
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
  patch -Np1 -i "${srcdir}/bfs462-rtmn-fix.patch"
  patch -Np1 -i "${srcdir}/bfs462-update_inittask.patch"

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
