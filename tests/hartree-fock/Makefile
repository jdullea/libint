TOPDIR=../..
ifndef SRCDIR
  SRCDIR=$(shell pwd)
endif
-include $(TOPDIR)/src/bin/MakeVars

# include headers the object include directory
CPPFLAGS += -I$(TOPDIR)/include -I$(TOPDIR)/include/libint2 -I$(SRCDIR)/$(TOPDIR)/src/lib/libint -DSRCDATADIR=\"$(SRCDIR)/$(TOPDIR)/lib/basis\"

COMPILER_LIB = $(TOPDIR)/src/bin/libint/libINT.a
COMPUTE_LIB = -lint2
vpath %.a $(TOPDIR)/lib:$(TOPDIR)/lib/.libs

OBJSUF = o
DEPSUF = d
CXXDEPENDSUF = none
CXXDEPENDFLAGS = -M


TEST1 = hartree-fock
CXXTEST1SRC = $(TEST1).cc
CXXTEST1OBJ = $(CXXTEST1SRC:%.cc=%.$(OBJSUF))
CXXTEST1DEP = $(CXXTEST1SRC:%.cc=%.$(DEPSUF))

TEST2 = hartree-fock++
CXXTEST2SRC = $(TEST2).cc
CXXTEST2OBJ = $(CXXTEST2SRC:%.cc=%.$(OBJSUF))
CXXTEST2DEP = $(CXXTEST2SRC:%.cc=%.$(DEPSUF))

check:: check1 check2

check1:: $(TEST1)
	./$(TEST1) $(SRCDIR)/h2o.xyz | \
 awk ' {print} /^\*\*/ { eref = -74.942080057698; tol = 1e-11; if ($$5 - eref < tol && eref - $$5 < tol) {print "HF energy check passed"; exit 0} else {print "HF energy check failed"; exit 1} }'

check2::  $(TEST2)
	./$(TEST2) $(SRCDIR)/h2o.xyz | \
 awk ' {print} /^\*\*/ { eref = -76.003354058456; tol = 1e-11; if ($$5 - eref < tol && eref - $$5 < tol) {print "HF energy check passed"; exit 0} else {print "HF energy check failed"; exit 1} }'

$(TEST1): $(CXXTEST1OBJ) $(COMPILER_LIB) $(COMPUTE_LIB)
	$(LD) -o $@ $(LDFLAGS) $^ $(SYSLIBS)

$(TEST2): $(CXXTEST2OBJ) $(COMPILER_LIB) $(COMPUTE_LIB)
	$(LD) -o $@ $(LDFLAGS) $^ $(SYSLIBS)

# Source files for timer and tester are to be compiled using CXXGEN
$(TEST1) $(TEST2): CXX=$(CXXGEN)
$(TEST1) $(TEST2): CXXFLAGS=$(CXXGENFLAGS)
$(TEST1) $(TEST2): LD=$(CXXGEN)

clean::
	-rm -rf $(TEST1) $(TEST2) *.o *.d

distclean:: realclean

realclean:: clean

targetclean:: clean

depend:: $(CXXTEST1DEP) $(CXXTEST2DEP)

ifneq ($(CXXDEPENDSUF),none)
%.d: %.cc
	$(CXXDEPEND) $(CXXDEPENDFLAGS) -c $(CPPFLAGS) $(CXXFLAGS) $< > /dev/null
	sed 's/^$*.o/$*.$(OBJSUF) $*.d/g' < $(*F).$(CXXDEPENDSUF) > $(@F)
	/bin/rm -f $(*F).$(CXXDEPENDSUF)
else
%.d: %.cc
	$(CXXDEPEND) $(CXXDEPENDFLAGS) -c $(CPPFLAGS) $(CXXFLAGS) $< | sed 's/^$*.o/$*.$(OBJSUF) $*.d/g' > $(@F)
endif

-include $(CXXTEST1DEP)
-include $(CXXTEST2DEP)
