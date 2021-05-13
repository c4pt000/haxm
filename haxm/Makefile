# SPDX-License-Identifier: GPL-2.0
#ccflags-y += -std=gnu99 -Wno-unused-function -Wno-declaration-after-statement
KVER ?= $(shell uname -r)
KDIR ?= /lib/modules/$(KVER)/build



MACHINE := $(shell uname -m)
ifeq ($(MACHINE),x86_64)
   NASM_OPTS := -f elf64
else
   NASM_OPTS := -f elf32
endif

HAXM_DIR     := .
HAXM_ASM_SRC := $(wildcard $(HAXM_DIR)/*.asm)
HAXM_ASM_OBJ := $(patsubst $(HAXM_DIR)/%.asm, $(HAXM_DIR)/%.o, $(HAXM_ASM_SRC))
HAXM_ASM_CMD := $(patsubst $(HAXM_DIR)/%.asm, $(HAXM_DIR)/.%.o.cmd, $(HAXM_ASM_SRC))
HAXM_C_SRC   := $(wildcard $(HAXM_DIR)/*.c)
HAXM_C_OBJ   := $(patsubst $(HAXM_DIR)/%.c, $(HAXM_DIR)/%.o, $(HAXM_C_SRC))
HAXM_C_CMD   := $(patsubst $(HAXM_DIR)/%.c, $(HAXM_DIR)/.%.o.cmd, $(HAXM_C_SRC))

.PHONY: all modules modules_install install uninstall clean

%.o: %.asm
	$(eval CMD := $(join $(dir $@), .$(notdir $@).cmd))
	nasm $(NASM_OPTS) -o $@ $^
	echo "" > $(CMD)

all: modules

modules: $(HAXM_ASM_OBJ)
	$(MAKE) -C $(KDIR) M=$$PWD modules

modules_install:
	$(MAKE) -C $(KDIR) M=$$PWD modules_install

install: modules_install
	./haxm-install.sh

uninstall:
	./haxm-uninstall.sh

clean:
	$(MAKE) -C $(KDIR) M=$$PWD clean
	rm -f $(HAXM_ASM_OBJ)
	rm -f $(HAXM_ASM_CMD)
	rm -f $(HAXM_C_OBJ)
	rm -f $(HAXM_C_CMD)

#ccflags-y += -std=gnu99 


#haxm-y 		+= chunk.o cpu.o cpuid.o dump.o emulate_ops.o emulate.o \
#			ept.o ept2.o ept_tree.o gpa_space.o hax.o \
#			ia32.o ia32_ops.o intr_exc.o memory.o memslot.o name.o page_walker.o \
#			ramblock.o vcpu.o vm.o vmx.o vmx_ops.o \
#			vtlb.o components.o hax_entry.o hax_event.o hax_host_mem.o hax_mem_alloc.o \
#			hax_mm.o hax_wrapper.o

obj-$(CONFIG_HAXM_CPU)  += haxm.o
