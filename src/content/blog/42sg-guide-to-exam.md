---
title: 42Singapore - How To Prepare For Exam
author: Frank Nguyen
pubDatetime: 2023-12-01T20:00:00+08:00
postSlug: how-to-prepare-42singapore-exam
featured: false
draft: false
tags:
  - How-To
  - 42Singapore
  - Exam
description: A simple guide that provides an efficient approach to tackle exam questions
---

![Paper Background](@assets/paper-background.jpg)

Assessment through examinations remains a crucial benchmark for gauging learners' comprehension of a subject. In the examination setting, individuals are tasked with solving problems within a limited timeframe and without assistance. [42Singapore](https://www.42singapore.sg/) places significant emphasis on examinations, presenting challenging questions to assess participants' abilities thoroughly. As a result, adequate preparation is crucial.

## Table of contents

## Prerequisite

- Do `libft` by yourself without any help because it is the foundation.

- Learn how to [debug with GDB](https://www.cs.umd.edu/~srhuang/teaching/cmsc212/gdb-tutorial-handout.pdf).

- String & Array (e.g. [wdmatch](https://github.com/pasqualerossi/42-School-Exam-Rank-02/tree/main/Level%202/wdmatch)).

- Bitwise Operation (e.g. [reverse_bits](https://github.com/pasqualerossi/42-School-Exam-Rank-02/tree/main/Level%202/reverse_bits)).

- Pointers and Memory Allocation (e.g. [ft_strdup](https://github.com/pasqualerossi/42-School-Exam-Rank-02/tree/main/Level%202/ft_strdup))

## Problem Solving Skill

I see some students trying to remember the solution implementation details, so that they can just type out the solution from the memory during the examination. This will make you panic not knowing where is the issue if the answer does not work.

Remembering implementation details is like trying to draw a bird by remember ![](@assets/bird.png)
Instead, you just need to remember ![](@assets/bird-outline.png)

Let me elaborate further by using one of the examination question.

```text
Assignment name  : last_word
Expected files   : last_word.c
Allowed functions: write
--------------------------------------------------------------------------------

Write a program that takes a string and displays its last word followed by a \n.

A word is a section of string delimited by spaces/tabs or by the start/end of
the string.

If the number of parameters is not 1, or there are no words, display a newline.

Example:

$> ./last_word "FOR PONY" | cat -e
PONY$
$> ./last_word "this        ...       is sparta, then again, maybe    not" | cat -e
not$
$> ./last_word "   " | cat -e
$
$> ./last_word "a" "b" | cat -e
$
$> ./last_word "  lorem,ipsum  " | cat -e
lorem,ipsum$
$>
```

How do you approach to solve this question? A student straight away told me that he would first check if number of params was one, then he would work on the spaces/tabs. Then he struggled to continue. In his mind, I think he was trying to remember this.

```C
#include <unistd.h>

void	last_word(char *str)
{
	int	j = 0;
	int i = 0;

	while (str[i])
	{
		if (str[i] == ' ' && str[i + 1] >= 33 && str[i + 1] <= 126)
			j = i + 1;
		i++;
	}
	while (str[j] >= 33 && str[j] <= 127)
	{
		write(1, &str[j], 1);
		j++;
	}
}

int		main(int argc, char **argv)
{
	if (argc == 2)
		last_word(argv[1]);
	write(1, "\n", 1);
	return (0);
}
```

It is not easy to capture the required steps to solve the problem, isn't it? How about describing the solution in English?

**Step 1**
Verify if command line params are valid

**Step 2**
Notice the followings,

- A separator (a.k.a. sep) is either a space or a tab

- A new word starts with a non-separator (a.k.a. non-sep) character and the previous character in the string (a.k.a. str) is a sep or there is no previous character

- A new word ends when the next character is a sep or there is no next character

**Step 3**
Use a variable wordStart to store the location of the last word's first letter. Initially,
wordStart = 0, then I will loop from second character of the string until the end.

If I see str[i - 1] is a sep and str[i] is a non-sep, then str[i] is the start of a new word. I will update
wordStart = i. The loop stops when it reaches the end of the string. At this moment, the value of wordStart is the location of the last word's first letter.

**Step 4**
Print the last word by printing each character starting from wordStart until the character is a separator or reaching the end of the string

**Step 5**
Working out the approach with a pen and paper.

Given:

| index | 0 1 2 3 4 5 6 7 8 9   |
| ----- | --------------------- |
| str   | \_ _ a b c _ x y z \_ |

Algorithm:

_Remember, wordStart = 0 initially_

| i   | str[i - 1] | str[i] | New Word? | wordStart |
| --- | ---------- | ------ | --------- | --------- |
| 1   | \_         | \_     | No        | 0         |
| 2   | \_         | a      | Yes       | 2         |
| 3   | a          | b      | No        | 2         |
| 4   | b          | c      | No        | 2         |
| 5   | c          | \_     | No        | 2         |
| 6   | \_         | x      | Yes       | 6         |
| 7   | x          | y      | No        | 6         |
| 8   | y          | z      | No        | 6         |
| 9   | z          | \_     | No        | 6         |

So, it is correct. Lets try another example,

Given:

| index | 0 1 2 3  |
| ----- | -------- |
| str   | a b c \_ |

Algorithm:

_Remember, wordStart = 0 initially_

| i   | str[i - 1] | str[i] | New Word? | wordStart |
| --- | ---------- | ------ | --------- | --------- |
| 1   | a          | b      | No        | 0         |
| 2   | b          | c      | No        | 0         |
| 3   | c          | \_     | No        | 0         |

So, it is correct again.

Conclusion, it is important that you can describe your solution using English and work out the solution on paper. This skill is the bread and butter for your future job interview also.

## Exam Simulator

Lots of people failed the exam and lots of them failed multiple times. Each time I fail, I learn where my knowledge gap is. I fill the gap and take the exam again, hoping to identify my next knowledge gap. When there is no knowledge gap, I pass the exam. Thus, my strategy is to fail a lot as fast as possible. To do this, I need [Exam Simulator](https://github.com/JCluzet/42_EXAM). Here are steps to open `Exam Simulator` quickly.

**Step 1**
Append `alias exam='bash -c "$(curl https://grademe.fr)"'` to your `~/.bashrc`

**Step 2**
Open a new terminal or run `source ~/.bashrc` in your current terminal

**Step 3**
Open the `Exam Simulator` by typing `exam` in your terminal

## Tips && Tricks

- Log in the exam computer using `user: exam` & `pass: exam`, not your account user & pass

- Once inside the exam computer, open a terminal and run `examshell` and log in using your account user & pass

- To quickly build and test your program, you should set alias at the beginning of the examination

```bash
alias build='cc -g -std=c99 -Wall -Wextra -Werror'
alias debug='gdb -tui --args'
```

To build a `.c` file

```bash
build main.c
```

To debug executable file without command line arguments

```bash
debug a.out
```

To debug executable file including command line arguments

```bash
debug a.out param1 param2
```
