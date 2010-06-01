# Maintainer: DonVla <donvla@users.sourceforge.net>
<<<<<<< HEAD
# Contributor: BlackEagle < ike DOT devolder AT herecura DOT be >
=======
>>>>>>> 574000342bfa0d530e365fcf50f5246714c9768f
# Contributor: Ulf Winkelvos <ulf [at] winkelvos [dot] de>
# Contributor: Ralf Barth <archlinux dot org at haggy dot org>
#
# Original credits go to Edgar Hucek <gimli at dark-green dot com>
# for his xbmc-vdpau-vdr PKGBUILD at https://archvdr.svn.sourceforge.net/svnroot/archvdr/trunk/archvdr/xbmc-vdpau-vdr/PKGBUILD

pkgname=xbmc-svn
<<<<<<< HEAD
pkgver=30746
=======
pkgver=30605
>>>>>>> 574000342bfa0d530e365fcf50f5246714c9768f
pkgrel=1
pkgdesc="XBMC Media Center from SVN"
provides=('xbmc')
conflicts=('xbmc' 'xbmc-pulse')
arch=('i686' 'x86_64')
url="http://xbmc.org"
license=('GPL' 'LGPL')
depends=('alsa-lib' 'curl' 'enca' 'faac' 'freetype2' 'fribidi' 'gawk' 'glew'
         'jasper' 'libgl' 'libjpeg>=7' 'libpng>=1.4' 'libmad' 'libmysqlclient'
         'libxinerama' 'libxrandr' 'lzo2' 'sdl_image>=1.2.10' 'sdl_mixer' 'sqlite3'
         'tre' 'unzip' 'xorg-server' 'libcdio' 'faad2' 'libsamplerate' 'smbclient' 
         'libmms' 'xorg-utils' 'wavpack' 'libmicrohttpd' 'libmpeg2' 'libmodplug'
         'libvdpau')
makedepends=('subversion' 'autoconf' 'automake' 'boost' 'cmake' 'gcc' 'gperf' 
             'libtool>=2.2.6a-1' 'make' 'nasm' 'patch' 'pkgconfig' 'zip' 'flex' 
             'bison' 'cvs')
optdepends=('lirc: remote controller support'
            'gdb: for meaningful backtraces in case of trouble - STRONGLY RECOMMENDED'
            'avahi: to use zerconf features (remote, etc...)'
            'unrar: access compressed files without unpacking them'
            'upower: used to trigger suspend functionality'
            'libva-sds: accelerated video playback for nvidia, ati/amd and some intel cards'
            'libssh: support for sshfs')
options=('makeflags')
install="${pkgname}.install"
source=("FEH.sh" 
        #"http://trac.xbmc.org/raw-attachment/ticket/8552/projectM.diff"
        "projectM.diff" )
md5sums=('c3e2ab79b9965f1a4a048275d5f222c4'
         '70eed644485de10cb80927bc1a3c77c7')
sha256sums=('1b391dfbaa07f81e5a5a7dfd1288bf2bdeab8dc50bbb6dbf39a80d8797dfaeb0'
            'c379ba3b2b74e825025bf3138b9f2406aa61650868715a8dfc9ff12c3333c2b6')

_svnmod=XBMC
_prefix=/usr

build() {

    _svntrunk=http://xbmc.svn.sourceforge.net/svnroot/xbmc/trunk

    cd ${srcdir}/
    if [ -d $_svnmod/.svn ]; then
        msg "SVN tree found, reverting changes and updating to -r$pkgver"
        (cd $_svnmod && svn revert -R . && make distclean; svn up -r $pkgver) || return 1
        #(cd $_svnmod && svn revert -R . && make distclean; svn up -r $pkgver)
    else
        msg "Checking out SVN tree of -r$pkgver"
        svn co $_svntrunk --config-dir ./ -r $pkgver $_svnmod || return 1
        #svn co $_svntrunk --config-dir ./ -r $pkgver $_svnmod
    fi

    # Configure XBMC
    #
    # Note on external-libs:
    #   - We cannot use external python because Arch's python was built with
    #     UCS2 unicode support, whereas xbmc expects UCS4 support
    #   - We cannot use Arch's libass because it's incompatible with XBMC's subtitle rendering
    #   - According to an xbmc dev using external/system ffmpeg with xbmc is "pure stupid" :D
    cd "${srcdir}/${_svnmod}"

    # Patch for missing projectM presets
    patch -p0 < ${srcdir}/projectM.diff || return 1

    # Archlinux Branding by SVN_REV
    export SVN_REV="${pkgver}-ARCH"

    # fix lsb_release dependency: IS THIS NEEDED???
    sed -i -e 's:/usr/bin/lsb_release -d:cat /etc/arch-release:' xbmc/utils/SystemInfo.cpp || return 1

	msg "Bootstrapping XBMC"
	./bootstrap || return 1

    msg "Configuring XBMC" 
    # some options - disable or enable stuff - copy them under ./configure
               # --disable-pulse \
               # --disable-avahi \
               # --disable-webserver \
               # --enable-ccache \
    ./configure --prefix=${_prefix} \
                --disable-hal \
<<<<<<< HEAD
                --enable-external-libraries \
=======
>>>>>>> 574000342bfa0d530e365fcf50f5246714c9768f
                --disable-external-ffmpeg \
                --disable-external-python \
                --disable-external-libass \
                --disable-pulse \
                --disable-avahi \
                --disable-webserver \
                --disable-debug || return 1

    # Now (finally) build
    msg "Running make" 
    make || return 1
}

package() {

    cd "${srcdir}/${_svnmod}"
    msg "Running make install" 
    make prefix=${pkgdir}${_prefix} install || return 1

    # Replace FEH.py with FEH.sh (and thus remove external python dependency)
    install -D -m 0755 ${srcdir}/FEH.sh ${pkgdir}${_prefix}/share/xbmc/FEH.sh || return 1

    sed -i -e "s/python \\${_prefix}\/share\/xbmc\/FEH.py \"\$@\"/\\${_prefix}\/share\/xbmc\/FEH.sh/g" ${pkgdir}${_prefix}/bin/xbmc || return 1

    # lsb_release fix
    sed -i -e 's/which lsb_release &> \/dev\/null/\[ -f \/etc\/arch-release ]/g' ${pkgdir}${_prefix}/bin/xbmc || return 1

    sed -i -e "s/lsb_release -a 2> \/dev\/null | sed -e 's\/\^\/    \/'/cat \/etc\/arch-release/g" ${pkgdir}${_prefix}/bin/xbmc || return 1

    # .desktop files
    install -D -m 0644 ${srcdir}/${_svnmod}/tools/Linux/xbmc.desktop ${pkgdir}${_prefix}/share/applications/xbmc.desktop || return 1

    install -D -m 0644 ${srcdir}/${_svnmod}/tools/Linux/xbmc.png ${pkgdir}${_prefix}/share/pixmaps/xbmc.png || return 1

    # Tools
    install -D -m 0755 ${srcdir}/${_svnmod}/xbmc-xrandr ${pkgdir}${_prefix}/share/xbmc/xbmc-xrandr || return 1

    install -D -m 0755 ${srcdir}/${_svnmod}/tools/TexturePacker/TexturePacker ${pkgdir}${_prefix}/share/xbmc/ || return 1

    # Licenses
    install -d -m 0755 ${pkgdir}${_prefix}/share/licenses/${pkgname}
    for licensef in LICENSE.GPL README.linux copying.txt; do
<<<<<<< HEAD
        install -m 0644 ${pkgdir}${_prefix}/share/doc/${licensef} ${pkgdir}${_prefix}/share/licenses/${pkgname} || return 1
=======
        mv ${pkgdir}${_prefix}/share/doc/${licensef} \
           ${pkgdir}${_prefix}/share/licenses/${pkgname} || return 1
>>>>>>> 574000342bfa0d530e365fcf50f5246714c9768f
    done
	
	# cleanup some stuff
	msg "Cleanup unneeded files"
	rm -rf ${pkgdir}/usr/share/xsessions
	rm -f ${pkgdir}/usr/share/xbmc/FEH.py

    # strip
	msg "Stripping binaries"
    find $pkgdir -type f -exec strip {} \; >/dev/null 2>/dev/null
}
