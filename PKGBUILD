# Maintainer: amt911 <your.email@example.com>
pkgname=config-saver
pkgver=3.0.1
pkgrel=1
pkgdesc="Utility to back up and restore configuration files"
arch=('any')
url="https://github.com/amt911/config-saver"
license=('MIT')
depends=('python' 'python-pydantic' 'python-colorama' 'python-tqdm' 'python-yaml' 'python-rich')
makedepends=('python-build' 'python-installer' 'python-wheel' 'git')
source=("$pkgname-$pkgver.tar.gz::$url/archive/refs/tags/$pkgver.tar.gz")
sha256sums=('54e7bb90bff67fd205e94e6bcc86477a8a5b6f82ec1319be8e612d36a5a750d4')     # Generated using makepkg -g

build() {
    cd "$srcdir/$pkgname-$pkgver"
    python -m build --wheel
}

package() {
    cd "$srcdir/$pkgname-$pkgver"
    python -m installer --destdir="$pkgdir" dist/*.whl
    install -Dm644 README.md "$pkgdir/usr/share/doc/$pkgname/README.md"

    # Install systemd unit and timer (installed system-wide under /usr/lib/systemd/system)
    install -Dm644 contrib/systemd/system/config-saver@.service "$pkgdir/usr/lib/systemd/system/config-saver@.service"
    install -Dm644 contrib/systemd/system/config-saver@.timer "$pkgdir/usr/lib/systemd/system/config-saver@.timer"    
}
