# Facade Pattern

## Overview

The Facade Pattern is a structural design pattern that provides a simplified interface to a complex subsystem. It aims to make the subsystem easier to use by offering a single point of access to its functionality.

## When to Use

- When you want to provide a simple interface to a complex subsystem.
- When there are many dependencies between clients and the implementation classes of an abstraction.
- When you want to layer your subsystems to better organize them.

## Implementation in Java

Here’s a step-by-step guide to implementing the Facade Pattern in Java.

1. **Subsystem Classes**: Implement the classes that constitute the complex subsystem.
2. **Facade Class**: Create a class that provides a simple interface to the subsystem.
3. **Client**: Use the facade to interact with the subsystem.

## Example Scenario

Let's consider a scenario where we have a complex home theater system with multiple components such as DVD player, projector, sound system, and lights. We want to provide a simplified interface to operate this system.

### Step-by-Step Implementation

1. **Subsystem Classes**

```java
public class DVDPlayer {
    public void on() {
        System.out.println("DVD Player is ON");
    }

    public void play(String movie) {
        System.out.println("Playing movie: " + movie);
    }

    public void off() {
        System.out.println("DVD Player is OFF");
    }
}

public class Projector {
    public void on() {
        System.out.println("Projector is ON");
    }

    public void off() {
        System.out.println("Projector is OFF");
    }

    public void setInput(String input) {
        System.out.println("Projector input set to: " + input);
    }
}

public class SoundSystem {
    public void on() {
        System.out.println("Sound System is ON");
    }

    public void setVolume(int level) {
        System.out.println("Setting volume to: " + level);
    }

    public void off() {
        System.out.println("Sound System is OFF");
    }
}

public class Lights {
    public void dim(int level) {
        System.out.println("Dimming lights to: " + level + "%");
    }

    public void on() {
        System.out.println("Lights are ON");
    }
}
```

2. **Facade Class**

```java
public class HomeTheaterFacade {
    private DVDPlayer dvdPlayer;
    private Projector projector;
    private SoundSystem soundSystem;
    private Lights lights;

    public HomeTheaterFacade(DVDPlayer dvdPlayer, Projector projector, SoundSystem soundSystem, Lights lights) {
        this.dvdPlayer = dvdPlayer;
        this.projector = projector;
        this.soundSystem = soundSystem;
        this.lights = lights;
    }

    public void watchMovie(String movie) {
        System.out.println("Get ready to watch a movie...");
        lights.dim(10);
        projector.on();
        projector.setInput("DVD");
        soundSystem.on();
        soundSystem.setVolume(5);
        dvdPlayer.on();
        dvdPlayer.play(movie);
    }

    public void endMovie() {
        System.out.println("Shutting down movie theater...");
        lights.on();
        projector.off();
        soundSystem.off();
        dvdPlayer.off();
    }
}
```

3. **Client Code**

```java
public class FacadePatternDemo {
    public static void main(String[] args) {
        DVDPlayer dvdPlayer = new DVDPlayer();
        Projector projector = new Projector();
        SoundSystem soundSystem = new SoundSystem();
        Lights lights = new Lights();

        HomeTheaterFacade homeTheater = new HomeTheaterFacade(dvdPlayer, projector, soundSystem, lights);

        homeTheater.watchMovie("Inception");
        // Output:
        // Get ready to watch a movie...
        // Dimming lights to: 10%
        // Projector is ON
        // Projector input set to: DVD
        // Sound System is ON
        // Setting volume to: 5
        // DVD Player is ON
        // Playing movie: Inception

        homeTheater.endMovie();
        // Output:
        // Shutting down movie theater...
        // Lights are ON
        // Projector is OFF
        // Sound System is OFF
        // DVD Player is OFF
    }
}
```

## Key Points to Memorize

- **Facade Pattern**: Provides a simplified interface to a complex subsystem.
- **Subsystem Classes**: The classes that implement the subsystem's functionality.
- **Facade**: A single class that provides a simple interface to the complex subsystem.

## Practical Example Scenario

Consider a banking system with multiple services like loan processing, account management, and customer support. Using the Facade Pattern, you can create a unified interface to interact with these services, simplifying the interaction for clients.

## Identifying When to Use the Facade Pattern

- **Simplified interfaces**: When you need to simplify the interface to a complex subsystem.
- **Decoupling clients**: When you want to decouple clients from the subsystem, reducing dependencies.
- **Organizing subsystems**: When you want to layer your subsystems and provide a simple interface for each layer.

### Exercises to Reinforce Learning

1. **Extend the Example**: Add more functionalities to the home theater system, like a popcorn maker or ambient lighting, and integrate them into the facade.
2. **Banking System Simulation**: Implement a banking system with various services and provide a facade to simplify interactions.
3. **Real-world Application**: Apply the Facade Pattern in a real-world scenario, such as a web application with multiple APIs that need to be accessed through a single interface.
