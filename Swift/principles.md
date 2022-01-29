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
