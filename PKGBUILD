##############################################################################

_pkgbuild_dir=$PWD

_mesa_commit="#commit=ec4990ef"

PGO="off" # possible values are ("generate", "use", "off")
_march="" # processor architeture, leave empty if unkown

# adapt the naming depeding on the compiled type
if [ "$PGO" != "off" ]; then
    pkgdesc="an open-source implementation of the OpenGL specification, compiled with LTO and PGO. Git version"
else
    pkgdesc="an open-source implementation of the OpenGL specification, compiled with LTO. Git version"
fi

##############################################################################

# sourcing scripts and envars
source "$_pkgbuild_dir/scripts/utils/envar"
source "$_pkgbuild_dir/scripts/aux-build"
source "$_pkgbuild_dir/scripts/aux-package"
source "$_pkgbuild_dir/scripts/aux-cleaning"
source "$_pkgbuild_dir/scripts/utils/directories"

###############################################################################

pkgname=lib32-mesa-lto-pgo-git

pkgver=24.1.0_devel.ec4990ef419_off

pkgrel=1

arch=('x86_64')

makedepends=('python-mako' 'lib32-libxml2' 'lib32-libx11' 'xorgproto'
             'lib32-gcc-libs' 'lib32-libvdpau' 'lib32-libelf' 'git' 'lib32-libglvnd'
             'wayland-protocols' 'lib32-wayland' 'meson' 'lib32-libva' 'lib32-libxrandr'
            )

depends=('mesa-lto-pgo-git' 'lib32-gcc-libs' 'lib32-libdrm' 'lib32-wayland' 'lib32-libxxf86vm' 'lib32-libxdamage' 'lib32-libxshmfence'
         'lib32-libelf' 'lib32-libunwind' 'lib32-lm_sensors' 'glslang' 'lib32-vulkan-icd-loader' 'lib32-zstd'
         )

optdepends=('opengl-man-pages: for the OpenGL API man pages')

provides=('lib32-vulkan-intel' 'lib32-vulkan-radeon' 'lib32-libva-mesa-driver' 'lib32-mesa-vdpau'
          'lib32-opengl-driver' 'lib32-vulkan-driver'
         )

conflicts=('lib32-mesa' 'lib32-vulkan-intel' 'lib32-vulkan-radeon' 'lib32-libva-mesa-driver' 'lib32-mesa-vdpau' 'lib32-mesa-git')

url="https://www.mesa3d.org"

license=('custom')

source=("mesa::git+https://gitlab.freedesktop.org/mesa/mesa.git$_mesa_commit"
        "LICENSE"
        "llvm32.native"
       )

md5sums=('SKIP'
         '5c65a0fe315dd347e09b1f2826a1df5a'
         '6b4a19068a323d7f90a3d3cd315ed1f9'
        )

sha512sums=('SKIP'
            '25da77914dded10c1f432ebcbf29941124138824ceecaf1367b3deedafaecabc082d463abcfa3d15abff59f177491472b505bcb5ba0c4a51bb6b93b4721a23c2'
            'c7dbb390ebde291c517a854fcbe5166c24e95206f768cc9458ca896b2253aabd6df12a7becf831998721b2d622d0c02afdd8d519e77dea8e1d6807b35f0166fe'
           )

##############################################################################

pkgver()
{
    cd mesa
    read -r _ver <VERSION
    echo ${_ver/-/_}.$(git rev-parse --short HEAD)_$PGO
}

prepare()
{
    # remove old _build directory if it exists
    _remove_build_dir

    # remove generated files from last build with PGO="generate" option
    _remove_pgo_build_dir
}

build()
{
    if [ "$PGO" = "generate" ]; then
        _generate_pgo_build
    elif [ "$PGO" = "use" ]; then
        _use_pgo_build
    else
        _no_pgo_build
    fi
}

package()
{
    # ninja install command, changed depending on type build
    _ninja_install

    if [ "$PGO" = "generate" ]; then
        # remove all unneeded files and directories from last pgo generate compile
        _remove_unneeded_pgo_build
    fi

    _remove_unneeded_files

    # indirect rendering
    ln -s "/usr/lib32/libGLX_mesa.so.0" "${pkgdir}/usr/lib32/libGLX_indirect.so.0"

    install -m644 -Dt  "$pkgdir/usr/share/licenses/$pkgbase/" -m644 "$srcdir/LICENSE"
}
###############################################################################
