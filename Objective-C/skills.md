# Objective-C Best Practices

This guide covers several common Objective-C patterns and best practices—from using dot syntax to managing notifications, singleton instances, and differentiating between instance and class methods.

---

## Dot Syntax vs. Message Syntax

- **Dot Syntax:**  
  Use dot syntax for properties, e.g.,  
  ```objc
  self.myArray.count
  ```  
- **Message Syntax:**  
  Use the traditional bracket syntax for method calls, e.g.,  
  ```objc
  [self.myArray count]
  ```

Dot syntax is generally preferred for property access, making your code more concise and readable.

---

## Checking the Device Type

To determine whether your app is running on an iPhone or iPad, use the `UI_USER_INTERFACE_IDIOM()` macro:

- **iPhone:**
  ```objc
  UI_USER_INTERFACE_IDIOM() == UIUserInterfaceIdiomPhone
  ```
- **iPad:**
  ```objc
  UI_USER_INTERFACE_IDIOM() == UIUserInterfaceIdiomPad
  ```

This allows you to tailor your UI logic based on the device type.

---

## Interface Orientation vs. Device Orientation

- **Interface Orientation:**  
  Determines the current layout of the UI on the screen. It can be:
  - `UIInterfaceOrientationPortrait`
  - `UIInterfaceOrientationLandscapeLeft` or `UIInterfaceOrientationLandscapeRight`
  
  Check it using:
  ```objc
  UIInterfaceOrientationIsPortrait([UIApplication sharedApplication].statusBarOrientation)
  UIInterfaceOrientationIsLandscape([UIApplication sharedApplication].statusBarOrientation)
  ```

- **Device Orientation:**  
  Indicates how the physical device is oriented (portrait, landscape, upside down, face up, face down, or unknown). This value does not directly affect the UI.

For most UI decisions, **interface orientation** is what you want to check.

---

## Notification Center: Adding Observers

There are two common approaches for adding observers with `NSNotificationCenter`:

1. **Block-Based Listener:**
   ```objc
   [[NSNotificationCenter defaultCenter] addObserverForName:CityAnalyticsDeviceIDDidChangeNotification
                                                     object:nil
                                                      queue:nil
                                                 usingBlock:^(NSNotification * _Nonnull note) {
       [self cityAnalyticsDeviceIDChanged];
   }];
   ```

2. **Selector-Based Listener:**
   ```objc
   [[NSNotificationCenter defaultCenter] addObserver:self
                                            selector:@selector(keyboardWillShow:)
                                                name:UIKeyboardWillShowNotification
                                              object:nil];
   ```

Choose the method that best fits your use case. Block-based listeners allow for inline handling, while selector-based observers are more traditional.

---

## Notification Center: Removing Observers

To avoid unexpected behavior or memory leaks, remove observers appropriately:

- **Remove All Observers for a Given Object:**
  ```objc
  [[NSNotificationCenter defaultCenter] removeObserver:self];
  ```

- **Remove a Specific Observer:**
  ```objc
  [[NSNotificationCenter defaultCenter] removeObserver:self
                                                  name:UIApplicationDidBecomeActiveNotification
                                                object:nil];
  ```

- **Remove Block-Based Observer:**
  If you stored the observer token (e.g., in `self.initialSettingsLoadedObserver`), remove it like so:
  ```objc
  self.initialSettingsLoadedObserver = [[NSNotificationCenter defaultCenter] addObserverForName:kSettingsLoaded
                                                                                             object:nil
                                                                                              queue:nil
                                                                                         usingBlock:^(NSNotification * _Nonnull note) {
      [[NSNotificationCenter defaultCenter] removeObserver:self.initialSettingsLoadedObserver];
      weakSelf.initialSettingsLoadedObserver = nil;
      // Your code goes here
  }];
  ```

---

## Defining a Singleton / Shared Instance

A common pattern for singletons in Objective-C uses `dispatch_once` to ensure a single instance is created:

```objc
+ (NNStyleManager *)sharedInstance {
    static dispatch_once_t onceToken;
    static NNStyleManager *sharedInstance = nil;
    
    dispatch_once(&onceToken, ^{
        sharedInstance = [NNStyleManager new];
    });
    
    return sharedInstance;
}
```

This method guarantees that `sharedInstance` is instantiated only once and is thread-safe.

---

## When to Use `self` vs. `weakSelf`

- **`self`:**  
  Use when there is no risk of causing a retain cycle—typically in synchronous code or when the object is guaranteed to persist (e.g., the app delegate or singleton).

- **`weakSelf`:**  
  Use inside completion blocks to avoid increasing the object's retain count. This avoids potential memory leaks if the object might have been deallocated by the time the block executes. For example:
  
  ```objc
  __weak typeof(self) weakSelf = self;
  [someObject performActionWithCompletion:^{
      [weakSelf doSomething];
  }];
  ```

---

## Instance Methods vs. Class Methods

- **Instance Methods:**  
  Operate on individual instances of a class. They are defined with a minus (`-`) sign:
  ```objc
  - (void)myInstanceMethod {
      // Instance-specific code here
  }
  ```
  Example usage:
  ```objc
  MyClass *instance = [MyClass new];
  [instance myInstanceMethod];
  ```

- **Class Methods:**  
  Operate on the class itself and do not require an instance. They are defined with a plus (`+`) sign:
  ```objc
  + (void)myClassMethod {
      // Code that applies to the class as a whole
  }
  ```
  Example usage:
  ```objc
  [MyClass myClassMethod];
  ```

Remember that class methods have no access to instance properties or instance-specific data because they are not tied to any particular object.

---

By following these best practices, you can write cleaner, more maintainable Objective-C code that leverages the language's conventions and built-in functionalities effectively. Happy coding!
