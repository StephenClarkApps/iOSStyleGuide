# Working with Tables  
**By: Stephen Clark (various original sources)**

When building iOS apps, tables are one of the fundamental building blocks for constructing user interfaces. Whether you're displaying lists, forms, or grouped data, tables provide a robust, flexible solution. This guide will walk you through the UIKit-based approach and how you can achieve similar functionality in SwiftUI.

---

## The UIKit-Based Approach

### Why Use a `UITableView`?

A `UITableView` is highly versatile and should be considered whenever your view controller needs to handle any of the following scenarios:

1. **Repeating Vertical Elements:**  
   If your view contains vertically repeating UI elements—such as lists of items or rows of information—each element can naturally map to a `UITableViewCell`. This simplifies both design and code, since each cell is responsible for its content.

2. **Conditional Display of Vertical Elements:**  
   Often, you might have UI blocks that should only be visible under certain conditions. Using a `UITableViewCell` for each block simplifies the process, as you can easily include or exclude cells (or even whole sections) without having to manipulate a complex layout of views and constraints.

3. **Automatic Scrolling:**  
   `UITableView` inherently supports scrolling and calculates its own content size. In contrast, using a `UIScrollView` with manually added subviews often requires extra work to determine the correct content size dynamically, especially when the views have varying dimensions.

### Identifying What Elements to Render

When your table includes different cell types or multiple sections, managing the layout becomes more structured by using `enum` values. This approach helps in determining which cells to display, in what order, and under which conditions—reducing repeated calculations and potential errors. Here's an example of how you could model a table that displays different payment card details:

#### 1. Define Your Cell Types

Create an enumeration that represents each type of cell that your table could display:

```swift
enum NRICardDetailsCellType: Int {
    case expiryDate
    case cvv
    case scanCard
    case cardNumber
    case saveData
}
```

#### 2. Calculate Which Rows to Display

Based on your application logic, create an array of these enum values in the order you want the cells to appear. For example, you might want to display different rows depending on whether a card is already saved:

```swift
func calculateRows() {
    if self.isSavedCard {
        self.rows = [.cardNumber, .expiryDate, .cvv]
    } else {
        self.rows = [.scanCard, .cardNumber, .expiryDate, .cvv, .saveData]
    }
}
```

#### 3. Return the Number of Rows or Sections

Depending on whether your table is divided into sections or simply rows, return the appropriate count:

```swift
// For rows in a single section:
func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    return self.rows.count
}

// For multiple sections:
func numberOfSections(in tableView: UITableView) -> Int {
    return self.sections.count
}
```

#### 4. Return the Correct `UITableViewCell`

Implement the data source method to dequeue and configure cells based on your enum sequence. Using a switch statement makes it straightforward to determine which cell to return:

```swift
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    let row = self.rows[indexPath.row]
    switch row {
    case .expiryDate:
        // Dequeue and configure the expiry date cell
    case .cvv:
        // Dequeue and configure the CVV cell
    case .scanCard:
        // Dequeue and configure the scan card cell
    case .cardNumber:
        // Dequeue and configure the card number cell
    case .saveData:
        // Dequeue and configure the save data cell
    }
}
```

This modular approach makes your table view dynamic and easy to update. You define the sequence once and then reuse it throughout your table view data source methods.

---

## The SwiftUI Approach

SwiftUI provides a different paradigm for building lists and dynamic interfaces, emphasizing a declarative style. One of the most straightforward ways to create a list in SwiftUI is by using the `List` view.

### Example: Creating a Dynamic List in SwiftUI

Consider an app that displays a list of restaurants. Each restaurant’s details are encapsulated in a model, and a row view is defined for presenting each restaurant’s information.

```swift
// A struct to store each restaurant’s data.
struct Restaurant: Identifiable {
    let id = UUID()
    let name: String
}

// A view that displays the information for one Restaurant.
struct RestaurantRow: View {
    var restaurant: Restaurant

    var body: some View {
        Text("Come and eat at \(restaurant.name)")
    }
}

// The main view that presents a list of restaurants.
struct ContentView: View {
    let restaurants = [
        Restaurant(name: "Joe's Original"),
        Restaurant(name: "The Real Joe's Original"),
        Restaurant(name: "Original Joe's")
    ]

    var body: some View {
        List(restaurants) { restaurant in
            RestaurantRow(restaurant: restaurant)
        }
    }
}
```

#### What's Happening Here?

- **Data Modeling:**  
  The `Restaurant` struct conforms to `Identifiable`, which SwiftUI uses to uniquely identify each item in the list.
  
- **Row View:**  
  The `RestaurantRow` view is a reusable component that takes a `Restaurant` instance and displays its content.
  
- **List Construction:**  
  In `ContentView`, the `List` view is created by passing the array of restaurants. SwiftUI automatically iterates over the array and instantiates a `RestaurantRow` for each item.

### Advantages of SwiftUI Lists

- **Declarative Syntax:**  
  Lists in SwiftUI are defined using a simple, declarative syntax that makes the intent of your UI clear.
  
- **Automatic Updates:**  
  SwiftUI monitors your data and automatically updates the UI when your data changes.

- **Integration with State:**  
  SwiftUI effortlessly integrates with state management, so dynamic content and interactions become straightforward.

---

## Conclusion

Both UIKit and SwiftUI offer robust ways to work with tables and lists, each with their own strengths:

- **UIKit** is flexible and highly customizable, especially suited to complex layouts where individual control over cell reuse, animations, and interface behavior is needed.
- **SwiftUI** offers a concise, declarative approach, making it easier to express dynamic content with less boilerplate code.

By understanding these approaches, you can choose the one that best fits your application’s needs or even mix both in a transitional project. Happy coding!
