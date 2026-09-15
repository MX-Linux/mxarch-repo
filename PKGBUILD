# Maintainer: Adrian <adrian@mxlinux.org>
pkgname=mxarch-repo
pkgver=20260915
pkgrel=2
pkgdesc="GPG key and mirror list for the MX Linux Arch repository (mxarch)"
arch=('any')
url="https://arch.mxrepo.com/"
license=('GPL-3.0-or-later')
backup=('etc/pacman.d/mxarch-mirrorlist')
install="$pkgname.install"
source=('mxarch.gpg'
        'mxarch-trusted'
        'mxarch-mirrorlist'
        'mxarch-enable')
sha256sums=('47b78f0737ff2a32800cfa53ccae7155b1272536d975c6a790845716bd85f7ff'
            '05a91e8443e7b11ba4e23442d330862260554accdeec9d690d01042b32ac05f3'
            '23da909be4603b561e55433998ee9d79d42dab32444c6094fac608d39bbd9733'
            'bb20c9105fb994f322acd7c5ec78fd45f34424a6cf1b88818e0d7ea04e2e939b')

package() {
    install -Dm644 mxarch.gpg "$pkgdir/usr/share/pacman/keyrings/mxarch.gpg"
    install -Dm644 mxarch-trusted "$pkgdir/usr/share/pacman/keyrings/mxarch-trusted"
    install -Dm644 mxarch-mirrorlist "$pkgdir/etc/pacman.d/mxarch-mirrorlist"
    install -Dm755 mxarch-enable "$pkgdir/usr/bin/mxarch-enable"
}
