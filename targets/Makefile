# tools
CC := gcc
RM := rm -f
CP := cp

# flags
CFLAGS := -ggdb
LDFLAGS :=
LDLIBS :=

# sources
sources := target1.c target2.c target3.c target4.c target5.c tmalloc.c
targets := target1 target2 target3 target4 target5

# gmake magic
.PHONY: default all clean

#targets
default: all
all: $(targets)


install: $(targets)
	-$(RM) /tmp/target?
	$(CP) $(targets) /tmp


setuid:
ifeq ($(shell id -u),0)
	chown root:root /tmp/target?
	chmod 4755 /tmp/target?
else
	@echo "'make setuid' must be run as root -- use su or a root login!"
endif


clean:
	$(RM) $(targets) $(sources:.c=.o)


#dependencies
target4: tmalloc.o
tmalloc.o target4.o: tmalloc.h
