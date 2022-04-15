# Maintainer: Philip Müller <philm[at]manjaro[dog]org>

pkgname=bengal-installer
_pkgname=calamares
pkgver=3.2.55
pkgrel=1
pkgdesc='Distribution-independent installer framework'
arch=('i686' 'x86_64')
license=(GPL)
url="https://github.com/calamares/calamares/releases"
license=('LGPL')
depends=('kconfig' 'kcoreaddons' 'kiconthemes' 'ki18n' 'kio' 'solid' 'yaml-cpp' 'kpmcore>=4.2.0' 'mkinitcpio-openswap'
         'boost-libs' 'ckbcomp' 'hwinfo' 'qt5-svg' 'polkit-qt5' 'gtk-update-icon-cache' 'plasma-framework'
         'qt5-xmlpatterns' 'squashfs-tools' 'libpwquality' 'efibootmgr' 'icu')
conflicts=()
makedepends=('extra-cmake-modules' 'qt5-tools' 'qt5-translations' 'git' 'boost')
backup=('usr/share/calamares/modules/bootloader.conf'
        'usr/share/calamares/modules/displaymanager.conf'
        'usr/share/calamares/modules/initcpio.conf'
        'usr/share/calamares/modules/unpackfs.conf')

source=("file:///home/harry/Bengal/calam/$_pkgname-$pkgver.tar.gz"
	"calamares.desktop"
	"calamares_polkit"
	"49-nopasswd-calamares.rules"
        "bengal-installer.svg")

sha256sums=('d717f4dab8f2ebef37cdb536cb11941a37081897d84bcaf2aa2e02073ae1ee42'
            '48a8141b8e5a640fb35dfc41351585d8c8428e6eeba6346a83dc4d9f5c7ecb36'
            'c176b28007bd1c1f23d8dbb2c936fa54d0c01bacfb67290ddad597606c129df3'
            '56d85ff6bf860b9559b8c9f997ad9b1002f3fccc782073760eca505e3bddd176'
            '421802810816abf6a0c377800e8fbb7b6d100090d97fa92954568dffaeb8a693')

prepare() {

	sed -i -e 's/"Install configuration files" OFF/"Install configuration files" ON/' "$srcdir/${_pkgname}-${pkgver}/CMakeLists.txt"
	sed -i -e 's/# DEBUG_FILESYSTEMS/DEBUG_FILESYSTEMS/' "$srcdir/${_pkgname}-${pkgver}/CMakeLists.txt"

	# add pkgrelease to patch-version
	cd ${_pkgname}-${pkgver}
	_patchver="$(cat CMakeLists.txt | grep -m3 -e CALAMARES_VERSION_PATCH | grep -o "[[:digit:]]*" | xargs)"
	sed -i -e "s|CALAMARES_VERSION_PATCH $_patchver|CALAMARES_VERSION_PATCH $_patchver-$pkgrel|g" CMakeLists.txt

}

build() {
	cd $_pkgname-$pkgver

	mkdir -p build
	cd build
	cmake .. \
	-DCMAKE_BUILD_TYPE=Release \
	-DCMAKE_INSTALL_PREFIX=/usr \
	-DCMAKE_INSTALL_LIBDIR=lib \
	-DWITH_PYTHONQT=OFF \
	-DWITH_KF5DBus=OFF \
	-DBoost_NO_BOOST_CMAKE=ON \
	-DWEBVIEW_FORCE_WEBKIT=OFF \
	-DSKIP_MODULES="webview tracking interactiveterminal initramfs \
	initramfscfg dracut dracutlukscfg \
	dummyprocess dummypython dummycpp \
	dummypythonqt services-openrc \
	keyboardq localeq welcomeq"
	make
}

package() {
	cd ${_pkgname}-${pkgver}/build
	make DESTDIR="$pkgdir" install
	install -Dm644 "${srcdir}/calamares.desktop" "$pkgdir/etc/xdg/autostart/installer.desktop"
	install -Dm755 "${srcdir}/calamares_polkit" "$pkgdir/usr/bin/bengal_polkit"
	install -Dm644 "${srcdir}/49-nopasswd-calamares.rules" "$pkgdir/etc/polkit-1/rules.d/49-nopasswd-bengal.rules"
	chmod 750 "$pkgdir"/etc/polkit-1/rules.d
    mv "${srcdir}/bengal-installer.svg" "$pkgdir/usr/share/icons/bengal-installer.svg"
}