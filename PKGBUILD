# Maintainer: Mark Weiman <mark.weiman@markzz.com>
pkgname=ovmf-git-macboot
pkgver=r23112.018432f0ce
epoch=1
pkgrel=1
arch=('any')
pkgdesc="Tianocore UEFI firmware for qemu."
url="http://sourceforge.net/apps/mediawiki/tianocore/index.php?title=EDK2"
license=('custom')
makedepends=('git' 'python2' 'iasl' 'nasm' 'subversion' 'perl-libwww')
provides=('ovmf')
conflicts=('ovmf')
source=('edk2::git+https://github.com/tianocore/edk2.git#branch=master'
        'macboot.patch::https://github.com/gsomlo/edk2/compare/master...gls-miscopt.patch'
        'macboot-undo.patch'
)
options=(!makeflags)
_toolchain_opt=GCC5

pkgver() {
  cd "${srcdir}"/edk2
  printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

prepare() {
  cd "${srcdir}"
  # edk2 uses python everywhere, but expects python2
  mkdir -p bin
  ln -sf /usr/bin/python2 bin/python

  cd edk2
  patch -p1 < $srcdir/macboot.patch
  patch -p1 < $srcdir/macboot-undo.patch
}

build() {
  if [ "$CARCH" != "x86_64" ]; then
    error "This package must be built under the x86_64 architecture."
    false
  fi
  export PATH="${srcdir}/bin:$PATH"
  cd "${srcdir}/"edk2
  make -C BaseTools
  export EDK_TOOLS_PATH="${srcdir}"/edk2/BaseTools
  . edksetup.sh BaseTools

  # Set RELEASE target, toolchain and number of build threads
  sed "s|^TARGET[ ]*=.*|TARGET = RELEASE|; \
       s|TOOL_CHAIN_TAG[ ]*=.*|TOOL_CHAIN_TAG = ${_toolchain_opt}|; \
       s|MAX_CONCURRENT_THREAD_NUMBER[ ]*=.*|MAX_CONCURRENT_THREAD_NUMBER = $(nproc)|;" -i Conf/target.txt
  # Build OVMF for ia32
  #sed "s|^TARGET_ARCH[ ]*=.*|TARGET_ARCH = IA32|; \
  #     s|^ACTIVE_PLATFORM[ ]*=.*|ACTIVE_PLATFORM = OvmfPkg/OvmfPkgIa32.dsc|;" -i Conf/target.txt
  #./BaseTools/BinWrappers/PosixLike/build
  # Build OVMF for x64
  sed "s|^TARGET_ARCH[ ]*=.*|TARGET_ARCH = X64|; \
       s|^ACTIVE_PLATFORM[ ]*=.*|ACTIVE_PLATFORM = OvmfPkg/OvmfPkgX64.dsc|;" -i Conf/target.txt
  ./BaseTools/BinWrappers/PosixLike/build
}

package() {
  #install -D -m644 "${srcdir}"/edk2/Build/OvmfIa32/RELEASE_${_toolchain_opt}/FV/OVMF_CODE.fd "${pkgdir}"/usr/share/ovmf/ovmf_code_ia32.bin
  #install -D -m644 "${srcdir}"/edk2/Build/OvmfIa32/RELEASE_${_toolchain_opt}/FV/OVMF_VARS.fd "${pkgdir}"/usr/share/ovmf/ovmf_vars_ia32.bin
  install -D -m644 "${srcdir}"/edk2/Build/OvmfX64/RELEASE_${_toolchain_opt}/FV/OVMF_CODE.fd "${pkgdir}"/usr/share/ovmf/x64/OVMF_CODE.fd
  install -D -m644 "${srcdir}"/edk2/Build/OvmfX64/RELEASE_${_toolchain_opt}/FV/OVMF_VARS.fd "${pkgdir}"/usr/share/ovmf/x64/OVMF_VARS.fd
  install -D -m644 "${srcdir}"/edk2/Build/OvmfX64/RELEASE_${_toolchain_opt}/FV/OVMF.fd "${pkgdir}"/usr/share/ovmf/x64/OVMF.fd
  install -D -m644 "${srcdir}"/edk2/OvmfPkg/License.txt "${pkgdir}"/usr/share/licenses/ovmf/License.txt
}

# makepkg -g >> PKGBUILD
sha256sums=('SKIP'
            'bed5fec14dde30bbe0f5490bd824c101cd2bd50e8db576c688f63725e2db89b5'
            '1f8cb879bd1e8548ad9efa8dbbaa280e753130b2e5851ab69238218633a39bd3')
