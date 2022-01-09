CC=gcc
CFLAGS=-Wall -g

all: exec

reverse.o: reverse.c reverse.h
	$(CC) $(CFLAGS) -c reverse.c

exec: main.c reverse.o
	$(CC) $(CFLAGS) -o $@ $^

clean:
	rm *.o exec