---
layout: single
title:  "Implementing a simple, generic mediator pattern in go"
date:   2022-12-18 23:50:00 +0300
tags: software-patterns golang
toc: true
header:
    og_image: /assets/images/Mediator-GenericMediator.webp
# classes: wide
---
This article presents a simple implementation of a generic mediator in Go.

# Introduction
As software systems become larger and more complex, **managing the interaction dependencies between different components can become complicated**. Refactoring and introducing changes can also become particularly challenging and error-prone when the interaction semantics change.

<p align="center">
  <img alt="dependencies-everywhere" src="/assets/images/Dependencies-Dependencies-Everywhere.webp" width="200">
  <br>
    <small>Image by <a href="https://github.com/MariaLetta/free-gophers-pack"> Maria Letta @free-gophers-pack</a></small>
</p> 

This is a common problem in software engineering and can be addressed by different techniques that attempt to decouple the components and reduce the dependencies to the maximum degree.

As more and more components are added as part of an extensive system, a commonly followed approach is the introduction of different layers of abstraction that separate the domain logic from business logic and the infrastructure. For example, Clean Architecture[^clean-architecture] is a well-known design philosophy that follows this approach.

The abstraction in layers can enable some control of the interaction between the different components of the system by following rules that make the dependency graph acyclic and manageable. However, the components still need to be aware of each other and depend on each other to some degree for communication purposes.

\- **Can we maybe *hide* these dependencies**❓ So, on top of abstracting the layers of the system based on some rules, *can we also abstract their interaction*❓

The answer is yes!

We can do this by using an incredible design pattern that encapsulates the interaction and abstracts the communication between components: **mediator pattern**.

<p align="center">
  <img alt="idea-mediator" src="/assets/images/lamp.webp" width="200">
  <br>
    <small>Image by <a href="https://github.com/MariaLetta/free-gophers-pack"> Maria Letta @free-gophers-pack</a></small>
</p>
The mediator pattern and flavors we will see in this article can be a precious tool in projects that follow a layered approach with well-defined bounds. It's also incredibly beneficial when combined with the CQRS[^cqrs] pattern.

In the following sections, we’ll explore the following:
1. What is the generic mediator pattern?
2. A simple implementation of the generic mediator in Go

Enjoy!

# The mediator pattern

Before we jump into the implementation of the mediator pattern in go, let’s briefly describe what the mediator pattern is.

*The mediator pattern is a behavioral design pattern originally proposed by the Gang of Four[^GoF], which attempts to address the communication/interaction complexity by encapsulating the interaction of objects within a mediator object*[^Wikipedia-Mediator].

The main things to remember here are:

1. The objects interact through the mediator, not directly with each other.
2. The mediator is responsible to *route* the requests to the corresponding operation handler and returning the result to the caller.

**What does this achieve❓**

\- The caller does not know anything about the operation handler, and the operation handler does not know anything about the caller.

## The original definition of the mediator pattern

The original definition of the mediator looks like this:
<p align="center">
  <img alt="original-mediator" src="/assets/images/Mediator-OriginalMediator.webp">
    <br>
  <small>The Mediator Pattern</small>
</p> 

- It involves a mediator interface and a concrete mediator that is responsible for the participants' (colleagues) interaction
- The mediator serves/implements the actual interactions between the two colleagues
- The caller makes the calls to the mediator
- The handler receives calls from the mediator
- Once the handler finishes the operation, it notifies the caller through the mediator


*For a complete definition of the mediator pattern, I would highly recommend the Wikipedia definition [here]([https://en.wikipedia.org/wiki/Mediator_pattern](https://en.wikipedia.org/wiki/Mediator_pattern)).*

## The generic, dynamic mediator
In this article, we will explore a different flavor of the original mediator pattern that works in a dynamic and generic way.
I heavily used this pattern in the MediatR[^mediatr] project by [Jimmy Bogard](https://github.com/jbogard) while working with C# projects.

Cool, how does it work, then?

1. **Handlers** dynamically **register** through the **mediator** interface to serve specific **request types** on **runtime**
2. **Callers** make **requests** through the **mediator** interace by passing a **request object**.
   - The **mediator** knows which **handler** should serve the **request** because it has already been registered to serve these specific **request types**.

<p align="center">
  <img alt="generic-mediator" src="/assets/images/Mediator-GenericMediator.webp">
  <br>
  <small>The Generic Mediator</small>
</p> 

\- **Awesome, what do we need to implement this❓**
1. An commonly agreed **handler interface** that handlers will need to implement to server-specific request types
2. A **mediator** object that exposes
   1. A **Register** method that will allow handlers to register themselves for specific request types dynamically
   2. A **Send** method that will allow callers to make requests
3. Both the handler and the caller should know the **request** model

### Wait, is this really a mediator pattern? 🤔

Some may argue that this is not the mediator pattern, and they can be somewhat correct since it doesn't match the exact proposed implementation.

Technically, this is probably a valid argument. However, it solves the same problem that the mediator tries to solve: simplify dependencies and increase maintainability using the power of generics and dynamic registration.

*For those interested in following some arguments about this, you can find an interesting article [here](https://jimmybogard.com/you-probably-dont-need-to-worry-about-mediatr/) 🍿*

## Pros and Cons of the mediator pattern

### Pros
The primary benefit of the mediator pattern is that it reduces coupling which results in the following:

1. Easier to manage dependencies
2. Higher maintainability

### Cons

As with most things in software engineering, the mediator has some tradeoffs.

#### Too much magic

🪄*Too much magic!* 🪄

I call a mediator, and the correct handler magically executes the call. Why is the logic hidden from me?

That's a valid question! The caller does not have knowledge of the handler before the code runs, which inhibits readability and can make troubleshooting difficult.

Well, this is a tradeoff. This can be considered too much magic for some (Gophers, in particular, seem to hate magic), but at the same time it provides the benefits that we have already explored. 🤷

#### Missing references in my IDE

Since the generic mediator is responsible for dynamically figuring out the handler of each request, developers lose the referencing features of the IDEs. 

This can be frustrating for troubleshooting and refactoring purposes.🤷

# Implementing the generic Mediator pattern in go

Let’s implement the generic mediator in go!

## Source Code 🗒️

You can find the source code of this simple implementation here: [https://github.com/pkritiotis/go-mediator](https://github.com/pkritiotis/go-mediator)

## The `go-mediator` package 📦

The basic components of the `go-mediator` package are the following:

1. The **`RequestHandler`** interface provides the definition of the method that needs to be implemented by components that handle *Requests*.
2. The **`Register`** generic method to map the `RequestHandler` to a specific **`Request`** and **`Result`**
3. The **`Send`** generic method to send the input `Request`s that return a **`TResult, error`**

Let’s check each component in detail.

## The `RequestHandler` interface

The `RequestHandler` interface defines the **generic handler method** that accepts a `TRequest` and returns a `(TResult, error)`. 

This is the interface that needs to be implemented by the *Request Handler*  and the interface that the mediator can ***register*** and route the ***Requests*** to.

```go
type RequestHandler[TRequest any, TResult any] interface {
	Handle(request TRequest) (TResult, error)
}
```

## Registering handlers using the `Register` method

The `mediator` package is stateful. The state is required to hold the registered **Handlers**  in a `[sync.Map](http://sync.Map)` that is used for routing the mediator request to the correct handler.

```go
var (
	registeredHandlers sync.Map
)

func init() {
	registeredHandlers = sync.Map{}
}

type key[TRequest any, TResult any] struct {}
```

The `key` struct represents the key used for the registered handlers. The pair of request type and result type allows us to support different handlers that accept the same request but return a different result.

The handlers can be registered using the `Register` method:

```go
// Register registers the provided request handler to be used for the corresponding requests
func Register[TRequest any, TResult any](handler RequestHandler[TRequest, TResult]) error {
	var req TRequest
	var res TResult
	k := key[TRequest, TResult]{}

	_, existed := registeredHandlers.LoadOrStore(reflect.TypeOf(k), handler)
	if existed {
		return errors.New("the provided type is already registered to a handler")
	}
	return nil
}
```

The only validation we need at this point is that another handler with the same `TRequest` and `TResult` is not already registered.

The reason for checking this is that if we had more than one handler, we would not know how to determine which one to call.

### Sending request via the mediator

Callers need to call the `Send` method to send requests via the mediator.

The `Send` method is implemented as follows:

```go
// Send processes the provided request and returns the produced result
func Send[TRequest any, TResult any](r TRequest) (TResult, error) {
	var zeroRes TResult
	var k key[TRequest, TResult]
	handler, ok := registeredHandlers.Load(reflect.TypeOf(k))
	if !ok {
		return zeroRes, errors.New("could not find zeroRes handler for this function")
	}
	switch handler := handler.(type) {
	case RequestHandler[TRequest, TResult]:
		return handler.Handle(r)
	}
	return zeroRes, errors.New("Invalid handler")
}
```

Based on the requested `Request` and `Result`, we try to load and cast the corresponding registered handler. 

If something goes wrong, we return an error. Otherwise, we call the `Handle` method of the registered handler.

## Using the `go-mediator`

To use the go-mediator package, we need the following:

1. Implement the `RequestHandler` interface for a specific **Request** that returns the respective **Response**.
2. Register the handler using the `mediator.Register` method
3. Call the `mediator.Send` providing the corresponding **Request**

### Source code

The source code of this example is here: [https://github.com/pkritiotis/go-mediator/tree/main/examples/notes-http-example](https://github.com/pkritiotis/go-mediator/tree/main/examples/notes-http-example)

### Example - Notes Http API Project

Let’s see the above in action using a minimal example of a notes app that exposes an endpoint that returns the list of in-memory notes.

#### Project Structure
The structure of this sample project follows a minimalistic version of Clean Architecture, with the app layer containing the business logic and the infra layer containing the output port and adapter for exposing notes on an HTTP API.

```go
.
├── app
│   └── notes.go
├── infra
│   └── http
│       ├── server.go
│       └── notes
│           └── handler.go
└── main.go
```

In a nutshell

- the `app/notes.go` contains the query `GetNotesRequestHandler` that processes the `GetNotesRequest`
  - It uses the `go-mediator` package to register the handler to the `mediator`.
- the `infra/http/server.go` spins up an http server which uses the `infra/http/notes/handler.go` HTTP handler that makes the `GetNotesRequest`.
  - It uses the `go-mediator` package to send the requests and get the `[]Note` result

Here is a sample diagram of the relations between the different components:
<p align="center">
  <img alt="notes-generic" src="/assets/images/Mediator-NotesExample-GenericMediator.webp">  
  <br>
  <small>Notes Example - Component Interaction</small> 
</p> 

The following diagram shows the execution flow looks like this:

<p align="center">
  <img alt="notes-mediator-flow" src="/assets/images/Mediator-NotesExample-Mediator.webp"> 
  <br>
  <small>Notes Example - Execution flow</small> 
</p> 



#### The Request Handler implementation

The request handler in our application is `app.GetAllNotesRequestHandler`

```go
func init() {
	err := mediator.Register[GetAllNotesRequest, []Note](getAllNotesRequestHandler{})
	if err != nil {
		panic(err)
	}
}

// GetAllNotesRequest contains the request params
type GetAllNotesRequest struct {
	Ctx context.Context
}

// Note is the result of the GetAllNotesRequest Query
type Note struct {
	ID        uuid.UUID
	Name      string
	Contents  string
	CreatedAt time.Time
}

// GetAllNotesRequestHandler Contains the dependencies of the Handler
type GetAllNotesRequestHandler interface {
	Handle() ([]Note, error)
}

type getAllNotesRequestHandler struct {
}

// Handle Handles the query
func (h getAllNotesRequestHandler) Handle(request GetAllNotesRequest) ([]Note, error) {

	result := []Note{
		{
			ID:        uuid.New(),
			Name:      "sample name",
			Contents:  "sample content",
			CreatedAt: time.Now(),
		},
	}

	return result, nil
}
```

- The `GetAllNotesRequest` and `Note` represent the request and response of this handler, respectively.
- The `getAllNotesRequestHandler` implements the `Handle` function of the `mediator.RequestHandler` interface
- The handler is registered by calling
    - `mediator.Register[GetAllNotesRequest, []Note](getAllNotesRequestHandler{})`

### Calling the mediator

The HTTP handler calls the mediator via the mediator in `infra/notes/handler.go`

```go
// GetAll Returns all available notes
func GetAll(w http.ResponseWriter, r *http.Request) {
	req := app.GetAllNotesRequest{Ctx: r.Context()}
	notes, err := mediator.Send[app.GetAllNotesRequest, []app.Note](req)
	if err != nil {
		w.WriteHeader(http.StatusInternalServerError)
		fmt.Fprintf(w, err.Error())
		return
	}

	err = json.NewEncoder(w).Encode(crags)
	if err != nil {
		return
	}
}
```

The caller

1. Initializes the request in `req := app.GetAllNotesRequest{Ctx: r.Context()}`
2. Executes the request and gets the result with `notes, err := mediator.Send[app.GetAllNotesRequest, []app.Note](req)`

Note that there is no knowledge of the request handler at this point. The HTTP handler is only aware of the request model.

# Improving `go-mediator`

The `go-mediator` is a minimal sample implementation of the mediator pattern. Let’s explore a couple of possible improvements.

## Introducing a `CommandHandler`  interface

Currently, the `go-mediator` only supports request handlers that always return a `TResult, error`. 

If we want to be stricter, we can introduce a CommandHandler interface that is almost the same as the `RequestHandler` interface; the only difference is that it does not return a `TResult`

```go
type CommandHandler[TRequest any] interface {
	Handle(request TRequest) error
}
```

With this interface, we would also need to include the corresponding `Register` method that accepts a `CommandHandler`.

## Asynchronous mediator call

Another possible improvement in the mediator pattern is introducing an asynchronous API for mediator calls.

```go
func Send[TRequest any, TResult any](req TRequest) (chan<- TResult, chan<- error) {
 //[..]
}

//or

func Send[TRequest any, TResult any](req TRequest, resChan chan<- TResult, errChan chan<- error) {
 //[..]
}
```

Instead of having the `Send` blocking call, we could allow executing the `Send` to return a channel that will provide the result of the operation once completed.
The asynchronous goroutine call can be implemented by the mediator itself or by the caller, depending on which of the two options we follow.
# Conclusion

In this article, we have seen how the mediator pattern can help us simplify the communication between different components by abstracting their interaction.

We have seen the original mediator pattern definition and the generic flavor of the mediator. We have demonstrated how we can implement the mediator pattern in a straightforward, minimalistic approach in go, using generics.

Off you go! Clone it, fork it, experiment with it! Happy mediation!

# References
[^GoF]: [Gang of Four](http://wiki.c2.com/?GangOfFour)
[^Wikipedia-Mediator]: [Mediator pattern - Wikipedia](https://en.wikipedia.org/wiki/Mediator_pattern)
[^cqrs]: [CQRS by Martin Fowler](https://martinfowler.com/bliki/CQRS.html)
[^mediatr]: MediatR- Simple Mediator in .NET [Gihub Repo](https://github.com/jbogard/MediatR)
[^clean-architecture]: [Clean Architecture in Go](https://pkritiotis.io/clean-architecture-in-golang/)