TESTS = 
noinst_PROGRAMS = test

test_SOURCES = test.c

INCLUDES = -I$(top_builddir)/include \
			-I ../../util/include \
			-I ../../axiom/include \
		    -I ../../include

test_LDADD = $(top_builddir)/src/libneethi.la \
			../../axiom/src/om/libaxis2_axiom.la \
			../../util/src/libaxutil.la \
			../../axiom/src/parser/libxml2/libaxis2_parser.la \
			../src/libneethi.la
