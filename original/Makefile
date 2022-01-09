CC=gcc
CFLAGS=-Wall -g

all: main

reverse.o: reverse.c reverse.h
	$(CC) $(CFLAGS) -c reverse.c

main: main.c reverse.o
	$(CC) $(CFLAGS) -o $@ $^

clean:
	rm *.o main