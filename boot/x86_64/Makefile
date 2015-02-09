include $(SOURCE_ROOT)/Rules.inc

GCCOPTS=-fno-pic -static -fno-builtin -fno-strict-aliasing -Wall -MD -ggdb -fno-omit-frame-pointer \
        -ffreestanding -fno-common -nostdlib -Iinclude -gdwarf-2 -m64 -DX64 -mcmodel=kernel \
        -mtls-direct-seg-refs -mno-red-zone -O0 -fno-stack-protector -nostdinc -I.

all:
	$(GCC) $(GCCOPTS) -o initcode.o -c initcode64.S
	$(LD) -m elf_x86_64 -nodefaultlibs -N -e start -Ttext 0 -o initcode.out initcode.o
	$(OBJCOPY) -S -O binary initcode.out initcode
	$(GCC) $(GCCOPTS) -o entryother.o -c entryother.S
	$(LD) -m elf_x86_64 -nodefaultlibs -N -e start -Ttext 0x7000 -o bootblockother.o entryother.o
	$(OBJCOPY) -S -O binary -j .text bootblockother.o entryother
	$(GCC) -gdwarf-2 -Wa,-divide -Iinclude -m64 -DX64 -mcmodel=kernel -mtls-direct-seg-refs -mno-red-zone -c -o entry64.o entry64.S
	$(GCC) $(GCCOPTS)  -c -o main.o main.c

clean:
	rm -f initcode.o initcode.out initcode entryother.o entryother bootblockother.o entry64.o main.o kernel.elf *.d

run:
	qemu-system-x86_64 -kernel kernel.elf -vnc :1 -no-reboot -d int -serial stdio
