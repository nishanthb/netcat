# netcat
Openbsd netcat

requires libbsd-devel

source at: http://ftp.acc.umu.se/debian/pool/main/n/netcat-openbsd/

# instructions

# $Id$
# Maintainer: Sergej Pupykin <pupykin.s+arch@gmail.com>
# Contributor: Andrej Gelenberg <andrej.gelenberg@udo.edu>

pkgname="openbsd-netcat"
pkgver=1.130_3
pkgrel=1
pkgdesc="TCP/IP swiss army knife. OpenBSD variant."
arch=('i686' 'x86_64')
url="http://packages.debian.org/sid/netcat-openbsd"
license=('BSD')
depends=('libbsd')
makedepends=('gcc' 'make')
provides=('netcat')
conflicts=('gnu-netcat')
source=("http://ftp.debian.org/debian/pool/main/n/netcat-openbsd/netcat-openbsd_${pkgver%_*}.orig.tar.gz"
	"http://ftp.debian.org/debian/pool/main/n/netcat-openbsd/netcat-openbsd_${pkgver/_/-}.debian.tar.xz")
sha256sums=('fd7205065d0b47898851f31f33e614de5d47a5b9dc81fd50d2ff51b63d091e5b'
            '2a41980c0c81159243ef7b97480241bb6c23f9c9bab81fea51689b2a77022439')

build() {
  cd "$srcdir"/netcat-openbsd-${pkgver%_*}
  for i in `cat ../debian/patches/series`; do
	echo "** patch $i" 1>&2
	cat "../debian/patches/$i"
  done | patch -p1
  make \
	CFLAGS="$CFLAGS -DDEBIAN_VERSION=\"\\\"${pkgrel}\\\"\" -I/usr/include/libbsd" \
	LDFLAGS="$LDFLAGS -lbsd"
}

package() {
  cd "$srcdir"/netcat-openbsd-${pkgver%_*}
  install -Dm0755 nc "$pkgdir"/usr/bin/nc
  ln -s nc "$pkgdir"/usr/bin/netcat
  install -Dm0644 nc.1 "$pkgdir"/usr/share/man/man1/nc.1
  install -dm0755 "$pkgdir"/usr/share/licenses/$pkgname/
  head -n28 netcat.c | tail -n+2 >"$pkgdir"/usr/share/licenses/$pkgname/LICENSE
}
