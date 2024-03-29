#                                               -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.

AC_INIT([lxc], [0.8.0-rc2])

AC_CONFIG_SRCDIR([configure.ac])
AC_CONFIG_AUX_DIR([config])
AM_CONFIG_HEADER([src/config.h])
AM_INIT_AUTOMAKE([-Wno-portability])
AC_CANONICAL_HOST
AM_PROG_CC_C_O
AC_GNU_SOURCE
AC_CHECK_PROG(SETCAP, setcap, yes, no, $PATH$PATH_SEPARATOR/sbin)

AC_ARG_ENABLE([rpath],
	[AC_HELP_STRING([--disable-rpath], [do not set rpath in executables])],
	[], [enable_rpath=yes])

AM_CONDITIONAL([ENABLE_RPATH], [test "x$enable_rpath" = "xyes"])

AC_ARG_ENABLE([apparmor],
	[AC_HELP_STRING([--enable-apparmor], [enable apparmor])],
	[], [enable_apparmor=yes])
AM_CONDITIONAL([ENABLE_APPARMOR], [test "x$enable_apparmor" = "xyes"])

AC_ARG_ENABLE([doc],
	[AC_HELP_STRING([--enable-doc], [make mans (require docbook2man installed) [default=auto]])],
	[], [enable_doc=auto])

if test "x$enable_doc" = "xyes" -o "x$enable_doc" = "xauto"; then
	AC_CHECK_PROG(have_docbook, [docbook2man], [yes], [no])

	test "x$have_docbook" = "xno" -a "x$enable_doc" = "xyes" && \
		AC_MSG_ERROR([docbook2man required by man request, but not found])
fi

AM_COND_IF([ENABLE_APPARMOR],
    [AC_CHECK_HEADER([sys/apparmor.h],[],[AC_MSG_ERROR([You must install the AppArmor development package in order to compile lxc])])
     AC_CHECK_LIB([apparmor], [aa_change_profile],[],[AC_MSG_ERROR([You must install the AppArmor development package in order to compile lxc])])
     AC_SUBST([APPARMOR_LIBS], [-lapparmor])])

AM_CONDITIONAL([ENABLE_DOCBOOK], [test "x$have_docbook" = "xyes"])

AC_ARG_ENABLE([examples],
	[AC_HELP_STRING([--disable-examples], [do not install configuration examples])],
	[], [enable_examples=yes])

AM_CONDITIONAL([ENABLE_EXAMPLES], [test "x$enable_examples" = "xyes"])

AS_AC_EXPAND(PREFIX, $prefix)
AS_AC_EXPAND(LIBDIR, $libdir)
AS_AC_EXPAND(BINDIR, $bindir)
AS_AC_EXPAND(LIBEXECDIR, $libexecdir)
AS_AC_EXPAND(INCLUDEDIR, $includedir)
AS_AC_EXPAND(SYSCONFDIR, $sysconfdir)
AS_AC_EXPAND(DATADIR, $datadir)
AS_AC_EXPAND(LOCALSTATEDIR, $localstatedir)
AS_AC_EXPAND(DOCDIR, $docdir)

AC_ARG_WITH([config-path],
	[AC_HELP_STRING(
		[--with-config-path=dir],
		[lxc configuration repository path]
	)], [], [with_config_path=['${localstatedir}/lib/lxc']])

AC_ARG_WITH([rootfs-path],
	[AC_HELP_STRING(
		[--with-rootfs-path=dir],
		[lxc rootfs mount point]
	)], [], [with_rootfs_path=['${libdir}/lxc/rootfs']])

AS_AC_EXPAND(LXC_GENERATE_DATE, "$(date)")

AC_SUBST(LXCPATH, "${with_config_path}")
AC_SUBST(LXCROOTFSMOUNT, "${with_rootfs_path}")
AC_SUBST(LXCINITDIR, ['${libexecdir}'])
AC_SUBST(LXCTEMPLATEDIR, ['/usr/share/lxc/templates'])

AC_CHECK_HEADERS([linux/unistd.h linux/netlink.h linux/genetlink.h],
				  [],
				  AC_MSG_ERROR([Please install the Linux kernel headers.]),
				  [#include <sys/socket.h>
		 ])

AC_CHECK_HEADERS([sys/capability.h], [], AC_MSG_ERROR([Please install the libcap development files.]),
[#include <sys/types.h>
#include <sys/capability.h>])
AC_CHECK_LIB(cap,cap_set_proc,caplib=yes,caplib=no)
AC_MSG_CHECKING([linux capabilities])
if test "x$caplib" = "xyes" ; then
   CAP_LIBS="-lcap"
   AC_MSG_RESULT([$CAP_LIBS])
else
   AC_MSG_ERROR([not found])
fi
AC_SUBST([CAP_LIBS])

# Some systems lack PR_CAPBSET_DROP definition => HAVE_DECL_PR_CAPBSET_DROP
AC_CHECK_DECLS([PR_CAPBSET_DROP], [], [], [#include <sys/prctl.h>])

AC_CHECK_HEADERS([sys/signalfd.h])

AC_PROG_GCC_TRADITIONAL

if test "x$GCC" = "xyes"; then
  CFLAGS="$CFLAGS -Wall"
fi

AC_CONFIG_FILES([
	Makefile
	lxc.pc
	lxc.spec
	config/Makefile

	doc/Makefile
	doc/lxc-create.sgml
	doc/lxc-destroy.sgml
	doc/lxc-execute.sgml
	doc/lxc-start.sgml
	doc/lxc-checkpoint.sgml
	doc/lxc-restart.sgml
	doc/lxc-stop.sgml
	doc/lxc-console.sgml
	doc/lxc-freeze.sgml
	doc/lxc-unfreeze.sgml
	doc/lxc-monitor.sgml
	doc/lxc-wait.sgml
	doc/lxc-ls.sgml
	doc/lxc-ps.sgml
	doc/lxc-cgroup.sgml
	doc/lxc-kill.sgml
	doc/lxc-attach.sgml
	doc/lxc.conf.sgml
	doc/lxc.sgml
	doc/common_options.sgml
	doc/see_also.sgml

	doc/rootfs/Makefile

	doc/examples/Makefile
	doc/examples/lxc-macvlan.conf
	doc/examples/lxc-vlan.conf
	doc/examples/lxc-no-netns.conf
	doc/examples/lxc-empty-netns.conf
	doc/examples/lxc-phys.conf
	doc/examples/lxc-veth.conf
	doc/examples/lxc-complex.conf

	templates/Makefile
	templates/lxc-lenny
	templates/lxc-debian
	templates/lxc-ubuntu
	templates/lxc-ubuntu-cloud
	templates/lxc-opensuse
	templates/lxc-busybox
	templates/lxc-fedora
	templates/lxc-altlinux
	templates/lxc-sshd
	templates/lxc-archlinux
	templates/lxc-gentoo

	src/Makefile
	src/lxc/Makefile
	src/lxc/lxc-ps
	src/lxc/lxc-ls
	src/lxc/lxc-netstat
	src/lxc/lxc-checkconfig
	src/lxc/lxc-setcap
	src/lxc/lxc-setuid
	src/lxc/lxc-version
	src/lxc/lxc-create
	src/lxc/lxc-clone
	src/lxc/lxc-shutdown
	src/lxc/lxc-destroy

])
AC_CONFIG_COMMANDS([default],[[]],[[]])
AC_OUTPUT

if test "x$SETCAP" = "xno"; then
   AC_MSG_NOTICE([

Warning:
--------

The setcap binary was not found. This means the tools to set the
privilege for the lxc commands are not available, that's ok, but you
will need to run these commands as root or install libcap-2.

])

else

   AC_MSG_NOTICE([

Advice:
-------

If you wish to have a non root user to use the lxc tools,
you can add the needed capabilities to the tools by invoking
the 'lxc-setcap' script. To remove the capabilities, use
'lxc-setcap -d'.
])

fi
