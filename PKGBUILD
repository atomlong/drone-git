# Maintainer: Atom Long <atom.long@hotmail.com>

pkgname=('drone-server-git')
_pkgname=drone
pkgver=2.0.3.r2.g4b7f52ad
pkgrel=1
pkgdesc="Drone is a Continuous Integration platform built on Docker, written in Go."
arch=('i686' 'x86_64' 'arm' 'armv6h' 'armv7h' 'aarch64')
url="http://github.com/drone/drone"
license=('Apache 2')
makedepends=('git' 'go')
backup=('etc/drone/server')
source=('git+https://github.com/drone/drone.git'
        'drone-server.service'
        'server.conf')
sha256sums=('SKIP'
			'5be757ee7375ec0b264a0f61745a045c1a605a460b22b8ccd3d86e5cbc2764c2'
			'4939f041cacbcab38aa2ea1af0fb229bc5afddfac28b8acf62f931ae632d913a')

pkgver() {
  cd "$srcdir/$_pkgname"
  git describe --long --tags | sed 's/^v//;s/\([^-]*-g\)/r\1/;s/-/./g'
}

prepare() {
  # setup local go env
  export GOPATH="$srcdir/go"
  mkdir -p "$GOPATH/src/github.com/drone"
  ln -sf "$srcdir/$_pkgname" "$GOPATH/src/github.com/drone/"

  # install go dependencies manually
  msg2 'installing go dependencies'
  pushd "$GOPATH/src/github.com/drone/drone" >/dev/null
    go get ./...
  popd
}

build() {
  msg2 'building drone-server'
  pushd "$GOPATH/src/github.com/drone/$_pkgname" >/dev/null
    go build \
      -o release/drone-server \
        github.com/drone/drone/cmd/drone-server
  popd
}

package_drone-server-git() {
  pkgdesc="Drone CI - Server"
  install='drone-server.install'
  provides=('drone-server')
  conflicts=('drone')
  # binaries
  install -Dm755 "$srcdir/$_pkgname/release/drone-server" "$pkgdir/usr/bin/drone-server"

  # license
  install -Dm644 "$_pkgname/LICENSE" "$pkgdir/usr/share/${pkgname}"

  # service
  install -Dm644 drone-server.service "$pkgdir/usr/lib/systemd/system/drone-server.service"

  # config
  install -Dm644 server.conf "$pkgdir/etc/drone/server"

  # db path
  install -dm700 "$pkgdir/var/lib/drone"
  chown -R 633:633 "$pkgdir/var/lib/drone"
}

# vim:set ts=2 sw=2 ft=sh et:
