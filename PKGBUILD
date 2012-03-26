# Maintainer: Keshav P R <(the.ridikulus.rat) (aatt) (gemmaeiil) (ddoott) (ccoomm)>

# _GNU_EFI_LIB_DIR="/usr/lib"

_actualname="refind"
_pkgname="${_actualname}-x86_64"
pkgname="${_pkgname}-git"

pkgver="20120326"
pkgrel="1"
pkgdesc="Rod Smith's fork of rEFIt (U)EFI Boot Manager - GIT Version"
url="http://www.rodsbooks.com/refind/index.html"
arch=('any')
license=('GPL3' 'custom')

makedepends=('gnu-efi')
depends=('dosfstools' 'efibootmgr')
optdepends=('mactel-boot: For bless command in Apple Mac systems')

conflicts=("${_pkgname}")
provides=("${_pkgname}")

backup=('boot/efi/efi/arch_refind/refind.conf'
        'boot/efi/efi/arch_refind/refind_linux.conf')

options=('!strip' 'docs')
install="${_pkgname}.install"

source=('refind_linux.conf')

sha256sums=('9aac6e65018965ba182ec2d246d37fc5f9269ae96504956d8a51355c3ba1b62f')

_gitroot="git://git.code.sf.net/p/refind/code"
_gitname="${_actualname}"
_gitbranch="master"

_update_git() {
	
	cd "${srcdir}/"
	
	msg "Connecting to GIT server...."
	
	if [[ -d "${srcdir}/${_gitname}/" ]]; then
		cd "${srcdir}/${_gitname}/"
		git reset --hard
		git fetch
		git checkout "${_gitbranch}"
		git merge "remotes/origin/${_gitbranch}"
		msg "The local GIT repo has been updated."
	else
		git clone "${_gitroot}" "${_gitname}"
		cd "${srcdir}/${_gitname}/"
		git checkout "${_gitbranch}"
		msg "GIT checkout done or server timeout"
	fi
	
	echo
	
}

build() {
	
	if [[ "${CARCH}" != "x86_64" ]]; then
		echo "${pkgname} package can be built only in a x86_64 system. Exiting."
		exit 1
	else
		_update_git
		echo
	fi
	
	
	rm -rf "${srcdir}/${_gitname}_build/" || true
	cp -r "${srcdir}/${_gitname}" "${srcdir}/${_gitname}_build"
	
	cd "${srcdir}/${_gitname}_build"
	
	make
	
}

package() {
	
	cd "${srcdir}/${_gitname}_build"
	
	## install the rEFInd x86_64 UEFI app
	install -d "${pkgdir}/boot/efi/efi/arch_refind/"
	install -D -m0644 "${srcdir}/${_gitname}_build/refind/refind.efi" "${pkgdir}/boot/efi/efi/arch_refind/refindx64.efi"
	
	## install the rEFInd config file
	install -D -m0644 "${srcdir}/${_gitname}_build/refind.conf-sample" "${pkgdir}/boot/efi/efi/arch_refind/refind.conf"
	install -D -m0644 "${srcdir}/refind_linux.conf" "${pkgdir}/boot/efi/efi/arch_refind/refind_linux.conf"
	
	## install the rEFInd icons
	install -d "${pkgdir}/boot/efi/efi/arch_refind/icons/"
	install -D -m0644 "${srcdir}/${_gitname}_build/icons"/* "${pkgdir}/boot/efi/efi/arch_refind/icons/"
	
	## install the rEFInd docs
	install -d "${pkgdir}/usr/share/refind/docs/html/"
	install -d "${pkgdir}/usr/share/refind/docs/Styles/"
	install -D -m0644 "${srcdir}/${_gitname}_build/docs/refind"/* "${pkgdir}/usr/share/refind/docs/html/"
	install -D -m0644 "${srcdir}/${_gitname}_build/docs/Styles"/* "${pkgdir}/usr/share/refind/docs/Styles/"
	install -D -m0644 "${srcdir}/${_gitname}_build/README.txt" "${pkgdir}/usr/share/refind/docs/README.txt"
	install -D -m0644 "${srcdir}/${_gitname}_build/NEWS.txt" "${pkgdir}/usr/share/refind/docs/NEWS.txt"
	rm -f "${pkgdir}/usr/share/refind/docs/html/.DS_Store" || true
	
	## rename linux.conf to refind_linux.conf
	sed 's|linux.conf|refind_linux.conf|g' -i "${pkgdir}/usr/share/refind/docs/html"/*.html
	sed 's|linux.conf|refind_linux.conf|g' -i "${pkgdir}/usr/share/refind/docs"/*.txt
	
	## install the rEFIt license file, since rEFInd is a fork of rEFIt
	install -d "${pkgdir}/usr/share/licenses/refind/"
	install -D -m0644 "${srcdir}/${_gitname}_build/LICENSE.txt" "${pkgdir}/usr/share/licenses/refind/LICENSE"
	
}
