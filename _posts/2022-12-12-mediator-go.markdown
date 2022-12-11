---
layout: single
title:  "Implementing a simple, generic mediator pattern in go"
date:   2022-12-12 22:15:52 +0300
tags: software-engineering-philosophy
# toc: true
# classes: wide
---
This article presents a simple implementation of a generic mediator in Go.

# Introduction
As software systems get larger and more complex, **managing the interaction dependencies between different components can become complicated**. Refactoring and introducing any changes can also become particularly challenging and error-prone when the interaction semantics change.

<p align="center">
  <img alt="dependencies-everywhere" src="/assets/images/Dependencies-Dependencies-Everywhere.png" width="200">
  <br>
    <small>Image by <a href="https://github.com/MariaLetta/free-gophers-pack"> Maria Letta @free-gophers-pack</a></small>
</p> 

This is a common problem in software engineering and can be addressed by different types of techniques that attempt to decouple the components and reduce the dependencies to the maximum degree.

A commonly followed approach, as more and more components are added as part of a big system, is the introduction of different layers of abstraction that separate the domain logic from business logic and from the infrastructure. For example, Clean architecture is a well-known design philosophy that follows this approach.

The abstraction in layers can enable some control of the interaction between the different components of the system by following rules that make the dependency graph acyclic and manageable. However, the components still need to be aware of eachother and depend on eachother to some degree for communication purposes.

\- **Can we maybe *hide* these dependencies**❓ So, on top of abstracting the layers of the system based on some rules, *can we also abstract their interaction*❓

The answer is yes!

We can do this by using an awesome design pattern that encapsulates the interaction and abstracts the communication between components: **mediator pattern**.

<p align="center">
  <img alt="idea-mediator" src="/assets/images/lamp.png" width="200">
  <br>
    <small>Image by <a href="https://github.com/MariaLetta/free-gophers-pack"> Maria Letta @free-gophers-pack</a></small>
</p>
The mediator pattern and flavours of it that we will see in this article can be a very valuable tool in projects that follow a layered approach with well-defined bounds. It's also especially useful when combined with the the CQRS[^cqrs] pattern.

In the following sections we’ll explore:
1. What is the generic mediator pattern?
2. A simple implementation of the generic mediator in Go

Enjoy!

# The mediator pattern

Before we jump into the implementation of the mediator pattern in go let’s briefly describe what the mediator pattern is.

*The mediator pattern is a behavioral design pattern originally proposed by the GoF[^GoF] which attempts to address the communication/interaction complexity by encapsulating the interaction of objects within a mediator object*[^1].

The main things to remember here is that:

1. The objects interact through the mediator and not directly with each other.
2. The mediator is responsible to *route* the requests to the corresponding operation handler and return the result back to the caller.

**What does this achieve❓**

\- The caller does not know anything about the operation handler and the operation handler does not know anything about the caller.

## The original definition of the mediator pattern

The original definition of the mediator looks like this:
<p align="center">
  <img alt="original-mediator" src="/assets/images/Mediator-OriginalMediator.png">
</p> 

- It involves a mediator interface and a concrete mediator that is responsible for the participants (collegues) interaction
- The mediator serves/implements the actual interactions between the two colleagues
- The caller makes the calls to the mediator
- The handler receives calls from the mediator
- Once the handler finishes the operation, it notifies the caller through the mediator

Some extra notes here:

- The mediator can work with the observer pattern for notifying the mediator and then the caller for asynchronous operations


*For a more complete definition of the meditator pattern I would highly recommend the wikipedia definition [here]([https://en.wikipedia.org/wiki/Mediator_pattern](https://en.wikipedia.org/wiki/Mediator_pattern)).*

## The generic, dynamic mediator
In this article we will explore a different flavor of the original mediator pattern that works in a dynamic and generic way.
This is a pattern used by the MediaTR[^mediatr] project by jboggard[^jboggard] that I heavily used while working with C# projects.

Cool, how does it work then?

1. **Handlers** dynamically **register** through the **mediator** interface to serve specific **request types** on **runtime**
2. **Callers** make **requests** through the **mediator** interace by passing a **request object**.
   - The **mediator** knows which **handler** should serve the **request** because it has already been registered to serve these specific **request types**.

<p align="center">
  <img alt="generic-mediator" src="/assets/images/Mediator-GenericMediator.png">
  <br>
  <small>Image generated by <a href="https://github.com/quasilyte/gopherkon"> gopherkon</a></small>
</p> 

\- **Awesome, what do we need to implement this❓**
1. An commonly agreed **handler interface** that handlers will need implement to server specific request types
2. A **mediator** object that exposes
   1. A **Register** method that will allow handlers to dynamically register themselves for specific request types
   2. A **Send** method that will allow callers to make requests
3. Both the handler and the caller should know the **request** model

### Wait, is this really a mediator pattern? 🤔

Some may argue that this is not the mediator pattern and they can be somewhat correct since it doesn't match the exact proposed implementation.

Technically, this is probably a valid argument. However, it solves the same problem that the mediator tries to solve: simplify dependencies and increase maintainability using the power of generics and dynamic registration.

*For those interested in following some arguments about this you can find an interesting article [here](https://jimmybogard.com/you-probably-dont-need-to-worry-about-mediatr/) 🍿*

## Pros and Cons of the mediator pattern

### Pros
The major benefit of the mediator pattern is that it reduces coupling which results in:

1. Easier to manage dependencies
2. Higher maintainability

### Cons

As with most things in software engineering, the mediator has some tradeoffs. In my opinion they are minimal, but of course it depends on the case.

#### Magic

🪄*Too much magic!* 🪄

I just make a call to a mediator and the call is magically executed by the correct handler. How is this done?

Good question! The caller does not have knowledge of the handler before the code runs which inhibits readability and can make troubleshooting difficult.

And this can be considered too much magic for some. Gophers in particular seem to hate magic 🪄

#### IDE features

Since the generic mediator is responsible to dynamically figure out the handler of each request, developers loose the referencing features of the IDEs. 

This can be frustrating for troubleshooting and refactoring purposes.

# Implementing the generic Mediator pattern in go

Let’s implement the generic mediator in go!

## Source Code

You can find the source code of this simple implementation here: [https://github.com/pkritiotis/go-mediator](https://github.com/pkritiotis/go-mediator)

## The `go-mediator` package

The basic components of the `go-mediator` package are the following:

1. The **`RequestHandler`** interface that provides the definition of the method that needs to be implemented by components that handle *Requests*.
2. The **`Register`** generic method to map the `RequestHandler` to a specific **`Request`** and **`Result`**
3. The **`Send`** generic method to send the input `Request`s that return a **`TResult, error`**

Let’s check each component in detail

## The `RequestHandler` interface

The `RequestHandler` interface defines the **generic handler method** that accepts a `TRequest` and returns a `(TResult, error)`. 

This is the interface that needs to be implemented by the *Request Handler*  and it is the interface that the mediator can ***register*** and route the ***Requests*** to.

```go
type RequestHandler[TRequest any, TResult any] interface {
	Handle(request TRequest) (TResult, error)
}
```

## Registering handlers using the `Register` method

The `mediator` package is stateful. The state is required to hold the registered *********Handlers*********  in a `[sync.Map](http://sync.Map)` that is used for routing the mediator request to the correct handler.

```go
var (
	registeredHandlers sync.Map
)

func init() {
	registeredHandlers = sync.Map{}
}

type key[TRequest any, TResult any] struct {}
```

The `key` struct represents the key used for the registered handlers. The pair of request type and result type, allows us to support different handlers that accept the same request but return a different result.

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

The reason for checking this is that if we had more than one handlers, we would not know how to determine which one to call.

### Sending request via the mediator

In order to send requests via the mediator, callers need to call the `Send` method.

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

Based on the requested `Request` and `Result` we try to load and cast the corresponding registered handler. 

If something goes wrong, we return an error otherwise we call the `Handle` method of the registered handler.

## Using the `go-mediator`

To use the go-mediator package we need the following:

1. Implement the `RequestHandler` interface for a specific ********Request******** that returns the respective ********Response********.
2. Register the handler using the `mediator.Register` method
3. Call the `mediator.Send` providing the corresponding *******Request*******

### Source code

The source code of this example is here: [https://github.com/pkritiotis/go-mediator/tree/main/examples/notes-http-example](https://github.com/pkritiotis/go-mediator/tree/main/examples/notes-http-example)

### Example - Notes Http API Project

Let’s see the above in action using a minimal example of a notes app that exposes an endpoint which returns the list of in-memory notes.

#### Project Structure
The structure of this sample project follows a minimalistic version of Clean Architecture with the app layer containing the business logic and the infra layer containing the output port and adapter for exposing notes on an http api.

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
- the `infra/http/server.go` spins up an http server which uses the `infra/http/notes/handler.go` http handler that makes teh `GetNotesRequest`.
  - It uses the `go-mediator` package to send the requests and get the `[]Note` result

Here is a sample diagram of the relations between the different components:
<p align="center">
  <img alt="notes-generic" src="/assets/images/Mediator-NotesExample-GenericMediator.png">  
</p> 

The following diagram shows the execution flow looks like this:

<p align="center">
  <img alt="notes-mediator-flow" src="/assets/images/Mediator-NotesExample-Mediator.png">  
</p> 



#### The Request Handler implementation

The request handler in our application is `app.GetAllNotesRequestHandler`

```go
func init() {
	err := mediator.Register[GetAllNotesRequest, []GetAllNotesResult](getAllNotesRequestHandler{})
	if err != nil {
		panic(err)
	}
}

// GetAllNotesRequest contains the request params
type GetAllNotesRequest struct {
	Ctx context.Context
}

// GetAllNotesResult is the result of the GetAllNotesRequest Query
type GetAllNotesResult struct {
	ID        uuid.UUID
	Name      string
	Contents  string
	CreatedAt time.Time
}

// GetAllNotesRequestHandler Contains the dependencies of the Handler
type GetAllNotesRequestHandler interface {
	Handle() ([]GetAllNotesResult, error)
}

type getAllNotesRequestHandler struct {
}

// Handle Handles the query
func (h getAllNotesRequestHandler) Handle(request GetAllNotesRequest) ([]GetAllNotesResult, error) {

	result := []GetAllNotesResult{
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

- The `GetAllNotesRequest` and `GetAllNotesResult` represent the respective request and response of this handler.
- The `getAllNotesRequestHandler` implements the `Handle` function of the `mediator.RequestHandler` interface
- The handler is registered by calling
    - `mediator.Register[GetAllNotesRequest, []GetAllNotesResult](getAllNotesRequestHandler{})`

### Calling the mediator

The mediator is called by the http handler via the mediator in `infra/notes/handler.go`

```go
// GetAll Returns all available notes
func GetAll(w http.ResponseWriter, r *http.Request) {
	req := app.GetAllNotesRequest{Ctx: r.Context()}
	notes, err := mediator.Send[app.GetAllNotesRequest, []app.GetAllNotesResult](req)
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
2. Executes the request and get the result with `notes, err := mediator.Send[app.GetAllNotesRequest, []app.GetAllNotesResult](req)`

Note that there is no knowledge of the request handler at this point. The http handler is only aware about the request model.

# Improving `go-mediator`

The `go-mediator` is a sample minimal implementation of the mediator pattern. Let’s explore a couple of possible improvements.

## Introducing a `CommandHandler`  interface

At the moment the `go-mediator` only supports request handlers that always return a `TResult, error`. 

If we want to be stricter, we can introduce a CommandHandler interface that is almost the same as the `RequestHandler` interface; the only difference is that it does not return a `TResult`

```go
type CommandHandler[TRequest any] interface {
	Handle(request TRequest) error
}
```

With this interface we would need to also include the corresponding Register method that accepts a `CommandHandler`.

## Asynchronous mediator call

Another possible improvement in the mediator pattern is to introduce an asynchronous API for mediator calls.

```go
func Send[TRequest any, TResult any](r TRequest, chan<- TResult, chan<- error) {
 //[..]
}
```

Instead of having the `Send` blocking call, we could allow executing the `Send` in a goroutine that accepts a channel which will return the result of the operation once completed.

# Conclusion

In this article we have seen how the mediator pattern can help us simplify the communication between different components by abstracting their interaction.

We have seen the original mediator pattern definition and the generic flavour of the mediator and have demonstrated how we can implement the mediator pattern in a very simple, minimalistic approach in go, using generics.

That's all folks, off you go mediate!

# References