pkgname=supercollider
pkgver=3.7.0
pkgrel=1
pkgdesc="An environment and programming language for real time audio synthesis and algorithmic composition."
url="http://supercollider.sourceforge.net/"
arch=('x86_64')
license=('GPL3')
depends=('jack' 'fftw' 'qtwebkit' 'libsndfile')
makedepends=('cmake' 'libsndfile' 'ruby' 'vim' 'emacs' 'boost' 'avahi' 'qt5-base' 'qt5-tools' 'qt5-webkit' 'qt5-location' 'qt5-sensors')
source=("${pkgname}::git+https://github.com/supercollider/supercollider.git#branch=3.7"
        "libatomic.patch")
md5sums=('SKIP'
         '2956e6172cafcc9a38bdfdd2bc33e87b')
install="$pkgname.install"

prepare() {
  cd $srcdir/$pkgname
  msg "Applying patch for fixing linking failure with GCC > 5.1"
  # https://gcc.gnu.org/bugzilla/show_bug.cgi?id=65913
  patch -Np1 -i ../libatomic.patch
}

build() {
  cd $srcdir/$pkgname
  git submodule update --init --recursive .
  _carch=$(uname -m)
  Qt5LinguistTools_DIR="/usr/lib/cmake/Qt5LinguistTools"
  [ -d bld ] || mkdir bld && cd bld
  export LDFLAGS="${LDFLAGS//-Wl,--as-needed}"
  msg "$_carch architecture detected."
  case "$_carch" in
    "armv6l")
      export CC="gcc"
      export CXX="g++"
      cmake .. -L \
        -DCMAKE_INSTALL_PREFIX=/usr \
        -DCMAKE_BUILD_TYPE=Release \
        -DBUILD_TESTING=OFF \
        -DSSE=OFF \
        -DSSE2=OFF \
        -DSUPERNOVA=OFF \
        -DNOVA_SIMD=ON \
        -DNATIVE=OFF \
        -DSC_QT=OFF \
        -DSC_WII=OFF \
        -DSC_ED=OFF \
        -DSC_IDE=OFF \
        -DSC_EL=ON \
        -DCMAKE_C_FLAGS="-march=armv6 -mfpu=vfp -mfloat-abi=hard" \
        -DCMAKE_CXX_FLAGS="-march=armv6 -mfpu=vfp -mfloat-abi=hard" 2>&1 | tee cmake-output.txt
      make -j4 2>&1 | tee make-output.txt
    ;;
    "armv7l")
      export CC="gcc"
      export CXX="g++"
      cmake .. -L \
        -DCMAKE_INSTALL_PREFIX=/usr \
        -DCMAKE_BUILD_TYPE=Release \
        -DBUILD_TESTING=OFF \
        -DSSE=OFF \
        -DSSE2=OFF \
        -DSUPERNOVA=OFF\
        -DNOVA_SIMD=ON \
        -DNATIVE=OFF \
        -DSC_QT=OFF \
        -DSC_WII=OFF \
        -DSC_ED=OFF \
        -DSC_IDE=OFF \
        -DSC_EL=ON \
        -DCMAKE_C_FLAGS="-march=armv7-a -mtune=cortex-a8 -mfloat-abi=hard -mfpu=neon" \
        -DCMAKE_CXX_FLAGS="-march=armv7-a -mtune=cortex-a8 -mfloat-abi=hard -mfpu=neon" 2>&1 | tee cmake-output.txt
      make -j4 2>&1 | tee build-output.txt
    ;;
    *)
    cmake .. -DCMAKE_INSTALL_PREFIX=/usr \
      -DSUPERNOVA=1 \
      -DCMAKE_BUILD_TYPE=Release 2>&1 | tee cmake-output.txt
    make 2>&1 | tee make-output.txt
    ;;
  esac
}

package() {
  cd "$srcdir/$pkgname/bld"
  make DESTDIR="$pkgdir/" install
}

