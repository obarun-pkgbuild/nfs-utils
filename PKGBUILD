# Maintainer: Eric Vidal <eric@obarun.org>
# based on the original https://projects.archlinux.org/svntogit/packages.git/tree/trunk?h=packages/nfs-utils
# 						Maintainer: AndyRTR <andyrtr@archlinux.org>
# 						Maintainer: Tobias Powalowski <tpowa@archlinux.org>
# 						Contributor: John Proctor <jproctor@prium.net>
# 						Contributor: dibblethewrecker <dibblethewrecker.at.jiwe.org>
# 						Contributor: abelstr <abel@pinklf.eu>
# 						Contributor: Marco Lima <cipparello gmail com>

pkgname=nfs-utils
pkgver=2.1.1
pkgrel=6
pkgdesc="Support programs for Network File Systems"
arch=(x86_64)
url='http://nfs.sourceforge.net'
license=('GPL2')
backup=(etc/{exports,nfs.conf,nfsmount.conf})
depends=('rpcbind' 'nfsidmap' 'gssproxy' 'libevent' 'device-mapper')
makedepends=('sqlite')
provides=('nfs-utils=$pkgver')
source=(https://www.kernel.org/pub/linux/utils/${pkgname}/${pkgver}/${pkgname}-${pkgver}.tar.xz
		id_resolver.conf
        exports)
optdepends=('sqlite: for nfsdcltrack usage'
            'python2: for nfsiostat and mountstats usage'
            'nfs-utils-s6serv: nfs-utils s6 service'
            'nfs-utils-s6rcserv: nfs-utils s6-rc service'
            'nfs-utils-runitserv: nfs-utils runit service')
md5sums=('59dfcb2e6254b129f901f40c86086b13'
         '2e203f35ee753f5264a951cf43d4168e'
         'e6ad3c7a59c7e4c24965a0e7da35026c')
validpgpkeys=('6DD4217456569BA711566AC7F06E8FDE7B45DAAC') # Eric Vidal

prepare() {
  cd ${pkgname}-${pkgver}

  # fix hardcoded sbin path to our needs
  sed -i "s|sbindir = /sbin|sbindir = /usr/bin|g" utils/*/Makefile.am
  autoreconf -vfi
}

build() {
  cd ${pkgname}-${pkgver}
  ./configure --prefix=/usr \
    --sbindir=/usr/bin \
    --sysconfdir=/etc \
    --enable-gss \
    --without-tcp-wrappers \
    --with-statedir=/var/lib/nfs \
    --enable-ipv6 \
    --enable-libmount-mount \
    --enable-mountconfig \
    --with-start-statd=/usr/bin/start-statd \
    --with-systemd=no \
    --enable-nfsv4
  make 
}

check() {
  cd ${pkgname}-${pkgver}
  make -k check
}

package() {
  cd ${pkgname}-${pkgver}
  make DESTDIR="$pkgdir" install
  
  install -D -m 644 utils/mount/nfsmount.conf "$pkgdir"/etc/nfsmount.conf
  install -D -m 644 nfs.conf "$pkgdir"/etc/nfs.conf
  
  # docs
  install -d -m 755 "$pkgdir"/usr/share/doc/$pkgname
  install -m 644 {NEWS,README} "$pkgdir"/usr/share/doc/$pkgname/
  

  install -D -m 644 ../exports "$pkgdir"/etc/exports
  install -D -m 644 ../id_resolver.conf "$pkgdir"/etc/request-key.d/id_resolver.conf
  
  mkdir "$pkgdir"/etc/exports.d
  mkdir -m 555 "$pkgdir"/var/lib/nfs/rpc_pipefs
  mkdir "$pkgdir"/var/lib/nfs/v4recovery
}
