---
layout: single
title:  "Implementing a simple, generic mediator in go"
date:   2022-12-10 22:15:52 +0300
tags: software-engineering-philosophy
# toc: true
# classes: wide
---
This article presents a simple implementation a generic mediator in Go.

# Introduction
In large and complex systems, managing the interaction dependencies between different components can become complex. It also gets particularly challenging and erroneous, when the interaction semantics change.

At the same time, as more and more components are added as part of a big system, it is helpful and can become necessary to introduce different layers of abstraction to separate the domain logic from business logic and from the infrastructure.

The abstraction of layers can enable some control of the interaction between the components but the components still need to be aware of eachother and depend on eachother for communication purposes.

A very helpful design pattern that encapsulates the interaction and abstract the communication between components is the  **mediator pattern**.

The mediator pattern, and especially the generic, dynamic mediator that we will see in this article is particularly useful in projects that follow a layered approach with well-defined bounds and the CQRS[^cqrs] pattern.

In the following sections we’ll explore:

1. What is the generic mediator pattern
2. A simple implementation of the generic mediator in Go

Enjoy!

# The mediator pattern

Before we jump into the implementation of the mediator pattern in go let’s briefly describe how the mediator pattern addresses the interaction complexity.

*The mediator pattern is a behavioral design pattern originally proposed by the GoF[^GoF] which attempts to address the communication/interaction complexity by encapsulating the interaction of objects within a mediator object*[^1].

The objects interact through the mediator and not directly with eachother; The mediator is responsible to route the requests to the corresponding handler and return the result back to the caller.

## The original definitions of the mediator pattern and the generic mediator definition
The original definition of the mediator looks like this:
- It involves a concrete mediator that references the participants


## The generic, dynamic mediator
In this article we will explore a different flavor of the original mediator pattern that works in a dynamic and generic way. One can argue that this is not the mediator pattern and they can be somewhat correct since it doesn't match the exact proposed implementation. However, it solves the same problem that the mediator tries to solve; simplify dependencies and increase maintainability using the power of generics and dynamic registration.


## Commonalities with the observer pattern and the command pattern

### Observer vs Mediator
### Command vs Mediator
## The benefits

The major benefit of the mediator pattern is that it reduces coupling which results in:

1. Easier to manage dependencies
2. Higher maintainability

For a more complete definition of the meditator pattern I would highly recommend the wikipedia definition [here]([https://en.wikipedia.org/wiki/Mediator_pattern](https://en.wikipedia.org/wiki/Mediator_pattern)).

## Drawbacks
As with most things in software engineering, the mediator has some tradeoffs. In my opinion they are minimal, but of course it dependes on a case by case.
### Magic
It's too magical! 

I just make a call to a mediator and the call is magically executed by the correct handler. How is this done?
That's true; the caller does not have knowledge of the handler before the code runs. And this can be considered too much magic for some. Gophers in particular seem to hate magic :magic:

### IDE features
Since the generic mediator is responsible to dynamically figure out the handler of each request, developers loose the referencing features of the IDEs. 

This can be frustrating in for troubleshooting and refactoring purposes.
# Implementing the generic dynamic Mediator pattern in go

Let’s implement the generic mediator in go!

## Source Code

You can find the source code of this simple implementation here: https://github.com/pkritiotis/go-mediator

## The `go-mediator` package

The basic components of the `go-mediator` package are the following:

1. The *`RequestHandler`* interface that provides the definition of the method that needs to be implemented by components that handle *Requests*.
2. The `Register` generic method to map the `RequestHandler` to a specific `Request` and `Result`
3. The `Send`generic method to send the input `Request`s that return a `TResult, error`

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

### Example Project

Let’s see the above in action using a minimal example of a notes app that exposes an endpoint which returns the list of in-memory notes.

The structure of this sample project follows a minimalistic version of Clean Architecture with the app layer containing the business logic and the infra layer containing the output port and adapter for exposing notes on an http api.

```go
.
├── app
│   └── notes.go
├── infra
│   ├── http.go
│   └── notes
│       └── handler.go
└── main.go
```

To be more specific

- the `app/notes.go` contains the query handler that processes the `GetNotesRequest`
- the `infra/http.go` spins up an http server which uses the `infra/notes/handler.go` http handler that makes teh `GetNotesRequest`.

### The Request Handler implementation

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

In this article we have seen how the mediator pattern can help us simplify the interaction between different components.
We have distinguished the generic flavor of the mediator pattern from the *original* mediator pattern definition and explained the common characteristics between the two.

We have also seen how we can implement the mediator pattern in a very simple, minimalistic approach in go, using generics.

# References