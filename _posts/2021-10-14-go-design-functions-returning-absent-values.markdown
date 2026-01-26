---
layout: single
title:  "Designing functions that return potentially absent values in Go"
date:   2021-10-14 18:00:00 +0300
tags: golang software-patterns
header:
  image: assets/images/find-functions-in-go.webp
toc: true
---

This article presents a micro-level design pattern to indicate the absence of function return values idiomatically in Go.

# Introduction

Code readability is one of the most critical properties of maintainable software and a significant factor in reducing cost in software development[^readability]. It allows developers to correctly understand how software behaves with less effort while also preventing errors by eliminating wrong assumptions.

Software engineers achieve readability by introducing naming conventions, documenting, commenting code, formating, and organizing code. While most of these practices are standard and followed in modern software development, the design of micro-level components is often overlooked.

An important principle that greatly improves readability and prevents errors when designing software at a micro-level (i.e., functions) is the **Principle of Least Astonishment**[^POLA].
> A component of a system should behave in a way that most users will expect it to behave.

As developers, we write code that will be read, consumed, and used by other colleagues and our future selves. Therefore, when we design even a simple function that is part of an API, it is crucial to put the effort to make functions deliver their expected behavior just by looking at their definition.

The factors affecting how a function is perceived can be its name, location within a project (package), and signature. 

This article focuses on the signature factor of **functions that return potentially absent values**. For the rest of this article, we will refer to these types of functions as ***'Find Functions'***.

**Find Functions in OOP vs Go**

In the Object-Oriented world, engineers don't have to think much about designing a function that returns an item if found. It is pretty straightforward:
- Return a *null reference* if the item is not found
- Return the *object reference* if the item is found
- If an unexpected error occurs during the finding operation, throw/propagate the corresponding ***exception***.

In Go, things are a bit different. Go supports pointers but doesn't have the notion of exceptions. However, Go has two features/tools that allow us to design functions differently:
*  *multiple return values* 
*  *error* type
  
These two features give the flexibility to design functions with different signatures that all achieve the same result.

In the following sections, we explore recommended and non-recommended signatures of 'Find Functions'.

# The value, error idiom
Firstly, let's introduce an idiom that is commonly found in Go.

In Go, functions that return a value with a possibility of an error have the following signature:
```go
func funcName(param Type) (ReturnType, error){}

//caller
val, err := funcName(input)
if err != nil{
    return err
}
```
The above function returns the value with an `error = nil` if no error occurs and returns a non-nil error if an error occurs.

We expect callers of this function **always** to check the `error` return value to ensure that no error occurred during the requested operation.

This design is often referred to as the *'value error'* idiom.

# **Pattern to avoid #1:** Indicate absence by returning a zero value

*Why don't we use the value, error idiom and return a zero value if the value is not found?*

Here is an example of how this would look like:

```go
func FindSomethingById(id int) (Something, error){}

//caller
val, err := FindSomethingById(myID)
if err != nil{
    return err
}
if (val == Something{}){
    //missing value
}
```
<br>
**"*As a developer, what do I expect by looking at this function signature?*"**

My thoughts:
1. The function accepts `id` as an input
2. If the `id` exists, the function will return the `Something` corresponding to the requested `id` and a `nil` `error`
3. If the `id` does not exist or something wrong occurs, the function will return an `error` and an **irrelevant `Something` value**

<br>
**Problem #1:** Unclear Expected Behavior

As we can see, just by looking at the signature, we don't get any information about the behavior of this function when the value of id is missing. Therefore **we can assume** that it is an unsupported use case for this function, and an error will be returned in this case. And then, we may assume that we don't need to check for a zero value.

**Problem #2**: A zero value could be a valid value

For many functions, a zero value could be a valid return value. I have also seen cases in which a zero value was initially not valid but became valid as the software with use cases grew. 

Based on the above, we can see that using zero values for signifying absence is unclear and can deliver wrong assumptions. If we want to be precise, we need to find better ways to indicate absence.

# **Pattern to avoid #2:** Indicate absence by returning an error

In this approach, the function returns a custom error if the element is not found.

```go
// Find Function
notFoundErr := errors.New("value not found")
func getMeSomething(id int) (*ValueType, error){
    //search for item
    if !found {
        return nil, notFoundErr
    }
    return &value, nil
}

//Caller
func caller() error{
    item,err := getMeSomething(1)
    if err != nil{
        if err != notFoundErr{
            return err
        }
        //not found - handled logic
    }
    // continue with found value
}
```
While the above code provides the required information to the caller, this approach should be avoided for Find Functions.

**Why should we avoid this?**

**Problem #1** The Function Signature is not Precise

The 'Not found' scenario is not unexpected in 'Find functions'. The absence of a value is a valid result of the function. Representing an expected returned value within an error can reduce clarity. Errors should only be returned in case of unexpected scenarios.

**Problem #2:** Caller Usage - Nested Conditions

In this approach, callers have to use complicated nested conditions to handle this valid use case. This results in degraded readability since the normal flow is handled as an edge case scenario.

It is important to note that functions using this design may be perfectly idiomatic if a not-found error *is unexpected*. However, this is not the case for Find Functions as per the definition. 

# **Recommended Pattern #1:** Indicate absence by returning `nil`

When the function we design *naturally* returns a pointer or a reference type, we can indicate absence by returning `nil` if the value is not found.

Here is an example:

```go

// reference type
func findSomethingById(id int) (value []int, err error){
    // search for item
    if err != nil {
        return nil, err
    }
    
    return value // []int or nil
}

// pointer
func findSomethingElseById(id int) (value *Struct, err error){
    //search for item
    if err != nil{
        return nil, err
    }
    
    return value *struct or nil
}

//caller

val, err := findSomethingById(1);
if err != nil{
    return err
}
if val == nil{
    //not found logic
}
//found logic
```
**Pros**

1. **Conciseness**\
    The signature is simple and concise. There is no need for extra parameters; we can return `nil` on the already existing reference that we return, which is just enough.
2. **Expected behavior**\
    In the above example, the function signature is clear: the function returns a pointer; `nil` is a possible value that can describe absence.

**Cons**

**Nil Check Ignorance**\
While `nil` is a value that can indicate absence, developers can *forget/miss* the check for this use case as it is not enforced.

## Why don't we use the 'pointer, ok, error' idiom
Why don't we use the 'comma ok idiom' in these cases?

Let's try this with an example:
```go
func getMeSomething(id int) (*Struct, bool, error);

//caller
val, found, err := getMeSomething(1);
if err != nil{
    return err
}
if !found {
    // not found logic
    if val == nil{
    //not found - nil logic
    }
    if val != nil {
    //not found - not nil logic
    }
}

// found - not nil logic
// here, we trust the function - val is not expected to be nil - we need another check to ensure that
```

As we can see from this example, by introducing the `found` flag, we increase the complexity and the number of cases we need to check. 

Let's see what the possible cases are:

|| value | found |
|-|-|-|
|Case A|non-nil| true|
|Case B|non-nil| false|
|Case C|nil| true|
|Case D|nil| false|

**In most cases, the found flag is redundant**\
If the function we design does not support Case B or Case C, the above method introduces unnecessary complexity. The found return value is redundant and can be unclear. Redundant code can be dangerous since it makes code more error-prone. At the same time, this redundancy can cause confusing assumptions.

**In rare cases, the pointer/reference bool error is necessary**\
Although the found flag is redundant in most cases, we have cases where it is necessary. It is essential when cases B or C are possible outputs of the function.

# **Recommended Pattern #2:** Indicate absence by returning the 'value found error' idiom

When the returned struct type is immutable, we rarely use pointers. This is also true for primitive types.

In this case, the 'value, found, error' idiom (similar to comma ok) is recommended. It is actually a more robust way to represent absence since developers have to ignore the value explicitly if they do not want to check for a not-found scenario. For example:

```go
func getMeStructValue(id int) (Struct, bool, error){}
func getMeAnInt(id int) (int, bool, error){}

//caller

val1, found, err := getMeStructValue(1); // don't ignore found flag
val2, _, err := getMeAnInt(1) // ignore found flag

if err != nil{
    return err
}
if !ok {
    // not found logic
}

// found logic
```

This approach has two benefits:
1. The main benefit is that it follows the principle of least astonishment[^POLA]; callers can understand how it works just by looking at the signature.
2. Another important benefit is that this approach forces the caller to handle or explicitly ignore the `found` value.

## Why don't we convert the returned value to a pointer to indicate absence with nil
Why not always use a pointer on the primitive type and avoid *value, found, error* idiom? For example:
```go
func getMeSomething(id int) (*int, error);

val, err := getMeSomething(1)

if val == nil{
    //not found
}
//found
```
Pointers of values express mutability. They can also be used for performance purposes, i.e., when passing large structs. Returning the pointer just for indicating absence can be confusing and can have further performance implications. [^pointers]

In addition, it is generally clearer to use the *value, found, error* idiom to indicate absence since it forces callers to ignore the absence check if they need to explicitly. This is not true for nil checks which are in the developer's discretion to implement.

# TLDR; Do's and Don'ts of Find Functions

**DO** return a `nil` value when the function returns a pointer or a reference type.

**DO** use the 'value, found, error' idiom when the function returns a primitive type or a non-mutable struct.

**DON'T** use a zero value to indicate absence unless you have a good reason.

**DON'T** use the 'pointer, found, error' idiom if a `nil` return value is never returned in combination with a `found=true` value.

**DON'T** use a pointer only for the purpose of indicating an absent value with `nil`.


# Thoughts
The above guidelines present my point of view on how things can be more idiomatic and robust when designing Find Functions. 

I understand that fellow engineers could have a different opinion, and that's, of course, fine. For example, some gophers could argue that the 'pointer everything to indicate absence' approach is more elegant, and the added condition complexity is better than 'polluting' the functions with a *found* flag.

Although I'm afraid I have to disagree with the above for the reasons and arguments that I clearly stated in this article, as in most cases, the most important aspect is consensus and consistency within a project/team.

# Conclusion
Although there are many ways to design Find Functions in Go, it is important for developers who want to expose an API to be precise, promote readability and provide a design that prevents errors (POLA principle).

We have presented two recommended approaches of designing Find Functions using the pointer, error idiom and the value, found, error idiom depending on the return type. We also saw why we should avoid indicating expected results through errors and the problem with indicating absence with zero values.

# References
[^readability]: [Collar, Emilio & Valerdi, Ricardo. (2014). Role of Software Readability on Software Development Cost.](https://www.researchgate.net/publication/249849316_Role_of_Software_Readability_on_Software_Development_Cost#:~:text=Since%20readability%20can%20affect%20maintainability,may%20affect%20readability%20of%20code.)
[^POLA]: [Principle of Least Astonishment](https://en.wikipedia.org/wiki/Principle_of_least_astonishment)
[^pointers]: [Using pointers in go by Ardan Labs](https://www.ardanlabs.com/blog/2014/12/using-pointers-in-go.html)
