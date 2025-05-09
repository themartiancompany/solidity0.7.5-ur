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
_static="true"
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
  )
  if [[ "" == "${_static}" ]]; then
    makedepends+=(
      "boost-static"
    )
  fi
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
  "0004-${pkgname}-limits-include-SourceLocation.patch"
  "0005-${pkgname}-fstream-include-SourceUpgrade.patch"
)
source=(
  "${_pkg}-v${pkgver}.tar.gz::${url}/releases/download/v${pkgver}/${_pkg}_${pkgver}.tar.gz"
  "${_patches[@]}"
)
sha512sums=(
  'ec095a5d8dc00187ef6df03fd14f8ce5da3f437ca55311617211ded7bd6461a680e7a42bee0819fe639cd1ef3554f9b363bcdc9cf04dddf6e136e1543a5e5f3a'
  '2ce1ba9454aa70b894fb46997d3c72412f306685a21349421ed3832a26bab9eef000ce926c3bb3095e8634ac740c2b828fd376d32e22ed647b996bf9b7ed5a03'
  '4af0991bc6505d7e1976a478fad893da6cee097791f5c0d38bbb9cd3f6e1a4a6d44b89a75be2671723217333cb2b83442f1cf8cf018bd28667079b765e3c2d7d'
  'dd90bae065e09ba725797ac508f1a688b424ed85f491243e0998389e0158faadb863aeb94220869c8568de51f8f74a8f6515fd699d4d71dc4129ed47112a3f9c'
  '56c6034e40698afb399b19df8d7eabd92aba4f8894faa7256a81aea6f4eb1ce16379f65c9a4aec8cb47db2fb5f7ecd32b0bbcf517869d7c1ab6b9b1c54bd3b2f'
  'e2216d8845d29b5c29e861a479b0685c5ddbbf5b088299a215dfdfde5297f08ab340a6e70cee3c5b61f34711ff5146bbf0036a87d374c4ee0b561d53cadd2e3f'
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
  sed \
    -e \
      "/-Wsign-conversion/d" \
    -i \
    "cmake/EthCompilerSettings.cmake"
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
    _boost_static_libs \
    _cmake_opts=() \
    _cxxflags=()
  _cxxflags=(
    "${CXXFLAGS}"
    -Wno-unknown-warning-option
    -Wno-deprecated-declarations
    -Wno-dangling-reference
    -Wno-sign-conversion
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
  if [[ "${_static}" == "true" ]]; then
    _boost_use_static_libs="ON"
  elif [[ "${_static}" == "false" ]]; then
    _boost_use_static_libs="OFF"
  fi
  _cmake_opts=(
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
    -D Boost_USE_STATIC_LIBS="${_boost_use_static_libs}"
    -D USE_SYSTEM_LIBRARIES="OFF"
    -DCMAKE_CXX_FLAGS="${_cxxflags[*]}"
    -S "${srcdir}/${_pkg}_${pkgver}/"
    -Wno-dev
  )
  export \
    CXXFLAGS="${_cxxflags[*]}"
  CXXFLAGS="${_cxxflags[*]}" \
  VERBOSE=1 \
  cmake \
    -B \
      "${srcdir}/${_pkg}_${pkgver}/build/" \
    "${_cmake_opts[@]}"
  CXXFLAGS="${_cxxflags[*]}" \
  VERBOSE=1 \
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
