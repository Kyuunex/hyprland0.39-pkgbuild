pkgname=hyprland0.39
projectname=hyprland
pkgver=0.39.1
pkgrel=5
pkgdesc='a highly customizable dynamic tiling Wayland compositor'
arch=(x86_64 aarch64)
url="https://github.com/hyprwm/${projectname^}"
license=(BSD-3-Clause)
provides=(hyprland)
conflicts=(hyprland)
depends=(cairo # libcairo.so
         gcc-libs # libgcc_s.so libstdc++.so
         glibc # libc.so libm.so
         glib2 libgobject-2.0.so
         glslang
         hyprcursor libhyprcursor.so
         hyprlang libhyprlang.so
         libdisplay-info libdisplay-info.so
         libdrm # libdrm.so
         libglvnd libEGL.so libGLESv2.so libOpenGL.so
         libinput # libinput.so
         libliftoff libliftoff.so
         libx11
         libxcb # libxcb-dri3.so libxcb-present.so libxcb-render.so libxcb-res.so libxcb-shm.so libxcb.so libxcb-xfixes.so libxcb-xinput.so
         libxcomposite # libxcb-composite.so
         libxfixes
         libxkbcommon libxkbcommon.so
         libxrender
         mesa # libgbm.so
         opengl-driver
         pango libpango-1.0.so libpangocairo-1.0.so
         pixman libpixman-1.so
         seatd libseat.so
         systemd-libs libsystemd.so
         tomlplusplus libtomlplusplus.so libudev.so
         vulkan-icd-loader
         vulkan-validation-layers
         wayland libwayland-client.so libwayland-server.so
         wayland-protocols
         xcb-proto
         xcb-util
         xcb-util-errors # libxcb-errors.so
         xcb-util-image
         xcb-util-keysyms
         xcb-util-renderutil # libxcb-render-util.so 
         xcb-util-wm # libxcb-ewmh.so  libxcb-icccm.so 
         xorg-xinput
         xorg-xwayland)
makedepends=(cmake
             gdb
             meson
             ninja
             vulkan-headers
             xorgproto)
optdepends=('cmake: to build and install plugins using hyprpm'
            'cpio: to build and install plugins using hyprpm'
            'meson: to build and install plugins using hyprpm')
_archive="${projectname^}-$pkgver"
source=("$_archive.tar.gz::$url/releases/download/v$pkgver/source-v$pkgver.tar.gz"
        "wlroots-hyprland-91de8da.zip::https://github.com/hyprwm/wlroots-hyprland/archive/91de8da4b6b9b3c5630123d2446cd6de4e80071a.zip")
sha256sums=('48d1c58fd8db365635a56d76e05e6ea2d99fa20c020a061ee88a20407cfde787'
            'd2b898ad150abdc05693d0a0eedb5e7a18f03a8167f00660fb395bf3e7055c4f')

prepare() {
	ln -sf hyprland-source "$_archive"
	cd "$_archive"
	rm -rf "subprojects/wlroots-hyprland"
	cp -r "../wlroots-hyprland-91de8da4b6b9b3c5630123d2446cd6de4e80071a" "subprojects/wlroots-hyprland"
	sed -i -e '/^release:/{n;s/-D/-DCMAKE_SKIP_RPATH=ON -D/}' Makefile
}

build() {
	cd "$_archive"
	make release
}

package() {
	cd "$_archive"
	find src \( -name '*.h' -o -name '*.hpp' \) -exec install -Dm0644 {} "$pkgdir/usr/include/hyprland/{}" \;
	pushd subprojects/wlroots-hyprland/include
	find . -name '*.h' -exec install -Dm0644 {} "$pkgdir/usr/include/hyprland/wlroots-hyprland/{}" \;
	popd
	pushd subprojects/wlroots-hyprland/build/include
	find . -name '*.h' -exec install -Dm0644 {} "$pkgdir/usr/include/hyprland/wlroots-hyprland/{}" \;
	popd
	mkdir -p "$pkgdir/usr/include/hyprland/protocols"
	cp protocols/*-protocol.h "$pkgdir/usr/include/hyprland/protocols"
	pushd build
	cmake -DCMAKE_INSTALL_PREFIX=/usr ..
	popd
	install -Dm0644 -t "$pkgdir/usr/share/pkgconfig" build/hyprland.pc
	install -Dm0644 -t "$pkgdir/usr/share/man/man1" docs/{Hyprland,hyprctl}.1
	for cmd in hyprctl hyprpm; do
		install -Dm0644 "$cmd/$cmd.bash" "$pkgdir/usr/share/bash-completion/completions/$cmd"
		install -Dm0644 "$cmd/$cmd.zsh" "$pkgdir/usr/share/zsh/site-functions/_$cmd"
		install -Dm0644 -t "$pkgdir/usr/share/fish/vendor_completions.d/" "$cmd/$cmd.fish"
	done
	install -Dm0755 -t "$pkgdir/usr/bin/" build/Hyprland build/hyprctl/hyprctl build/hyprpm/hyprpm
	install -Dm0644 -t "$pkgdir/usr/share/$projectname/" assets/*.png
	install -Dm0644 -t "$pkgdir/usr/share/wayland-sessions/" "example/$projectname.desktop"
	install -Dm0644 -t "$pkgdir/usr/share/$projectname/" "example/$projectname.conf"
	install -Dm0644 -t "$pkgdir/usr/share/licenses/$projectname/" LICENSE
        find subprojects/wlroots-hyprland/build -name 'libwlroots.so.*' -type f -execdir \
                install -Dm0755 -t "$pkgdir/usr/lib/" {} \;
}
