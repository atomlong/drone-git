# $Id$
# Maintainer: Chris Fordham <chris [at] fordham-nagy [dot] id [dot] au> aka flaccid
# Contributor: Mikkel Oscar Lyderik Larsen <m at moscar dot net>

pkgname=('drone-server-git' 'drone-agent-git')
_pkgname=drone
pkgver=1.10.0.r3.g6af2647e
pkgrel=1
pkgdesc="Drone is a Continuous Integration platform built on Docker, written in Go."
arch=('i686' 'x86_64' 'arm' 'armv6h' 'armv7h' 'aarch64')
url="http://github.com/drone/drone"
license=('Apache 2')
makedepends=('git' 'go')
backup=('etc/drone/server' 'etc/drone/agent')
source=('git+https://github.com/drone/drone.git'
        'drone-server.service'
        'drone-agent.service'
        'server.conf'
        'agent.conf')
sha1sums=('SKIP'
          '7b5132cd845d9fb869fed3c6ceb96f6105997c35'
          '7d7da680b78a51ba11a8bc63f93b09b228f5b544'
          '7669f9b31bc7391be1c0138845a7d2267d21999d'
          'f23754fe65f35c5d6e4d0b73a7b9448cfcb5e2e9')

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
    go build \
      -o release/drone-agent \
        github.com/drone/drone/cmd/drone-agent
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
  install -Dm644 "$_pkgname/LICENSE" "$pkgdir/usr/share/$_pkgname"

  # service
  install -Dm644 drone-server.service "$pkgdir/usr/lib/systemd/system/drone-server.service"

  # config
  install -Dm644 server.conf "$pkgdir/etc/drone/server"

  # db path
  install -dm700 "$pkgdir/var/lib/drone"
  chown -R 633:633 "$pkgdir/var/lib/drone"
}

package_drone-agent-git() {
  pkgdesc="Drone CI - Agent"
  install='drone-agent.install'
  provides=('drone-agent')
  depends=('docker')
  # binaries
  install -Dm755 "$srcdir/$_pkgname/release/drone-agent" "$pkgdir/usr/bin/drone-agent"

  # license
  install -Dm644 "$_pkgname/LICENSE" "$pkgdir/usr/share/$_pkgname"

  # service
  install -Dm644 drone-agent.service "$pkgdir/usr/lib/systemd/system/drone-agent.service"

  # config
  install -Dm644 agent.conf "$pkgdir/etc/drone/agent"
}

# vim:set ts=2 sw=2 ft=sh et:
