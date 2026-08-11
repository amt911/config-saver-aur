# Maintainer: amt911 <your.email@example.com>
pkgname=config-saver
pkgver=3.2.0
pkgrel=1
pkgdesc="Utility to back up and restore configuration files"
arch=('any')
url="https://github.com/amt911/config-saver"
license=('MIT')
depends=('python' 'python-pydantic' 'python-colorama' 'python-tqdm' 'python-yaml' 'python-rich')
makedepends=('python-build' 'python-installer' 'python-wheel' 'git')
# Archive encryption shells out to one of these. Without them config-saver works
# exactly as before; only a configuration that asks for encryption needs one.
optdepends=('age: encrypt archives with age'
            'gnupg: encrypt archives with gpg')
source=("$pkgname-$pkgver.tar.gz::$url/archive/refs/tags/$pkgver.tar.gz")
# Refresh with `updpkgsums` once the upstream tag exists; the placeholder makes
# makepkg fail loudly instead of building an unverified tarball.
sha256sums=('SHA256-PENDING-RUN-updpkgsums')

build() {
    cd "$srcdir/$pkgname-$pkgver"
    python -m build --wheel
}

package() {
    cd "$srcdir/$pkgname-$pkgver"
    python -m installer --destdir="$pkgdir" dist/*.whl
    install -Dm644 README.md "$pkgdir/usr/share/doc/$pkgname/README.md"
    install -Dm644 CHANGELOG.md "$pkgdir/usr/share/doc/$pkgname/CHANGELOG.md"
    install -Dm644 LICENSE "$pkgdir/usr/share/licenses/$pkgname/LICENSE"

    # Install all config files
    install -d "$pkgdir/etc/config-saver/configs"
    cp -r configs/* "$pkgdir/etc/config-saver/configs/"
    find "$pkgdir/etc/config-saver/configs" -type f -exec chmod 644 {} \;
    find "$pkgdir/etc/config-saver/configs" -type d -exec chmod 755 {} \;

    # Templated system units: `systemctl enable --now config-saver@alice.timer`
    install -Dm644 contrib/systemd/system/config-saver@.service "$pkgdir/usr/lib/systemd/system/config-saver@.service"
    install -Dm644 contrib/systemd/system/config-saver@.timer "$pkgdir/usr/lib/systemd/system/config-saver@.timer"

    # User units: `systemctl --user enable --now config-saver.timer`
    install -Dm644 contrib/systemd/user/config-saver.service "$pkgdir/usr/lib/systemd/user/config-saver.service"
    install -Dm644 contrib/systemd/user/config-saver.timer "$pkgdir/usr/lib/systemd/user/config-saver.timer"
}
