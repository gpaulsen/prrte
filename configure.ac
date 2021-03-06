# -*- shell-script -*-
#
# Copyright (c) 2004-2009 The Trustees of Indiana University and Indiana
#                         University Research and Technology
#                         Corporation.  All rights reserved.
# Copyright (c) 2004-2015 The University of Tennessee and The University
#                         of Tennessee Research Foundation.  All rights
#                         reserved.
# Copyright (c) 2004-2007 High Performance Computing Center Stuttgart,
#                         University of Stuttgart.  All rights reserved.
# Copyright (c) 2004-2005 The Regents of the University of California.
#                         All rights reserved.
# Copyright (c) 2006-2018 Cisco Systems, Inc.  All rights reserved
# Copyright (c) 2006-2008 Sun Microsystems, Inc.  All rights reserved.
# Copyright (c) 2006-2017 Los Alamos National Security, LLC.  All rights
#                         reserved.
# Copyright (c) 2009      Oak Ridge National Labs.  All rights reserved.
# Copyright (c) 2011-2013 NVIDIA Corporation.  All rights reserved.
# Copyright (c) 2012      Oracle and/or its affiliates.  All rights reserved.
# Copyright (c) 2013      Mellanox Technologies, Inc.
#                         All rights reserved.
# Copyright (c) 2013-2020 Intel, Inc.  All rights reserved.
# Copyright (c) 2014-2019 Research Organization for Information Science
#                         and Technology (RIST).  All rights reserved.
# Copyright (c) 2016-2017 IBM Corporation.  All rights reserved.
# Copyright (c) 2018      Amazon.com, Inc. or its affiliates.
#                         All Rights reserved.
# $COPYRIGHT$
#
# Additional copyrights may follow
#
# $HEADER$
#

############################################################################
# Initialization, version number, and other random setup/init stuff
############################################################################

# Load in everything found by autogen.pl
m4_include([config/autogen_found_items.m4])

# Load the version number code
m4_include([config/prrte_get_version.m4])
AC_LANG([C])

# Init autoconf
# We don't have the version number to put in here yet, and we can't
# call PRRTE_GET_VERSION (etc.) before AC_INIT.  So use the shell
# version.  project_name_* comes from config/project_list.m4, which
# was set during autogen.pl.

AC_INIT([project_name_long],
        [m4_normalize(esyscmd([config/prrte_get_version.sh VERSION --tarball]))],
        [https://github.com/openpmix/prrte/], [project_name_short])

AC_PREREQ(2.60)
AC_CONFIG_AUX_DIR(config)
AC_CONFIG_MACRO_DIR(config)
PRRTE_CAPTURE_CONFIGURE_CLI([PRRTE_CONFIGURE_CLI])

# Get our platform support file.  This has to be done very, very early
# because it twiddles random bits of autoconf
PRRTE_LOAD_PLATFORM

#
# Start it up
#

AC_CHECK_PROG([PERL],[perl],[perl],[no])
AS_IF([test "X$PERL" = "Xno"],
      [AC_MSG_ERROR(["PRRTE requires perl. Aborting"])])

PRRTE_CONFIGURE_SETUP

prrte_show_title "Configuring project_name_long"

#
# Setup some things that must be done before AM-INIT-AUTOMAKE
#

prrte_show_subtitle "Startup tests"

AC_CANONICAL_HOST
AC_CANONICAL_TARGET
AC_DEFINE_UNQUOTED(PRRTE_ARCH, "$target", [PRTE architecture string])

AS_IF([test "$host" != "$target"],
      [AC_MSG_WARN([Cross-compile detected])
       AC_MSG_WARN([Cross-compiling is only partially supported])
       AC_MSG_WARN([Proceed at your own risk!])])

# AC_USE_SYSTEM_EXTENSIONS alters CFLAGS (e.g., adds -g -O2)

PRRTE_VAR_SCOPE_PUSH([CFLAGS_save])
CFLAGS_save=$CFLAGS
AC_USE_SYSTEM_EXTENSIONS

# AC_USE_SYSTEM_EXTENSIONS will modify CFLAGS if nothing was in there
# beforehand.  We don't want that.  So if there was nothing in
# CFLAGS, put nothing back in there.

AS_IF([test -z "$CFLAGS_save"], [CFLAGS=])

PRRTE_VAR_SCOPE_POP

#
# Init automake
#

AM_INIT_AUTOMAKE([foreign dist-bzip2 subdir-objects no-define 1.12.2 tar-ustar])

# SILENT_RULES is new in AM 1.11, but we require 1.11 or higher via
# autogen.  Limited testing shows that calling SILENT_RULES directly
# works in more cases than adding "silent-rules" to INIT_AUTOMAKE
# (even though they're supposed to be identical).  Shrug.

AM_SILENT_RULES([yes])

# Make configure depend on the VERSION file, since it's used in AC_INIT
AC_SUBST([CONFIGURE_DEPENDENCIES], ['$(top_srcdir)/VERSION'])

prrte_show_subtitle "Checking versions"

# Get the version of PRTE that we are installing
PRRTE_SAVE_VERSION([PRRTE], [PMIx Reference Run-Time Environment],
                          [$srcdir/VERSION],
                          [src/include/version.h])

# Get shared library version numbers
. $srcdir/VERSION

AC_SUBST(libprrte_so_version)
AC_SUBST(libprrte_common_alps_so_version)

#
# Get the versions of the autotools that were used to bootstrap us
# (helpful for debugging reports)
#

AC_MSG_CHECKING([for bootstrap Autoconf version])
acversion=`grep "Generated by GNU Autoconf" $0 | head -n 1 | awk '{ print $6 }'`
AC_MSG_RESULT([$acversion])

AC_MSG_CHECKING([for bootstrap Automake version])
AC_MSG_RESULT([$am__api_version])

AC_MSG_CHECKING([for boostrap Libtool version])
ltversion=`grep VERSION= $srcdir/config/ltmain.sh | head -n 1 | cut -d= -f2`
AC_MSG_RESULT([$ltversion])

# List header files to generate

AC_CONFIG_HEADERS([src/include/prrte_config.h])

prrte_show_subtitle "Initialization, setup"

PRRTE_TOP_BUILDDIR="`pwd`"
AC_SUBST(PRRTE_TOP_BUILDDIR)
cd "$srcdir"
PRRTE_TOP_SRCDIR="`pwd`"
AC_SUBST(PRRTE_TOP_SRCDIR)
cd "$PRRTE_TOP_BUILDDIR"
AC_MSG_NOTICE([builddir: $PRRTE_TOP_BUILDDIR])
AC_MSG_NOTICE([srcdir: $PRRTE_TOP_SRCDIR])

if test "$PRRTE_TOP_BUILDDIR" != "$PRRTE_TOP_SRCDIR"; then
    AC_MSG_NOTICE([Detected VPATH build])
fi

# Setup the top of the src/include/prrte_config.h file

AH_TOP([/* -*- c -*-
 *
 * Copyright (c) 2004-2005 The Trustees of Indiana University.
 *                         All rights reserved.
 * Copyright (c) 2004-2005 The Trustees of the University of Tennessee.
 *                         All rights reserved.
 * Copyright (c) 2004-2005 High Performance Computing Center Stuttgart,
 *                         University of Stuttgart.  All rights reserved.
 * Copyright (c) 2004-2005 The Regents of the University of California.
 *                         All rights reserved.
 * Copyright (c) 2014      Intel, Inc. All rights reserved.
 * $COPYRIGHT$
 *
 * Additional copyrights may follow
 *
 * $HEADER$
 *
 * Function: - OS, CPU and compiler dependent configuration
 */

#ifndef PRRTE_CONFIG_H
#define PRRTE_CONFIG_H

#include "prrte_config_top.h"
])

AH_BOTTOM([
#include "prrte_config_bottom.h"
#endif /* PRRTE_CONFIG_H */
])

# Other basic setup stuff (shared with components)

PRRTE_BASIC_SETUP

PRRTE_TOP_SRCDIR="$PRRTE_TOP_SRCDIR"
PRRTE_TOP_BUILDDIR="$PRRTE_TOP_BUILDDIR"
AC_SUBST(PRRTE_TOP_SRCDIR)
AC_SUBST(PRRTE_TOP_BUILDDIR)

############################################################################
# Configuration options
############################################################################

PRRTE_CONFIGURE_OPTIONS
PRRTE_CHECK_OS_FLAVORS
PRRTE_CONFIGURE_OPTIONS

if test "$enable_binaries" = "no" && test "$enable_dist" = "yes"; then
    AC_MSG_WARN([--disable-binaries is incompatible with --enable dist])
    AC_MSG_ERROR([Cannot continue])
fi


############################################################################
# Libtool: part one
# (before C compiler setup)
############################################################################

#
# Part one of libtool magic.  Default to: enable shared, disable static.
#

AM_ENABLE_SHARED
AM_DISABLE_STATIC
PRRTE_SETUP_WRAPPER_INIT

##################################
# Check for known incompatibility
##################################

# Do *not* print a message that we're checking the OS because this
# test is *not* meant to be an all-inclusive "if it passes this test,
# then configure must succeed" test.  This test is *only* mean to
# screen out the versions of OS X where we know OMPI will cause kernel
# panics because of bad implementations of pty's.  See
# https://svn.open-mpi.org/trac/ompi/ticket/1637 for details.
#   OS X name         OS X Version    $host_os value
# OS X Tiger             10.4.x           darwin8.x
# OS X Leopard           10.5.x           darwin9.x
# OS X Snow Leopard      10.6.x           darwin10.x
# OS X Lion              10.7.x           darwin11.x
# We do not support OS X before version 10.5 (Leopard)
case $host_os in
# Corresponds to OS X 10.0 - 10.4 (additional [] quoting for m4)
darwin[[45678]]*)
    AC_MSG_WARN([PRTE does not support OS X prior to version 10.5 (Leopard)])
    AC_MSG_ERROR([Cannot continue])
esac

############################################################################
# Check for compilers and preprocessors
############################################################################

prrte_show_title "Compiler and preprocessor tests"

##################################
# C compiler characteristics
##################################

PRRTE_SETUP_CC

# Does the compiler support "ident"-like constructs?

PRRTE_CHECK_IDENT([CC], [CFLAGS], [c], [C])

#
# Check for some types
#

AC_CHECK_TYPES(int8_t)
AC_CHECK_TYPES(uint8_t)
AC_CHECK_TYPES(int16_t)
AC_CHECK_TYPES(uint16_t)
AC_CHECK_TYPES(int32_t)
AC_CHECK_TYPES(uint32_t)
AC_CHECK_TYPES(int64_t)
AC_CHECK_TYPES(uint64_t)
AC_CHECK_TYPES(int128_t)
AC_CHECK_TYPES(__int128)
AC_CHECK_TYPES(uint128_t)
AC_CHECK_TYPES(long long)
AC_CHECK_TYPES(__float128)
AC_CHECK_TYPES(long double)
# We only need these types if we're building the OMPI project, but
# PRRTE currently doesn't protect for their lack of presence well.
AC_CHECK_HEADERS(complex.h)
AC_CHECK_TYPES(float _Complex)
AC_CHECK_TYPES(double _Complex)
AC_CHECK_TYPES(long double _Complex)
AC_CHECK_TYPES(intptr_t)
AC_CHECK_TYPES(uintptr_t)
AC_CHECK_TYPES(mode_t)
AC_CHECK_TYPES(ssize_t)
AC_CHECK_TYPES(ptrdiff_t)

#
# Check for type sizes
#

AC_CHECK_SIZEOF(_Bool)
AC_CHECK_SIZEOF(char)
AC_CHECK_SIZEOF(short)
AC_CHECK_SIZEOF(int)
AC_CHECK_SIZEOF(long)
AC_CHECK_SIZEOF(long long)
AC_CHECK_SIZEOF(float)
AC_CHECK_SIZEOF(double)

if test "$ac_cv_type_long_double" = yes; then
    AC_CHECK_SIZEOF(long double)
fi

AC_CHECK_SIZEOF(long double)
if test "$ac_cv_type___float128" = yes; then
    AC_CHECK_SIZEOF(__float128)
fi

# We only need these types if we're building the OMPI project, but
# PRRTE currently doesn't protect for their lack of presence well.
AC_CHECK_SIZEOF(float _Complex)
AC_CHECK_SIZEOF(double _Complex)
AC_CHECK_SIZEOF(long double _Complex)

AC_CHECK_SIZEOF(void *)
AC_CHECK_SIZEOF(size_t)

if test "$ac_cv_type_ssize_t" = yes ; then
    AC_CHECK_SIZEOF(ssize_t)
fi

if test "$ac_cv_type_ptrdiff_t" = yes; then
    AC_CHECK_SIZEOF(ptrdiff_t)
else
    AC_MSG_ERROR([ptrdiff_t type is not available, this is required by C99 standard.  Cannot continue])
fi

AC_CHECK_SIZEOF(wchar_t)
AC_CHECK_SIZEOF(pid_t)

# Check sizes of atomic types so we can define fixed-width types in PRRTE
AC_CHECK_SIZEOF(atomic_short, [],[[#include <stdatomic.h>]])
AC_CHECK_SIZEOF(atomic_int,[],[[#include <stdatomic.h>]])
AC_CHECK_SIZEOF(atomic_long,[],[[#include <stdatomic.h>]])
AC_CHECK_SIZEOF(atomic_llong,[],[[#include <stdatomic.h>]])

#
# Check for type alignments
#

PRRTE_C_GET_ALIGNMENT(bool, PRRTE_ALIGNMENT_BOOL)
PRRTE_C_GET_ALIGNMENT(int8_t, PRRTE_ALIGNMENT_INT8)
PRRTE_C_GET_ALIGNMENT(int16_t, PRRTE_ALIGNMENT_INT16)
PRRTE_C_GET_ALIGNMENT(int32_t, PRRTE_ALIGNMENT_INT32)
PRRTE_C_GET_ALIGNMENT(int64_t, PRRTE_ALIGNMENT_INT64)

if test "$ac_cv_type_int128_t" = yes ; then
  PRRTE_C_GET_ALIGNMENT(int128_t, PRRTE_ALIGNMENT_INT128)
fi

PRRTE_C_GET_ALIGNMENT(char, PRRTE_ALIGNMENT_CHAR)
PRRTE_C_GET_ALIGNMENT(short, PRRTE_ALIGNMENT_SHORT)
PRRTE_C_GET_ALIGNMENT(wchar_t, PRRTE_ALIGNMENT_WCHAR)
PRRTE_C_GET_ALIGNMENT(int, PRRTE_ALIGNMENT_INT)
PRRTE_C_GET_ALIGNMENT(long, PRRTE_ALIGNMENT_LONG)
PRRTE_C_GET_ALIGNMENT(long long, PRRTE_ALIGNMENT_LONG_LONG)
PRRTE_C_GET_ALIGNMENT(float, PRRTE_ALIGNMENT_FLOAT)
PRRTE_C_GET_ALIGNMENT(double, PRRTE_ALIGNMENT_DOUBLE)
PRRTE_C_GET_ALIGNMENT(long double, PRRTE_ALIGNMENT_LONG_DOUBLE)
if test "$ac_cv_type___float128" = yes; then
    PRRTE_C_GET_ALIGNMENT(__float128, PRRTE_ALIGNMENT___FLOAT128)
fi

# We only need these types if we're building the OMPI project, but
# PRRTE currently doesn't protect for their lack of presence well.
PRRTE_C_GET_ALIGNMENT(float _Complex, PRRTE_ALIGNMENT_FLOAT_COMPLEX)
PRRTE_C_GET_ALIGNMENT(double _Complex, PRRTE_ALIGNMENT_DOUBLE_COMPLEX)
PRRTE_C_GET_ALIGNMENT(long double _Complex, PRRTE_ALIGNMENT_LONG_DOUBLE_COMPLEX)

PRRTE_C_GET_ALIGNMENT(void *, PRRTE_ALIGNMENT_VOID_P)
PRRTE_C_GET_ALIGNMENT(size_t, PRRTE_ALIGNMENT_SIZE_T)

#
# Check for other compiler characteristics
#

PRRTE_C_WEAK_SYMBOLS
PRRTE_C_MACRO_WEAK_SYMBOLS


if test "x$CC" = "xicc"; then
    PRRTE_CHECK_ICC_VARARGS
fi

# Check if we support the offsetof compiler directive
PRRTE_CHECK_OFFSETOF

##################################
# Only after setting up C check compiler attributes.
##################################

prrte_show_subtitle "Compiler characteristics"

PRRTE_CHECK_ATTRIBUTES
PRRTE_CHECK_COMPILER_VERSION_ID

##################################
# Assembler Configuration
##################################

prrte_show_subtitle "Assembler"

AM_PROG_AS
PRRTE_CONFIG_ASM

##################################
# Header files
##################################

prrte_show_title "Header file tests"

AC_CHECK_HEADERS([alloca.h aio.h arpa/inet.h dirent.h \
    dlfcn.h endian.h execinfo.h err.h fcntl.h grp.h libgen.h \
    libutil.h memory.h netdb.h netinet/in.h netinet/tcp.h \
    poll.h pthread.h pty.h pwd.h sched.h \
    strings.h stropts.h linux/ethtool.h linux/sockios.h \
    sys/fcntl.h sys/ipc.h sys/shm.h \
    sys/ioctl.h sys/mman.h sys/param.h sys/queue.h \
    sys/resource.h sys/select.h sys/socket.h sys/sockio.h \
    sys/stat.h sys/statfs.h sys/statvfs.h sys/time.h sys/tree.h \
    sys/types.h sys/uio.h sys/un.h net/uio.h sys/utsname.h sys/vfs.h sys/wait.h syslog.h \
    termios.h ulimit.h unistd.h util.h utmp.h malloc.h \
    ifaddrs.h crt_externs.h regex.h mntent.h paths.h \
    ioLib.h sockLib.h hostLib.h shlwapi.h sys/synch.h db.h ndbm.h zlib.h ieee754.h])

AC_CHECK_HEADERS([sys/mount.h], [], [],
[AC_INCLUDES_DEFAULT
#if HAVE_SYS_PARAM_H
#include <sys/param.h>
#endif
])

AC_CHECK_HEADERS([sys/sysctl.h], [], [],
[AC_INCLUDES_DEFAULT
#if HAVE_SYS_PARAM_H
#include <sys/param.h>
#endif
])

# Needed to work around Darwin requiring sys/socket.h for
# net/if.h

AC_CHECK_HEADERS([net/if.h], [], [],
    [#include <stdio.h>
#if STDC_HEADERS
# include <stdlib.h>
# include <stddef.h>
#else
# if HAVE_STDLIB_H
#  include <stdlib.h>
# endif
#endif
#if HAVE_SYS_SOCKET_H
# include <sys/socket.h>
#endif
])

# checkpoint results
AC_CACHE_SAVE

##################################
# Types
##################################

prrte_show_title "Type tests"

AC_CHECK_TYPES([socklen_t, struct sockaddr_in, struct sockaddr_in6,
                struct sockaddr_storage, struct ifreq, struct ethtool_cmd],
               [], [], [AC_INCLUDES_DEFAULT
#if HAVE_SYS_SOCKET_H
#include <sys/socket.h>
#endif
#ifdef HAVE_NETINET_IN_H
#include <netinet/in.h>
#endif
#ifdef HAVE_LINUX_ETHTOOL_H
#include <linux/ethtool.h>
#endif
#ifdef HAVE_NET_IF_H
#include <net/if.h>
#endif])

AC_CHECK_DECLS([ethtool_cmd_speed, SIOCETHTOOL],
               [], [], [AC_INCLUDES_DEFAULT
#ifdef HAVE_LINUX_ETHTOOL_H
#include <linux/ethtool.h>
#endif
#ifdef HAVE_LINUX_SOCKIOS_H
#include <linux/sockios.h>
#endif])

AC_CHECK_MEMBERS([struct ethtool_cmd.speed_hi],
               [], [], [AC_INCLUDES_DEFAULT
#ifdef HAVE_LINUX_ETHTOOL_H
#include <linux/ethtool.h>
#endif
#ifdef HAVE_LINUX_SOCKIOS_H
#include <linux/sockios.h>
#endif])

AC_CHECK_MEMBERS([struct ethtool_cmd.speed_hi],
               [], [], [AC_INCLUDES_DEFAULT
#ifdef HAVE_LINUX_ETHTOOL_H
#include <linux/ethtool.h>
#endif
#ifdef HAVE_LINUX_SOCKIOS_H
#include <linux/sockios.h>
#endif])

AC_CHECK_MEMBERS([struct ethtool_cmd.speed_hi],
               [], [], [AC_INCLUDES_DEFAULT
#ifdef HAVE_LINUX_ETHTOOL_H
#include <linux/ethtool.h>
#endif
#ifdef HAVE_LINUX_SOCKIOS_H
#include <linux/sockios.h>
#endif])

AC_CHECK_MEMBERS([struct ethtool_cmd.speed_hi],
               [], [], [AC_INCLUDES_DEFAULT
#ifdef HAVE_LINUX_ETHTOOL_H
#include <linux/ethtool.h>
#endif
#ifdef HAVE_LINUX_SOCKIOS_H
#include <linux/sockios.h>
#endif])

AC_CHECK_DECLS([AF_UNSPEC, PF_UNSPEC, AF_INET6, PF_INET6],
               [], [], [AC_INCLUDES_DEFAULT
#if HAVE_SYS_SOCKET_H
#include <sys/socket.h>
#endif
#ifdef HAVE_NETINET_IN_H
#include <netinet/in.h>
#endif])

# SA_RESTART in signal.h

PRRTE_VAR_SCOPE_PUSH([MSG])
AC_MSG_CHECKING([if SA_RESTART defined in signal.h])
AC_EGREP_CPP(yes, [
#include <signal.h>
#ifdef SA_RESTART
    yes
#endif ], [MSG=yes VALUE=1], [MSG=no VALUE=0])
AC_DEFINE_UNQUOTED(PRRTE_HAVE_SA_RESTART, $VALUE,
    [Whether we have SA_RESTART in <signal.h> or not])
AC_MSG_RESULT([$MSG])
PRRTE_VAR_SCOPE_POP

AC_CHECK_MEMBERS([struct sockaddr.sa_len], [], [], [
#include <sys/types.h>
#if HAVE_SYS_SOCKET_H
#include <sys/socket.h>
#endif])

AC_CHECK_MEMBERS([struct dirent.d_type], [], [], [
#include <sys/types.h>
#include <dirent.h>])

AC_CHECK_MEMBERS([siginfo_t.si_fd],,,[#include <signal.h>])

AC_CHECK_MEMBERS([siginfo_t.si_band],,,[#include <signal.h>])

#
# Checks for struct member names in struct statfs
#

AC_CHECK_MEMBERS([struct statfs.f_type], [], [], [
AC_INCLUDES_DEFAULT
#ifdef HAVE_SYS_VFS_H
#include <sys/vfs.h>
#endif
#ifdef HAVE_SYS_STATFS_H
#include <sys/statfs.h>
#endif
])

AC_CHECK_MEMBERS([struct statfs.f_fstypename], [], [], [
AC_INCLUDES_DEFAULT
#ifdef HAVE_SYS_PARAM_H
#include <sys/param.h>
#endif
#ifdef HAVE_SYS_MOUNT_H
#include <sys/mount.h>
#endif
#ifdef HAVE_SYS_VFS_H
#include <sys/vfs.h>
#endif
#ifdef HAVE_SYS_STATFS_H
#include <sys/statfs.h>
#endif
])

#
# Checks for struct member names in struct statvfs
#

AC_CHECK_MEMBERS([struct statvfs.f_basetype], [], [], [
AC_INCLUDES_DEFAULT
#ifdef HAVE_SYS_STATVFS_H
#include <sys/statvfs.h>
#endif
])

AC_CHECK_MEMBERS([struct statvfs.f_fstypename], [], [], [
AC_INCLUDES_DEFAULT
#ifdef HAVE_SYS_STATVFS_H
#include <sys/statvfs.h>
#endif
])

#
# Check for ptrdiff type.  Yes, there are platforms where
# sizeof(void*) != sizeof(long) (64 bit Windows, apparently).
#

AC_MSG_CHECKING([for pointer diff type])
if test $ac_cv_type_ptrdiff_t = yes ; then
    prrte_ptrdiff_t="ptrdiff_t"
    prrte_ptrdiff_size=$ac_cv_sizeof_ptrdiff_t
elif test $ac_cv_sizeof_void_p -eq $ac_cv_sizeof_long ; then
    prrte_ptrdiff_t="long"
    prrte_ptrdiff_size=$ac_cv_sizeof_long
elif test $ac_cv_type_long_long = yes && test $ac_cv_sizeof_void_p -eq $ac_cv_sizeof_long_long ; then
    prrte_ptrdiff_t="long long"
    prrte_ptrdiff_size=$ac_cv_sizeof_long_long
else
    AC_MSG_ERROR([Could not find datatype to emulate ptrdiff_t.  Cannot continue])
fi

AC_DEFINE_UNQUOTED([PRRTE_PTRDIFF_TYPE], [$prrte_ptrdiff_t],
    [type to use for ptrdiff_t])
AC_MSG_RESULT([$prrte_ptrdiff_t (size: $prrte_ptrdiff_size)])

#
# Find corresponding types for MPI_Aint, MPI_Count, and MPI_Offset.
# And if relevant, find the corresponding MPI_ADDRESS_KIND,
# MPI_COUNT_KIND, and MPI_OFFSET_KIND.
#

# checkpoint results
AC_CACHE_SAVE

##################################
# Linker characteristics
##################################

AC_MSG_CHECKING([the linker for support for the -fini option])
PRRTE_VAR_SCOPE_PUSH([LDFLAGS_save])
LDFLAGS_save=$LDFLAGS
LDFLAGS="$LDFLAGS_save -Wl,-fini -Wl,finalize"
AC_TRY_LINK([void finalize (void) {}], [], [AC_MSG_RESULT([yes])
        prrte_ld_have_fini=1], [AC_MSG_RESULT([no])
        prrte_ld_have_fini=0])
LDFLAGS=$LDFLAGS_save
PRRTE_VAR_SCOPE_POP

##################################
# Libraries
##################################

prrte_show_title "Library and Function tests"

# Darwin doesn't need -lutil, as it's something other than this -lutil.
PRRTE_SEARCH_LIBS_CORE([openpty], [util])

PRRTE_SEARCH_LIBS_CORE([gethostbyname], [nsl])

PRRTE_SEARCH_LIBS_CORE([socket], [socket])

# Solaris has sched_yield in -lrt, usually in libc
PRRTE_SEARCH_LIBS_CORE([sched_yield], [rt])

# IRIX and CentOS have dirname in -lgen, usually in libc
PRRTE_SEARCH_LIBS_CORE([dirname], [gen])

# Darwin doesn't need -lm, as it's a symlink to libSystem.dylib
PRRTE_SEARCH_LIBS_CORE([ceil], [m])

# -lrt might be needed for clock_gettime
PRRTE_SEARCH_LIBS_CORE([clock_gettime], [rt])

AC_CHECK_FUNCS([asprintf snprintf vasprintf vsnprintf openpty isatty getpwuid fork waitpid execve pipe ptsname setsid mmap tcgetpgrp posix_memalign strsignal sysconf syslog vsyslog regcmp regexec regfree _NSGetEnviron socketpair strncpy_s usleep mkfifo dbopen dbm_open statfs statvfs setpgid setenv __malloc_initialize_hook])

# Sanity check: ensure that we got at least one of statfs or statvfs.

if test $ac_cv_func_statfs = no && test $ac_cv_func_statvfs = no; then
    AC_MSG_WARN([neither statfs() and statvfs() were found])
    AC_MSG_ERROR([Cannot continue])
fi

# On some hosts, htonl is a define, so the AC_CHECK_FUNC will get
# confused.  On others, it's in the standard library, but stubbed with
# the magic glibc foo as not implemented.  and on other systems, it's
# just not there.  This covers all cases.

AC_CACHE_CHECK([for htonl define],
  [ompi_cv_htonl_define],
  [AC_PREPROC_IFELSE([AC_LANG_PROGRAM([
#ifdef HAVE_SYS_TYPES_H
#include <sys/types.h>
#endif
#ifdef HAVE_NETINET_IN_H
#include <netinet/in.h>
#endif
#ifdef HAVE_ARPA_INET_H
#include <arpa/inet.h>
#endif],[
#ifndef ntohl
#error "ntohl not defined"
#endif
])], [prrte_cv_htonl_define=yes], [prrte_cv_htonl_define=no])])

AC_CHECK_FUNC([htonl], [prrte_have_htonl=yes], [prrte_have_htonl=no])

AS_IF([test "$prrte_cv_htonl_define" = "yes" || test "$prrte_have_htonl" = "yes"],
    [AC_DEFINE_UNQUOTED([HAVE_UNIX_BYTESWAP], [1],
        [whether unix byteswap routines -- htonl, htons, nothl, ntohs -- are available])])

#
# Make sure we can copy va_lists (need check declared, not linkable)
#

AC_CHECK_DECL(va_copy, PRRTE_HAVE_VA_COPY=1, PRRTE_HAVE_VA_COPY=0,
    [#include <stdarg.h>])

AC_DEFINE_UNQUOTED(PRRTE_HAVE_VA_COPY, $PRRTE_HAVE_VA_COPY,
    [Whether we have va_copy or not])

AC_CHECK_DECL(__va_copy, PRRTE_HAVE_UNDERSCORE_VA_COPY=1,
    PRRTE_HAVE_UNDERSCORE_VA_COPY=0, [#include <stdarg.h>])

AC_DEFINE_UNQUOTED(PRRTE_HAVE_UNDERSCORE_VA_COPY, $PRRTE_HAVE_UNDERSCORE_VA_COPY,
    [Whether we have __va_copy or not])

AC_CHECK_DECLS(__func__)

# checkpoint results
AC_CACHE_SAVE

##################################
# System-specific tests
##################################

prrte_show_title "System-specific tests"

# Do we have _SC_NPROCESSORS_ONLN? (only going to pass if we also have
# <unistd.h> and sysconf(), which is ok) OS X 10.4 has <unistd.h> and
# sysconf(), but does not have _SC_NPROCESSORS_ONLN.  Doh!

AC_CACHE_CHECK([for _SC_NPROCESSORS_ONLN],
  [ompi_cv_have__SC_NPROCESSORS_ONLN],
  [AC_COMPILE_IFELSE([AC_LANG_PROGRAM([
AC_INCLUDES_DEFAULT
#include <unistd.h>
],
     [int i = _SC_NPROCESSORS_ONLN;])],
     [ompi_cv_have__SC_NPROCESSORS_ONLN="yes"],
     [ompi_cv_have__SC_NPROCESSORS_ONLN="no"])])

AS_IF([test "$ompi_cv_have__SC_NPROCESSORS_ONLN" = "yes"],
      [result=1], [result=0])

AC_DEFINE_UNQUOTED([PRRTE_HAVE__SC_NPROCESSORS_ONLN], [$result],
  [Define to 1 ifyou have the declaration of _SC_NPROCESSORS_ONLN, and to 0 otherwise])

# all: endian
AC_C_BIGENDIAN

PRRTE_CHECK_BROKEN_QSORT

#
# Check out what thread support we have
#

PRRTE_CONFIG_THREADS

CFLAGS="$CFLAGS $THREAD_CFLAGS"
LDFLAGS="$LDFLAGS $THREAD_LDFLAGS"
LIBS="$LIBS $THREAD_LIBS"

PRRTE_WRAPPER_FLAGS_ADD([CFLAGS], [$THREAD_CFLAGS])
PRRTE_WRAPPER_FLAGS_ADD([LDFLAGS], [$THREAD_LDFLAGS])

# no need to update WRAPPER_EXTRA_LIBS - we'll get it from LT later
#
# What is the local equivalent of "ln -s"
#

AC_PROG_LN_S
AC_PROG_GREP
AC_PROG_EGREP

#
# We need as and lex
#

AM_PROG_AS
AM_PROG_LEX

# If we don't have Flex and we don't have a generated .c file
# (distribution tarballs will have the .c file included, but git
# clones will not), then error.  Must have Flex -- other versions of
# Lex are not workable (all things being equal, since this is *only*
# required for developers, we decided that it really was not worth it
# to be portable between different versions of lex ;-).

if test -z "$LEX" || \
   test -n "`echo $LEX | $GREP missing`" || \
   test "`basename $LEX`" != "flex"; then
    if test ! -f "$srcdir/prrte/util/show_help_lex.c"; then
        AC_MSG_WARN([*** Could not find Flex on your system.])
        AC_MSG_WARN([*** Flex is required for developer builds of PRTE.])
        AC_MSG_WARN([*** Other versions of Lex are not supported.])
        AC_MSG_WARN([*** YOU DO NOT NEED FLEX WHEN BUILDING DISTRIBUTION TARBALLS!])
        AC_MSG_ERROR([Cannot continue])
    fi
fi

#
# Look for ps command and arguments
#
PRRTE_PS_FLAVOR_CHECK

#
# File system case sensitivity
#

PRRTE_CASE_SENSITIVE_FS_SETUP

#
# Do we have RLIMIT_NPROC in <sys/resources.h>? (e.g., Solaris does not)
#

AC_CHECK_DECLS([RLIMIT_NPROC], [], [], [
AC_INCLUDES_DEFAULT
#if HAVE_SYS_RESOURCE_H
#include <sys/resource.h>
#endif])

#
# Do we have RLIMIT_MEMLOCK in <sys/resources.h>? (e.g., Solaris does not)
#

AC_CHECK_DECLS([RLIMIT_MEMLOCK], [], [], [
AC_INCLUDES_DEFAULT
#if HAVE_SYS_RESOURCE_H
#include <sys/resource.h>
#endif])

#
# Do we have RLIMIT_NOFILE in <sys/resources.h>? (e.g., Solaris does not)
#

AC_CHECK_DECLS([RLIMIT_NOFILE], [], [], [
AC_INCLUDES_DEFAULT
#if HAVE_SYS_RESOURCE_H
#include <sys/resource.h>
#endif])

#
# Do we have RLIMIT_MEMLOCK in <sys/resources.h>? (e.g., Solaris does not)
#

AC_CHECK_DECLS([RLIMIT_FSIZE], [], [], [
AC_INCLUDES_DEFAULT
#if HAVE_SYS_RESOURCE_H
#include <sys/resource.h>
#endif])

#
# Do we have RLIMIT_CORE in <sys/resources.h>? (e.g., Solaris does not)
#

AC_CHECK_DECLS([RLIMIT_CORE], [], [], [
AC_INCLUDES_DEFAULT
#if HAVE_SYS_RESOURCE_H
#include <sys/resource.h>
#endif])

#
# Do we have RLIMIT_STACK in <sys/resources.h>? (e.g., Solaris does not)
#

AC_CHECK_DECLS([RLIMIT_STACK], [], [], [
AC_INCLUDES_DEFAULT
#if HAVE_SYS_RESOURCE_H
#include <sys/resource.h>
#endif])

#
# Do we have RLIMIT_AS in <sys/resources.h>? (e.g., Solaris does not)
#

AC_CHECK_DECLS([RLIMIT_AS], [], [], [
AC_INCLUDES_DEFAULT
#if HAVE_SYS_RESOURCE_H
#include <sys/resource.h>
#endif])

# checkpoint results
AC_CACHE_SAVE

##################################
# Libevent
##################################

prrte_show_title "Configure libevent"

PRRTE_LIBEV_CONFIG
PRRTE_LIBEVENT_CONFIG

AS_IF([test $prrte_libevent_support -eq 1 && test $prrte_libev_support -eq 1],
      [AC_MSG_WARN([Both libevent and libev support have been specified.])
       AC_MSG_WARN([Only one can be configured against at a time. Please])
       AC_MSG_WARN([remove one from the configure command line.])
       AC_MSG_ERROR([Cannot continue])])

AS_IF([test $prrte_libevent_support -eq 0 && test $prrte_libev_support -eq 0],
      [AC_MSG_WARN([Either libevent or libev support is required, but neither])
       AC_MSG_WARN([was found. Please use the configure options to point us])
       AC_MSG_WARN([to where we can find one or the other library])
       AC_MSG_ERROR([Cannot continue])])

##################################
# PMIx
##################################

prrte_show_title "Configure PMIx"

PRRTE_CHECK_PMIX

##################################
# HWLOC
##################################

prrte_show_title "Configure HWLOC"

PRRTE_HWLOC_CONFIG

##################################
# MCA
##################################

prrte_show_title "Modular Component Architecture (MCA) setup"

AC_MSG_CHECKING([for subdir args])
PRRTE_CONFIG_SUBDIR_ARGS([prrte_subdir_args])
AC_MSG_RESULT([$prrte_subdir_args])

PRRTE_MCA

# checkpoint results
AC_CACHE_SAVE

##################################
# Visibility
##################################
# Check the visibility declspec at the end to avoid problem with
# the previous tests that are not necessarily prepared for
# the visibility feature.

prrte_show_title "Symbol visibility feature"

PRRTE_CHECK_VISIBILITY

##################################
# STOP-ON-EXEC
##################################
# Check ptrace support for stop-on-exec

prrte_show_title "Ptrace stop-on-exec support"

PRRTE_CHECK_PTRACE

############################################################################
# Final top-level PRTE configuration
############################################################################

prrte_show_title "Final top-level PRTE configuration"

############################################################################
# Libtool: part two
# (after C compiler setup = no compiler/linker tests after this)
############################################################################
prrte_show_subtitle "Libtool configuration"

# Use the undocumented solaris_use_stlport4 libtool variable to turn off any
# Cstd/stlport4 linkage. This allows PRTE to be C++ STL agnostic.
if test "x$prrte_cv_c_compiler_vendor" = "xsun"; then
    solaris_use_stlport4="yes"
fi

LT_INIT([dlopen win32-dll])


# What's the suffix of shared libraries?  Inspired by generated
# Libtool code (even though we don't support several of these
# platforms, there didn't seem to be any harm in leaving in some of
# them, alhtough I did remove some that we have never/will never
# support, like OS/2).
PRRTE_DYN_LIB_PREFIX=lib
case $host_os in
cygwin*)
    PRRTE_DYN_LIB_PREFIX=cyg
    PRRTE_DYN_LIB_SUFFIX=dll
    ;;
mingw* | pw32* | cegcc*)
    PRRTE_DYN_LIB_SUFFIX=dll
    ;;
darwin* | rhapsody*)
    PRRTE_DYN_LIB_SUFFIX=dylib
    ;;
hpux9* | hpux10* | hpux11*)
    case $host_cpu in
        ia64*)
        PRRTE_DYN_LIB_SUFFIX=so
        ;;
    *)
        PRRTE_DYN_LIB_SUFFIX=sl
        ;;
    esac
    ;;
*)
   PRRTE_DYN_LIB_SUFFIX=so
   ;;
esac

AC_SUBST(PRRTE_DYN_LIB_PREFIX)
AC_SUBST(PRRTE_DYN_LIB_SUFFIX)

# Need the libtool binary before the rpathify stuff
LT_OUTPUT

############################################################################
# final compiler config
############################################################################
prrte_show_subtitle "Compiler flags"

#
# This is needed for VPATH builds, so that it will -I the appropriate
# include directory.  We delayed doing it until now just so that
# '-I$(top_srcdir)' doesn't show up in any of the configure output --
# purely aesthetic.
#

# Because prrte_config.h is created by AC_CONFIG_HEADERS, we
# don't need to -I the builddir for prrte/include. However, we do
# need to add it for prrte as it doesn't have an AC_CONFIG_HEADERS that
# will install it for us. If we VPATH building, we do need to include the
# source directories, however.
#

if test "$PRRTE_TOP_BUILDDIR" != "$PRRTE_TOP_SRCDIR"; then
    # Note the embedded m4 directives here -- we must embed them
    # rather than have successive assignments to these shell
    # variables, lest the $(foo) names try to get evaluated here.
    # Yuck!
    CPPFLAGS='-I$(top_srcdir) -I$(top_builddir) -I$(top_srcdir)/src/include -I$(top_builddir)/src/include'" $CPPFLAGS"
else
    CPPFLAGS='-I$(top_srcdir) -I$(top_srcdir)/src/include'" $CPPFLAGS"
fi

#
# Delayed the substitution of CFLAGS and CXXFLAGS until now because
# they may have been modified throughout the course of this script.
#

AC_SUBST(CFLAGS)

#
# Aggregate MCA parameters directory
#
AC_SUBST([AMCA_PARAM_SETS_DIR], ['$(prrtedatadir)/amca-param-sets'])

############################################################################
# final wrapper compiler config
############################################################################
prrte_show_subtitle "Wrapper compiler final setup"

PRRTE_SETUP_WRAPPER_FINAL

# If there is a local hook, call it.
m4_ifdef([prrte_CONFIG_LOCAL], [prrte_CONFIG_LOCAL])

############################################################################
# Party on
############################################################################

AC_MSG_CHECKING([if libtool needs -no-undefined flag to build shared libraries])

case "`uname`" in
  CYGWIN*|MINGW*|AIX*)
    ## Add in the -no-undefined flag to LDFLAGS for libtool.
    AC_MSG_RESULT([yes])
    LDFLAGS="$LDFLAGS -no-undefined"
    ;;
  *)
    ## Don't add in anything.
    AC_MSG_RESULT([no])
    ;;
esac

# prrtedatadir, prrtelibdir, and prrteinclude are essentially the same as
# pkg*dir, but will always be */prrte.

prrtedatadir='${datadir}/prrte'
prrtelibdir='${libdir}/prrte'
prrteincludedir='${includedir}/prrte'
AC_SUBST(prrtedatadir)
AC_SUBST(prrtelibdir)
AC_SUBST(prrteincludedir)

PRRTE_SET_MCA_PREFIX([PRRTE_MCA_])
PRRTE_SET_MCA_CMD_LINE_ID([mca])

prrte_want_prd=0

prrte_show_subtitle "Final output"

AC_CONFIG_FILES([
    Makefile
    config/Makefile
    contrib/Makefile
])

PRRTE_CONFIG_FILES

PRRTE_CHECK_LIBNL_SUMMARY

AC_OUTPUT

PRRTE_SUMMARY_PRINT
