dnl Process this file with autoconf to produce a configure script.

AC_PREREQ(2.61)

AC_INIT(file-roller, 2.23.4, [http://bugzilla.gnome.org/enter_bug.cgi?product=file-roller])
AM_INIT_AUTOMAKE(AC_PACKAGE_NAME, AC_PACKAGE_VERSION)

AC_CONFIG_SRCDIR([config.h.in])
AC_CONFIG_HEADER([config.h])

GNOME_COMMON_INIT

AC_PROG_LIBTOOL

AC_PATH_PROG(GLIB_GENMARSHAL, glib-genmarshal)

dnl ==========================================================================
dnl
dnl If you add a version number here, you *must* add an AC_SUBST line for
dnl it too, or it will never make it into the spec file!
dnl
dnl ==========================================================================

GLIB_REQUIRED=2.16.0
GIO_REQUIRED=2.16.0
GTK_2_12_REQUIRED=2.12.0
GTK_2_13_REQUIRED=2.13.0
LIBGNOME_REQUIRED=2.6.0
LIBGNOMEUI_REQUIRED=2.6.0
LIBGLADE_REQUIRED=2.4.0
NAUTILUS_REQUIRED=2.22.2

AC_SUBST(GLIB_REQUIRED)
AC_SUBST(GIO_REQUIRED)
AC_SUBST(LIBGNOME_REQUIRED)
AC_SUBST(LIBGNOMEUI_REQUIRED)
AC_SUBST(LIBGLADE_REQUIRED)
AC_SUBST(NAUTILUS_REQUIRED)

dnl ===========================================================================

if pkg-config --atleast-version=$GTK_2_13_REQUIRED gtk+-2.0; then
	GTK_REQUIRED=$GTK_2_13_REQUIRED
	AC_DEFINE(GTK_2_13, "1", [Use the Gtk+ 2.13 series])
else
	GTK_REQUIRED=$GTK_2_12_REQUIRED
fi
AC_SUBST(GTK_REQUIRED)

PKG_CHECK_MODULES(FR,					\
	glib-2.0 >= $GLIB_REQUIRED			\
	gthread-2.0					\
	gio-unix-2.0 >= $GIO_REQUIRED			\
	gtk+-2.0 >= $GTK_REQUIRED			\
	libgnome-2.0 >= $LIBGNOME_REQUIRED		\
	libgnomeui-2.0 >= $LIBGNOMEUI_REQUIRED		\
	libglade-2.0 >= $LIBGLADE_REQUIRED)
AC_SUBST(FR_CFLAGS)
AC_SUBST(FR_LIBS)


NAUTILUS_CFLAGS=""
NAUTILUS_LIBS=""
build_nautilus_actions=no
AC_ARG_ENABLE(nautilus_actions, AC_HELP_STRING([--disable-nautilus-actions],[don't build the nautilus context menu actions]))

if test x"$enable_nautilus_actions" != xno; then
	if pkg-config --atleast-version=$NAUTILUS_REQUIRED libnautilus-extension; then
		NAUTILUS_CFLAGS=`pkg-config --cflags libnautilus-extension glib-2.0 gio-2.0`
		NAUTILUS_LIBS=`pkg-config --libs libnautilus-extension glib-2.0 gio-2.0`
		build_nautilus_actions=yes
	fi
fi
AM_CONDITIONAL(ENABLE_NAUTILUS_ACTIONS, test "x$build_nautilus_actions" = xyes)
AC_SUBST(NAUTILUS_CFLAGS)
AC_SUBST(NAUTILUS_LIBS)


dnl Checks for mkdtemp function

mkdtemp_missing=false
AC_CHECK_FUNC(mkdtemp,
    [AC_DEFINE([HAVE_MKDTEMP], 1, [Have GlibC function to make temp dirs])],
    mkdtemp_missing=true)
AM_CONDITIONAL(MKDTEMP_MISSING, test x$mkdtemp_missing = xtrue)

dnl ******************************

GETTEXT_PACKAGE=file-roller
AC_SUBST(GETTEXT_PACKAGE)
AC_DEFINE_UNQUOTED(GETTEXT_PACKAGE, "$GETTEXT_PACKAGE", [GetText Package])

AM_GLIB_GNU_GETTEXT
IT_PROG_INTLTOOL([0.35.0])

if test "x${prefix}" = "xNONE"; then
  AC_DEFINE_UNQUOTED(LOCALEDIR, "${ac_default_prefix}/${DATADIRNAME}/locale", [Locale dir])
else
  AC_DEFINE_UNQUOTED(LOCALEDIR, "${prefix}/${DATADIRNAME}/locale", [Locale dir])
fi

dnl ******************************

PKG_PROG_PKG_CONFIG
GNOME_DOC_INIT

dnl ******************************

AC_ARG_ENABLE(deprecations,AC_HELP_STRING([--enable-deprecations],[warn about deprecated usages]))
AM_CONDITIONAL(ENABLE_DEPRECATIONS, test "x$enable_deprecations" = xyes)

dnl ******************************

AC_PATH_PROG(GCONFTOOL, gconftool-2, no)
if test x"$GCONFTOOL" = xno; then
	AC_MSG_ERROR([gconftool-2 executable not found in your path - should be installed with GConf])
fi
AM_GCONF_SOURCE_2

dnl ******************************

NAUTILUS_EXTENSION_DIR=`$PKG_CONFIG --variable=extensiondir libnautilus-extension`
AC_SUBST(NAUTILUS_EXTENSION_DIR)

dnl ******************************

AC_CONFIG_FILES([Makefile
		 file-roller.spec
		 data/Makefile
		 data/file-roller.desktop.in
		 data/glade/Makefile
		 data/icons/Makefile
		 data/icons/16x16/Makefile
		 data/icons/16x16/actions/Makefile
		 data/icons/16x16/apps/Makefile
		 data/icons/22x22/Makefile
		 data/icons/22x22/apps/Makefile
		 data/icons/24x24/Makefile
		 data/icons/24x24/actions/Makefile
		 data/icons/24x24/apps/Makefile
		 data/icons/32x32/Makefile
		 data/icons/32x32/apps/Makefile
		 data/icons/scalable/Makefile
		 data/icons/scalable/apps/Makefile
		 src/Makefile
		 src/sh/Makefile
		 nautilus/Makefile
		 help/Makefile
		 po/Makefile.in])
AC_OUTPUT

echo "
Configuration:

	Source code location:   ${srcdir}
	Compiler:               ${CC}
	Internal mkdtemp:       ${mkdtemp_missing}
	Nautilus support:       ${build_nautilus_actions}
	Gtk+ series:            ${GTK_REQUIRED}
"