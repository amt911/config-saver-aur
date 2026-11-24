# Maintainer: amt911 <your.email@example.com>
pkgname=config-saver
pkgver=3.7.0
pkgrel=1
pkgdesc="Utility to back up and restore configuration files"
arch=('any')
url="https://github.com/amt911/config-saver"
license=('MIT')
depends=('python' 'python-pydantic' 'python-colorama' 'python-tqdm' 'python-yaml' 'python-rich')
makedepends=('python-build' 'python-installer' 'python-wheel' 'git')
# Archive encryption shells out to one of these. Without them config-saver works
# exactly as before; only a configuration that asks for encryption needs one.
optdepends=('age: encrypt archives with age (also needed for the restic store password and --sync)'
            'gnupg: encrypt archives with gpg'
            'restic: deduplicated encrypted storage and retention (config-saver --restic-init), and --sync'
            'rclone: multi-machine sync over Google Drive, Proton Drive and other rclone remotes (config-saver --sync)'
            'libnotify: desktop notification when a scheduled sync fails (notify-send)')
install=config-saver.install
source=("$pkgname-$pkgver.tar.gz::$url/archive/refs/tags/$pkgver.tar.gz")
# Refresh with `updpkgsums` once the upstream tag exists; the placeholder makes
# makepkg fail loudly instead of building an unverified tarball.
sha256sums=('b69be78d57207396368c89b6b79be7a58b928b4ddb60067d90091d6193437581')

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

    # Example configurations. They go to /usr/share, not /etc: a package must not
    # put example content in /etc, or the state of /etc stops being the
    # administrator's. config-saver reads this directory only when neither
    # /etc/config-saver/configs nor ~/.config/config-saver/configs.d has anything.
    # System policy directory: empty, owned by the package, so the location a
    # declarative installer writes to exists with the right permissions.
    install -d "$pkgdir/etc/config-saver/configs"

    install -d "$pkgdir/usr/share/config-saver/configs"
    cp -r configs/* "$pkgdir/usr/share/config-saver/configs/"
    find "$pkgdir/usr/share/config-saver/configs" -type f -exec chmod 644 {} \;
    find "$pkgdir/usr/share/config-saver/configs" -type d -exec chmod 755 {} \;

    # Templated system units: `systemctl enable --now config-saver@alice.timer`
    install -Dm644 contrib/systemd/system/config-saver@.service "$pkgdir/usr/lib/systemd/system/config-saver@.service"
    install -Dm644 contrib/systemd/system/config-saver@.timer "$pkgdir/usr/lib/systemd/system/config-saver@.timer"

    # User units: `systemctl --user enable --now config-saver.timer`
    install -Dm644 contrib/systemd/user/config-saver.service "$pkgdir/usr/lib/systemd/user/config-saver.service"
    install -Dm644 contrib/systemd/user/config-saver.timer "$pkgdir/usr/lib/systemd/user/config-saver.timer"

    # Multi-machine sync: `systemctl --user enable --now config-saver-sync.timer`
    install -Dm644 contrib/systemd/user/config-saver-sync.service "$pkgdir/usr/lib/systemd/user/config-saver-sync.service"
    install -Dm644 contrib/systemd/user/config-saver-sync.timer "$pkgdir/usr/lib/systemd/user/config-saver-sync.timer"
    install -Dm644 contrib/systemd/user/config-saver-notify@.service "$pkgdir/usr/lib/systemd/user/config-saver-notify@.service"
}
