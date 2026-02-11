*This project has been created as part of the 42 curriculum by aalemami.*

## Description

get_next_line is a C function that reads and returns one line at a time from a file descriptor. Each call returns the next line, and `NULL` is returned when there is nothing left to read. A bonus version supports reading from multiple file descriptors simultaneously.

## Instructions

### Compilation

Compile the source files alongside your own `main.c`:

```sh
# Mandatory
cc -Wall -Wextra -Werror -D BUFFER_SIZE=42 get_next_line.c get_next_line_utils.c main.c -o gnl

# Bonus (multiple fd support)
cc -Wall -Wextra -Werror -D BUFFER_SIZE=42 get_next_line_bonus.c get_next_line_utils_bonus.c main.c -o gnl
```

### Usage

```c
#include "get_next_line.h"

int main(void)
{
    int   fd = open("file.txt", O_RDONLY);
    char  *line;

    while ((line = get_next_line(fd)) != NULL)
    {
        printf("%s", line);
        free(line);
    }
    close(fd);
}
```

## Algorithm

The function uses a **static variable** to persist leftover data between calls. The algorithm follows a simple accumulate-and-split pattern:

1. Check if the static buffer already contains a `\n`. If so, extract and return the line immediately.
2. Otherwise, `read()` `BUFFER_SIZE` bytes and append them to the static buffer. Repeat until a `\n` is found or EOF is reached.
3. Once a newline is found, split the buffer: return everything up to and including `\n`, and keep the rest for the next call.

The bonus version uses an array of static pointers (`static char *str[4096]`) indexed by fd, so each file descriptor maintains its own independent buffer.

This approach was chosen over alternatives like linked lists because it is simpler to implement and verify for memory leaks, while correctly handling all edge cases (lines longer than `BUFFER_SIZE`, files without trailing newlines, empty files). The trade-off is $O(n^2)$ concatenation cost in the worst case, which is acceptable for typical file sizes.

## Resources

- [read(2) man page](https://man7.org/linux/man-pages/man2/read.2.html)
- [Static variables in C](https://en.cppreference.com/w/c/language/storage_duration)
- AI was not used in the development of this project.
