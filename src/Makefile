# NOTE: this makefile makes use of 'ifdef'/'endif' and 'ifndef'/'endif'
# constructs which are supported by gnu Make.  If you don't have GNU make,
# and cannot install it or get it installed, you will have to hand modify
# all Makefiles (this one and the ones in the modules directories) to
# remove those constructs and set the values manually the way you believe
# they should be set.  All of them are conditional on options set in this
# file, so it should be relatively easy to determine which branch is correct

BINDIR = /home/ecartis
MODDIR = /home/ecartis/modules

# First things first we need to set up some various options since OS's aren't
# as nice about things are they could be.

# Uncomment this if you are on a SunOS v5/Solaris system
#
#SUNOS_5 = 1

# Uncomment this if you are on a DEC->Compaq->HP Tru64 v5.1A system
#
#DEC_UNIX=-DDEC_UNIX

# Uncomment this instead if you are on a DEC->Compaq->HP Tru64 system 
# before 5.1A, this provides legacy support for older Tru64 systems that 
# have more esoteric usleep statements.
#
#DEC_UNIX=-DDEC_UNIX -DDEC_UNIX_USLEEP

# Uncomment this is you're unlucky enough to run Irix
#
#IRIX=-DIRIX_IS_CRAP

# Uncomment this if you are on a BSD system AND you want dynamic modules
# SunOS (4 and 5 both) also needs this setting.
#
BSDMOD = 1

# Uncomment this if you are compiling on OpenBSD with modules (BSDMOD = 1)
#OBSDMOD = 1

# Uncomment this if you are compiling on BSDI with modules (*comment* BSDMOD)
#BSDIMOD=1

# Uncomment this if you plan on compiling with dynamic modules.  Dynamic
# modules are currently only suported on very few operating systems.  If 
# you get link errors, do a 'make pristine', change this setting (and the
# BSDMOD one above) and recompile.
#
# DYNMOD does work under SunOS 4 and 5 now.
# Dynamic modules compile under Digital Unix, but are untested.
#
DYNMOD=1


# Uncomment this if you have the GNU strftime() function.  This version
# of the function understands %z to return the numerical offset.  It's better
# to use that if it's available for RFC822 date headers
GNU_STRFTIME=-DGNU_STRFTIME

# Uncomment this if you are on a machine that doesn't have strerror()
#
#NEED_STRERROR=-DNEED_STRERROR

# Uncomment this if you do NOT have the snprintf() function.   If this is
# uncommented the code will use an internal implmentation of snprintf()
# which might not be quite as robust.  Solaris <= 2.5.1 REQUIRES to be
# uncommented, as do Digital Unix and the abysmal Irix 5.3
# This will never need to be passed to the modules as we wrap either the
# system snprintf or this version inside an API call
#NEED_SNPRINTF=-DNEED_SNPRINTF

# Uncomment this if you do NOT have the flock() function.  If this is
# uncommented, the code will use an internal implementation of flock()
# based on the fcntl() calls.   Solaris REQUIRES this to be uncommented,
# as does Digital Unix.
# This should never need to be passed to the modules since the modules
# should use the wrapper open_file() calls.
#NEED_FLOCK=-DNEED_FLOCK

# Uncomment the following if your ecartis data directory is NFS mounted.
# On most systems, flock()/fcntl() do not work over NFS.  This implements
# a 'hitchingpost' style lock which should work, but limits the number of
# simultaneous accesses you may have.
#USE_HITCHING_LOCK=-DUSE_HITCHING_LOCK

# Some systems (apparently SunOs 4.1.4) has memset, but not memmove.
# This tells Ecartis to use bcopy() instead.
#NO_MEMMOVE=-DNO_MEMMOVE

# Apparently some systems do not have the tm_gmtoffset member in struct
# tm.  If this is the case, define this and we won't try to use the %z
# in strftime.
#NO_TM_GMTOFF=-DNO_TM_GMTOFF

# Let's try to use the tzname info if they have it and don't have the
# tm_gmtoff bits above
#HAVE_TZNAME=-DHAVE_TZNAME

# If we have the timezone() function, we can try that as well
#HAVE_TIMEZONE=-DHAVE_TIMEZONE

# If we don't have the strchr function then define this
#NEED_STRCHR=-DNEED_STRCHR

# If we don't have the strchr function then define this
#NEED_STRRCHR=-DNEED_STRRCHR

# If you are compiling on some BSDI (and who knows if anywhere else),
# the structure %+.02d in a printf format string will not do what
# any sane person would expect, and instead treats the sign byte as one
# of the two characters in the length of the result :(  Define this if
# the date field shows up as -400 instead of -0400 (or whatever your
# offset from GMT is)
#MY_PRINTF_IS_BRAINDEAD=-DMY_PRINTF_IS_BRAINDEAD

# Set up the compiler.  We *HIGHLY* recommend GCC.
# If fact, we can't promise it'll work with anything else.
# It almost certainly won't with the K&R cc that comes standard with
# SunOS.
ifdef BSDIMOD
CC=shlicc
else
CC=gcc
endif

# Warning-level flags.  Under SunOS (4 and 5), this NEEDS TO BE BLANK.
# Only use -Wall -Werror if you are on an ANSI system; K&R systems such
# as SunOS will not compile if you use these flags.
#
# It appears that IRIX is also rather stupid, and may require this
# to be removed.
WFLAGS=-Wall -Werror
# Uncomment this to build with gdb symbols (comment above line)
#WFLAGS=-ggdb -Wall -Werror

# Now set up the initial command line.  SUNOS_5 makes this a bit wierd.
ifndef SUNOS_5
CFLAGS=-I./inc ${GNU_STRFTIME} ${NEED_SNPRINTF} ${NEED_FLOCK} ${DEC_UNIX} ${IRIX} ${USE_HITCHING_LOCK} ${NO_MEMMOVE} ${NEED_STRERROR} ${NO_TM_GMTOFF} ${HAVE_TZNAME} ${HAVE_TIMEZONE} ${NEED_STRCHR} ${NEED_STRRCHR} ${MY_PRINTF_IS_BRAINDEAD} ${DETECT_BROKEN_HOSTNAME}
else
CFLAGS=-I./inc -DSUNOS_5 ${GNU_STRFTIME} ${NEED_SNPRINTF} ${NEED_FLOCK} ${USE_HITCHING_LOCK} ${NO_MEMMOVE} ${NEED_STRERROR} ${NO_TM_GMTOFF} ${HAVE_TZNAME} ${HAVE_TIMEZONE} ${NEED_STRCHR} ${NEED_STRRCHR} ${MY_PRINTF_IS_BRAINDEAD}
endif

# Now set up the initial library path.  Once again, SUNOS_5 makes this
# wierd.
ifndef SUNOS_5
LFLAGS=
else
LFLAGS=
endif

# Now set up the libraries that are needed to compile.  SUNOS_5 strikes
# again.  If you are not using encrypted site passwords, you can remove
# -lcrypt
ifndef SUNOS_5
ifndef BSDIMOD
ifndef OBSDMOD
LIBS=-lcrypt
else
LIBS=
endif
else
LIBS=
endif
else
LIBS=-lsocket -lnsl -lcrypt
endif


# OK, modules.  We need to have some or all of these enabled, even if 
# we aren't using dynamic modules.  I'm aware that we also need a small
# amount of informative blurb on each.

LS_BOUNCER=bouncer
LS_LISTARCHIVE=listarchive
LS_FILEARCHIVE=filearchive
LS_ADMIN=admin
LS_ACCTMGR=acctmgr
LS_STAT=stat
LS_SEND=send
LS_BASE=base
LS_DIGEST=digest
LS_ANTISPAM=antispam
LS_ADMINISTRIVIA=administrivia
LS_TOOLBOX=toolbox
LS_PANTOMIME=pantomime
LS_LISTARGATE=lsg2
LS_PASSWORD=password
LS_PERUSER=peruser

MODULES=${LS_BOUNCER} ${LS_LISTARCHIVE} ${LS_FILEARCHIVE} ${LS_ADMIN} \
${LS_ACCTMGR} ${LS_STAT} ${LS_SEND} ${LS_BASE} ${LS_DIGEST} \
${LS_ANTISPAM} ${LS_ADMINISTRIVIA} ${LS_TOOLBOX} ${LS_PANTOMIME} \
${LS_LISTARGATE} ${LS_PASSWORD} ${LS_PERUSER}



#------------------------------------------------------------------------
# You shouldn't need to change anything below this line
#------------------------------------------------------------------------

# Set up some extra flags based on DYNMOD
ifdef BSDMOD
ifdef OBSDMOD
EXTRA_CFLAGS= -DDYNMOD -DBSDMOD -DOBSDMOD
else
EXTRA_CFLAGS= -DDYNMOD -DBSDMOD
endif
EXTRA_LIBS=
EXTRA_LFLAGS= 
else
ifdef BSDIMOD
EXTRA_CFLAGS= -DDYNMOD -DBSDIMOD
else
EXTRA_CFLAGS= -DDYNMOD
endif
EXTRA_LIBS= -ldl
ifndef BSDIMOD
EXTRA_LFLAGS= -rdynamic
endif
endif

ifdef SUNOS_5
EXTRA_LIBS= -ldl
endif

ifndef DYNMOD
ifdef LS_BOUNCER
BOUNCER=-DBOUNCER
endif
ifdef LS_LISTARCHIVE
LISTARCHIVE=-DLISTARCHIVE
endif
ifdef LS_FILEARCHIVE
FILEARCHIVE=-DFILEARCHIVE
endif
ifdef LS_ADMIN
ADMIN=-DADMIN
endif
ifdef LS_ACCTMGR
ACCTMGR=-DACCTMGR
endif
ifdef LS_STAT
STAT=-DSTAT
endif
ifdef LS_SEND
SEND=-DSEND
endif
ifdef LS_BASE
BASE=-DBASE
endif
ifdef LS_DIGEST
DIGEST=-DDIGEST
endif
ifdef LS_ANTISPAM
ANTISPAM=-DANTISPAM
endif
ifdef LS_ADMINISTRIVIA
ADMINISTRIVIA=-DADMINISTRIVIA
endif
ifdef LS_TOOLBOX
TOOLBOX=-DTOOLBOX
endif
ifdef LS_PANTOMIME
PANTOMIME=-DPANTOMIME
endif
ifdef LS_LISTARGATE
LISTARGATE=-DLISTARGATE
endif
ifdef LS_PASSWORD
PASSWORD=-DPASSWORD
endif
ifdef LS_PERUSER
PERUSER=-DPERUSER
endif
endif


ifdef DYNMOD
MODULE_OBJ= dynmod.o
else
EXTRA_CFLAGS=
EXTRA_CDEFS=${BOUNCER} ${LISTARCHIVE} ${FILEARCHIVE} ${ADMIN} ${ACCTMGR} \
            ${STAT} ${SEND} ${BASE} ${DIGEST} ${ANTISPAM} ${ADMINISTRIVIA} \
            ${TOOLBOX} ${PANTOMIME} ${LISTARGATE} ${PASSWORD} ${PERUSER}
EXTRA_LIBS= $(patsubst %,-l%,$(MODULES))
EXTRA_LFLAGS= -L.
MODULE_OBJ= staticmod.o
endif

all: targets

OBJS=alias.o command.o user.o parse.o list.o core.o forms.o smtp.o io.o \
     regerror.o regsub.o regexp.o flag.o cookie.o file.o module.o \
     fileapi.o variables.o internal.o cmdarg.o modes.o ${MODULE_OBJ} \
     unmime.o codes.o hooks.o tolist.o mystring.o lma.o userstat.o \
     snprintf.o moderate.o mysignal.o unhtml.o liscript.o submodes.o \
     lcgi.o upgrade.o passwd.o funcparse.o trust.o rfc2047.o

targets: make_modules ecartis

ecartis: ${OBJS}
	${CC} ${EXTRA_LFLAGS} ${LFLAGS} -o ecartis ${OBJS} ${EXTRA_LIBS} ${LIBS}
	cp ecartis build/ecartis
	chmod ug+s build/ecartis

make_modules:
	@for dir in ${MODULES}; \
		do \
		  if (${MAKE} -C modules/$${dir} \
			DYNMOD=${DYNMOD} BSDMOD=${BSDMOD} \
			OBSDMOD=${OBSDMOD} BSDIMOD=${BSDIMOD} \
			GNU_STRFTIME=${GNU_STRFTIME} SUNOS_5=${SUNOS_5} \
                        NEED_STRCHR=${NEED_STRCHR} \
                        NEED_STRRCHR=${NEED_STRRCHR} \
			WFLAGS="${WFLAGS}" CC=${CC}) then \
			echo \[Build: $${dir}\] Built module successfully. ;\
			else exit; fi; \
		[ ! -x build/$${dir}.lpm ] || \
			chmod a-x build/$${dir}.lpm; \
	done

install: targets
	cp build/ecartis ${BINDIR}
	chmod ug+s ${BINDIR}/ecartis
ifdef DYNMOD
	@for dir in ${MODULES}; do \
		cp build/$${dir}.lpm ${MODDIR}/$${dir}.lpm; \
		echo "Installing module $${dir}..."; \
	done;
endif

clean: clean_modules
	rm -f ${OBJS} ecartis

update_build:
	-chmod +x ./mkbuild.sh
	./mkbuild.sh

checkin: update_build
	(cd ..; cvs commit)

refresh:
	cvs update -Pd

pristine: pristine_modules
	rm -f build/ecartis ${OBJS} ecartis

clean_modules:
	@for dir in ${MODULES}; do ${MAKE} -C modules/$${dir} DYNMOD=${DYNMOD} BSDMOD=${BSDMOD} BSDIMOD=${BSDIMOD} SUNOS_5=${SUNOS_5} GNU_STRFTIME=${GNU_STRFTIME} WFLAGS="${WFLAGS}" CC=${CC} clean; done

pristine_modules:
	@for dir in ${MODULES}; do ${MAKE} -C modules/$${dir} DYNMOD=${DYNMOD} BSDMOD=${BSDMOD} BSDIMOD=${BSDIMOD} SUNOS_5=${SUNOS_5} GNU_STRFTIME=${GNU_STRFTIME} WFLAGS="${WFLAGS}" CC=${CC} pristine; done

.c.o:
	${CC} ${WFLAGS} ${CFLAGS} ${EXTRA_CDEFS} ${EXTRA_CFLAGS} -c $*.c
