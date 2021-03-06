# Copyright (C) 2002-2014  Free Software Foundation, Inc.
#      Written by Werner Lemberg <wl@gnu.org>
#
# This file is part of groff.
#
# groff is free software; you can redistribute it and/or modify it under
# the terms of the GNU General Public License as published by the Free
# Software Foundation, either version 3 of the License, or
# (at your option) any later version.
#
# groff is distributed in the hope that it will be useful, but WITHOUT ANY
# WARRANTY; without even the implied warranty of MERCHANTABILITY or
# FITNESS FOR A PARTICULAR PURPOSE.  See the GNU General Public License
# for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program. If not, see <http://www.gnu.org/licenses/>.


# Use this file to produce documentation in various formats; e.g. you can
# say
#
#   make groff.pdf
#
# to get the groff texinfo manual as a PDF file.
#
# Note that you have to compile the groff package first.


srcdir=.
top_srcdir=/home/root/groff-1.22.3

top_builddir=/home/root/groff-1.22.3
pnmtops=pnmtops

# These may be overridden if cross-compiling.
TROFFBIN=$(top_builddir)/src/roff/troff/troff
GROFFBIN=$(top_builddir)/src/roff/groff/groff
GROFF_BIN_PATH=`echo $(groff_bin_dirs) \
                | LANG=C LC_ALL=C sed -e 's|  *|:|g'`

# info files are distributed with the groff package, so makeinfo is
# usually not needed (however, to build from the repo you NEED it!)
MAKEINFO=

# Users who want to print out the groff manual are expected to have a
# working TeX installation.  Note that texi2dvi properly honours the
# `MAKEINFO' environment variable.
TEXI2DVI=texi2dvi

groff_bin_dirs=\
  $(top_builddir)/src/roff/groff \
  $(top_builddir)/src/roff/troff \
  $(top_builddir)/src/preproc/preconv \
  $(top_builddir)/src/preproc/pic \
  $(top_builddir)/src/preproc/eqn \
  $(top_builddir)/src/preproc/tbl \
  $(top_builddir)/src/preproc/grn \
  $(top_builddir)/src/preproc/refer \
  $(top_builddir)/src/preproc/soelim \
  $(top_builddir)/src/preproc/html \
  $(top_builddir)/src/devices/grops \
  $(top_builddir)/src/devices/gropdf \
  $(top_builddir)/src/devices/grodvi \
  $(top_builddir)/src/devices/grotty \
  $(top_builddir)/src/devices/grolj4 \
  $(top_builddir)/src/devices/grolbp \
  $(top_builddir)/src/devices/grohtml

version=`cat $(top_srcdir)/VERSION`
# No additional number if revision is zero.
revision=`sed -e 's/^0$$//' -e 's/^[1-9].*$$/.&/' $(top_srcdir)/REVISION`

FFLAG=-F$(top_builddir)/font -F$(top_srcdir)/font
TFLAG=-M$(top_builddir)/tmac -M$(top_srcdir)/tmac

TROFF=$(TROFFBIN) $(TFLAG) $(FFLAG) -ww
GROFF=\
  LANG=C \
  LC_ALL=C \
  sed -e "s;@VERSION@;$(version)$(revision);" $< \
    | GROFF_COMMAND_PREFIX= \
      GROFF_BIN_PATH="$(GROFF_BIN_PATH)" \
      $(GROFFBIN) $(TFLAG) $(FFLAG) -Upet -ww

imagedir=img

.SUFFIXES: .me .ms .ps .html .txt .texinfo .dvi .pdf .xhtml
.PHONY: all prepare_examples clean distclean realclean extraclean

# For simplicity, we always call preconv, grn, and eqn.
.me.txt:
	$(GROFF) -k -Tutf8 -ge -me >$@
.me.ps:
	$(GROFF) -k -Tps -ge -me >$@

.ms.html:
	$(GROFF) -P-p -P-b -P-I`basename $< | sed -e 's|.ms$$||'` \
	         -P-D$(imagedir) -P-V -Thtml -ms >$@
.ms.txt:
	$(GROFF) -Tascii -ms -mwww >$@
.ms.ps:
	$(GROFF) -Tps -ms -mwww >$@

.texinfo.txt:
	LANG=C \
	LC_ALL=C \
	$(MAKEINFO) --enable-encoding -I$(srcdir) --plaintext -o $@ $<
.texinfo.dvi:
	LANG=C \
	LC_ALL=C \
	MAKEINFO=$(MAKEINFO) $(TEXI2DVI) -e $<
.texinfo.pdf:
	LANG=C \
	LC_ALL=C \
	MAKEINFO=$(MAKEINFO) $(TEXI2DVI) -e --pdf $<
.texinfo.html:
	LANG=C \
	LC_ALL=C \
	$(MAKEINFO) --enable-encoding -I$(srcdir) --html --no-split $< \
	  && $(SHELL) $(srcdir)/fixinfo.sh $@

all: prepare_examples

prepare_examples: grnexmpl.g groff.css
	test -f grnexmpl.g || cp $(srcdir)/grnexmpl.g .
	test -f groff.css || cp $(srcdir)/groff.css .

groff.info: groff.texinfo
	$(MAKEINFO) --enable-encoding -I$(srcdir) $(srcdir)/groff.texinfo

gnu.eps: gnu.xpm
	xpmtoppm $(srcdir)/gnu.xpm | pnmdepth 15 | $(pnmtops) -noturn >$@

pic.html: pic.ms
	$(GROFF) -P-p -P-I`basename $< | sed -e 's|.ms$$||'` \
	         -P-D$(imagedir) -P-j`basename $< | sed -e 's|.ms$$||'` \
	         -Thtml -P-V -ms >$@

webpage.html: webpage.ms gnu.eps groff.css
	$(GROFF) -P-j`basename $< | sed -e 's|.ms$$||'` \
	         -P-nrpb -P-I`basename $< | sed -e 's|.ms$$||'` \
	         -P-D$(imagedir) -Thtml -ms >$@

webpage.ps: gnu.eps

grnexmpl.ps: grnexmpl.me grnexmpl.g

split-html:
	LANG=C \
	LC_ALL=C \
	$(MAKEINFO) --enable-encoding -I$(srcdir) --html groff.texinfo \
	  && for f in groff/*; do $(SHELL) $(srcdir)/fixinfo.sh $$f; done

clean:
	rm -f *.ps *.html *.txt core
	rm -f *.aux *.dvi *.pdf *.log *.toc texput.log
	rm -f *.cp *.cps *.cv *.cn *.es *.ess *.fn *.fns *.ky *.kys \
	      *.ma *.mas *.op *.ops *.pg *.pgs *.rq *.rqs *.st *.sts \
	      *.tp *.tps *.tr *.vr *.vrs *.xhtml
	-rm -rf img

distclean: clean

realclean: distclean
	rm -f *.png *.eps

extraclean: distclean
	rm -f core *~ \#* junk temp grot

########################################################################
# Emacs settings
########################################################################
#
# Local Variables:
# mode: makefile
# End:
