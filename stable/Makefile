# Stable Makefile for Dynamips
# Copyright (c) 2005-2006 Christophe Fillot.
# Copyright (c) 2013 Daniel Lintott.

# Get include files from the current directory and from the common directory
INCLUDE+=-I. -I../common

VERSION=$(VERSION_TRAIN)$(VERSION_SUB)
VERSION_DEV=$(VERSION_TRAIN)-$(shell date +%Y%m%d-%H)

# If make cannot find a file, try the common directory
VPATH = ../common

CC?=gcc
RM=rm
TAR=tar
CP=cp
MIPS64_ARCH_INC_FILE=\"mips64_$(DYNAMIPS_ARCH)_trans.h\"
PPC32_ARCH_INC_FILE=\"ppc32_$(DYNAMIPS_ARCH)_trans.h\"
OSNAME=unknown

CFLAGS+= $(INCLUDE) -Wall -O2 -fomit-frame-pointer \
	-DJIT_ARCH=\"$(DYNAMIPS_ARCH)\" -DJIT_CPU=CPU_$(DYNAMIPS_ARCH) \
	-DMIPS64_ARCH_INC_FILE=$(MIPS64_ARCH_INC_FILE) \
	-DPPC32_ARCH_INC_FILE=$(PPC32_ARCH_INC_FILE) \
	-DDYNAMIPS_VERSION=\"$(VERSION)\" \
	-D_LARGEFILE_SOURCE -D_LARGEFILE64_SOURCE \
	-DHAS_RFC2553=$(HAS_RFC2553) \
	-DHAS_POSIX_MEMALIGN=$(HAS_POSIX_MEMALIGN)

PCAP_LIB?=-lpcap

ifeq ($(shell uname), FreeBSD)
   PTHREAD_LIBS?=-pthread
   LOCALBASE?=/usr/local
   OSNAME=FreeBSD
   CFLAGS+=-I$(LOCALBASE)/include -I$(LOCALBASE)/include/libelf \
	-D_FILE_OFFSET_BITS=64
   LIBS=-L$(LOCALBASE)/lib -L. -lelf $(PTHREAD_LIBS) $(LDFLAGS)
else
ifeq ($(shell uname), Linux)
   PTHREAD_LIBS?=-lpthread
   OSNAME=Linux
   CFLAGS+=-I/usr/include -I. -D_FILE_OFFSET_BITS=64
   LIBS=-L/usr/lib -L. -ldl -lrt -luuid -lelf $(PTHREAD_LIBS) $(LDFLAGS)
else
ifeq ($(shell uname), GNU/kFreeBSD)
   PTHREAD_LIBS?=-lpthread
   OSNAME=GNU/kFreeBSD
   CFLAGS+=-I/usr/include -I. -D_FILE_OFFSET_BITS=64
   LIBS=-L/usr/lib -L. -ldl -lrt -luuid -lelf $(PTHREAD_LIBS) $(LDFLAGS)
   DESTDIR=/usr
else
ifeq ($(shell uname -s), Darwin)
   OSNAME=Darwin
   CFLAGS+=-I/usr/local/include -I/opt/local/include -mdynamic-no-pic -D_FILE_OFFSET_BITS=64
   LIBS=-L/usr/local/lib -L/opt/local/lib -L. -ldl -lelf -lpthread
ifeq ($(DYNAMIPS_ARCH),amd64)
   CFLAGS+= -DMAC64HACK
endif
else
ifeq ($(shell uname -s), SunOS)
   OSNAME=SunOS
   CFLAGS+=-I/usr/local/include -DINADDR_NONE=0xFFFFFFFF \
	-I /opt/csw/include -DSUNOS
   LIBS=-L/usr/local/lib -L. -ldl -lelf -lpthread -L/opt/csw/lib \
	-lsocket -lnsl -lresolv
   PCAP_LIB=/opt/csw/lib/libpcap.a
else
ifeq ($(shell uname -o), Cygwin)
   OSNAME=Windows
   CFLAGS+=-I/usr/local/include -I/usr/local/include/libelf -I/usr/include -I/usr/include/libelf \
	-I/usr/include/winpcap -DCYGWIN -static -static-libgcc
   LIBS=-L/usr/local/lib -L/usr/lib -L. -lintl -lelf -lpthread -luuid
   PCAP_LIB=-lpacket -lwpcap
else
   CFLAGS+=-I/usr/include/libelf -D_FILE_OFFSET_BITS=64
   LIBS=-L. -ldl /usr/lib/libelf.a -lpthread
endif
endif
endif
endif
endif
endif

CFLAGS+=-DOSNAME=$(OSNAME)

PROG=dynamips$(BIN_EXT)

# Header and source files
HDR=mempool.h registry.h rbtree.h hash.h utils.h parser.h plugin.h \
	gen_uuid.h crc.h sbox.h base64.h \
	net.h net_io.h net_io_bridge.h net_io_filter.h \
	atm.h atm_vsar.h atm_bridge.h frame_relay.h eth_switch.h \
	ptask.h timer.h dev_vtty.h hypervisor.h dynamips.h insn_lookup.h \
	vm.h cpu.h jit_op.h memory.h device.h \
	mips64.h mips64_mem.h mips64_exec.h mips64_jit.h mips64_cp0.h \
	ppc32.h ppc32_mem.h ppc32_exec.h ppc32_jit.h ppc32_vmtest.h \
	nmc93cX6.h cisco_eeprom.h cisco_card.h dev_ds1620.h dev_rom.h \
	pci_dev.h pci_io.h dev_mpc860.h dev_gt.h dev_mv64460.h dev_plx.h \
	dev_dec21140.h dev_am79c971.h dev_i8254x.h dev_i8255x.h \
	dev_mueslix.h dev_nm_16esw.h dev_wic_serial.h \
	dev_c7200.h dev_c7200_mpfpga.h \
	dev_c3600.h dev_c3600_iofpga.h dev_c3600_bay.h \
	dev_c2691.h dev_c2691_iofpga.h \
	dev_c3725.h dev_c3725_iofpga.h \
	dev_c3745.h dev_c3745_iofpga.h \
	dev_c2600.h dev_c2600_iofpga.h \
	dev_c1700.h dev_c1700_iofpga.h \
	dev_c6msfc1.h dev_c6msfc1_mpfpga.h \
	dev_c6sup1.h dev_c6sup1_mpfpga.h \
	rommon_var.h get_cpu_time.h fs_nvram.h

SOURCES=mempool.c registry.c rbtree.c hash.c sbox.c utils.c parser.c \
	gen_uuid.c plugin.c ptask.c timer.c crc.c base64.c \
	net.c net_io.c net_io_bridge.c net_io_filter.c \
	atm.c atm_vsar.c atm_bridge.c frame_relay.c eth_switch.c \
	dynamips.c insn_lookup.c vm.c cpu.c jit_op.c \
	mips64.c mips64_mem.c mips64_cp0.c mips64_jit.c mips64_exec.c \
	ppc32.c ppc32_mem.c ppc32_jit.c ppc32_exec.c ppc32_vmtest.c \
	memory.c device.c nmc93cX6.c cisco_eeprom.c cisco_card.c \
	pci_dev.c pci_io.c \
	dev_zero.c dev_bswap.c dev_vtty.c dev_ram.c dev_rom.c dev_nvram.c \
	dev_bootflash.c dev_flash.c dev_mpc860.c dev_ds1620.c \
	dev_remote.c dev_clpd6729.c dev_pcmcia_disk.c dev_gt.c dev_mv64460.c \
	dev_plx.c dev_dec21x50.c dev_pericom.c dev_ti2050b.c dev_ap1011.c \
	dev_plx6520cb.c dev_ns16552.c \
	dev_dec21140.c dev_am79c971.c dev_i8254x.c dev_i8255x.c \
	dev_mueslix.c dev_wic_serial.c \
	dev_c3600.c dev_c3600_bay.c dev_c3600_iofpga.c \
	dev_c3600_eth.c dev_c3600_serial.c \
	dev_c7200.c dev_c7200_iofpga.c dev_c7200_mpfpga.c \
	dev_c7200_sram.c dev_c7200_eth.c dev_c7200_serial.c dev_c7200_pos.c \
	dev_c7200_bri.c dev_c7200_jcpa.c \
	dev_c2691.c dev_c2691_iofpga.c dev_c2691_eth.c dev_c2691_serial.c \
	dev_c2691_wic.c dev_c2691_pcmod.c \
	dev_c3725.c dev_c3725_iofpga.c dev_c3725_eth.c dev_c3725_serial.c \
	dev_c3725_wic.c dev_c3725_pcmod.c \
	dev_c3745.c dev_c3745_iofpga.c dev_c3745_eth.c dev_c3745_serial.c \
	dev_c3745_wic.c dev_c3745_pcmod.c \
	dev_c2600.c dev_c2600_pci.c dev_c2600_iofpga.c \
	dev_c2600_eth.c dev_c2600_pcmod.c dev_c2600_wic.c \
	dev_c1700.c dev_c1700_iofpga.c dev_c1700_eth.c dev_c1700_wic.c \
	dev_c6msfc1.c dev_c6msfc1_iofpga.c dev_c6msfc1_mpfpga.c \
	dev_c6sup1.c dev_c6sup1_iofpga.c dev_c6sup1_mpfpga.c \
	dev_nm_16esw.c dev_pa_a1.c dev_pa_mc8te1.c \
	dev_sb1.c dev_sb1_io.c dev_sb1_pci.c hypervisor.c \
	hv_nio.c hv_nio_bridge.c \
	hv_frsw.c hv_atmsw.c hv_atm_bridge.c hv_ethsw.c \
	hv_vm.c hv_vm_debug.c hv_store.c \
	hv_c7200.c hv_c3600.c hv_c2691.c hv_c3725.c hv_c3745.c \
	hv_c2600.c hv_c1700.c \
	rommon_var.c get_cpu_time.c fs_nvram.c

# Profiling
#SOURCES += profiler.c
#CFLAGS += -p -DPROFILE -DPROFILE_FILE=\"$(PROG).profile\"

#Debugging symbols (overrides optimization level)
#WITH_DEBUG=2
ifneq ($(WITH_DEBUG),)
CFLAGS+= -O$(WITH_DEBUG) -ggdb -fasynchronous-unwind-tables -fvar-tracking
endif

ifeq ($(DYNAMIPS_ARCH),x86)
HDR += x86-codegen.h mips64_x86_trans.h ppc32_x86_trans.h
SOURCES += mips64_x86_trans.c ppc32_x86_trans.c
CFLAGS += -m32
endif

ifeq ($(DYNAMIPS_ARCH),amd64)
HDR += x86-codegen.h amd64-codegen.h mips64_amd64_trans.h ppc32_amd64_trans.h
SOURCES += mips64_amd64_trans.c ppc32_amd64_trans.c
CFLAGS += -m64
endif

ifeq ($(DYNAMIPS_ARCH),nojit)
HDR += mips64_nojit_trans.h ppc32_nojit_trans.h
SOURCES += mips64_nojit_trans.c ppc32_nojit_trans.c
endif

# RAW Ethernet support for Linux
ifeq ($(shell uname), Linux)
CFLAGS += -DLINUX_ETH
HDR += linux_eth.h
SOURCES += linux_eth.c
endif

# Generic Ethernet support with libpcap (0.9+)
ifeq ($(HAS_PCAP), 1)
CFLAGS += -DGEN_ETH
HDR += gen_eth.h
SOURCES += gen_eth.c

LIBS += $(PCAP_LIB)
endif

C_OBJS=$(SOURCES:.c=.o)

SUPPL=mips_mts.c Makefile ChangeLog COPYING README README.hypervisor TODO \
	dynamips.1 nvram_export.1 hypervisor_mode.7 \
	mips64_microcode ppc32_microcode

FILE_LIST := $(HDR) $(SOURCES) $(SUPPL) \
	x86-codegen.h amd64-codegen.h \
	mips64_x86_trans.c mips64_x86_trans.h \
	mips64_amd64_trans.c mips64_amd64_trans.h \
	mips64_nojit_trans.c mips64_nojit_trans.h \
	ppc32_x86_trans.c ppc32_x86_trans.h \
	ppc32_amd64_trans.c ppc32_amd64_trans.h \
	ppc32_nojit_trans.c ppc32_nojit_trans.h \
	linux_eth.c linux_eth.h gen_eth.c gen_eth.h \
	profiler.c profiler_resolve.pl bin2c.c rom2c.c \
	nvram_export.c udp_send.c udp_recv.c

.PHONY: all
all: $(PROG) nvram_export$(BIN_EXT)

$(PROG): mips64_microcode_dump.inc ppc32_microcode_dump.inc \
	$(C_OBJS)
	@echo "Linking $@"
	@$(CC) $(CFLAGS) -o $@ $(C_OBJS) $(LIBS)

udp_send$(BIN_EXT): udp_send.c net.c crc.c
	@echo "Linking $@"
	@$(CC) -Wall $(CFLAGS) -o $@ udp_send.c net.c crc.c $(LIBS)

udp_recv$(BIN_EXT): udp_recv.c net.c crc.c
	@echo "Linking $@"
	@$(CC) -Wall $(CFLAGS) -o $@ udp_recv.c net.c crc.c $(LIBS)

rom2c$(BIN_EXT): rom2c.c
	@echo "Linking $@"
	@$(CC) -Wall $(CFLAGS) -o $@ $< $(LIBS)

mips64_microcode_dump.inc: rom2c$(BIN_EXT) mips64_microcode
	@./rom2c$(BIN_EXT) mips64_microcode mips64_microcode_dump.inc 0xbfc00000

ppc32_microcode_dump.inc: rom2c$(BIN_EXT) ppc32_microcode
	@./rom2c$(BIN_EXT) ppc32_microcode ppc32_microcode_dump.inc 0xfff00000

# explicit dependencies needed for parallel jobs
dev_rom.c: mips64_microcode_dump.inc ppc32_microcode_dump.inc

nvram_export$(BIN_EXT): nvram_export.o fs_nvram.o
	@echo "Linking $@"
	@$(CC) -Wall $(CFLAGS) $(INCLUDE) $(LDFLAGS) -o $@ nvram_export.o fs_nvram.o

.PHONY: clean
clean:
	$(RM) -f rom2c$(BIN_EXT) microcode_dump.inc \
	$(C_OBJS) $(PROG) nvram_export$(BIN_EXT) nvram_export.o \
	mips64_microcode_dump.inc ppc32_microcode_dump.inc
	$(RM) -f *~

.SUFFIXES: .c .o

.c.o:
	@echo "Compiling $<"
	@$(CC) $(CPPFLAGS) $(CFLAGS) $(INCLUDE) -c -o $*.o $<

