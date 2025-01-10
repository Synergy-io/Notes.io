# Builder Pattern

## Overview

The Builder Pattern is a creational design pattern that allows you to construct complex objects step by step. Unlike other creational patterns, which often result in the creation of objects in a single step, the Builder Pattern focuses on constructing a complex object incrementally.

## When to Use

- When you need to construct a complex object with many optional parameters or configurations.
- When the construction process should be independent of the parts that make up the object.
- When you want to improve the readability and maintainability of the creation of complex objects.

## Implementation in Java

Here’s a step-by-step guide to implementing the Builder Pattern in Java.

1. **Product Class:** The complex object to be built.
2. **Builder Interface:** Specifies methods for creating the different parts of the product.
3. **Concrete Builder:** Implements the Builder interface and provides methods to assemble the parts of the product.
4. **Director:** Constructs the object using the Builder interface.
5. **Client:** Uses the Director and Builder to construct the object.

## Example Scenario

Let's consider a scenario where we need to construct a complex `House` object with optional features like a garage, swimming pool, and garden.

### Step-by-Step Implementation

1. **Product Class**

```java
public class House {
    private String foundation;
    private String structure;
    private String roof;
    private boolean hasGarage;
    private boolean hasSwimmingPool;
    private boolean hasGarden;

    // Getters and Setters
    public void setFoundation(String foundation) { this.foundation = foundation; }
    public void setStructure(String structure) { this.structure = structure; }
    public void setRoof(String roof) { this.roof = roof; }
    public void setHasGarage(boolean hasGarage) { this.hasGarage = hasGarage; }
    public void setHasSwimmingPool(boolean hasSwimmingPool) { this.hasSwimmingPool = hasSwimmingPool; }
    public void setHasGarden(boolean hasGarden) { this.hasGarden = hasGarden; }

    @Override
    public String toString() {
        return "House with foundation: " + foundation + ", structure: " + structure + ", roof: " + roof +
               ", garage: " + hasGarage + ", swimming pool: " + hasSwimmingPool + ", garden: " + hasGarden;
    }
}
```

2. **Builder Interface**

```java
public interface HouseBuilder {
    void buildFoundation();
    void buildStructure();
    void buildRoof();
    void buildGarage();
    void buildSwimmingPool();
    void buildGarden();
    House getHouse();
}
```

3. **Concrete Builder**

```java
public class ConcreteHouseBuilder implements HouseBuilder {
    private House house;

    public ConcreteHouseBuilder() {
        this.house = new House();
    }

    @Override
    public void buildFoundation() {
        house.setFoundation("Concrete foundation");
    }

    @Override
    public void buildStructure() {
        house.setStructure("Wooden structure");
    }

    @Override
    public void buildRoof() {
        house.setRoof("Metal roof");
    }

    @Override
    public void buildGarage() {
        house.setHasGarage(true);
    }

    @Override
    public void buildSwimmingPool() {
        house.setHasSwimmingPool(true);
    }

    @Override
    public void buildGarden() {
        house.setHasGarden(true);
    }

    @Override
    public House getHouse() {
        return this.house;
    }
}
```

4. **Director**

```java
public class HouseDirector {
    private HouseBuilder houseBuilder;

    public HouseDirector(HouseBuilder houseBuilder) {
        this.houseBuilder = houseBuilder;
    }

    public House constructHouse() {
        houseBuilder.buildFoundation();
        houseBuilder.buildStructure();
        houseBuilder.buildRoof();
        houseBuilder.buildGarage();
        houseBuilder.buildSwimmingPool();
        houseBuilder.buildGarden();
        return houseBuilder.getHouse();
    }
}
```

5. **Client Code**

```java
public class BuilderPatternDemo {
    public static void main(String[] args) {
        HouseBuilder builder = new ConcreteHouseBuilder();
        HouseDirector director = new HouseDirector(builder);

        House house = director.constructHouse();
        System.out.println(house);  // Output: House with foundation: Concrete foundation, structure: Wooden structure, roof: Metal roof, garage: true, swimming pool: true, garden: true
    }
}
```

## Key Points to Memorize

- **Builder Pattern**: Separates the construction of a complex object from its representation, allowing the same construction process to create different representations.
- **Builder Interface**: Defines the steps to build the product.
- **Concrete Builder**: Implements the steps defined in the builder interface.
- **Director**: Constructs the object using the builder interface.
- **Product Class**: The complex object that is being built.

## Practical Example Scenario

Consider an application where you need to create various types of meal combos (e.g., vegetarian, non-vegetarian) in a restaurant. Using the Builder Pattern, you can construct different meal combos by assembling various parts like the main course, drink, and dessert.

## Identifying When to Use the Builder Pattern

- **Complex objects with many parts**: When you need to create an object that requires numerous steps and various configurations.
- **Construction process isolation**: When the construction process should be independent of the parts that make up the object.
- **Improved readability and maintainability**: When you want to improve the readability and maintainability of creating complex objects.

### Exercises to Reinforce Learning

1. **Extend the Example**: Add new features to the `House` class and modify the builder and director accordingly.
2. **Different Builders**: Create different builders for various types of houses (e.g., StoneHouseBuilder, WoodenHouseBuilder) and use the same director to construct them.
3. **Real-world Application**: Implement the Builder Pattern in a real-world application scenario, such as building a car with various configurations.
