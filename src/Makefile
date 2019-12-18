CC=gcc
LD=gcc

ifdef DEBUG
CFLAGS += -g -DDEBUG=$(DEBUG) -fstack-protector-all
DEBUG_OBJ=mallocv.o
else
LDFLAGS += -s
endif

CFLAGS += -Wall -Wextra
override CFLAGS+= -D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64 -D_LARGEFILE64_SOURCE

INSTALL_FLAGS=-m 0755 -p -D

UNAME_S := $(shell uname -s)
UNAME_P := $(shell uname -p)
UNAME_O := $(shell uname -o)

EXECUTABLE:=fatsort

ifeq ($(UNAME_O),Cygwin)
	_CYGWIN=1
endif

ifeq ($(OS),Windows_NT)
	ifndef _CYGWIN
		override CCFLAGS += -D __WIN32__ -mno-ms-bitfields
	else
		override CFLAGS += -D __CYGWIN__
		override CFLAGS += -D __LINUX__
		override LDFLAGS += -liconv
		SBINDIR=/usr/local/sbin
	endif
else
	ifdef MINGW
		CC=i686-w64-mingw32-gcc
		#LD=i686-w64-mingw32-gcc
		EXECUTABLE:=${EXECUTABLE}.exe
		override LDFLAGS += -liconv -static
		override CFLAGS += -D __WIN32__ -D __MINGW__
	else

		ifeq ($(UNAME_S),Linux)
			override CFLAGS += -D __LINUX__
		endif
		ifeq ($(UNAME_S),FreeBSD)
			override CFLAGS += -D __BSD__
		endif
		ifeq ($(UNAME_S),OpenBSD)
			override CFLAGS += -D __BSD__
		endif
		ifeq ($(UNAME_S),NetBSD)
			override CFLAGS += -D __BSD__
		endif
		ifeq ($(UNAME_S),Darwin)
			# Detect Mac OS X ($OSTYPE = darwin9.0 for Mac OS X 10.5 and darwin10.0 for Mac OS X 10.6)
			override CFLAGS += -D __OSX__
			override LDFLAGS += -liconv
			# OS X's install does not support the '-D' flag.
			INSTALL_FLAGS=-m 0755 -p
			# Mac OS X does not have a "/usr/local/sbin"
			SBINDIR=/usr/local/bin
		else
			SBINDIR=/usr/local/sbin
		endif
	endif
endif

OBJ=fatsort.o FAT_fs.o deviceio.o endianness.o sig.o entrylist.o errors.o options.o clusterchain.o sort.o misc.o natstrcmp.o stringlist.o regexlist.o

all: ${EXECUTABLE}

${EXECUTABLE}: $(OBJ) $(DEBUG_OBJ) Makefile
	${CC} $(OBJ) $(DEBUG_OBJ) -o $@ ${LDFLAGS}

fatsort.o: fatsort.c endianness.h sig.h FAT_fs.h  options.h \
 stringlist.h errors.h sort.h clusterchain.h misc.h mallocv.h Makefile
	$(CC) ${CFLAGS} -c $< -o $@

FAT_fs.o: FAT_fs.c FAT_fs.h  errors.h endianness.h deviceio.h \
 mallocv.h Makefile
	$(CC) ${CFLAGS} -c $< -o $@

endianness.o: endianness.c endianness.h mallocv.h Makefile
	$(CC) ${CFLAGS} -c $< -o $@

sig.o: sig.c sig.h mallocv.h Makefile
	$(CC) ${CFLAGS} -c $< -o $@

entrylist.o: entrylist.c entrylist.h FAT_fs.h  options.h \
 stringlist.h errors.h natstrcmp.h mallocv.h endianness.h Makefile
	$(CC) ${CFLAGS} -c $< -o $@

errors.o: errors.c errors.h mallocv.h Makefile
	$(CC) ${CFLAGS} -c $< -o $@

options.o: options.c options.h  FAT_fs.h stringlist.h regexlist.h errors.h \
 mallocv.h Makefile
	$(CC) ${CFLAGS} -c $< -o $@

clusterchain.o: clusterchain.c clusterchain.h  errors.h \
 mallocv.h Makefile
	$(CC) ${CFLAGS} -c $< -o $@

sort.o: sort.c sort.h FAT_fs.h  clusterchain.h entrylist.h \
 errors.h options.h stringlist.h regexlist.h endianness.h sig.h misc.h deviceio.h \
 mallocv.h Makefile
	$(CC) ${CFLAGS} -c $< -o $@

misc.o: misc.c misc.h options.h  FAT_fs.h stringlist.h \
 mallocv.h Makefile
	$(CC) ${CFLAGS} -c $< -o $@

natstrcmp.o: natstrcmp.c natstrcmp.h mallocv.h Makefile
	$(CC) ${CFLAGS} -c $< -o $@

stringlist.o: stringlist.c stringlist.h  FAT_fs.h errors.h \
 mallocv.h Makefile
	$(CC) ${CFLAGS} -c $< -o $@

regexlist.o: regexlist.c regexlist.h  FAT_fs.h errors.h \
 mallocv.h Makefile
	$(CC) ${CFLAGS} -c $< -o $@

deviceio.o: deviceio.c deviceio.h  \
	 mallocv.h Makefile
		$(CC) ${CFLAGS} -c $< -o $@

mallocv.o: mallocv.c mallocv.h errors.h
	$(CC) ${CFLAGS} -c $< -o $@

install:
	install $(INSTALL_FLAGS) ${EXECUTABLE} $(DESTDIR)$(SBINDIR)/${EXECUTABLE}

clean:
	rm -f *.o ${EXECUTABLE}

.PHONY: all clean
