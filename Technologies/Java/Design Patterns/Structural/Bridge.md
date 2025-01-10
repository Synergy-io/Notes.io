# Bridge Pattern

## Overview

The Bridge Pattern is a structural design pattern that decouples an abstraction from its implementation so that the two can vary independently. It is used to separate the abstraction and its implementation into different class hierarchies.

## When to Use

- When you want to avoid a permanent binding between an abstraction and its implementation.
- When both the abstractions and their implementations should be extensible by subclassing.
- When changes in the implementation of an abstraction should have no impact on clients.
- When you want to share an implementation among multiple objects and hide the implementation details from the clients.

## Implementation in Java

Here’s a step-by-step guide to implementing the Bridge Pattern in Java.

1. **Abstraction**: Defines the interface for the client.
2. **Refined Abstraction**: Extends the interface defined by Abstraction.
3. **Implementor**: Defines the interface for implementation classes.
4. **Concrete Implementor**: Implements the Implementor interface.

## Example Scenario

Let's consider a scenario where we have different shapes (e.g., Circle, Square) that can be drawn with different colors. We want to decouple the shape from the color.

### Step-by-Step Implementation

1. **Implementor Interface**

```java
public interface Color {
    void applyColor();
}
```

2. **Concrete Implementors**

```java
public class RedColor implements Color {
    @Override
    public void applyColor() {
        System.out.println("Applying red color");
    }
}

public class BlueColor implements Color {
    @Override
    public void applyColor() {
        System.out.println("Applying blue color");
    }
}
```

3. **Abstraction**

```java
public abstract class Shape {
    protected Color color;

    public Shape(Color color) {
        this.color = color;
    }

    abstract void draw();
}
```

4. **Refined Abstractions**

```java
public class Circle extends Shape {
    public Circle(Color color) {
        super(color);
    }

    @Override
    void draw() {
        System.out.print("Drawing Circle in ");
        color.applyColor();
    }
}

public class Square extends Shape {
    public Square(Color color) {
        super(color);
    }

    @Override
    void draw() {
        System.out.print("Drawing Square in ");
        color.applyColor();
    }
}
```

5. **Client Code**

```java
public class BridgePatternDemo {
    public static void main(String[] args) {
        Shape redCircle = new Circle(new RedColor());
        Shape blueSquare = new Square(new BlueColor());

        redCircle.draw();  // Output: Drawing Circle in Applying red color
        blueSquare.draw();  // Output: Drawing Square in Applying blue color
    }
}
```

## Key Points to Memorize

- **Bridge Pattern**: Decouples an abstraction from its implementation so that the two can vary independently.
- **Abstraction and Implementor**: Defines separate class hierarchies for abstraction and implementation.
- **Client**: Uses the abstraction which is linked to an implementor.

## Practical Example Scenario

Consider an application where you have different types of documents (e.g., PDF, Word) that can be generated with different themes (e.g., Dark, Light). Using the Bridge Pattern, you can decouple the document type from the theme, allowing them to vary independently.

## Identifying When to Use the Bridge Pattern

- **Decoupling abstraction and implementation**: When you need to decouple an abstraction from its implementation so that both can vary independently.
- **Extensibility**: When both the abstractions and their implementations need to be extensible by subclassing.
- **Implementation hiding**: When you want to hide the implementation details from the client.

### Exercises to Reinforce Learning

1. **Extend the Example**: Add more shapes (e.g., Rectangle) and more colors (e.g., Green) to see how the pattern scales.
2. **Real-world Application**: Implement the Bridge Pattern in a real-world scenario, such as a notification system where notifications can be sent via different channels (e.g., Email, SMS) and can have different formats (e.g., Text, HTML).
3. **Complex Implementations**: Use the Bridge Pattern to separate complex interfaces, like different database drivers (e.g., MySQL, PostgreSQL) and different query builders.
