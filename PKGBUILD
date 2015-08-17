pkgname=nvidia-utils-pselect
pkgver=190.53
pkgrel=1
pkgdesc="NVIDIA drivers utilities and libraries with pselect support."
arch=('i686' 'x86_64')
[ "$CARCH" = "i686"   ] && ARCH=x86
[ "$CARCH" = "x86_64" ] && ARCH=x86_64
url="http://www.nvidia.com/"
depends=('xorg-server' 'pselect-git')
optdepends=('gtk2: nvidia-settings' 'pkgconfig: nvidia-xconfig')
conflicts=('nvidia-utils')
provides=("nvidia-utils=$pkgver")
license=('custom')
install=nvidia.install
source=("http://download.nvidia.com/XFree86/Linux-${ARCH}/${pkgver}/NVIDIA-Linux-${ARCH}-${pkgver}-pkg0.run"
        'nvidia-libgl.pselect.in')
options=(force)
md5sums=('2e80419f6f9ac16beecd839874d0c5ab'
         '2dca4fd61aec5b7a0ea7a22ebae3aa5c')
[ "$CARCH" = "x86_64" ] && md5sums=('3d3e956366f9df0e4c64d2e0299d1029' '2dca4fd61aec5b7a0ea7a22ebae3aa5c')

build() {
	cd $srcdir
	
	# Allow gl lib link switching with pselect
	install -m755 -d ${pkgdir}/usr/share/pselect/modules || return 1
	sed s:@version@:${pkgver}: nvidia-libgl.pselect.in >nvidia-libgl.pselect
	install -m644 nvidia-libgl.pselect ${pkgdir}/usr/share/pselect/modules/ || return 1
	
	
	sh NVIDIA-Linux-${ARCH}-${pkgver}-pkg0.run --extract-only
	cd NVIDIA-Linux-${ARCH}-${pkgver}-pkg0/usr/

	mkdir -p $pkgdir/usr/{lib,bin,share/applications,share/pixmaps,share/man/man1}
	mkdir -p $pkgdir/usr/lib/xorg/modules/{extensions,drivers}
	mkdir -p $pkgdir/usr/lib/vdpau
	mkdir -p $pkgdir/usr/share/licenses/nvidia
	mkdir -p $pkgdir/usr/include/cuda

	install -m644 include/cuda/cuda*.h $pkgdir/usr/include/cuda/

	install lib/{libGLcore,libGL,libnvidia-cfg,libcuda,tls/libnvidia-tls}.so.${pkgver} $pkgdir/usr/lib/ || return 1
	install -m755 lib/vdpau/libvdpau_nvidia.so.${pkgver} "${pkgdir}/usr/lib/vdpau/" || return 1
	ln -sf libvdpau_nvidia.so.${pkgver} "${pkgdir}/usr/lib/vdpau/libvdpau_nvidia.so.1"

	install -m644 share/man/man1/* $pkgdir/usr/share/man/man1/ || return 1
	rm $pkgdir/usr/share/man/man1/nvidia-installer.1.gz || return 1
	install X11R6/lib/libXv* $pkgdir/usr/lib/ || return 1
	install -m644 share/applications/nvidia-settings.desktop $pkgdir/usr/share/applications/ || return 1
	# fix nvidia .desktop file
	sed -e 's:__UTILS_PATH__:/usr/bin:' -e 's:__PIXMAP_PATH__:/usr/share/pixmaps:' -i $pkgdir/usr/share/applications/nvidia-settings.desktop
	install -m644 share/pixmaps/nvidia-settings.png $pkgdir/usr/share/pixmaps/ || return 1
	install X11R6/lib/modules/drivers/nvidia_drv.so $pkgdir/usr/lib/xorg/modules/drivers || return 1
	install X11R6/lib/modules/extensions/libglx.so.$pkgver $pkgdir/usr/lib/xorg/modules/extensions || return 1
	install -m755 bin/nvidia-{settings,xconfig,bug-report.sh} $pkgdir/usr/bin/ || return 1
	cd $pkgdir/usr/lib/
	ln -s libGLcore.so.$pkgver libGLcore.so.1 || return 1
	ln -s libnvidia-cfg.so.$pkgver libnvidia-cfg.so.1 || return 1
	ln -s libnvidia-tls.so.$pkgver libnvidia-tls.so.1 || return 1
	ln -s libcuda.so.$pkgver libcuda.so.1 || return 1
	ln -s libcuda.so.$pkgver libcuda.so || return 1
	ln -s libXvMCNVIDIA.so.$pkgver libXvMCNVIDIA_dynamic.so.1 || return 1

	install -m644 $srcdir/NVIDIA-Linux-${ARCH}-${pkgver}-pkg0/LICENSE $pkgdir/usr/share/licenses/nvidia/ || return 1
	ln -s nvidia $startdir/pkg/usr/share/licenses/nvidia-utils || return 1

	install -D -m644 $srcdir/NVIDIA-Linux-${ARCH}-${pkgver}-pkg0/usr/share/doc/README.txt $pkgdir/usr/share/doc/nvidia/README || return 1

	find $pkgdir/usr -type d -exec chmod 755 {} \;
	chmod 644 $pkgdir/usr/lib/libXvMCNVIDIA.a
}
