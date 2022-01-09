CC=gcc
CFLAGS=-Wall -g

# add the new shared library to the default "all" rule
all: main libreverse.so main_runtime

libreverse.o: libreverse.c reverse.h
	$(CC) $(CFLAGS) -c libreverse.c

# This is newly added for compiling reverse.c to be a library
# .so - dynamically linked library (.dll on Windows)
libreverse.so: libreverse.c reverse.h
	$(CC) $(CFLAGS) -fPIC -shared -o $@ libreverse.c -lc

main: main.c libreverse.o
	$(CC) $(CFLAGS) -o $@ $^

main_runtime: main.c
	$(CC) $(CFLAGS) -o $@ $^ -L. -lreverse

clean:
	rm *.o *.so main main_runtime