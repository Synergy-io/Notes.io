# Decorator Pattern

## Overview

The Decorator Pattern is a structural design pattern that allows behavior to be added to individual objects, either statically or dynamically, without affecting the behavior of other objects from the same class. This pattern provides a flexible alternative to subclassing for extending functionality.

## When to Use

- When you need to add responsibilities to individual objects dynamically and transparently, without affecting other objects.
- When extending a class with many possible variations of functionality and avoiding an explosion of subclasses.
- When you want to add functionality to an object without altering its structure.

## Implementation in Java

Here’s a step-by-step guide to implementing the Decorator Pattern in Java.

### Example Scenario

Let's consider a scenario where we have a simple text editor and we want to add various functionalities like spell-checking and formatting dynamically.

### Step-by-Step Implementation

1. **Component Interface**

```java
public interface Text {
    String getContent();
}
```

2. **Concrete Component**

```java
public class PlainText implements Text {
    private String text;

    public PlainText(String text) {
        this.text = text;
    }

    @Override
    public String getContent() {
        return text;
    }
}
```

3. **Decorator Abstract Class**

```java
public abstract class TextDecorator implements Text {
    protected Text decoratedText;

    public TextDecorator(Text decoratedText) {
        this.decoratedText = decoratedText;
    }

    @Override
    public String getContent() {
        return decoratedText.getContent();
    }
}
```

4. **Concrete Decorators**

```java
public class BoldTextDecorator extends TextDecorator {
    public BoldTextDecorator(Text decoratedText) {
        super(decoratedText);
    }

    @Override
    public String getContent() {
        return "<b>" + super.getContent() + "</b>";
    }
}

public class ItalicTextDecorator extends TextDecorator {
    public ItalicTextDecorator(Text decoratedText) {
        super(decoratedText);
    }

    @Override
    public String getContent() {
        return "<i>" + super.getContent() + "</i>";
    }
}

public class SpellCheckDecorator extends TextDecorator {
    public SpellCheckDecorator(Text decoratedText) {
        super(decoratedText);
    }

    @Override
    public String getContent() {
        // Here you could add spell checking functionality
        return super.getContent().replaceAll("teh", "the"); // Simplified example
    }
}
```

5. **Client Code**

```java
public class DecoratorPatternDemo {
    public static void main(String[] args) {
        Text plainText = new PlainText("This is teh plain text.");

        Text boldText = new BoldTextDecorator(plainText);
        Text italicText = new ItalicTextDecorator(plainText);
        Text spellCheckedText = new SpellCheckDecorator(plainText);
        Text boldItalicText = new BoldTextDecorator(new ItalicTextDecorator(plainText));

        System.out.println("Plain Text: " + plainText.getContent());
        System.out.println("Bold Text: " + boldText.getContent());
        System.out.println("Italic Text: " + italicText.getContent());
        System.out.println("Spell-Checked Text: " + spellCheckedText.getContent());
        System.out.println("Bold and Italic Text: " + boldItalicText.getContent());
    }
}
```

## Key Points to Memorize

- **Decorator Pattern**: Adds behavior to objects dynamically without altering their structure.
- **Component Interface**: Defines the interface for objects that can have responsibilities added to them.
- **Concrete Component**: The original object that can be decorated.
- **Decorator**: Abstract class that implements the component interface and contains a reference to a component object.
- **Concrete Decorators**: Extend the decorator class to add functionalities to the component.

## Practical Example Scenario

Consider a coffee shop where you have a basic coffee and you want to add various condiments (e.g., milk, sugar, cream) dynamically. Using the Decorator Pattern, you can create a basic coffee object and then wrap it with various decorators to add condiments.

## Identifying When to Use the Decorator Pattern

- **Dynamic behavior addition**: When you need to add responsibilities to objects dynamically without affecting other objects.
- **Avoid subclass explosion**: When extending functionality through subclassing would lead to an explosion of subclasses to support every combination of features.
- **Runtime extension**: When you need the ability to extend behavior at runtime.

### Exercises to Reinforce Learning

1. **Extend the Example**: Add more decorators like `UnderlineTextDecorator` or `UpperCaseDecorator`.
2. **Coffee Shop Simulation**: Implement a coffee shop ordering system using the Decorator Pattern where you can add condiments to the coffee.
3. **Real-world Application**: Apply the Decorator Pattern in a real-world scenario, such as enhancing the functionalities of a graphical user interface component.
