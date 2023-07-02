# Maintainer: Pellegrino Prevete (tallero) <pellegrinoprevete@gmail.com>
# Contributor: Jan Alexander Steffens (heftig) <heftig@archlinux.org>
# Contributor: Levente Polyak <anthraxx[at]archlinux[dot]org>
# Contributor: Jan de Groot <jgc@archlinux.org>
# Contributor: Tom Gundersen <teg@jklm.no>
# Contributor: John Proctor <jproctor@prium.net>

_pkg="libxml2"
pkgbase="${_pkg}-2.9"
pkgname=(
  "${pkgbase}"
  "${pkgbase}"-docs
)
pkgver=2.9.10
pkgrel=1
pkgdesc="XML C parser and toolkit"
url="https://gitlab.gnome.org/GNOME/${_pkg}/-/wikis/home"
arch=(x86_64)
license=(custom:MIT)
depends=(
  icu
  ncurses
  readline
  xz
  zlib
)
makedepends=(
  git
  python
)
# _commit=223cb03a5d27b1b2393b266a8657443d046139d6  # tags/v2.10.4^0
_commit=41a34e1f4ffae2ce401600dbb5fe43f8fe402641 # tags/v2.9.10^0
# _commit=3ebf94cd96ba78ea25f929a1c948ad54a262e75e # tags/v2.4.30^0
source=(
  "git+https://gitlab.gnome.org/GNOME/libxml2.git#commit=$_commit"
        libxml2-2.9.8-python3-unicode-errors.patch
        fix-relaxed-approach-to-nested-documents.patch
        libxml2-2.9.10-CVE-2019-20388.patch
        libxml2-2.9.10-CVE-2020-7595.patch
        libxml2-2.9.10-parenthesize-type-checks.patch
        libxml2-2.9.10-CVE-2020-24977.patch
        libxml2-2.9.10-fix-integer-overflow.patch
        libxml2-2.9.10-icu68.patch
  https://www.w3.org/XML/Test/xmlts20130923.tar.gz
)
sha256sums=(
  'SKIP'
  '37eb81a8ec6929eed1514e891bff2dd05b450bcf0c712153880c485b7366c17c'
  '50f04807b86a179d051fb86755e82f55ba7aac9d0c005eefea93d2599a911d01'
  'cfe1b3e0f026df6f979dbd77c1dcd1268e60acf3d7a8ff3f480b4e67bfcc19d6'
  'c6105ff40d7b1b140fcd821b5d64ab8c7b596708071c26964727e7352b07ac7e'
  'b63c161e4c8a6f0a65ba091c3d3ed09d3110d21f997ee61077c782b311fd4b33'
  '62eafffc2b4949489c261c63883d27c2e83d688f1d4c899000b283e4c2a682be'
  'fd227780ad5699bebca7ef412d2d50fb1d21a54f6e3fdcad0bda5bdc8f8b2525'
  'f02a435761f26ff664041d49f9d05924dc627bf103c7f542feee891f69aa84a2'
  '9b61db9f5dbffa545f4b8d78422167083a8568c59bd1129f94138f936cf6fc1f'
)

pkgver() {
  cd "${_pkg}"
  git describe --tags | sed 's/-rc/rc/;s/^v//;s/[^-]*-g/r&/;s/-/+/g'
}

prepare() {
  cd "${_pkg}"

  # From https://src.fedoraproject.org/rpms/libxml2/tree/master
  patch -Np1 -i ../libxml2-2.9.8-python3-unicode-errors.patch
  patch -Np1 -i ../fix-relaxed-approach-to-nested-documents.patch
  patch -Np1 -i ../libxml2-2.9.10-CVE-2019-20388.patch
  patch -Np1 -i ../libxml2-2.9.10-CVE-2020-7595.patch
  patch -Np1 -i ../libxml2-2.9.10-parenthesize-type-checks.patch
  patch -Np1 -i ../libxml2-2.9.10-CVE-2020-24977.patch
  patch -Np1 -i ../libxml2-2.9.10-fix-integer-overflow.patch

  patch -Np1 -i ../libxml2-2.9.10-icu68.patch

  NOCONFIGURE=1 ./autogen.sh
}

build() {
  local configure_options=(
    --prefix=/usr
    --sysconfdir=/etc
    --localstatedir=/var
    --program-suffix=-2.9
    --libdir="/usr/lib/${pkgbase}"
    --includedir="/usr/include/${pkgbase}"
    --with-history
    --with-icu
    --without-python
    --with-threads
    --disable-static
  )

  cd "${_pkg}"

  ./configure "${configure_options[@]}"
  sed -i -e 's/ -shared / -Wl,-O1,--as-needed\0/g' libtool
  make
}

check() {
  cd "${_pkg}"
  make check
}

package_libxml2-2.9() {
  optdepends=('python: Python bindings')
  provides=("libxml2.so=${pkgver}")

  cd libxml2

  make DESTDIR="${pkgdir}" install

  mkdir -p ../doc/usr/share
  mv "${pkgdir}/usr/share/"{doc,gtk-doc} -t ../doc/usr/share

  install -Dm644 Copyright -t "${pkgdir}/usr/share/licenses/${pkgname}"
}

package_libxml2-2.9-docs() {
  pkgdesc+=" (documentation)"
  depends=()

  mv doc/* "${pkgdir}"

  install -Dm644 "${_pkg}/Copyright" -t "${pkgdir}/usr/share/licenses/${pkgname}"
}

# vim:set sw=2 sts=-1 et: