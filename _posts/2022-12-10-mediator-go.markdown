---
layout: single
title:  "The Mediator Pattern in go"
date:   2022-05-20 22:15:52 +0300
tags: software-engineering-philosophy
toc: true
# classes: wide
---

This article presents a simple implementation of the Mediator pattern in Go.

# Introduction

Since the official introduction of generics in Go, I wanted to experiment with a small project that would perhaps benefit from generics. 

This is something that I haven’t seen used in Go projects that much and always wanted to experiment with it.

The mediator pattern also checks the boxes of taking advantage of generics. And here I am with this article! 😃

Having previously worked with C# and .NET, I had heavily used the mediator pattern to manage the project dependencies when I need to communicate between layers of abstraction. 

In projects that followed a layered approach with well-defined bounds and abstracted interactions, the mediator pattern can be very useful in reducing coupling between different packages. 

In the following sections we’ll explore:

1. What is the mediator pattern
2. How can we implement it in Go
3. Pros
4. Cons
5. Thoughts on generics

Enjoy!

# The mediator pattern

Before we jump into the implementation of the mediator pattern in go let’s briefly describe what problem the mediator pattern solves.

## The problem

In many occasions, the code that implements a specific functionality is in a different package than the code that calls this functionality. 

In large enough and complex systems, managing the interaction dependencies between these packages can become complex. It also gets particularly challenging and erroneous, when the interaction semantics change.

## How does the mediator pattern address communication dependencies

*The mediator pattern attempts to address the communication complexity by encapsulating the interaction of objects within a mediator object*[^1].

The caller does not need to know about who implements a specific functionality. It just calls the mediator with an encapsulated object and the mediator knows where the handler of the functionality is located. 

## The benefits

The major benefit of the mediator pattern is that it reduces coupling which results in:

1. Easier to manage dependencies
2. Higher maintainability

For a more complete definition of the meditator pattern I would highly recommend the wikipedia definition [here]([https://en.wikipedia.org/wiki/Mediator_pattern](https://en.wikipedia.org/wiki/Mediator_pattern)).

# Implementing the Mediator pattern in go

Let’s implement the mediator in go!

The basic components of mediator are the following:

1. The *Request Handler* interface that provides the definition of the method that needs to be implemented by components that handle *Requests*.
2. The mediator package which is used for **registering** ***Handlers*** and **executing** ***Requests***.

## The `RequestHandler` interface

The `RequestHandler` interface that defines the **generic handler method** that accepts a `TRequest` and returns a `(TResult, error)`. This is the interface that needs to be implemented by the *Request Handler* 

```go
type RequestHandler[TRequest any, TResult any] interface {
	Handle(request TRequest) (TResult, error)
}
```

## The `mediator` package

The `mediator` struct that exposes the following API:

1. `Register` generic method to map the `RequestHandler` to a specific `Request` and `Result`
2. `Send`generic method to send the input `Request`s that return a `TResult, error`

### Storing the registered Handlers

```go
var (
	registeredHandlers sync.Map
)

func init() {
	registeredHandlers = sync.Map{}
}

type key[TRequest any, TResult any] struct {}
```

The `mediator` package is stateful. The state is required to hold the registered *********Handlers*********  in a `[sync.Map](http://sync.Map)` that is used for routing the mediator request to the correct handler.

The `key` struct represents the key used for the registered handlers. The pair of request type and result type, allows us to support different handlers that accept the same request but return a different result.

### Registering handlers

The handlers can be registered using the `Register` method is implemented as follows:

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

## Using the `go-mediator` - Example

To use the go-mediator package we need the following:

1. Implement the `RequestHandler` interface for a specific ********Request******** that returns a specific ********Response********.
2. Register the handler using the `mediator.Register` method
3. Call the `mediator.Send` providing the corresponding *******Request*******

Let’s see the above in action using a minimal example of a notes app that exposes an endpoint which returns the list of in-memory notes.

### The source code is here: [https://github.com/pkritiotis/go-mediator/tree/main/examples/notes-http-example](https://github.com/pkritiotis/go-mediator/tree/main/examples/notes-http-example)

The structure of this sample project is the following:

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

The logic behind this example is that 

- the `app/notes.go` contains the business logic of the application
- the `infra/` package spins up an http server and the http handler need to call the notes app logic to obtain and return the notes.

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

# Possible Improvements

The `go-mediator` is a sample minimal implementation of the mediator pattern so there are possible improvements.

## Introducing a `CommandHandler`  interface

At the moment the go-mediator only supports request handlers that always return a `TResult, error`. 

If we want to be stricter, we can introduce a CommandHandler interface that is almost the same as the `RequestHandler` interface; the only difference is that it does not return a `TResult`

```go
type CommandHandler[TRequest any] interface {
	Handle(request TRequest) error
}
```

## Asynchronous mediator call

Another possible improvement in the mediator pattern is to introduce an asynchronous API for mediator calls.

Instead of having the `Send` blocking call, we could allow executing the `Send` in a goroutine that accepts a channel which will return the result of the operation once completed.

# Notes

The `go-mediator` is a very simplistic version of the mediator pattern.

# Conclusion

# References