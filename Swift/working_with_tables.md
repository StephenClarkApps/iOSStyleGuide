# Working with Tables
***By: Stephen Clark (various original sources)***

Tables are a fundamental building block of the UI within iOS and can be used for many views which may not at first glance appear to be a table. You should consider using a `UITableView` for any view controller which has the following:

### Repeating vertical elements (rows)

If you have the same or similar UI elements repeated vertically then these naturally lend themselves to being a `UITableViewCell`

### Vertical elements that are to be included / excluded in certain circumstances

When you have vertical blocks of UI which may or may not display based on certain criteria, it's far easier to build these as `UITableViewCells` and include / exclude them rather than attempting to have the UI in a `UIView` with a complex set of constraints that are enabled / disabled or have varying priorities.

### Where scrolling is required

`UITableViews` naturally support scrolling and are capable of calculating their own content size. If you create a `UIScrollView` an add subviews then you need to help the system identify the height of the content and this can often be more complex.

Identifying What Elements to Render
======

When working with tables that contain different cell types or sections, the best approach is to define `enum` values for each section / cell type and then use an array to build up the sequence that these should be displayed in. That way you only have to calculate the order of elements once, and then when you are returning the various cells / headers etc you can just refer to the enum value. The following outlines the steps needed. This can apply to either sections or rows, in the example below it's applied to a table with a single section a multiple rows:

Define your `enum`
------

```swift
enum NRICardDetailsCellType: Int {
    case expiryDate
    case cvv
    case scanCard
    case cardNumber
    case saveData
}
```

Calculate which rows to display
------

Based on whatever criteria is applicable create an array of the `enum` values based on the sequence you want to render the cells:

```swift
func calculateRows() {
    if self.isSavedCard {
        self.rows = [.cardNumber, .expiryDate, .cvv]
    } else {
        self.rows = [.scanCard, .cardNumber, .expiryDate, .cvv, .saveData]
    }
}
```

Identifying the number of rows/sections to return
------

Depending on whether you are working with sections or rows use one of the following:

```swift
func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    return self.rows.count
}
```

or

```swift
func numberOfSections(in tableView: UITableView) -> Int {
    return self.sections.count
}
```

Return the correct `UITableViewCell`
------

```swift
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    let row = self.rows[indexPath.row]
    switch row {
    case .expiryDate:
        // Return expiry cell
    case .cvv:
        // Return cvv cell
    case .scanCard:
        // Return scan cell
    case .cardNumber:
        // Return card number cell
    case .saveData:
        // Return save cell
    }
}
```
