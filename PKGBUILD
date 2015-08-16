pkgname=mingw-w64-gtkglext
pkgver=1.2.0
pkgrel=2
pkgdesc="opengl extensions for gtk2 (mingw-w64)"
arch=(any)
url="http://gtkglext.sourceforge.net"
license=("LGPL")
makedepends=(mingw-w64-gcc mingw-w64-pkg-config)
depends=(mingw-w64-crt mingw-w64-gtk2)
options=(staticlibs !strip !buildflags)
source=("http://downloads.sourceforge.net/sourceforge/gtkglext/gtkglext-${pkgver}.tar.bz2"
"gtk2.20.patch"
"gtkglext-1.0.6-pangox.patch")
md5sums=('ed7ba24ce06a8630c07f2d0ee5f04ab4'
         'e5a87ec3f2d0e616c6f32f90c3f7237f'
         '7e35925b3c722717f969558cc627f39f')

_architectures="i686-w64-mingw32 x86_64-w64-mingw32"

prepare() {
	cd "${srcdir}/gtkglext-${pkgver}"
  patch -Np1 -i "${srcdir}/gtk2.20.patch"
  patch -p1 -R -i "${srcdir}/gtkglext-1.0.6-pangox.patch"
  autoconf --force
}

build() {
  for _arch in ${_architectures}; do
    unset LDFLAGS
    mkdir -p "${srcdir}/${pkgname}-${pkgver}-build-${_arch}"
    cp -r "${srcdir}/gtkglext-${pkgver}/"* "${srcdir}/${pkgname}-${pkgver}-build-${_arch}"
    cd "${srcdir}/${pkgname}-${pkgver}-build-${_arch}"
    "${srcdir}"/${pkgname#mingw-w64-}-${pkgver}/configure \
      --prefix=/usr/${_arch} \
      --build=$CHOST \
      --host=${_arch} \
      --with-gdktarget=win32 \
      --without-x \
      --disable-glibtest \
      --disable-gtktest \
      --disable-debug
    make
  done
}

package() {
  for _arch in ${_architectures}; do
    cd "${srcdir}/${pkgname}-${pkgver}-build-${_arch}"
    make DESTDIR="$pkgdir" install
    find "$pkgdir/usr/${_arch}" -name '*.exe' -o -name '*.bat' -o -name '*.def' -o -name '*.exp' | xargs -rtl1 rm
    find "$pkgdir/usr/${_arch}" -name '*.dll' | xargs -rtl1 ${_arch}-strip --strip-unneeded
    find "$pkgdir/usr/${_arch}" -name '*.a' -o -name '*.dll' | xargs -rtl1 ${_arch}-strip -g
    rm -r "$pkgdir/usr/${_arch}/share"
  done
}