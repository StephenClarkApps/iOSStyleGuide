# Objective-C Style Guide  
**By: Stephen Clark (Various original sources)**

This guide is influenced by Apple’s documentation and several reputable sources. For further reading, see:

- [The Objective-C Programming Language](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
- [Cocoa Fundamentals Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)
- [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
- [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)

---

## Table of Contents

1. [Properties](#properties)
    - [Format](#format)
    - [Array & Dictionary Format](#collection-formats)
    - [Primitive & Outlet Formats](#primitive--outlet-formats)
2. [Variable Property Attributes](#variable-property-attributes)
    - [Atomicity](#atomicity)
    - [Access](#access)
    - [Storage](#storage)
3. [Private Properties](#private-properties)
4. [Generics](#generics)
5. [Nullability Annotations](#nullability-annotations)
6. [Method Declarations](#method-declarations)
7. [Imports & Header Organization](#imports--header-organization)
8. [Implementation Files](#implementation-files)
9. [Constants](#constants)
10. [Accessors and Ivars](#getters-setters-and-ivars)
11. [Dot Notation](#dot-notation)
12. [Grouping](#grouping-properties--methods)
13. [Nil Checks and Enumerations](#nil-checks--enumeration)
14. [instancetype & Safe Casting](#instancetype--safe-casting)
15. [Object Initializers](#allocating--initialising-objects)
16. [Switch Statements & Enums](#switch-statements--enums)
17. [Arrays and Dictionaries](#arrays--dictionaries)
18. [64-bit Compatible Types](#64bit-compatible-types)
19. [Reuse Identifiers](#table--collection-view-reuse-identifiers)
20. [Completion Blocks](#completion-block-parameter-formatting)
21. [Clearing UI](#clearing-ui)
22. [Singleton Pattern](#singleton-pattern)
23. [Blocks & __block Variables](#blocks)
24. [Retain Cycles in ARC](#retain-cycles-in-arc-using-blocks)
25. [CGGeometry & CGRect Functions](#cggeometry)
26. [String Replacement](#string-replacement)
27. [Commenting](#commenting)
28. [Fonts, Colours, File Management, and General Practices](#fonts)
29. [Spacing and Indentation](#spacing)
30. [Conditionals and Ternary Operator](#conditionals--ternary-operator)
31. [Error Handling](#error-handling)
32. [Methods & Variables](#methods--variables)
33. [Naming Conventions](#naming)
34. [Categories](#categories)
35. [init and dealloc](#init-and-dealloc)
36. [Literals](#literals)
37. [Constants and Bitmasks](#constants--bitmasks)
38. [Image Naming & Booleans](#image-naming--booleans)
39. [Imports and Protocols](#imports--protocols)
40. [Xcode Project Organization](#xcode-project)
41. [Other Objective-C Style Guides](#other-objective-c-style-guides)

---

## Properties

**Format:**  
Declare properties using the following template:

```objc
@property ([weak/strong], nonatomic, [readonly/readwrite], [nullability]) [type] *[name];
```

### Collection Formats

- **Array:**  
  ```objc
  @property (strong, nonatomic, [readwrite/readonly], [nullability]) NSArray<[ElementType] *> *name;
  ```

- **Dictionary:**  
  ```objc
  @property (strong, nonatomic, [readwrite/readonly], [nullability]) NSDictionary<[KeyType] *, [ValueType] *> *name;
  ```

### Primitive & Outlet Properties

- **Primitives:**  
  ```objc
  @property (nonatomic, assign) Type name;
  // `assign` is the default; you can omit it if desired.
  ```

- **Outlets:**  
  ```objc
  @property (weak, nonatomic) IBOutlet Type *name;
  ```
  *(Do not add nullability annotations to weak properties.)*

---

## Variable Property Attributes

### Atomicity

- **atomic (default):**  
  Ensures properties are thread-safe by synchronizing access.
  
- **nonatomic:**  
  Faster, but not thread-safe. Use when you don’t require atomicity.

### Access

- **readonly:**  
  Only a getter is generated.
  
- **readwrite (default):**  
  Generates both getter and setter.  
  *Tip:* In the public interface, mark properties as `readonly` if external classes shouldn’t modify them; redefine as `readwrite` in a private class extension if needed.

### Storage

- **strong:**  
  Maintains a strong reference to the object.
  
- **weak:**  
  Does not keep the referenced object alive; set to nil when no strong references remain.
  
- **assign:**  
  For primitive types or objects you don’t own (e.g., delegates).
  
- **copy:**  
  Creates an immutable copy, useful when the object is mutable and you don’t want future changes to affect your property.

  ```objc
  @property (copy) NSString *name;
  ```

---

## Private Properties

Declare private properties in a class extension (anonymous category) within the implementation (.m) file:

```objc
@interface MyClass ()
@property (strong, nonatomic) Type *privateProperty;
@end
```

Keep properties and methods private by default; expose only what is needed in the public interface.

---

## Generics

**Always specify types in collections.**

- **Array without:**  
  ```objc
  @property (strong, nonatomic, nullable) NSArray *homeScreenModules;
  ```
- **Array with:**  
  ```objc
  @property (strong, nonatomic, nullable) NSArray<ModuleType *> *homeScreenModules;
  ```

- **Dictionary without:**  
  ```objc
  @property (strong, nonatomic, nullable) NSDictionary *modulesDictionary;
  ```
- **Dictionary with:**  
  ```objc
  @property (strong, nonatomic, nullable) NSDictionary<NSString *, ModuleType *> *modulesDictionary;
  ```

---

## Nullability Annotations

Annotate properties, methods, and blocks to signal whether pointers may be nil:

- **Constants:**  
  ```objc
  Type * _Nonnull const ConstantName;
  ```
- **Properties:**  
  ```objc
  @property (strong, nonatomic, nonnull) NSString *currencyCode;
  ```
- **Methods:**  
  ```objc
  + (nonnull instancetype)currencyWithCode:(nonnull NSString *)code symbol:(nonnull NSString *)symbol countryCode:(nonnull NSString *)countryCode;
  ```
- **Blocks:**  
  ```objc
  typedef void(^CompletionBlock)(NSDictionary * _Nonnull responseDictionary, NSURLResponse * _Nonnull response, NSError * _Nullable error);
  ```

To apply annotations to many declarations at once, wrap with:

```objc
NS_ASSUME_NONNULL_BEGIN
// Declarations (all pointers assume to be nonnull unless annotated)
NS_ASSUME_NONNULL_END
```

---

## Method Declarations

- Place the opening brace on the same line as the declaration:
  
  ```objc
  - (void)viewDidLoad {
      // Implementation
  }
  ```

- For long declarations, split parameters onto new lines with aligned colons:

  ```objc
  + (nullable instancetype)currencyWithCurrencyCode:(nonnull NSString *)currencyCode
                                      currencySymbol:(nonnull NSString *)currencySymbol
                                         countryCode:(nonnull NSString *)countryCode
                                         countryName:(nullable NSString *)countryName;
  ```

- Avoid adding unused sender parameters in IBAction methods.

---

## Imports & Header Organization

- **Frameworks:** Use `@import` (e.g., `@import UIKit;`) instead of `#import`.
- **Order:**  
  - System/framework imports first.
  - Then project-specific imports.
- **Tip:** Use forward declarations (`@class`) in headers to reduce dependencies; import in implementation files if necessary.

---

## Header Files (.h) and Implementation Files (.m)

**Header Files:**

- Expose only the minimum API.
- Group properties and methods by feature.
- Include comments for non-obvious methods.

**Implementation Files:**

- Redeclare any readonly properties as readwrite in a private extension.
- Order methods logically (e.g., initialization, view lifecycle, then helper methods).
- Example order:
  ```objc
  - (instancetype)init;
  - (void)viewDidLoad;
  - (void)viewWillAppear:(BOOL)animated;
  - (void)viewDidAppear:(BOOL)animated;
  - (void)viewWillDisappear:(BOOL)animated;
  - (void)viewDidDisappear:(BOOL)animated;
  - (void)dealloc;
  ```

---

## Constants

- **Declaration in header (.h):**
  ```objc
  extern NSString * _Nonnull const ProjectPrefixNotificationName;
  ```
- **Definition in implementation (.m):**
  ```objc
  NSString * _Nonnull const ProjectPrefixNotificationName = @"NotificationName";
  ```

*Avoid preprocessor macros for constants as they’re untyped and harder to debug.*

---

## Getters, Setters, and Instance Variables

- **Access properties via `self.property`** (except in initializers, dealloc, or custom accessors).
- **Naming conventions:**  
  - Getter: same name as the property.
  - Setter: prefixed with `set` (e.g., `setPropertyName:`).
- Let LLVM synthesize properties unless custom behavior is needed.

---

## Dot Notation

Use dot notation exclusively for properties:

```objc
self.titleLabel.text = @"Text";
```

Avoid mixing with bracket notation for property access. Use brackets for all other method calls.

---

## Grouping Properties and Methods

- **Properties:** Group related properties and list outlets at the top.
- **Methods:** Use `#pragma mark -` to separate method groups (e.g., delegate methods, private helpers).

---

## Nil Checks and Enumerations

- **Nil Checks:**  
  - Always use an explicit check:
    ```objc
    if (object != nil) { … }
    ```
- **Empty String Checks:**  
  ```objc
  if (title.length == 0) { … }
  ```
- **Enumeration:**  
  - Use fast enumeration if index isn’t needed:
    ```objc
    for (NSString *title in titles) { … }
    ```
  - Otherwise, use a standard for loop:
    ```objc
    for (NSUInteger i = 0; i < titles.count; i++) { … }
    ```

---

## instancetype and Safe Casting

- **instancetype:**  
  Use for initializer and factory method return types to help the compiler infer types.
  
  ```objc
  + (instancetype)personWithName:(NSString *)name;
  ```

- **Safe Casting:**  
  Check object types before casting to avoid runtime issues.

---

## Allocating and Initialising Objects

- Use the convenience method `[Object new]` when appropriate:

  ```objc
  MyViewController *vc = [MyViewController new];
  ```

- **Initializer Pattern:**
  
  ```objc
  - (instancetype)init {
      self = [super init];
      if (self) {
          // Custom initialization
      }
      return self;
  }
  ```

- For custom initializers, mark the default `init`/`new` as unavailable if needed.

---

## Switch Statements & Enums

- **Switch Statements:**  
  Use braces for each case and include a break or return.
  
  ```objc
  switch (buttonIndex) {
      case 0: {
          self.currentSort = SortTypeHigh;
          break;
      }
      case 1: {
          self.currentSort = SortTypeLow;
          break;
      }
      default: {
          break;
      }
  }
  ```

- **Enums:**  
  Always use NS_ENUM with a project prefix.
  
  ```objc
  typedef NS_ENUM(NSInteger, MyTableViewCellStyle) {
      MyTableViewCellStyleDefault,
      MyTableViewCellStyleValue1,
      MyTableViewCellStyleValue2,
      MyTableViewCellStyleSubtitle
  };
  ```

---

## Arrays and Dictionaries

- **Literal Syntax:** Always use literal syntax and ensure no nil values are inserted.
  
  **Array Example:**
  ```objc
  NSArray<NSString *> *names = @[@"Alice", @"Bob", @"Charlie"];
  ```
  
  **Dictionary Example:**
  ```objc
  NSDictionary<NSString *, NSString *> *nicknames = @{ @"Alice" : @"Ally", @"Bob" : @"Bobby" };
  ```
- Use `firstObject` to access the first element rather than `[array objectAtIndex:0]`.

---

## 64-bit Compatible Types

Favor architecture-safe types:
  
| Allowed      | Not Allowed       |
| ------------ | ----------------- |
| NSInteger    | int               |
| NSUInteger   | unsigned int      |
| CGFloat      | float             |

---

## Reuse Identifiers for Table/Collection Views

- Prefer category methods for nib and reuse identifier extraction:

  ```objc
  [tableView registerNib:[MyTableViewCell nibForClass] forCellReuseIdentifier:[MyTableViewCell nibIdentifier]];
  ```
- When not possible, use constants instead of literal strings.

---

## Completion Blocks

- Format parameters on new lines if they exceed the page guide.
  
  ```objc
  [object doSomethingWithCompletion:^(NSDictionary *result,
                                        NSURLResponse *response,
                                        NSError *error) {
      // Handle completion
  }];
  ```

---

## Clearing UI

- To clear UI elements, set them to nil (not to empty strings or new objects):

  ```objc
  label.text = nil;
  imageView.image = nil;
  ```

---

## Singleton Pattern

- Use a thread-safe method:

  ```objc
  + (instancetype)sharedInstance {
      static dispatch_once_t onceToken;
      static MyClass *sharedInstance = nil;
      dispatch_once(&onceToken, ^{
          sharedInstance = [self new];
      });
      return sharedInstance;
  }
  ```

---

## Blocks and __block Variables

- **Blocks:**  
  Blocks are Objective-C objects. They can capture values from their surrounding scope:
  
  ```objc
  NSArray *items = @[@"A", @"B", @"C"];
  [items enumerateObjectsUsingBlock:^(id obj, NSUInteger idx, BOOL *stop) {
      NSLog(@"Item: %@", obj);
  }];
  ```

- **__block Variables:**  
  Use `__block` to allow a block to modify an external variable:
  
  ```objc
  __block NSInteger counter = 0;
  dispatch_async(queue, ^{
      counter++;
  });
  ```

---

## Retain Cycles in ARC Using Blocks

Always create a weak reference to `self` inside blocks to avoid retain cycles:

```objc
__weak typeof(self) weakSelf = self;
runOnMainQueueWithoutDeadlocking(^{
    weakSelf.someProperty = @"Text";
    [weakSelf someMethod];
});
```

Some projects define macros to simplify this pattern:

```objc
#define weaken(object, newName) __typeof__(object) __weak newName = object
```

---

## CGGeometry & CGRect Functions

- **Access CGRect Components:**  
  Use the provided functions to ensure standardized (positive) values:
  
  ```objc
  CGRect frame = self.view.frame;
  CGFloat x = CGRectGetMinX(frame);
  CGFloat y = CGRectGetMinY(frame);
  CGFloat width = CGRectGetWidth(frame);
  CGFloat height = CGRectGetHeight(frame);
  ```

---

## String Replacement

- Use `stringByReplacingOccurrencesOfString:withString:` for single substitutions.

---

## Commenting

- **Documentation Comments:** Use Xcode’s documentation shortcuts (⌥⌘/) for generating comments.
- **Style:**  
  - Use block comments for method documentation.
  - Keep comments focused on explaining *why* something is done.
  - Avoid excessive inline comments that duplicate self-explanatory code.
  
  **Example:**
  ```objc
  /*!
   Creates and returns a configured action.
   @param dataComponents An array of strings used for initialization.
   @return A configured action object.
  */
  + (nullable instancetype)actionWithDataComponents:(nonnull NSArray<NSString *> *)dataComponents;
  ```

---

## Fonts, Colours, and File Management

- **Fonts & Colours:**  
  Don’t hardcode values; use category methods.
- **File Structure:**  
  Maintain a flat folder structure in Xcode groups; use asset catalogs for media.

---

## General Practices

- **Magic Numbers/Strings:**  
  Replace “magic” literals with named constants.
- **Pragma Marks:**  
  Use them liberally to organize your code.
- **Enums:**  
  Always use NS_ENUM.
- **Primitive Types:**  
  Use NSInteger, NSUInteger, and CGFloat.
- **Boolean Values:**  
  Use BOOL; compare using conditional checks (e.g., `if (isAwesome)`) rather than `== YES`.

---

## Spacing and Indentation

- Use Xcode default settings (typically tabs).
- **Braces:**  
  Place opening braces on the same line as the declaration and closing braces on a new line:
  
  ```objc
  if (condition) {
      // Code
  } else {
      // Alternate code
  }
  ```
- **Blank Lines:**  
  Use a single blank line between methods.

---

## Conditionals and Ternary Operator

- **Braces:**  
  Always use braces, even for single-line bodies.
  
  ```objc
  if (condition) {
      // code
  }
  ```
- **Ternary Operator:**  
  Avoid its use; prefer clear if/else statements.

---

## Error Handling

When a method returns an error by reference, check the method’s return value, not the error variable:

```objc
NSError *error = nil;
if (![self trySomethingWithError:&error]) {
    // Handle error
}
```

---

## Methods and Variables

- **Method Signatures:**  
  Include a space after the `-` or `+`.
  
  ```objc
  - (void)configureCellWithIdentifier:(NSString *)cellIdentifier
                                  type:(NSString *)cellType
                            cellHeight:(CGFloat)cellHeight;
  ```
- **Variable Declarations:**  
  Declare one variable per line, attach asterisks to the variable name (e.g., `NSString *title;`).

---

## Naming Conventions

- Use clear, descriptive names.
- Use a three-letter prefix (e.g., `NYT`) for classes and constants.
- **Example:**  
  ```objc
  static const NSTimeInterval NYTAnimationDuration = 0.3;
  ```
- Instance variables should be prefixed with an underscore.

---

## Categories

- Name categories descriptively and prefix method names to avoid collisions.
  
  **Example:**
  ```objc
  @interface NSArray (NYTAccessors)
  - (id)nyt_objectOrNilAtIndex:(NSUInteger)index;
  @end
  ```

---

## init and dealloc

- **init:**  
  Structure your initializer as follows:
  
  ```objc
  - (instancetype)init {
      self = [super init];
      if (self) {
          // Custom initialization
      }
      return self;
  }
  ```
- **dealloc:**  
  Place dealloc at the top of the implementation file (after synthesize/dynamic, if any).

---

## Literals

Favor literal syntax for `NSString`, `NSArray`, `NSDictionary`, and `NSNumber` to reduce verbosity and prevent errors with nil values.

**Examples:**
```objc
NSArray *names = @[@"Alice", @"Bob", @"Charlie"];
NSDictionary *info = @{@"name": @"Alice", @"age": @30};
NSNumber *flag = @YES;
```

---

## Constants and Bitmasks

- **Constants:**  
  Declare as static constants instead of `#define`s.
  
  ```objc
  static NSString * const NYTCompanyName = @"The New York Times Company";
  static const CGFloat NYTThumbnailHeight = 50.0;
  ```
- **Bitmasks:**  
  Use NS_OPTIONS.
  
  ```objc
  typedef NS_OPTIONS(NSUInteger, NYTAdCategory) {
      NYTAdCategoryAutos      = 1 << 0,
      NYTAdCategoryJobs       = 1 << 1,
      NYTAdCategoryRealState  = 1 << 2,
      NYTAdCategoryTechnology = 1 << 3
  };
  ```

---

## Image Naming & Booleans

- **Image Naming:**  
  Use camel case and descriptive names (e.g., `RefreshBarButtonItem`, `ArticleNavigationBarWhite`).
- **Booleans:**  
  Use BOOL with values YES/NO. Compare objects with `== nil` or `!= nil` rather than implicitly.

---

## Imports and Protocols

- **Grouping:**  
  Group import statements by type (framework, model, view, etc.).  
  Use `@import` for modules.
  
  **Example:**
  ```objc
  // Frameworks
  @import QuartzCore;
  
  // Models
  #import "NYTUser.h";
  
  // Views
  #import "NYTButton.h";
  #import "NYTUserView.h";
  ```
- **Protocols:**  
  In delegate or data source methods, include the sender as the first parameter.
  
  ```objc
  - (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath;
  ```

---

## Xcode Project Organization

- Reflect the physical file structure in your Xcode groups.
- Enable “Treat Warnings as Errors” and add as many compiler warnings as possible.
- Use Clang pragmas to suppress warnings for legacy code if needed.

---

## Other Objective-C Style Guides

If this guide doesn’t suit your needs, consider these alternatives:

- [Google Objective-C Style Guide](http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml)
- [GitHub Objective-C Conventions](https://github.com/github/objective-c-conventions)
- [Adium Coding Style](https://trac.adium.im/wiki/CodingStyle)
- [Sam Soffes’ Guide](https://gist.github.com/soffes/812796)
- [CocoaDevCentral](http://cocoadevcentral.com/articles/000082.php)
- [Luke Redpath’s Guide](http://lukeredpath.co.uk/blog/2011/06/28/my-objective-c-style-guide/)
- [Marcus Zarra’s Guide](http://www.cimgf.com/zds-code-style-guide/)
- [Wikimedia iOS Style Guide](https://www.mediawiki.org/wiki/Wikimedia_Apps/Team/iOS/ObjectiveCStyleGuide)

---

This optimized guide emphasizes consistency, clarity, and modern best practices in Objective-C development. Following these guidelines will help ensure your code is easier to read, maintain, and scale over time. Happy coding!
