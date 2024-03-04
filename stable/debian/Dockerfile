FROM debian:buster-slim

ENV VARNISH_SIZE 100M

RUN set -e; \
    BASE_PKGS="curl dpkg-dev debhelper devscripts equivs git pkg-config apt-utils fakeroot"; \
    export DEBIAN_FRONTEND=noninteractive; \
    export DEBCONF_NONINTERACTIVE_SEEN=true; \
    tmpdir="$(mktemp -d)"; \
    cd "$tmpdir"; \
    apt-get update; \
    apt-get install -y --no-install-recommends $BASE_PKGS; \
    git clone https://github.com/varnishcache/pkg-varnish-cache.git; \
    cd pkg-varnish-cache; \
    rm -rf .git; \
    curl -f https://varnish-cache.org/downloads/varnish-4.0.5.tgz -o $tmpdir/orig.tgz; \
    echo "a08259f6f9c6fffa188b26c1f8c630de5e6d2f6d52f6efa9d5d8239cdd8721c53e2be3379f8100efb537e74416eadd6c865f4cc687db1c5a9f757bb3f73abeda  $tmpdir/orig.tgz" | sha512sum -c -; \
    tar xavf $tmpdir/orig.tgz --strip 1; \
    sed -i -e "s|@VERSION@|4.0.5|"  "debian/changelog"; \
    mk-build-deps --install --tool="apt-get -o Debug::pkgProblemResolver=yes --yes" debian/control; \
    sed -i '' debian/varnish*; \
    sed -i -e "s|usr/share/varnish/vsctool.py||" debian/varnish-dev.install; \
    sed -i -e "s|usr/share/varnish/vcc||" debian/varnish.install; \
    sed -i -e "s|usr/share/varnish/vcl||" debian/varnish.install; \
    dpkg-buildpackage -us -uc -j"$(nproc)"; \
    apt-get -y --no-install-recommends install ../*.deb; \
    apt-get -y purge --auto-remove varnish-build-deps $BASE_PKGS; \
    mkdir /pkgs; \
    mv ../*dev*.deb /pkgs; \
    rm -rf /var/lib/apt/lists/* "$tmpdir";

WORKDIR /etc/varnish

COPY scripts/ /usr/local/bin/
ENTRYPOINT ["/usr/local/bin/docker-varnish-entrypoint"]

EXPOSE 80 8443
CMD []
