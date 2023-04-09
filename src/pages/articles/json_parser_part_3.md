---
title: "Json Parser: part 3: Implementation"
date: 2022-04-07
layout: ../../layouts/MainLayoutWithNav.astro
---

## TLDR

I explain the logic I used to parser and serialize JSON. 
JSON has non-primitive data types. So, there is nesting. I could have used the similar technique that I describe in [part 1](/p_blog/articles/json_parser_part_1) of this series; that is: have functions taking care of each data entity and they recursively call each other going through the JSON string.

## Serialization

![recursive illustration](/p_blog/assets/rec_ill.svg)

But I wanted to make the parser performant and resilient. There would be a lot of function calls and that would slow down our program. And on top of that the calls are recursive. That means our program becomes dependent on the stack size of the operating system. A JSON string that is heavily nested may cause StackOverflow!

According to the crude test I ran in my Linux env without changing any stack settings, I got this result.

![stack overflow](/p_blog/assets/stack_test.png)

The limit is around 261K recursive function calls. I haven't seen any JSON file nested on 261K levels but the limit still made me uncomfortable.

I knew that the same effect as recursion can be achieved by keeping track of context in the stack data structure.

I used `std::vector` to implement the stack. Now, we are limited by the maximum size of the vector container in cpp; that is, the output is given by `std::vector::max_size()` which on my system came out `1152921504606846975` ~ 10^18. That would serve as a maximum limit approximately for other container types too; which means max such number of array elements.

![stack overflow](/p_blog/assets/shapes_non_rec.svg)

If we encounter primitive data types we pushed to the mouth which pointed to the top of the stack, else if we encountered a container we updated the stack. And pop from the stack when a container ended.

I used `std::shared_ptr` for pointers, so there is no manual memory management. And the private value inside the object that held the actual values is not dynamically allocated; which meant the value would be destroyed if the object is destroyed.

Now that I had built a parser/serializer according to the JSON [spec](https://www.json.org/json-en.html), I thought it would be a good idea to test it against some sophisticated test cases. So, I searched the internet and found [this](https://github.com/nst/JSONTestSuite/tree/master/test_parsing) repository where they had a bunch of test cases.

With some code changes over a week, I was able to pass all the test cases.

<video src="/p_blog/assets/running_tests.webm" width="100%" controls> </video>

I have also added functionality to pretty print JSON just like [jq](https://github.com/stedolan/jq)!.

![pretty print](/p_blog/assets/pretty_print.png)

For small files, it surpasses JSON.stringify of JS in the speed of serialization. However, for too big strings: like > 20MB, it is slower than `JSON.parse`, but not too much. That's because v8 used by JS heavily optimizes strings and I am using `std::string` in my program. A slight overhead is also added because of the use of smart\_pointers.

Here is the parse speed comparison against JSON.parse for a large test file of ~ 32MB.

![performance comparison](/p_blog/assets/performance_against_json_parse.png)

To make sure there aren't any memory leaks, I made the parser parse the same 32MB file and ran the process through Valgrind. Thankfully, there weren't any leaks.

![memory leak check](/p_blog/assets/leak.png)

I plan on improving the performance of the parser in the future. I tried to use raw pointers and manual memory management on a different branch, but it only bought an improvement of 10 ms. So, I abandoned the idea.
