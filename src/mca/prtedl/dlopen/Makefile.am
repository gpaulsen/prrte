#
# Copyright (c) 2004-2010 The Trustees of Indiana University.
#                         All rights reserved.
# Copyright (c) 2014-2015 Cisco Systems, Inc.  All rights reserved.
# Copyright (c) 2019-2020 Intel, Inc.  All rights reserved.
# $COPYRIGHT$
#
# Additional copyrights may follow
#
# $HEADER$
#

sources = \
        prtedl_dlopen.h \
        prtedl_dlopen_component.c \
        prtedl_dlopen_module.c

# This component will only ever be built statically -- never as a DSO.

noinst_LTLIBRARIES = libmca_prtedl_dlopen.la

libmca_prtedl_dlopen_la_SOURCES = $(sources)
libmca_prtedl_dlopen_la_LDFLAGS = -module -avoid-version
libmca_prtedl_dlopen_la_LIBADD = $(prrte_prtedl_dlopen_LIBS)
