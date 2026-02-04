# Maintainer: amt911 <your.email@example.com>
pkgname=config-saver
pkgver=3.1.0
pkgrel=1
pkgdesc="Utility to back up and restore configuration files"
arch=('any')
url="https://github.com/amt911/config-saver"
license=('MIT')
depends=('python' 'python-pydantic' 'python-colorama' 'python-tqdm' 'python-yaml' 'python-rich')
makedepends=('python-build' 'python-installer' 'python-wheel' 'git')
source=("$pkgname-$pkgver.tar.gz::$url/archive/refs/tags/$pkgver.tar.gz")
sha256sums=('a23a02b7bf15f40290dca27d9f67654b69ab825f4a0166e4740b24db77054819')     # Generated using makepkg -g

build() {
    cd "$srcdir/$pkgname-$pkgver"
    python -m build --wheel
}

package() {
    cd "$srcdir/$pkgname-$pkgver"
    python -m installer --destdir="$pkgdir" dist/*.whl
    install -Dm644 README.md "$pkgdir/usr/share/doc/$pkgname/README.md"
    
    # Install all config files
    install -d "$pkgdir/etc/config-saver/configs"
    cp -r configs/* "$pkgdir/etc/config-saver/configs/"
    find "$pkgdir/etc/config-saver/configs" -type f -exec chmod 644 {} \;
    find "$pkgdir/etc/config-saver/configs" -type d -exec chmod 755 {} \;
    
    # Install systemd unit and timer (installed system-wide under /usr/lib/systemd/system)
    install -Dm644 contrib/systemd/system/config-saver@.service "$pkgdir/usr/lib/systemd/system/config-saver@.service"
    install -Dm644 contrib/systemd/system/config-saver@.timer "$pkgdir/usr/lib/systemd/system/config-saver@.timer"    
}
