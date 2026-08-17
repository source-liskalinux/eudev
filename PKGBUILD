# PKGBUILD For eudev

# Contributor: Janorovic Volkov <janorovicvolkov@gmail.com>
# Maintainer: Janorovic Volkov <janorovicvolkov@gmail.com>

pkgname=eudev
pkgver=3.2.14
pkgrel=1
pkgdesc="Standalone implementation of systemd-udev for systemd-free systems"
arch=('x86_64')
url="https://github.com/eudev-project/eudev"
license=('GPL-2.0-or-later' 'LGPL-2.1-or-later')
depends=('glibc' 'kmod' 'hwdata' 'util-linux')
makedepends=('gperf' 'glib2' 'pkgconf')
provides=('udev' 'libudev.so')
conflicts=('systemd' 'systemd-libs')
source=("https://github.com/eudev-project/eudev/releases/download/v${pkgver}/eudev-${pkgver}.tar.gz")
sha256sums=('SKIP')

build() {
    cd "${srcdir}/eudev-${pkgver}"
    ./configure \
        --prefix=/usr \
        --sysconfdir=/etc \
        --libexecdir=/usr/lib \
        --bindir=/usr/bin \
        --sbindir=/usr/sbin \
        --enable-hwdb \
        --disable-manpages \
        --enable-kmod
    make
}

package() {
    cd "${srcdir}/eudev-${pkgver}"
    make DESTDIR="${pkgdir}" install
    real_udevd=""
    for candidate in usr/lib/udev/udevd usr/sbin/udevd usr/bin/udevd sbin/udevd bin/udevd; do
        if [ -f "${pkgdir}/${candidate}" ] && [ ! -L "${pkgdir}/${candidate}" ]; then
            real_udevd="${candidate}"
            break
        fi
    done
    if [ -z "${real_udevd}" ]; then
        echo "ERROR: udevd binary not found anywhere under ${pkgdir} after install!" >&2
        exit 1
    fi
    install -d -m755 "${pkgdir}/usr/lib/udev"
    if [ "${real_udevd}" != "usr/lib/udev/udevd" ]; then
        mv "${pkgdir}/${real_udevd}" "${pkgdir}/usr/lib/udev/udevd"
    fi
    install -d -m755 "${pkgdir}/sbin"
    ln -sf /usr/lib/udev/udevd "${pkgdir}/sbin/udevd"
    install -d -m755 "${pkgdir}/etc/udev/rules.d"
    install -d -m755 "${pkgdir}/usr/lib/udev/rules.d"
    install -d -m755 "${pkgdir}/run/udev"
    echo "Generating udev hwdb.bin inside package root...."
    if [ -f "${srcdir}/eudev-${pkgver}/src/udev/udevadm" ]; then
        "${srcdir}/eudev-${pkgver}/src/udev/udevadm" hwdb --update --root="${pkgdir}"
    else
        udevadm hwdb --update --root="${pkgdir}"
    fi
}
