---
layout: single
title:  "Agile: Embrace the Philosophy, Not Rigid Framework Rules"
date:   2024-07-20 15:25:00 +0300
toc: true
tags: golang software-patterns
# classes: wide
header:
    og_image: /assets/images/Agile-Philosophy-Vs-Framework-OG.png
---
# Introduction

I’ve been fascinated with Domain-Driven Design (DDD) for a while now. Over the past year, I’ve spent considerable time deep-diving into its details, refining my understanding, and exploring how to apply it effectively in real-world projects.

While I’ve used DDD concepts in various ways, I’ve never approached it dogmatically - I believe in practical applications over rigid adherence to theory. Early in my career, working with OOP, I found that many DDD principles felt more straightforward due to built-in language features like encapsulation and rich domain models. There’s also significantly more material on applying DDD in OOP languages. Even Eric Evans, the author of the well-known Blue Book, was involved in one of the first attempts to codify DDD in Java.

But Go, with its different programming paradigm, makes things more interesting. Go’s simplicity and explicitness force you to think differently about domain modeling. Some DDD concepts map well, while others require careful adaptation. That’s what I want to explore.

So, I thought I’d start with the most fundamental building block of domain modeling: the entity.

In this post, I’ll share:
- How to properly define an entity in Go
- Common mistakes and anti-patterns I’ve seen (and made :smiling_face_with_tear:)
- Go-specific considerations, like encapsulation and invariance

# What is an Entity in DDD?

Before we jump into defining an Entity in Go, it’s necessary to give a small reminder of what an Entity is. I’m not going to deep-dive into this, as you can find multiple resources explaining it in detail.

To understand entities in Domain-Driven Design (DDD), it’s best to start with the original definition from Eric Evans’ Domain-Driven Design: Tackling Complexity in the Heart of Software:

	“An object that is not defined by its attributes, but rather by a thread of continuity and its identity.”

At its core, an entity is something that is uniquely identifiable and represents a state that changes over time. Unlike a Value Object, which is defined by its attributes and is typically immutable, an entity’s identity is what makes it distinct.

For example, consider a User in a system:
- If we update their email address or profile information, it’s still the same user.
- Even if another user has the same name, email, and age, they are different entities because they have distinct identities (often represented by a unique ID).

This distinction is critical because **entities are long-lived objects that evolve**. A well-designed entity ensures that state changes remain valid throughout its lifecycle.

# Defining an Entity in Go

Now that we’ve established what an Entity is in DDD, let’s look at how to properly define one in Go.

Unlike object-oriented languages that provide built-in encapsulation through classes and access modifiers, Go requires a more intentional approach to structuring entities while maintaining clarity, correctness, and domain integrity.

## How to Properly Define an Entity in Go

A Go entity is typically modeled using a struct, where the most important characteristic is identity. Here’s a simple example of a User entity:

```go
//Entity
type User struct {
    id    uuid.UUID
    name  string
    email EmailAddress  //Value Object
}
```
- Identity (id) is what defines the entity.
  - Even if two users have the same name and email, they are different entities if their id values are different.
- Fields are unexported, enforcing encapsulation.
  - External packages cannot modify the entity’s fields directly, ensuring controlled access.
- State is encapsulated within the entity itself.
  - The only\* way to modify the entity should be through well-defined methods.

A better way to instantiate an entity is by using a constructor function, ensuring valid creation:
```go
func NewUser(name string, email string) (*User, error) {
    id := uuid.New()
    if name == "" {
        return nil, errors.New("name cannot be empty")
    }

    emailAddr, err := NewEmailAddress(email)
    if err != nil {
        return nil, err
    }

    return &User{
        id:    id,
        name:  name,
        email: emailAddr,
    }, nil
}
```

This guarantees that all invariants are enforced at creation.

## Common Anti-Patterns & Mistakes

Even though Go allows flexible struct usage, there are a few pitfalls that can lead to poorly designed entities.

### 1. Exposing Fields Publicly

One of the most common mistakes in Go is exporting struct fields, allowing direct access:
```go
type User struct {
    ID    uuid.UUID
    Name  string
    Email EmailAddress  //Value Object
}
```
Since all fields are public, they can be modified arbitrarily:
```go
u := User{Name: ""} //Invalid Name Instantiation
u.Email = NewEmailAddress("newemail@example.com")  // Invalid modification outside the entity
```

🔴***Why this is bad:***
- There’s no validation when modifying fields.
- Other parts of the system can accidentally put the entity in an invalid state.

👉 Better approach: Use private fields and define methods to access them:
```go
func (u *User) Email() EmailAddress {
    return u.email
}
```

### 2. Mixing Business Logic & Infrastructure

Entities should contain domain logic, but they shouldn’t know how they are stored or retrieved. A common mistake is embedding database logic inside entities:

```go
func (u *User) SaveToDB(db *sql.DB) error {
    _, err := db.Exec(...)
    return err
}
```
🔴***Why this is bad:***\n
	•	The entity now depends on infrastructure (database logic).
	•	It violates separation of concerns, making the entity hard to test.

:point_right:Better approach: Separate persistence logic into an external service, in this case a **repository**:
```go
type UserRepository struct {
    db *sql.DB
}

func (r *UserRepository) Save(u *User) error {
    query := "INSERT INTO users (id, name, email) VALUES (?, ?, ?)"
    _, err := r.db.Exec(query, u.ID(), u.Name(), u.Email().String())
    return err
}
```
This way, the User entity stays focused on domain logic while persistence is handled separately.

### 3. Blind Setters - Not Protecting Invariants

A critical responsibility of an entity is to enforce invariants - rules that must always be true.

🔴 Example of a bad entity:
```go
type BankAccount struct {
    Balance float64
}

func (b *BankAccount) SetBalance(amount float64) {
    b.Balance = amount
}
```
The above code allows an account to have a negative balance, which is likely invalid.

✅ Better approach:
```go
type BankAccount struct {
    balance float64
}

func (b *BankAccount) Withdraw(amount float64) error {
    if amount <= 0 {
        return errors.New("withdrawal amount must be positive")
    }
    if b.balance < amount {
        return errors.New("insufficient funds")
    }
    b.balance -= amount
    return nil
}

func (b *BankAccount) Balance() float64 {
    return b.balance
}
```
Now, the entity always remains in a valid state.

### Wait a minute... What if you don't use the constructor?

This one bugs me. Or at least, it did. And I’ve made my peace with it.

If you’ve been paying attention, you’ll notice that the structs in my examples are all exported. And that’s intentional—for convenience.

***What if someone decides to not use the constructor?*** \n 
Well, they can 🤷
```go
u := User{}
```

***Okay.. and what should we do about this?***

Well, there’s not much you can do.

In Go, a constructor is just a factory function - there’s no way to enforce its usage. Unlike languages with access modifiers (private, protected), Go allows direct struct instantiation as long as the struct is exported.

So if you choose to export the struct, you can’t guarantee that someone won’t misuse it.

However, if you really, really want to prevent this, you can try something—which, personally, I consider blasphemy to Go: **Work Only with Interfaces**

Instead of exposing a struct directly, you only expose an interface, forcing users to interact with entities through predefined methods.

Here’s an example:
```go
package domain

import "github.com/google/uuid"

type User interface {
    ID() uuid.UUID
    Name() string
    Email() EmailAddress
    Rename(newName string) error
    ...
}

type user struct {
    id    uuid.UUID
    name  string
    email EmailAddress
}

func NewUser(name string, email EmailAddress) User {
    //validations go here
    return &user{id: uuid.New(), name: name, email: email}
}

func (u *user) ID() uuid.UUID     { return u.id }
func (u *user) Name() string      { return u.name }
func (u *user) Email() EmailAddress { return u.email }
...
```
Since the struct is unexported, this ensures the factory method is the only way to create an instance.

**What does this do?**
- The actual implementation (user) is unexported, meaning no one outside this package can create an instance of it.
- The only way to get a User instance is through the NewUser function.
- Consumers interact only with the User interface, which enforces controlled access.

**Should you do this?** *(Please don't)*

That depends (Nope). If you absolutely need to prevent struct instantiation (You don't), this approach works. But in most(all) cases, it’s overkill.

Go is designed to favor simplicity over strict enforcement. If you trust(just a little bit) your team and codebase, a well-documented constructor is usually enough.