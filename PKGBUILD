# Maintainer: Yardena Cohen <yardenack at gmail dot com>
# tracks: https://projects.archlinux.org/svntogit/packages.git/log/trunk?h=packages/linux

pkgname=linux-linode
_basekernel=5.0.12
_hardenedver=a
pkgrel=1
_kernelname=${pkgname#linux}
_srcname=linux-${_basekernel}
_patchname=linux-hardened
pkgver=${_basekernel}.${_hardenedver}
arch=('x86_64')
url="https://github.com/yardenac/linux-linode"
license=(GPL2)
makedepends=(xmlto docbook-xsl kmod inetutils bc libelf)
options=('!strip')
source=("https://www.kernel.org/pub/linux/kernel/v5.x/${_srcname}.tar."{xz,sign}
        "https://github.com/anthraxx/${_patchname}/releases/download/${pkgver}/${_patchname}-${pkgver}.patch"{,.sig}
        'config'
        '08_linux_linode'
        '98-linux-linode.hook'
        '99-grub-ll.hook'
        'menu.lst'
        'preset')
sha512sums=('cf3b2607d9f4fac1e5724c71fdb7cbbcd86711ddad45a045f805ac9250c759ec5589e36078275e5d948c73a34ccb8f2af6f16027a5646755b8a49ca13047c810' 'SKIP'
            '6b3140f5107001ef39e62bdfbf08d449dfc7edf7814f3a79cfad3a97f186c1eff6439c41b517d6d24fb05cf43d3b775d0dbd2f6d1a77233e4e7bc7730cdfcb19' 'SKIP'
            'e3b7ebfa878b89ea8ab551b0d3fd019c6585a739c8c782c6888d48b3c2f59d6c88284d7b3e772d449d0c2c7ff2a0c3c60b189fb926c316ae70eb2a6b7f0a9095'
            '7a80f858c32a9dd62f43aba0b7119a1196869216117164bcde24ab46022e8a1bbe27821faa26ca690a1633a5a9fe324e98e5cdf14f37591d569cbc71f542482d'
            'c57a6c8d9978cb6a1034bed33ba5e06bef9b134f22113761798d4fa46e8091e7b0bd26f3a14d79122ba780b2f7a93ca26850f4da6a654f81b34cc79c242f683f'
            'db9080b2548e4dcd61eaaf20cd7d37cbbc8c204ce85a2e3408d0671f6b26010f77a61affd2c77e809768714eca29d3afb64765a3f2099317a2c928eff3feb4cf'
            '73cb4c064d8942fddaac48158b7e77d19afc1cb61f83936f21832ba7d7266ccfd3021114252edd5cec5542096204f48cf30544fd6bffff79bc94d96fabe74f52'
            '62870a08f000abfe8eb1f50271afdf04686af108554f7629dc5e1d7610ad14bdc9cd14d2609270b83f9edb745a520b81fa7bfb92ebcc28a146df040c895b549b')
sha256sums=('1e0278196679d04980b9370a1fdab9065946cf324b859b91bb59e689201508f0' 'SKIP'
            '710ea9f7aab089ac15012ecd09254241301ae336ebd41778cd7f7ad86e787b6f' 'SKIP'
            'd13d17939e832cd7dafb9ace24a89f27bcebc184a7d0ab05233d3e670052674c'
            '7d56a81083d1468d90ebec97a44ec44f80f8cb87bd506ed1918d6664d6309ad5'
            '3efa91fcb4698bde0598678bbf9a4a747c011823af82704eed2c146ed7cd9734'
            '368fb58e7aa465f597e9a72da4b6eea4183c1a85242173412d54ad18d10d8fb3'
            'a055b6005a324240b35c416d9d08fba21c5f614eefb46f244035d04bf085224f'
            '29fa2c1ea75f55a61276496507b788b1a8bde1d7c16bee4f525651db34076e46')
md5sums=('db9c5bea726e4a3a4f2e41e15dd27583' 'SKIP'
         '22aa8be906e07eee6af53a86ab92c5b8' 'SKIP'
         'ca8f778856d8b9cf544cf2cdc4c8349b'
         'bc30565cf444b710c252675cf65fb46e'
         'c52c29a8502f6c75e309208f0afab11f'
         '625481f015365febcd65aa136ee555f9'
         '15fae1e2da901b937d284890fd91cfab'
         'e1b5255cfbf5f8ca79b4b4568feec801')
validpgpkeys=(
              'ABAF11C65A2970B130ABE3C479BE3E4300411886' # Linux Torvalds
              '647F28654894E3BD457199BE38DBBDC86092693E' # Greg Kroah-Hartman
              'E240B57E2C4630BA768E2F26FC1B547C8D8172C8' # Levente Polyak
)
pkgdesc="Kernel for Linode servers"
depends=(coreutils linux-firmware kmod mkinitcpio grub)
provides=(linux)
conflicts=(grub-legacy)
backup=(etc/mkinitcpio.d/${pkgname}.preset boot/grub/menu.lst)
install=install

prepare() {
  cd "${srcdir}/${_srcname}"
  patch -p1 -i "${srcdir}/${_patchname}-${pkgver}.patch"
  sed -e "/^EXTRAVERSION =/s/=.*/= .${_hardenedver}/" -i Makefile
  scripts/setlocalversion --save-scmversion
  cp "${srcdir}/config" .config
  make oldconfig
  make -s kernelrelease > ../version
}

build() {
  cd "${srcdir}/${_srcname}"
  [[ "$MAKEFLAGS" =~ -j[0-9]* ]] || MAKEFLAGS+=" -j$(nproc)"
  ionice -c 3 nice -n 16 make ${MAKEFLAGS} bzImage modules
}

package() {
  cd "${srcdir}/${_srcname}"
  local _kernver="$(<${srcdir}/version)"
  emdir="extramodules${_kernelname}"
  mkdir -p "${pkgdir}"/{usr/lib/modules/"$emdir",boot/grub}
  make INSTALL_MOD_PATH="${pkgdir}/usr" modules_install
  rm -rf "${pkgdir}"/usr/lib/modules/${_kernver}/{source,build}
  install -D -m644 "$(make -s image_name)"          "${pkgdir}/boot/vmlinuzll-${pkgname}"
  install -D -m644 vmlinux "${pkgdir}/usr/lib/modules/${_kernver}/build/vmlinux"
  install -D -m644 "${srcdir}/preset" "${pkgdir}/etc/mkinitcpio.d/${pkgname}.preset"
  install -D -m644 "${srcdir}/98-linux-linode.hook" "${pkgdir}/usr/share/libalpm/hooks/98-linux-linode.hook"
  install -D -m644 "${srcdir}/99-grub-ll.hook"      "${pkgdir}/usr/share/libalpm/hooks/99-grub-ll.hook"
  install -D -m755 "${srcdir}/08_linux_linode" "${pkgdir}/etc/grub.d/08_linux_linode"
  install -D -m644 "${srcdir}/version"              "${pkgdir}/usr/lib/modules/${emdir}/version"
  sed \
    -e  "s/KERNEL_NAME=.*/KERNEL_NAME=${_kernelname}/" \
    -i "${startdir}/install"
  sed \
    -e "s|ALL_kver=.*|ALL_kver=\"/boot/vmlinuzll-${pkgname}\"|" \
    -e "s|default_image=.*|default_image=\"/boot/initramfs-${pkgname}.img\"|" \
    -e "s|fallback_image=.*|fallback_image=\"/boot/initramfs-${pkgname}-fallback.img\"|" \
    -i "${pkgdir}/etc/mkinitcpio.d/${pkgname}.preset"
  ln -s "../${emdir}" "${pkgdir}/usr/lib/modules/${_kernver}/extramodules"
  sed "s/%VER%/${pkgver}-${pkgrel}/ig" "${srcdir}/menu.lst" > "${pkgdir}/boot/grub/menu.lst"
  chmod -Rc u=rwX,go=rX "$pkgdir"
}
