---
title: 'Using Design Patterns in Dart'
pubDate: 2022-02-10T15:00:00Z
description: 'Improve the quality and maintainability of your code with desing patterns.'
author: 'Codderlly'
image: '/blog/post-004-cover.jpg'
tags: ['flutter', 'dart']
---
## Using Design Patterns in Dart
**Design patterns** are proven, repeatable solutions to common problems in software design. By applying them, you can significantly improve the quality, maintainability, and scalability of your code. Dart, being an object-oriented language, lends itself perfectly to implementing many of the classic design patterns. In this post, we will explore some of the most relevant design patterns and how to apply them in your projects with Dart.

### What are Design Patterns?
**Design patterns** are general descriptions or templates for solving common design problems that occur repeatedly in software development. They fall into three main categories:

* **Creational Patterns.** These deal with the creation of objects, helping to make the system independent of how the objects are created, composed, and represented.
* **Structural Patterns.** These deal with the composition of classes or objects, providing ways to form larger structures.
* **Behavioral Patterns.** These deal with communication between objects, helping to improve the assignment of responsibilities between objects.

### Creational Patterns
#### Singleton Pattern

The **Singleton** is one of the most well-known design patterns. It ensures that a class has a single instance and provides a global access point to that instance.

In Dart, you can implement a Singleton using a private constructor and a static instance:

```dart
    class Logger {
        static final Logger _instance = Logger._internal();

        factory Logger() {
            return _instance;
        }

        Logger._internal();

        void log(String message) {
            print('LOG: $message');
        }
    }

    void main() {
        var logger1 = Logger();
        var logger2 = Logger();

        logger1.log('This is a log message.');
        print(identical(logger1, logger2)); // true, both are the same instance
    }
```
Here, *Logger* ensures that only one instance of the class is available across the entire application, which is ideal for handling shared resources like logs, configurations, or database connections.

#### Factory Method Pattern

The **Factory Method** provides an interface for creating objects in a superclass, but allows subclasses to alter the type of objects that will be created.

Consider the following example where we create different types of buttons:

```dart
    abstract class Button {
        void render();
    }

    class AndroidButton implements Button {
        @override
        void render() {
            print('Rendered an Android-style button');
        }
    }

    class IOSButton implements Button {
        @override
        void render() {
            print('Rendered an iOS-style button');
        }
    }

    abstract class ButtonFactory {
        Button createButton();
    }
    
    class AndroidButtonFactory extends ButtonFactory {
        @override Button createButton() {
            return AndroidButton();
        }
    }
    
    class IOSButtonFactory extends ButtonFactory {
        @override Button createButton() {
            return IOSButton();
        }
    }
    
    void main() {
        ButtonFactory buttonFactory = AndroidButtonFactory();
        Button button = buttonFactory.createButton();
        button.render();

        buttonFactory = IOSButtonFactory();
        button = buttonFactory.createButton();
        button.render();
    }
```
This pattern is useful when your code needs to work with multiple versions of the same object type and you want to encapsulate the creation logic.

### Structural Patterns

#### Adapter Pattern

The Adapter allows classes with incompatible interfaces to work together by converting the interface of one class to one that the client expects.

Imagine you have an old API that returns user data in XML format, but your new application requires JSON:

```dart
    // Old API that returns XML
    class XmlUserService {
        String getUserData() {
            return '<user><name>John Doe</name></user>';
        }
    }

    // Class expected by modern application
    class JsonUserService {
        String getUserData() {
            return '{"name": "John Doe"}';
        }
    }

    // Adapter to convert XML to JSON
    class UserServiceAdapter implements JsonUserService {
        final XmlUserService _xmlUserService;

        UserServiceAdapter(this._xmlUserService);

        @override
        String getUserData() {
            // Convert XML to JSON (simplified)
            var xmlData = _xmlUserService.getUserData();
            return '{"name": "${xmlData.replaceAll(RegExp(r'<[^>]*>'), '')}"}';
        }
    }

    void main() {
    var xmlService = XmlUserService();
    var adapter = UserServiceAdapter(xmlService);
    print(adapter.getUserData()); // Return JSON instead of XML
}
```

The **Adapter** is useful when you need to integrate legacy code with modern systems or when you work with libraries that have incompatible interfaces.

#### Decorator Pattern

The **Decorator** allows you to add additional responsibilities to an object dynamically, without modifying its structure. It is a flexible alternative to inheritance for extending functionality.

Imagine you have a Notifier class that sends notifications and you want to add additional features such as sending emails or SMS without changing the original class:

```dart
    abstract class Notifier {
        void send(String message);
    }

    class SimpleNotifier implements Notifier {
        @override
        void send(String message) {
            print('Sending notification: $message');
        }
    }

    class EmailDecorator implements Notifier {
        final Notifier _notifier;

        EmailDecorator(this._notifier);

        @override
        void send(String message) {
            _notifier.send(message);
            print('Sending email: $message');
        }
    }

    class SmsDecorator implements Notifier {
        final Notifier _notifier;

        SmsDecorator(this._notifier);

        @override
        void send(String message) {
            _notifier.send(message);
            print('Sending SMS: $message');
        }
    }

    void main() {
    Notifier notifier = SimpleNotifier();
    notifier = EmailDecorator(notifier);
    notifier = SmsDecorator(notifier);

    notifier.send('Hello, World!');
    }
```
The **Decorator pattern** is ideal for adding functionality to objects without modifying their original classes, providing great flexibility.

### Behavioral Patterns
#### Observer Pattern

The **Observer** defines a one-to-many dependency between objects, so that when one of the objects changes state, all of its dependents are notified and updated automatically.

A classic example is a subscription system for data updates:

```dart
    class Subject {
        List<Observer> _observers = [];

        void attach(Observer observer) {
            _observers.add(observer);
        }
        
        void detach(Observer observer) {
            _observers.remove(observer);
        }
        
        void notify(String event) {
            for (var observer in _observers) {
                observer.update(event);
            }
        }
    }
    
    abstract class Observer {
        void update(String event);
    }
    
    class EmailNotifier implements Observer {
        @override void update(String event) {
                print('Email Notifier: New event: $event');
        }
    }

    class SmsNotifier implements Observer {
        @override
        void update(String event) {
            print('SMS Notifier: New event: $event');
        }
    }

    void main() {
    var subject = Subject();

    var emailNotifier = EmailNotifier();
    var smsNotifier = SmsNotifier();

    subject.attach(emailNotifier);
    subject.attach(smsNotifier);

    subject.notify('New data available');
    }
```
The **Observer** pattern is useful in situations where a change in an object needs to be communicated to other objects without creating a strong dependency between them.

#### Command Pattern

The **Command pattern** turns a request into an independent object that contains all the information needed to execute the action. This is useful for operations such as undo, request queues, and transactions.

Suppose you have an application that performs several operations in a text editor:

```dart
    // Base command
    abstract class Command {
        void execute();
    }

    // Command to copy text
    class CopyCommand implements Command {
        final Editor _editor;

        CopyCommand(this._editor);

        @override
        void execute() {
            _editor.copy();
        }   
    }

    // Command to paste text
    class PasteCommand implements Command {
        final Editor _editor;

        PasteCommand(this._editor);

        @override
        void execute() {
            _editor.paste();
        }
    }

    // Text editor
    class Editor {
        void copy() {
            print('Text copied');
        }

        void paste() {
            print('Text pasted');
        }
    }

    void main() {
        var editor = Editor();
        var copyCommand = CopyCommand(editor);
        var pasteCommand = PasteCommand(editor);

        copyCommand.execute();
        pasteCommand.execute();
    }
```
The **Command pattern** is ideal for encapsulating operations that can be handled uniformly and stored for later execution.

## Conclusion
**Design patterns** are powerful tools for improving code quality and making it more flexible and maintainable. Applying these patterns in Dart will not only help you write cleaner, more structured code, but will also facilitate teamwork and software evolution in the long run.

Whether you're developing small applications or large, complex systems, using design patterns will allow you to tackle common problems efficiently and with proven solutions. As you become more familiar with these patterns, you'll see how they help you solve design problems more naturally and effectively in your Dart projects.