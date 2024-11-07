# Design Patterns in Software Development

Design patterns are well-established solutions to common problems in software design. They offer templates for solving frequently encountered issues in software development. These patterns help create reusable, efficient, and flexible code by promoting separation of concerns, modularity, and low coupling between components.

---

## Table of Contents
1. [Creational Design Patterns](#1-creational-design-patterns)
2. [Structural Design Patterns](#2-structural-design-patterns)
3. [Behavioral Design Patterns](#3-behavioral-design-patterns)
4. [Benefits of Using Design Patterns](#4-benefits-of-using-design-patterns)
5. [Conclusion](#5-conclusion)

---

## 1. Creational Design Patterns
Creational patterns focus on object creation mechanisms, aiming to create objects in a manner suitable to the situation.

- [Singleton](#Singleton-Pattern): Ensures a class has only one instance and provides a global access point to it.
- [Simple factory](#Simple-factory): Provides a single class responsible for creating instances of other classes based on input parameters.
- [Factory Method](#Factory-Method-Pattern): Defines an interface for creating objects, allowing subclasses to alter the type.
- **Abstract Factory**: Provides an interface for creating families of related or dependent objects.
- **Builder**: Separates the construction of a complex object from its representation.
- **Prototype**: Creates new objects by copying an existing object (the prototype).

---

## 2. Structural Design Patterns
Structural patterns deal with the composition of classes or objects to simplify system organization.

- **Adapter**: Allows incompatible interfaces to work together.
- **Bridge**: Separates an abstraction from its implementation.
- **Composite**: Composes objects into tree structures to represent part-whole hierarchies.
- **Decorator**: Adds new functionality to an object dynamically.
- **Facade**: Provides a simplified interface to a complex subsystem.
- **Flyweight**: Shares data to reduce the cost of creating and managing similar objects.
- **Proxy**: Provides a surrogate or placeholder to control access to another object.

---

## 3. Behavioral Design Patterns
Behavioral patterns focus on communication between objects and how they interact.

- **Chain of Responsibility**: Passes a request along a chain of handlers until it is handled.
- **Command**: Encapsulates a request as an object, allowing for parameterization of clients with queues, requests, and operations.
- **Interpreter**: Defines a representation for a language’s grammar and provides an interpreter for it.
- **Iterator**: Provides a way to access elements of a collection sequentially.
- **Mediator**: Reduces dependencies between objects by centralizing communication.
- **Memento**: Captures an object's state for potential restoration later.
- **Observer**: Defines a dependency between objects for automatic updates.
- **State**: Allows an object to alter its behavior when its state changes.
- **Strategy**: Defines a family of interchangeable algorithms.
- **Template Method**: Defines the skeleton of an algorithm, deferring specific steps to subclasses.
- **Visitor**: Separates operations from the objects they operate on.

---

## Benefits of Using Design Patterns
- **Efficiency**: Patterns save development time by providing tried-and-tested solutions.
- **Reusability**: They encourage modular and reusable code.
- **Scalability**: Patterns make systems easier to expand and maintain.
- **Communication**: Patterns establish a common language among developers.
- **Flexibility**: They allow for changes in the system with minimal impact.

---

## Conclusion
Design patterns serve as blueprints for building flexible, reusable, and reliable software. Mastering them enhances problem-solving abilities and prepares developers for complex, real-world challenges in software development.

---

## Singleton Pattern

The Singleton pattern is a **creational design pattern** that ensures a class has only one instance and provides a global point of access to that instance. This pattern is useful for resources like logging systems, configuration managers, or database connections, where only a single instance is needed across the application.

---

## Key Features of Singleton Pattern
1. **Single Instance**: Guarantees that only one instance of the class is created.
2. **Global Access**: Provides a way to access this single instance from anywhere in the application.
3. **Lazy Initialization**: The instance is created only when it is first needed.

### When to Use Singleton Pattern
- When exactly one instance of a class is required.
- For classes that handle tasks like logging, configuration settings, or database connections.

---

## Singleton Pattern Implementation in Laravel

### Method 1: Manual Implementation of Singleton in Laravel

This example demonstrates creating a Singleton class for a Logger service.

#### Step 1: Define the Singleton Class

```php
<?php

namespace App\Services;

class LoggerService
{
    // Holds the single instance of the LoggerService class
    private static $instance = null;

    // Make the constructor private to prevent instantiation
    private function __construct()
    {
        // Initialize any resources here (e.g., open file, set up configuration)
    }

    // Prevent the instance from being cloned
    private function __clone() {}

    // Prevent unserializing the instance
    private function __wakeup() {}

    /**
     * Get the Singleton instance of the LoggerService.
     *
     * @return LoggerService
     */
    public static function getInstance()
    {
        if (self::$instance === null) {
            self::$instance = new self();
        }

        return self::$instance;
    }

    /**
     * Log a message to a file or storage.
     *
     * @param string $message
     */
    public function log($message)
    {
        file_put_contents(storage_path('logs/custom.log'), $message . PHP_EOL, FILE_APPEND);
    }
}
```
**Explanation:** The LoggerService class has a private static property $instance that holds the single instance. A getInstance() method creates and returns the instance if it doesn’t already exist.

Step 2: Using the Singleton Class in Laravel
```php
<?php
use App\Services\LoggerService;

class SomeController extends Controller
{
    public function logMessage()
    {
        $logger = LoggerService::getInstance();
        $logger->log("This is a log message from the Singleton pattern.");
    }
}
```
**Method 2:** Using Laravel’s Service Container
Laravel’s Service Container provides an easy way to register a class as a singleton using the singleton method.

**Step 1:** Register the Service in a Service Provider
In the AppServiceProvider:

```php
<?php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use App\Services\LoggerService;

class AppServiceProvider extends ServiceProvider
{
    public function register()
    {
        $this->app->singleton(LoggerService::class, function ($app) {
            return new LoggerService();
        });
    }
}
```
**Step 2:** Using the Singleton in a Controller

```php
<?php
use App\Services\LoggerService;

class SomeController extends Controller
{
    protected $logger;

    public function __construct(LoggerService $logger)
    {
        $this->logger = $logger;
    }

    public function logMessage()
    {
        $this->logger->log("This is a log message using Laravel's singleton service container.");
    }
}
```
**Explanation:** By injecting LoggerService in the controller, Laravel provides the single instance managed by the service container.
Pros and Cons of Singleton Pattern
**Pros:**

Controlled Access: Ensures only one instance, simplifying resource management.
Lazy Initialization: Only creates an instance when needed.
Global Access: Allows centralized access to critical services or resources.

**Cons:**

**Global State:** Can introduce global state, which may make testing and debugging harder.
**Scalability:** May not suit highly scalable applications, especially in multi-threaded or distributed environments.
The Singleton pattern is powerful in certain scenarios but should be used with caution, particularly in large applications. Laravel’s Service Container provides a clean and maintainable way to implement this pattern, offering dependency injection and ease of testing.

---
## Simple Factory
The Simple Factory pattern is useful in situations where you want to centralize and simplify the creation of objects. In Laravel, this can be handy for services like sending notifications through different channels (e.g., Email, SMS, Push Notification). Let's use this example to illustrate how a simple factory might be implemented.
A Simple Factory is not technically a design pattern but rather a technique to encapsulate object creation. It has a single factory class with a method that returns instances of different classes based on input.

*When to Use:* When you have a straightforward need to instantiate objects based on some condition or input.

**Scenario:** Notification Service Factory
Suppose you have an application that needs to send notifications through different channels. Depending on the notification type (email, SMS, or push notification), you want to create different notification service classes.

Instead of directly instantiating these classes wherever they’re needed, a Simple Factory can be used to streamline object creation, making the code more maintainable and scalable.

*Step 1:* Define the Notification Interfaces and Classes
Define an interface for notifications, and then create classes for each type of notification.

Notification Interface

```php
<?php

namespace App\Services\Notifications;

interface NotificationService
{
    public function send($recipient, $message);
}
?>
```
*Email Notification Class*
```php
<?php

namespace App\Services\Notifications;

class EmailNotification implements NotificationService
{
    public function send($recipient, $message)
    {
        // Logic to send email notification
        echo "Sending Email to $recipient: $message";
    }
}
?>
```
*SMS Notification Class*
```php
<?php

namespace App\Services\Notifications;

class SmsNotification implements NotificationService
{
    public function send($recipient, $message)
    {
        // Logic to send SMS notification
        echo "Sending SMS to $recipient: $message";
    }
}
?>
```

*Push Notification Class*

```php
<?php

namespace App\Services\Notifications;

class PushNotification implements NotificationService
{
    public function send($recipient, $message)
    {
        // Logic to send push notification
        echo "Sending Push Notification to $recipient: $message";
    }
}
?>
```

*Step 2:* Create the Simple Factory Class
The factory class will determine which notification class to instantiate based on a parameter. This centralizes the logic and allows you to add or modify notification types more easily.

```php
<?php

namespace App\Services;

use App\Services\Notifications\EmailNotification;
use App\Services\Notifications\SmsNotification;
use App\Services\Notifications\PushNotification;
use InvalidArgumentException;

class NotificationFactory
{
    public static function create(string $type)
    {
        return match($type) {
            'email' => new EmailNotification(),
            'sms' => new SmsNotification(),
            'push' => new PushNotification(),
            default => throw new InvalidArgumentException("Notification type $type is not supported.")
        };
    }
}
```
Here, the create method takes in a type string, which decides which notification service to instantiate. If an unsupported type is provided, it throws an exception.

*Step 3:* Using the Factory in a Controller
In a Laravel controller, you can now use the factory to send notifications without worrying about the specific notification service implementation.

```php
<?php

namespace App\Http\Controllers;

use App\Services\NotificationFactory;
use Illuminate\Http\Request;

class NotificationController extends Controller
{
    public function sendNotification(Request $request)
    {
        $type = $request->input('type'); // 'email', 'sms', or 'push'
        $recipient = $request->input('recipient');
        $message = $request->input('message');

        try {
            $notificationService = NotificationFactory::create($type);
            $notificationService->send($recipient, $message);
            return response()->json(['status' => 'Notification sent successfully']);
        } catch (\InvalidArgumentException $e) {
            return response()->json(['error' => $e->getMessage()], 400);
        }
    }
}
```
*With this setup:*

The controller uses the factory to create the appropriate notification service.
The logic for creating each service type is centralized in the NotificationFactory class, making it easier to manage and extend.

*Pros and Cons of Simple Factory*

*Pros:*

1. Encapsulation of Object Creation: Centralizes the creation logic, keeping it separate from business logic.
2. Code Reusability: Reuse the factory method across various parts of the application, making it more modular.
3. Single Responsibility: Keeps the responsibility of instantiation within the factory, helping maintain SRP (Single Responsibility Principle).

*Cons:*

1. Limited Flexibility: If you add many notification types, the factory can become complex, and it may be harder to manage (can lead to many if-else or switch statements).
2. Not Extensible for Subclasses: A Simple Factory doesn’t support inheritance or polymorphism as well as more advanced patterns like Factory Method or Abstract Factory.
3. Hard to Unit Test: Direct instantiation inside the factory can make it harder to mock or replace classes during testing, unless you use dependency injection.

*Alternative:* Dependency Injection in Laravel

In Laravel, you might often use dependency injection to avoid directly using a factory in the controller. You could bind your services in a Service Provider, allowing Laravel to resolve the dependencies automatically.

This Simple Factory approach is effective for scenarios where you have only a few types to instantiate, and where flexibility is not a primary concern.
---

**Factory Method Pattern**
The Factory Method Pattern is a creational design pattern that defines an interface for creating objects but allows subclasses to alter the type of object that will be created. This pattern is useful when there is a need to instantiate different classes based on certain conditions while ensuring that the creation logic is encapsulated.

We define a FeedbackChannel interface to provide a uniform way to handle feedback.
We create different classes for each feedback channel (e.g., SMSFeedback, EmailFeedback, and WebSurveyFeedback).
We use a Factory Method to determine the appropriate channel class dynamically.

**Scenario: Feedback Channel System**
A business collects customer feedback through various channels and wants a system to handle each channel’s feedback in a unique way while keeping the creation logic organized.

*Step 1:* Define the FeedbackChannel Interface
The FeedbackChannel interface defines the common method collectFeedback, which each feedback channel will implement.

```php
<?php

namespace App\Services\Feedback;

interface FeedbackChannel
{
    public function collectFeedback($customerId, $message);
}
```
Step 2: Create Concrete Feedback Channel Classes
Each feedback channel will implement the FeedbackChannel interface and provide specific logic.

SMS Feedback Channel
```php
<?php

namespace App\Services\Feedback;

class SMSFeedback implements FeedbackChannel
{
    public function collectFeedback($customerId, $message)
    {
        // Logic to send SMS and log feedback
        echo "Collecting feedback via SMS from Customer $customerId: $message";
    }
}
```
Email Feedback Channel
```php
<?php

namespace App\Services\Feedback;

class EmailFeedback implements FeedbackChannel
{
    public function collectFeedback($customerId, $message)
    {
        // Logic to send an email and log feedback
        echo "Collecting feedback via Email from Customer $customerId: $message";
    }
}
```
Web Survey Feedback Channel
```php
<?php

namespace App\Services\Feedback;

class WebSurveyFeedback implements FeedbackChannel
{
    public function collectFeedback($customerId, $message)
    {
        // Logic to display survey form on web and log feedback
        echo "Collecting feedback via Web Survey from Customer $customerId: $message";
    }
}
```
Step 3: Create the FeedbackChannelFactory Class
In the Factory Method pattern, the factory is an abstract class that can be extended. Each subclass of the factory will instantiate a different feedback channel based on specific criteria.

```php
<?php

namespace App\Services\Feedback;

use InvalidArgumentException;

abstract class FeedbackChannelFactory
{
    // The factory method is abstract and is implemented by subclasses
    abstract protected function createFeedbackChannel(): FeedbackChannel;

    public function collect($customerId, $message)
    {
        // Create the feedback channel
        $channel = $this->createFeedbackChannel();
        
        // Use the feedback channel to collect feedback
        return $channel->collectFeedback($customerId, $message);
    }
}
```

Step 4: Implement Factory Subclasses for Each Channel
Each subclass overrides the createFeedbackChannel method to instantiate the specific feedback channel.

SMS Feedback Factory
```php
<?php

namespace App\Services\Feedback;

class SMSFeedbackFactory extends FeedbackChannelFactory
{
    protected function createFeedbackChannel(): FeedbackChannel
    {
        return new SMSFeedback();
    }
}
```
Email Feedback Factory

```php
<?php

namespace App\Services\Feedback;

class EmailFeedbackFactory extends FeedbackChannelFactory
{
    protected function createFeedbackChannel(): FeedbackChannel
    {
        return new EmailFeedback();
    }
}
```
Web Survey Feedback Factory
```php
<?php

namespace App\Services\Feedback;

class WebSurveyFeedbackFactory extends FeedbackChannelFactory
{
    protected function createFeedbackChannel(): FeedbackChannel
    {
        return new WebSurveyFeedback();
    }
}
```
Step 5: Using the Factories in a Controller
Now that we have our factory classes, let’s use them in a Laravel controller to collect feedback from different channels.

```php
<?php

namespace App\Http\Controllers;

use App\Services\Feedback\SMSFeedbackFactory;
use App\Services\Feedback\EmailFeedbackFactory;
use App\Services\Feedback\WebSurveyFeedbackFactory;
use Illuminate\Http\Request;

class FeedbackController extends Controller
{
    public function collectFeedback(Request $request)
    {
        $type = $request->input('type'); // 'sms', 'email', or 'web'
        $customerId = $request->input('customer_id');
        $message = $request->input('message');

        switch ($type) {
            case 'sms':
                $factory = new SMSFeedbackFactory();
                break;
            case 'email':
                $factory = new EmailFeedbackFactory();
                break;
            case 'web':
                $factory = new WebSurveyFeedbackFactory();
                break;
            default:
                return response()->json(['error' => 'Invalid feedback type'], 400);
        }

        $factory->collect($customerId, $message);
        return response()->json(['status' => 'Feedback collected successfully']);
    }
}
```
In this setup:

The controller dynamically chooses the appropriate factory based on the type parameter.
Each factory then uses its specific feedback channel to collect feedback, ensuring modularity and adherence to the Factory Method pattern.

Pros and Cons of Factory Method Pattern
Pros
1. Flexibility: Allows for dynamic creation of objects, making it easy to add new feedback channels without modifying existing code.
2. Encapsulation: Keeps the instantiation logic separate from business logic, adhering to the Single Responsibility Principle.
3. Open/Closed Principle: New feedback channels can be added by creating new factory subclasses, without modifying the existing code.

Cons
1. Complexity: Adds additional classes (factories and products) that may seem like an overhead for simple cases.
2. Overhead in Simple Use-Cases: The Factory Method pattern may be more complex than needed for cases with few variations.

Conclusion
The Factory Method pattern is a powerful way to create instances dynamically based on conditions, particularly when dealing with scenarios requiring multiple classes with different behaviors. In this Laravel example, it allows for flexible handling of customer feedback channels, making the code easier to manage and extend. This pattern is especially useful when the number of classes can grow over time, as it allows you to encapsulate creation logic in separate factory classes.


The Factory Method Pattern and the Simple Factory Pattern are related but differ in their flexibility and structure. Here’s a quick breakdown of how they differ:

Key Differences
Responsibility for Object Creation:

Simple Factory Pattern: Centralizes all object creation logic in a single, non-polymorphic class. It provides one factory class with a method (e.g., create()), which handles instantiation based on conditions.
Factory Method Pattern: Defines an interface for creating objects but leaves the instantiation to subclasses. Each subclass implements its own factory method, providing a more flexible structure for creating objects.
Polymorphism:

Simple Factory: Does not use polymorphism. All the creation logic is hard-coded within one factory class.
Factory Method: Uses polymorphism. The base factory class defines an abstract creation method, and each subclass implements it to produce a specific type of object.
Flexibility:

Simple Factory: Suitable for cases with a limited number of types to be created. Adding new types requires modifying the factory, potentially violating the Open/Closed Principle.
Factory Method: Provides better extensibility by allowing new subclasses to implement the creation method, supporting Open/Closed Principle.
When to Use Each
Simple Factory Pattern: When you have a limited and predictable set of objects to instantiate, and the creation logic does not need to be altered.
Factory Method Pattern: When the types of objects may vary significantly, or when subclasses need more control over the instantiation process. This pattern is ideal when the instantiation logic might evolve or if you need to adhere to polymorphic behavior.
In Our Example
The example provided above for the Customer Feedback system is indeed an example of the Factory Method Pattern because:

Each subclass (e.g., SMSFeedbackFactory, EmailFeedbackFactory) is responsible for instantiating its specific feedback channel.
We achieve flexibility by letting each factory subclass decide which feedback class to instantiate, making it easier to add or remove channels without changing the existing code.
In contrast, a Simple Factory Pattern in this context would look like a single FeedbackFactory class with a method that instantiates SMSFeedback, EmailFeedback, or WebSurveyFeedback based on an input parameter, without using separate subclasses for each feedback channel.

This makes the Factory Method Pattern more flexible and extensible but adds complexity, which is suitable here because feedback channels may expand or have unique handling logic in a real-world application like Medallia Concierge.


