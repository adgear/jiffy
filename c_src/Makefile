# Based on c_src.mk from erlang.my by Loic Hoguin <essen@ninenines.eu>

CURDIR  := $(shell pwd)
BASEDIR := $(abspath $(CURDIR)/..)

PROJECT ?= $(notdir $(BASEDIR))
PROJECT := $(strip $(PROJECT))

ERL_CCFLAGS ?= $(shell erl -noshell -noinput -eval "io:format(\"-I~ts/erts-~ts/include -I~ts\", [code:root_dir(), erlang:system_info(version), code:lib_dir(erl_interface, include)])." -s init stop)
ERL_LDFLAGS ?= $(shell erl -noshell -noinput -eval "io:format(\"-L~ts\", [code:lib_dir(erl_interface, lib)])." -s init stop)

HAVE_LTO  ?= 0
DEBUG     ?= 0
NIF_DEBUG ?= $(DEBUG)

# System type and C compiler/flags.
CC_OPTS  := -Wno-unused-function -finline-functions -Wall -Werror
CXX_OPTS := -Wno-unused-function -finline-functions -Wall -Werror
LD_OPTS  := -lstdc++

ifeq ($(NIF_DEBUG),0)
	OPTIMIZE = -O3 -DNDEBUG
else
	OPTIMIZE = -O0
endif

ifeq ($(HAVE_LTO),1)
	CC_OPTS += -flto
endif

UNAME_SYS := $(shell uname -s)
ifeq ($(UNAME_SYS), Darwin)
	CC_OPTS  += -fPIE
	CXX_OPTS += -fPIE
	LDFLAGS  += -flat_namespace -undefined suppress
endif

CC ?= gcc
CFLAGS    += $(CC_OPTS) $(OPTIMIZE) -fPIC $(ERL_CCFLAGS)
CXXFLAGS  += $(CXX_OPTS) $(OPTIMIZE) -fPIC $(ERL_CCFLAGS)
LDFLAGS   += $(LD_OPTS) $(ERL_LDFLAGS) -lei -shared

SOURCES   := $(wildcard *.c **.cc double-conversion/*.c double-conversion/*.cc)
OBJECTS    = $(addsuffix .o, $(basename $(SOURCES)))
SO_OUTPUT ?= $(CURDIR)/../priv/$(PROJECT).so

# Targets

all: $(SO_OUTPUT)

$(SO_OUTPUT): $(OBJECTS)
	@mkdir -p $(BASEDIR)/priv/
	$(CC) $(OBJECTS) $(LDFLAGS) $(OUTPUT_OPTION)

%.o: %.c
	$(CC) $(CFLAGS) -c $< $(OUTPUT_OPTION)

%.o: %.cc
	$(CC) $(CXXFLAGS) -c $< $(OUTPUT_OPTION)

clean:
	@rm -f $(SO_OUTPUT) $(OBJECTS)
