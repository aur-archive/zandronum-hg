# Maintainer: Robert La Spina <rkidlaspina at gmail dot com>
# Based on the skulltag package by Mikael Eriksson <mikael_eriksson@miffe.org>
# and Sean Streeter <anubis2591 at gmail dot com>
pkgname=zandronum-hg
pkgver=3499
pkgrel=1
pkgdesc="OpenGL ZDoom port with Client/Server multiplayer."
arch=('i686' 'x86_64')
url="http://zandronum.com/"
license=('custom')
provides=('zandronum')
makedepends=('cmake' 'mercurial' 'yasm' 'mesa') # Not sure if it needs yasm.
depends=('sdl' 'gtk2' 'libjpeg6')
optdepends=('timidity++: midi support'
            'freedoom: free IWAD')
install=zandronum.install

_64=

source=(
    "http://www.fmod.org/index.php/release/version/fmodapi42416linux.tar.gz"
    "zandronum.patch"
    )
md5sums=(
    '00b157e8d09d539b0a7023c48b71422a'
    '0690cb3ad70c360bf721ffee43a17274'
    )

if [ $CARCH == x86_64 ]; then
    source[0]="http://www.fmod.org/index.php/release/version/fmodapi42416linux64.tar.gz"
    depends=(${depends[@]} libpng12)
    md5sums[0]='70b2a6a2618ee9823ab564b4e945c885'
    _64=64
fi

_hgroot=https://bitbucket.org/Torr_Samaho
_hgrepo=zandronum

build() {
 
cd $srcdir
msg "Connecting to Mercurial server...."

  if [[ -d "$_hgrepo" ]]; then
    cd "$_hgrepo"
    hg pull -u || true
    msg "The local files are updated."
  else
    hg clone "${_hgroot}/${_hgrepo}" "$_hgrepo" 
  fi

  msg "Mercurial checkout done or server timeout"
  msg "Starting build..."

  rm -rf "$srcdir/$_hgrepo-build"
  hg clone  "$srcdir/$_hgrepo" "$srcdir/$_hgrepo-build"
  cd "$srcdir/$_hgrepo-build"
  patch -p1 -i $srcdir/zandronum.patch

  cp -r "$srcdir/fmodapi42416linux${_64}/" "$srcdir/$_hgrepo-build/"
  mkdir zan-bin
  cd zan-bin
  cmake ..
  make
}

package(){
  cd $srcdir/zandronum-build/zan-bin
  cat > zandronum.sh << EOF
#!/bin/sh
export LD_LIBRARY_PATH=/usr/share/zandronum/lib
exec /usr/share/zandronum/zandronum "\$@"
EOF

  cat > zandronum-server.sh << EOF
#!/bin/sh
export LD_LIBRARY_PATH=/usr/share/zandronum/lib
exec /usr/share/zandronum/zandronum -host "\$@"
EOF

  
  install -Dm644 "zandronum.pk3" "$pkgdir/usr/share/zandronum/zandronum.pk3"
  install -Dm644 "brightmaps.pk3" "$pkgdir/usr/share/zandronum/brightmaps.pk3"
  install -Dm644 "skulltag_actors.pk3" "$pkgdir/usr/share/zandronum/skulltag_actors.pk3"
  install -Dm755 "output_sdl/liboutput_sdl.so" "$pkgdir/usr/share/zandronum/lib/liboutput_sdl.so"
  install -Dm755 "snes_spc/libsnes_spc.a" "$pkgdir/usr/share/zandronum/lib/libsnes_spc.so"
  install -Dm755 "zandronum" "$pkgdir/usr/share/zandronum/zandronum"
  install -Dm755 "zandronum.sh" "$pkgdir/usr/bin/zandronum"
  install -Dm755 "zandronum-server.sh" "$pkgdir/usr/bin/zandronum-server"

  install -Dm755 "../fmodapi42416linux${_64}/api/lib/libfmodex${_64}-4.24.16.so" "$pkgdir/usr/share/zandronum/lib/libfmodex${_64}-4.24.16.so"
  install -Dm644 "../LICENSE.txt" "$pkgdir/usr/share/licenses/${pkgname}/LICENSE"
}

# vim:set ts=2 sw=2 et:
