# Composite Pattern

## Overview

The Composite Pattern is a structural design pattern that allows you to compose objects into tree structures to represent part-whole hierarchies. It lets clients treat individual objects and compositions of objects uniformly.

## When to Use

- When you need to represent part-whole hierarchies of objects.
- When you want clients to be able to ignore the difference between compositions of objects and individual objects.
- When you want to simplify client code that deals with complex tree structures.

## Implementation in Java

Here’s a step-by-step guide to implementing the Composite Pattern in Java.

1. **Component**: Declares the interface for objects in the composition and implements default behavior for common interface methods.
2. **Leaf**: Represents leaf objects in the composition. A leaf has no children.
3. **Composite**: Represents a node that can have children. Implements methods to manage children.

## Example Scenario

Let's consider a scenario where we have an organization with employees. The organization can be composed of individual employees (leaf) and departments (composite) that contain employees and other departments.

### Step-by-Step Implementation

1. **Component Interface**

```java
import java.util.List;

public interface Employee {
    void showEmployeeDetails();
}
```

2. **Leaf**

```java
public class Developer implements Employee {
    private String name;
    private long empId;
    private String position;

    public Developer(long empId, String name, String position) {
        this.empId = empId;
        this.name = name;
        this.position = position;
    }

    @Override
    public void showEmployeeDetails() {
        System.out.println(empId + " " + name + " " + position);
    }
}

public class Manager implements Employee {
    private String name;
    private long empId;
    private String position;

    public Manager(long empId, String name, String position) {
        this.empId = empId;
        this.name = name;
        this.position = position;
    }

    @Override
    public void showEmployeeDetails() {
        System.out.println(empId + " " + name + " " + position);
    }
}
```

3. **Composite**

```java
import java.util.ArrayList;
import java.util.List;

public class CompanyDirectory implements Employee {
    private List<Employee> employeeList = new ArrayList<Employee>();

    @Override
    public void showEmployeeDetails() {
        for (Employee emp : employeeList) {
            emp.showEmployeeDetails();
        }
    }

    public void addEmployee(Employee emp) {
        employeeList.add(emp);
    }

    public void removeEmployee(Employee emp) {
        employeeList.remove(emp);
    }
}
```

4. **Client Code**

```java
public class CompositePatternDemo {
    public static void main(String[] args) {
        Employee dev1 = new Developer(100, "John Doe", "Pro Developer");
        Employee dev2 = new Developer(101, "Jane Doe", "Developer");

        Employee manager1 = new Manager(200, "Alice Smith", "Manager");
        
        CompanyDirectory engDirectory = new CompanyDirectory();
        engDirectory.addEmployee(dev1);
        engDirectory.addEmployee(dev2);
        
        CompanyDirectory managerDirectory = new CompanyDirectory();
        managerDirectory.addEmployee(manager1);
        
        CompanyDirectory companyDirectory = new CompanyDirectory();
        companyDirectory.addEmployee(engDirectory);
        companyDirectory.addEmployee(managerDirectory);
        
        companyDirectory.showEmployeeDetails();
        // Output:
        // 100 John Doe Pro Developer
        // 101 Jane Doe Developer
        // 200 Alice Smith Manager
    }
}
```

## Key Points to Memorize

- **Composite Pattern**: Composes objects into tree structures to represent part-whole hierarchies.
- **Component**: Declares the interface for objects in the composition.
- **Leaf**: Represents leaf objects in the composition that do not have children.
- **Composite**: Represents nodes that can have children and implements methods to manage children.

## Practical Example Scenario

Consider a graphical application where you have shapes (e.g., circles, squares) and groups of shapes. Using the Composite Pattern, you can treat individual shapes and groups of shapes uniformly, making it easier to work with complex graphical objects.

## Identifying When to Use the Composite Pattern

- **Hierarchical tree structures**: When you need to work with tree structures representing part-whole hierarchies.
- **Uniform treatment**: When you want to treat individual objects and compositions of objects uniformly.

### Exercises to Reinforce Learning

1. **Extend the Example**: Add more types of employees or different roles and extend the composite structure.
2. **Real-world Application**: Implement the Composite Pattern in a real-world scenario, such as a file system where files and directories are treated uniformly.
3. **Complex Structures**: Use the Composite Pattern to manage complex hierarchical data structures in a project.
