MAJOR ?= 5
STABLE = $(MAJOR).4
CONFIG ?= $(MAJOR).4.0
default: build

build:
	echo fetch, uroot, kernel, or image.bin

image.bin:  kernel
	utk \
		  -xzPath  /usr/bin/xz \
		ROM.bin \
	        remove Ip.*  \
                remove Tcp.*  \
                remove Usb.*  \
                remove Udp.*  \
                remove Dhcp.*  \
                remove .np.*  \
                remove .tftp.*  \
                remove Http.* \
                remove .*Dns.* \
                remove Arp.* \
                remove .*NetworkStackSetupScreen.*  \
                remove Iscsi.* \
                remove Scsi.* \
                remove Fat.* \
                remove Ahci.* \
                remove Partition.* \
                remove Sata.* \
                remove Disk.* \
                remove Whea.* \
                remove .*Pxe.* \
                remove Ata.* \
		remove AMITSE \
		replace_pe32 FullShell kernel  \
	save image.bin

kernel: uroot bzImage
	cp linux/arch/x86/boot/bzImage kernel

netboot: netbooturoot bzImage
	cp linux/arch/x86/boot/bzImage kernel

readrom:
	echo You need to get a ROM image from *somewhere*
	echo Once you do, put it in ROM.bin

writerom: image.bin
	echo Here is where you would do the flashrom, e.g.
	echo sudo flashrom -p dediprog -w image.bin

netbooturoot:
	u-root -build=bb github.com/u-root/u-root/cmds/core/init github.com/u-root/u-root/cmds/core/ls github.com/u-root/u-root/cmds/core/ip   github.com/u-root/u-root/cmds/core/elvish    github.com/u-root/u-root/cmds/core/sshd    github.com/u-root/u-root/cmds/core/scp github.com/u-root/u-root/cmds/boot/*
	cp /tmp/initramfs.linux_amd64.cpio .
	lzma -k -f initramfs.linux_amd64.cpio
	ls -l initramfs*
	cp *lzma linux

uroot:
	u-root -build=bb github.com/u-root/u-root/cmds/core/init github.com/u-root/u-root/cmds/exp/rush github.com/u-root/u-root/cmds/core/ls github.com/u-root/u-root/cmds/core/ip   github.com/u-root/u-root/cmds/core/elvish    github.com/u-root/u-root/cmds/core/sshd    github.com/u-root/u-root/cmds/core/scp
	cp /tmp/initramfs.linux_amd64.cpio .
	lzma -k -f initramfs.linux_amd64.cpio
	ls -l initramfs*
	cp *lzma linux

bzImage:
	cp config-$(CONFIG) linux/.config
	(cd linux && make oldconfig && make -j32)

fetch: getkernel geturoot getfiano getrom
getkernel:
	rm -rf linux
	wget -O kernel.xz https://cdn.kernel.org/pub/linux/kernel/v$(MAJOR).x/linux-$(STABLE).tar.xz
	xzcat kernel.xz | tar x
	mv linux-$(STABLE) linux
getfiano:
	go get -u github.com/linuxboot/fiano/cmds/utk
	go install github.com/linuxboot/fiano/cmds/utk
getrom:
	@echo If you plan to flash to hardware,
	@echo you will need to get a rom imagei
	@echo and put it in rom.bin
	@echo you can get it via wget, flashrom, whatever.
	@echo If you are not targeting hardware, you can
	@echo make kernel
	@echo and run that kernel via qemu

geturoot:
	go get -u github.com/u-root/u-root

