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
        prtedl_libltdl.h \
        prtedl_libltdl_component.c \
        prtedl_libltdl_module.c

# This component will only ever be built statically -- never as a DSO.

noinst_LTLIBRARIES = libmca_prtedl_libltdl.la

libmca_prtedl_libltdl_la_SOURCES = $(sources)
libmca_prtedl_libltdl_la_CPPFLAGS = $(prrte_prtedl_libltdl_CPPFLAGS)
libmca_prtedl_libltdl_la_LDFLAGS = \
        $(prrte_prtedl_libltdl_LDFLAGS) \
        -module -avoid-version
libmca_prtedl_libltdl_la_LIBADD = $(prrte_prtedl_libltdl_LIBS)
