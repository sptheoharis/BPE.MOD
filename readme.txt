
Bit plane encoder


Please note:
(1)	Before you download and use the program, you must read and agree the license agreement carefully.
(2)	We supply the source code and program WITHOUT ANY WARRANTIES. The users will be responsible for any lose or damage caused by the use of the source code and program.

Author:

Hongqiang Wang
Department of Electrical Engineering
University of Nebraska-Lincoln

Email: hqwang@bigred.unl.edu

Update: Dec. 14, 2021

Nikolaos Kefalas
Alma Technologies S.A.

URL: https://www.alma-technologies.com
Email: info@alma-technologies.com

Your comment and suggestions are welcome. Please report bugs to me via email and I would greatly appreciate it.

Nov. 3, 2006
///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
Content index
1. Introduction
2. File descrprtion
3. Structure and type definiation.
4. Function description
5. How it works.

///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

1. Introduction

This program is developed based on the DRAFT RECOMMENDED STANDARD CCSDS 122.0-R-2 RED BOOK, which was released on July 2005. The final version of the recommended standard (blue book?) is being evalued and will be released soon. The program will be modified to accomodate the future changes.

The basic principal of the coder follows the typical wavelet based coding strategy.  That is, a 3-level wavelet decomposition is applied to the whole image first. Then the resulting coefficients are grouped into 8x8 blocks. In each block,  the coefficients are classified into Direct component (DC), parents, children and grandchildren respectively. Usually most energy is located in the upper left of the block.

A number of consecutive (raster order) 8x8 blocks are grouped into a segment. In each segment, every 16 blocks compose a gaggle.

The coding process starts with the entropy coding of the DCs in a segment, followed by the maximum bit depth of AC of each block (ACdepth). After the encoding of DCs and ACdepth, the bit plane coding starts working. Given a packet, the encoder scans all coefficients (except DC)  from the highest bit plane, and determine the coefficients that are hit and that are not hit. If a coefficient is hit, it is recorded along with its sign, corresponding to their locations, i.e., parents, children, or grandchildren. Some transitional bits are used to indicate whether children family, or grandchildren family have pixels being hit. As at the high bit planes most coefficients are of small values in the children family, or grandchildren family, use of the transitional bits can significantly save the coding efficiency.

The coefficients that are found to be hit for the first time at a bit plane are grouped and then entropyed coded in a gaggle. The entropy coders are Golomb-Rice code which requires the splitting coding paramters. These parameters can be obtained by exhausively searching over the gaggle.  Alternatively this parameters can be estimated on the fly.

For a coefficient, if it’s been hit in a higher bit plane, then its bits at the lower bit plane are directly transmitted without further coding.

As this coding is packet based, for each segment there is a packet header. In the header, coding parameters, such as the number of bits desired in the segment, packet size, type of wavelet transform, are specified. The coding parameters in the header are flexible and may be changed to accommodate the new coding requirments as encoding proceeds.

2. File descrprtion

The program is developed using C language and tested under Misrosoft Visual C++ 2005. It is also compiled and tested under Linux Fedora 5.0.  I assume this program complies with standard ANSI C, but not gurantee.

The total program contains 3 header files, and  18 source files.

Three header files (in alphabet order):

getopt.h (this files is obtained for free under GNU General Public License)
tailor.h (this files is obtained for free under GNU General Public License)

these two files are the header files for getopt.c, which is to get the parameters from the command line.  These have no direct link with the coding process.

global.h:
This files defines the struction and types that are used in the coder, as well as some macro variables and global varables. Some global functions are also defined.
The defination of structures are in capital letters, and their typedef variables in lower case

Structures:

SYMBOLDETAILS:
This struct record the value, mapping value, length, sign, and type of a symbol. This is used for the bit plane coding

BITSTREAM:
This structure contains the information of bit stream, including the total number of bits in current segment, the total number of  bits output so far; byte buffer; and the number of bits in a byte that have filled, and FILE type for bit stream output. This one is used throughout the encoding and decoding process.
TYPEC:
Record the type of children subblock
TRANH:
Record the transition to grandchildren subblocks
TRANHI:
Record the transition to grandchildren subblock Hi
TYPEHIJ:
Record the type of grandchildren subblock Hij

PARENTREFINE:
Record the refine bits of parent subblock
CHILDRENREF:
Record the refine bits of children subblock
GRANDCHILDREDREF:
Record the refine bits of children subblock

REFINEMENTBIT:
Record all refine bits in a block, i.e., including PARENTREFINE, CHILDRENREF, and GRANDCHILDREDREF
PLANEHIT:
Record all type bits and transitional records
BLOCKBITSHIT:
Record all coding information of a block, including DC, AC, mapping, maximum AC depth, and some coding structures defined above.

HEADER_STRUCTURE_PART1:
part 1 structure of header structure.
HEADER_STRUCTURE_PART2:
part 2 structure of header structure.
HEADER_STRUCTURE_PART3:
part 3 structure of header structure.
HEADER_STRUCTURE_PART4:
Part 4 structure of header structure.

HEADER:
Consists of the above 4 header structures.

STR_STOPLOCATION:
Structure to record the partial decoding locations. Used for decoding only.

CODINGPARAMETERS:
Contains all coding parameters, image information, files to output and input, rate to be reached.

BLOCKSTRING:
For decoding only. A link will be built to record the decoded blocks. The link is reorganized and reconstructed using inverse wavelet transform (IWT).

There is one UNION type:
HEADERUNION:
This is to save storage space and facilitate some operations.

4. Function description

ACBpeDecoding:
AC component decoding, called only by DecoderEngine
ACBpeEncoding:
AC component decoding, called only by EncoderEngine
ACDepthEncoder:
ACdepth encoding, called only by ACBpeEncoding
ACDepthDecoder:
ACdepth decoding, called only by ACBpeDecoding
ACGaggleEncoding:
This is for AC encoding of gaggles, Called by ACDepthEncoder
ACGaggleDecoding:
This is for AC decoding of gaggles, Called by ACDepthDecoder
AdjustOutPut:
Called by DecoderEngine(StructCodingPara *PtrCoding)
BitPlaneSymbolReset:
Reset bitplane structure to all 0s. called by StagesEnCodingGaggles1, StagesEnCodingGaggles2, and StagesEnCodingGaggles3.
BitsOutput:
This is a small function which outputs bits to buffer and files. For encoding only
BitsRead:
This is a small function which read bits to buffer and files. For decoding only
BlockScanEncode:
This is the kernel part of the bit plane coding. It determines the type of subblocks and transitional bits. Called by ACBpeEncoding only.
BuildBlockString:
This is to build block string index for further encoding. Called by EncoderEngine only.
CheckUsefill:
This is to check if usefill is enabled and if so how many bits need to fill. called by ACBpeDecoding only.
CodingOptions:
This is to determine the scoding parameters of Golomb-Rice decoding, called by StagesEnCoding only.
CoeffDegroup:
Reorganize back into the block ready for inverse wavelet transform. called by DecodingOutputInteger only.
CoeffDegroupFloating:
Reorganize back into the block ready for inverse wavelet transform. called by DecodingOutputFloating only.
CoefficientsRescaling:
Rescale the coefficients before inverse DWT, called by DWT_Reverse only.
CoefficientsScaling:
Rescale the coefficients after DWT, called by DWT_ only.
CoeffRegroup:
Reorganize the transform coefficients into the blocks after wavelet transform. called by DWT_ only
CoeffRegroupF97:
Reorganize the transform coefficients (float) into the blocks after wavelet transform. called by DWT_ only
ConvTwosComp:
Convert DC into 2’s complement representation, called by DCEncoding only
DCDeCoding:
DC decoding, called by DecoderEngine only.
DCEncoder:
DC entropy encoding, called by DCEntropyEncoder only.
DCEncoding:
DC encoding, called by EncoderEngine only.
DCEntropyDecoder:
DC entropy decoding, called by DCDeCoding only.
DCEntropyEncoder:
DC entropy encoding, called by DCEncoding only.
 DCGaggleDecoding:
DC decoding in a gaggle, called by DCEntropyDecoder.
DebugInfo:
This is a function to output debug information.
DecoderEngine:
The kernel function for decoding, called by main only.
DecodingOutputFloating:
this is to output the floating point DWT decoding results, called by DecoderEngine only.
DecodingOutputInteger
	this is to output the integer DWT decoding results, called by DecoderEngine only.
DeConvTwosComp:
Convert coefficients back from 2’s complement representation to normal, called byAdjustOutPut only.
DeMappingPattern:
Demap the decoded symbols back to its original value.     Called by StagesDeCodingGaggles1,  StagesDeCodingGaggles2, StagesDeCodingGaggles3
DPCM_ACDeMapper
Demap the decoded ACdepth back to its original value. Called by ACDepthDecoder.
DPCM_ACMapper
Map the ACdepth  from double (negative and positive) side value to positive value. Called by ACDepthEncoder
DPCM_DCDeMapper
Demap the decoded DC value  back to its original value. Called by DCDeCoding.
DPCM_DCMapper
Map the DC  from double (negative and positive) side value to positive value. Called by DCEncoding
DWT_
DWT transform, called by EncoderEngine only.
DWT_Reverse:
Inverse DWT transform, taking integer input,called by DecoderEngine only.
DWT_ReverseFloating
Inverse DWT transform, taking floating input, called by DecoderEngine only.
EncoderEngine
The kernel function for decoding, called by main only.
ErrorMsg
This is to output error code to a file and standard terminal.
forwardf97f
             Forward transform of floating 97.
forwardf97M
Forward transform of integer 97.
HeaderInilization
This is to initialize the header before coding. Called by main only.
HeaderOutput
Output the header. For each segment, header will be output. Called by DCEncoding
HeaderReadin
Read the header from the coded bit stream. Called DecoderEngine.
HeaderUpdate,
header is updated after the coding of a segment and ready for the coding of next segment  called by EncoderEngine only
ImageRead
	Open an image to read. Called by EncoderEngine
ImageSize
	Determine the image size. Called by EncoderEngine
ImageWrite
Output the decoded image. Called by DecodingOutputInteger
ImageWriteFloat,
Output the decoded image (in floating mode) DecodingOutputFloating(StructCodingPara *PtrCP, float **imgout_floatingcase)
inversef97f
inverse  floating DWT transform
inversef97M
inverse  integer DWT transform
lifting_f97_2D
lifting algorithm of floating 97 DWT transform
lifting_M97_2D
lifting algorithm of integer 97 DWT transform
main
main function.
OutputCodeWord:
Output a codeword. called by BitsOutput(StructCodingPara *Ptr, DWORD bit, int length)
ParameterValidCheck:
Called by main to verify the coding parameters are valid.
PatternMapping:
Map the original symbol to new one according to table. Called by CodingOptions.
RefBitsDe:
Get refine bits from the coded bit stream, called by StagesDeCoding
RefBitsEn:
Output refine bits from the coded bit stream, called by StagesDeCoding
RiceCoding:
 Rice encoding based on the coding parameters. Called by StagesEnCodingGaggles1, StagesEnCodingGaggles2, and StagesEnCodingGaggles3.
RiceDecoding:
Rice decoding based on the coding parameters. Called by StagesDeCodingGaggles1, StagesDeCodingGaggles2, and StagesDeCodingGaggles3.
SegmentBufferFlushDecoder
	Flush the leftover bits in a segment coding and reset the byte alignment for the coding of next segment. Called by EncoderEngine

SegmentBufferFlushEncoder
Flush the leftover bits in a segment decoding and reset the byte alignment for the decoding of next segment. Called by DecoderEngine
StagesDeCoding
ACBpeDecoding
StagesDeCodingGaggles1
Bit plane Decoding of stage 1, Called by StagesDeCoding
StagesDeCodingGaggles2
Bit plane Decoding of stage 2, Called by StagesDeCoding
StagesDeCodingGaggles3
Bit plane Decoding of stage 3, Called by StagesDeCoding
StagesEnCodingGaggles1
Bit plane encoding of stage 1, Called by StagesEnCoding
StagesEnCodingGaggles2
Bit plane encoding of stage 2, Called by StagesEnCoding
StagesEnCodingGaggles3
Bit plane encoding of stage 3, Called by StagesEnCoding
TempCoeffOutput Usage
	Temporal coefficients output, for debug purpose.


5. The programs take parameters from command line.
/*****************************************************/
bpe [-e]/[-d] [Input_file_name] [-o Output_file_name] [-r BitsPerPixel]

Parameters:
[-e]: encoding filename.
[-d]: decoding filename.
[-o]: provide ouput file name.
[-r]: bits per pixel for encoding. (by default it is 0 and encoded to the last bitplane
[-w]: the number of pixels of each row.
[-h]: the number of pixels of each column.
[-b]: the number of bits of each pixel. By default it is 8.
[-f]: byte order of a pixel, if it consists of more than one bytes. 0 means litttle endian, 1 means big endian. Default value is 0.
[-t]: wavelet transform. 1 is integer 9-7 DWT and 0 is floating 9-7 DWT. By default it is integer DWT
[-s]: the number of blocks in each segment. By default it is 256.
[-l]: Set OptDCSelect. By default it is 1 (enabled).
[-k]: Set OptACSelect. By default it is 1 (enabled).
[-m]: Set DCStop. By default it is 0 (disabled).
[-n]: Set Stage Stop (0-3). By default is 3 (all).
[-p]: Set Biplane Stop. By default is 0 (all).

eg 1: bpe -e sensin.img -o codes -r 1.0 -w 256 -h 256 -s 256
eg 2: bpe -d codes -o ss.img
