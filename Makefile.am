# Top-level Makefile.am
#
# Copyright (c) 2013-2014 Reuben Thomas <rrt@sc3d.org>
#
# This file is part of cw.
#
# This program is free software; you can redistribute it and/or modify it
# under the terms of the GNU General Public License as published by
# the Free Software Foundation; either version 3, or (at your option)
# any later version.
#
# This program is distributed in the hope that it will be useful, but
# WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
# General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>.

ACLOCAL_AMFLAGS = -I m4

bin_SCRIPTS = cw cw-definitions-path $(top_srcdir)/script/*
man_MANS = $(PACKAGE).1

EXTRA_DIST = $(srcdir)/def/*.lua $(PACKAGE).1.in $(bin_SCRIPTS)
DISTCLEANFILES = $(man_MANS)

pkglibexec_SCRIPTS = $(basename $(wildcard def/*.lua))
$(pkglibexec_SCRIPTS): config.status

%: %.lua
	printf "#!$(bindir)/$(PACKAGE)\n" > $@
	cat $< >> $@

edit = sed \
	-e 's|@pkglibexecdir[@]|$(pkglibexecdir)|g'

$(top_builddir)/$(PACKAGE): $(PACKAGE).lua Makefile
	rm -f $@ $@.tmp
	$(edit) '$@.lua' >$@.tmp
	mv $@.tmp $@
	chmod +x $@
	$(LUA) $@ --version >/dev/null || ( cat $@ && rm $@ )

$(top_builddir)/$(PACKAGE)-definitions-path: $(PACKAGE)-definitions-path.lua Makefile
	rm -f $@ $@.tmp
	$(edit) '$@.lua' >$@.tmp
	mv $@.tmp $@
	chmod +x $@
	$(LUA) $@ --version >/dev/null || ( cat $@ && rm $@ )

$(PACKAGE).1: $(srcdir)/$(PACKAGE).1.in Makefile.am config.status
	rm -f $@ $@.tmp
	$(edit) $(srcdir)/$(PACKAGE).1.in >$@.tmp
	mv $@.tmp $@

clean-local:
	rm -f $(pkglibexec_SCRIPTS) $(PACKAGE) $(PACKAGE)-definitions-path

rockspecs:
	rm -f *.rockspec
	$(LUA) mkrockspecs.lua $(PACKAGE) $(VERSION)
	$(LUA) mkrockspecs.lua $(PACKAGE) git

tag-release:
	git diff --exit-code && \
	git tag -a -m "Release tag" v$(VERSION) && \
	git push && git push --tags

release: rockspecs
	$(MAKE) tag-release && \
	LUAROCKS_CONFIG=$(abs_srcdir)/luarocks-config.lua luarocks --tree=$(abs_srcdir)/luarocks build $(PACKAGE)-$(VERSION)-1.rockspec && \
	woger luarocks package=$(PACKAGE) version=$(VERSION)
