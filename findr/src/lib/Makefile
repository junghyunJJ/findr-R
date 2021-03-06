FTYPEBITS=32
GTYPEBITS=8
LIB_NAME=findr
LIB_NAMEFULL="Fast Inference of Networks from Directed Regulations"
LIB_FNAME=lib$(LIB_NAME).so
AUTHOR="Lingfei Wang"
AUTHOR_EMAIL="Lingfei.Wang.github@outlook.com"
URL_LIB="https://github.com/lingfeiwang/findr"
URL_BIN="https://github.com/lingfeiwang/findr-bin"
URL_PYTHON="https://github.com/lingfeiwang/findr-python"
URL_R="https://github.com/lingfeiwang/findr-R"
URL_DOC="https://github.com/lingfeiwang/findr/blob/master/doc.pdf"
URL_LIB_REL="$(URL_LIB)/releases"
URL_BIN_REL="$(URL_BIN)/releases"
URL_R_REL="$(URL_R)/releases"
VERSION1=1
VERSION2=0
VERSION3=7
LICENSE=AGPL-3
LICENSE_FULL="GNU Affero General Public License, Version 3"
LICENSE_URL="https://www.gnu.org/licenses/agpl-3.0"
ifdef INCLUDE_MAKEFILE_BEFORE
#Input package info here
include $(INCLUDE_MAKEFILE_BEFORE)
endif
include Makefile.flags
ifndef LIB_FNAME
LIB_FNAME=lib
endif
ifndef PREFIX
PREFIX=/usr/local
endif
ifndef DIR_BUILD
DIR_BUILD=.
endif
ifndef DIR_SRC
DIR_SRC=.
endif
DIR_INSTALL_PREFIX=$(PREFIX)
DIR_INSTALL_LIB=$(DIR_INSTALL_PREFIX)/lib
DIR_INSTALL_INC0=$(DIR_INSTALL_PREFIX)/include
DIR_INSTALL_INC=$(DIR_INSTALL_INC0)/$(LIB_NAME)

CC=gcc
CFLAGSI=$(addprefix -I ,. $(R_INCLUDE_DIR) $(PREFIX)/include /usr/local/include)
F90C=gfortran
F90FLAGS=-fPIC -fdefault-real-8 -ffixed-form -O3
LD=gcc
#INSTALL=install
OPTFLAGS=-O3 -DNDEBUG=1 -DGSL_RANGE_CHECK_OFF=1 -DHAVE_INLINE=1

LIB_CONFIG=base/config_auto.h
LIB_C=$(wildcard $(DIR_SRC)/*/*.c) $(wildcard $(DIR_SRC)/*/*/*.c)
LIB_C_B=$(basename $(LIB_C))
LIB_F90=$(wildcard $(DIR_SRC)/*/*.f90) $(wildcard $(DIR_SRC)/*/*/*.f90)
LIB_F90_B=$(basename $(LIB_F90))
LIB_H=$(wildcard $(DIR_SRC)/*/*.h) $(wildcard $(DIR_SRC)/*/*/*.h) $(LIB_CONFIG)
LIB_H_B=$(basename $(LIB_H))
LIB_O_C=$(addsuffix .o,$(LIB_C_B))
LIB_O_F90=$(addsuffix .o,$(LIB_F90_B))
LIB_O=$(LIB_O_C) $(LIB_O_F90)
LIB_PRODUCT=$(LIB_O)
LIB_DPRODUCT=$(DIR_BUILD)/$(LIB_FNAME)
INC_DPRODUCT=$(LIB_CONFIG)
INC_INSTALL_FILES=$(LIB_H)
INC_INSTALL_DIRS=$(dir $(LIB_H))
LIB_UNINSTALL=$(addprefix $(DIR_INSTALL_LIB)/,$(notdir $(LIB_DPRODUCT)))
INC_UNINSTALL=$(DIR_INSTALL_INC)
PKGCONFIG=$(LIB_NAME).pc
PKGCONFIG_UNINSTALL=$(DIR_INSTALL_LIB)/pkgconfig/$(LIB_NAME).pc

.PHONY: all clean distclean install-lib install-inc install uninstall

all: $(LIB_DPRODUCT) $(PKGCONFIG)

$(PKGCONFIG):
	@echo "prefix=$(DIR_INSTALL_PREFIX)" > $@
	@echo "exec_prefix=$(DIR_INSTALL_PREFIX)" >> $@
	@echo "libdir=$(DIR_INSTALL_LIB)" >> $@
	@echo "includedir=$(DIR_INSTALL_INC0)" >> $@
	@echo >> $@
	@echo "Name: $(LIB_NAME)" >> $@
	@echo "Description: Fast Inference of Networks from Directed Regulations" >> $@
	@echo "Version: $(VERSION1).$(VERSION2).$(VERSION3)" >> $@
	@echo "Libs: -L$(DIR_INSTALL_LIB) -l$(LIB_NAME) -lgsl" >> $@
	@echo "Cflags: -I$(DIR_INSTALL_INC0)" >> $@

$(LIB_CONFIG):
	@echo "#ifndef _HEADER_LIB_CONFIG_AUTO_H_" > $@
	@echo "#define _HEADER_LIB_CONFIG_AUTO_H_" >> $@
	@echo "#define FTYPEBITS $(FTYPEBITS)" >> $@
	@echo "#define GTYPEBITS $(GTYPEBITS)" >> $@
	@echo "#define LIB_NAME $(LIB_NAME)" >> $@
	@echo "#define VERSION1 $(VERSION1)" >> $@
	@echo "#define VERSION2 $(VERSION2)" >> $@
	@echo "#define VERSION3 $(VERSION3)" >> $@
	@if [ -n "$(DIR_SRC_GSL)" ]; then \
		echo "#define LIBGSL_LOCAL $(LIBGSL_LOCAL)" >> $@; \
	fi
	@echo "#endif" >> $@

$(DIR_BUILD):
	mkdir -p $@

$(LIB_O_C): $(LIB_CONFIG)

$(LIB_O_F90):
	$(F90C) -o $@ -c $(F90FLAGS) $(addsuffix .f90,$(basename $@))

$(LIB_DPRODUCT): $(LIB_PRODUCT) $(DIR_BUILD)
	$(LD) -o $@ $(LIB_PRODUCT) $(LDFLAGS)

clean:
	$(RM) $(LIB_PRODUCT)

distclean: clean
	$(RM) $(LIB_DPRODUCT) $(PKGCONFIG) $(LIB_CONFIG) Makefile.flags $(TMP_FILE)

install-lib: SHELL:=/bin/bash
install-lib: all
	umask 0022 && mkdir -p $(DIR_INSTALL_LIB) && \
	cp $(LIB_DPRODUCT) $(DIR_INSTALL_LIB)/ && \
	chmod 0755 $(DIR_INSTALL_LIB)/$(notdir $(LIB_DPRODUCT)) && \
	ldconfig $(DIR_INSTALL_LIB) || true

install-inc: SHELL:=/bin/bash
install-inc: $(LIB_CONFIG)
	umask 0022 && mkdir -p $(DIR_INSTALL_INC) && \
	for dname in $(INC_INSTALL_DIRS); do \
		mkdir -p $(DIR_INSTALL_INC)/$$dname || exit 1; \
	done
# Then Files
	umask 0022 && for fname in $(INC_INSTALL_FILES); do \
		cp $$fname $(DIR_INSTALL_INC)/$$fname || exit 1; \
		chmod 0644 $(DIR_INSTALL_INC)/$$fname || exit 1; \
	done

install-pkgconfig: $(PKGCONFIG)
	umask 0022 && mkdir -p $(DIR_INSTALL_LIB)/pkgconfig && \
	cp $< $(DIR_INSTALL_LIB)/pkgconfig/
	chmod 0644 $(DIR_INSTALL_LIB)/pkgconfig/$(notdir $<)

install: install-lib install-inc install-pkgconfig

uninstall:
	$(RM) -R $(LIB_UNINSTALL) $(INC_UNINSTALL) $(PKGCONFIG_UNINSTALL)

TMP_FILE=.tmp
Makefile.flags:
	@echo "Testing gcc"
	if ! $(CC) --version > /dev/null 2>&1; then echo "GCC not found. Please download the latest GCC or specify its location in CC variable in Makefile."; exit 1; fi
	gver="$$($(CC) --version)"; \
	t1=$$(echo "$$gver" | grep -io gcc); \
	if ! [ -n "$$t1" ]; then echo "Invalid GCC version. Please download the latest GCC."; exit 1; fi
	cflags="$(CFLAGS) $(CFLAGS_EXTRA) $(CFLAGSI) -fopenmp -ggdb -fPIC -Wall -Wextra -Wconversion -Wsign-conversion -Wundef -Wendif-labels -std=c99 -pedantic-errors $(OPTFLAGS)"; \
	ldflags="$(LDFLAGS) $(LDFLAGS_EXTRA) -L $(PREFIX)/lib -L /usr/local/lib -L /usr/lib -fopenmp -lm -shared -lc"; \
	echo "Testing test method"; \
	if ! $(LD) $$ldflags -o $(TMP_FILE) > /dev/null 2>&1; then \
	echo "Linking with default flags failed."; exit 1; fi; \
	echo "Testing gfortran"; \
	$(LD) $$ldflags -lgfortran -o $(TMP_FILE) > /dev/null 2>&1 && \
	ldflags="$$ldflags -lgfortran"; \
	echo "Testing local GSL" ; \
	if [ -n "$(DIR_SRC_GSL)" ] ; then \
		echo "Testing -Wl,--whole-archive" ; \
		ldflags2="$(DIR_SRC_GSL)/.libs/libgsl.a $(DIR_SRC_GSL)/cblas/.libs/libgslcblas.a"; \
		$(LD) $$ldflags "-Wl,--whole-archive $$ldflags2 -Wl,--no-whole-archive" --shared -o $(TMP_FILE) > /dev/null 2>&1 && \
			ldflags2="-Wl,--whole-archive $$ldflags2 -Wl,--no-whole-archive"; \
		if ! $(LD) $$ldflags $$ldflags2 --shared -o $(TMP_FILE) > /dev/null 2>&1; then \
			echo "Can't link to embedded GSL with right flag." ; exit 1; fi; \
		cflags="-I $(DIR_SRC_GSL) $$cflags" ; \
		ldflags="$$ldflags $$ldflags2" ; \
	else \
		ldflags="$$ldflags -lgsl -lgslcblas"; \
		if ! $(LD) $$ldflags --shared -o $(TMP_FILE) > /dev/null 2>&1; then \
		echo "Link to installed GSL failed."; exit 1; fi; \
	fi ; \
	echo "Testing -Wl,--no-as-needed" ; \
	$(LD) -Wl,--no-as-needed $$ldflags --shared -o $(TMP_FILE) > /dev/null 2>&1 && \
	ldflags="-Wl,--no-as-needed $$ldflags"; \
	echo "CFLAGS=$$cflags" > $@ && \
	echo "LDFLAGS=$$ldflags" >> $@ && \
	$(RM) $(TMP_FILE)







ifdef INCLUDE_MAKEFILE_AFTER
include $(INCLUDE_MAKEFILE_AFTER)
endif


