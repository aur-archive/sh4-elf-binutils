# Maintainer: Daniel YC Lin <dlin.tw at gmail>
# Contributor: Anatol Pomozov <anatol.pomozov@gmail.com>
# Contributor: Martin Schmölzer <mschmoelzer@gmail.com>
# contributor: Allan McRae <allan@archlinux.org>

# ref: https://wiki.archlinux.org/index.php/Cross_Compiling_Tools_Package_Guidelines
# toolchain build order: binutils, headers, gcc(pass 1), libc, gcc(pass 2)

_target=sh4-elf
#_sysroot=/usr/lib/${_target}
pkgname=${_target}-binutils
pkgver=2.24
pkgrel=3
pkgdesc='A set of programs to assemble and manipulate binary and object files for the sh4 elf target'
arch=(i686 x86_64)
url='http://www.gnu.org/software/binutils/'
license=(GPL)
depends=(zlib)
options=('staticlibs' '!distcc' '!ccache')
#checkdepends=('dejagnu' 'bc')
#conflicts=('binutils-multilib')
#replaces=('binutils-multilib')
#install=binutils.install
source=(http://ftp.gnu.org/gnu/binutils/binutils-${pkgver}.tar.bz2{,.sig}
        fix_gcc49_error.patch
        binutils-2.24-CVE-2014-8484.patch
        binutils-2.24-CVE-2014-8485.patch
        binutils-2.24-CVE-2014-8501.patch
        binutils-2.24-CVE-2014-8502.patch
        binutils-2.24-CVE-2014-8503.patch
        binutils-2.24-CVE-2014-8504.patch
        binutils-2.24-CVE-2014-8737.patch
        binutils-2.24-CVE-2014-8738.patch
        binutils-2.24-strings-all-default.patch)
sha256sums=('e5e8c5be9664e7f7f96e0d09919110ab5ad597794f5b1809871177a0f0f14137'
	    'SKIP'
            '50388c0e1b34fe19c3e28c3acaec1100f88c07353a083e272ad02f9282c8944e'
            'f4eb21ee16f34d7d60f9dd2d6a45616a78e60c79ae40a2a691316ed73704f8a1'
            '8bab2ee0dba00bccf78f3a9fee492342c6d6e362b43bdebe20b5226bbc76d3e7'
            '15d8878af78a26bc7ff9e40312c3265d8172328d505c03d2429177c981ab4397'
            '0df3391383ada2bb9bf45e0b2aced3e1cab8b21c2ffb7112440c0d51930eed77'
            '03261cba91e0a93a71d1554660d7dadf0735f6ec358ca6ad1443eb66b92b45ae'
            '47b092c472373d60655f1cde6d8f83dcf4e2ccdc818fb4c335b141ea2f472a02'
            '86fc02360f3c93ab73e2cc7df4f9516611220185fb543f9be4d87b10bc1d73f4'
            '51e116f55dd72ae8b8af6d8f9755ff557953857251e5490532840cca4a6fae51'
            'ae65ff226096aab7986c6c0086572990170cba7575dfddda3abe1224f7305db3')


prepare() {
  cd binutils-${pkgver}
  patch -p1 < $srcdir/fix_gcc49_error.patch

  # CVE-2014-8484 - backport of commit bd25671c
  patch -p1 -i $srcdir/binutils-2.24-CVE-2014-8484.patch

  # CVE-2014-8485 - commit 493a3386
  patch -p1 -i $srcdir/binutils-2.24-CVE-2014-8485.patch

  # CVE-2014-8501 - commit 7e1e1988
  patch -p1 -i $srcdir/binutils-2.24-CVE-2014-8501.patch

  # CVE-2014-8502 - commits bf67003b and 5a4b0ccc
  patch -p1 -i $srcdir/binutils-2.24-CVE-2014-8502.patch

  # CVE-2014-8503 - commit 0102ea8c
  patch -p1 -i $srcdir/binutils-2.24-CVE-2014-8503.patch

  # CVE-2014-8504 - commit 708d7d0d
  patch -p1 -i $srcdir/binutils-2.24-CVE-2014-8504.patch

  # CVE-2014-8737 - commit dd9b91de
  patch -p1 -i $srcdir/binutils-2.24-CVE-2014-8737.patch

  # CVE-2014-8738 - commit bb0d8671
  patch -p1 -i $srcdir/binutils-2.24-CVE-2014-8738.patch

  # strings -a default - commit 7fac9594 (fedora backport)
  patch -p0 -i $srcdir/binutils-2.24-strings-all-default.patch

  sed -i "/ac_cpp=/s/\$CPPFLAGS/\$CPPFLAGS -O2/" libiberty/configure

  mkdir ${srcdir}/binutils-build
}

build() {
  cd ${srcdir}/binutils-build

  ${srcdir}/binutils-$pkgver/configure --target=$_target \
              --with-sysroot=/usr/$_target \
              --prefix=/usr \
              --enable-multilib \
              --enable-interwork \
              --with-gnu-as \
              --with-gnu-ld \
              --disable-nls \
              --enable-plugins

  make
}


check() {
  cd ${srcdir}/binutils-build

  # unset LDFLAGS as testsuite makes assumptions about which ones are active
  # ignore failures in gold testsuite...
  make -k LDFLAGS="" check || true
}

package() {
  cd ${srcdir}/binutils-build
  make DESTDIR="$pkgdir" install

  # Remove file conflicting with host binutils and manpages for MS Windows tools
  rm "$pkgdir"/usr/share/man/man1/sh4-elf-{dlltool,nlmconv,windres,windmc}*

  # Remove info documents that conflict with host version
  rm -rf "$pkgdir"/usr/share/info
}
