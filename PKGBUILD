# PACKAGER: wolftankk <wolftankk@gmail.com>
# Maintainer: Joseph R. Quinn <quinn.josephr@protonmail.com>
pkgname=php-phalcon
pkgver=4.0.0
pkgrel=1
pkgdesc="Web framework delivered as a C-extension for PHP"
url="http://phalconphp.com"
arch=('x86_64' 'i686')
[[ $CARCH == 'i686' ]] && cd _arch=32bits || _arch=64bits
license=('PHP')
depends=('php>=5.5')
makedepends=('gcc')
backup=('etc/php/conf.d/phalcon.ini')

source=(
	"https://github.com/phalcon/cphalcon/archive/v$pkgver.zip"
)

sha256sums=('99471eb2cec0f3002e327ee05d8b5a407ead0fd3a9aa07cc5f5c1c278cddaaa6')

#get php version
PHP_FULL_VERSION=`php-config --version`
if [ "${PHP_FULL_VERSION:0:1}" == "5" ]; then
    PHP_VERSION="php5"
else
    PHP_VERSION="php7"
fi

build() {
  cd "$srcdir/cphalcon-$pkgver"
  #Check best compilation flags for GCC
  export CC="gcc"
  export CFLAGS="-march=native -mtune=native -O2 -fomit-frame-pointer"
  export CPPFLAGS="-DPHALCON_RELEASE"
  echo "int main() {}" > t.c
  $CC $CFLAGS t.c -o t 2> t.t
  if [ $? != 0 ]; then
	  chmod +x gcccpuopt
	  BFLAGS=`./gcccpuopt`
	  export CFLAGS="-O2 -fomit-frame-pointer $BFLAGS"
	  $CC $CFLAGS t.c -o t 2> t.t
	  if [ $? != 0 ]; then
		  export CFLAGS="-O2"
	  fi
  fi

  if [ $($CC -dumpversion | cut -f1 -d.) -ge 4 ]; then
	  $CC $CFLAGS -fvisibility=hidden t.c -o t 2> t.t && export CFLAGS="$CFLAGS -fvisibility=hidden"
  fi

  rm -f t.t t.c t


  #cd dir
  cd "$srcdir/cphalcon-$pkgver/build/$PHP_VERSION/$_arch"

  #Clean current compilation
  if [ -f Makefile ]; then
	  make clean
	  phpize --clean
  fi

  phpize
  ./configure --prefix=/usr --enable-phalcon
  make
}

package() {
  cd "$srcdir/cphalcon-$pkgver/build/$PHP_VERSION/$_arch"

  make INSTALL_ROOT="$pkgdir" install
  echo 'extension=phalcon.so' > phalcon.ini 
  install -Dm644 phalcon.ini "$pkgdir/etc/php/conf.d/phalcon.ini"
}
