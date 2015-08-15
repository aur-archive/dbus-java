# Maintainer: Atilla ÖNTAŞ <tarakbumba at gmail dot com>
# Contributor: [Sinisa Dukanovic](mailto:sdukan at gmail dot com)

pkgname=dbus-java
pkgver=2.7
pkgrel=4
pkgdesc="Java bindings for D-Bus"
arch=('i686' 'x86_64')
url="http://www.freedesktop.org/software/dbus"
license=('LGPL2' 'AFL')
depends=('dbus>=1.0' 'java-runtime' 'libmatthew-java>=0.8')
makedepends=('java-environment' 'apache-ant' 'gettext')
source=("http://dbus.freedesktop.org/releases/dbus-java/${pkgname}-${pkgver}.tar.gz"
        "build.xml")
sha256sums=('be404ea36284d938646192d0ee42e146853064217d4e3aaf89b56bd351ebca33'
            '6e9a62b9b9f197ea51a2c525028cdc38e7f6c39ed774395ef8f106ccc43845bf')

_JARPATH=/usr/share/java/${pkgname}
_JAVAUNIXJARPATH=/usr/share/java/libmatthew-java
_JAVAUNIXLIBPATH=/usr/lib
_DEBUG=disable
_JAVA=java

build() {

	cd $srcdir
	
	mv build.xml ${pkgname}-${pkgver}
	
	cd ${pkgname}-${pkgver}
	
	mkdir -p src build
	mv org src
	
	# make messages
	echo "#java-format" > translations/en_GB.po
	find src/org/freedesktop -maxdepth 2 -name "*.java" -exec sed -n '/_(/s/.*_("\([^"]*\)").*/\1/p' {} \; > en_GB.tmp
	find src/org/freedesktop/dbus/exceptions -name "*.java" -exec sed -n '/_(/s/.*_("\([^"]*\)").*/\1/p' {} \; >> en_GB.tmp
	find src/org/freedesktop/dbus/types -name "*.java" -exec sed -n '/_(/s/.*_("\([^"]*\)").*/\1/p' {} \; >> en_GB.tmp
	find src/org/freedesktop/dbus/bin -name "*.java" -exec sed -n '/_(/s/.*_("\([^"]*\)").*/\1/p' {} \; >> en_GB.tmp
	find src/org/freedesktop/dbus/viewer -name "*.java" -exec sed -n '/_(/s/.*_("\([^"]*\)").*/\1/p' {} \; >> en_GB.tmp
	sort -u en_GB.tmp | sed 's/\(.*\)/msgid "\1"\nmsgstr "\1"/' >> translations/en_GB.po
	cd translations
	for i in *.po; do
		echo ${i%.po}
		msgfmt --java2 -r dbusjava_localized -d ../build -l ${i%.po} ${i}
	done
	cd ..
	msgfmt --java2 -r dbusjava_localized -d build translations/en_GB.po

	ant \
		-Dpkgver=${pkgver} \
		-Dpkg.libmatthew-java.path=${_JAVAUNIXJARPATH} \
		-Dpkg.libmatthew-lib.path=${_JAVAUNIXLIBPATH} \
		-Dpkg.debug=${_DEBUG} \
		-Dpkg.jarpath=${_JARPATH} \
		jars
	rm dist/*-test-${pkgver}.jar
}

package() {
  install -m755 -d ${pkgdir}/usr/share/java/${pkgname} ${pkgdir}/usr/bin

  cd ${srcdir}/${pkgname}-${pkgver}

  for i in dist/*.jar; do
    	install -m 644 ${i} ${pkgdir}/usr/share/java/${pkgname}
		_tmpfilename=${i##*\/}
		ln -sf ${_tmpfilename} ${pkgdir}/usr/share/java/${pkgname}/${_tmpfilename/-[0-9]\.[0-9]/}
    done
    for i in *.sh; do
		sed "s,\%JARPATH\%,${_JARPATH},;s,\%JAVAUNIXJARPATH\%,${_JAVAUNIXJARPATH},;s,\%JAVAUNIXLIBPATH\%,${_JAVAUNIXLIBPATH},;s,\%VERSION\%,${pkgver},;s,\%DEBUG\%,${_DEBUG},;s,\%JAVA\%,${_JAVA}," <${i} >${pkgdir}/usr/bin/${i%.sh}
		chmod 755 ${pkgdir}/usr/bin/${i%.sh}
    done
}
