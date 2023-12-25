---
title: 42Singapore - How To Solve 'pipex' Problem
author: Frank Nguyen
pubDatetime: 2023-12-09T10:00:00+08:00
postSlug: how-to-solve-pipex
featured: false
draft: false
tags:
  - how-to
  - 42singapore
description: A guide to solve the pipex problem from 42 Singapore programme
---

![Pipeline In Desert](@assets/pipeline-desert.jpg)

Pipex is a project that requires you to implement shell pipe `|` in C.

## Table of contents

## Introduction

During Piscine, you worked on some exercises in Shell01 that required using 'pipe' (i.e. cmd1 | cmd2). Let us revise below question from Shell01.

Question:<br/>

_Write a command line that displays your machine's MAC addresses. Each address must be followed by a line break._

Answer:

```bash
ifconfig | grep "ether " | cut -d ' ' -f 2
```

Similar to a warehouse packing conveyor belt where individual machines handle specific tasks, such as placing packages on the belt and performing the packing, the original task in the preceding question is broken down into several subtasks. Each subtask is managed by a distinct command, and the output is subsequently transferred to the next command for processing through a 'pipe'. This approach allow you to solve complex problems just by arranging Linux commands to work together.

![Commands Pipeline](@assets/pipe-cmd.jpeg)

## Understand how pipe() works

_Reference: [Linux Manual Page](https://man7.org/linux/man-pages/man2/pipe.2.html)_

<br/>

Let's look at its prototype

```c
int pipe(int pipefd[2]);
```

**pipe()** creates a directional channel that data flows from pipefd[1] (write-end) to pipefd[0] (read-end). Data written to the write-end is buffered by the kernel until it is read from the read-end. Here is the sample code:

```c
#include <stdio.h>
#include <string.h>
#include <unistd.h>

int main()
{
    int n, pipefd[2];
    char buf[1025], *data = "hello... this is sample data";

    pipe(pipefd);
    write(pipefd[1], data, strlen(data));
    if ((n = read(pipefd[0], buf, 1024)) >= 0)
    {
        buf[n] = 0; /* terminate the string */
        printf("read from the pipe: \"%s\"\n", buf);
    }
    return 0;
}

// Output:

// read from the pipe: "hello... this is sample data"
```

## Understand how fork() works

In C, fork() duplicates the current process. The new process is referred to as the child process, and the original process is referred to as the parent process. The child process is an exact copy of the parent process, except that it has a different process ID and a different parent process ID. The process ID of the child process is returned by fork() to the parent process, and the value 0 is returned to the child process. The child process and the parent process continue to execute from the point where fork() was called. The child process and the parent process have their own copies of the data segment, the heap, and the stack. The child process and the parent process do not share these segments.

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main() {
    int data = 10;
    pid_t pid;

    pid = fork();
    if (pid == 0) { // if it is child process
        data = -1;  // child process has a copy of data
                    // hence, this change does not reflect in
                    // variable data in the parent process
    } else {
      // this is parent process
      waitpid(pid, NULL, 0); // wait till child process is finished
      printf("data = %d\n", data); // data should not be affected by the child process
    }
    return 0;
}
```
Output
```
data = 10
```

## Understand how dup2() works

dup2(newfd, oldfd) works like a redirection from oldfd to newfd. Any data written to oldfd will be written to newfd instead. Let us look at below two example

**Example 1**
```c
#include <stdio.h>
#include <unistd.h>
#include <fcntl.h>

int main() {
    int file = open("output.txt", O_WRONLY | O_CREAT, 0644);
    int stdout = 1;

    dup2(stdout, file);
    // writing to output.txt will be redirected to stdout
    // hence, output.txt does not contain anything
    write(file, "example of dup2\n", 16);
    close(file);
    return 0;
}
```
stdout
```
example of dup2
```
output.txt
```
```

**Example 2**
```c
#include <stdio.h>
#include <unistd.h>
#include <fcntl.h>

int main() {
    int file = open("output.txt", O_WRONLY | O_CREAT, 0644);
    int stdout = 1;

    dup2(file, stdout);
    // writing to stdout will be redirected to output.txt
    // hence, stdout does not contain anything
    // but output.txt contains the written string
    write(stdout, "example of dup2\n", 16); //
    close(file);
    return 0;
}
```
stdout
```
```
output.txt
```
example of dup2
```

## How to solve pipex

### Step 1: Validate the input
For example,
- Check if the number of arguments is correct
- Check if the input file exists
- Check if the output file is writable or can be created
- Check if a pipe can be created

### Step 2: Create a child process
The child process will execute the first command with the input file descriptor being duplicated to the `stdin` and the `stdout` is duplicated to the write-end of the pipe.

### Step 3: Handle the parent process
The parent process will execute the second command with the read-end of the pipe being duplicated to the `stdin` and the `stdout` is duplicated to the output file descriptor.

Here is the scaffold

```c
run_cmd(cmd, input_fd, output_fd) {
  dup2(input_fd, 0); // duplicate input_fd to stdin
  dup2(output_fd, 1); // duplicate output_fd to stdout
  // execute the command
}

main() {
  var pipefd[2];

  pipe(pipefd); // pipefd[1] is the write-end of the pipe
                // pipefd[0] is the read-end of the pipe
  fin = open(input_file, O_RDONLY); // input file descriptor
  fout = open(output_file, O_WRONLY | O_CREAT | O_TRUNC, 0644); // output file descriptor
  pid = fork();
  if (pid == 0) // child process
  {
    run_cmd(cmd1, fin, pipefd[1]);
  }
  else // parent process
  {
    close(pipefd[1]) // must close the write end before reading from the pipe
    run_cmd(cmd2, pipefd[0], fout);
  }
}
```

### Step 4: Handle the command

Scenario 1: Command is the custom command (e.g. ./custom_cmd)<br/>
In this case, we need to check if the path of the custom command is valid using `access()` function.
```c
access(cmd, X_OK) // check if the command is executable
```

Scenario 2: Command is the built-in command (e.g. ls, cat, grep, etc.)<br/>
If the access check above fails, it means the path to the command is stored in PATH environment variable. You need to locate the command from the PATH in the `env` strings passed to the main function, i.e. `int main(int argc, char **argv, char **env)`.

Here is the psuedo code

```
get_path(env) {
  for each string in env {
    if string starts with "PATH=" {
      return string
    }
  }
}

get_cmd_path(path, cmd) {
  paths = split(path, ":")
  for each directory in path {
    if access(directory/cmd, X_OK) == 0 {
      return directory/cmd
    }
  }
}
```

Scenario 3: Command contains double quotes (e.g. grep "hello world")<br/>
In practice, you will get `argv[3] = "grep \"hello world\""` if grep "hello world" is passed to the main function. You will need to split `argv[3]` into two strings "grep" and "\"hello world\"", then remove the double quotes from the second string by copying only non-double-quote characters to the new string.

### Step 5: Test your code
After you complete your code, you can test using my [Pipex Checker](https://github.com/frankng-sg/42singapore/tree/main/cursus/pipex_checker)
