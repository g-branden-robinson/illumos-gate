#
# CDDL HEADER START
#
# The contents of this file are subject to the terms of the
# Common Development and Distribution License (the "License").
# You may not use this file except in compliance with the License.
#
# You can obtain a copy of the license at usr/src/OPENSOLARIS.LICENSE
# or http://www.opensolaris.org/os/licensing.
# See the License for the specific language governing permissions
# and limitations under the License.
#
# When distributing Covered Code, include this CDDL HEADER in each
# file and include the License file at usr/src/OPENSOLARIS.LICENSE.
# If applicable, add the following below this CDDL HEADER, with the
# fields enclosed by brackets "[]" replaced with your own identifying
# information: Portions Copyright [yyyy] [name of copyright owner]
#
# CDDL HEADER END
#

#
# Copyright (c) 1990, 2010, Oracle and/or its affiliates. All rights reserved.
# Copyright 2022 Garrett D'Amore <garrett@damore.org>
# Copyright 2019, Joyent, Inc.
#

SYNCPROG=	syncinit syncloop syncstat

PROG=		6to4relay arp if_mpadm \
		in.comsat in.fingerd in.rarpd in.rexecd in.rlogind \
		in.rshd in.rwhod in.telnetd in.tftpd ipaddrsel \
		ndd ndp $(SYNCPROG)

MANIFEST=	rarp.xml telnet.xml comsat.xml finger.xml \
		login.xml shell.xml rexec.xml
SVCMETHOD=	svc-sockfilter

ROOTFS_PROG=	hostconfig route soconfig
SBINLINKS=	hostconfig route

RPCSVCPROG=	hostconfig
AUDITPROG=	in.rexecd in.rlogind in.rshd in.telnetd
PAMPROG=	in.rexecd in.rlogind in.rshd in.telnetd
SOCKETPROG=	6to4relay arp hostconfig if_mpadm in.comsat \
		in.fingerd in.rarpd in.rexecd in.rlogind in.rshd \
		in.rwhod in.telnetd in.tftpd ipaddrsel ndp route
NSLPROG=	6to4relay arp hostconfig in.comsat in.rarpd \
		in.rexecd in.rlogind in.rshd in.rwhod in.telnetd \
		in.tftpd ipaddrsel ndp route
CMDPROG=	in.telnetd
K5PROGS=	in.telnetd in.rlogind in.rshd
TSNETPROG=	route
DLADMPROG=	6to4relay
DEFAULTFILES=	telnetd.dfl

PROGSRCS=	$(PROG:%=%.c)
TFTPDOBJS=	in.tftpd.o tftpsubs.o
OTHERSRC=	../usr.bin/tftp/tftpsubs.c
K5RLOGINOBJS=	in.rlogind.o
K5RSHDOBJS=	in.rshd.o
K5TELNETOBJS=	in.telnetd.o
SRCS=		$(PROGSRCS) $(OTHERSRC)

SUBDIRS=	ifconfig ilbadm in.rdisc in.routed \
		in.talkd inetadm inetconv ipadm ipmpstat ipqosconf ipsecutils \
		nwamadm nwamcfg ping routeadm \
		snoop sppptun traceroute wificonfig

MSGSUBDIRS=	ifconfig ilbadm in.routed in.talkd \
		inetadm inetconv ipadm ipmpstat ipqosconf ipsecutils \
		nwamadm nwamcfg routeadm sppptun snoop wificonfig

# This controls the degree of compiler warnings emitted, and is named for
# 'lint' for historical reasons.  Eventually this hack should go away, and all
# in PROG should be clean.
LINTCLEAN=	6to4relay arp in.rlogind in.rshd in.telnetd in.tftpd \
		ipaddrsel ndp route \
		in.rarpd if_mpadm $(SYNCPROG)
# And as programs are verified not to attempt to write into constants,
# -xstrconst should be used to ensure they stay that way.
CONSTCLEAN=

include ../../Makefile.cmd
ROOTMANIFESTDIR=	$(ROOTSVCNETWORK)
$(ROOTMANIFEST)		:= FILEMODE= 444
include ../Makefile.cmd-inet

ROOTSBINPROG = $(ROOTFS_PROG:%=$(ROOTSBIN)/%)
ROOTUSRSBINLINKS = $(SBINLINKS:%=$(ROOTUSRSBIN)/%)

COMMONOBJS=	addr_match.o kcmd.o store_forw_creds.o
COMMONSRCS=	$(COMMONOBJS:%.o=$(CMDINETCOMMONDIR)/%.c)
SRCS+=		$(COMMONSRCS)

CERRWARN += -_gcc=-Wno-implicit-function-declaration
CERRWARN += $(CNOWARN_UNINIT)
CERRWARN += -_gcc=-Wno-unused-variable
CERRWARN += -_gcc=-Wno-unused-function
CERRWARN += -_gcc=-Wno-parentheses
CERRWARN += -_gcc=-Wno-char-subscripts
CERRWARN += -_gcc=-Wno-extra
CERRWARN += -_gcc=-Wno-address

# "missing type for func"
SMATCH=off

#
# Message catalog
#
POFILES=	6to4relay.po if_mpadm.po in.comsat.po ipaddrsel.po route.po \
		soconfig.po
POFILE=		usr.sbin.po

all:=		TARGET= all
install:=	TARGET= install
clean:=		TARGET= clean
clobber:=	TARGET= clobber
_msg:=		TARGET= _msg

CLOBBERFILES +=	$(ROOTFS_PROG) $(PROG)
CLEANFILES += $(COMMONOBJS) $(K5RLOGINOBJS) $(K5RSHDOBJS) $(TFTPDOBJS) \
		$(K5TELNETOBJS)

CPPFLAGS +=	-DSYSV -DBSD_COMP -I$(CMDINETCOMMONDIR) -I

include $(SRC)/lib/gss_mechs/mech_krb5/Makefile.mech_krb5
K5LIBS=

# Eventually just plain CFLAGS should be += -v, but not until all in
# PROGS are lint clean.
$(LINTCLEAN)		:=	CFLAGS += $(CCVERBOSE)
$(CONSTCLEAN)		:=	CFLAGS += $(XSTRCONST)

$(SYNCPROG)		:=	LDLIBS += -ldlpi
$(SOCKETPROG)		:=	LDLIBS += -lsocket
$(NSLPROG)		:=	LDLIBS += -lnsl
$(AUDITPROG)		:=	LDLIBS += -lbsm
$(PAMPROG)		:=	LDLIBS += -lpam
$(RPCSVCPROG)		:=	LDLIBS += -lrpcsvc
$(K5PROGS)		:=	LDFLAGS += $(KRUNPATH) \
				-L$(ROOT)$(KLIBDIR_DO) -L$(ROOT)$(KLIBDIR_GL)
$(K5PROGS)		:=	K5LIBS=  -lmech_krb5
$(K5PROGS)		:=	CPPFLAGS += -I$(SRC)/head \
				-I$(SRC)/uts/common/ \
				-I$(SRC)/uts/common/gssapi/mechs/krb5/include \
				-I$(SRC)/lib/gss_mechs/mech_krb5/include \
				-I$(SRC)/lib/pam_modules/krb5
LDLIBS +=	$(K5LIBS)
$(TSNETPROG)		:=	LDLIBS += -ltsnet
$(DLADMPROG)		:=	LDLIBS += -ldladm

in.rarpd		:=	LDLIBS += -linetutil -ldlpi
if_mpadm		:=	LDLIBS += -linetutil -lipmp
if_mpadm.po		:=	XGETFLAGS += -a
route			:=	CPPFLAGS += -DNDEBUG
ndd			:=	LDLIBS += -ldladm -lipadm
$(RELEASE_BUILD)ndd	:=	CERRWARN += -_gcc=-Wno-unused
in.comsat		:=	LDFLAGS += $(MAPFILE.NGB:%=-Wl,-M%)

.KEEP_STATE:

.PARALLEL:

all: $(PROG) $(ROOTFS_PROG) $(SUBDIRS) THIRDPARTYLICENSE.arp

#
# message catalog
#
_msg: $(MSGSUBDIRS) $(POFILE)

syncutil: $(SYNCPROG)

$(POFILE): $(POFILES)
	$(RM) $@
	cat $(POFILES) > $@

%.o: $(CMDINETCOMMONDIR)/%.c
	$(COMPILE.c) -o $@ $<

in.telnetd: $(K5TELNETOBJS)
	$(LINK.c) $(K5TELNETOBJS) -o $@ $(LDLIBS)
	$(POST_PROCESS)

in.rlogind: $(K5RLOGINOBJS) $(COMMONOBJS)
	$(LINK.c) $(K5RLOGINOBJS) $(COMMONOBJS) -o $@ $(LDLIBS)
	$(POST_PROCESS)

in.rshd: $(K5RSHDOBJS) $(COMMONOBJS)
	$(LINK.c) $(K5RSHDOBJS) $(COMMONOBJS) -o $@ $(LDLIBS)
	$(POST_PROCESS)

in.tftpd: $(TFTPDOBJS)
	$(LINK.c) $(TFTPDOBJS) -o $@ $(LDLIBS)
	$(POST_PROCESS)

tftpsubs.o: $(OTHERSRC)
	$(COMPILE.c) $(OTHERSRC) -o $@
	$(POST_PROCESS_O)

$(ROOTUSRSBINLINKS):
	-$(RM) $@; $(SYMLINK) ../../sbin/$(@F) $@

install: $(PROG) $(ROOTFS_PROG) $(SUBDIRS) .WAIT $(ROOTUSRSBINPROG) \
	$(ROOTSBINPROG) $(ROOTUSRSBINLINKS) $(ROOTETCDEFAULTFILES) \
	$(ROOTMANIFEST) $(ROOTSVCMETHOD) THIRDPARTYLICENSE.arp

THIRDPARTYLICENSE.arp: arp.c
	$(SED) -n '/University of California/,/SUCH DAMAGE/p' arp.c > $@

CLOBBERFILES += THIRDPARTYLICENSE.arp

$(SUBDIRS): FRC
	cd $@; pwd; $(MAKE) $(TARGET)

FRC:

check:	$(CHKMANIFEST)

clean: $(SUBDIRS) clean_local
clean_local:
	$(RM) $(CLEANFILES)

clobber: $(SUBDIRS) clobber_local
clobber_local: clean_local
	$(RM) $(CLOBBERFILES)
