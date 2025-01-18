# Adopting SOLID Design Principles  
**By: Stephen Clark (Various original sources)**

Software design can be challenging, but following the SOLID principles greatly enhances code maintainability, readability, and flexibility. SOLID is a mnemonic for five fundamental design principles that help you build robust, scalable, and loosely coupled systems. In this guide, we explain each principle, its benefits, and offer practical Swift code examples.

---

## The SOLID Principles

### 1. Single Responsibility Principle (SRP)
A class should have **only one responsibility**—that is, only one reason to change. In practice, this means that a class should encapsulate a single part of the functionality provided by the software, avoiding mixing domain logic with ancillary concerns like persistence or UI.

### 2. Open/Closed Principle (OCP)
Software entities such as classes, modules, and functions should be **open for extension but closed for modification**. This principle encourages you to design modules that can be extended to change behavior without altering the existing source code, thereby reducing the risk of introducing bugs.

### 3. Liskov Substitution Principle (LSP)
Objects in a program should be **replaceable with instances of their subtypes** without altering the correctness of the program. In essence, if a class `S` is a subtype of class `T`, then objects of type `T` may be replaced by objects of type `S` without affecting the functionality or behavior of a program.

### 4. Interface Segregation Principle (ISP)
Rather than having one large, general-purpose interface, it is better to have several smaller, client-specific interfaces. This prevents clients from depending on methods they do not use, leading to a more modular and maintainable codebase.

### 5. Dependency Inversion Principle (DIP)
High-level modules should not depend on low-level modules; both should depend on **abstractions**. By depending on protocols or abstract classes rather than concrete implementations, you allow your system to be more flexible and resilient to changes in low-level details.

---

## Swift Code Examples

### Single Responsibility Principle

In this example, the `Diary` class encapsulates journal functionality, while persistence is handled by a separate `Persistence` class. This separation ensures that changes in persistence logic do not affect how the journal operates.

```swift
class Diary: CustomStringConvertible {
    var entries = [String]()
    var count = 0

    // Only responsible for managing journal entries
    func addEntry(_ text: String) -> Int {
        count += 1
        entries.append("Entry \(count): \"\(text)\"")
        return count - 1
    }

    func removeEntry(_ index: Int) {
        entries.remove(at: index)
    }

    var description: String {
        return entries.joined(separator: "\n")
    }
    
    // The following persistence methods do NOT belong inside Diary.
    func load(_ filename: String) {}
    func load(_ url: URL) {}
}

// Separate class for persistence; handles saving without polluting the Diary class.
class Persistence {
    func saveToFile(_ journal: Diary, _ filename: String, _ overwrite: Bool = false) {
        print("Saving: \(journal) to file: \(filename) with overwrite: \(overwrite)")
    }
}

func main() {
    let myJournal = Diary()
    let firstEntry = myJournal.addEntry("Today was AWESOME!")
    let bikeEntry = myJournal.addEntry("I went for a bike ride.")
    
    print(myJournal)
    
    print("Removing the bike entry")
    myJournal.removeEntry(bikeEntry)
    
    print(myJournal)
    
    let persistence = Persistence()
    let filename = "/mnt/c/something"
    persistence.saveToFile(myJournal, filename, false)
}

main()
```

### Open/Closed Principle

In this example, we define a protocol `Shape` and provide concrete implementations like `Circle` and `Square`. The function `calculateTotalArea` works on an array of shapes and does not need to be modified when adding new shapes.

```swift
// Protocol defining the interface for shapes
protocol Shape {
    func area() -> Double
}

// Concrete implementation of a circle
class Circle: Shape {
    let radius: Double

    init(radius: Double) {
        self.radius = radius
    }

    func area() -> Double {
        return Double.pi * radius * radius
    }
}

// Concrete implementation of a square
class Square: Shape {
    let side: Double

    init(side: Double) {
        self.side = side
    }

    func area() -> Double {
        return side * side
    }
}

// Function to calculate the total area of all shapes without changing its code
func calculateTotalArea(_ shapes: [Shape]) -> Double {
    shapes.reduce(0.0) { $0 + $1.area() }
}

// Usage example
let shapes: [Shape] = [Circle(radius: 5.0), Square(side: 4.0)]
let totalArea = calculateTotalArea(shapes)
print("Total area: \(totalArea)")
```

This example demonstrates how new shapes can be added without modifying existing code, ensuring the software remains open for extension but closed for modification.

### Liskov Substitution Principle

Here, we illustrate the Liskov Substitution Principle with a simple `Bird` protocol. Even though an `Ostrich` is a bird that can’t fly, the function works correctly because each subtype implements the protocol. (Note: In real-world scenarios, you might split birds into flying and non-flying categories to avoid design issues.)

```swift
// Protocol defining the behavior of a bird
protocol Bird {
    func fly()
}

// Sparrow can fly
class Sparrow: Bird {
    func fly() {
        print("Sparrow flying")
    }
}

// Ostrich, although a bird, does not fly
class Ostrich: Bird {
    func fly() {
        // Ostriches cannot fly
    }
}

// Function that accepts any Bird and makes it fly
func makeBirdFly(_ bird: Bird) {
    bird.fly()
}

// Usage example
let sparrow = Sparrow()
let ostrich = Ostrich()

makeBirdFly(sparrow) // Outputs: Sparrow flying
makeBirdFly(ostrich) // Outputs nothing because ostrich cannot fly
```

This example shows that subtypes (like `Ostrich`) can substitute their base type without breaking program correctness, aligning with Liskov's principle.

### Interface Segregation Principle

Instead of one bloated interface, we define multiple small, specific protocols: `Worker`, `Eater`, and `Sleeper`. Clients only implement what they need.

```swift
// Protocol for work-related behavior
protocol Worker {
    func work()
}

// Protocol for eating behavior
protocol Eater {
    func eat()
}

// Protocol for sleeping behavior
protocol Sleeper {
    func sleep()
}

// Robot only needs to work, so it conforms solely to Worker
class RobotWorker: Worker {
    func work() {
        print("Robot working")
    }
}

// Human may need to work and eat
class HumanWorker: Worker, Eater {
    func work() {
        print("Human working")
    }

    func eat() {
        print("Human eating")
    }
}

// Another human class that works and sleeps
class HumanSleeper: Worker, Sleeper {
    func work() {
        print("Human working")
    }

    func sleep() {
        print("Human sleeping")
    }
}

// Usage example
let robot = RobotWorker()
let humanWorker = HumanWorker()

robot.work()   // Outputs: Robot working
humanWorker.work() // Outputs: Human working
humanWorker.eat()  // Outputs: Human eating
```

This design avoids forcing classes to implement methods they don't need, leading to cleaner and more maintainable code.

### Dependency Inversion Principle

The Dependency Inversion Principle is all about inverting dependencies: high-level modules depend on abstractions (protocols) rather than on low-level module details. In the following example, a `CoffeeMachine` class depends on a `Coffee` protocol, allowing different types of coffee (e.g., `Arabica` or `Robusta`) to be injected as needed without modifying the machine.

```swift
// The 'Coffee' protocol represents the abstraction for coffee preparation
protocol Coffee {
    func prepare() -> String
}

// Concrete implementation for Arabica coffee
class Arabica: Coffee {
    func prepare() -> String {
        let growing = "Growing Arabica: Typically grown in high altitudes with a mild climate."
        let harvesting = "Harvesting Arabica: Hand-picking cherries when perfectly ripe."
        let processing = "Processing Arabica: Cherries are either sun-dried (dry method) or pulped and dried (wet method)."
        let roasting = "Roasting Arabica: Beans roasted at around 200°C until dark brown."
        let grinding = "Grinding Arabica: Roasted beans ground to a consistency similar to sea salt."
        let shipping = "Shipping Arabica: Packaged and shipped globally."
        
        return "\(growing)\n\(harvesting)\n\(processing)\n\(roasting)\n\(grinding)\n\(shipping)"
    }
}

// Concrete implementation for Robusta coffee
class Robusta: Coffee {
    func prepare() -> String {
        return "Preparing Robusta coffee..."
    }
}

// High-level module that depends on the Coffee abstraction
class CoffeeMachine {
    var coffee: Coffee
    
    init(coffee: Coffee) {
        self.coffee = coffee
    }
    
    func getCoffee() {
        print(coffee.prepare())
    }
}

// Usage example:
let arabica = Arabica()
let robusta = Robusta()

let coffeeMachine = CoffeeMachine(coffee: arabica)
coffeeMachine.getCoffee()

// Switching coffee type without modifying CoffeeMachine's code
coffeeMachine.coffee = robusta
coffeeMachine.getCoffee()
```

The above code illustrates how high-level modules (the coffee machine) rely on the `Coffee` protocol—an abstraction—so that the underlying coffee implementation can change dynamically, improving flexibility and maintainability.

---

## Conclusion

By adopting SOLID design principles, you ensure that your codebase remains:

- **Modular** and easier to understand (SRP).
- **Extendable** without modifying existing code (OCP).
- **Consistent** and reliable when substituting implementations (LSP).
- **Tailored** to specific client needs without unnecessary dependencies (ISP).
- **Flexible** by decoupling high-level and low-level modules (DIP).

These principles not only improve the quality of your code but also make it easier to test, maintain, and extend over time. Happy coding!
