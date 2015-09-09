# Maintainer: Danil Semelenov <mail@danil.mobi>
pkgname=pmenu
pkgver=0.1.0
pkgrel=1
pkgdesc="Dynamic menu like dmenu for terminal written in Python without dependencies with optional sorting by usage and application launcher."
url=https://gitlab.com/sgtpep/pmenu
arch=(any)
license=(GPL3)
depends=(python)
makedepends=(git)
source=(git+https://gitlab.com/sgtpep/pmenu.git)
md5sums=('SKIP')

package() {
  install -D -t "$pkgdir/usr/bin" "$srcdir/$pkgname/pmenu"{,-run}
}
