---
title: 'Get_it and Testing in Flutter'
pubDate: 2022-02-10T15:00:00Z
description: 'How to maintain testability and avoid direct dependency injection.'
author: 'Codderlly'
image: '/blog/post-005-cover.jpg'
tags: ['flutter', 'dart']
---
## Get_it and Testing in Flutter: How to Maintain Testability and Avoid Direct Dependency Injection

In Flutter, dependency injection is a key practice that allows you to write cleaner, more modular, and more easily testable code. However, managing these dependencies can get complicated, especially when it comes to writing unit tests. A popular tool that makes dependency management easier in Flutter is [**get_it**](https://pub.dev/packages/get_it), a service locator that allows you to access dependencies globally.

This post delves into how to use **get_it** in Flutter, focusing on how to maintain the testability of your code and avoid the problems that often arise with direct dependency injection. We'll explore how to structure your code to be flexible, testable, and maintainable, even when you use get_it to manage dependencies.

### What is get_it and why is it useful in Flutter?
**get_it** is a Dart package that implements the **service locator pattern**. This pattern provides a way to register and retrieve services or dependencies from a centralized container anywhere in the application. This means that you can avoid the need to explicitly inject dependencies through constructors or methods.

### Advantages of get_it

* **Centralization.** All dependencies are registered in one place, making them easier to manage and configure.
* **Global access.** Registered dependencies can be accessed from anywhere in the application without the need to pass them from one class to another.
* **Simplicit.y** It allows easy access to services or instances without the need to manually create each instance.

However, using a service locator like **get_it** can also introduce some challenges, particularly in terms of testability. By hiding dependency injection behind a global container, it can become more difficult to replace these dependencies with mocks or fakes in testing.

### Testability with get_it: Addressing the Challenges
Despite the benefits that **get_it** offers in terms of simplicity and centralization, its use can complicate unit testing if not implemented correctly. The main reason is that when dependencies are retrieved implicitly from **get_it**, code can become less predictable and harder to isolate during testing.

Here are some strategies for using **get_it** while maintaining the testability of your code.

#### 1. Registering and Replacing Dependencies in the Testing Context

One of the main techniques for maintaining testability when using **get_it** is to register mocks instead of the actual implementations of the dependencies within the test environment.

Imagine you have an *ApiService* that provides data to your application:

```dart
    class ApiService {
        Future<String> fetchData() async => "Real Data";
    }
```

You can register this service in **get_it** and retrieve it anywhere in your application:

```dart
    GetIt getIt = GetIt.instance;

    void setup() {
        getIt.registerSingleton<ApiService>(ApiService());
    }
```
However, when testing, it is preferable to use a mock version of *ApiService*:

```dart
    class MockApiService extends Mock implements ApiService {}

    void main() {
    final getIt = GetIt.instance;

    setUp(() {
        // Replace ApiService with a mock for testing
        getIt.registerSingleton<ApiService>(MockApiService());
    });

    tearDown(() {
        // Reset the state of get_it after each test
        getIt.reset();
    });

    test('fetchData returns "Mocked Data"', () async {
        final apiService = getIt<ApiService>();
        when(apiService.fetchData()).thenAnswer((_) async => "Mocked Data");

        expect(await apiService.fetchData(), "Mocked Data");
    });
    }
```
* Replacement during *setUp*: When using **get_it** in a test environment, make sure to replace dependencies with mocks before each test. This allows you to control the behavior of the dependencies and isolate the code you are testing.
* Reset after tearDown: Always reset the state of get_it after each test to avoid interference between tests.
#### 2. Using Factories Instead of Singletons

When using **get_it**, you have the option to register dependencies as *singletons* or *factories*. Singletons maintain a single instance shared across the application, while factories create a new instance each time it is requested.

Why use factories for testing:

* **Test isolation.** By using factories, you can ensure that each test receives a new instance of the dependency, avoiding side effects between tests.
* **Flexibility.** Factories allow you to dynamically change the instance that is returned, which is useful when different tests require different configurations.

```dart
    getIt.registerFactory<ApiService>(() => MockApiService());
```
Each time *getIt<ApiService>()* is called, a new instance of *MockApiService* will be created, ensuring a clean environment for each test.

#### 3. Direct Injection with Optional Constructors

To maximize testability, even when using **get_it**, you can provide direct dependency injection options via optional constructors. This allows you to keep your code flexible and easily testable without relying exclusively on **get_it**.

```dart
    class MyClass {
        final ApiService _apiService;

        // Optional constructor that allows for direct dependency injection
        MyClass([ApiService? apiService])
        : _apiService = apiService ?? GetIt.instance<ApiService>();

        Future<String> getData() {
        r   eturn _apiService.fetchData();
        }
    }
```

In this case:

* **During production.** *MyClass* will use the *ApiService* instance registered in **get_it** if one is not explicitly provided.
* **During testing.** You can inject a mock directly when instantiating *MyClass* to ensure that your test is completely isolated from the global container.

Benefits of this approach:

* **Clarity.** Using optional constructors makes it clear what the dependencies of each class are, keeping the code more explicit.
* **Testability.** By allowing direct injection, you simplify unit testing, as you do not need to rely exclusively on **get_it** to handle dependencies.

#### 4. Resetting and Managing State in get_it

A crucial aspect when using **get_it** is ensuring that its state is handled properly during testing. If you don't reset **get_it** after each test, you could end up with unwanted shared instances and state that affect the reliability of your tests.

After each test, it is recommended to call **getIt.reset()** to clean up all registered instances. This ensures that each test starts with a clean **get_it** container, avoiding interference.

```dart
    tearDown(() {
        getIt.reset();
    });
```

## Conclusion

Using **get_it** in Flutter provides a powerful and easy way to manage dependencies, especially in complex applications where dependencies are shared across multiple components. However, to maintain testability, it is important to follow a few key practices:

* **Registering Mocks.** During testing, make sure to register mocks in **get_it** to substitute for real dependencies.
* **Using Factories.** Consider registering dependencies as factories instead of singletons to ensure that each test gets a new instance.
* **Optional Constructors.** Provide optional constructors to allow direct dependency injection, which makes unit testing easier and code more explicit.
* **Managing get_it State.** Reset **get_it**'s state after each test to prevent tests from affecting each other.


By combining these techniques, you can enjoy the simplicity and flexibility that **get_it** offers without sacrificing the testability of your code. Not only will this make your code easier to maintain, but it will also ensure that your tests are reliable, isolated, and effective, which ultimately translates to faster, less error-prone development.

With these practices in mind, you can effectively incorporate **get_it** into your Flutter projects, knowing that your code will remain clean, modular, and testable, even in the largest and most complex applications.