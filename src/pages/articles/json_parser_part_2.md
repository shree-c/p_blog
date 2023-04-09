---
title: "Json Parser: part 2: Figuring out data types"
date: 2022-04-07
layout: ../../layouts/MainLayoutWithNav.astro
---

## TLDR

I explain how I organized data to serialize JSON.

## Data types

JSON has both primitive and container data types. Primitives include boolean, number, and string. Container data types include dictionary/object, and array.

I was using CPP. The first problem I faced was: how to organize data. CPP is a strongly typed language, but containers in JSON can store data of different types. The containers can only have data of the same type in cpp containers. So, how are we going to tackle the problem of storing heterogeneous data in cpp containers? I googled the problem and found some helpful [StackOverflow](https://stackoverflow.com/questions/19543326/datatypes-for-representing-json-in-c) answers.

The answer is polymorphism. I made a generic base class called JsonEntity and made different classes for each of the datatypes in JSON, such as JsonBool for representing boolean, JsonObj for representing objects, JsonNumber for representing Numbers, and so on. These classes for data types inherit the generic base class JsonEntity. And I stored a pointer to JsonEntity inside a vector for implementing Json Arrays, or a map to implement Json Objects.

![class diagram](/assets/json.drawio.svg)

The next natural problem is: how are we going to determine the type of generic pointers in containers. The answer is: to add a method called `get_type` to each type object which returned an enum signifying type. And then, we would cast the pointer to its type before working with it.
Thus we have solved the problem of managing heterogeneous data types. Now the work left is to validate the JSON string and serialize the data to native data types so that it can be consumed in any cpp application.

In the next article, I am going to explain the approach I use to serialize data. _spoiler: I don't use recursion_

[Json Parser: part 3: Implementation](/p_blog/articles/json_parser_part_3)
