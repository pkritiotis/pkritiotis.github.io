---
layout: single
title:  "Implementing a simple, generic, command dispatcher pattern in go"
date:   2022-12-18 23:50:00 +0300
tags: software-patterns golang
toc: true
header:
    og_image: /assets/images/CommandDispatcher-GenericDispatcher.png
# classes: wide
---
This article presents a simple implementation of a generic command dispatcher in Go.

# Introduction
As software systems become larger and more complex, **managing the interaction dependencies between different components can become complicated**. Refactoring and introducing changes can also become particularly challenging and error-prone when the interaction semantics change.

<p align="center">
  <img alt="dependencies-everywhere" src="/assets/images/Dependencies-Dependencies-Everywhere.png" width="200">
  <br>
    <small>Image by <a href="https://github.com/MariaLetta/free-gophers-pack"> Maria Letta @free-gophers-pack</a></small>
</p> 

This is a common problem in software engineering and can be addressed by different techniques that attempt to decouple the components and reduce the dependencies to the maximum degree.

As more and more components are added as part of an extensive system, a commonly followed approach is the introduction of different layers of abstraction that separate the domain logic from business logic and the infrastructure. For example, Clean Architecture[^clean-architecture] is a well-known design philosophy that follows this approach.

The abstraction in layers can enable some control of the interaction between the different components of the system by following rules that make the dependency graph acyclic and manageable. However, the components still need to be aware of each other and depend on each other to some degree for communication purposes.

\- **Can we maybe *hide* these dependencies**❓ So, on top of abstracting the layers of the system based on some rules, *can we also abstract their interaction*❓

The answer is yes!

We can do this by using an incredible design pattern that encapsulates the interaction and abstracts the communication between components: **command dispatcher pattern**.

<p align="center">
  <img alt="idea-pattern" src="/assets/images/lamp.png" width="200">
  <br>
    <small>Image by <a href="https://github.com/MariaLetta/free-gophers-pack"> Maria Letta @free-gophers-pack</a></small>
</p>
The command dispatcher pattern we will see in this article can be a precious tool in projects that follow a layered approach with well-defined bounds. It's also incredibly beneficial when combined with the CQRS[^cqrs] pattern.

In the following sections, we’ll explore the following:
1. What is the command dispatcher pattern?
2. A simple implementation of the generic command dispatcher pattern in Go

Enjoy!

# The command dispatcher pattern

Before we jump into the implementation of the command dispatcher pattern in go, let’s briefly describe what the command dispatcher pattern is.

*The command dispatcher pattern is a behavioral design pattern which attempts to address the communication/interaction complexity by encapsulating the interaction of objects through a dispatcher object*.[^CommandDispatcher]

The main things to remember here are:

1. The objects interact through the *dispatcher*, not directly with each other.
2. The dispatcher is responsible to *route* the requests to the corresponding operation handler and returning the result to the caller.

**What does this achieve❓**

\- The caller does not know anything about the operation handler, and the operation handler does not know anything about the caller.


**Cool, how does it work, then?**

1. **Handlers** dynamically **register** through the **dispatcher** interface to serve specific **request types** on **runtime**
2. **Callers** make **requests** through the **dispatcher** interface by passing a **request object**.
   - The **dispatcher** knows which **handler** should serve the **request** because it has already been registered to serve these specific **request types**.

<p align="center">
  <img alt="generic-command-dispatcher" src="/assets/images/CommandDispatcher-GenericDispatcher.png">
  <br>
  <small>Command Dispatcher</small>
</p> 

\- **Awesome, what do we need to implement this❓**
1. An commonly agreed **handler interface** that handlers will need to implement to server-specific request types
2. A **dispatcher** object that exposes
   1. A **Register** method that will allow handlers to register themselves for specific request types dynamically
   2. A **Send** method that will allow callers to make requests
3. Both the handler and the caller should know the **request** model

## Pros and Cons of the command dispatcher pattern

### Pros
The primary benefit of the command dispatcher pattern is that it reduces coupling which results in the following:

1. Easier to manage dependencies
2. Higher maintainability

### Cons

As with most things in software engineering, the command dispatcher has some tradeoffs.

#### Too much magic

🪄*Too much magic!* 🪄

I call a dispatcher, and the correct handler magically executes the call. Why is the logic hidden from me?

That's a valid question! The caller does not have knowledge of the handler before the code runs, which inhibits readability and can make troubleshooting difficult.

Well, this is a tradeoff. This can be considered too much magic for some (Gophers, in particular, seem to hate magic), but at the same time it provides the benefits that we have already explored. 🤷

#### Missing references in my IDE

Since the generic dispatcher is responsible for dynamically figuring out the handler of each request, developers lose the referencing features of the IDEs. 

This can be frustrating for troubleshooting and refactoring purposes.🤷

# Implementing the command dispatcher pattern in go

Let’s implement the command dispatcher pattern in go!

## Source Code 🗒️

You can find the source code of this simple implementation here: [https://github.com/pkritiotis/go-command-dispatcher](https://github.com/pkritiotis/go-command-dispatcher)

## The `go-command-dispatcher` package 📦

The basic components of the `go-command-dispatcher` package are the following:

1. The **`RequestHandler`** interface provides the definition of the method that needs to be implemented by components that handle *Requests*.
2. The **`Register`** generic method to map the `RequestHandler` to a specific **`Request`** and **`Result`**
3. The **`Send`** generic method to send the input `Request`s that return a **`TResult, error`**

Let’s check each component in detail.

## The `RequestHandler` interface

The `RequestHandler` interface defines the **generic handler method** that accepts a `TRequest` and returns a `(TResult, error)`. 

This is the interface that needs to be implemented by the *Request Handler*  and the interface that the dispatcher can ***register*** and route the ***Requests*** to.

```go
type RequestHandler[TRequest any, TResult any] interface {
	Handle(request TRequest) (TResult, error)
}
```

## Registering handlers using the `Register` method

The `dispatcher` package is stateful. The state is required to hold the registered **Handlers**  in a `[sync.Map](http://sync.Map)` that is used for routing the request to the correct handler.

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

### Sending request via the dispatcher

Callers need to call the `Send` method to send requests via the dispatcher.

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

## Using the `go-command-dispatcher`

To use the go-command-dispatcher package, we need the following:

1. Implement the `RequestHandler` interface for a specific **Request** that returns the respective **Response**.
2. Register the handler using the `dispatcher.Register` method
3. Call the `dispatcher.Send` providing the corresponding **Request**

### Source code

The source code of this example is here: [https://github.com/pkritiotis/go-command-dispatcher/tree/main/examples/notes-http-example](https://github.com/pkritiotis/go-command-dispatcher/tree/main/examples/notes-http-example)

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
  - It uses the `dispatcher` package to register the handler to the `dispatcher`.
- the `infra/http/server.go` spins up an http server which uses the `infra/http/notes/handler.go` HTTP handler that makes the `GetNotesRequest`.
  - It uses the `dispatcher` package to send the requests and get the `[]Note` result

Here is a sample diagram of the relations between the different components:
<p align="center">
  <img alt="notes-generic" src="/assets/images/CommandDispatcher-NotesExample-CommandDispatcher.png">  
  <br>
  <small>Notes Example - Component Interaction</small> 
</p> 

The following diagram shows the execution flow looks like this:

<p align="center">
  <img alt="notes-dispatcher-flow" src="/assets/images/CommandDispatcher-NotesExample-Dispatcher.png"> 
  <br>
  <small>Notes Example - Execution flow</small> 
</p> 



#### The Request Handler implementation

The request handler in our application is `app.GetAllNotesRequestHandler`

```go
func init() {
	err := dispatcher.Register[GetAllNotesRequest, []Note](getAllNotesRequestHandler{})
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
- The `getAllNotesRequestHandler` implements the `Handle` function of the `dispatcher.RequestHandler` interface
- The handler is registered by calling
    - `dispatcher.Register[GetAllNotesRequest, []Note](getAllNotesRequestHandler{})`

### Calling the dispatcher

The HTTP handler gets the notes via the dispatcher in `infra/notes/handler.go`

```go
// GetAll Returns all available notes
func GetAll(w http.ResponseWriter, r *http.Request) {
	req := app.GetAllNotesRequest{Ctx: r.Context()}
	notes, err := dispatcher.Send[app.GetAllNotesRequest, []app.Note](req)
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
2. Executes the request and gets the result with `notes, err := dispatcher.Send[app.GetAllNotesRequest, []app.Note](req)`

Note that there is no knowledge of the request handler at this point. The HTTP handler is only aware of the request model.

# Improving `go-command-dispatcher`

The `go-command-dispatcher` is a minimal sample implementation of the command dispatcher pattern. Let’s explore a couple of possible improvements.

## Introducing a `CommandHandler`  interface

Currently, the `go-command-dispatcher` only supports request handlers that always return a `TResult, error`. 

If we want to be stricter, we can introduce a CommandHandler interface that is almost the same as the `RequestHandler` interface; the only difference is that it does not return a `TResult`

```go
type CommandHandler[TRequest any] interface {
	Handle(request TRequest) error
}
```

With this interface, we would also need to include the corresponding `Register` method that accepts a `CommandHandler`.

## Asynchronous dispatcher call

Another possible improvement in the command dispatcher pattern is introducing an asynchronous API for `dispatcher` calls.

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
The asynchronous goroutine call can be implemented by the dispatcher itself or by the caller, depending on which of the two options we follow.

# A note on the Command Dispatcher Pattern and the Mediator Pattern
The Command Dispatcher Pattern is a commonly used pattern, in multiple languages and appears in different names such as *mediator*, *command*, *command bus*, *event dispatcher* or just *dispatcher*. 

It seems that patterns outside the ones defined by the GoF are not mapped to specific terminology which can make things confusing.

## Isn't this the mediator pattern?
Strictly technically speaking no. The command dispatcher is different from the mediator pattern[^Wikipedia-Mediator] according to the definition of the GoF[^GoF].

In the previous version of this blog post I referred to this pattern as a flavor of the mediator pattern. This was also inspired by the quite popular MediatR[^mediatr] library  I used in .NET that used the term mediator.
Why? Because it has the same intent and tries to solve the same problem but the structure and the proposed solution that each pattern offers is different.

Why did I decide to change it to this one? Because there is a formal definition of this pattern using a different name: command dispatcher. 

The difference might be considered subtle by some, but it is important. Terms are important for alignment when communicating about these concepts.


# Conclusion

In this article, we have seen how the command dispatcher pattern can help us simplify the communication between different components by abstracting their interaction.

 We have demonstrated how we can implement the command dispatcher pattern in a straightforward, minimalistic approach in go, using generics.

Off you go! Clone it, fork it, experiment with it! Happy dispatching!

# References
[^CommandDispatcher]: [Command Dispatcher Pattern - Benoit Dupire and Eduardo B Fernandez](https://hillside.net/plop/plop/plop2001/accepted_submissions/PLoP2001/bdupireandebfernandez0/PLoP2001_bdupireandebfernandez0_1.pdf)
[^GoF]: [Gang of Four](http://wiki.c2.com/?GangOfFour)
[^Wikipedia-Mediator]: [Mediator pattern - Wikipedia](https://en.wikipedia.org/wiki/Mediator_pattern)
[^cqrs]: [CQRS by Martin Fowler](https://martinfowler.com/bliki/CQRS.html)
[^mediatr]: MediatR- Simple Mediator in .NET [Gihub Repo](https://github.com/jbogard/MediatR)
[^clean-architecture]: [Clean Architecture in Go](https://pkritiotis.io/clean-architecture-in-golang/)