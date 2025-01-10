# Factory Method Pattern

## Overview

The Factory Method Pattern defines an interface for creating an object, but lets subclasses alter the type of objects that will be created. This pattern promotes loose coupling by eliminating the need to bind application-specific classes into the code. The code interacts solely with the interface or abstract class.

## When to Use

- When a class cannot anticipate the type of objects it needs to create.
- To delegate the responsibility of instantiating objects to subclasses.
- When you want to provide a library of objects and only expose their interfaces, hiding the actual implementation.

## Implementation in Java

Here’s a step-by-step guide to implementing the Factory Method Pattern in Java.

1. **Product Interface:** Define the interface of objects that the factory method creates.
2. **Concrete Product Classes:** Implement the product interface.
3. **Creator Class:** Declare the factory method, which returns an object of type Product.
4. **Concrete Creator Classes:** Override the factory method to return an instance of a concrete product.

## Example Scenario

Let's consider a scenario where we need to create different types of vehicles (e.g., Car, Bike). The type of vehicle to create depends on the client’s request.

### Step-by-Step Implementation

1. **Product Interface**

```java
public interface Vehicle {
    void drive();
}
```

2. **Concrete Product Classes**

```java
public class Car implements Vehicle {
    @Override
    public void drive() {
        System.out.println("Driving a car.");
    }
}

public class Bike implements Vehicle {
    @Override
    public void drive() {
        System.out.println("Riding a bike.");
    }
}
```

3. **Creator Class**

```java
public abstract class VehicleFactory {
    public abstract Vehicle createVehicle();

    public void deliverVehicle() {
        Vehicle vehicle = createVehicle();
        vehicle.drive();
    }
}
```

4. **Concrete Creator Classes**

```java
public class CarFactory extends VehicleFactory {
    @Override
    public Vehicle createVehicle() {
        return new Car();
    }
}

public class BikeFactory extends VehicleFactory {
    @Override
    public Vehicle createVehicle() {
        return new Bike();
    }
}
```

5. **Client Code**

```java
public class FactoryMethodPatternDemo {
    public static void main(String[] args) {
        VehicleFactory carFactory = new CarFactory();
        VehicleFactory bikeFactory = new BikeFactory();

        carFactory.deliverVehicle();  // Output: Driving a car.
        bikeFactory.deliverVehicle();  // Output: Riding a bike.
    }
}
```

## Key Points to Memorize

- **Factory Method**: Defines an interface for creating objects.
- **Product Interface**: Represents the objects created by the factory method.
- **Concrete Products**: Different implementations of the product interface.
- **Concrete Creators**: Override the factory method to create and return different product instances.

## Practical Example Scenario

Consider an application that can produce different types of documents (e.g., Word, PDF). Using the Factory Method pattern, you can have a document factory that creates different types of documents based on the user's selection, allowing for easy extensibility when new document types need to be added.

## Identifying When to Use the Factory Method Pattern

- **Need for flexibility in object creation**: When the exact type of object to create is not known until runtime.
- **Single Responsibility Principle**: To delegate the responsibility of instantiating objects to subclasses.

### Exercises to Reinforce Learning

1. **Implement Different Factories**: Create factories for different types of products, such as electronics (PhoneFactory, LaptopFactory) or animals (DogFactory, CatFactory).
2. **Refactor Existing Code**: Identify areas in your projects where object creation logic is tightly coupled and refactor using the Factory Method pattern.
3. **Extend the Example**: Add more vehicle types like Truck or Bus and corresponding factories to see how easily the Factory Method pattern handles new types.
