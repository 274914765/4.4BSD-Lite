# makefile for "except" - C exception handling
#
# Jeffrey Mogul		Stanford	4 March 1983
#
#
CFLAGS = -O
DESTDIR = /usr/local/lib
INCLUDEDIR = /usr/include

all: libexcept.a libexcept_p.a

install: install.lib install.man

install.man:
	rm -f /us/man/man3/except.3
	cp except.3 /usr/man/man3

except.o except_p.o: except.c except.h
	${CC} $(CFLAGS) -c -p except.c
	mv except.o except_p.o
	${CC} $(CFLAGS) -c except.c

libexcept.a: except.o
	ar cr libexcept.a except.o
	ranlib libexcept.a

libexcept_p.a: except_p.o
	ar cr libexcept_p.a except_p.o
	ranlib libexcept_p.a

install.lib: libexcept.a libexcept_p.a
	rm -f $(DESTDIR)/libexcept.a $(DESTDIR)/libexcept_p.a \
			$(INCLUDEDIR)/except.h
	install libexcept.a $(DESTDIR)
	ranlib $(DESTDIR)/libexcept.a
	install libexcept_p.a $(DESTDIR)
	ranlib $(DESTDIR)/libexcept_p.a
	cp except.h $(INCLUDEDIR)

clean:
	rm -f *.BAK *.CKP *.o *.a *.tar core a.out test

distrib: clean
	tar cf except.tar `ls|grep -v tar`

test: test.o libexcept.a
	${CC} -o test test.o libexcept.a

test.o: test.c except.h
	${CC} -c test.c -I.
