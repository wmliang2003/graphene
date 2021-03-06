c_executables = $(patsubst %.c,%,$(wildcard *.c))
cxx_executables = $(patsubst %.cpp,%,$(wildcard *.cpp))
manifests = $(patsubst %.manifest.template,%.manifest,$(wildcard *.manifest.template)) manifest

exec_target = $(c_executables) $(cxx_executables) file_check_policy_strict.manifest file_check_policy_allow_all_but_log.manifest
target = $(exec_target) $(manifests)

clean-extra += clean-tmp

.PHONY: default
default: all

include ../Makefile.Test

ifeq ($(findstring x86_64,$(SYS))$(findstring linux,$(SYS)),x86_64linux)

CFLAGS-bootstrap_static = -static
CFLAGS-bootstrap_pie = -fPIC -pie
CFLAGS-shared_object = -fPIC -pie
CFLAGS-syscall += -I$(PALDIR)/../include -I$(PALDIR)/host/$(PAL_HOST)
CFLAGS-openmp = -fopenmp
CFLAGS-multi_pthread = -pthread
CFLAGS-exit_group = -pthread
CFLAGS-abort_multithread = -pthread
CFLAGS-eventfd = -pthread
CFLAGS-futex_bitset = -pthread
CFLAGS-futex_requeue = -pthread
CFLAGS-futex_wake_op = -pthread
CFLAGS-spinlock += -I$(PALDIR)/../lib -pthread
CFLAGS-sigprocmask += -pthread

%: %.c
	$(call cmd,csingle)

%: %.cpp
	$(call cmd,cxxsingle)

else
.IGNORE: $(c_executables) $(cxx_executables)
$(c_executables) $(cxx_executables):
endif

export PAL_LOADER = $(RUNTIME)/pal-$(PAL_HOST)
export PYTHONPATH=../../../../Scripts

.PHONY: regression
regression: $(target)
	$(RM) libos-regression.xml
	$(MAKE) libos-regression.xml

libos-regression.xml:
	python3 -m pytest --junit-xml $@ -v test_libos.py

.PHONY: clean-tmp
clean-tmp:
	rm -rf *.tmp *.cached *.manifest.sgx *~ *.sig *.token .cache __pycache__ libos-regression.xml testfile tmp/*
