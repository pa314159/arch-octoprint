# Maintainer: pappy <pa314159@users.noreply.github.com>

pkgname=octoprint
pkgver=1.10.3
pkgrel=2
pkgdesc="The snappy web interface for your 3D printer on Arch Linux"
arch=(any)
url="https://octoprint.org/"
license=('AGPL3')
depends=(
		python-argon2_cffi # aur
		python-click
		python-colorlog
		python-emoji
		python-feedparser
		python-filetype # aur
		python-flask-assets # aur
		python-flask-login
		python-flask-limiter # aur
		python-frozendict
		python-netaddr
		python-netifaces
		python-passlib
		python-pathvalidate # aur
		python-pkginfo
		python-psutil
		python-pylru # aur
		python-pyserial
		python-regex
		python-requests
		python-sarge # aur
		python-tornado
		python-unidecode
		python-websocket-client
		python-zipstream-ng #aur
)
makedepends=('python-virtualenv' 'rust')
optdepends=('ffmpeg: timelapse support'
			'curaengine: fast and robust engine for processing 3D models'
			'mjpg-streamer: stream images from webcam'
			'motion: motion detector which grabs images from video4linux devices and/or from webcams'
			)
conflicts=('octoprint-venv')
install=octoprint.install
backup=(etc/conf.d/octoprint)
source=("${pkgname}-${pkgver}.tar.gz::https://github.com/OctoPrint/OctoPrint/archive/refs/tags/${pkgver}.tar.gz"
		010-python-version.patch
		octoprint.sysusers
		octoprint.tmpfiles
		octoprint.service
		octoprint-serve
		octoprint.conf
		)
options=(!distcc !ccache)
sha256sums=('51d903e0fa03a1d24bb986edb543624cc937de91f44b258fed1e1a7bbcf2d4df'
            '2403f4d263a6804b5d1cfbcc4684a0800d3a8ae13ab2664bb045d62a5ae9396b'
            'bd9b7f989aefb02da1ac414f306861f21f084d886f0283eea11516482b407d65'
            'b07af51817cd209cdf019d6347ce5d62121ccbf20835dad8bb8316a80bc82346'
            '231685e84b0241a466766c766f8d3ba31efda3238f19e9adedea380e7b861737'
            '08e6ff10fb7f61c40e5770b67e8f7201d02d82d3bd46c5441a7f2b0435fbe9c2'
            '02be5d5a18febe215809882d96f068092c4474abb4e76d82e4450b860a4e9ef5')

prepare()
{
	cd $srcdir/OctoPrint-${pkgver}

	for s in ${source[@]}; do
		case $s in
			*.patch)	echo -n Applying patch $s...
						patch -s -p1 -i ${srcdir}/$s
						echo ' done'
						;;
		esac
	done
}

package() {
	# TODO remove venv
	python -m venv --system-site-packages --symlinks $pkgdir/usr/lib/$pkgname
	source $pkgdir/usr/lib/$pkgname/bin/activate

	pushd $srcdir/OctoPrint-${pkgver}
	pip --no-cache-dir install  . --compile
	popd

	find $pkgdir/usr/lib/$pkgname/bin -type f -exec grep -q $pkgdir {} \; -exec sed -i "s:$pkgdir::g" {} \;
	find $pkgdir/usr/lib/$pkgname/lib -type d \( -name testsuite -o -name tests \) -exec rm -rf {} \; 2>/dev/null || true

	install -Dm644 octoprint.sysusers $pkgdir/usr/lib/sysusers.d/octoprint.conf
	install -Dm644 octoprint.tmpfiles $pkgdir/usr/lib/tmpfiles.d/octoprint.conf
	install -Dm644 octoprint.service $pkgdir/usr/lib/systemd/system/octoprint.service
	install  -m755 octoprint-serve $pkgdir/usr/lib/$pkgname/bin
	install -Dm644 octoprint.conf $pkgdir/etc/conf.d/octoprint
	install -dm750 $pkgdir/var/lib/$pkgname
}
