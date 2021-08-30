# Welcome to Homework 0!

For these questions you'll need the mini course and virtual machine (Linux-In-TheBrowser) at -

http://cs-education.github.io/sys/

Let's take a look at some C code (with apologies to a well known song)-
```C
// An array to hold the following bytes. "q" will hold the address of where those bytes are.
// The [] mean set aside some space and copy these bytes into teh array array
char q[] = "Do you wanna build a C99 program?";

// This will be fun if our code has the word 'or' in later...
#define or "go debugging with gdb?"

// sizeof is not the same as strlen. You need to know how to use these correctly, including why you probably want strlen+1

static unsigned int i = sizeof(or) != strlen(or);

// Reading backwards, ptr is a pointer to a character. (It holds the address of the first byte of that string constant)
char* ptr = "lathe"; 

// Print something out
size_t come = fprintf(stdout,"%s door", ptr+2);

// Challenge: Why is the value of away equal to 1?
int away = ! (int) * "";


// Some system programming - ask for some virtual memory

int* shared = mmap(NULL, sizeof(int*), PROT_READ | PROT_WRITE, MAP_SHARED | MAP_ANONYMOUS, -1, 0);
munmap(shared,sizeof(int*));

// Now clone our process and run other programs
if(!fork()) { execlp("man","man","-3","ftell", (char*)0); perror("failed"); }
if(!fork()) { execlp("make","make", "snowman", (char*)0); execlp("make","make", (char*)0)); }

// Let's get out of it?
exit(0);
```

## So you want to master System Programming? And get a better grade than B?
```C
int main(int argc, char** argv) {
	puts("Great! We have plenty of useful resources for you, but it's up to you to");
	puts(" be an active learner and learn how to solve problems and debug code.");
	puts("Bring your near-completed answers the problems below");
	puts(" to the first lab to show that you've been working on this.");
	printf("A few \"don't knows\" or \"unsure\" is fine for lab 1.\n"); 
	puts("Warning: you and your peers will work hard in this class.");
	puts("This is not CS225; you will be pushed much harder to");
	puts(" work things out on your own.");
	fprintf(stdout,"This homework is a stepping stone to all future assignments.\n");
	char p[] = "So, you will want to clear up any confusions or misconceptions.\n";
	write(1, p, strlen(p) );
	char buffer[1024];
	sprintf(buffer,"For grading purposes, this homework 0 will be graded as part of your lab %d work.\n", 1);
	write(1, buffer, strlen(buffer));
	printf("Press Return to continue\n");
	read(0, buffer, sizeof(buffer));
	return 0;
}
```
## Watch the videos and write up your answers to the following questions

**Important!**

The virtual machine-in-your-browser and the videos you need for HW0 are here:

http://cs-education.github.io/sys/

The coursebook:

http://cs241.cs.illinois.edu/coursebook/index.html

Questions? Comments? Use Ed:
https://edstem.org/

The in-browser virtual machine runs entirely in Javascript and is fastest in Chrome. Note the VM and any code you write is reset when you reload the page, *so copy your code to a separate document.* The post-video challenges (e.g. Haiku poem) are not part of homework 0 but you learn the most by doing (rather than just passively watching) - so we suggest you have some fun with each end-of-video challenge.

HW0 questions are below. Copy your answers into a text document (which the course staff will grade later) because you'll need to submit them later in the course. More information will be in the first lab.

## Chapter 1

In which our intrepid hero battles standard out, standard error, file descriptors and writing to files.

### Hello, World! (system call style)
1. Write a program that uses `write()` to print out "Hi! My name is `<Your Name>`".
```C
int main() {
	write(1, "Hi! My name is Robert Villa", 27);
	return 0;
}
```
### Hello, Standard Error Stream!
2. Write a function to print out a triangle of height `n` to standard error.
   - Your function should have the signature `void write_triangle(int n)` and should use `write()`.
   - The triangle should look like this, for n = 3:
   ```C
   *
   **
   ***
   ```
   
```C  
void write_triangle(int n);

int main() {
	write_triangle(3);
	return 0;
}

void write_triangle(int n) {
	int i = 0;
	int j = 0;
	for (i = 1; i <= n; i++) {
		for (j = 0; j < i; j++) {
			write(2, "*", 1);
		}
		write(2, "\n", 1);
	}
}
```

### Writing to files
3. Take your program from "Hello, World!" modify it write to a file called `hello_world.txt`.
   - Make sure to to use correct flags and a correct mode for `open()` (`man 2 open` is your friend).

```C  
#include <unistd.h>
#include <fcntl.h>
#include <stdio.h>
#include <sys/stat.h>
#include <sys/types.h>

int main() {
	mode_t mode = S_IRUSR |  S_IWUSR;
	int fildes = open("hello_world.txt", O_CREAT | O_TRUNC | O_RDWR ,mode);
	write(fildes, "Hi! My name is Robert Villa", 27);
	close(fildes);
	return 0;
}
```


### Not everything is a system call
4. Take your program from "Writing to files" and replace `write()` with `printf()`.
   - Make sure to print to the file instead of standard out!
```C  
#include <unistd.h>
#include <fcntl.h>
#include <stdio.h>
#include <sys/stat.h>
#include <sys/types.h>

int main() {
	close(1);
	mode_t mode = S_IRUSR |  S_IWUSR;
	int fildes = open("hello_world.txt", O_CREAT | O_TRUNC | O_RDWR ,mode);
	printf("Hi! My name is Robert Villa\n");
	close(fildes);
	return 0;
}
```

5. What are some differences between `write()` and `printf()`?
```C  
printf() is a function from the standard c library that can be used to stdout. write() is a systme that can be used to write to a file, stdout, or stderr. printf() uses write(). printf() also has a buffer.  
```
## Chapter 2

Sizing up C types and their limits, `int` and `char` arrays, and incrementing pointers

### Not all bytes are 8 bits?
1. How many bits are there in a byte?
```C  
There are at least 8 bits in a byte
```
2. How many bytes are there in a `char`?
```C  
There is 1 byte in a char on every machine
```
3. How many bytes the following are on your machine?
   - `int` 4, `double` 8, `float` 4, `long` 4, and `long long` 8
### Follow the int pointer
4. On a machine with 8 byte integers:
```C
int main(){
    int data[8];
} 
```
If the address of data is `0x7fbd9d40`, then what is the address of `data+2`?

```C  
0x7fbd9d50
```

5. What is `data[3]` equivalent to in C?
   - Hint: what does C convert `data[3]` to before dereferencing the address?

```C  
3[data] or *(data + 3)
```

### `sizeof` character arrays, incrementing pointers
  
Remember, the type of a string constant `"abc"` is an array.

6. Why does this segfault?
```C
char *ptr = "hello";
*ptr = 'J';
```
hello is a string constant stored in read only memory and ptr points to that address in memory, so when trying to write to this memory there will be be a segmentation fault. This is because a copy of the string outside of this read only memory is not made like with a char array.


7. What does `sizeof("Hello\0World")` return?
```C  
12
```
8. What does `strlen("Hello\0World")` return?
```C  
5
```
9. Give an example of X such that `sizeof(X)` is 3.
```C  
sizeof("hi");
```
10. Give an example of Y such that `sizeof(Y)` might be 4 or 8 depending on the machine.
```C  
sizeof(int);
```

## Chapter 3

Program arguments, environment variables, and working with character arrays (strings)

### Program arguments, `argc`, `argv`
1. What are two ways to find the length of `argv`?
```C  
use the value in argc or iterate through argv and count the number of entries until the null string is reached
```
2. What does `argv[0]` represent?
```C  
The executtion name of the program
```
### Environment Variables
3. Where are the pointers to environment variables stored (on the stack, the heap, somewhere else)?
```C  
You have to use environ to access environment variables because there are stored somewhere else
```
### String searching (strings are just char arrays)
4. On a machine where pointers are 8 bytes, and with the following code:
```C
char *ptr = "Hello";
char array[] = "Hello";
```
What are the values of `sizeof(ptr)` and `sizeof(array)`? Why?
```C  
sizeof(ptr) will equal 8 because it is getting the size of a pointer and pointers are said to be 8 bytes on this machine. sizeof(array) will equal 6 because the array holds a copy of the string. This means it holds space for the 5 characters in "hello" and the null character, totaling 6 bytes.
```

### Lifetime of automatic variables

5. What data structure manages the lifetime of automatic variables?
```C  
The stack manages the lifetime of these variables
```

## Chapter 4

Heap and stack memory, and working with structs

### Memory allocation using `malloc`, the heap, and time
1. If I want to use data after the lifetime of the function it was created in ends, where should I put it? How do I put it there?
```C  
You should put it in heap memory by allocating memory on the heap, you can do this by calling malloc
```
2. What are the differences between heap and stack memory?
```C  
Memory on the stack is freed after a function is completed. Memory on the heap needs to be manually freed or else it will last the lifetime of the program. Because of this, it can be used after the lifetime of a function.
```
3. Are there other kinds of memory in a process?
```C  
Memory is used for things such as read only string literals and instructions that the system uses.
```
4. Fill in the blank: "In a good C program, for every malloc, there is a ___".
```C  
a free called for the memory allocated
```
### Heap allocation gotchas
5. What is one reason `malloc` can fail?
```C  
malloc can fail if there is not enough free heap memory to allocate.
```
6. What are some differences between `time()` and `ctime()`?
```C  
time() returns the number of seconds since 1970 as a time_t. ctime() takes a pointer to a time_t and returns a char pointer that formats the time in a more readable format.
```
7. What is wrong with this code snippet?
```C
free(ptr);
free(ptr);
```
```C  
This is a double free eror. By freeing memory that has already been freed it could delete data or instructions that the system needs.
```
8. What is wrong with this code snippet?
```C
free(ptr);
printf("%s\n", ptr);
```
```C  
Accessing memory that has been freed will have unintended consequences because the you no longer have control what the dangling pointer is pointing to.
```
9. How can one avoid the previous two mistakes? 
```C  
You can avoiding these mistakes by assigning a pointer to null directly after the memory is freed.
```
### `struct`, `typedef`s, and a linked list
10. Create a `struct` that represents a `Person`. Then make a `typedef`, so that `struct Person` can be replaced with a single word. A person should contain the following information: their name (a string), their age (an integer), and a list of their friends (stored as a pointer to an array of pointers to `Person`s).
11. Now, make two persons on the heap, "Agent Smith" and "Sonny Moore", who are 128 and 256 years old respectively and are friends with each other.
```C  
// Questions 10 and 11 
#include <stdio.h>

struct Person {
	char* name;
	int age;
	struct Person** friends;
};
typedef struct Person pers;

int main() {
	pers* p1 = (pers*)malloc(sizeof(pers)); 
	pers* p2 = (pers*)malloc(sizeof(pers)); 
	p1->name = "Agent Smith";
	p2->name = "Sonny Moore";
	p1->age = 128;
	p2->age = 256;
	p1->friends = malloc(sizeof(pers));
	*(p1->friends) = p2;
	p2->friends = malloc(sizeof(pers));
	*(p2->friends) = p1;
	return 0;
}
```
### Duplicating strings, memory allocation and deallocation of structures
Create functions to create and destroy a Person (Person's and their names should live on the heap).
12. `create()` should take a name and age. The name should be copied onto the heap. Use malloc to reserve sufficient memory for everyone having up to ten friends. Be sure initialize all fields (why?).
13. `destroy()` should free up not only the memory of the person struct, but also free all of its attributes that are stored on the heap. Destroying one person should not destroy any others.
```C
// Questions 12 and 13 
#include <stdio.h>

struct Person {
	char* name;
	int age;
	struct Person** friends;
};
typedef struct Person pers;

pers* create(char* name, int age) {
	char* cname = strdup(name);
	pers* p = malloc(sizeof(pers));
	p->name = cname;
	p->age = age;
	p->friends = malloc(sizeof(pers*)*10);
	int i = 0;
	// important to initialize all friends to 0 / NULL because you dont know
	// what memory is currently in this location
	for (i = 0; i < 10; i++) {
		p->friends[i] = 0;
	}
	return p;
}

void destroy(pers* p) {
	free(p->friends);
	free(p->name);
	free(p);
	p = 0;
}

int main() {
	return 0;
}
```



## Chapter 5 

Text input and output and parsing using `getchar`, `gets`, and `getline`.

### Reading characters, trouble with gets
1. What functions can be used for getting characters from `stdin` and writing them to `stdout`?
```C
You can get a char from stdin with getchar() and write it to stdout with putchar()
```
2. Name one issue with `gets()`.
```C
If the buffer passed to char is not big enough, gets() will change memory outside the buffer and could corrupt other variables. There is no way to tell gets() that it should not accept inputs that are too long.
```
### Introducing `sscanf` and friends
3. Write code that parses the string "Hello 5 World" and initializes 3 variables to "Hello", 5, and "World".
```C
#include <stdarg.h>
#include <string.h>
#include <stdlib.h>
#include <stdio.h>

int main() {
	char* data = "Hello 5 World";
	char s1[6];
	int num;
	char s2[6];
	sscanf(data, "%s %d %s", s1, &num, s2);
	return 0;
} 
```
### `getline` is useful
4. What does one need to define before including `getline()`?
```C
You need to define _GNU_SOURCE. Need to initialize a character pointer buffer and a size_t capacity before getline() as well.
```
6. Write a C program to print out the content of a file line-by-line using `getline()`.
```C
#define _GNU_SOURCE
#include <stdio.h>
#include <stdlib.h>

int main() {
	char* buffer = 0;
	size_t capacity = 0;
	getline(&buffer, &capacity, stdin);
	printf("%s\n", buffer);
	free(buffer);
	buffer = 0;
	return 0;
}
```
## C Development

These are general tips for compiling and developing using a compiler and git. Some web searches will be useful here

1. What compiler flag is used to generate a debug build?
```C
-g
```
2. You modify the Makefile to generate debug builds and type `make` again. Explain why this is insufficient to generate a new build.
```C
make will keep the build in the debug mode instead of creating an entirely new build
```
3. Are tabs or spaces used to indent the commands after the rule in a Makefile?
```C
tabs
```
4. What does `git commit` do? What's a `sha` in the context of git?
```C
git commit saves the current state of the project and can be thought of a safe way of saving. sha is a hashing that can be used to identify a commit.
```
5. What does `git log` show you?
```C
It shows a list of all the commits and their sha made to a repository.
```
6. What does `git status` tell you and how would the contents of `.gitignore` change its output?
```C
git status shows the state of the local directory and shows all the files that have been changed. .gitignore specifies what files not to track. a file included in .gitignore will not show up in git status.
```
7. What does `git push` do? Why is it not just sufficient to commit with `git commit -m 'fixed all bugs' `?
```C
git push updates will updates the remote repository with the latest commit
```
8. What does a non-fast-forward error `git push` reject mean? What is the most common way of dealing with this?
```C
This reject can happen when a git commit is lost or when someone else pushes to the same branch. The best way to resolve this is to fetch and merge the conflict locally.
```

## Optional (Just for fun)
- Convert your a song lyrics into System Programming and C code and share on Ed.
- Find, in your opinion, the best and worst C code on the web and post the link to Ed.
- Write a short C program with a deliberate subtle C bug and post it on Ed to see if others can spot your bug.
- Do you have any cool/disastrous system programming bugs you've heard about? Feel free to share with your peers and the course staff on Ed.
