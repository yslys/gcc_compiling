## Write our own library now!
This is based on [YouTube video by Jacob Sorber](https://www.youtube.com/watch?v=JbHmin2Wtmc&t=153s).


Now, we are going to make ```reverse.c``` and ```reverse.h``` into a shared 
library. Therefore, in the Makefile, you would notice a little difference than
that in ```../original``` directory, as shown below:
```
libreverse.so: reverse.c reverse.h
    $(CC) $(CFLAGS) -fPIC -shared -o $@ reverse.c -lc
```

**Notice some mistakes in the above code?**

The mistake here is - **for compiling a .so file, i.e. dynamically linked library,** 
the file name of the target should be **the same as** the file name of the .c 
file. Since we are compiling a dynamically linked library, we **should** name 
the target s.t. it begins with lib, hence we need to change reverse.c to 
libreverse.c

The code in Makefile should be:
```
libreverse.so: libreverse.c reverse.h
    $(CC) $(CFLAGS) -fPIC -shared -o $@ reverse.c -lc
```

### What is .so file?
```.so``` stands for shared object (but is actually NOT a shared object - see
next section for the reason), or a dynamically linked library. Such file is the 
same as what we see a ```.dll``` file on Windows (dll - dynamically linked 
library).


### Are "shared objects" & "shared libraries" the same thing?
No, they are a little different. They still hold code, but the linker actually
puts that .o file into the finaly compiled binary at compile time; while a .so 
file is separate:
- .so file is designed to be separate.
- .so file is designed to be loaded at runtime, i.e. loads when program runs.

### What are those newly added flags when compiling?
Going back to the newly added lines in Makefile:
```
libreverse.so: reverse.c reverse.h
    $(CC) $(CFLAGS) -fPIC -shared -o $@ reverse.c -lc
```
+ ```-fPIC```: 
    + The compiler is going to generate "position independent code".
    + That is the code s.t. can be placed anywhere in memory and still run correctly.
    + Since at runtime you are going to load that code into memory, and we do not know where the binary is going to be put in memory - that is why position independent code is very important.
+ ```-shared```:
    + Basically, it is saying, "I want a shared library".

### What should we do next?
We need to add the new shared library (libreverse.so) to the default "all" rule,
so that we could compile it, as shown below:
```
# add the new shared library to the default "all" rule
all: main libreverse.so
```

Then, we are all set! Type make and see what happens!

### How to test our newly compiled dynamically linked library?
From the above code snippet in Makefile, we are compiling two things:
- main: the original executable without dynamically linked library
- libreverse.so: the dynamically linked library we want to build
In order to test our newly compiled dynamically linked library, we need to 
compile another program. Let's name such program: ```main_runtime```, i.e. the
dynamically linked library (libreverse.so) will be loaded at runtime.
```
all: main libreverse.so main_runtime
```

Next, we are going to add another rule to Makefile:
```
# main_runtime depends on main.c as well
main_runtime: main.c
    $(CC) $(CFLAGS) -o $@ $^ -L. -lreverse
```

### What are the meanings of ```-L.``` and ```-lreverse```?
+ ```-L.```: tell the compiler to look in the current directory for the source code of the library (libreverse.c).
+ ```-lreverse```: tell the compiler, "I want to link the program with a library with name being libreverse"
    + The compiler is assuming that all libraries begin with ```lib```.
    + E.g., ```-lc``` would be telling the compiler to link the program with ```libC```.

### Let's test our program with dynamically linked library!
```
$ ./main_runtime hello
./main_runtime: error while loading shared libraries: libreverse.so: cannot open shared object file: No such file or directory
```
Why? The problem is the program loader is looking for libraries and it can't 
find our new library. 

So, we're going to have to help it. We can tell the loader where to find our new
library by adding it to the ```LD_LIBRARY_PATH``` environment variable.

Basically, we need to specify ```LD_LIBRARY_PATH``` everytime we want to execute
the program, as follows:
```
$ LD_LIBRARY_PATH="/path/to/the/dynamically/linked/library;$LD_LIBRARY_PATH" \
  ./main_runtime "hello my friend"
```

### Is there a way we can eliminate the step of specifying ```LD_LIBRARY_PATH```?
Yes, we can install such library to one of the directories that the program 
loader automatically searches for libraries at runtime:
- /lib
- /usr/lib
- /usr/local/lib
- Wanna see more directories? Type this: ```ld --verbose | grep SEARCH_DIR | tr -s ' ;' \\012```

By putting new library in one of these directories then we won't need all that
LD_LIBRARY_PATH business. We can just run our program and it will find it.

