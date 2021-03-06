PATH := $(DEVKITPPC)/bin:$(PATH)
PREFIX ?= powerpc-eabi-
CC = $(PREFIX)gcc
AS = $(PREFIX)gcc
CFLAGS = -std=gnu89 -O3 -Wall -nostdinc -fno-builtin -I$(DEVKITPPC)/lib/gcc/powerpc-eabi/6.2.0/include -I$(DEVKITPPC)/powerpc-eabi/include
ASFLAGS = -mregnames -x assembler-with-cpp
LD = $(PREFIX)ld
OBJCOPY = $(PREFIX)objcopy
LDFLAGS=-Ttext 1808000 -L$(DEVKITPPC)/lib/gcc/powerpc-eabi/6.2.0 -L$(DEVKITPPC)/powerpc-eabi/lib -lgcc -lc
OBJDUMP ?= $(PREFIX)objdump
project	:=	.
root	:=	$(CURDIR)
build	:=	 $(root)/bin

FIRMWARE = 550

all: clean setup main

$(CURDIR)/payload/arm_kernel_bin.h:  $(CURDIR)/payload/wupserver_bin.h $(CURDIR)/payload/arm_user_bin.h
	@$(MAKE) --no-print-directory -C $(CURDIR)/arm_kernel -f  $(CURDIR)/arm_kernel/Makefile
	@-mkdir -p $(CURDIR)/payload
	@cp -p $(CURDIR)/arm_kernel/arm_kernel_bin.h $@

$(CURDIR)/payload/wupserver_bin.h:
	@$(MAKE) --no-print-directory -C $(CURDIR)/../../wupserver -f  $(CURDIR)/../../wupserver/Makefile
	@-mkdir -p $(CURDIR)/payload
	@cp -p $(CURDIR)/../../wupserver/wupserver_bin.h $@

$(CURDIR)/payload/arm_user_bin.h:
	@$(MAKE) --no-print-directory -C $(CURDIR)/arm_user -f  $(CURDIR)/arm_user/Makefile
	@-mkdir -p $(CURDIR)/payload
	@cp -p $(CURDIR)/arm_user/arm_user_bin.h $@

setup:
	mkdir -p $(root)/bin/

main: $(CURDIR)/payload/arm_kernel_bin.h
	$(CC) $(CFLAGS) -DVER=$(FIRMWARE) -c $(project)/main.c
	$(AS) $(ASFLAGS) -DVER=$(FIRMWARE) -c $(project)/crt0.S
	cp -r $(root)/*.o $(build)
	rm $(root)/*.o
	$(LD) -o CBHC.elf $(build)/crt0.o `find $(build) -name "*.o" ! -name "crt0.o"` $(LDFLAGS) -Map CBHC.map
	$(OBJCOPY) CBHC.elf -S -O binary ../CBHC.bin

clean:
	rm -rf $(build) payload
	rm -rf CBHC.elf CBHC.map
	$(MAKE) --no-print-directory -C $(CURDIR)/arm_user -f  $(CURDIR)/arm_user/Makefile clean
	@$(MAKE) --no-print-directory -C $(CURDIR)/../../wupserver -f  $(CURDIR)/../../wupserver/Makefile clean
	$(MAKE) --no-print-directory -C $(CURDIR)/arm_kernel -f  $(CURDIR)/arm_kernel/Makefile clean
