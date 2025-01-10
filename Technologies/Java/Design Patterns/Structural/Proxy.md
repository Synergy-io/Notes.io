# Proxy Pattern

## Overview

The Proxy Pattern is a structural design pattern that provides a surrogate or placeholder for another object to control access to it. This pattern involves a proxy class that represents the functionality of another class.

## When to Use

- When you need to control access to an object.
- When creating a resource-intensive object on demand is required (lazy initialization).
- When adding a layer of security to an object is necessary.
- When logging, caching, or monitoring the calls to an object without modifying the actual object.

## Implementation in Java

Here’s a step-by-step guide to implementing the Proxy Pattern in Java.

1. **Subject Interface**: Defines the common interface for RealSubject and Proxy.
2. **RealSubject**: The real object that the proxy represents.
3. **Proxy**: Controls access to the RealSubject and may perform additional functions.

## Example Scenario

Let's consider a scenario where we have an image that needs to be loaded from disk. Loading the image is resource-intensive, so we use a proxy to load it only when needed.

### Step-by-Step Implementation

1. **Subject Interface**

```java
public interface Image {
    void display();
}
```

2. **RealSubject**

```java
public class RealImage implements Image {
    private String filename;

    public RealImage(String filename) {
        this.filename = filename;
        loadFromDisk();
    }

    private void loadFromDisk() {
        System.out.println("Loading " + filename);
    }

    @Override
    public void display() {
        System.out.println("Displaying " + filename);
    }
}
```

3. **Proxy**

```java
public class ProxyImage implements Image {
    private RealImage realImage;
    private String filename;

    public ProxyImage(String filename) {
        this.filename = filename;
    }

    @Override
    public void display() {
        if (realImage == null) {
            realImage = new RealImage(filename);
        }
        realImage.display();
    }
}
```

4. **Client Code**

```java
public class ProxyPatternDemo {
    public static void main(String[] args) {
        Image image = new ProxyImage("test_image.jpg");

        // Image will be loaded from disk
        image.display();  
        System.out.println("");

        // Image will not be loaded from disk
        image.display();  
    }
}
```

## Key Points to Memorize

- **Proxy Pattern**: Provides a surrogate or placeholder for another object to control access to it.
- **Subject Interface**: Declares the common interface for RealSubject and Proxy.
- **RealSubject and Proxy**: The real object and the proxy class that controls access to the real object.

## Practical Example Scenario

Consider a scenario in a security system where you want to control access to certain resources. A proxy can be used to check user permissions before allowing access to the resource.

## Identifying When to Use the Proxy Pattern

- **Access Control**: When access to an object needs to be controlled or regulated.
- **Resource Management**: When creating a resource-intensive object only when needed.
- **Logging and Monitoring**: When you need to log, cache, or monitor the calls to an object without modifying the actual object.
- **Security**: When adding an additional layer of security to an object.

### Exercises to Reinforce Learning

1. **Extend the Example**: Add more functionality to the proxy.
2. **Real-world Application**: Implement the Proxy Pattern in a real-world scenario, such as a web service proxy that caches responses.
3. **Advanced Implementations**: Use the Proxy Pattern to implement virtual proxies for large data sets that load data on demand.
