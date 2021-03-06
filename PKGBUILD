# Maintainer: DonVla <donvla@users.sourceforge.net>
# Contributor: BlackEagle < ike DOT devolder AT herecura DOT be >
# Contributor: Ulf Winkelvos <ulf [at] winkelvos [dot] de>
# Contributor: Ralf Barth <archlinux dot org at haggy dot org>
#
# Original credits go to Edgar Hucek <gimli at dark-green dot com>
# for his xbmc-vdpau-vdr PKGBUILD at https://archvdr.svn.sourceforge.net/svnroot/archvdr/trunk/archvdr/xbmc-vdpau-vdr/PKGBUILD

pkgname=xbmc-svn
pkgver=31122
pkgrel=2
pkgdesc="XBMC Media Center from SVN"
provides=('xbmc')
conflicts=('xbmc' 'xbmc-pulse')
arch=('i686' 'x86_64')
url="http://xbmc.svn.sourceforge.net/viewvc/xbmc/trunk"
license=('GPL' 'LGPL')
depends=('bzip2' 'faac' 'faad2' 'ffmpeg' 'fribidi' 'glew' 'jasper' 'libass' 'libcdio' 'libgl' 'libmad' 
         'libmicrohttpd' 'libmms' 'libmodplug' 'libmpeg2' 'libmysqlclient' 'libsamplerate' 
         'libxinerama' 'libxtst' 'lzo2' 'sdl_image>=1.2.10' 'sdl_mixer' 'smbclient' 'wavpack')
makedepends=('cvs' 'boost' 'cmake' 'gperf' 'nasm' 'subversion' 'zip')
optdepends=('avahi: to use zerconf features (remote, etc...)' 
            'gdb: for meaningful backtraces in case of trouble - STRONGLY RECOMMENDED' 
            'libssh: support for sshfs'
            'libvdpau: accelerated video playback for nvidia cards' 
            'libva-sds: accelerated video playback for nvidia, ati/amd and some intel cards'
            'lirc: remote controller support' 
            'pulseaudio: pulseaudio support'
            'udisks: automount external drives' 
            'upower: used to trigger suspend functionality' 
            'unrar: access compressed files without unpacking them')
options=('makeflags')
install="${pkgname}.install"
source=("FEH.sh") 
md5sums=('c3e2ab79b9965f1a4a048275d5f222c4')
sha256sums=('1b391dfbaa07f81e5a5a7dfd1288bf2bdeab8dc50bbb6dbf39a80d8797dfaeb0')

_svnmod=XBMC
_prefix=/usr

build() {

    _svntrunk=http://xbmc.svn.sourceforge.net/svnroot/xbmc/trunk

    cd ${srcdir}/
    if [ -d $_svnmod/.svn ]; then
        msg "SVN tree found, reverting changes and updating to -r$pkgver"
        (cd $_svnmod && svn revert -R . && make distclean; svn up -r $pkgver) || return 1
    else
        msg "Checking out SVN tree of -r$pkgver"
        svn co $_svntrunk --config-dir ./ -r $pkgver $_svnmod || return 1
    fi

    # Configure XBMC
    #
    # Note on external-libs:
    #   - We cannot use external python because Arch's python was built with
    #     UCS2 unicode support, whereas xbmc expects UCS4 support
    cd "${srcdir}/${_svnmod}"

    # Archlinux Branding by SVN_REV
    export SVN_REV="${pkgver}-ARCH"

    # fix lsb_release dependency: IS THIS NEEDED???
    sed -i -e 's:/usr/bin/lsb_release -d:cat /etc/arch-release:' xbmc/utils/SystemInfo.cpp || return 1

    msg "Bootstrapping XBMC"
    ./bootstrap || return 1

    msg "Configuring XBMC" 
    ./configure --prefix=${_prefix} \
                --disable-hal \
                --enable-external-libraries \
                --enable-external-libass \
                --enable-external-ffmpeg \
                --disable-external-python \
                --enable-debug || return 1

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
    sed -i -e 's/^python \(.*\)FEH.py \(.*\)$/\1FEH.sh \2/' ${pkgdir}${_prefix}/bin/xbmc || return 1

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
    for licensef in LICENSE.GPL copying.txt; do 
        mv ${pkgdir}${_prefix}/share/doc/${licensef} ${pkgdir}${_prefix}/share/licenses/${pkgname} || return 1 
    done 

# Docs 
    install -d -m 0755 ${pkgdir}${_prefix}/share/doc/${pkgname} 
    for docsf in keymapping.txt README.linux; do 
        mv ${pkgdir}${_prefix}/share/doc/${docsf} ${pkgdir}${_prefix}/share/doc/${pkgname} || return 1 
     done

# cleanup some stuff
    msg "Cleanup unneeded files"
    rm -rf ${pkgdir}/usr/share/xsessions
    rm -f ${pkgdir}/usr/share/xbmc/FEH.py

# strip
    msg "Stripping binaries"
    find $pkgdir -type f -exec strip {} \; >/dev/null 2>/dev/null
}
