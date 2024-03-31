pkgbase=obs-vkcapture-lw
pkgname=("$pkgbase" "lib32-$pkgbase")
pkgver=1.5.0
pkgrel=1
url='https://github.com/nowrep/obs-vkcapture'
license=('GPL2')
arch=('x86_64')
depends=('vulkan-icd-loader' 'libgl')
optdepends=('obs-studio>=30')
makedepends=(
    'git' 'gcc' 'cmake' 'vulkan-headers' 'lib32-gcc-libs'
    'lib32-vulkan-icd-loader' 'lib32-libgl' 'obs-studio'
)
source=("$pkgbase::git+$url")
sha512sums=('SKIP')

build() {
    cmake -B build -S "$pkgbase" -DCMAKE_INSTALL_PREFIX=/usr \
        -DCMAKE_INSTALL_LIBDIR=lib \
        -DCMAKE_INSTALL_DATADIR=/share \
        -DCMAKE_BUILD_TYPE=Release
    make -C build

    export CFLAGS="-m32 ${CFLAGS}"
    export CXXFLAGS="-m32 ${CXXFLAGS}"
    export LDFLAGS="-m32 ${LDFLAGS}"

    cmake -B build32 -S "$pkgbase" -DCMAKE_INSTALL_PREFIX=/usr \
        -DCMAKE_INSTALL_LIBDIR=lib32 \
        -DCMAKE_INSTALL_DATADIR=/share \
        -DCMAKE_LIBRARY_PATH=/usr/lib32 \
        -DCMAKE_BUILD_TYPE=Release \
        -DBUILD_PLUGIN=OFF
    make -C build32
}

package_obs-vkcapture-lw() {
    pkgdesc='OBS Linux Vulkan/OpenGL game capture for Lux Wine'
    conflicts=('obs-vkcapture' 'obs-vkcapture-git')
    provides=('obs-vkcapture')

    make -C build DESTDIR="$pkgdir" install
    mv -f "$pkgdir/usr/share/vulkan/implicit_layer.d" "$pkgdir/usr/share/vulkan/explicit_layer.d"
    sed -i 's|OBS_VKCAPTURE=1|OBS_VKCAPTURE=1 VK_LOADER_LAYERS_ENABLE="$VK_LOADER_LAYERS_ENABLE,VK_LAYER_OBS_vkcapture_*"|' \
        "$pkgdir/usr/bin/obs-gamecapture"
}

package_lib32-obs-vkcapture-lw() {
    pkgdesc='OBS Linux Vulkan/OpenGL game capture (32-bit) for Lux Wine'
    depends=('lib32-vulkan-icd-loader' 'lib32-libgl')
    conflicts=('lib32-obs-vkcapture' 'lib32-obs-vkcapture-git')
    provides=('lib32-obs-vkcapture')

    make -C build32 DESTDIR="$pkgdir" install
    rm -r "$pkgdir/usr/bin"
    mv -f "$pkgdir/usr/share/vulkan/implicit_layer.d" "$pkgdir/usr/share/vulkan/explicit_layer.d"
}
