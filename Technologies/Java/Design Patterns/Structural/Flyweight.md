# Flyweight Pattern

## Overview

The Flyweight Pattern is a structural design pattern that allows programs to support vast quantities of objects by keeping their memory consumption low. It achieves this by sharing as much data as possible with similar objects, reducing the number of redundant objects and minimizing memory usage.

## When to Use

- When a large number of similar objects are used and memory usage is a concern.
- When most object properties can be shared across multiple objects, distinguishing only a few intrinsic properties.
- When you want to improve performance by reducing memory consumption.

## Implementation in Java

Here’s a step-by-step guide to implementing the Flyweight Pattern in Java.

1. **Flyweight Interface**: Defines the interface for flyweight objects.
2. **Concrete Flyweight**: Implements the flyweight interface and stores intrinsic state.
3. **Flyweight Factory**: Manages the flyweight objects and ensures shared instances.
4. **Client**: Uses the flyweight objects, differentiating them by extrinsic properties.

## Example Scenario

Let's consider a scenario where we have a text editor that needs to handle many characters. Each character can be represented as a flyweight object to minimize memory usage.

### Step-by-Step Implementation

1. **Flyweight Interface**

```java
public interface CharacterFlyweight {
    void display(int fontSize, String color);
}
```

2. **Concrete Flyweight**

```java
public class ConcreteCharacter implements CharacterFlyweight {
    private final char character;

    public ConcreteCharacter(char character) {
        this.character = character;
    }

    @Override
    public void display(int fontSize, String color) {
        System.out.println("Character: " + character + " | Font Size: " + fontSize + " | Color: " + color);
    }
}
```

3. **Flyweight Factory**

```java
import java.util.HashMap;
import java.util.Map;

public class CharacterFactory {
    private Map<Character, CharacterFlyweight> characters = new HashMap<>();

    public CharacterFlyweight getCharacter(char character) {
        CharacterFlyweight flyweight = characters.get(character);
        if (flyweight == null) {
            flyweight = new ConcreteCharacter(character);
            characters.put(character, flyweight);
        }
        return flyweight;
    }
}
```

4. **Client Code**

```java
public class FlyweightPatternDemo {
    public static void main(String[] args) {
        CharacterFactory factory = new CharacterFactory();

        CharacterFlyweight characterA1 = factory.getCharacter('A');
        CharacterFlyweight characterA2 = factory.getCharacter('A');
        CharacterFlyweight characterB = factory.getCharacter('B');

        characterA1.display(12, "Red");
        characterA2.display(14, "Blue");
        characterB.display(16, "Green");

        // Checking if the same instances are reused
        System.out.println("characterA1 and characterA2 are the same instance: " + (characterA1 == characterA2));
    }
}
```

## Key Points to Memorize

- **Flyweight Pattern**: Reduces memory usage by sharing as much data as possible with similar objects.
- **Intrinsic vs. Extrinsic State**: Intrinsic state is shared (invariant), while extrinsic state is unique to each instance.
- **Flyweight Factory**: Ensures that flyweight objects are shared and reused appropriately.

## Practical Example Scenario

Consider a game where numerous trees are displayed on a map. Each tree has common properties like type and color, which can be shared among all instances, but unique properties like position can be extrinsic.

## Identifying When to Use the Flyweight Pattern

- **Memory Constraints**: When memory consumption is a critical concern, and many similar objects are used.
- **Shared Properties**: When objects have significant shared data that can be extrinsic.

### Exercises to Reinforce Learning

1. **Extend the Example**: Add more properties to the character (e.g., font style) and update the flyweight and factory to handle these properties.
2. **Game Development**: Implement the Flyweight Pattern in a game scenario with multiple shared elements like trees, rocks, or enemies.
3. **Real-world Application**: Apply the Flyweight Pattern to a real-world application, such as rendering a large number of graphical elements in a UI library.
