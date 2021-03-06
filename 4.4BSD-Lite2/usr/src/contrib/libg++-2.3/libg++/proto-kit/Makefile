#******************************************************************************
#  This GNUmakefile is useful for large and complex projects which involve
# both G++ library prototypes, and prototypes added by the user(s).  It 
# is assumed that certain conventions are followed.
#
# 1) each object has its own source and header files.  For example class W 
#    would have W.h and W.cc
#
# 2) each prototype object has its own source and header files (W.hP and W.ccP)
#
# 3) object header files are contained in directory $(IDIR) (for Include
#    DIRectory)
#
# 4) object source files are contained in the current directory
#
# 5) prototype instantiations are contained in $(PC) (for Prototype C++
#    directory)
#
# 6) user prototype files (both header and source) are contained in $(IPP)
#
# 7) non-class code is contained in .h and .cc files, which live in $(IDIR)
#    and the current directory respectively
#
# *) as an extra, I have added the convention that whenever a W.cc file
#    is being compiled, the macro _W_cc is defined.  This is useful
#    particularly for conditional parts of header files (for example,
#    W.h could have ifdef _W_cc ...
#
# The makefile works in the following manner: the source dependencies are
# contained in .Makefile.S_DEP, while the (machine generated) object 
# dependencies are contained in .Makefile.O_DEP.  The types needed by
# the program are defined by several variables, and the makefile generates
# the necessary dependencies (both source and object) from those variables.
# The prototype lists are contained in .Makefile.TYPES.
# 
# NOTE: in order to create the machine generated files .Makefile.TYPES,
#  .Makefile.S_DEP, and .Makefile.O_DEP, empty versions of these
#  file must exist.  They can be easily created using "touch <file>".
#
#  -- Carl Staelin
#     staelin@princeton.edu
#******************************************************************************

#******************************************************************************
# standard Makefile definitions

comma := ,
space := $(empty) $(empty)
tab := $(empty)	$(empty)

#******************************************************************************
# definitions from environment variables

# the CPATH include directories 
cpath-include-directories := $(addprefix -I, $(subst :,$(space),$(CPATH)))

# the LPATH load directories 
lpath-load-directories := $(addprefix -L, $(subst :,$(space),$(LPATH)))

#******************************************************************************
# Basic definitions

HOST := $(shell hostname)

BASE := 

# CMU Directories
USR  := $(BASE)/usr
MACH := $(BASE)/usr/mach
CS   := $(BASE)/usr/cs

# GNU directories
GNULIBDIR := $(BASE)/usr/local/lib
GNUIDIR := $(GNULIBDIR)/g++-include

# the g++ library prototypes...
LPP := $(GNUIDIR)
PC := ../libg++

# the user's library prototypes...
IPP := ../proto

# the include directory
IDIR := ../include

# the include directories needed...
IDIRS := -I$(IDIR) -I$(PC) -I$(GNUIDIR) $(cpath-include-directories) 

# the directories where the libraries live...
LDIRS := $(lpath-load-directories)

#******************************************************************************
# standard function (and associated variables) definitions

C++ = g++
C++FLAGS = -pipe -O $(IDIRS) 
		-Dc_plusplus \
		-DDEBUG \
		-D$(strip $(subst .,_, _$(basename $(@F))_cc)) 


GENCLASS = ../bin/genclass.extnsn
PREPEND  = ../bin/prepend
HIERARCHY	= ../bin/hierarchy
MAKE_TYPES	= ../bin/make-types
PROTOTYPE	= ../bin/prototype

#******************************************************************************
# implicit rules

%.o : %.cc
	$(C++)	$(C++FLAGS) \
		-c -o $@ $(@D)$(basename $(@F)).cc

#******************************************************************************
# User types

BASIC_TYPES = \
	int \
	u_int \
	l_int \
	l_u_int 

# generated by this make file (initially create an empty .Makefile.TYPES
# so that "make dependencies" can create the real file)
include .Makefile.TYPES

#******************************************************************************
# User types (with sample values filled in) 
# 
# NOTE: do NOT add .h to the end of these, MAKE does it for you!

HEADER_FILES = \
	disk_set \
	file_types \
	include \
	inode \
	inode_t \
	lib \
	main \
	nfs_functions \
	nfs_mount \
	nfs_params \
	nfs_prot \
	system 

SOURCE_FILES = \
	disk_set \
	lib \
	nfs_auxil \
	nfs_dirent \
	nfs_fh \
	nfs_ipcress \
	nfs_links \
	nfs_main \
	nfs_mnt_serv \
	nfs_mount_xdr \
	nfs_prot_xdr \
	nfs_serv1 \
	nfs_serv2 \
	nfs_serv3 \
	nfs_serv4 \

#******************************************************************************
# some sample machine generated user-file stuff
#
../include/file_types.h : ../include/file_types.type.h ../include/file_types.operation.h
../include/Substrate_file.h : ../include/file_types.includes.h

../include/file_types.type.h : ../include/file_types.hierarchy
	$(HIERARCHY) -voutput_type=type ../include/file_types.hierarchy > $@

../include/file_types.includes.h : ../include/file_types.hierarchy
	$(HIERARCHY) -voutput_type=general -vpreamble='#include "' \
		-vpostamble='.h"' -vprint_first=1 ../include/file_types.hierarchy > $@

../include/file_types.operation.h : ../include/file_types.hierarchy
	(echo '#define FILE_TYPES_OPERATION(OPERATION)  \' ; \
	 $(HIERARCHY) -voutput_type=general -vpreamble='OPERATION(' \
		-vpostamble=') \' -vprint_first=1 ../include/file_types.hierarchy ; \
	 echo ; ) | sed 's/\.//g' > $@ ;

#******************************************************************************
# source files...

HEADERS := $(wildcard $(addprefix $(IDIR)/, $(addsuffix .h, $(USER_TYPES) $(HEADER_FILES))))

SOURCES := $(wildcard $(addsuffix .cc, $(USER_TYPES) $(SOURCE_FILES)))

OBJECTS := $(patsubst %.cc,%.o,$(SOURCES))

#******************************************************************************
# User prototype instantiations

USER_PROTO_HEADERS := $(addprefix $(PC)/, $(addsuffix .h, $(foreach type, $(basename $(notdir $(wildcard $(addprefix $(IPP)/, $(addsuffix .hP, $(USER_BASE_PROTO_TYPES)))))), $(filter %.$(type), $(USER_PROTO_TYPES)))))

USER_PROTO_SOURCES := $(addprefix $(PC)/, $(addsuffix .cc, $(foreach type, $(basename $(notdir $(wildcard $(addprefix $(IPP)/, $(addsuffix .ccP, $(USER_BASE_PROTO_TYPES)))))), $(filter %.$(type), $(USER_PROTO_TYPES)))))

USER_PROTO_OBJECTS := $(patsubst %.cc, %.o, $(USER_PROTO_SOURCES))

USER_PROTO_TYPE_SOURCES := $(wildcard $(addprefix $(IPP)/, $(addsuffix .hP, $(USER_BASE_PROTO_TYPES)) $(addsuffix .ccP, $(USER_BASE_PROTO_TYPES))))

#******************************************************************************
# libg++ prototype instantiations

LIB_PROTO_HEADERS := $(addprefix $(PC)/, $(addsuffix .h, $(foreach type, $(basename $(notdir $(wildcard $(addprefix $(LPP)/, $(addsuffix .hP, $(LIB_BASE_PROTO_TYPES)))))), $(filter %.$(type), $(LIB_PROTO_TYPES)))))

LIB_PROTO_SOURCES := $(addprefix $(PC)/, $(addsuffix .cc, $(foreach type, $(basename $(notdir $(wildcard $(addprefix $(LPP)/, $(addsuffix .ccP, $(LIB_BASE_PROTO_TYPES)))))), $(filter %.$(type), $(LIB_PROTO_TYPES)))))

LIB_PROTO_OBJECTS := $(patsubst %.cc, %.o, $(LIB_PROTO_SOURCES))

.PHONY : test
test :
	@echo LIB_PROTO_TYPES = "${LIB_PROTO_TYPES}";
	@echo LIB_BASE_PROTO_TYPES = "${LIB_BASE_PROTO_TYPES}" ;
	@echo LIB_PROTO_OBJECTS = "${LIB_PROTO_OBJECTS}" ;
	@echo LIB_PROTO_HEADERS = "${LIB_PROTO_HEADERS}" ;

#*****************************************************************************
# main procedures..

# example:
foo : foo.o $(OBJECTS) 
	$(C++) $(C++FLAGS) -o $@ foo.o $(OBJECTS) $(LDIRS)

sources : $(SOURCES) 

#******************************************************************************
# various cleanup stuff...

.PHONY : rmback clean realclean

rmback : 
	cd .. ; rm -f proto/*~ include/*~ src/*~

clean : rmback
	rm -f $(OBJECTS) $(PROTOLIB) $(LIB_PROTO_OBJECTS) $(USER_PROTO_OBJECTS)

realclean : clean
	rm -f $(LIB_PROTO_SOURCES) $(USER_PROTO_SOURCES) $(LIB_PROTO_HEADERS) $(USER_PROTO_HEADERS)


#*****************************************************************************
# debugging actions

%.E : %.cc
	$(C++) $(C++FLAGS) -E $(@D)$(basename $(@F)).cc > $@

%.s : %.cc
	$(C++) -S $(C++FLAGS) $< -o $@

#******************************************************************************
# "functions" for creating library source code from prototypes

parameters = $(subst .,$(space),$(notdir $(basename $(basename $@))))
ifdef_filename = $(subst .,_,$(notdir $(basename $@)))

generic-type = $(subst .,,$(suffix $(basename $(notdir $@))))

#
# return the type if it should be a "val" type (pointer or basic type)
#
parameter-val-q = $(filter %_p, $(parameter)) $(filter-out %_p, $(filter $(parameter), $(BASIC_TYPES)))

#
# return the list of pairs of types and their prototype type (val or ref)
#
proto-type-arg = $(parameter) \
	$(patsubst %, val, $(filter 1, $(words $(parameter-val-q)))) \
	$(patsubst %, ref, $(filter-out 1, $(words $(parameter-val-q))))

define make-proto-file
$(GENCLASS) \
  $(dir $<) \
  $(dir $@) \
  $(suffix $@) \
  $(foreach parameter, $(parameters), $(proto-type-arg)) \
  $(generic-type) ;
endef


define prepend-proto-includes
$(PREPEND) \
  $(addsuffix .h, $(basename $@)) \
  '#include "include.h"' \
  $(patsubst %, '#include "%.h"', $(foreach type, $(USER_TYPES) $(USER_PROTO_TYPES) $(LIB_PROTO_TYPES), $(patsubst %, $(type), $(filter $(subst .,, $(type)), $(patsubst %_p, %, $(parameters)))))) ;
endef

#******************************************************************************
# libg++ prototype dependencies


#
# Used to automatically generate a %_p.defs file, which is required by classes
# which do comparisons, for pointer types to objects which have the real 
# comparison code.
#
# this will cat the appropriate strings 
#
$(addprefix $(PC)/, $(addsuffix .h, $(filter-out char_p.defs, $(filter %_p.defs, $(LIB_PROTO_TYPES))))) : 
	@ echo creating $@ ; \
	( ifdef_filename=$(strip $(ifdef_filename)) \
	  p=$(strip $(patsubst %_p, %, $(parameters))) \
	  ../bin/make-defs-file ) > $@ ;

lib_proto_intermediate_sources = \
$(filter-out %_p.defs.h, $(LIB_PROTO_HEADERS) $(LIB_PROTO_SOURCES)) \
$(addprefix $(PC)/, $(addsuffix .h, char_p.defs)) 

lib_type_and_suffix_list = $(sort $(join $(suffix $(basename $(lib_proto_intermediate_sources))), $(addprefix ., $(suffix $(lib_proto_intermediate_sources)))))

#******************************************************************************
# prototype dependencies

proto_intermediate_sources = \
	$(USER_PROTO_HEADERS) \
	$(USER_PROTO_SOURCES) 

type_and_suffix_list = $(sort $(join $(suffix $(basename $(proto_intermediate_sources))), $(addprefix ., $(suffix $(proto_intermediate_sources)))))

#******************************************************************************
# generate machine generated dependencies 

H_FILES  = $(HEADERS) $(LIB_PROTO_HEADERS) $(USER_PROTO_HEADERS)
CC_FILES = $(SOURCES) $(LIB_PROTO_SOURCES) $(USER_PROTO_SOURCES)

DEPENDENCIES = $(addsuffix .d, $(basename $(CC_FILES)))

$(addsuffix .d, $(basename $(CC_FILES))) : %.d : %.cc
	( echo -n $(@D) ; $(C++) -M $(C++FLAGS) $*.cc) > $@;

#******************************************************************************
# machine generated type stuff
#

$(DEPENDENCIES) : $(H_FILES)

.PHONY : dependencies source-dependencies object-dependencies 

dependencies : type-definitions source-dependencies
	@ $(MAKE) object-dependencies 

object-dependencies : $(DEPENDENCIES) 
	@echo making object dependencies... ; \
	rm .Makefile.O_DEP ; \
	ls . | grep '\.d$$' > /tmp/dependencies ; \
	ls $(PC) | gawk '{printf "$(PC)/%s\n", $$0 ; }' | grep '\.d$$' >> \
		/tmp/dependencies ; \
	for file in `cat /tmp/dependencies` ; do \
	  cat $$file ; \
	done > .Makefile.O_DEP.tmp ; \
	mv .Makefile.O_DEP.tmp .Makefile.O_DEP ; \
	for file in `cat /tmp/dependencies` ; do \
	  rm $$file ; \
	done ; \
	rm /tmp/dependencies ;


source-dependencies : 
	@ echo making source dependencies... ; \
	($(PROTOTYPE) -voutput_type=libg++-prototypes \
		../include/prototype.dependencies | \
	 gawk '($$1 !~ "_p.defs$$") || ($$1 == "char_p.defs") { print ; }' | \
	 PROTO_SOURCE=$(LPP) PP=LPP ../bin/make-source-dependencies ; \
	 ($(PROTOTYPE) -voutput_type=user-prototypes \
		../include/prototype.dependencies ; \
	  $(HIERARCHY) -voutput_type=general ../include/file_types.hierarchy) | \
	 PROTO_SOURCE=$(IPP) PP=IPP ../bin/make-source-dependencies ; \
	) > .Makefile.S_DEP ;

type-definitions :
	@ echo make type definitions... ; \
	$(MAKE_TYPES) > .Makefile.TYPES ;

#******************************************************************************
# include machine generated dependencies 

include .Makefile.S_DEP
include .Makefile.O_DEP
