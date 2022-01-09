## The original and simple makefile we would write
This directory has all the files and a Makefile that we may write when we first
learned C programming.

### What do $@ and $^ in Makefile mean?
The following answer is copied from a [question on Stackoverflow](https://stackoverflow.com/questions/3220277/what-do-the-makefile-symbols-and-mean#:~:text=%24%40-,is%20the%20name,-of%20the%20target).

```$@``` is the name of the target being generated, and ```$<``` the first prerequisite (usually a source file). You can find a list of all these special variables in the [GNU Make manual](https://www.gnu.org/software/make/manual/html_node/Automatic-Variables.html#Automatic-Variables).

For example, consider the following declaration:
```
main: library.cpp main.cpp
```
In this case:

- ```$@``` evaluates to main
- ```$<``` evaluates to library.cpp
- ```$^``` evaluates to library.cpp main.cpp


This [link](https://stackoverflow.com/questions/3220277/what-do-the-makefile-symbols-and-mean#:~:text=112-,From%20Managing,-Projects%20with%20GNU) is helpful as well.