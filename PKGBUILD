# Maintainer: Allen Zhong <moeallenz@gmail.com>
# Maintainer: Yardena Cohen <yardenack at gmail dot com>
# tracks: https://projects.archlinux.org/svntogit/packages.git/log/trunk?h=packages/linux

pkgbase=linux-linode
_basekernel=4.14
_kernelname=${pkgname#linux}
_srcname=linux-${_basekernel}
pkgver=${_basekernel}.9
pkgrel=1
arch=('x86_64')
url="https://github.com/AstroProfundis/linux-linode"
license=(GPL2)
makedepends=(xmlto docbook-xsl kmod inetutils bc libelf)
options=('!strip')
_ckpatchversion=1
_ckpatchname="patch-${_basekernel}-ck${_ckpatchversion}"
_gcc_patch="enable_additional_cpu_optimizations_for_gcc_v4.9+_kernel_v4.13+.patch"
_preck2='https://github.com/ckolivas/linux/pull/7/commits'
source=("https://www.kernel.org/pub/linux/kernel/v4.x/${_srcname}.tar."{xz,sign}
        "https://www.kernel.org/pub/linux/kernel/v4.x/patch-${pkgver}."{xz,sign}
	"http://ck.kolivas.org/patches/4.0/${_basekernel}/${_basekernel}-ck${_ckpatchversion}/${_ckpatchname}.xz"
	"https://raw.githubusercontent.com/graysky2/kernel_gcc_patch/master/${_gcc_patch}"
	"https://raw.githubusercontent.com/dolohow/uksm/master/uksm-${_basekernel}.patch"
        'config'
        '08_linux_linode'
        '98-linux-linode.hook'
        '99-grub-ll.hook'
        'menu.lst'
        'preset'
	# pre ck2 patches
	"$_preck2/811cb391e71c1d60387dbbd6ae0bbc4e61f06acb.patch"
	"$_preck2/6bfb805cbac27b18fb4ad7537fe90dfc39e17f35.patch"
	"$_preck2/1588e6bf316231685204e358dfe172851b39fd1e.patch"
	"$_preck2/df2a75f4864b30011ab6a6f365d9378d8eafa53b.patch"
	"$_preck2/a79d648fcde72fc98048d4435bc86864a59fd01b.patch"
	"0001-add-sysctl-to-disallow-unprivileged-CLONE_NEWUSER-by.patch"
	"0001-e1000e-Fix-e1000_check_for_copper_link_ich8lan-retur.patch"
	"0002-dccp-CVE-2017-8824-use-after-free-in-DCCP-code.patch"
	"0001-Revert-xfrm-Fix-stack-out-of-bounds-read-in-xfrm_sta.patch"
	"0002-xfrm-Fix-stack-out-of-bounds-read-on-socket-policy-l.patch"
	"0003-cgroup-fix-css_task_iter-crash-on-CSS_TASK_ITER_PROC.patch"
	"0001-ALSA-usb-audio-Fix-the-missing-ctl-name-suffix-at-pa.patch"
	)
sha512sums=('77e43a02d766c3d73b7e25c4aafb2e931d6b16e870510c22cef0cdb05c3acb7952b8908ebad12b10ef982c6efbe286364b1544586e715cf38390e483927904d8'
            'SKIP'
            '8085944e5b8d49b07075776a1604a25ba87efe8b5a8a1a276a75dc47a87de4e487e72d9ace605166be977340b81a10a3cbc25c84b857509da8bba6d9184513fa'
            'SKIP'
            '5e81d6a8319d030572ad5b6379bc43f2f5f98b6e4f050b1285bd6004516d1bf2e8eee8d3425a09317e15309cf030da2534417265b474aaab80e72e22c04741a2'
            '5ca7ae20245a54caa71fb570d971d6872d4e888f35c6123b93fbca16baf9a0e2500d6ec931f3906e4faecaaca9cad0d593694d9cab617efd0cb7b5fc09c0fa48'
            '44b31276d4d712e4e1e1455e128daa079ddd9d72a4620289607faf6134a225737004e8742de79e0283e98ef2d4f746f075e041870d37eab191c93c566f945c7f'
            'fb6333d857a49d7d3289c699e5674765f536de5f5fc14785574cc46fbad604d096ed05055aae1ef78537d7d3d6a26d87a5254d665eddf3d774c0e3b6d66e8995'
            '7a80f858c32a9dd62f43aba0b7119a1196869216117164bcde24ab46022e8a1bbe27821faa26ca690a1633a5a9fe324e98e5cdf14f37591d569cbc71f542482d'
            'c57a6c8d9978cb6a1034bed33ba5e06bef9b134f22113761798d4fa46e8091e7b0bd26f3a14d79122ba780b2f7a93ca26850f4da6a654f81b34cc79c242f683f'
            'db9080b2548e4dcd61eaaf20cd7d37cbbc8c204ce85a2e3408d0671f6b26010f77a61affd2c77e809768714eca29d3afb64765a3f2099317a2c928eff3feb4cf'
            '73cb4c064d8942fddaac48158b7e77d19afc1cb61f83936f21832ba7d7266ccfd3021114252edd5cec5542096204f48cf30544fd6bffff79bc94d96fabe74f52'
            '62870a08f000abfe8eb1f50271afdf04686af108554f7629dc5e1d7610ad14bdc9cd14d2609270b83f9edb745a520b81fa7bfb92ebcc28a146df040c895b549b'
            '16439b1fb759ba7c4a89a3a7d69b89d5cfc594b1b47c30cfaae4942d806f47373c1c8d93a2fe9afd151bd8a2062806572fd16aa975f6a8876439c760c21b8acb'
            '12a871b010ea01d2462337701aac8e6ecdc10a7130f6b69feb394825ac71633fecf0a0917d499011ed34baae733e9d0957b0d99f33f06988306daa88383dc788'
            '63e92a677e5a093af6f414a730c38fdd3f388e00e608fe2f1466e433793963c36192bf8eeae07e7d49cdf4a7f28540e2348285126d1203ecf2db8f0e7d875bfd'
            '92c997ee4d317602dafcfb2a916d45f7f1c5039cc2dbf09d948d945c4a561b8552e0c4efd590ea7d97ff7d9539f7b14472fa63b5b47ef1a694157b46385a70c6'
            '43278da2ec74abee03d41f453f894c0a7bc1dfcd8c0382087d52e36f9dcf83f78a9067339d74b2a028cc0b09d5e0865ec291e159a5d7d2b1909d1e43f574981f'
            '6fd42090bd39228ac625d0c2074ae55ac3e8368de63f550951c3ac6e6bfdbaf47ab67e018e21890b8ad75bb6706eff5dce05070ad6c281ecedf2a353d8871d96'
            '4b461e3f194fd11ec4321cfbe63dbc5f59c2ed0ee71cae5753b64761c6cc816e28fe89f9c472f92a6cf22557ab88243c16f7f2d2e754ba0b47f82608dc9ddc25'
            '93131d8ad8b118a1c1bcabce357ba7e61233c99188f2d0123977c436e2932555bde4e19de4ca63ac27c6e9b26d8373fb99b52db18b7518122433616d7060082d'
            '973bf63857968e76d15286aea5add9589e3248b7b70da25629b91618cfdbbd5784cd0d97daccb3168fd369adb41ebd5768788ad25dc54b7a5c0b9f16e07a9d38'
            '39bf2a3eeca5efce6c8214c49fba001a767fa3c94157255451a8c4739a3adeef74f2644a2ab6a7a423a65e76466c02d7c1f124cdddcfce37145fc3be92d8fa6c'
            '5ad03cf5b0acfa1ca554a0462d83c0be8ddf9974d7248b9ff9a516e68dad0425a205b224ca4cf680428feedcc4e7a1153d5aa12a12abfa96a503e9a0d65c712a'
            '6f01527ed7a25bf78d7c0515c8726a47bef0e99cf1c90f07d0f6a59e70f12e04260eed2c4ca831fb339d3a027085bcd73f8ab3b1968582f75b688823b18716cb')
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
  patch -p1 -i "${srcdir}/patch-${pkgver}"

  # disable USER_NS for non-root users by default
  patch -Np1 -i ../0001-add-sysctl-to-disallow-unprivileged-CLONE_NEWUSER-by.patch

  # https://bugs.archlinux.org/task/56575
  patch -Np1 -i ../0001-e1000e-Fix-e1000_check_for_copper_link_ich8lan-retur.patch

  # https://nvd.nist.gov/vuln/detail/CVE-2017-8824
  patch -Np1 -i ../0002-dccp-CVE-2017-8824-use-after-free-in-DCCP-code.patch

  # https://bugs.archlinux.org/task/56605
  patch -Np1 -i ../0001-Revert-xfrm-Fix-stack-out-of-bounds-read-in-xfrm_sta.patch
  patch -Np1 -i ../0002-xfrm-Fix-stack-out-of-bounds-read-on-socket-policy-l.patch

  # https://bugs.archlinux.org/task/56846
  patch -Np1 -i ../0003-cgroup-fix-css_task_iter-crash-on-CSS_TASK_ITER_PROC.patch

  # https://bugs.archlinux.org/task/56830
  patch -Np1 -i ../0001-ALSA-usb-audio-Fix-the-missing-ctl-name-suffix-at-pa.patch

  # fix naming schema in EXTRAVERSION of ck patch set
  sed -i -re "s/^(.EXTRAVERSION).*$/\1 = /" ../${_ckpatchname}

  # Patch source with ck patchset
  msg "Applying ck patchset..."
  patch -Np1 -i ../${_ckpatchname}
  patch -Np1 -i ../811cb391e71c1d60387dbbd6ae0bbc4e61f06acb.patch
  patch -Np1 -i ../6bfb805cbac27b18fb4ad7537fe90dfc39e17f35.patch
  patch -Np1 -i ../1588e6bf316231685204e358dfe172851b39fd1e.patch
  patch -Np1 -i ../df2a75f4864b30011ab6a6f365d9378d8eafa53b.patch
  patch -Np1 -i ../a79d648fcde72fc98048d4435bc86864a59fd01b.patch

  # Patch source with UKSM patchset
  msg "Applying UKSM patchset..."
  patch -Np1 -i ../uksm-${_basekernel}.patch

  # Patch source to unlock additional gcc CPU optimizatons
  # https://github.com/graysky2/kernel_gcc_patch
  msg "Enabling additional CPU optimizations..."
  patch -Np1 -i ../${_gcc_patch}

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
  _kernver="$(make LOCALVERSION= kernelrelease)"
  emdir="extramodules-${_basekernel}${_kernelname:--ARCH}"
  mkdir -p "${pkgdir}"/{usr/lib/modules/"$emdir",boot/grub}
  make LOCALVERSION= INSTALL_MOD_PATH="${pkgdir}/usr" modules_install
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
