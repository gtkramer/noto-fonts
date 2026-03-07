# Maintainer: Antonio Rojas <arojas@archlinux.org>

pkgbase=noto-fonts
pkgname=(ttf-noto-fonts ttf-noto-fonts-extra otf-noto-fonts otf-noto-fonts-extra)
pkgver=2026.03.01
pkgrel=2
epoch=1
pkgdesc='Google Noto fonts'
arch=(any)
url='https://fonts.google.com/noto'
license=(OFL-1.1-no-RFN)
_font_root_dir="notofonts.github.io-noto-monthly-release-${pkgver}/fonts"
_fontconfig_files=(66-noto-sans.conf 66-noto-serif.conf 46-noto-sans.conf 46-noto-serif.conf)
source=("https://github.com/notofonts/notofonts.github.io/archive/refs/tags/noto-monthly-release-${pkgver}.zip")
source+=("${_fontconfig_files[@]}")
b2sums=('469753c89e0a50b222b0ce233b008156343f25b809596b935ed50526260a503a4e6e8ee187fb131d8dddb8ee789701cc1e0d914ec772973a4f589acaf10e484f'
        'e815ccb7ab5640b065719dc7d2a15fd09054d838f92d1c139bfb60ee4d84cad54800b2e0604746814470f7e9366b1c4858ba73ce308cba39164cc2375ff35d99'
        'd91cd726e49a4b0a8351299017b9693bbbb9c7761277ba528687894db7d3bc82312b299e255e47018c14b7d0afffba43c076f0f8513609a52d310f3f6f598e72'
        '0da01b10cb835c78d5d5469ca5572397d47fb90815009176a65d9e616e344ec8e5c786a164d8ef00586ba1f81b07d7f949f39b5950f577a9c0e47ec7eb732434'
        '88ff5f0df73498b47d8ee6256989aea8f760fa3558841901f3ec389c3f6d7906e8d83d82629ee5f11ba04f4da1dd7199decf32dbdd7fc9707b2791e3d6aa9cf5')

_install_fontconfig() {
  local conf_avail_dir="${pkgdir}/usr/share/fontconfig/conf.avail"
  local conf_default_dir="${pkgdir}/usr/share/fontconfig/conf.default"

  install -d -m755 "${conf_avail_dir}" "${conf_default_dir}"
  local conf
  for conf in "${_fontconfig_files[@]}"; do
    install -m644 "${srcdir}/${conf}" "${conf_avail_dir}/${conf}"
    ln -rs "${conf_avail_dir}/${conf}" "${conf_default_dir}/${conf}"
  done
}

_is_extra_font_variant() {
  local font_name="${1:?font name required}"
  [[ "${font_name}" == *-Condensed* || "${font_name}" == *-Semi* || "${font_name}" == *-Extra* ]]
}

_install_selected_fonts() {
  local font_format="${1:?font format required}"
  local font_variant="${2:?font variant required}"

  local font_target_dir="${pkgdir}/usr/share/fonts/${pkgbase//-fonts/}"
  install -d -m755 "${font_target_dir}"

  local -a font_find_expr
  case "${font_format}" in
    ttf)
      font_find_expr=(-iname '*.ttf' -o -iname '*.ttc')
      ;;
    *)
      font_find_expr=(-iname "*.${font_format}")
      ;;
  esac

  local font_dir
  while IFS= read -r -d '' font_dir; do
    local selected_font_dir
    if [[ -d "${font_dir}/full/${font_format}" ]]; then
      selected_font_dir="${font_dir}/full/${font_format}"
    elif [[ -d "${font_dir}/hinted/${font_format}" ]]; then
      selected_font_dir="${font_dir}/hinted/${font_format}"
    else
      continue
    fi

    local font_file
    while IFS= read -r -d '' font_file; do
      local font_name="${font_file##*/}"
      case "${font_variant}" in
        regular)
          _is_extra_font_variant "${font_name}" && continue
          ;;
        extra)
          _is_extra_font_variant "${font_name}" || continue
          ;;
        *)
          printf 'Unknown font variant selector: %s\n' "${font_variant}" >&2
          return 1
          ;;
      esac

      install -m644 "${font_file}" -t "${font_target_dir}"
    done < <(find "${selected_font_dir}" -type f \( "${font_find_expr[@]}" \) -print0)
  done < <(find "${srcdir}/${_font_root_dir}" -mindepth 1 -maxdepth 1 -type d -print0)
}

package_ttf-noto-fonts() {
  optdepends=(
    'ttf-noto-fonts-extra: Extra width and weight variants (condensed, semi, extra)'
    'ttf-noto-fonts-cjk: Chinese, Japanese, and Korean glyphs'
    'ttf-noto-fonts-emoji: Emoji glyphs'
  )
  provides=(noto-fonts ttf-font)
  conflicts=(noto-fonts)
  replaces=(noto-fonts)

  _install_selected_fonts ttf regular
  install -D -m644 "${srcdir}/${_font_root_dir}/LICENSE" -t "${pkgdir}/usr/share/licenses/${pkgname}"

  _install_fontconfig
}

package_ttf-noto-fonts-extra() {
  pkgdesc="Extra variants of ${pkgdesc}"
  depends=(ttf-noto-fonts)
  provides=(noto-fonts-extra)
  conflicts=(noto-fonts-extra)
  replaces=(noto-fonts-extra)

  _install_selected_fonts ttf extra
  install -D -m644 "${srcdir}/${_font_root_dir}/LICENSE" -t "${pkgdir}/usr/share/licenses/${pkgname}"
}

package_otf-noto-fonts() {
  optdepends=(
    'otf-noto-fonts-extra: Extra width and weight variants (condensed, semi, extra)'
    'otf-noto-fonts-cjk: Chinese, Japanese, and Korean glyphs'
    'ttf-noto-fonts-emoji: Emoji glyphs'
  )
  provides=(noto-fonts)
  conflicts=(noto-fonts)

  _install_selected_fonts otf regular
  install -D -m644 "${srcdir}/${_font_root_dir}/LICENSE" -t "${pkgdir}/usr/share/licenses/${pkgname}"

  _install_fontconfig
}

package_otf-noto-fonts-extra() {
  pkgdesc="Extra variants of ${pkgdesc}"
  depends=(otf-noto-fonts)
  provides=(noto-fonts-extra)
  conflicts=(noto-fonts-extra)

  _install_selected_fonts otf extra
  install -D -m644 "${srcdir}/${_font_root_dir}/LICENSE" -t "${pkgdir}/usr/share/licenses/${pkgname}"
}
