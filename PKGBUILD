# SPDX-License-Identifier: AGPL-3.0

#    ----------------------------------------------------------------------
#    Copyright © 2024, 2025  Pellegrino Prevete
#
#    All rights reserved
#    ----------------------------------------------------------------------
#
#    This program is free software: you can redistribute it and/or modify
#    it under the terms of the GNU Affero General Public License as published by
#    the Free Software Foundation, either version 3 of the License, or
#    (at your option) any later version.
#
#    This program is distributed in the hope that it will be useful,
#    but WITHOUT ANY WARRANTY; without even the implied warranty of
#    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#    GNU Affero General Public License for more details.
#
#    You should have received a copy of the GNU Affero General Public License
#    along with this program.  If not, see <https://www.gnu.org/licenses/>.

# Maintainer: Truocolo <truocolo@aol.com>
# Maintainer: Truocolo <truocolo@0x6E5163fC4BFc1511Dbe06bB605cc14a3e462332b>
# Maintainer: Pellegrino Prevete (dvorak) <pellegrinoprevete@gmail.com>
# Maintainer: Pellegrino Prevete (dvorak) <dvorak@0x87003Bd6C074C713783df04f36517451fF34CBEf>
# Contributor: Matheus <matheusgwdl@protonmail.com>
# Contributor: Serge K <arch@phnx47.net>
# Contributor: Nicola Squartini <tensor5@gmail.com>

# shellcheck disable=SC2034
# shellcheck disable=SC2154
_os="$( \
  uname \
    -o)"
_pkg="solidity"
pkgver="0.7.5"
pkgname="${_pkg}${pkgver}"
pkgrel="1"
pkgdesc="Smart contract programming language."
arch=(
  "x86_64"
  "i686"
  "aarch64"
  "arm"
  "armv7l"
  "armv6l"
  "mips"
  "powerpc"
  "pentium4"
)
_http="https://github.com"
_ns="ethereum"
url="${_http}/${_ns}/${_pkg}"
license=(
  "GPL-3.0-or-later"
)
depends=()
if [[ "${_os}" == "Android" ]]; then
  depends+=(
    "boost"
  )
elif [[ "${_os}" == "GNU/Linux" ]]; then
  depends+=(
    "boost-libs"
  )
fi
optdepends=(
  "cvc4: SMT checker"
  "z3: SMT checker"
)
makedepends=(
  "cmake"
)
if [[ "${_os}" == "Android" ]]; then
  makedepends+=(
    "boost-headers"
    "boost-static"
  )
elif [[ "${_os}" == "GNU/Linux" ]]; then
  makedepends+=(
    "boost"
  )
fi
checkdepends=(
  "evmone"
)
provides=(
  "${_pkg}=${pkgver}"
  "solc=${pkgver}"
)
conflicts=(
  "${pkgname}-bin"
  # "${_pkg}-git"
)
_patches=(
  "0001-${pkgname}-basename-CommandLineInterface.patch"
  "0002-${pkgname}-cstdint-include-YulString.patch"
  "0003-${pkgname}-cstdint-include-EWasmObjectCompiler.patch"
  "0004-${pkgname}-limits-change-SourceLocation.patch"
  "0005-${pkgname}-fstream-include-SourceLocation.patch"
)
source=(
  "${_pkg}-v${pkgver}.tar.gz::${url}/releases/download/v${pkgver}/${_pkg}_${pkgver}.tar.gz"
  "${_patches[@]}"
)
sha512sums=(
  'ec095a5d8dc00187ef6df03fd14f8ce5da3f437ca55311617211ded7bd6461a680e7a42bee0819fe639cd1ef3554f9b363bcdc9cf04dddf6e136e1543a5e5f3a'
  'fa8e3e56a72a3fc8e912c7ebb729d3c3c5eb0a88af49009d1be33610d01d994cd4ecec20e90a5a10c45d5c3976209dcc0c6c84a5fbb80a33811550aa02847137'
  '4af0991bc6505d7e1976a478fad893da6cee097791f5c0d38bbb9cd3f6e1a4a6d44b89a75be2671723217333cb2b83442f1cf8cf018bd28667079b765e3c2d7d'
  'dd90bae065e09ba725797ac508f1a688b424ed85f491243e0998389e0158faadb863aeb94220869c8568de51f8f74a8f6515fd699d4d71dc4129ed47112a3f9c'
  '6fdb366cb69c12b94c5987d435faf5a341316940f61457a41a2bc0177e5b3d2a918608222ec152c54ebf38093ef55513c5f21148896d6ffc06d170a813c35b21'
  'cea3809e8e2c87f8700b55dece46a3c89adfe75ebffdb8ed27702b0382228bd45290e703f03b3e7d59beea7f1973dcfa044cc8747f8041d026d97ffd5d8a7d97'
)

prepare() {
  local \
    _patch
  cd \
    "${srcdir}/${_pkg}_${pkgver}"
  _boost_version="$( \
    _boost_version_get)"
  if _verlt \
       "1.83.0" \
       "${_boost_version}"; then
    msg \
      "Installed boost version" \
      ">=1.83.0, applying patches"
  for _patch in "${_patches[@]}"; do
    patch \
      -uNp1 \
      -i \
        "${srcdir}/${_patch}" || \
      return 1
  done
  fi
}

_boost_version_get() {
  pacman \
    -Qi \
    boost | \
    grep \
      "Version" | \
      awk \
        '{print $3}' | \
        rev | \
          cut \
            -d \
              "-" \
            -f \
	      2 | \
            rev
}

_verlte() {
  printf \
    '%s\n' \
    "${1}" \
    "${2}" | \
    sort \
      -C \
      -V
}

_verlt() {
  ! _verlte \
      "${2}" \
      "${1}"
}

_compile() {
  local \
    _tests="${1}" \
    _boost_version \
    _cmake_opts=() \
    _cxxflags=()
  _cxxflags=(
    "${CXXFLAGS}"
    -Wno-deprecated-declarations
  )
  if [[ "${_os}" == "Android" ]]; then
    _cxxflags+=(
      -Wno-unused-but-set-variable
      -Wno-unqualified-std-cast-call
      -Wno-dangling-field
    )
  elif [[ "${_os}" == "GNU/Linux" ]]; then
    _cxxflags+=(
      -Wno-overloaded-virtual
    )
  fi
  _cmake_opts=(
    # -D CMAKE_BUILD_TYPE="None"
    -D CMAKE_BUILD_TYPE="Release"
    -D CMAKE_INSTALL_PREFIX="/usr/"
    -D CMAKE_EXECUTABLE_SUFFIX="${pkgver}"
    -D ONLY_BUILD_SOLIDITY_LIBRARIES="OFF"
    -D PEDANTIC="ON"
    -D PROFILE_OPTIMIZER_STEPS="OFF"
    -D SOLC_LINK_STATIC="OFF"
    -D SOLC_STATIC_STDLIBS="OFF"
    -D STRICT_NLOHMANN_JSON_VERSION="OFF"
    -D STRICT_Z3_VERSION="OFF"
    -D TESTS="${_tests}"
    -D USE_LD_GOLD="OFF"
    -D USE_SYSTEM_LIBRARIES="OFF"
    -DCMAKE_CXX_FLAGS="${_cxxflags[*]}"
    -S "${srcdir}/${_pkg}_${pkgver}/"
    -Wno-dev
  )
  export \
    CXXFLAGS="${_cxxflags[*]}"
  CXXFLAGS="${_cxxflags[*]}" \
  cmake \
    -B \
      "${srcdir}/${_pkg}_${pkgver}/build/" \
    "${_cmake_opts[@]}"
  CXXFLAGS="${_cxxflags[*]}" \
  cmake \
    --build \
      "${srcdir}/${_pkg}_${pkgver}/build/"
}

build()
{
  local \
    _tests_switch=() \
    _tests_switch_status
  _tests_switch=(
    "OFF"
    # "ON"
  )
  for _tests_switch_status \
    in "${_tests_switch[@]}"; do
    _compile \
      "${_tests_switch_status}"
  done
}

check()
{
  _compile \
    "ON"
  "${srcdir}/${_pkg}_${pkgver}/build/test/soltest" \
    -p \
      true -- \
    --testpath \
      "${srcdir}/${_pkg}_${pkgver}/test/"
  _compile \
    "OFF"
}

package() {
  local \
    _binaries=() \
    _bin
  _binaries=(
    "solc"
    "yul-phaser"
  )
  # Assure that the directories exist.
  mkdir \
    -p \
      "${pkgdir}/usr/share/doc/${pkgname}/"
  # Install the software.
  DESTDIR="${pkgdir}/" \
  cmake \
    --install \
      "${srcdir}/${_pkg}_${pkgver}/build/"
  # Rename the binaries because
  # CMAKE_EXECUTABLE_SUFFIX doesn't work
  for _bin in "${_binaries[@]}"; do
    mv \
      "${pkgdir}/usr/bin/${_bin}" \
      "${pkgdir}/usr/bin/${_bin}${pkgver}" || \
      true
  done
  # Install the documentation.
  install \
    -Dm644 \
    "${srcdir}/${_pkg}_${pkgver}/README.md" \
    "${pkgdir}/usr/share/doc/${pkgname}/"
  cp \
    -r \
    "${srcdir}/${_pkg}_${pkgver}/docs/"* \
    "${pkgdir}/usr/share/doc/${pkgname}/"
  find \
    "${pkgdir}/usr/share/doc/${pkgname}/" \
    -type \
      d \
    -exec \
      chmod \
        755 \
	{} +
  find \
    "${pkgdir}/usr/share/doc/${pkgname}/" \
    -type \
      f \
    -exec \
      chmod \
      644 \
      {} +
}
