---
title: "Tutorial on Object Oriented Programming and Inheritance (Java)"
description: "Finally a good tutorial on OOP and its implementation in Java"
layout: blog
date: 2026-04-04
---

I've seen a couple of online tutorials regarding this topic on various platforms. However, to me most of these tutorials did not do a good job at explaining the core idea behind object orientation and inheritance: Maybe its nice to know how to have something called **Dog** print "woof" but especially when learning to code, you might look for an example with more connection to actual use cases.

So I decided to write my own tutorial on this topic - Enjoy!

---

## _This tutorial is not yet completed_

## Introduction

With this tutorial, I strike to explain the theory behind Object Oriented Programming and Inheritance in the way I wished I had learned these concepts: Using actual examples for use cases instead of abstract ideas.

Instead of using the wildly popular **animal hierarchy** example, I will focus this tutorial on a **user management system** which we are going to develop throughout this article.

The language we are going to use for this is the **Java Programming language** since its quite object-based and therefore a good way to start explaining this topic.

However, this article does not require you to have a deep knowledge of Java, neither does it apply to Java only. The core principles taught apply to almost every programming language out there.

---

**Note**: This is a longer tutorial. For better retention, consider working through one section per day (sections are labeled with Roman numerals) so you have more time to really take in the information given.

---


## I: Objects and Instantiation - The Basics

### 1. The Scaling Problem: Multiple copies of the same code?

Our tutorial starts with a simple scenario:

We want to build an application where users can register with a user name and an age. We then want to store all of these data together. So how would we do that?

The first idea that comes to my mind is creating a bunch of variables to store these data:

```java
String userName1;
int userAge1;

String userName2;
int userAge2;

String userName3;
int userAge3;
```

However, this code has an obvious problem: We only have a limited amount of variables, so if our app was to have more than 3 users, we would not be able to store all the users' information. In addition, this code gets hard to manage very quickly, is error-prone, and impossible to scale dynamically.

So we need some way to automatically create more variables if they are needed so that if a fourth user was to sign up, we would have new copies of our user variables that can be filled with the new user's data.

This is the exact idea of instantiation.

---

### 2. Classes: A blueprint for data

To solve our problem, we can use a so-called class that contains the variables we want to have for a single user. You can think of it as a blueprint containing the data we later want to create more of. These data are called **fields**.

In Java, we'd simply create a new file named after the class and then define it using the **public class** keywords:

```java
// File: User.java
public class User {

}
```

Now we can fill in the data we want to store per user:

```java
public class User {
    public String name;
    public int age;
}
```

Notice that _(other than normally)_ we do not use the **static** keyword when defining the variables. Instead, we leave it out and directly write `public <TYPE> <NAME>`. This is because we want these fields to be a part of the "blueprint" that defines which variables a user should have. Using `static` would mean all users share the same name, which obviously is not what we want.

---

### 3. Instantiation: Copying the blueprint into an instance

Now that we have our `User` class with the **name** and **age** fields, we can use it to create a user that can store these variables. In other words: We want to create a new instance of this class.

In Java, we do so like this:

```java
new User();
```

The `new` keyword tells Java to create a new instance of a class. To define the class to be instantiated, we write the class name, followed by parenthesis.

But this new instance is only useful if we have a way to reference it. So lets store the user instance in a variable, allowing us to access it later:

```java
User userInstance = new User();
```

This follows the same pattern every other variable declaration in Java follows:

1. We define the type of variable we want to store. In our case, we want to store an instance of `User`.
2. We give the variable a name to access it later. I decided to go with _userInstance_ but you could also name it _bob_ or anything else.
3. A `=` to set the value of the variable
4. The value to assign. Because we want to create and store an instance of **User**, this is `new User()`.

This pattern can now be repeated multiple times:

```java
User user1 = new User();
User user2 = new User();
User user3 = new User();
User user4 = new User();
User user5 = new User();
```

---

### 4. Instance Interaction: Accessing and modifying the variables in an instance

As soon as we have an instance of our `User`, we can access and modify the variables inside it like normal variables.

All we have to do is add a `.<VARIABLE NAME>` after the user variable:

```java
// Modify values
user1.name = "John";
user1.age = 31;

// Access values
System.out.println(user1.name);
System.out.println(user1.age);
```

The great thing about this is that the variables in two instances are completely independent of one another. This means, modifying one user's name will not affect the name variables of the other users even though they are all defined by the same `public String name` in the user class:

```java
User user1 = new User();
User user2 = new User();

user1.name = "John";
user2.name = "Jane";

System.out.println(user1.name); // Outputs "John"
System.out.println(user2.name); // Outputs "Jane"
```

Every instance is like a separate form filled out from the same template: Changing one form doesn’t affect the other. This introduces scalabilty to our code and adds much-needed structure.

---


## II: Initialization: Constructors in your classes

### 1. No-Arg Constructors: Running code on instantiation

In some cases, you might want to execute some code when instantiating. In our example, we want to notify the app's developer once a new user object was created.

To do so, we can define a **constructor**. You can think of it as a special method that is automatically invoked as soon as you create an instance of the class using the `new` keyword:

```java
public class User {
    public String name;
    public int age;

    public User() {
        
    }
}
```

The declaration of a contructor is quite similar to a method declaration. The only difference is that the constructor does not allow the modifier `static` and does not define a return type / `void`.

Now that we have our constrcutor, we can use it to print the notification:

```java
public class User {
    public String name;
    public int age;

    public User() {
        System.out.println("A new instance of User was created");
    }
}
```

When now calling `new User()`, the message will be printed to the console.


### 2. Initializing Defaults in Constructors: Defining default values for the variables

Currently, our `User` class just holds the empty **name** and **age** variables. If we were to directly output these, we'd get this:

```java
User userInstance = new User();

System.out.println(userInstance.name); // Prints "null"
System.out.println(userInstance.age);  // Prints "0"
```

So we might want to define a default value for these fields. This can easily be done by adding a few lines to our constructor:

```java
public class User {
    public String name;
    public int age;

    public User() {
        name = "Unknown Name";
        age = 6;
    }
}

// For simplification purposes, the notification about newly created users has been removed
```

If we now were to directly access the fields, we'd get the fallback name _"Unknown Name"_ and the age _6_ which is the minumum age to use our app. But we can still change the value of these fields the way we are used to:

```java
User userInstance = new User();

System.out.println(userInstance.name); // Prints "Unknown Name"

userInstance.name = "Jane";

System.out.println(userInstance.name); // Prints "Jane"
```

---

### 3. Parameterized Constructor: Directly defining the values of our fields when instantiating

Currently, using our class can be quite tedious since we have to manually set every value of the new instance after it was created:

```java
User user = new User();
user.name = "John";
user.age = 31;

// The variable was called "user" instead of "userInstance" to keep the code shorter. This choise is purely stylistic
```

The more variables this class contains, the more lines of code will be needed for a single instantiation and initialization process. Luckily, we have a simple way to avoid all of this boilerplate: By adding arguments to our constructor.

Arguments can be added to a constructor the same way they are added to a method _(by defining the `<TYPE> <NAME>` inside the parenthesis)_:

```java
public class User {
    public String name;
    public int age;

    public User(String name, int age) {
        
    }
}
```

When a caller now wants to instantiate the class, they will have to provide the required arguments inside the parenthesis:

```java
new User("John", 31);

// Instantiation without arguments will no longer work
```

All we now have to do is assign the contructor's arguments to the variables inside the class. But if we try to do so, we will quickly face an issue:

```java
public class User {
    public String name;
    public int age;

    public User(String name, int age) {
        name = name; // Does not change the class' field
        age = age;   // Does not change the class' field
    }
}
```

Because the constructor's arguments have the same name as the class' fields, the arguments will thake priority over the fields since they are "closer" to the code refering them. This means, that by calling `name = name`, we actually set the value of the constrcutor's argument **name** to the value of the constructor's argument **name** which effectively has no effect.

To tell Java, that we mean to access the fields instead of the arguments, we can use the keyword `this`. The keyword can only be used inside an instantiated class and references the current instance. So by calling `this.name` we explicitely tell Java that we want to access the current instance's field **name** and not the constructor argument:

```java
public class User {
    public String name;
    public int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

Now, we set this instance's field **name** to the value of the constructor argument **name**.

And with that we have collapsed the long instantiation and initialization process into one line of code, keeping the rest of the usage the same:

```java
// Create and initialize (1 line)
User user = new User("John", 31);

// Access normally
System.out.println(user.name);

// Change normally
user.name = "Jane";
```

---

### 4. Constructor Overloading: Providing multiple ways to instantiate the class

Similar to normal Java methods, constructors can be **overloaded**. That means that you can have multiple constructors for the same class as long as the number and type of constructor arguments differ. When instantiating a class, Java will automatically understand which constructor you want to use based on the argument you give it.

In our example, we might want to be able to create a test user that is initialized to some default values but at the same time want to make use of parameterized constructors for simpler initialization.

We can do so by defining two constructors: One with arguments for simple creation and one without arguments that automatically initializes the fields to some defualts:

```java
public class User {
    public String name;
    public int age;

    public User() {
        name = "Unknown Name";
        age = 6;
    }

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

Now we have two ways to initialize a user:

```java
User user1 = new User();
User user2 = new User("Jane", 24);

System.out.println(user1.name); // Prints the default: "Unknown Name"
System.out.println(user2.name); // Prints the specified name: "Jane"
```

However, this introduces **multiple sources of truth** in our code. This means, there are multiple positions at which the values are initialzed. When programming, we try to avoid this sice it makes debugging your code harder and adds redundant complexity.

Instead, you should always try to set the values in the constructor with the most arguments, making it your **single source of truth**. All other constructors should delegate to that one, defining their defaults as arguments for the final constructor.

To make a constructor delegate to another construcor, we can (again) use the magic keyword `this` which references our current instance. By adding parenthesis after the keyword, we can specify that we want to access a constructor:

```java
public class User {
    public String name;
    public int age;

    public User() {
        this("Unknown Name", 6);
    }

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

Keep in mind, that calling another constructor has to be the first action to be executed inside a constructor sice you have to create an instance before being able to use it. Also, you can only delegate to max 1 other constructor.

> Since _Java 25_, **Flexible Constructor Bodies** allow you to perform some operations before delegating to another constructor. However, you cannot expose instance-dependent values before the instance was created. ([Learn more](https://docs.oracle.com/en/java/javase/25/language/flexible-constructor-bodies.html))



## _To Be Continued_