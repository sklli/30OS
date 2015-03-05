#Makefile for MyOS

#COMMAND USED
TOOLPATH 		= ../z_tools/
INCPATH  		= ../z_tools/haribote/

RULEFILE 		= $(TOOLPATH)haribote/haribote.rul.OSMain
RULEFILE_MAIN	= $(TOOLPATH)haribote/haribote.rul.main
OBJFILES		= funcUtils.obj icon.obj hankaku.obj string.obj graphic.obj bootpac.obj \
							globalTbl.obj interrupt.obj dataStruct.obj mouse_keyboard.obj mem.obj \
							sheet.obj tim.obj multiTask.obj wnd.obj wnd_console.obj cmd.obj api.obj \
							fat.obj

CC					= $(TOOLPATH)cc1.exe -I$(INCPATH) -Os -Wall -quiet
ASSEMBLER		= $(TOOLPATH)nask.exe
EDIMG				= $(TOOLPATH)edimg.exe
IMGTOL			= $(TOOLPATH)imgtol.com
GAS2NASK 		= $(TOOLPATH)gas2nask.exe -a
OBJ2BIM 		= $(TOOLPATH)obj2bim.exe
BIM2HRB 		= $(TOOLPATH)bim2hrb.exe
MAKEFONT 		= $(TOOLPATH)makefont.exe
BIN2OBJ  		= $(TOOLPATH)bin2obj.exe
GOLIB				= $(TOOLPATH)golib00.exe

RM	= rm
CP	= cp
CAT	= cat

#USED BY USER
COPY : IMG
	CP -f tmp.img "/cygdrive/d/Virtual Machines/30OS.img"

IMG : OS.img


#CALLED TARGET
SYS_LST : asmhead.nas ipl.nas
	$(ASSEMBLER) asmhead.nas asmhead.bin asmhead.lst
	$(ASSEMBLER) ipl.nas ipl.bin ipl.lst


OS.img : OS.sys HZK16.fnt SYS_LST crack2.bin hello.bin color.bin read.bin tim.h mem.h readme.txt
	$(EDIMG)   imgin:../z_tools/fdimg0at.tek \
		wbinimg src:ipl.bin len:512 from:0 to:0 \
		copy from:OS.sys to:@: \
		copy from:mem.h to:@: \
		copy from:HZK16.fnt to:@: \
		copy from:crack2.bin to:@: \
		copy from:hello.bin to:@: \
		copy from:color.bin to:@: \
		copy from:read.bin to:@: \
		copy from:tim.h to:@: \
		copy from:readme.txt to:@: \
		imgout:tmp.img

apilib.lib : app_funcUtils.obj app_funcUtils1.obj
	$(GOLIB) app_funcUtils.obj app_funcUtils1.obj out:apilib.lib

crack1.bin : crack1.c apilib.lib
	$(CC) -o crack1.gas crack1.c
	$(GAS2NASK) crack1.gas crack1.nas
	$(ASSEMBLER) crack1.nas crack1.obj crack1.lst
	$(OBJ2BIM) @$(RULEFILE_MAIN) out:crack1.bim map:crack1.map crack1.obj apilib.lib
	$(BIM2HRB) crack1.bim crack1.bin 0
	
crack2.bin : crack2.nas
	$(ASSEMBLER) crack2.nas crack2.obj crack2.lst
	$(OBJ2BIM) @$(RULEFILE_MAIN) out:crack2.bim map:crack2.map crack2.obj
	$(BIM2HRB) crack2.bim crack2.bin 0

hello.bin : hello.c apilib.lib
	$(CC) -o hello.gas hello.c
	$(GAS2NASK) hello.gas hello.nas
	$(ASSEMBLER) hello.nas hello.obj hello.lst
	$(OBJ2BIM) @$(RULEFILE_MAIN) out:hello.bim stack:1k map:hello.map hello.obj apilib.lib
	$(BIM2HRB) hello.bim hello.bin 64k

color.bin : color.c apilib.lib
	$(CC) -o color.gas color.c
	$(GAS2NASK) color.gas color.nas
	$(ASSEMBLER) color.nas color.obj color.lst
	$(OBJ2BIM) @$(RULEFILE_MAIN) out:color.bim stack:512k map:color.map color.obj apilib.lib
	$(BIM2HRB) color.bim color.bin 64k

read.bin : read.c apilib.lib
	$(CC) -o read.gas read.c
	$(GAS2NASK) read.gas read.nas
	$(ASSEMBLER) read.nas read.obj read.lst
	$(OBJ2BIM) @$(RULEFILE_MAIN) out:read.bim stack:1k map:read.map read.obj apilib.lib
	$(BIM2HRB) read.bim read.bin 64k

hlt.bin : hlt.nas
	$(ASSEMBLER) hlt.nas hlt.bin

%.gas	:	%.c
	$(CC) -o $@ $<

%.nas : %.gas
	$(GAS2NASK) $< $@

# font file begin
hankaku.bin : hankaku.txt Makefile
	$(MAKEFONT) hankaku.txt hankaku.bin
hankaku.obj : hankaku.bin Makefile
	$(BIN2OBJ) hankaku.bin hankaku.obj _hankaku
# font file end

%.obj : %.nas
	$(ASSEMBLER) $< $*.obj $*.lst
	
bootpac.hrb : $(OBJFILES) SYS_LST
	$(OBJ2BIM) @$(RULEFILE) out:bootpac.bim stack:3136k map:bootpac.map $(OBJFILES)
	$(BIM2HRB) bootpac.bim bootpac.hrb 0

OS.sys : bootpac.hrb SYS_LST
	$(CAT)  asmhead.bin bootpac.hrb > OS.sys
	

clean :
	RM *.img *.bak *.sys *.lst *.bin *.map *.gas *.obj bootpac.nas *.hrb *.bim