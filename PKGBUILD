pkgname=hplip-lite
_pkgname=hplip
pkgver=3.15.2
pkgrel=1
pkgdesc="HP printer and scanner drivers without the extra bloat"
arch=('i686' 'x86_64')
url="http://hplipopensource.com"
license=('GPL')
depends=('ghostscript>=8.64-6' 'foomatic-db' 'foomatic-db-engine'
	 'net-snmp>=5.7.1' 'wget')
makedepends=('sane' 'rpcbind' 'cups' 'libusb')
optdepends=('cups: for printing support'
            'sane: for scanner support'
            'rpcbind: for network support'
            'hplip-plugin: binary blobs for specific devices (AUR) or run hp-setup to download the plugin'
            'libusb: for advanced usb support')
install=hplip.install
source=(http://downloads.sourceforge.net/${_pkgname}/$_pkgname-$pkgver.tar.gz
)
conflicts=('hplip')
provides=('hplip')
sha1sums=('efebad73f29ab9f77d5a866c1c5d3857cc5f1df5')

prepare() {
 cd $_pkgname-$pkgver

 # https://bugs.archlinux.org/task/30085 - hack found in Gentoo
 # Use system foomatic-rip for hpijs driver instead of foomatic-rip-hplip
 # The hpcups driver does not use foomatic-rip
	local i
	for i in ppd/hpijs/*.ppd.gz ; do
		rm -f ${i}.temp
		gunzip -c ${i} | sed 's/foomatic-rip-hplip/foomatic-rip/g' | \
			gzip > ${i}.temp || return 1
		mv ${i}.temp ${i}
	done
   
 export AUTOMAKE='automake --foreign'
 autoreconf --force --install
}

build() {
 cd $_pkgname-$pkgver
 ./configure --prefix=/usr \
             --disable-foomatic-rip-hplip-install \
             --enable-foomatic-ppd-install \
             --enable-hpcups-install \
             --enable-lite-build \
             --enable-new-hpcups \
             --enable-cups-ppd-install \
             --enable-cups-drv-install \
             --enable-hpijs-install \
             --enable-foomatic-drv-install \
             --enable-pp-build \
             --enable-udev-acl-rules #--help

 make
}

package() {
 #cd $_pkgname-$pkgver
 cd $_pkgname-${pkgver/.a/a}
 make rulesdir=/usr/lib/udev/rules.d DESTDIR="$pkgdir/" install

 # need this for scanners to be recognised
 install -Dm644 data/models/models.dat "$pkgdir/"usr/share/hplip/data/models/models.dat
 
 # doesn't seem to be needed
 # remove config provided by sane and autostart of hp-daemon
 #rm -rf "$pkgdir"/etc/{sane.d,xdg}
 rm -rf "$pkgdir"/etc/sane.d
 
 # remove HAL .fdi file because HAL is no longer used
 rm -vrf "$pkgdir"/usr/share/hal
 
 # doesn't seem to be needed
 # remove rc script
 #rm -vrf "$pkgdir"/etc/init.d
}
