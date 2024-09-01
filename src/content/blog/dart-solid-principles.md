---
title: 'SOLID Principles Applied to Dart'
pubDate: 2022-01-27T05:00:00Z
description: 'The SOLID principles, proposed by Robert C. Martin (aka Uncle Bob), are a set of five fundamental guidelines designed to help you achieve maintainable and scalable code.'
author: 'Codderlly'
image: '/blog/post-003-cover.jpg'
tags: ['flutter', 'dart']
---
## SOLID Principles Applied to Dart
When it comes to software development, one of the biggest challenges is ensuring that code is easy to maintain, understand, and scale as the application grows in complexity. The **SOLID principles**, proposed by Robert C. Martin (aka Uncle Bob), are a set of five fundamental guidelines designed to help you achieve these goals. In this post, we'll explore how to apply these principles in Dart development, allowing you to write cleaner, more robust code.

### What are the SOLID Principles?
SOLID is an acronym that stands for five basic principles of object-oriented programming that can be applied to any object-oriented programming language, including Dart. These principles are:

* **Single Responsibility Principle (SRP)**
* **Open/Closed Principle (OCP)**
* **Liskov Substitution Principle (LSP)**
* **Interface Segregation Principle (ISP)**
* **Dependency Inversion Principle (DIP)**
Each of these principles addresses different aspects of software design, and when applied together, they help build flexible, robust, and maintainable systems.

#### Single Responsibility Principle (SRP)
The **Single Responsibility Principle (SRP)** states that a class should have one and only one reason to change, i.e., it should have a single responsibility or purpose.

Suppose you have a class in Dart that handles both business logic and data management for a user:

```dart
    class UserManager {
        void saveUser(User user) {
        // Logic to save the user to the database
        }

        void calculateUserScore(User user) {
        // Logic to calculate the user's score
        }
    }
```
Here, *UserManager* has two responsibilities: saving the user and calculating their score. This violates SRP because any changes to the business logic or data management could affect the class.

To implement SRP, we can separate these responsibilities into two classes:

```dart
    class UserRepository {
        void saveUser(User user) {
            // Logic for saving the user to the database
        }
    }

    class UserScoreCalculator {
        void calculateUserScore(User user) {
            // Logic for calculating the user's score
        }
    }
```
Now, *UserRepository* takes care of data persistence and *UserScoreCalculator* takes care of the business logic related to calculating the score, thus respecting the single responsibility principle.

#### Open/Closed Principle (OCP)
The **Open/Closed Principle (OCP)** states that software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification. This means that you should be able to add new functionality to a class without modifying its existing code.

Consider a class that applies discounts to products:

```dart
    class DiscountService {
        double applyDiscount(double price, String discountType) {
        if (discountType == 'fixed') {
            return price - 10;
        } else if (discountType == 'percentage') {
            return price * 0.9;
        }
        return price;
        }
    }
```
Here, if a new discount type is required to be added, you would have to modify the DiscountService class, which violates the OCP.

To comply with the OCP, you can use inheritance or polymorphism:

```dart
    abstract class Discount {
        double apply(double price);
    }

    class FixedDiscount implements Discount {
        @override
        double apply(double price) => price - 10;
    }

    class PercentageDiscount implements Discount {
        @override
        double apply(double price) => price * 0.9;
    }

    class DiscountService {
        double applyDiscount(double price, Discount discount) {
            return discount.apply(price);
        }
    }
```
Now, if you need to add a new type of discount, you just need to create a new class that implements *Discount*, without modifying *DiscountService*.

#### Liskov Substitution Principle (LSP)
The **Liskov Substitution Principle (LSP)** states that derived classes should be substitutable by their base classes without altering the expected behavior of the program. This implies that a subclass should be able to be used in place of its superclass without the code that uses it needing to know about it.

Suppose you have the following class hierarchy:

```dart
    class Bird {
        void fly() {
            // Logic for flying
        }
    }

    class Penguin extends Bird {
        @override
        void fly() {
            throw UnsupportedError('Penguins cannot fly');
        }
    }
```
Here, *Penguin* violates the Liskov Substitution Principle, as it should not behave inconsistently with the base class *Bird*.

To comply with LSP, you could refactor the class hierarchy:

```dart
    abstract class Bird {}

    abstract class FlyingBird extends Bird {
        void fly();
    }

    class Sparrow extends FlyingBird {
        @override
        void fly() {
        // Logic for flying
        }
    }

    class Penguin extends Bird {
        // Penguins can't fly, so they don't inherit from FlyingBird
    }
```
Now, *Sparrow* is a *FlyingBird* and can fly, while Penguin is just a *Bird* without the ability to fly. This respects LSP, as now every subclass complies with the expectations set by its base class.

#### Interface Segregation Principle (ISP)
The **Interface Segregation Principle (ISP)** states that no class should depend on methods that it doesn't use. In other words, it's better to have small, specific interfaces rather than large, generic interfaces.

Consider an interface with multiple responsibilities:

```dart
    abstract class Worker {
        void work();
        void eat();
    }

    class Programmer implements Worker {
        @override
        void work() {
            // Write code
        }

        @override
        void eat() {
            // Eat lunch
        }
    }

    class Robot implements Worker {
        @override
        void work() {
            // Assemble parts
        }

        @override
        void eat() {
            throw UnsupportedError('Robots don't eat');
        }
    }
```
In this case, *Robot* has to implement the *eat()* method even though it doesn't make sense for this class, which violates ISP.

To fix this, you can break the interface into smaller, more specific interfaces:

```dart
    abstract class Workable {
        void work();
    }

    abstract class Eatable {
        void eat();
    }

    class Programmer implements Workable, Eatable {
        @override
        void work() {
            // Write code
        }

        @override
        void eat() {
            // Eat lunch
        }
    }

    class Robot implements Workable {
        @override
        void work() {
            // Assemble parts
        }
    }
```
Now, *Robot* only implements the *Workable* interface, while *Programmer* implements both interfaces, *Workable* and *Eatable*, respecting the interface segregation principle.

#### Dependency Inversion Principle (DIP)
The **Dependency Inversion Principle (DIP)** states that high-level modules should not depend on low-level modules, but both should depend on abstractions. Also, abstractions should not depend on details, but details should depend on abstractions.

Consider the following example where a high-level class directly depends on a low-level class:

```dart
    class EmailService {
        void sendEmail(String message) {
            // Send email
        }
    }

    class NotificationManager {
        final EmailService emailService = EmailService();

        void sendNotification(String message) {
            emailService.sendEmail(message);
        }
    }
```
Here, *NotificationManager* directly depends on *EmailService*, which violates the DIP.

To comply with the DIP, you can introduce an abstraction:

```dart
    abstract class MessageService {
        void sendMessage(String message);
    }

    class EmailService implements MessageService {
        @override
        void sendMessage(String message) {
            // Send email
        }
    }

    class NotificationManager {
        final MessageService messageService;

        NotificationManager(this.messageService);

        void sendNotification(String message) {
            messageService.sendMessage(message);
        }
    }
```
Now, *NotificationManager* depends on the *MessageService* abstraction, and not on the concrete *EmailService* implementation, complying with the dependency inversion principle.

## Conclusion
SOLID principles are fundamental to writing high-quality code in Dart, or any other object-oriented language. Applying these principles not only improves the structure and clarity of the code, but also makes it easier to maintain and scale. By following SOLID, you can develop more robust, easier to understand, and less error-prone systems.

Implementing SOLID requires discipline and practice, but the long-term benefits are invaluable. If you are developing in Dart, starting to apply these principles will allow you to create cleaner, more flexible, and more maintainable applications.