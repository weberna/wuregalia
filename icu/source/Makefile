#******************************************************************************
#
#   Copyright (C) 1998-2010, International Business Machines
#   Corporation and others.  All Rights Reserved.
#
#******************************************************************************
## Top-level Makefile.in for ICU
## Stephen F. Booth

srcdir = .
top_srcdir = .

top_builddir = .

include $(top_builddir)/icudefs.mk

docdir = $(datadir)/doc
docsubdir = $(PACKAGE)$(ICULIBDASHSUFFIX)/html
docsubsrchdir = $(docsubdir)/search
docfilesdir = doc/html
docfiles = $(docfilesdir)/*.png $(docfilesdir)/*.html $(docfilesdir)/*.css $(docfilesdir)/*.tag $(docfilesdir)/installdox
docsrchdir = $(docfilesdir)/search
docsrchfiles = $(docsrchdir)/*

##

## Build directory information
subdir = .

#AUTOCONF = @AUTOCONF@

## Optional directory setup
LAYOUT = layout layoutex
ICUIO = io
EXTRA = extra
TEST = test
SAMPLE = samples

DOXYGEN = 
DOCZIP = icu-docs.zip

## Files to remove for 'make clean'
CLEANFILES = *~

ALL_PKGCONFIG_SUFFIX=uc i18n io le lx
ALL_PKGCONFIG_FILES=$(ALL_PKGCONFIG_SUFFIX:%=$(top_builddir)/config/icu-%.pc)

## Files built (autoconfed) and installed
INSTALLED_BUILT_FILES = $(top_builddir)/config/Makefile.inc $(top_builddir)/config/pkgdata.inc $(top_builddir)/config/icu-config $(top_srcdir)/config/mh-darwin $(EXTRA_DATA:%=$(DESTDIR)$(pkglibdir)/%) $(ALL_PKGCONFIG_FILES)

## Files built (autoconfed) but not installed
LOCAL_BUILT_FILES = icudefs.mk config/icucross.mk

DOCDIRS = common i18n
SUBDIRS =  stubdata common i18n $(LAYOUT) tools data $(ICUIO) $(EXTRA) $(SAMPLE) $(TEST)

SECTION = 1

MANX_FILES = config/icu-config.$(SECTION)

ALL_MAN_FILES = $(MANX_FILES)

## Extra files to install [nothing at present]
EXTRA_DATA =

## List of phony targets
.PHONY : all all-local all-recursive install install-local install-udata install-udata-files install-udata-dlls		\
install-recursive clean clean-local clean-recursive distclean		\
distclean-local distclean-recursive doc dist dist-local dist-recursive	\
check check-local check-recursive clean-recursive-with-twist install-icu \
doc install-doc tests icu4j-data icu4j-data-install update-windows-makefiles xcheck-local xcheck-recursive xperf xcheck xperf-recursive

## Clear suffix list
.SUFFIXES :

## List of standard targets
all: all-local all-recursive
install: install-recursive install-local
clean: clean-recursive-with-twist clean-local
distclean : distclean-recursive distclean-local
dist: dist-recursive dist-local
check: all check-recursive
check-recursive: all
xcheck: all xcheck-recursive
xperf: all xperf-recursive

xcheck-recursive: all xcheck-local
	@$(MAKE) -C test xcheck

xperf-recursive: all tests
	@$(MAKE) -C test/perf xperf

ifeq ($(DOXYGEN),)
doc:
	@echo you need Doxygen to generate documentation. Doxygen can be found on the Web
	@echo at http://www.doxygen.org/
else
doc: doc/html/index.html

doc/html/index.html: Doxyfile $(wildcard ./common/unicode/platform.h $(srcdir)/common/unicode/*.h $(srcdir)/i18n/unicode/*.h $(srcdir)/layout/unicode/*.h $(srcdir)/io/unicode/*.h)
	$(DOXYGEN)

Doxyfile: $(srcdir)/Doxyfile.in
	CONFIG_FILES=$@ CONFIG_HEADERS= $(SHELL) ./config.status

$(DOCZIP): doc
	-$(RMV) $(DOCZIP)
	( cd doc/html ; zip -r ../../$(DOCZIP) * )
endif

LOCAL_SUBDIRS = $(SUBDIRS)
CLEAN_FIRST_SUBDIRS = tools

$(LIBDIR) $(BINDIR):
	-$(MKINSTALLDIRS) $@

## Recursive targets
all-recursive install-recursive clean-recursive distclean-recursive dist-recursive check-recursive: $(LIBDIR) $(BINDIR)
	@dot_seen=no; \
	target=`echo $@ | sed s/-recursive//`; \
	list='$(LOCAL_SUBDIRS)'; for subdir in $$list; do \
	  echo "$(MAKE)[$(MAKELEVEL)]: Making \`$$target' in \`$$subdir'"; \
	  if test "$$subdir" = "."; then \
	    dot_seen=yes; \
	    local_target="$$target-local"; \
	  else \
	    local_target="$$target"; \
	  fi; \
	  (cd $$subdir && $(MAKE) RECURSIVE=YES $$local_target) || exit; \
	done; \
	if test "$$dot_seen" = "no"; then \
	  $(MAKE) "$$target-local" || exit; \
	fi

clean-recursive-with-twist:
	$(MAKE) clean-recursive LOCAL_SUBDIRS='$(CLEAN_FIRST_SUBDIRS) $(filter-out $(CLEAN_FIRST_SUBDIRS),$(LOCAL_SUBDIRS))'

all-local: $(srcdir)/configure $(LOCAL_BUILT_FILES) $(INSTALLED_BUILT_FILES)

install-local: install-icu install-manx

install-icu: $(INSTALLED_BUILT_FILES)
	@$(MKINSTALLDIRS) $(DESTDIR)$(pkgdatadir)/config
	@$(MKINSTALLDIRS) $(DESTDIR)$(pkglibdir)
	@$(MKINSTALLDIRS) $(DESTDIR)$(bindir)
	@$(MKINSTALLDIRS) $(DESTDIR)$(sbindir)
	$(INSTALL_DATA) $(top_srcdir)/config/mh-darwin $(DESTDIR)$(pkgdatadir)/config/mh-darwin
	$(INSTALL_SCRIPT) $(top_srcdir)/mkinstalldirs $(DESTDIR)$(pkgdatadir)/mkinstalldirs
	$(INSTALL_SCRIPT) $(top_srcdir)/install-sh $(DESTDIR)$(pkgdatadir)/install-sh
	@$(MKINSTALLDIRS) $(DESTDIR)$(libdir)/pkgconfig
	$(INSTALL_DATA) $(ALL_PKGCONFIG_FILES) $(DESTDIR)$(libdir)/pkgconfig/
	$(INSTALL_DATA) $(top_srcdir)/../license.html $(DESTDIR)$(pkgdatadir)/license.html
	$(INSTALL_SCRIPT) $(top_builddir)/config/icu-config $(DESTDIR)$(bindir)/icu-config
	$(INSTALL_DATA) $(top_builddir)/config/Makefile.inc $(DESTDIR)$(pkglibdir)/Makefile.inc
	$(INSTALL_DATA) $(top_builddir)/config/pkgdata.inc $(DESTDIR)$(pkglibdir)/pkgdata.inc
	cd $(DESTDIR)$(pkglibdir)/..; \
	    $(RM) current && ln -s $(VERSION) current; \
	    $(RM) Makefile.inc && ln -s current/Makefile.inc Makefile.inc; \
	    $(RM) pkgdata.inc && ln -s current/pkgdata.inc pkgdata.inc

ifeq ($(DOXYGEN),)
install-doc:
else
install-doc: doc
	$(RM) -r $(DESTDIR)$(docdir)/$(docsubdir)
	$(MKINSTALLDIRS) $(DESTDIR)$(docdir)/$(docsubsrchdir)
	$(INSTALL_DATA) $(docfiles) $(DESTDIR)$(docdir)/$(docsubdir)
	$(INSTALL_DATA) $(docsrchfiles) $(DESTDIR)$(docdir)/$(docsubsrchdir)
endif

$(DESTDIR)$(pkglibdir)/%: $(top_srcdir)/../data/%
	$(INSTALL_DATA) $< $@

# Build the tests, but don't run them.
tests: all
	$(MAKE) -C $(top_builddir)/test

clean-local:
	test -z "$(CLEANFILES)" || $(RMV) $(CLEANFILES)
	-$(RMV) "test-*.xml"
	-$(RMV) "perf-*.xml"
	-$(RMV) $(ALL_PKGCONFIG_FILES)
	$(RMV) Doxyfile doc $(DOCZIP)

distclean-local: clean-local
	$(RMV) $(top_builddir)/config/Makefile.inc $(top_builddir)/config/pkgdata.inc $(top_builddir)/config/icu-config $(top_builddir)/config/icu.pc $(ALL_PKGCONFIG_FILES)
	$(RMV) config.cache config.log config.status $(top_builddir)/config/icucross.mk autom4te.cache
	$(RMV) Makefile config/Makefile icudefs.mk $(LIBDIR) $(BINDIR)
	-$(RMV) dist

check-local: xcheck-local
	-$(RMV) test-local.xml

xcheck-local: $(top_builddir)/config/icu-config $(top_builddir)/config/Makefile.inc $(top_builddir)/config/pkgdata.inc
	@echo verifying that icu-config --selfcheck can operate
	@test "passed" = "$(shell $(top_builddir)/config/icu-config --selfcheck 2>&1)" || (echo "FAIL: icu-config could not run properly." ; exit 1)
	@echo verifying that $(MAKE) -f Makefile.inc selfcheck can operate
	@test "passed" = "$(shell $(MAKE) --no-print-directory -f $(top_builddir)/config/Makefile.inc SELFCHECK=1 selfcheck)" || (echo "FAIL: Makefile.inc could not run properly." ; exit 1 )
	@echo "PASS: config selfcheck OK"

#$(srcdir)/configure : $(srcdir)/configure.in $(top_srcdir)/aclocal.m4
#	cd $(srcdir) && $(AUTOCONF)

icudefs.mk: $(srcdir)/icudefs.mk.in  $(top_builddir)/config.status
	cd $(top_builddir) \
		&& CONFIG_FILES=$(subdir)/$@ CONFIG_HEADERS= $(SHELL) ./config.status

config/icucross.mk: $(top_builddir)/icudefs.mk  $(top_builddir)/Makefile
	@echo rebuilding $@
	@(echo "CROSS_ICU_VERSION=$(VERSION)" ;\
	  echo "TOOLEXEEXT=$(EXEEXT)" \
	   ) > $@
	@(echo 'TOOLBINDIR=$$(cross_buildroot)/bin' ;\
	  echo 'TOOLLIBDIR=$$(cross_buildroot)/lib' ;\
	  echo "INVOKE=$(LDLIBRARYPATH_ENVVAR)=$(LIBRARY_PATH_PREFIX)"'$$(TOOLLIBDIR):$$(cross_buildroot)/stubdata:$$(cross_buildroot)/tools/ctestfw:$$$$'"$(LDLIBRARYPATH_ENVVAR)" ;\
	  echo "PKGDATA_INVOKE=$(LDLIBRARYPATH_ENVVAR)=$(LIBRARY_PATH_PREFIX)"'$$(cross_buildroot)/stubdata:$$(cross_buildroot)/tools/ctestfw:$$(TOOLLIBDIR):$$$$'"$(LDLIBRARYPATH_ENVVAR) "'$$'"(PKGDATA_INVOKE_OPTS)" ;\
	  echo ) >> $@


config/icu.pc: $(srcdir)/config/icu.pc.in
	cd $(top_builddir) \
	 && CONFIG_FILES=$(subdir)/$@ CONFIG_HEADERS= $(SHELL) ./config.status

config/icu-uc.pc: config/icu.pc Makefile icudefs.mk
	@cat config/icu.pc > $@
	@echo "Description: $(PACKAGE_ICU_DESCRIPTION): Common and Data libraries" >> $@
	@echo "Name: $(PACKAGE)-uc" >> $@
	@echo "Libs:" '-L$${libdir}' "${ICULIBS_UC}" "${ICULIBS_DT}" >> $@
	@echo "Libs.private:" '$${baselibs}' >> $@
	@echo $@ updated.

config/icu-i18n.pc: config/icu.pc Makefile icudefs.mk
	@cat config/icu.pc > $@
	@echo "Description: $(PACKAGE_ICU_DESCRIPTION): Internationalization library" >> $@
	@echo "Name: $(PACKAGE)-i18n" >> $@
	@echo "Requires: icu-uc" >> $@
	@echo "Libs:" "${ICULIBS_I18N}" >> $@
	@echo $@ updated.

config/icu-io.pc: config/icu.pc Makefile icudefs.mk
	@cat config/icu.pc > $@
	@echo "Description: $(PACKAGE_ICU_DESCRIPTION): Stream and I/O Library" >> $@
	@echo "Name: $(PACKAGE)-io" >> $@
	@echo "Requires: icu-i18n" >> $@
	@echo "Libs:" "${ICULIBS_IO}" >> $@
	@echo $@ updated.

config/icu-le.pc: config/icu.pc Makefile icudefs.mk
	@cat config/icu.pc > $@
	@echo "Description: $(PACKAGE_ICU_DESCRIPTION): Layout library" >> $@
	@echo "Name: $(PACKAGE)-le" >> $@
	@echo "Requires: icu-uc" >> $@
	@echo "Libs:" "${ICULIBS_LE}" >> $@
	@echo $@ updated.

config/icu-lx.pc: config/icu.pc Makefile icudefs.mk
	@cat config/icu.pc > $@
	@echo "Description: $(PACKAGE_ICU_DESCRIPTION): Paragraph Layout library" >> $@
	@echo "Name: $(PACKAGE)-lx" >> $@
	@echo "Requires: icu-le" >> $@
	@echo "Libs:" "${ICULIBS_LX}" >> $@
	@echo $@ updated.


Makefile: $(srcdir)/Makefile.in icudefs.mk $(top_builddir)/config.status
	cd $(top_builddir) \
		&& CONFIG_FILES=$(subdir)/$@ CONFIG_HEADERS= $(SHELL) ./config.status

$(top_builddir)/config/Makefile.inc: $(srcdir)/config/Makefile.inc.in  $(top_builddir)/config.status
	cd $(top_builddir) \
		&& CONFIG_FILES=$@ CONFIG_HEADERS= $(SHELL) ./config.status

$(top_builddir)/config/pkgdata.inc: icudefs.mk $(top_builddir)/config/pkgdataMakefile
	cd $(top_builddir)/config; \
		$(MAKE) -f pkgdataMakefile

$(top_builddir)/config/pkgdataMakefile:
	cd $(top_builddir) \
		&& CONFIG_FILES=$@ CONFIG_HEADERS= $(SHELL) ./config.status

$(top_builddir)/config/icu-config: $(top_builddir)/Makefile $(top_srcdir)/config/icu-config-top $(top_srcdir)/config/icu-config-bottom $(top_builddir)/config/Makefile.inc $(top_srcdir)/config/mh-darwin $(top_srcdir)/config/make2sh.sed
	-$(RMV) $@
	$(INSTALL_SCRIPT) $(top_srcdir)/config/icu-config-top $@
	chmod u+w $@
	@echo "# Following from $(top_srcdir)/config/mh-darwin" >> $@
	LC_ALL=C sed -f $(top_srcdir)/config/make2sh.sed < $(top_builddir)/config/Makefile.inc | grep -v '#M#' | uniq >> $@
	LC_ALL=C sed -f $(top_srcdir)/config/make2sh.sed < $(top_srcdir)/config/mh-darwin | grep -v '#M#' | uniq >> $@
	cat $(top_srcdir)/config/icu-config-bottom >> $@
	echo "# Rebuilt on "`date` >> $@
	chmod u-w $@

config.status: $(srcdir)/configure $(srcdir)/common/unicode/uvernum.h
	@echo
	@echo
	@echo "*** config.status has become stale ***"
	@echo "   'configure' and/or 'uvernum.h' have changed, please"
	@echo "  do 'runConfigureICU' (or 'configure') again, as per"
	@echo "  the readme.html."
	@echo
	@echo
	exit 1


install-manx: $(MANX_FILES)
	$(MKINSTALLDIRS) $(DESTDIR)$(mandir)/man$(SECTION)
	$(INSTALL_DATA) $? $(DESTDIR)$(mandir)/man$(SECTION)

config/%.$(SECTION): $(srcdir)/config/%.$(SECTION).in
	cd $(top_builddir) \
	 && CONFIG_FILES=$(subdir)/$@ CONFIG_HEADERS= $(SHELL) ./config.status

icu4j-data-install icu4j-data: all tests
	@echo ICU4J_ROOT=$(ICU4J_ROOT)
	@$(MAKE) -C test/testdata $@
	@$(MAKE) -C data $@

# For updating Windows makefiles

WINDOWS_UPDATEFILES=$(srcdir)/data/makedata.mak $(shell find $(srcdir) -name '*.vcproj' -o -name '*.vcxproj')

WINDOWS_UPDATEFILES_SED=config/windows-update.sed

update-windows-makefiles: config.status
	@echo Updating Windows Makefiles for ICU $(VERSION)
	CONFIG_FILES=$(WINDOWS_UPDATEFILES_SED) CONFIG_HEADERS= $(SHELL) ./config.status
	@for file in $(WINDOWS_UPDATEFILES); do \
	  echo "Updating $$file"; \
	  mv "$${file}" "$${file}.bak" && \
	  sed -f $(WINDOWS_UPDATEFILES_SED) < "$${file}.bak" > "$${file}" && \
	  rm "$${file}.bak"; \
	done;
	$(RMV) $(WINDOWS_UPDATEFILES_SED)
	@echo Please check over the changes carefully before checking them in.

# For building a source distribution.
distcheck dist-local:
	$(MAKE) -C . -f config/dist.mk srcdir="$(srcdir)" top_srcdir="$(top_srcdir)" $@
