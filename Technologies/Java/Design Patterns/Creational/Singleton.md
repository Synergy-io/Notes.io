# Singleton Pattern

## Overview

The Singleton Pattern ensures that a class has only one instance and provides a global point of access to it. This pattern is useful when exactly one object is needed to coordinate actions across the system.

## When to Use

- When you need exactly one instance of a class to control the access to some shared resource (e.g., configuration settings, logging, database connections).

## Implementation in Java

Here’s a step-by-step guide to implementing the Singleton Pattern in Java.

1. **Private constructor:** Ensure that the class cannot be instantiated from outside.
2. **Static field:** Hold the single instance of the class.
3. **Static method:** Provide a global point of access to the instance.

```java
public class Singleton {
    // Static variable to hold the single instance
    private static Singleton instance;

    // Private constructor to prevent instantiation
    private Singleton() {
        // Initialize the instance
    }

    // Static method to provide the single point of access
    public static Singleton getInstance() {
        if (instance == null) {
            // Create the instance if it doesn't exist
            instance = new Singleton();
        }
        return instance;
    }

    // Example method to demonstrate functionality
    public void showMessage() {
        System.out.println("Hello from Singleton!");
    }
}

public class SingletonPatternDemo {
    public static void main(String[] args) {
        // Get the only object available
        Singleton singleton = Singleton.getInstance();

        // Show the message
        singleton.showMessage();
    }
}
```

## Key Points to Memorize

- **Singleton ensures only one instance**: This is crucial when a single instance is required to manage the state across the application.
- **Private constructor**: Prevents external instantiation.
- **Static method**: Provides a global access point.

## Practical Example Scenario

Imagine you are building a logging system for an application. You need to ensure that all parts of the application use the same logger instance to maintain consistency in logging. The Singleton pattern ensures that all logging requests are handled by a single instance of the logger.

## Identifying When to Use the Singleton Pattern

- **Global access point**: You need a single point of access to a resource.
- **Single instance constraint**: The application requires only one instance of a class to ensure proper behavior.

### Exercises to Reinforce Learning

1. **Implement Singleton in Different Scenarios**: Try implementing the Singleton pattern in different scenarios like database connections or configuration managers.
2. **Thread Safety**: Modify the Singleton implementation to make it thread-safe.
3. **Eager Initialization**: Implement Singleton with eager initialization and compare it with lazy initialization.
