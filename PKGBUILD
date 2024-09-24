# Maintainer: Xianyi Lin <iynaix@gmail.com>
pkgbase="focal-git"
pkgname=('focal-hyprland-git' 'focal-sway-git')
_pkgname=focal
pkgver=r42.4c86317
pkgrel=1
pkgdesc="Rofi menu for capturing and copying screenshots or videos on hyprland / sway."
arch=(x86_64 aarch64)
url="https://github.com/iynaix/focal"
license=('MIT')
makedepends=(git cargo)
optdepends=('tesseract: OCR support'
			'waybar: waybar module support')
source=("$_pkgname::git+$url.git")
sha256sums=('SKIP')

_depends=(glibc gcc-libs ffmpeg grim rofi-wayland slurp wf-recorder wl-clipboard)

pkgver() {
  cd "$_pkgname"
  printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

build() {
  # fix ELF file ('usr/bin/focal') lacks GNU_PROPERTY_X86_FEATURE_1_SHSTK.
  export RUSTFLAGS='-C link-args=-Wl,-z,shstk'
  RUSTUP_TOOLCHAIN=stable cargo build --release --frozen --manifest-path=$_pkgname/Cargo.toml --target-dir=$_pkgname/target --no-default-features --features="hyprland,ocr"
}

build_focal-sway-git() {
  # fix ELF file ('usr/bin/focal') lacks GNU_PROPERTY_X86_FEATURE_1_SHSTK.
  export RUSTFLAGS='-C link-args=-Wl,-z,shstk'
  RUSTUP_TOOLCHAIN=stable cargo build --release --frozen --manifest-path=$_pkgname/Cargo.toml --target-dir=$_pkgname/target --no-default-features --features="sway,ocr"
}

_package() {
  install -Dm755 "$_pkgname/target/release/${_pkgname}"{,-waybar} -t "$pkgdir/usr/bin"

  for cmd in focal focal-waybar; do
    $_pkgname/target/release/$cmd generate bash > "$_pkgname/target/release/$cmd.bash"
    $_pkgname/target/release/$cmd generate zsh > "$_pkgname/target/release/$cmd.zsh"
    $_pkgname/target/release/$cmd generate fish > "$_pkgname/target/release/$cmd.fish"

    install -Dm644 "$_pkgname/target/release/$cmd.bash" "$pkgdir/usr/share/bash-completion/completions/$cmd"
    install -Dm644 "$_pkgname/target/release/$cmd.zsh" "$pkgdir/usr/share/zsh/site-functions/_$cmd"
    install -Dm644 "$_pkgname/target/release/$cmd.fish" "$pkgdir/usr/share/fish/vendor_completions.d/$cmd.fish"
  done
}

package_focal-hyprland-git() {
  depends=("${_depends[@]}" hyprland)
  provides=("focal")
  conflicts=("focal-sway-git")

  _package
  install -Dm644 "$_pkgname/LICENSE" "${pkgdir}/usr/share/licenses/focal-hyprland-git/LICENSE"
}

package_focal-sway-git() {
  depends=("${_depends[@]}" sway)
  provides=("focal")
  conflicts=("focal-sway-git")

  _package
  install -Dm644 "$_pkgname/LICENSE" "${pkgdir}/usr/share/licenses/focal-sway-git/LICENSE"
}
