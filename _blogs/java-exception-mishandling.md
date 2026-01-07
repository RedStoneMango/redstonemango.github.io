---
title: "Java Exceptions Aren’t Bad — You Are"
description: "Why you’ve been mishandling Java exceptions your whole life (and how to stop)."
layout: blog
date: 2026-01-07
---

Java has a reputation problem. Mention “exceptions” in a room full of developers
and someone will inevitably say something like _“good concept, terrible implementation”_
followed by a suggestion that we should all be using errors-as-values instead.

After working with Java for a while, I’ve realized something interesing:
Java’s exception philosophy is actually really good.
The part that’s broken is how we use it.

## Why I Prefer Exceptions Over Errors-as-Values

Since I started working with Java, I’ve found myself consistently preferring
exceptions for error handling instead of return types like the new 
`std::expected` in C++ or Rust's `Result` enum.

One big reason is separation of concerns. When a method succeeds, it simply returns a value.
When it fails, normal execution stops and control jumps to a `catch` block.
There’s no half-success state where you have to remember to inspect a return value **every single time**.

This also massively reduces boilerplate.
Compare these two mental models:

- Open a file, check return value, log something, read the file, check return value, log something again
  <details markdown="1">
    <summary>Code</summary>
  
    ```java
    var reader = openFile(file);
    if (reader == null) {
        LOGGER.error("Ooops...");
        return;
    }
    var bytes = readerOptional.get().readBytes();
    if (bytes == null) {
        LOGGER.error("Ooops...");
        return;
    }
    ```
  </details>
      
- Assume everything works inside a <code>try</code> block and handle all failures in one place
  <details markdown="1">
    <summary>Code</summary>
    ```java
    try {
        var bytes = openFile(file).readBytes();
    }
    catch (IOException _) {
        LOGGER.log("Ooops...");
    }
    ```
  </details>

The second approach is not just shorter — it’s easier to reason about.
You describe the happy path once, and the unhappy path once.
No need for a mixture of both or some functional nesting.

##  Checked vs. Unchecked: A Feature, Not a Bug

Java’s split between checked and unchecked exceptions is often used as
“proof” that Java got exceptions wrong.
I strongly disagree.

Checked exceptions are incredibly useful when used correctly.
If a method declares a checked exception, the compiler guarantees that
you can’t accidentally ignore it.
So as long as the method author made the right call,
you literally cannot forget to handle the error.

The challenge, however, is that many developers don’t internalize this distinction.
To see how this concept can work well in practice, let’s take a quick look at Rust.

## A Rust Perspective

Rust encourages developers to distinguish between recoverable and irrecoverable errors.
Recoverable errors are returned as `Result<T, E>`, which the caller is expected to handle.
Irrecoverable errors, on the other hand, trigger a `panic!()`,
which unwinds the stack and signals a bug or an unrecoverable situation.

In practice, Rust developers automatically think about whether an error can be handled or if it should crash the program.
Java’s checked vs unchecked exceptions _should_ serve the same purpose: checked exceptions for recoverable errors the caller can handle,
unchecked exceptions for unrecoverable ones.

Java even has an advantage here: recoverable errors are handled via exceptions rather than return values,
which keeps the happy path clean and separates error handling from normal logic.

The problem is that, unlike Rust, many Java APIs and developers fail to use this distinction properly — which is why exception handling often feels messy in the wild.

This misuse isn’t theoretical — it shows up in real code all the time, even in libraries you’d expect to get it right.

## Where Things Get Messy

What I see far too often are methods that throw unchecked exceptions for things that are very much expected to fail.
A classic example: parsing with Google GSON.

If parsing a JSON config fails, that’s not some exotic,
programmer-error-only scenario.
That’s a real, expected failure mode.
Throwing an unchecked exception here is basically saying:

> “Yes, parsing failed, but I don’t think you should handle this.
Let your application crash instead.”

On the flip side, we also get APIs that throw checked exceptions
for things that are extremely unlikely to fail if the developer uses them correctly.
This leads to everyone’s favorite Java pattern:

```java
try {
    doSomething();
} catch (Exception e) {
    throw new RuntimeException(e);
}
```

This is not only boilerplate — it actively makes stack traces worse.
We add an extra layer of indirection, gain no new information,
and still don’t actually recover from anything.

This, my friends, is not a language flaw but a misjudgement the developer made.

## How It Can Be Done Right

The frustrating boilerplate we just saw is not inevitable.
Some frameworks show that checked exceptions can be handled gracefully,
so the caller never has to write `catch + throw new RuntimeException`.

A great example is Spring’s `JdbcTemplate`.
When interacting with a database, JDBC throws an `SQLException`
— a checked exception that would normally force you to catch it everywhere.

Spring wraps these `SQLException`s once at the API boundary into
an unchecked <code>DataAccessException</code>.
This means you can write clean, straight-line code like:

```java
List<User>; users = jdbcTemplate.query(
    "SELECT * FROM users",
    (rs, rowNum) -> new User(rs.getLong("id"), rs.getString("name"))
);
```

No try-catch, no `RuntimeException` wrapping, and the stack trace remains clean.
If something truly goes wrong, the exception still contains
all the original information — but the caller isn’t burdened with boilerplate. 

## So… Is Java’s Exception System Bad?

In my opinion: no. Java has a solid, well-thought-out exception system.
It gives API designers the tools to clearly communicate
what can fail, how it can fail, and who is responsible for handling it.

The problem is that those tools are often used poorly —
sometimes even by very large, popular frameworks.

If Java exceptions feel painful, it’s usually not because
the language got it wrong.
It’s because someone, somewhere, chose the wrong kind of exception
and made it everyone else’s problem.

So if your stack traces are messy, don’t blame Java.
Blame whoever thought `catch (Exception e) { throw new RuntimeException(e); }` was a good idea. 
And maybe… check yourself before you wreck yourself.
