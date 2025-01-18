# Swift Style Guide
*By: Stephen Clark (adapted from various sources)*

This guide is a synthesis of best practices drawn from well‐known style guides—including those from Kodeco, Airbnb, Apple, and others—to promote clarity, consistency, and maintainability.

---

## Table of Contents
- [Code Formatting](#code-formatting)
- [Naming Conventions](#naming-conventions)
- [Coding Style](#coding-style)
- [Comments](#comments)
- [Classes vs. Structs](#classes-vs-structs)
- [Enums](#enums)
- [Use of Singletons](#use-of-singletons)
- [Dependency Injection](#dependency-injection)
  - [Constructor Injection](#constructor-injection)
  - [Property and Parameter Injection](#property-and-parameter-injection)
- [Higher Order Functions](#higher-order-functions)
- [Indentation and Line Length](#indentation-and-line-length)
- [Declaring Variables](#declaring-variables)
- [Generics](#generics)
- [Collections: Arrays & Dictionaries](#collections-arrays--dictionaries)
- [Optionals](#optionals)
- [Initialization](#initialization)
- [Use of Self](#use-of-self)
- [Access Control](#access-control)
- [Life Cycle Methods](#life-cycle-methods)
- [Protocols & Protocol-Oriented Programming](#protocols--protocol-oriented-programming)
- [Early Returns](#early-returns)
- [IBAction](#ibaction)
- [Handling Incomplete Code](#handling-incomplete-code)
- [Magic Numbers and Numeric Constants](#magic-numbers-and-numeric-constants)
- [Completion Blocks and Escaping Closures](#completion-blocks-and-escaping-closures)
- [Avoiding Retain Cycles](#avoiding-retain-cycles)
- [References](#references)

---

## Code Formatting

### Indentation and Spacing
- **Indentation:** Use 4 spaces (or 4 tabs, per your Xcode settings). *(Many teams now use 2 spaces; choose one style and remain consistent.)*
- **Line Length:** Avoid excessively long lines; aim for a maximum of approximately 160 characters.
  > *Tip:* In Xcode, set the page guide at column 160 via **Xcode > Preferences > Text Editing > Page guide**.
- **Line-Wrapping:** Break long expressions into multiple lines.
- **Braces (1TBS Style):**  
  Place the opening brace on the same line as its associated statement. For keywords following a block (e.g., `else`, `catch`), place the keyword on the same line as the block’s closing brace.

  **Example:**
  ```swift
  if condition {
      // do something
  } else {
      // do something else
  }

## Naming Conventions

- **Types (Classes, Structs, Enums, Protocols, Type Aliases):** Use **PascalCase**.
  - _Example:_ `UserProfile`, `NetworkManager`
- **Functions, Methods, Variables, and Properties:** Use **camelCase**.
  - _Example:_ `fetchUser()`, `currentUser`
- **Boolean Values:** Prefix with “is” or “has.”
  - _Example:_ `isEnabled`, `hasData`
- **Constants:**  
  Use `static let` for instance-independent constants and group similar constants together.
- **Function Arguments:** Use descriptive names that clarify the purpose of each parameter.
- **Protocols:**  
  Use nouns if the protocol describes a thing (e.g., `Collection`), or adjectives/suffixes like *-able*, *-ible*, or *-ing* for capabilities (e.g., `Equatable`, `ProgressReporting`). Optionally, add “Protocol” if needed.
- **Avoid Abbreviations:** Use full, unambiguous names rather than shortened or single-letter names unless used in a limited local context.

---

## Coding Style

- **Method Overriding:**  
  Mark methods as `final` when they are not intended to be overridden. *(Be cautious when using `final` in libraries.)*
- **Control Flow:**  
  Place keywords like `else` and `catch` on the same line as the preceding block’s closing brace.
- **Consistency:**  
  Always follow your chosen style for braces, spaces, and punctuation throughout your code.

---

## Comments

- **Purpose:**  
  Use comments to explain *why* something is done, not merely what is done.
- **Documentation Comments:**  
  Use triple-slash `///` for documenting public methods and types. These comments can be auto-generated in Xcode's documentation.
  - _Example:_
    ```swift
    /// Loads the latest albums from the photo library.
    func reloadAlbums() {
        albums = PhotoLibrary.shared.albums
    }
    ```
- **Inline Comments:**  
  Use sparingly. Avoid inline block comments; instead, use clear and descriptive names.
- **Special Directives:**  
  Use markers such as `// MARK:`, `// TODO:`, and `// FIXME:` for organization and to flag items for later revision.

---

## Classes vs. Structs

- **Structs:**  
  Use structs for small, copiable data types that do not require identity. They are value types, allocated on the stack.
- **Classes:**  
  Use classes when reference semantics or identity are necessary. They are allocated on the heap.
- **Guideline:**  
  *Prefer structs unless there is a strong reason to use a class.*  
  *(Refer to [Swift.org](https://swift.org/documentation/api-design-guidelines/) for more details.)*

---


## Enums

- **Definition:**  
  Swift enums can have associated values and methods.
  ```swift
  enum CompassPoint {
      case north, south, east, west
  }
  ```
- **Usage:**  
  Use the shorthand enum-case notation when possible.
  - _Preferred:_
    ```swift
    imageView.setImage(withURL: url, type: .person)
    ```
  - _Not Preferred:_
    ```swift
    imageView.setImage(withURL: url, type: AsyncImageView.Type.person)
    ```

---

## Use of Singletons

- **Avoid Overuse:**  
  Singletons hide dependencies, can violate the Single Responsibility Principle, and lead to tight coupling. Use only when a unique, globally accessible resource is necessary (e.g., logging).
- **Alternative:**  
  Use dependency injection to supply instances instead of relying on global singletons.

---

## Dependency Injection

Dependency Injection (DI) decouples components and facilitates unit testing.

### Constructor Injection
- **Preferred:**  
  Inject dependencies via initializers.
  ```swift
  protocol Propulsion {
      func start()
  }
  
  class RaceCarEngine: Propulsion {
      func start() {
          print("Vroom!")
      }
  }
  
  class Vehicle {
      let engine: Propulsion
      init(engine: Propulsion) {
          self.engine = engine
      }
  }
  
  let car = Vehicle(engine: RaceCarEngine())
  ```

### Property and Parameter Injection
- **Property Injection:**  
  Set dependencies on properties after initialization when necessary.
  ```swift
  class CarViewModel {
      var carType: CarType?
  }
  ```
- **Parameter Injection:**  
  Pass dependencies as parameters—often with default values—to avoid hard-coding global references.
  ```swift
  class MessageSender {
      static func send(_ message: Message,
                       to user: User,
                       database: Database = .shared,
                       networkManager: NetworkManager = .shared) throws {
          database.insert(message)
          let data = try wrap(message)
          let endpoint = Endpoint.sendMessage(to: user)
          networkManager.post(data, to: endpoint.url)
      }
  }
  ```

---

## Higher Order Functions

- **Use Built-In Functions:**  
  Prefer functions like `map`, `filter`, `reduce`, and `forEach` for collection transformations.
  ```swift
  let evenNumbers = [1, 2, 3, 4].filter { $0 % 2 == 0 }
  [1, 2, 3, 4].forEach { print($0) }
  ```
- **For Early Exit:**  
  Use traditional loops when you need to break early.

---

## Indentation and Line Length

- **Indentation:**  
  Consistently use your chosen indent width (e.g., 4 spaces).
- **Line Length:**  
  Break long lines so that they remain within 160 characters.
- **Braces:**  
  Place opening and closing braces according to the 1TBS style.

---

## Declaring Variables

- **Type Inference:**  
  Specify types only when no initial value is provided; otherwise, let the compiler infer the type.
  - _Preferred:_
    ```swift
    var title = "Hello, world!"
    var count: Int  // when declaring without an initializer
    ```
- **Spacing:**  
  Place the colon immediately after the variable name with a single space after it.
  ```swift
  var title: String
  ```

---

## Generics

- **Syntax:**  
  Use angle brackets to declare generics and constrain them as needed.
  ```swift
  func mergeSort<T: Comparable>(_ array: [T]) -> [T] {
      // implementation here
  }
  ```
- **Naming:**  
  Use descriptive generic parameter names or single uppercase letters (e.g., `T`, `U`) when appropriate.
  - _Preferred:_
    ```swift
    struct Stack<Element> { … }
    func swap<T>(_ a: inout T, _ b: inout T) { … }
    ```
  - _Not Preferred:_
    ```swift
    struct Stack<T> { … }
    func swap<Thing>(_ a: inout Thing, _ b: inout Thing) { … }
    ```

---

## Collections: Arrays & Dictionaries

- **Arrays:**  
  Use literal syntax.
  ```swift
  let names: [String]
  ```
- **Dictionaries:**  
  Use concise literal syntax.
  ```swift
  let items: [String: AnyObject]
  ```

---

## Optionals

### Optional Binding

- **`if let`:**
  - _Preferred:_
    ```swift
    if let title = someOptionalString {
        print(title)
    }
    ```
  - _Not Preferred:_
    ```swift
    if someOptionalString != nil {
        print(someOptionalString!)
    }
    ```

- **`guard let`:**
  - _Preferred:_
    ```swift
    func printMessage(_ message: String?) {
        guard let message = message else { return }
        print(message)
    }
    ```

### Nil-Coalescing Operator
Provide a default value using `??`.
```swift
let result = optionalString ?? "Default value"
```

### Optional Chaining
Access properties or methods safely on an optional.
```swift
let favoriteFood = myCat?.favoriteFood
```

### Implicitly Unwrapped Optionals
Use sparingly (primarily for IBOutlets).
```swift
@IBOutlet weak var someLabel: UILabel!
```

---

## Initialization

- **Constructor Calls:**  
  Do not explicitly call `.init` unless needed.
  - _Preferred:_
    ```swift
    let alert = AlertController(title: "Title", message: "Message", preferredStyle: .alert)
    ```
  - _Not Preferred:_
    ```swift
    let alert = AlertController.init(title: "Title", message: "Message", preferredStyle: .alert)
    ```

- **Empty Arrays:**  
  Use shorthand literal syntax.
  - _Preferred:_
    ```swift
    var myArray = [MyClass]()
    ```
  - _Not Preferred:_
    ```swift
    var myArray: [MyClass] = []
    ```

---

## Use of Self

- **General Rule:**  
  Omit the `self.` prefix unless needed for disambiguation (e.g., within an initializer or an escaping closure).
- **Explicit Self Style:**  
  Some teams prefer always using `self.` for clarity. Choose a consistent approach.
  - _Example (Explicit Self):_
    ```swift
    private let setupMessage = "Setting Up Now"
    
    private func setup() {
        print(self.setupMessage)
    }

    override func viewDidLoad() {
        super.viewDidLoad()
        self.setup()
    }
    ```

---

## Access Control

- **Modifiers:**  
  Use `private` for properties and methods that should be hidden; use `fileprivate` if access within the same file is necessary.
  - _Preferred:_
    ```swift
    private static let privateValue: Int = 42
    ```
  - _Not Preferred:_
    ```swift
    static private let privateValue: Int = 42
    ```
- **Default Level:**  
  The default access level is `internal`; do not explicitly mark it unless clarity is needed.
- **Public vs. Open:**  
  Use `public` for framework APIs and `open` if classes should be subclassable externally.

---

## Life Cycle Methods

Place life cycle methods at the beginning of a class in the order they are called:
```swift
override func viewDidLoad() { … }
override func viewWillAppear(_ animated: Bool) { … }
override func viewDidAppear(_ animated: Bool) { … }
override func viewWillDisappear(_ animated: Bool) { … }
override func viewDidDisappear(_ animated: Bool) { … }
deinit { … }
```

---

## Protocols & Protocol-Oriented Programming

- **Definition:**  
  Use protocols to define a blueprint of methods, properties, and other requirements.
- **Documentation:**  
  Use `///` to document protocol requirements.
- **Adoption and Conformance:**  
  Place protocol conformance in extensions and mark these sections with `// MARK:`.
  - _Preferred:_
    ```swift
    class MyClass { /* core implementation */ }

    // MARK: - UITableViewDataSource
    extension MyClass: UITableViewDataSource {
        // Data source methods here
    }

    // MARK: - UITableViewDelegate
    extension MyClass: UITableViewDelegate {
        // Delegate methods here
    }
    ```
  - _Not Preferred:_
    ```swift
    class MyClass: UITableViewDataSource, UITableViewDelegate {
        // All methods in one place
    }
    ```
- **Protocol Inheritance:**  
  Protocols can inherit multiple protocols.
  ```swift
  protocol InheritingProtocol: SomeProtocol, AnotherProtocol { … }
  ```
- **Protocol-Oriented Programming:**  
  Favor small, focused protocols and use default implementations via protocol extensions to share functionality.

---

## Early Returns

- **Purpose:**  
  Reduce nesting by returning early when conditions are not met.
  ```swift
  func process() {
      guard condition else { return }
      // Process further
  }
  ```

---

## IBAction

- **Simplify Signatures:**  
  Omit the sender parameter if it is not used.
  - _Preferred:_
    ```swift
    @IBAction func listSelected() {
      // ...
    }
    ```
  - _Not Preferred:_
    ```swift
    @IBAction func listSelected(_ sender: Any) {
      // ...
    }
    ```

---

## Handling Incomplete Code

- **TODO Comments:**  
  Use `// TODO:` to flag code that requires further work.
- **Remove Empty Methods:**  
  Delete automatically generated stub methods that aren’t used.
- **Placeholder UI:**  
  For UI elements not yet connected to functionality, provide a temporary alert or comment indicating they are pending implementation.

---

## Magic Numbers and Numeric Constants

- **Avoid Inline Hard-Coded Numbers:**  
  Define all numeric constants at the top of the file (or in a dedicated constants file) with descriptive names.
  ```swift
  let cellHeight: CGFloat = 44.0
  let sectionHeaderHeight: CGFloat = 58.0
  let itemCellHeight: CGFloat = 95.0
  ```
- **Group Related Constants:**  
  Group constants logically for clarity and maintainability.

---

## Completion Blocks and Escaping Closures

- **Typealiases:**  
  Define typealiases for common closure types for clarity.
  ```swift
  typealias CompletionHandler = (String) -> Void
  func performAction(input: String, completion: CompletionHandler) { … }
  ```
- **Escaping Closures:**  
  Annotate closures with `@escaping` when they are called after the function returns.
  ```swift
  func asyncProcess(completion: @escaping (Result<String, Error>) -> Void) {
      // implementation
  }
  ```

---

## Avoiding Retain Cycles

### In Closures
- **Capture Lists:**  
  Use `[weak self]` to prevent strong reference cycles.
  - _Preferred:_
    ```swift
    resource.request().onComplete { [weak self] response in
        guard let self = self else { return }
        self.updateUI(with: response)
    }
    ```
  - **Avoid:**  
    Using `[unowned self]` unless you are absolutely certain `self` will not be deallocated during the closure's lifetime.

### In Delegate Patterns
- **Weak Delegates:**  
  Declare delegate properties as weak and constrain the protocol to `AnyObject`.
  ```swift
  protocol DataDelegate: AnyObject { … }
  
  class Sender {
      weak var delegate: DataDelegate?
  }
  ```

---

## References

- [Swift API Design Guidelines](https://swift.org/documentation/api-design-guidelines/)
- [Kodeco Swift Style Guide](https://www.kodeco.com/)
- [Airbnb Swift Style Guide](https://github.com/airbnb/swift)
- [Apple Developer Documentation](https://developer.apple.com/swift/)
- [Ray Wenderlich Swift Style Guide](https://github.com/raywenderlich/swift-style-guide)
- [John Sundell on Dependency Injection](https://www.swiftbysundell.com/articles/dependency-injection-in-swift/)

---

*This guide is intended to evolve as our projects and Swift itself progress. Please propose amendments as needed to continuously improve our shared coding standards. Happy coding! :]
