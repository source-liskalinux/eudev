pkgname=eudev
pkgver=3.2.14
pkgrel=1
pkgdesc="Standalone implementation of systemd-udev for systemd-free systems"
arch=('x86_64')
url="https://github.com/eudev-project/eudev"
license=('GPL-2.0-or-later' 'LGPL-2.1-or-later')
depends=('glibc' 'kmod' 'hwdata' 'util-linux')
makedepends=('gperf' 'glib2' 'pkgconf' 'meson' 'ninja')
provides=('udev' 'libudev.so')
conflicts=('systemd' 'systemd-libs')
source=("https://github.com/eudev-project/eudev/releases/download/v${pkgver}/eudev-${pkgver}.tar.gz")
sha256sums=('SKIP')

build() {
    meson setup "eudev-${pkgver}" build \
        --prefix=/usr \
        --sysconfdir=/etc \
        --libexecdir=/usr/lib \
        -Drootprefix=/ \
        -Drootlibdir=/usr/lib \
        -Drootlibexecdir=/usr/lib/udev \
        -Dbindir=/usr/bin \
        -Dsbindir=/usr/bin \
        -Dhwdb=true \
        -Dman=false \
        -Dkmod=true
    meson compile -C build
}

package() {
    DESTDIR="${pkgdir}" meson install -C build
    install -d -m755 "${pkgdir}/sbin"
    ln -sf /usr/lib/udev/udevd "${pkgdir}/sbin/udevd"
    install -d -m755 "${pkgdir}/etc/udev/rules.d"
    install -d -m755 "${pkgdir}/usr/lib/udev/rules.d"
    install -d -m755 "${pkgdir}/run/udev"
    echo "Generating udev hwdb.bin inside package root...."
    if [ -f "${srcdir}/build/src/udev/udevadm" ]; then
        "${srcdir}/build/src/udev/udevadm" hwdb --update --root="${pkgdir}"
    else
        udevadm hwdb --update --root="${pkgdir}"
    fi
}
