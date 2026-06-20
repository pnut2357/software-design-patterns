# Program to an Interface, not an Implementation

## Principle Definition

> **Program to an interface, not an implementation. Depend on abstractions, not on concrete classes.**

## Why Flexibility Matters

You can tell that a design is flexible enough if you can easily extend it without breaking any existing code. 

**Analogy:** A Cat that can eat *any food* is more flexible than one that can eat just *sausages*. You can still feed the first cat with sausages because they are a subset of "any food"; however, you can extend that cat's menu with any other food.

---

## The Problem: Tightly Coupled Classes

### What is Tight Coupling?

Tight coupling occurs when one class directly depends on concrete implementations of another class. This creates several problems:

### Why Tightly Coupled Classes Are Bad

1. **Reduced Flexibility**
   - Changes to one class force changes in dependent classes
   - Difficult to extend functionality without breaking existing code

2. **Poor Reusability**
   - Cannot reuse code with different implementations
   - Must override most of the dependent class instead of reusing it

3. **Difficult to Test**
   - Hard to test classes in isolation
   - Cannot easily substitute mock objects

4. **Violation of Open/Closed Principle**
   - Not open for extension
   - Requires modification of existing code to add new functionality

---

## The Solution: How to Improve Tightly Coupled Classes

### Step-by-Step Approach

When you want to make two classes collaborate more flexibly, follow these steps:

#### 1. **Determine Requirements**
   - Identify what exactly one object needs from the other
   - Which methods does it execute?

#### 2. **Create an Interface or Abstract Class**
   - Describe the required methods in a new interface or abstract class
   - This defines the contract for collaboration

#### 3. **Implement the Interface**
   - Make the dependency class implement this interface
   - The concrete class now fulfills the contract

#### 4. **Depend on the Interface**
   - Make the second class dependent on the interface rather than the concrete class
   - You can still work with objects of the original class, but the connection is now much more flexible

---

## Example: Company and Employees

### BEFORE: All Classes Are Tightly Coupled

```
class Company {
    getProfit() {
        // Directly depends on concrete classes
        Designer designer = new Designer();
        Programmer programmer = new Programmer();
        Tester tester = new Tester();
        
        // Tightly coupled to specific implementations
        designer.doWork();
        programmer.doWork();
        tester.doWork();
    }
}
```

**Problems:**
- Company class is tightly coupled to concrete classes (Designer, Programmer, Tester)
- Cannot easily add new employee types
- Cannot create different types of companies with different employees
- Must modify Company class to support new employee types

---

### BETTER: Using Polymorphism (But Still Coupled)

```
interface Employee {
    doWork();
}

class Designer implements Employee { ... }
class Programmer implements Employee { ... }
class Tester implements Employee { ... }

class Company {
    List<Employee> employees;
    
    getProfit() {
        // Polymorphism: treat all employees uniformly
        for (Employee employee : employees) {
            employee.doWork();
        }
    }
    
    // BUT: Still tightly coupled here!
    createEmployees() {
        employees.add(new Designer());
        employees.add(new Programmer());
        employees.add(new Tester());
    }
}
```

**Improvements:**
- Extracted common Employee interface
- Can treat different employee types uniformly through polymorphism

**Remaining Problems:**
- Company class still coupled to concrete employee classes in `createEmployees()`
- If we introduce new types of companies that work with other types of employees, we'll need to override most of the Company class instead of reusing that code

---

### AFTER: Fully Decoupled with Abstract Method (Factory Method Pattern)

```
interface Employee {
    doWork();
}

abstract class Company {
    List<Employee> employees;
    
    getProfit() {
        // Independent of concrete employee classes
        for (Employee employee : getEmployees()) {
            employee.doWork();
        }
    }
    
    // Abstract method: let subclasses decide which employees to create
    abstract List<Employee> getEmployees();
}

class GameDevCompany extends Company {
    List<Employee> getEmployees() {
        // Creates only employees needed for game development
        return [new Designer(), new Programmer()];
    }
}

class OutsourcingCompany extends Company {
    List<Employee> getEmployees() {
        // Creates only employees needed for outsourcing
        return [new Programmer(), new Tester()];
    }
}
```

**Benefits of This Approach:**

1. **Company class is now independent** from concrete employee classes
2. **Reusability:** Can extend Company class and introduce new types of companies and employees while still reusing base company code
3. **Open/Closed Principle:** Extending the base company class doesn't break any existing code that already relies on it
4. **Flexibility:** Each company subclass creates only the employees it needs

---

## Key Takeaways

### When to Apply This Principle

Apply "Program to an Interface" when:
- You anticipate needing multiple implementations
- The code might be a good extension point for extra functionality
- Other developers might want to extend your code
- You need to improve testability

### Trade-offs

**Costs:**
- Code becomes more complicated initially
- More classes and interfaces to manage
- May not feel immediate benefit

**Benefits:**
- Long-term flexibility and maintainability
- Easier to extend without breaking existing code
- Better code reusability
- Improved testability
- Follows Open/Closed Principle

---

## Summary

| Aspect | Tightly Coupled | Loosely Coupled (Interface-based) |
|--------|----------------|-----------------------------------|
| **Dependency** | Depends on concrete classes | Depends on abstractions |
| **Flexibility** | Low - hard to change | High - easy to extend |
| **Reusability** | Poor | Excellent |
| **Testing** | Difficult | Easy with mocks |
| **Maintenance** | Changes ripple through code | Changes isolated |
| **Extension** | Requires modification | Add new implementations |

**Remember:** This pattern demonstrates the Factory Method design pattern in action, where object creation is delegated to subclasses while the base class remains independent of concrete types.
