###############################################
PROJECT  = bpe
SRC_PATH = ../../src
DST_PATH = ../../bin
CC       = gcc
LD       = gcc
IDIR     = $(SRC_PATH)
OBJDIR   = $(DST_PATH)/objs
AFLAGS   = -m64
CFLAGS   = $(AFLAGS) -c -W -Wno-deprecated -Wfloat-equal -O2 -DUNIX -D_GNU_SOURCE -I $(IDIR)
LDFLAGS  = $(AFLAGS)

###############################################
OBJS     = AC_BitPlaneCoding.o StagesCodingGaggles.o header.o AdjustOutput.o bitsIO.o lifting_97M.o \
           BPEBlockCoding.o bpe_decoder.o lifting_97f.o CoeffGroup.o bpe_encoder.o main.o DC_EnDeCoding.o \
           errorhandle.o ricecoding.o PatternCoding.o getopt.o waveletbpe.o

###############################################
all: $(PROJECT); rm -f $(OBJS)

###############################################
$(PROJECT) : $(OBJS)
	-mkdir -p $(DST_PATH)
	$(LD) $(LDFLAGS) $(OBJS) -o $(DST_PATH)/$(PROJECT)

###############################################
AC_BitPlaneCoding.o : $(SRC_PATH)/AC_BitPlaneCoding.c
	$(CC) $(CFLAGS) $(SRC_PATH)/AC_BitPlaneCoding.c

StagesCodingGaggles.o : $(SRC_PATH)/StagesCodingGaggles.c
	$(CC) $(CFLAGS) $(SRC_PATH)/StagesCodingGaggles.c

header.o : $(SRC_PATH)/header.c
	$(CC) $(CFLAGS) $(SRC_PATH)/header.c

AdjustOutput.o : $(SRC_PATH)/AdjustOutput.c
	$(CC) $(CFLAGS) $(SRC_PATH)/AdjustOutput.c

bitsIO.o : $(SRC_PATH)/bitsIO.c
	$(CC) $(CFLAGS) $(SRC_PATH)/bitsIO.c

lifting_97M.o : $(SRC_PATH)/lifting_97M.c
	$(CC) $(CFLAGS) $(SRC_PATH)/lifting_97M.c

BPEBlockCoding.o : $(SRC_PATH)/BPEBlockCoding.c
	$(CC) $(CFLAGS) $(SRC_PATH)/BPEBlockCoding.c

bpe_decoder.o : $(SRC_PATH)/bpe_decoder.c
	$(CC) $(CFLAGS) $(SRC_PATH)/bpe_decoder.c

lifting_97f.o : $(SRC_PATH)/lifting_97f.c
	$(CC) $(CFLAGS) $(SRC_PATH)/lifting_97f.c

CoeffGroup.o : $(SRC_PATH)/CoeffGroup.c
	$(CC) $(CFLAGS) $(SRC_PATH)/CoeffGroup.c

bpe_encoder.o : $(SRC_PATH)/bpe_encoder.c
	$(CC) $(CFLAGS) $(SRC_PATH)/bpe_encoder.c

main.o : $(SRC_PATH)/main.c
	$(CC) $(CFLAGS) $(SRC_PATH)/main.c

DC_EnDeCoding.o : $(SRC_PATH)/DC_EnDeCoding.c
	$(CC) $(CFLAGS) $(SRC_PATH)/DC_EnDeCoding.c

errorhandle.o : $(SRC_PATH)/errorhandle.c
	$(CC) $(CFLAGS) $(SRC_PATH)/errorhandle.c

ricecoding.o : $(SRC_PATH)/ricecoding.c
	$(CC) $(CFLAGS) $(SRC_PATH)/ricecoding.c

PatternCoding.o : $(SRC_PATH)/PatternCoding.c
	$(CC) $(CFLAGS) $(SRC_PATH)/PatternCoding.c

getopt.o : $(SRC_PATH)/getopt.c
	$(CC) $(CFLAGS) $(SRC_PATH)/getopt.c

waveletbpe.o : $(SRC_PATH)/waveletbpe.c
	$(CC) $(CFLAGS) $(SRC_PATH)/waveletbpe.c

###############################################
clean:
	rm -f $(OBJS)
