# libguestfs OCaml tools common code
# Copyright (C) 2011-2020 Red Hat Inc.
#
# This program is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation; either version 2 of the License, or
# (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program; if not, write to the Free Software
# Foundation, Inc., 51 Franklin Street, Fifth Floor, Boston, MA 02110-1301 USA.

include $(top_srcdir)/subdir-rules.mk

EXTRA_DIST = \
	$(SOURCES_MLI) \
	$(SOURCES_ML) \
	$(SOURCES_C) \
	augeas.README

SOURCES_MLI = \
	augeas.mli

SOURCES_ML = \
	augeas.ml

SOURCES_C = \
	augeas-c.c

# We pretend that we're building a C library.  automake handles the
# compilation of the C sources for us.  At the end we take the C
# objects and OCaml objects and link them into the OCaml library.
# This C library is never used.

noinst_LIBRARIES = libmlaugeas.a

if !HAVE_OCAMLOPT
MLAUGEAS_CMA = mlaugeas.cma
else
MLAUGEAS_CMA = mlaugeas.cmxa
endif

noinst_DATA = $(MLAUGEAS_CMA)

libmlaugeas_a_SOURCES = $(SOURCES_C)
libmlaugeas_a_CPPFLAGS = \
	-DCAML_NAME_SPACE \
	-I. \
	-I$(top_builddir) \
	-I$(shell $(OCAMLC) -where)
libmlaugeas_a_CFLAGS = \
	$(WARN_CFLAGS) $(NO_SNV_CFLAGS) $(NO_UM_CFLAGS) $(WERROR_CFLAGS) \
	$(AUGEAS_CFLAGS) $(LIBXML2_CFLAGS) \
	-fPIC

BOBJECTS = $(SOURCES_ML:.ml=.cmo)
XOBJECTS = $(BOBJECTS:.cmo=.cmx)

OCAMLPACKAGES =
OCAMLFLAGS = $(OCAML_FLAGS) $(OCAML_WARN_ERROR) -ccopt '$(CFLAGS)'

if !HAVE_OCAMLOPT
OBJECTS = $(BOBJECTS)
else
OBJECTS = $(XOBJECTS)
endif

libmlaugeas_a_DEPENDENCIES = $(OBJECTS)

$(MLAUGEAS_CMA): $(OBJECTS) libmlaugeas.a
	$(OCAMLFIND) mklib $(OCAMLPACKAGES) \
	    $(OBJECTS) $(libmlaugeas_a_OBJECTS) -cclib -laugeas -o mlaugeas

# Dependencies.
.depend: $(srcdir)/*.mli $(srcdir)/*.ml
	$(top_builddir)/ocaml-dep.sh $^
-include .depend
