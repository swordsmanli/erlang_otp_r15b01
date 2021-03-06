#
# %CopyrightBegin%
# 
# Copyright Ericsson AB 1997-2011. All Rights Reserved.
# 
# The contents of this file are subject to the Erlang Public License,
# Version 1.1, (the "License"); you may not use this file except in
# compliance with the License. You should have received a copy of the
# Erlang Public License along with this software. If not, it can be
# retrieved online at http://www.erlang.org/.
# 
# Software distributed under the License is distributed on an "AS IS"
# basis, WITHOUT WARRANTY OF ANY KIND, either express or implied. See
# the License for the specific language governing rights and limitations
# under the License.
# 
# %CopyrightEnd%
#
include $(ERL_TOP)/make/target.mk
include $(ERL_TOP)/make/$(TARGET)/otp.mk

CC = gcc
LD = $(CC)
LIBS = -lutil -ldl -lm  
CPU_SUP_LIBS = 

BINDIR = ../priv/bin/$(TARGET)
OBJDIR = ../priv/obj/$(TARGET)

# ----------------------------------------------------
# Application version
# ----------------------------------------------------
include ../vsn.mk
VSN=$(OS_MON_VSN)

# ----------------------------------------------------
# Release directory specification
# ----------------------------------------------------
RELSYSDIR = $(RELEASE_PATH)/lib/os_mon-$(VSN)

# ----------------------------------------------------
# Target Specs
# ----------------------------------------------------
ifeq ($(findstring win32,$(TARGET)),win32)
PROGRAMS = \
	win32sysinfo.exe \
	nteventlog.exe
C_FILES=win32sysinfo.c \
	nteventlog/elog_main.c \
	nteventlog/elog_util.c \
	nteventlog/elog_registry.c \
	nteventlog/elog_pipe_stdin.c \
	nteventlog/elog_format.c

EVLOG_OBJECTS = \
	$(OBJDIR)/elog_main.o \
	$(OBJDIR)/elog_util.o \
	$(OBJDIR)/elog_registry.o \
	$(OBJDIR)/elog_pipe_stdin.o \
	$(OBJDIR)/elog_format.o 

ENTRY_OBJ=$(ERL_TOP)/erts/obj/$(TARGET)/port_entry.o
PORT_ENTRY_POINT=erl_port_entry
ENTRY_LDFLAGS=-entry:$(PORT_ENTRY_POINT)
else
ifeq ($(findstring vxworks_simso,$(TARGET)),vxworks_simso)
PROGRAMS =
else
PROGRAMS = \
	memsup  cpu_sup
C_FILES= $(PROGRAMS:%=%.c)
endif
endif

TARGET_FILES= $(PROGRAMS:%=$(BINDIR)/%)

LDFLAGS = -Wl,-Bsymbolic-functions 

ALL_CFLAGS = -g -O0 -I/usr/local/src/erlang-otp-r15b01-0.1+imvu/erts/x86_64-pc-linux-gnu   -fno-tree-copyrename -g -O0 -D_GNU_SOURCE -DHAVE_CONFIG_H $(CFLAGS)

# ----------------------------------------------------
# Targets
# ----------------------------------------------------

_create_dirs := $(shell mkdir -p $(OBJDIR) $(BINDIR))

debug opt: $(TARGET_FILES)

clean:
	rm -f $(TARGET_FILES)
	rm -f core *~

docs:

# ----------------------------------------------------
# Special Build Targets
# ----------------------------------------------------

$(BINDIR)/win32sysinfo.exe: $(OBJDIR)/win32sysinfo.o $(ENTRY_OBJ)
	$(LD) $(LDFLAGS) $(ENTRY_LDFLAGS) -o $@ $(OBJDIR)/win32sysinfo.o $(ENTRY_OBJ)

$(BINDIR)/nteventlog.exe: $(EVLOG_OBJECTS)
	$(LD) $(LDFLAGS) $(ENTRY_LDFLAGS) -o $@ $(EVLOG_OBJECTS) $(ENTRY_OBJ)

$(BINDIR)/ferrule: $(OBJDIR)/ferrule.o
	$(LD) $(LDFLAGS) -o $@ $<

$(BINDIR)/mod_syslog: $(OBJDIR)/mod_syslog.o
	$(LD) $(LDFLAGS) -o $@ $<

$(BINDIR)/memsup: $(OBJDIR)/memsup.o
	$(LD) $(LDFLAGS) -o $@ $<

$(BINDIR)/cpu_sup: $(OBJDIR)/cpu_sup.o
	$(LD) $(LDFLAGS) -o $@ $< $(CPU_SUP_LIBS) 

$(OBJDIR)/%.o: %.c
	$(CC) -c -o $@ $(ALL_CFLAGS) $<

$(OBJDIR)/%.o: nteventlog/%.c
	$(CC) -c -o $@ $(ALL_CFLAGS) $<

$(OBJDIR)/memsup.o: memsup.h

# ----------------------------------------------------
# Release Target
# ---------------------------------------------------- 
include $(ERL_TOP)/make/otp_release_targets.mk

ifeq ($(findstring vxworks_simso,$(TARGET)),vxworks_simso)
release_spec:
else
release_spec: opt
	$(INSTALL_DIR) $(RELSYSDIR)/src
	$(INSTALL_DATA) $(C_FILES) $(RELSYSDIR)/src
	$(INSTALL_DIR) $(RELSYSDIR)/priv/bin
	$(INSTALL_PROGRAM) $(TARGET_FILES) $(RELSYSDIR)/priv/bin
endif

release_docs_spec:
