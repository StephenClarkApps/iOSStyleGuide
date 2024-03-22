Adopting SOLID Design Principles
====
***By: Stephen Clark (Various original sources)***

## The Principles

**Single Responsibility Principle**:
a class should have only a single responsibility (i.e. changes to only one part of the software's specification should be able to affect the specification of the class).

**Open/Closed Principle**:
"software entities … should be open for extension, but closed for modification."

**Liskov Substitution Principle**:
"objects in a program should be replaceable with instances of their subtypes without altering the correctness of that program."

**Interface Segregation Principle**:
"many client-specific interfaces are better than one general-purpose interface."

**Dependency Inversion Principle**:
one should "depend upon abstractions, [not] concretions."

****
## Swift Code Examples

### Single Responsibility Principle

```swift
class Diary: CustomStringConvertible {
    var entries = [String]()
    var count = 0

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
    // THE BELOW DOES NOT BELONG INSIDE THE JOURNAL
    func load(_ filename: String) {}
    func load(_ url: URL) {}
}

// THE BELOW CORRECTLY SEPERATES OUT PERSISTANCE FROM THE MODEL OBJECT
// One impovement could be to make this save a generic type that conforms to a
// protocol like Codable
class Persistence {
    func saveToFile(_ journal: Diary, _ filename: String, _ overwrite: Bool = false) {
        print("We are saving: \(journal) To file: \(filename) with Overwrite: \(overwrite)")
    }
}

func main() {
    let myJournal = Diary()
    let _ = myJournal.addEntry("Today was AWESOME!")
    let bike = myJournal.addEntry("I went for a bike ride.")

    print(myJournal)

    print("remove Entry \"bike\"")
    myJournal.removeEntry(bike)

    print(myJournal)

    let p = Persistence()
    let filename = "/mnt/c/something"
    p.saveToFile(myJournal, filename, false)
}

main()

```
### Open/Closed Principle

```swift
// Open/Closed Principle Example

// Protocol defining the shape
protocol Shape {
    func area() -> Double
}

// Concrete implementation of a Circle
class Circle: Shape {
    let radius: Double

    init(radius: Double) {
        self.radius = radius
    }

    func area() -> Double {
        return Double.pi * radius * radius
    }
}

// Concrete implementation of a Square
class Square: Shape {
    let side: Double

    init(side: Double) {
        self.side = side
    }

    func area() -> Double {
        return side * side
    }
}

// Function to calculate total area of shapes
func calculateTotalArea(_ shapes: [Shape]) -> Double {
    var totalArea = 0.0
    for shape in shapes {
        totalArea += shape.area()
    }
    return totalArea
}

// Usage example
let shapes: [Shape] = [Circle(radius: 5.0), Square(side: 4.0)]
let totalArea = calculateTotalArea(shapes)
print("Total area: \(totalArea)")

```
This example demonstrates the Open/Closed Principle by allowing for extension (new shapes can be added) without modifying existing code (the calculateTotalArea function).

### Liskov Substitution Principle

```swift
// Liskov Substitution Principle Example

// Protocol defining the behavior of a Bird
protocol Bird {
    func fly()
}

// Concrete implementation of a Sparrow conforming to Bird
class Sparrow: Bird {
    func fly() {
        print("Sparrow flying")
    }
}

// Concrete implementation of an Ostrich conforming to Bird
class Ostrich: Bird {
    func fly() {
        // Ostriches cannot fly, so this method does nothing
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
makeBirdFly(ostrich) // Outputs: (nothing, as ostriches cannot fly)

```
In this example, the makeBirdFly function accepts any type conforming to the Bird protocol. It demonstrates the Liskov Substitution Principle by allowing instances of concrete bird types (Sparrow and Ostrich) to be passed in interchangeably without altering the correctness of the program, even though ostriches cannot fly.


### Interface Segregation Principle

```swift
// Interface Segregation Principle Example

// Protocol defining the behavior of a Worker
protocol Worker {
    func work()
}

// Protocol defining the behavior of a Eater
protocol Eater {
    func eat()
}

// Protocol defining the behavior of a Sleeper
protocol Sleeper {
    func sleep()
}

// Concrete implementation of a RobotWorker conforming to Worker
class RobotWorker: Worker {
    func work() {
        print("Robot working")
    }
}

// Concrete implementation of a HumanWorker conforming to Worker and Eater
class HumanWorker: Worker, Eater {
    func work() {
        print("Human working")
    }

    func eat() {
        print("Human eating")
    }
}

// Concrete implementation of a HumanSleeper conforming to Worker and Sleeper
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
let human = HumanWorker()

robot.work() // Outputs: Robot working
human.work() // Outputs: Human working
human.eat()  // Outputs: Human eating
```

This example demonstrates the Interface Segregation Principle by avoiding a single general-purpose interface for workers and instead providing separate interfaces (Worker, Eater, Sleeper) that clients can use individually based on their specific needs. This way, clients don't need to implement or depend on methods they don't use.


### Dependency Inversion Principle

Do you DIP? ... Let's find out.

The Dependency Inversion Principle (DIP) is a design principle in object-oriented programming that helps to make software modules more reusable and maintainable.


Think of it like this: instead of your coffee machine (high-level module) needing to know how to grow, harvest, grind, and brew coffee beans (low-level modules), it just needs to know about the concept of 'coffee' (the abstraction). This way, if you want to change how your coffee is made, you can do so without having to buy a new coffee machine¹. This principle makes your code more flexible and easier to manage.

**Swift:**
In Swift, the Dependency Inversion Principle is about creating a level of abstraction between modules through protocols. High-level modules, like a view controller, should not depend directly on low-level things, like a networking component. Instead, it should depend on abstractions or in Swift term, protocol⁶. This allows you to switch out dependencies without changing the high-level module.

**Objective-C:**
In Objective-C, the Dependency Inversion Principle is similar but uses a different mechanism for abstraction. High-level modules should not depend on low-level modules. Both should depend on abstractions. Objective-C also uses abstract classes as a form of abstraction. An abstract class in Objective-C is a class that declares but doesn’t fully implement its methods. Subclasses of this abstract class are expected to provide the implementation. This is another way to achieve dependency inversion: the high-level module depends on the abstract class, not the concrete subclass.By using these, you can invert the dependencies in your code, making high-level modules independent of the low-level module implementation details.

```swift

// The 'Coffee' protocol represents the abstraction of coffee
protocol Coffee {
    func prepare() -> String
}

// 'Arabica' and 'Robusta' are concrete implementations (low-level modules) of the 'Coffee' protocol
class Arabica: Coffee {
    func prepare() -> String {
        let growing = "Growing Arabica coffee: Arabica coffee is typically grown in high altitudes with a mild climate and rich soil."
        let harvesting = "Harvesting Arabica coffee: The coffee cherries are hand-picked when they are perfectly ripe."
        let processing = "Processing Arabica coffee: The cherries are processed either by the dry method (sun-drying) or the wet method (removing the pulp and drying the beans)."
        let roasting = "Roasting Arabica coffee: The green coffee beans are roasted at a temperature of about 200°C until they reach a rich, dark brown color."
        let grinding = "Grinding Arabica coffee: The roasted beans are ground to a consistency similar to sea salt for a balanced extraction."
        let shipping = "Shipping Arabica coffee: The brewed coffee is packaged and shipped to coffee shops and stores around the world."
        
        return "\(growing)\n\(harvesting)\n\(processing)\n\(roasting)\n\(grinding)\n\(shipping)"
    }
}

class Robusta: Coffee {
    func prepare() -> String {
        return "Preparing Robusta coffee..."
    }
}

// 'CoffeeMachine' is a high-level module that depends on the 'Coffee' abstraction
class CoffeeMachine {
    var coffee: Coffee
    
    init(coffee: Coffee) {
        self.coffee = coffee
    }
    
    func getCoffee() {
        print(coffee.prepare())
    }
}

// Usage example
let arabica = Arabica()
let robusta = Robusta()

let coffeeMachine = CoffeeMachine(coffee: arabica)
coffeeMachine.getCoffee()

// Changing the coffee type without buying a new coffee machine
coffeeMachine.coffee = robusta
coffeeMachine.getCoffee()



```
