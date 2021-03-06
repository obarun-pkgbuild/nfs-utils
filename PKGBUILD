# Maintainer: Eric Vidal <eric@obarun.org>
# based on the original https://projects.archlinux.org/svntogit/packages.git/tree/trunk?h=packages/nfs-utils
# 						Maintainer: AndyRTR <andyrtr@archlinux.org>
# 						Maintainer: Tobias Powalowski <tpowa@archlinux.org>
# 						Contributor: John Proctor <jproctor@prium.net>
# 						Contributor: dibblethewrecker <dibblethewrecker.at.jiwe.org>
# 						Contributor: abelstr <abel@pinklf.eu>
# 						Contributor: Marco Lima <cipparello gmail com>

pkgbase=nfs-utils
pkgname=('nfs-utils' 'nfsidmap')
pkgver=2.3.1
pkgrel=2
pkgdesc="Support programs for Network File Systems"
arch=(x86_64)
url='http://nfs.sourceforge.net'
makedepends=('libevent' 'sqlite' 'device-mapper')
provides=('nfs-utils=$pkgver')
source=(https://www.kernel.org/pub/linux/utils/${pkgname}/${pkgver}/${pkgname}-${pkgver}.tar.xz
		id_resolver.conf
        exports)
md5sums=('d77b182a9ee396aa6221ac2401ad7046'
         '2e203f35ee753f5264a951cf43d4168e'
         'e6ad3c7a59c7e4c24965a0e7da35026c')
validpgpkeys=('6DD4217456569BA711566AC7F06E8FDE7B45DAAC') # Eric Vidal

prepare() {
  cd ${pkgbase}-${pkgver}

  # fix hardcoded sbin path to our needs
  sed -i "s|sbindir = /sbin|sbindir = /usr/bin|g" utils/*/Makefile.am
  autoreconf -vfi
}

build() {
  cd ${pkgbase}-${pkgver}
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
  cd ${pkgbase}-${pkgver}
  make -k check || /bin/true
}

package_nfs-utils() {
  pkgdesc="Support programs for Network File Systems"
  license=('GPL2')

   backup=(etc/{exports,nfs.conf,nfsmount.conf})
   depends=('rpcbind' 'nfsidmap' 'gssproxy' 'libevent' 'device-mapper')
   optdepends=('sqlite: for nfsdcltrack usage'
              'python: for nfsiostat and mountstats usage')

  cd ${pkgbase}-${pkgver}
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
  
  # nfsidmap cleanup
  rm -vrf $pkgdir/usr/include #/nfsid*
  rm -vrf $pkgdir/usr/lib/libnfsidmap*
  rm -vrf $pkgdir/usr/lib/pkgconfig #/libnfsidmap.pc
  rm -v $pkgdir/usr/share/man/{man3/nfs4_uid_to_name*,man5/idmapd.conf*}
  rm -rf $pkgdir/usr/share/man/man3
}

package_nfsidmap() {

  pkgdesc="Library to help mapping IDs, mainly for NFSv4"
  license=('GPL2')
  backup=(etc/idmapd.conf)
  depends=('libldap')

  cd ${pkgbase}-${pkgver}
  make -C support  DESTDIR="$pkgdir" install
  # config file  
  install -D -m 644 support/nfsidmap/idmapd.conf "$pkgdir"/etc/idmapd.conf
  # license
  install -Dm644 support/nfsidmap/COPYING $pkgdir/usr/share/licenses/nfsidmap/LICENSE
}

