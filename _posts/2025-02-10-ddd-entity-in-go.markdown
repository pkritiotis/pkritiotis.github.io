---
layout: single
title:  "Domain-Driven Design Entities in Go: What Works, What Breaks, and What’s Overkill"
date:   2025-02-10 22:00:00 +0300
toc: true
tags: golang software-patterns
# classes: wide
header:
    og_image: /assets/images/Agile-Philosophy-Vs-Framework-OG.png
---
# Introduction

I’ve been fascinated with Domain-Driven Design (DDD) for a while now. Over the past year, I’ve spent considerable time deep-diving into its details, refining my understanding, and exploring how to apply it effectively in real-world projects.

While I’ve used DDD concepts in various ways, I’ve never approached it dogmatically - *I believe in practical applications over rigid adherence to theory*. Early in my career, working with OOP, I found that many DDD principles felt more straightforward due to built-in language features like encapsulation and rich domain models. There’s also significantly more material on applying DDD in OOP languages. Even Eric Evans, the author of the well-known Blue Book[^ddd], was involved in one of the first attempts to codify DDD in Java.

But Go, with its different programming paradigm, makes things more interesting.

Go’s simplicity and explicitness force you to think differently about domain modeling. Some DDD concepts map well, while others require careful adaptation. That’s what I want to explore.

So, I thought I’d start with one of the fundamental building blocks of domain modeling: the entity.

In this post, I’ll share:
- How to properly define an entity in Go
- Common mistakes and anti-patterns I’ve seen (and made 🥲)
- Go-specific considerations, like encapsulation and invariance

# What is an Entity in DDD?

Before we jump into defining an Entity in Go, it’s necessary to give a small reminder of what an Entity is. I’m not going to deep-dive into this, as you can find multiple resources explaining it in detail. If you're looking for a DDD starter, my favourite book that I always recommend is *Learning Domain-Driven Design: Aligning Software Architecture and Business Strategy by Vlad Khononov*[^ddd-favourite].

To understand entities in Domain-Driven Design (DDD), it’s best to start with the most common definition:

>“An object primarily defined by its identity”

At its core, an entity is something that is **uniquely identifiable** and represents a **state that changes over time**. Unlike a *Value Object*, which is defined by its attributes and is typically immutable, an entity’s identity is what makes it distinct.

For example, consider a User in a system:
- If we update their email address or profile information, it’s still the same user.
- Even if another user has the same name, email, and age, they are different entities because they have distinct identities (often represented by a unique ID).

This distinction is critical because **entities are long-lived objects that evolve**. A well-designed entity ensures that state changes remain valid throughout its lifecycle.

# Defining an Entity in Go

Now that we’ve established what an Entity is in DDD, let’s look at how to properly define one in Go.

Unlike object-oriented languages that provide built-in encapsulation through classes and access modifiers, Go requires a more intentional approach to structuring entities while maintaining clarity, correctness, and domain integrity.

## How to Properly Define an Entity in Go

A Go entity is typically modeled using a struct, where the most important characteristic is **identity**. Here’s a simple example of a User entity:

```go
//Entity
type User struct {
    id    uuid.UUID
    name  string
    email EmailAddress  //Value Object
}
```
- **Identity** (id) is what defines the entity.
  - Even if two users have the same name and email, they are different entities if their id values are different.
- Fields are **unexported**, enforcing encapsulation.
  - External packages cannot modify the entity’s fields directly, ensuring controlled access.
- **State is encapsulated within the entity itself**.
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

### Wait a minute... What if you don't use the constructor?

This one bugs me. Or at least, it did. And I’ve made my peace with it. 🧘

If you’ve been paying attention, you’ll notice that the structs in my examples are all exported. And that’s intentional—for convenience.

***What if someone decides to not use the constructor?***<br> 
Well, they can 🤷
```go
u := User{}
```

***Okay.. and what should we do about this?***

Well, there’s not much you can do.

In Go, a constructor is just a factory function - there’s no way to enforce its usage. Unlike languages with access modifiers (private, protected), Go allows direct struct instantiation as long as the struct is exported.

So if you choose to export the struct, you can’t guarantee that someone won’t misuse it.

However, if you really, really want to prevent this, you can try something, which, personally, I consider blasphemy to Go: *interface everything* 💥

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

func NewUser(name string, email string) User {
    //validations and value object creations go here
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

**Should you do this?** *(Please don't 🙏)*

That depends (Nope). If you absolutely need to prevent struct instantiation (You don't), this approach works. Keep in mind that an interface is an extra level of abstraction, and for this particular purpose, in most(all) cases, it’s overkill.

Go is designed to favor simplicity over strict enforcement. **Idiomatic Go often relies on conventions and documentation rather than strict enforcement**. For many teams, documenting *“please use NewUser”* is sufficient, and over-engineering with interfaces might add unnecessary complexity.


# Common Anti-Patterns & Mistakes

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

👉 Better approach: Use private fields and **define read-only accessor** methods:
```go
func (u *User) ID() uuid.UUID {
    return u.id
}

func (u *User) Name() string {
    return u.name
}

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
This way, the User entity stays focused on domain logic while persistence is handled separately. This of course has additional benefits on testability and respects the single responsibility principle.

### 3. Blind Setters And Not Protecting Invariants

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
Now, the entity always remains in a valid state. By enforcing invariants through controlled methods, you not only safeguard your entity’s integrity but also make it easier to write unit tests that assume a consistent state.

Admittedly, this was a mistake I made repeatedly in my early attempts at applying DDD in my projects. 🙈. Separating application logic from domain logic can be confusing for first-time adopters. Remember, if you tamper directly with an entity’s attributes, you are essentially embedding domain logic outside of its intended boundaries.

# Conclusion
Designing DDD entities in Go requires a deliberate approach. Unlike OOP languages with built-in encapsulation, Go relies on structuring entities intentionally while maintaining clarity, correctness, and domain integrity.

Here are the key takeaways:
- Identity matters—an entity is defined by its identity, not just its attributes.
- Encapsulation is key—use unexported fields and well-defined methods to enforce business rules.
- Protect invariants—never allow an entity to enter an invalid state.
- Keep entities focused—they should contain domain logic, not persistence details.
- Go is pragmatic—while constructors are great, absolute enforcement isn’t always necessary.

What’s Next?

Entities are just the beginning. In future posts, I’ll explore how to expand entities into aggregates, define domain services, and structure repositories to build a complete domain model in Go.



## References

[^ddd]: **Domain-Driven Design: Tackling Complexity in the Heart of Software (Blue Book)**  
    [Eric Evans' Blue Book](https://www.domainlanguage.com/ddd/)

[^ddd-favourite]: **Learning Domain-Driven Design: Aligning Software Architecture and Business Strategy**  
    [Vlad Khononov](https://vladikk.com/page/about/)