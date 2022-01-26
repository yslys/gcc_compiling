## Execution order of a Makefile:

### Makefile in original:
```
all: exec

reverse.o: reverse.c reverse.h
	$(CC) $(CFLAGS) -c reverse.c

exec: main.c reverse.o
	$(CC) $(CFLAGS) -o $@ $^
```
After we type make:
```
gcc -Wall -g -c reverse.c
gcc -Wall -g -o exec main.c reverse.o
```
### 1st version of Makefile in create_our_own_library:

```
# add the new shared library to the default "all" rule
all: main libreverse.so

libreverse.o: libreverse.c reverse.h
	$(CC) $(CFLAGS) -c libreverse.c

# This is newly added for compiling reverse.c to be a library
# .so - dynamically linked library (.dll on Windows)
libreverse.so: libreverse.c reverse.h
	$(CC) $(CFLAGS) -fPIC -shared -o $@ libreverse.c -lc

main: main.c libreverse.o
	$(CC) $(CFLAGS) -o $@ $^
```
After we type make:
```
gcc -Wall -g -c libreverse.c
gcc -Wall -g -o main main.c libreverse.o
gcc -Wall -g -fPIC -shared -o libreverse.so libreverse.c -lc
```


### 2nd version of Makefile in create_our_own_library:
Added a new target to "all" rule: main_runtime.
```
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
```
After we type make:
```
gcc -Wall -g -c libreverse.c
gcc -Wall -g -o main main.c libreverse.o
gcc -Wall -g -fPIC -shared -o libreverse.so libreverse.c -lc
gcc -Wall -g -o main_runtime main.c -L. -lreverse
```
