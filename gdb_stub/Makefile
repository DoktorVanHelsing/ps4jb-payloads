all: payload.bin gdbstub.bin gdbstub-int.bin gdbstub-lib.bin gdbstub-lib-redir.bin gdbstub-obj.o

clean:
	rm -f payload.elf payload.bin gdbstub.elf gdbstub.bin gdbstub-int.elf gdbstub-int.bin gdbstub-lib.elf gdbstub-lib.bin gdbstub-lib-redir.elf gdbstub-lib-redir.bin gdbstub-obj.o

../lib/lib.a:
	cd ../lib; make

payload.elf: ../lib/lib.a dbg.c ps4libs.c main.c
	gcc -g -isystem ../freebsd-headers -nostdinc -nostdlib -fno-stack-protector -fPIE -ffreestanding -static ../lib/lib.a -D__PS4__ -DINTERRUPTER_THREAD -DPS4LIBS dbg.c main.c ps4libs.c -o payload.elf

payload.bin: payload.elf
	objcopy payload.elf --only-section .text --only-section .data --only-section .bss --only-section .rodata --set-section-flags .bss=alloc,load,contents -O binary payload.bin
	file payload.bin | fgrep -q 'payload.bin: DOS executable (COM)'

gdbstub.elf: ../lib/lib.a dbg.c _end.c
	gcc -g -isystem ../freebsd-headers -nostdinc -nostdlib -fno-stack-protector -fPIE -ffreestanding -static ../lib/lib.a -D__PS4__ -Ddbg_enter=main -DBLOB dbg.c _end.c -Wl,-gc-sections -o gdbstub.elf

gdbstub.bin: gdbstub.elf
	objcopy gdbstub.elf --only-section .text --only-section .data --only-section .bss --only-section .rodata --set-section-flags .bss=alloc,load,contents -O binary gdbstub.bin
	file gdbstub.bin | fgrep -q 'gdbstub.bin: DOS executable (COM)'

gdbstub-int.elf: ../lib/lib.a dbg.c _end.c
	gcc -g -isystem ../freebsd-headers -nostdinc -nostdlib -fno-stack-protector -fPIE -ffreestanding -static ../lib/lib.a -D__PS4__ -Ddbg_enter=main -DBLOB -DINTERRUPTER_THREAD dbg.c _end.c -Wl,-gc-sections -o gdbstub-int.elf

gdbstub-int.bin: gdbstub-int.elf
	objcopy gdbstub-int.elf --only-section .text --only-section .data --only-section .bss --only-section .rodata --set-section-flags .bss=alloc,load,contents -O binary gdbstub-int.bin
	file gdbstub-int.bin | fgrep -q 'gdbstub-int.bin: DOS executable (COM)'

gdbstub-lib.elf: ../lib/lib.a dbg.c ps4libs.c _end.c
	gcc -g -isystem ../freebsd-headers -nostdinc -nostdlib -fno-stack-protector -fPIE -ffreestanding -static ../lib/lib.a -D__PS4__ -Ddbg_enter=main -DBLOB -DINTERRUPTER_THREAD -DPS4LIBS dbg.c ps4libs.c _end.c -Wl,-gc-sections -o gdbstub-lib.elf

gdbstub-lib.bin: gdbstub-lib.elf
	objcopy gdbstub-lib.elf --only-section .text --only-section .data --only-section .bss --only-section .rodata --set-section-flags .bss=alloc,load,contents -O binary gdbstub-lib.bin
	file gdbstub-lib.bin | fgrep -q 'gdbstub-lib.bin: DOS executable (COM)'

gdbstub-lib-redir.elf: ../lib/lib.a dbg.c ps4libs.c _end.c
	gcc -g -isystem ../freebsd-headers -nostdinc -nostdlib -fno-stack-protector -fPIE -ffreestanding -static ../lib/lib.a -D__PS4__ -Ddbg_enter=main -DBLOB -DINTERRUPTER_THREAD -DPS4LIBS -DSTDIO_REDIRECT dbg.c ps4libs.c _end.c -Wl,-gc-sections -o gdbstub-lib-redir.elf

gdbstub-lib-redir.bin: gdbstub-lib-redir.elf
	objcopy gdbstub-lib-redir.elf --only-section .text --only-section .data --only-section .bss --only-section .rodata --set-section-flags .bss=alloc,load,contents -O binary gdbstub-lib-redir.bin
	file gdbstub-lib-redir.bin | fgrep -q 'gdbstub-lib-redir.bin: DOS executable (COM)'

gdbstub-obj.o: ../lib/lib.a dbg.c ps4libs.c _end.c
	gcc -g -isystem ../freebsd-headers -nostdinc -nostdlib -fno-stack-protector -fPIE -ffreestanding -static ../lib/lib.a -D__PS4__ -Ddbg_enter=main -DOBJECT_FILE -DINTERRUPTER_THREAD -DPS4LIBS -DSTDIO_REDIRECT dbg.c ps4libs.c _end.c -Wl,--entry,_start -Wl,-r -Wl,-gc-sections -o $@
	objcopy --prefix-symbols=gdbstub_ $@
	objcopy --redefine-sym gdbstub__start=dbg_enter --redefine-sym gdbstub__GLOBAL_OFFSET_TABLE_=_GLOBAL_OFFSET_TABLE_ $@
