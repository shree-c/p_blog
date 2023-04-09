---
title: "Json Parser: part 1: Background"
date: 2022-04-01
layout: ../../layouts/MainLayoutWithNav.astro
---

## TLDR

How one day I felt like building a JSON parser while trying to understand an example from [K and R](https://en.wikipedia.org/wiki/The_C_Programming_Language) book and my journey of building one.

## Background

I bought K and R book at the start of my programming journey. C is the first programming language I tried to learn. The book turned out difficult to comprehend as a beginner; I abandoned it just within a day. Later, as I gained some understanding of the basics of programming from the other more beginner-friendly [book](https://www.amazon.in/Let-Us-C-Yashavant-Kanetkar/dp/8183331637) from an Indian author, I started reading K and R again. I understood most parts of it, except for a few difficult examples.

One example that I found particularly difficult was a program that converts a C declaration into a word description. In C, the declarations of variables can become complicated if the variable is a pointer to a function, and more so if that function returns something, say: a pointer to an array, etc. Such declarations are difficult to understand because they cannot be read straight from left to right.

```C
int *int_ptr // description: pointer to int
void (*comp)() // description: pointer to function returning void
char (*(*x())[]) () // description: function returning pointer to array[] of pointer to function returning char


```

The program is based on the declaration grammar of C. It went over my head when I was reading it two years back. I moved on to trying to learn other stuff.

I was however unsatisfied that I never completed the OG book. There was still that program I did not understand. Now that, after two years, I had gained a bit of confidence in my programming skills, I tried to understand the program again.

Here is the simplified [grammar](https://en.wikipedia.org/wiki/Formal_grammar) for declarations in C taken from the book:

```

dcl: optional *'s direct-dcl
direct-dcl: name or
            (dcl) or
            direct-dcl() or
            direct-dcl[optional-size] or

```

<!-- You can think of it as the whole declaration consisting of two entities that are defined in terms of each other. The name above is not a variable name, it is void | int | double etc. -->

The program consisted of a tokenizer, which divided the input into tokens such as parenthesis, brackets, names, etc. Two functions: `dcl()` and `dir-dcl()`, that called each other; in the same way they are defined in terms of each other in the grammar above.

The concept that I found a bit difficult was: two functions calling each other. I knew it is allowed but I had never come across such usage till then.

I was always intrigued by how compilers and interpreters understood the text given to them. It all felt like magic when I compiled a program or ran a script. Understanding this program was like the Ahaha moment. Dividing the input into tokens, and making sense of the arrangement of those tokens is so beautiful.

I tried to write a similar program to parse a correct arithmetic equation following a similar pattern.

I had come across a lot of JSON in web programming. I thought why not parse JSON; it would be a significant confidence booster if I successfully do so.

I went along with a similar idea: dividing the input into tokens, and functions calling each other based on grammar.

I chose CPP because it would be nicer to have classes and containers around.

![Naive implementation](/p_blog/assets/test.drawio.svg)

Here is a naive implementation of the parser. This doesn't involve serializing data. All the program does is takes input and check whether it is in valid format; in our case, valid JSON format.

The tokenizer consumed input and classified it into different types. All functions throw errors if there is any unexpected input indicating the text input is in the incorrect format. I used enums to represent token classifications.

In the next part, we shall see how I used CPP data structures to serialize JSON to native types and tackled a few interesting problems that came with it.

[JSON Parser: part 2: Figuring out data types](/p_blog/articles/json_parser_part_2)
