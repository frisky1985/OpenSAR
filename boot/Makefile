# Makefile for the OpenRTOS part of OpenOSEK

#output EXE
TARGET = OpenBoot

#Base Directory
BD = ..

OUT = $(BD)/out

#  >>>>> Config <TRUE/FALSE> <<<<<
DEBUG=FALSE
USE_DET=TRUE


#common compilers
AS = gcc
CC = gcc
LD = gcc
AR = ar
RM = rm

#common flags
ASFLGS = 
# share the include file of AUTOSAR
INCDIR = 	-I$(BD)/include 		\
			-I$(BD)/include/generic \
			-I$(BD)/include/sys		\
			-I$(BD)/boot/config		\
			-I$(BD)/boot/inc        \
			-I$(BD)/boot/src/mingw	\
			-I$(BD)/arch/generic/mingw/kernel	\
			-I$(BD)/system/SchM		\
			-I$(BD)/system/kernel/include	\
			-I$(BD)/diagnostic/Dcm

MACROS = 	-DWIN32 			\
			-DAUTOSAR_SIMULATOR	\
			-DHIS_FL			\
			-DOS_USE_APPLICATIONS	\
			-DUSE_CAN				\
			-DUSE_CANTP				\
			-DUSE_CANIF				\
			-DUSE_DCM				\
			-DUSE_FLS				\
			-DUSE_FEE				\
			-DDEBUG_FEE

ifeq ($(DEBUG),TRUE)
MACROS += -DUSE_DEBUG_PRINTF
endif

CCFLGS = -c -g $(MACROS) -std=gnu99 -O2 -Wall $(INCDIR)
LDFLGS = -g $(MACROS) -std=gnu99 -O2 -Wall $(INCDIR) -lwsock32
ARFLGS = rv
RMFLGS = -f	

#common objectives		
dir-y =	$(BD)/boot/src	\
		$(BD)/boot/config	\
		$(BD)/boot/src/mingw	\
		$(BD)/communication/CanIf	\
		$(BD)/communication/CanTp 	\
		$(BD)/communication/PduR 	\
		$(BD)/diagnostic/Dcm		\
		$(BD)/memory/Fee

ifeq ($(USE_DET),TRUE)
INCDIR += -I$(BD)/diagnostic/Det
MACROS += -DUSE_DET	
dir-y  += $(BD)/diagnostic/Det
endif
		
src-y = $(foreach x,$(dir-y),$(wildcard $(addprefix $(x)/*,.c)))
obj-y = $(subst .c,.o,$(src-y))	
# others
obj-y += 	$(BD)/arch/generic/mingw/drivers/Can.o 	\
		$(BD)/arch/generic/mingw/drivers/CanSocket.o \
		$(BD)/arch/generic/mingw/drivers/Mcu.o		\
		$(BD)/arch/generic/mingw/drivers/Fls.o

# common library needed for OpenSAR
lib-y = 

#common rules
%.o:%.c
	@echo compile $<  to $@
	@$(CC) $(CCFLGS) -o $@ $<

.PHONY:all clean gen

gen:
			
all:$(obj-y) Flash.img
	@$(LD) $(BD)/boot/main.c -o $(OUT)/$(TARGET) $(obj-y) $(LDFLGS)
	@echo ">>>>>>>>>>>>>>>>>  BUILD $(TARGET).EXE DONE   <<<<<<<<<<<<<<<<<<<<<<"

Flash.img:
	dd if=/dev/zero of=Flash.img bs=1M count=1
run:Flash.img
	@(start python $(BD)/tools/can/CanBusServer.py --server 8000)  #LS
	@(start $(OUT)/$(TARGET).exe)
	@(start python $(BD)/tools/can/uds.py --port 8998 0x731 0x732)

clean:
	@$(RM) -f $(OUT)/$(TARGET).exe $(obj-y)
	@echo ">>>>>>>>>>>>>>>>>>>> CLEAN $(TARGET) Objs Done <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<"		