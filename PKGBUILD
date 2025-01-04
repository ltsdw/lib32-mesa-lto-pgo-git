##############################################################################

_mesa_commit="#commit=98cdb934"

##############################################################################

_pgo="off" # possible values are ("generate", "use", "off")
_use_clang="" # leave empty to default to gcc
_host_machine_os="linux" # os that's currently running (probably linux for Arch Linux)
_cpu_architecture="x86_64" # cpu architeture ("x86", "x86_64", "arch64", etc)
_endianess="little" # possible values are ("little", "big")
_cpu_microarchitecture="" # processor architeture, ("bulldozer", "znver1", "sandybridge", etc) leave empty if unknown
_avx="" # which avx instrunctions to be used, leave empty if unknown

##############################################################################

# adapt the naming depeding on the compiled type
if [ "${_pgo}" != "off" ]; then
    pkgdesc="an open-source implementation of the OpenGL specification, compiled with LTO and PGO. Git version"
else
    pkgdesc="an open-source implementation of the OpenGL specification, compiled with LTO. Git version"
fi

# add clang as a dependency if needed
if [ ! -z "${_use_clang}" ]; then
    makedepends+=('clang' 'lib32-llvm' 'lib32-llvm-libs' 'lld')
fi

##############################################################################

# sourcing scripts and envars
source "${startdir}/scripts/aux-build"
source "${startdir}/scripts/aux-package"
source "${startdir}/scripts/aux-cleaning"
source "${startdir}/scripts/utils/directories"

###############################################################################

pkgname=lib32-mesa-lto-pgo-git
pkgver=24.1.0_devel.f3fe1f2f18d_off
pkgrel=1
arch=('x86_64')

makedepends+=(
    'python-mako' 'lib32-libxml2' 'xorgproto'
    'lib32-clang' 'lib32-gcc-libs' 'lib32-libvdpau' 'lib32-libelf' 'git' 'lib32-libglvnd'
    'wayland-protocols' 'lib32-wayland' 'meson' 'lib32-libva' 'lib32-libxrandr'
    'python-packaging' 'python-pyaml' 'lib32-spirv-tools' 'lib32-spirv-llvm-translator'
)

depends=(
    'mesa-lto-pgo-git' 'lib32-gcc-libs' 'lib32-libdrm' 'lib32-wayland' 'lib32-libxxf86vm' 'lib32-libxdamage' 'lib32-libxshmfence'
    'lib32-libx11' 'lib32-libelf' 'lib32-libunwind' 'lib32-lm_sensors' 'glslang' 'lib32-vulkan-icd-loader' 'lib32-zstd'
)

optdepends=('opengl-man-pages: for the OpenGL API man pages')

provides=(
    'lib32-vulkan-intel' 'lib32-vulkan-radeon' 'lib32-libva-mesa-driver' 'lib32-mesa-vdpau'
    'lib32-opengl-driver' 'lib32-vulkan-driver'
)

conflicts=('lib32-mesa' 'lib32-vulkan-intel' 'lib32-vulkan-radeon' 'lib32-libva-mesa-driver' 'lib32-mesa-vdpau' 'lib32-mesa-git')

url="https://www.mesa3d.org"

license=('custom')

source=(
    "mesa::git+https://gitlab.freedesktop.org/mesa/mesa.git${_mesa_commit}"
    "LICENSE"
)

md5sums=(
    'SKIP'
    '5c65a0fe315dd347e09b1f2826a1df5a'
)

sha512sums=(
    'SKIP'
    '25da77914dded10c1f432ebcbf29941124138824ceecaf1367b3deedafaecabc082d463abcfa3d15abff59f177491472b505bcb5ba0c4a51bb6b93b4721a23c2'
)

options=(!debug)

##############################################################################

pkgver()
{
    cd mesa
    read -r _ver <VERSION
    echo ${_ver/-/_}.$(git rev-parse --short HEAD)_${_pgo}
}

prepare()
{
    # remove old _build directory if it exists
    _remove_build_dir

    # remove generated files from last build with PGO="generate" option
    _remove_pgo_build_dir

    # write native file
    _write_native_file

    # set enough fs
    _set_number_of_fds
}

build()
{
    if [ "${_pgo}" = "generate" ]; then
        _generate_pgo_build
    elif [ "${_pgo}" = "use" ]; then
        _use_pgo_build
    else
        _no_pgo_build
    fi
}

package()
{
    # ninja install command, changed depending on type build
    _ninja_install

    if [ "${_pgo}" = "generate" ]; then
        # remove all unneeded files and directories from last pgo generate compile
        _remove_unneeded_pgo_build
    fi

    _remove_unneeded_files

    # indirect rendering
    ln -s "/usr/lib32/libGLX_mesa.so.0" "${pkgdir}/usr/lib32/libGLX_indirect.so.0"

    install -m644 -Dt  "${pkgdir}/usr/share/licenses/${pkgbase}/" -m644 "${srcdir}/LICENSE"
}
###############################################################################
