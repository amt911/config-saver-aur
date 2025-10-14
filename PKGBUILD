# Maintainer: amt911 <your.email@example.com>
pkgname=config-saver
pkgver=1.0.0
pkgrel=1
pkgdesc="Python CLI tool for validating, converting, and managing YAML/JSON configs, with tar compression utilities"
arch=('any')
url="https://github.com/amt911/config-saver"
license=('MIT')
depends=('python' 'python-pydantic' 'python-colorama' 'python-tqdm' 'python-yaml')
source=("$pkgname-$pkgver.tar.gz::$url/archive/refs/tags/$pkgver.tar.gz")
md5sums=('SKIP')  # Replace 'SKIP' with actual checksum for production

package() {
  cd "$srcdir/$pkgname-$pkgver"
  install -Dm644 README.md "$pkgdir/usr/share/doc/$pkgname/README.md"
  install -Dm755 -t "$pkgdir/usr/bin" __main__.py
  ln -sf /usr/bin/__main__.py "$pkgdir/usr/bin/config-saver"
}
