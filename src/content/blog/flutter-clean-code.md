---
title: 'Flutter and Dart Clean Code'
pubDate: 2022-01-05T05:00:00Z
description: 'Clean code is an essential philosophy and practice in software development that focuses on writing code that is easy to understand, maintain, and modify.'
author: 'Codderlly'
image: '/blog/post-001-cover.jpg'
tags: ['flutter', 'dart']
---

## Introduction to Clean Code in Dart and Flutter
**Clean code** is an essential philosophy and practice in software development that focuses on writing code that is easy to understand, maintain, and modify.

In the context of Dart and Flutter, applying clean code not only improves code quality in the short term, but also makes the project easier to maintain and scale in the long term.

This approach is especially crucial in mobile app development, where complexity can quickly increase as new functionality is added and the user experience is refined.

## Fundamental Principles of Clean Code

* **Readability.** Code should be intuitive, allowing other developers (or yourself, in the future) to quickly understand it without the need for excessive documentation. Readability refers not only to correctly naming variables and methods, but also to the clarity with which the logical flow of the program is presented. Writing readable code involves:


* **Meaningful names.** Variable, method, and class names should clearly reflect their purpose. Avoid ambiguous abbreviations and prefer descriptive names.


* **Clear structuring.** Organize your code so that the flow of execution is clear and logical. This can include separating responsibilities within a class and modularizing code into smaller, more manageable functions.

#### Simple example

```dart
    // Unclean code
    void f(List<int> x) {
        var t = 0;
        for (var i = 0; i < x.length; i++) {
            t += x[i];
        }
        print(t);
    }

    // Clean code
    void calculateAndPrintSum(List<int> numbers) {
        final sum = numbers.reduce((value, element) => value + element);
        print('The sum is: $sum');
    }
```

In this example, readability is significantly improved by renaming *f* to *calculateAndPrintSum* and changing *x* to *numbers*, which makes the purpose of the code immediately apparent.

* **Simplicity.** Code should be as simple as possible, without being simpler than necessary. This means avoiding over-engineering and opting for clear, straightforward solutions. Simplicity also involves eliminating redundant code and preferring well-known design structures and patterns that are easy to follow.

* **Using Pure Functions.** Pure functions are an excellent example of simplicity. A pure function has no side effects and always returns the same result for the same input parameters. This not only makes the code easier to understand, but also simplifies testing and debugging.

```dart
    int add(int a, int b) => a + b;
```
This function is pure, as its result depends only on the parameters *a* and *b*, without altering the global state or depending on external variables.


* **Maintainability.** Code should be easy to modify and extend. This is achieved through a well-organized code structure, with clearly defined responsibilities and a high degree of modularity. Maintainability is also helped by good dependency management and adherence to design principles such as **SOLID**.

#### Advanced example

```dart
    // Unclean code
    class UserMgr {
        void crtUsr(String n, String e, int a) {
            // Logic to create user
        }

        void updUsr(int id, String n, String e, int a) {
            // Logic to update user
        }

        void delUsr(int id) {
            // Logic to delete user
        }
    }

    // Clean code
    class UserManager {
        Future<User> createUser(UserCreationDto userDto) async {
            final user = User.fromDto(userDto);
            await _validateUser(user);
            return await _userRepository.save(user);
        }

        Future<User> updateUser(int id, UserUpdateDto updateDto) async {
            final existingUser = await _userRepository.findById(id);
            final updatedUser = existingUser.applyUpdate(updateDto);
            await _validateUser(updatedUser);
            return await _userRepository.update(updatedUser);
        }

        Future<void> deleteUser(int id) async {
            await _userRepository.delete(id);
        }

        Future<void> _validateUser(User user) async {
            // Validation logic
        }
    }
```
In this example, we can see how applying principles such as single responsibility and separating logical layers (validation, persistence, etc.) make the *UserManager* class much more maintainable and extensible.

## Complementary Best Practices

* **Using Comments When Necessary.** ​​Although **Clean Code** advocates minimizing comments when writing self-descriptive code, there are times when comments are valuable, especially to explain complex design decisions or to point out aspects of the code that might not be apparent to other developers.

```dart
    // This method makes sure that the user has a valid age before saving it to the database.
    Future<void> _validateUser(User user) async {
        // Validation logic
    }
```
In this case, a short comment explains why the validation is being performed, which can be helpful for those unfamiliar with the context of the code.

* **Code Structure and Regular Refactoring.** A key part of keeping code clean is constant refactoring. It is not uncommon that as a project grows, certain parts of the code need to be restructured to maintain readability and simplicity. It is important to adopt a continuous improvement mindset, where code is regularly reviewed and improved.

* **Using Extensions in Dart.** Dart allows for the creation of extension methods, which are a powerful way to keep code clean and organized, especially when working with external classes or when you want to add utilities to existing types without modifying your original code.

```dart
    extension DateTimeExtensions on DateTime {
        bool isWeekend() => this.weekday == DateTime.saturday || this.weekday == DateTime.sunday;
    }
```
With this extension, any *DateTime* instance can now use the *isWeekend()* method naturally, improving code expressiveness and reusability.

## Summary

**Clean Code** in Dart and Flutter is not just a matter of following rules or conventions, but of adopting a mindset that prioritizes code quality in terms of readability, simplicity, and maintainability.

By applying these principles, you not only improve the developer experience, but you also create code that is easier to scale, test, and debug, resulting in more robust and reliable software.