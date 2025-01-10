# Prototype Pattern

## Overview

The Prototype Pattern is a creational design pattern that allows you to copy existing objects without making your code dependent on their classes. This pattern involves implementing a prototype interface that tells how to create a copy of the current object. The prototype pattern is particularly useful when the cost of creating a new instance of a class is expensive.

## When to Use

- When creating an instance of a class is costly or time-consuming.
- When you want to avoid subclassing and create objects based on a dynamically determined type.
- When you need to keep a few instances of the same class and have the ability to clone them.

## Implementation in Java

Here’s a step-by-step guide to implementing the Prototype Pattern in Java.

1. **Prototype Interface:** Declares the clone method.
2. **Concrete Prototype Classes:** Implement the clone method to return a copy of the object.
3. **Client:** Uses the clone method to create a new object.

## Example Scenario

Let's consider a scenario where we need to create different types of shapes (e.g., circles, rectangles) and clone them to create new instances.

### Step-by-Step Implementation

1. **Prototype Interface**

```java
public interface Shape extends Cloneable {
    Shape clone();
    void draw();
}
```

2. **Concrete Prototype Classes**

```java
public class Circle implements Shape {
    private int radius;

    public Circle(int radius) {
        this.radius = radius;
    }

    @Override
    public Circle clone() {
        return new Circle(this.radius);
    }

    @Override
    public void draw() {
        System.out.println("Drawing a circle with radius: " + radius);
    }
}

public class Rectangle implements Shape {
    private int width;
    private int height;

    public Rectangle(int width, int height) {
        this.width = width;
        this.height = height;
    }

    @Override
    public Rectangle clone() {
        return new Rectangle(this.width, this.height);
    }

    @Override
    public void draw() {
        System.out.println("Drawing a rectangle with width: " + width + " and height: " + height);
    }
}
```

3. **Client Code**

```java
public class PrototypePatternDemo {
    public static void main(String[] args) {
        Shape originalCircle = new Circle(10);
        Shape clonedCircle = originalCircle.clone();
        
        Shape originalRectangle = new Rectangle(20, 30);
        Shape clonedRectangle = originalRectangle.clone();
        
        originalCircle.draw();  // Output: Drawing a circle with radius: 10
        clonedCircle.draw();    // Output: Drawing a circle with radius: 10

        originalRectangle.draw();  // Output: Drawing a rectangle with width: 20 and height: 30
        clonedRectangle.draw();    // Output: Drawing a rectangle with width: 20 and height: 30
    }
}
```

## Key Points to Memorize

- **Prototype Pattern**: Allows cloning of objects without being tied to their specific classes.
- **Prototype Interface**: Declares the `clone` method.
- **Concrete Prototypes**: Implement the `clone` method to return a copy of the object.
- **Shallow vs. Deep Copy**: Ensure proper cloning if objects have complex structures or references.

## Practical Example Scenario

Consider an application where you need to create various game characters (e.g., heroes, monsters) with different attributes. Instead of creating each character from scratch, you can create a prototype for each character type and clone it to create new instances.

## Identifying When to Use the Prototype Pattern

- **Costly object creation**: When creating new instances is costly or time-consuming.
- **Dynamic object creation**: When the exact type of objects to create is determined at runtime.
- **Avoiding subclasses**: When you want to create new objects without relying on subclasses.

### Exercises to Reinforce Learning

1. **Extend the Example**: Add more shape types (e.g., Triangle, Square) and implement their cloning methods.
2. **Complex Objects**: Implement the Prototype Pattern for objects with complex structures, ensuring proper handling of deep cloning.
3. **Real-world Application**: Apply the Prototype Pattern in a real-world scenario, such as cloning configurations or settings in an application.
