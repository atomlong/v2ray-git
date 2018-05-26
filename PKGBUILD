# Maintainer: dctxmei <dctxmei@gmail.com>
# Maintainer: Felix Yan <felixonmars@archlinux.org>
# Contributor: pandada8 <pandada8@gmail.com>

pkgname=v2ray-git
pkgver=3.23
pkgrel=1
pkgdesc="A platform for building proxies to bypass network restrictions"
arch=("x86_64")
url="https://github.com/v2ray/v2ray-core"
license=("MIT")
makedepends=("git" "go")
source=("git+${url}.git"
        "v2ray.service")
sha512sums=("SKIP"
            "bebb87f43b3920be8efde9aece9c4305323cd7d3a65a3b2441f0dbd0098c02a18ca89a5b08833eee70482cffae2c8e7b7237bb5a4d1076e3c30d273898e2bedb")

pkgver() {
    git describe | sed -e 's/^v//' -e 's/-/./g'
}

prepare() {
    export GOPATH="$srcdir/build"
    go get v2ray.com/core github.com/miekg/dns golang.org/x/sys/unix v2ray.com/ext/assert \
        github.com/gorilla/websocket golang.org/x/crypto/chacha20poly1305 golang.org/x/crypto/sha3 \
        v2ray.com/ext golang.org/x/net/proxy github.com/google/go-github/github golang.org/x/oauth2 \
        github.com/gogo/protobuf/proto google.golang.org/grpc
    go install v2ray.com/ext/tools/build/vbuild
}

build() {
    cd v2ray-core
    "$GOPATH"/bin/vbuild
}

check() {
    cd v2ray-core
    go test -p 1 -tags json -v v2ray.com/core/...
}

package() {
    cd v2ray-core
    install -Dm644 LICENSE "$pkgdir"/usr/share/licenses/v2ray/LICENSE
    install -Dm644 release/config/systemd/v2ray.service ${pkgdir}/usr/lib/systemd/system/v2ray.service
    sed -i '/ExecStart/c\ExecStart=/usr/bin/env v2ray.location.asset=/etc/v2ray /usr/bin/v2ray -config /etc/v2ray/config.json' "$pkgdir"/usr/lib/systemd/system/v2ray.service
    install -Dm644 release/config/geoip.dat release/config/geosite.dat release/config/*.json -t "$pkgdir"/usr/bin/
    install -Dm755 "$GOPATH"/bin/v2ray-custom-linux-64/{v2ray,v2ctl} -t "$pkgdir"/usr/bin/

    install -Dm644 "$srcdir"/v2ray.service "$pkgdir"/usr/lib/systemd/system/v2ray@.service
}
