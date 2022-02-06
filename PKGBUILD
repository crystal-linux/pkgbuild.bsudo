
# Arch Maintainer: Evangelos Foutras <evangelos@foutrelis.com>
# Arch Contributor: Allan McRae <allan@archlinux.org>
# Arch Contributor: Tom Newsom <Jeepster@gmx.co.uk>

pkgname=bsudo
_sudover=1.9.9
pkgrel=4
pkgver=${_sudover/p/.p}
pkgdesc="Give certain users the ability to run some commands as root"
arch=('x86_64')
url="https://www.sudo.ws/sudo/"
license=('custom')
groups=('base-devel')
depends=('glibc' 'openssl' 'pam' 'libldap' 'zlib')
conflicts=('sudo')
backup=('etc/pam.d/sudo'
        'etc/sudo.conf'
        'etc/sudo_logsrvd.conf'
        'etc/sudoers')
install=$pkgname.install
source=(https://www.sudo.ws/sudo/dist/sudo-$_sudover.tar.gz{,.sig}
        sudo_logsrvd.service
        disable-non-interative-auth.patch
        mkbsudo.patch
        sudo.pam)
sha256sums=('6d6ee863a3bc26c87661093a74ec63e10fd031ceba714642d21636dfe25e3e00'
            'SKIP'
            '42801e622daf9878c20bd3e45090d42ffe4ade65a06e092c35219aaf696910c9'
            '094387d71f6866ff85ab1cccbdf685f97c02a803eb01b41c80c52918785db85c'
            '4a4baddb5d75a1cb5b53aaf249aace79428e8e267555aeb6d400cf86858dacd9'
            'ad3dbd687bb3ec0849ff91cd774fbeec21f9c4e6d1334de34d0f84a8519966e5')
validpgpkeys=('59D1E9CCBA2B376704FDD35BA9F4C021CEA470FB')

prepare() {
  cd "$srcdir/sudo-$_sudover"
  patch -Np1 -i ../mkbsudo.patch
  patch -Np1 -i ../disable-non-interative-auth.patch
}

build() {
  cd "$srcdir/sudo-$_sudover"

  ./configure \
    --prefix=/usr \
    --sbindir=/usr/bin \
    --libexecdir=/usr/lib \
    --with-rundir=/run/sudo \
    --with-vardir=/var/db/sudo \
    --with-logfac=auth \
    --enable-tmpfiles.d \
    --with-pam \
    --with-sssd \
    --with-ldap \
    --with-ldap-conf-file=/etc/openldap/ldap.conf \
    --with-env-editor \
    --with-passprompt="[bitches] password for %p: " \
    --with-all-insults
  make
}

check() {
  cd "$srcdir/sudo-$_sudover"
  make check
}

package() {
  cd "$srcdir/sudo-$_sudover"
  make DESTDIR="$pkgdir" install

  # sudo_logsrvd service file (taken from sudo-logsrvd-1.9.0-1.el8.x86_64.rpm)
  install -Dm644 -t "$pkgdir/usr/lib/systemd/system" ../sudo_logsrvd.service

  # Remove sudoers.dist; not needed since pacman manages updates to sudoers
  rm "$pkgdir/etc/sudoers.dist"

  # Remove /run/sudo directory; we create it using systemd-tmpfiles
  rmdir "$pkgdir/run/sudo"
  rmdir "$pkgdir/run"

  install -Dm644 "$srcdir/sudo.pam" "$pkgdir/etc/pam.d/sudo"

  install -Dm644 LICENSE.md -t "$pkgdir/usr/share/licenses/bsudo"

  echo "Defaults    insults" >> "$pkgdir/etc/sudoers"
}

# vim:set ts=2 sw=2 et:
