# Maintainer: amt911 <your.email@example.com>
pkgname=config-saver
pkgver=1.0.0
pkgrel=1
pkgdesc="Utility to back up and restore configuration files"
arch=('any')
url="https://github.com/amt911/config-saver"
license=('MIT')
depends=('python' 'python-pydantic' 'python-colorama' 'python-tqdm' 'python-yaml' 'python-rich')
makedepends=('python-build' 'python-installer' 'python-wheel' 'git')
# Cambia 'main' por el nombre de la rama que quieras usar
_gitbranch="expand-vars"       # DEBUG
source=("$pkgname::git+$url#branch=${_gitbranch}")      # DEBUG
sha256sums=('SKIP')
# source=("$pkgname-$pkgver.tar.gz::$url/archive/refs/tags/v$pkgver.tar.gz")        # Oficial
build() {
    # cd "$srcdir/$pkgname-$pkgver"         # Oficial
    cd "$srcdir/$pkgname"       # DEBUG
    python -m build --wheel
}

package() {
    # cd "$srcdir/$pkgname-$pkgver"         # Oficial
    cd "$srcdir/$pkgname"       # DEBUG
    python -m installer --destdir="$pkgdir" dist/*.whl
    install -Dm644 README.md "$pkgdir/usr/share/doc/$pkgname/README.md"
    install -Dm644 configs/default-config.yaml "$pkgdir/etc/config-saver/configs/default-config.yaml"
}
