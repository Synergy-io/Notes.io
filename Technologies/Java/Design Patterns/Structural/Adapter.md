# Adapter Pattern

## Overview

The Adapter Pattern is a structural design pattern that allows objects with incompatible interfaces to work together. It acts as a bridge between two incompatible interfaces by wrapping an object to expose a compatible interface.

## When to Use

- When you want to use an existing class, but its interface does not match the one you need.
- When you want to create a reusable class that cooperates with unrelated or unforeseen classes, those that do not necessarily have compatible interfaces.
- When you need to use several existing subclasses, but it’s impractical to adapt their interface by subclassing every one of them.

## Implementation in Java

Here’s a step-by-step guide to implementing the Adapter Pattern in Java.

1. **Target Interface**: This is the interface that the client expects.
2. **Adaptee Class**: This is the existing class with an incompatible interface.
3. **Adapter Class**: This class implements the target interface and holds an instance of the adaptee class. It translates the target interface methods into the adaptee class methods.

## Example Scenario

An existing implementation for applying a `Vivid filter` to an image exists. Now we need to use 3rd party filter called `Caramel` for an image. But it is not implemented by the `Filter` interface.

### Step-by-Step Implementation

1. **Target Interface**

```java
public interface Filter {
    void apply(Image image);
}
```

- **Existing Implementation**

```java
public class Image {
}

public class VividFilter implements Filter {

    @Override
    public void apply(Image image) {
        System.out.println("Applying Vivid Filter");
    }

}
```

2. **Adaptee Class**

```java
public class Caramel {

    public void init(){
    }

    public void render(Image image){
        System.out.println("Applying Caramel filter");
    }
}
```

3. 1. **Adapter Class**  (using `Class adapter`)

```java
public class CaramelAdapter extends Caramel implements Filter{

    @Override
    public void apply(Image image) {
        init();
        render(image);
    }
}
```

3. 2. **Adapter Class**  (using `Object adapter`)

```java
public class CramelFilter implements Filter{
    private Caramel caramel;

    public CramelFilter(Caramel caramel) {
        this.caramel = caramel;
    }

    @Override
    public void apply(Image image) {
        caramel.init(); //requirement from the 3rd party library
        caramel.render(image);
    }
}
```

4. **Client Class**

```java
public class ImageView {
    private Image image;

    public ImageView(Image image) {
        this.image = image;
    }

    //the interface that the client expects (Filter)
    public void apply(Filter filter){
        filter.apply(image);
    }
    
}

public class Main {
    public static void main(String[] args) {
        
        var imageView = new ImageView(new Image());
        imageView.apply(new VividFilter());

        imageView.apply(new CramelFilter(new Caramel())); //object adpter

        imageView.apply(new CaramelAdapter()); //class adapter
        
    }
}
// class adapter and object adapter do the same thing, but different implementation
```

## Key Points to Memorize

- **Adapter Pattern**: Converts the interface of a class into another interface that a client expects.
- **Target Interface**: The interface that the client expects.
- **Adaptee Class**: The existing class that needs to be adapted.
- **Adapter Class**: Implements the target interface and translates requests to the adaptee class.

## Practical Example Scenario

Consider an application where you need to integrate multiple third-party services with different interfaces. Using the Adapter Pattern, you can create adapters for each service to provide a unified interface for your application.

## Identifying When to Use the Adapter Pattern

- **Incompatible interfaces**: When you have classes with incompatible interfaces that need to work together.
- **Legacy code integration**: When you need to integrate new functionality with existing legacy code.
- **Reusable class**: When you want to create a reusable class that works with unrelated classes with different interfaces.

### Exercises to Reinforce Learning

1. **Real-world Application**: Apply the Adapter Pattern to a real-world scenario, such as integrating different payment gateways with your application.
2. **Bi-directional Adapter**: Implement a bi-directional adapter that can work in both directions between two different interfaces.


## Another example

The PIR sensor can provide an accurate measured `voltage`-to-`distance` conversion for distance range 2.5cm to 40cm.

### Step-by-Step Implementation

1. **Target Interface**

```java
interface DistanceSensor {
    public double getDistance();
}
```

2. **Adaptee Class**

```java
class PIRGP2d120 { 
    private double vout;

    public PIRGP2d120() { 
        vout = 0.0; 
    }
    public double getVoltage() {
        // code for reading Vo pin of GP2d120 module
        return vout;
    }
}
```

3. **Adapter Class**

```java
class PIRDistanceSensor implements DistanceSensor { 
    private PIRGP2d120 sensor;

    PIRDistanceSensor() { 
        sensor = new PIRGP2d120(); 
    }

    public double getDistance() {
        double v = sensor.getVoltage();
        double d=0.0;
        //code to convert voltage to distance using graph in datasheet
        d=12.34; // example
        return d;
    }
}
```

4. **Client Class**

```java
public class RobotCar {
    public static void main(String [] args) {
        DistanceSensor ds = new PIRDistanceSensor();
        System.out.println("Obstacle at "+ds.getDistance()+" cm");
    }   
}
```
